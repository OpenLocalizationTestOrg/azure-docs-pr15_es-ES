<properties
    pageTitle="El proceso de la ciencia de datos de equipo en acción: utilizando SQL Server | Microsoft Azure"
    description="Proceso de análisis avanzado y la tecnología en acción"  
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="fashah;bradsev"/>


# <a name="the-team-data-science-process-in-action-using-sql-server"></a>El proceso de la ciencia de datos de equipo en acción: uso de SQL Server

En este tutorial, se tutorial creación e implementación de un modelo de aprendizaje de la máquina con SQL Server y un conjunto de datos disponible públicamente--el conjunto de datos de [Viajes de Taxi de Nueva York](http://www.andresmh.com/nyctaxitrips/) . El procedimiento sigue un flujo de trabajo de la ciencia de datos estándar: ingesta y explorar los datos, funciones de ingeniería para facilitar el aprendizaje, a continuación, generar e implementar un modelo.


## <a name="dataset"></a>Descripción del conjunto de datos de viajes de Taxi de Nueva York

Los datos de Nueva York de ida y vuelta Taxi son unos 20GB de archivos CSV comprimidos (~ 48GB sin comprimir), que comprende más de 173 millones de viajes individuales y las tarifas pagadas por cada viaje. Cada registro de viaje incluye la ubicación de recogida y entrega y tiempo, hack anónimos (controlador) número de licencia y el número de medallón (identificador único del taxi). Los datos cubre todos los viajes del año 2013 y se proporcionan en los dos conjuntos de datos siguientes para cada mes:

1. 'trip_data' CSV contiene detalles de ida y vuelta, como el número de pasajeros, recogida y devolución puntos, duración del viaje y duración del viaje. Aquí están algunos registros de ejemplo:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. La 'trip_fare' CSV contiene detalles sobre el precio pagado por cada viaje, como tipo de pago, importe del billete, suplemento y los impuestos, sugerencias y peajes y el importe total pagado. Aquí están algunos registros de ejemplo:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La clave única para unirse a de ida y vuelta\_datos y viaje\_tarifa se compone de los campos: medallón, hack\_licencia y recogida\_datetime.

## <a name="mltasks"></a>Ejemplos de tareas de predicción

Formulamos tres problemas de predicción basados en la *sugerencia\_importe*, a saber:

1. Clasificación binaria: predecir o no una sugerencia se pagó por un trayecto, es decir, un *sugerencia\_importe* que es mayor de $0 es un ejemplo positivo, mientras un *sugerencia\_importe* $ 0 es un ejemplo negativo.

2. Clasificación multiclase: para predecir el intervalo de propina para el viaje. Dividimos la *sugerencia\_importe* en cinco ubicaciones o clases:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. Tarea de regresión: para predecir la cantidad de propina para un viaje.  


## <a name="setup"></a>Ajuste el Azure datos ciencia entorno para análisis avanzado

Como puede ver en la Guía de [Plan Your Environment](machine-learning-data-science-plan-your-environment.md) , hay varias opciones para trabajar con el conjunto de datos de viajes de Nueva York Taxi en Azure:

- Trabajar con los datos de BLOB de Azure y modelo de aprendizaje de la máquina de Azure
- Cargar los datos en una base de datos de SQL Server, a continuación, modelo de aprendizaje de la máquina de Azure

En este tutorial demostraremos importación masiva paralela de los datos de un SQL Server, la característica, exploración de los datos de ingeniería y hacia abajo de muestreo utilizando SQL Server Management Studio, así como utilizando el Bloc de notas de IPython. [Secuencias de comandos de ejemplo](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) y [IPython portátiles](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) se comparten en GitHub. Un bloc de notas de IPython de ejemplo para trabajar con los datos de BLOB de Azure también está disponible en la misma ubicación.

Para configurar el entorno de la ciencia de datos Azure:

1. [Crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md)

2. [Crear un área de trabajo de aprendizaje de la máquina de Azure](machine-learning-create-workspace.md)

3. [Una máquina Virtual de la ciencia de los datos de provisión](machine-learning-data-science-setup-sql-server-virtual-machine.md), que servirá como un SQL Server también un servidor de Bloc de notas de IPython.

    > [AZURE.NOTE] Las secuencias de comandos de ejemplo y cuadernos de IPython se descargará a su equipo virtual de ciencia de datos durante el proceso de instalación. Cuando se completa la secuencia de comandos de instalación posterior a la máquina virtual, las muestras será de biblioteca de documentos de la VM:  
    > - Secuencias de comandos de ejemplos:`C:\Users\<user_name>\Documents\Data Science Scripts`  
    > - Cuadernos de IPython de ejemplo:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
    > donde `<user_name>` es el nombre de inicio de sesión de Windows de la máquina virtual. Nos referiremos a las carpetas de ejemplo como **Secuencias de comandos de ejemplo** y **Blocs de notas de ejemplo IPython**.


Según el tamaño del conjunto de datos, la ubicación de origen de datos y el entorno de destino seleccionado de Azure, este escenario es similar al [escenario \#5: conjunto de datos grande en los archivos locales de destino de SQL Server en la máquina virtual de Azure](../machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Obtener los datos de origen público

Para obtener el conjunto de datos de [Viajes de Taxi de Nueva York](http://www.andresmh.com/nyctaxitrips/) desde su ubicación pública, puede utilizar cualquiera de los métodos descritos en [Mover los datos hacia y desde el almacenamiento de Azure Blob](machine-learning-data-science-move-azure-blob.md) para copiar los datos a la nueva máquina virtual.

Para copiar los datos mediante AzCopy:

1. Inicie sesión en la máquina virtual (VM)

2. Crear un nuevo directorio en disco de los datos de la máquina virtual (Nota: no utilice el disco temporal que viene con la máquina virtual como un disco de datos).

3. En una ventana de símbolo del sistema, ejecute la siguiente línea de comandos Azcopy, reemplazando < path_to_data_folder > con la carpeta de datos creada en (2):

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

    Cuando se completa la AzCopy, en un total de 24 zip archivos CSV (12 para viaje de\_datos y 12 para viaje de\_tarifa) debe estar en la carpeta de datos.

4. Descomprima los archivos descargados. Anote la carpeta donde residen los archivos descomprimidos. Esta carpeta se denominará la < ruta de acceso\_a\_datos\_archivos\>.

## <a name="dbload"></a>Importación masiva de datos en la base de datos de SQL Server

El rendimiento de carga/transferir grandes cantidades de datos a una base de datos SQL y las consultas subsiguientes se puede mejorar mediante el uso de _particiones de tablas y vistas_. En esta sección, seguiremos las instrucciones descritas en [Paralelo masiva importación utilizando SQL partición de tablas de datos](machine-learning-data-science-parallel-load-sql-partitioned-tables.md) para crear una nueva base de datos y cargar los datos en tablas con particiones en paralelo.

1. Cuando inicie sesión en su máquina virtual, inicie **SQL Server Management Studio**.

2. Conectar mediante autenticación de Windows.

    ![Conectar SSMS][12]

3. Si aún no ha cambiado el modo de autenticación de SQL Server y crea un nuevo usuario de inicio de sesión SQL, abra el archivo de script denominado **cambiar\_auth.sql** en la carpeta de **Secuencias de comandos de ejemplo** . Cambiar el nombre de usuario y la contraseña. ¡Haga clic en **! Ejecutar** en la barra de herramientas para ejecutar la secuencia de comandos.

    ![Ejecutar secuencia de comandos][13]

4. Comprobar o cambiar el SQL Server la base de datos y de registro carpetas predeterminadas para garantizar que se almacenan en un disco de datos recién creado bases de datos. La imagen de máquina virtual de SQL Server que está optimizada para las cargas del almacén de datos viene preconfigurada con discos de datos y de registro. Si la VM no incluía un disco de datos y agregar nuevos discos duros virtuales durante el proceso de configuración de máquina virtual, cambie las carpetas predeterminadas de la siguiente manera:

    - Haga clic en el nombre de SQL Server en el panel izquierdo y haga clic en **Propiedades**.

        ![Propiedades de SQL Server][14]

    - Seleccione **Configuración de base de datos** de la lista **Seleccionar una página** a la izquierda.

    - Comprobar o cambiar las **ubicaciones predeterminadas de base de datos** a las ubicaciones de **Disco de los datos** de su elección. Esto es donde nuevas bases de datos residen si creado con la configuración de ubicación predeterminada.

        ![Valores predeterminados de la base de datos SQL][15]  

5. Para crear una nueva base de datos y un conjunto de grupos de archivos para almacenar las tablas con particiones, abra la secuencia de comandos **crear\_db\_default.sql**. La secuencia de comandos creará una nueva base de datos denominada **TaxiNYC** y 12 grupos de archivos en la ubicación predeterminada de los datos. Cada grupo de archivos contiene un mes de ida y vuelta\_datos y viaje de\_datos de tarifas. Modificar el nombre de la base de datos, si lo desea. ¡Haga clic en **! Ejecutar** para ejecutar el script.

6. A continuación, cree dos tablas de partición, uno para el viaje de\_datos y otro para el viaje de\_tarifa. Abra la secuencia de comandos **crear\_con particiones\_table.sql**, que será:

    - Crear una función de partición para dividir los datos por mes.
    - Crear un esquema de partición para asignar los datos de cada mes a un grupo de archivos diferente.
    - Crear dos tablas con particiones asignadas al esquema de particiones: **nyctaxi\_viaje** contendrá el viaje\_datos y **nyctaxi\_tarifa** contendrá el viaje\_datos de tarifas.

    ¡Haga clic en **! Ejecutar** para ejecutar la secuencia de comandos y crear las tablas con particiones.

7. En la carpeta de **Secuencias de comandos de ejemplo** , hay dos scripts de PowerShell de ejemplo proporcionados para demostrar importa paralelo masiva de datos en tablas de SQL Server.

    - **bcp\_paralelo\_generic.ps1** es una secuencia de comandos genérica para los datos de importación masiva paralelo en una tabla. Modificar esta secuencia de comandos para establecer las variables de entrada y destino como se indica en las líneas de comentario en la secuencia de comandos.
    - **bcp\_paralelo\_nyctaxi.ps1** es una versión preconfigurada de la secuencia de comandos genérica y puede utilizarse para cargar ambas tablas para los datos de Nueva York Taxi viajes.  

8. Haga clic en el **bcp\_paralelo\_nyctaxi.ps1** nombre de secuencia de comandos y haga clic en **Editar** para abrirlo en PowerShell. Revisar las variables predefinidas y modificar según su nombre de base de datos seleccionada, carpeta de datos de entrada, la carpeta de registro de destino y rutas de acceso a los archivos de formato de ejemplo **nyctaxi_trip.xml** y **nyctaxi\_fare.xml** (que se proporciona en la carpeta de **Secuencias de comandos de ejemplo** ).

    ![Datos de importación masiva][16]

    También puede seleccionar el modo de autenticación, el valor predeterminado es autenticación de Windows. Haga clic en la flecha verde de la barra de herramientas para ejecutar. La secuencia de comandos iniciará 24 operaciones de importación masiva en paralelo, 12 para cada tabla con particiones. Puede supervisar el progreso de la importación de datos abriendo la carpeta de datos de SQL Server predeterminada anteriormente.

9. La secuencia de comandos de PowerShell informes inicial y hora final. Cuando todas las importaciones completas de forma masiva, se informa de la hora de finalización. Compruebe la carpeta de registro de destino para comprobar que las importaciones a granel tuvieron éxito, es decir, no hay errores registrados en la carpeta de registro de destino.

10. La base de datos está preparado para la exploración, ingeniería de la característica y otras operaciones como se desee. Dado que las tablas se dividen según la **recogida\_datetime** campo, las consultas que incluyen **recogida\_datetime** condiciones en la cláusula **WHERE** se beneficiarán el esquema de partición.

11. En **SQL Server Management Studio**, explorar el script de ejemplo proporcionado **ejemplo\_queries.sql**. Para ejecutar cualquiera de las consultas de ejemplo, resalte las líneas de consulta, a continuación, haga clic en **! Ejecutar** en la barra de herramientas.

12. Se cargan los datos de Nueva York Taxi viajes en dos tablas distintas. Para mejorar las operaciones de combinación, se recomienda indizar las tablas. La secuencia de comandos **crear\_con particiones\_index.sql** crea índices con particiones en la clave de combinación compuestos **medallón, hack\_licencia y recogida\_datetime**.

## <a name="dbexplore"></a>Exploración de datos e ingeniería de la característica en SQL Server

En esta sección, realizaremos exploración de datos y generación de función mediante la ejecución de las consultas SQL directamente en **SQL Server Management Studio** mediante la base de datos de SQL Server que creó anteriormente. Una secuencia de comandos de ejemplo denominado **ejemplo\_queries.sql** se proporciona en la carpeta de **Secuencias de comandos de ejemplo** . Modificar la secuencia de comandos para cambiar el nombre de la base de datos, si es diferente del valor predeterminado: **TaxiNYC**.

En este ejercicio, vamos a:

- Conectar con **SQL Server Management Studio** utilizando autenticación de Windows o autenticación de SQL y el nombre de inicio de sesión SQL y la contraseña.
- Explore las distribuciones de datos de algunos campos en diferentes ventanas de tiempo.
- Investigue la calidad de los datos de los campos de longitud y latitud.
- Generar etiquetas de clasificación multiclase y binario basadas en la **sugerencia\_importe**.
- Generar características y compute o comparar las distancias de ida y vuelta.
- Combine las dos tablas y extraer una muestra aleatoria que se utilizará para crear modelos.

Cuando esté listo para continuar con el aprendizaje de la máquina de Azure, puede:  

1. Guarde la consulta SQL final para extraer y los datos de ejemplo y copiar y pegar la consulta directamente en una [Importación de datos] de[ import-data] módulo de aprendizaje de la máquina de Azure, o
2. Conservar los datos muestreados y de ingeniería que piensa utilizar para la compilación en una nueva tabla de base de datos del modelo y utilizar la nueva tabla en la [Importación de datos] [ import-data] módulo de aprendizaje de la máquina de Azure.

En esta sección se guardará la consulta final para extraer los datos de ejemplo. El segundo método se muestra en la sección de [exploración de datos y función de ingeniería en el Bloc de notas de IPython](#ipnb) .

Para una comprobación rápida del número de filas y columnas de las tablas rellenadas anteriormente mediante la importación masiva paralela,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploración: Distribución de ida y vuelta por medallón

Este ejemplo identifica el medallón (números de taxi) con más de 100 viajes dentro de un período de tiempo determinado. La consulta se beneficiaría al acceso de la tabla con particiones ya que está condicionado por el esquema de partición de **recogida\_datetime**. Consultar el conjunto de datos completo también hará uso de la tabla con particiones o análisis de índice.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploración: Distribución de ida y vuelta medallón y hack_license

    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Evaluación de la calidad de los datos: Comprobar registros con longitud incorrecta o latitude

En este ejemplo se investiga si alguno de los campos de longitud o latitud o contiene un valor no válido (radianes grados deben estar entre -90 y 90), o (0, 0) coordenadas.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploración: Distribución con punta frente a no superpuesto paseos

Este ejemplo busca el número de viajes que se han superpuesto frente no superpuesto en un momento determinado período (o en el conjunto de datos completo si abarca todo el año). Esta distribución refleja la distribución de etiqueta binario que se utilizará más adelante para el modelado de clasificación binario.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Exploración: Distribución de clase o intervalo de sugerencia

Este ejemplo calcula la distribución de los rangos de sugerencia en un período de tiempo (o en el conjunto de datos completo si abarca todo el año). Se trata de la distribución de las clases de etiqueta que se utilizará más adelante para el modelado de clasificación multiclase.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploración: Calcular y comparar la distancia del viaje

Este ejemplo convierte la recogida y entrega de longitud y latitud geografía de SQL señala, calcula la distancia de viaje con diferencia de puntos geográficos SQL y devuelve una muestra aleatoria de los resultados de la comparación. En el ejemplo se limita los resultados a coordenadas válidos sólo con la consulta de evaluación de calidad de datos tratada anteriormente.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Ingeniería de la característica de consultas SQL

Las consultas de exploración etiqueta generación y geografía conversión también pueden utilizarse para generar etiquetas/características al quitar la parte de recuento. Se proporcionan ejemplos SQL ingeniería de característica adicional en la sección de [exploración de datos y función de ingeniería en el Bloc de notas de IPython](#ipnb) . Resulta más eficaz para ejecutar consultas de generación de la característica en el conjunto de datos completo o un subconjunto grande de utilizando las consultas SQL que se ejecutan directamente en la instancia de base de datos de SQL Server. Las consultas se pueden ejecutar en **SQL Server Management Studio**, Bloc de notas de IPython o cualquier herramienta o entorno de desarrollo que puede tener acceso a la base de datos local o remotamente.

#### <a name="preparing-data-for-model-building"></a>Preparar los datos para la compilación del modelo

La siguiente consulta se une a la **nyctaxi\_viaje de** y **nyctaxi\_tarifa** tablas, genera una clasificación binaria etiqueta **superpuesto**, una etiqueta de clasificación de clase múltiples **sugerencia\_clase**y extrae una muestra aleatoria del 1% del conjunto de datos completo combinada. Esta consulta puede copiar entonces pegar directamente en el [Estudio de aprendizaje de máquina de Azure](https://studio.azureml.net) [Importar datos] [ import-data] módulo de recopilación de datos directos de la instancia de base de datos de SQL Server en Azure. La consulta excluye los registros con incorrecto (0, 0) coordenadas.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Exploración de datos e ingeniería de la característica en el Bloc de notas de IPython

En esta sección, se realizará la exploración de datos y generación de característica mediante consultas de Python y SQL en la base de datos de SQL Server que creó anteriormente. Se proporciona un bloc de notas de IPython de ejemplo denominado **machine-Learning-data-science-process-sql-story.ipynb** en la carpeta **Blocs de notas de ejemplo IPython** . Este bloc de notas también está disponible en [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

La secuencia recomendada cuando se trabaja con datos grandes es la siguiente:

- Leer en una pequeña muestra de los datos en una trama de datos en memoria.
- Realizar algunas visualizaciones y exploraciones utilizando los datos muestreados.
- Experimente con la ingeniería de la característica con los datos muestreados.
- Para la exploración de datos más grande, manipulación de datos e ingeniería de la característica, utilizar Python para emitir consultas SQL directamente en la base de datos de SQL Server en la máquina virtual de Azure.
- Decidir el tamaño de muestra a utilizar para la compilación del modelo de aprendizaje de la máquina de Azure.

Cuando esté listo para proceder al aprendizaje de la máquina de Azure, puede:  

1. Guarde la consulta SQL final para extraer y los datos de ejemplo y copiar y pegar la consulta directamente en una [Importación de datos] de[ import-data] módulo de aprendizaje de la máquina de Azure. Este método se muestra en la sección de [Modelos de edificio en aprendizaje de máquinas de Azure](#mlmodel) .    
2. Conservar los datos muestreados y de ingeniería que piensa utilizar para la compilación en una nueva tabla de base de datos del modelo, a continuación, utilizar la nueva tabla en la [Importación de datos] de[ import-data] módulo.

Éstos son unos característica ejemplos de ingeniería, visualización de datos y exploración de los datos. Para obtener más ejemplos, consulte el Bloc de notas de ejemplo SQL IPython en la carpeta **Blocs de notas de ejemplo IPython** .

#### <a name="initialize-database-credentials"></a>Inicializar las credenciales de la base de datos

Inicializar la configuración de conexión de base de datos en las variables siguientes:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Crear la conexión de base de datos

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Informe de número de filas y columnas en la tabla nyctaxi_trip

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de filas = 173179759  
- Número total de columnas = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Lectura de una muestra pequeña de datos desde la base de datos de SQL Server

    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tiempo para leer que la tabla de ejemplo es 6.492000 segundos  
Número de filas y columnas recuperadas = (84952, 21)

#### <a name="descriptive-statistics"></a>Estadística descriptiva

Ahora está listo para explorar los datos muestreados. Empezamos con mirar Estadística descriptiva para el **viaje\_distancia** (o cualquier otro) Field (s):

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualización: Ejemplo de trazado del cuadro

A continuación, veremos el diagrama de caja para la distancia de ida y vuelta visualizar los cuantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Trazar #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualización: Ejemplo de trazado de distribución

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Trazar #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualización: Barra y línea parcelas

En este ejemplo, hemos bin la distancia del viaje en cinco ubicaciones y visualizar los resultados de binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos trazar la distribución bin arriba en una barra o línea de trazado como abajo

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Trazar #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Trazar #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualización: Ejemplo de diagrama de dispersión

Mostramos un gráfico de dispersión entre **viaje\_tiempo de\_en\_segundos** y **viaje\_distancia** para ver si existe alguna correlación

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Trazar #6][6]

Del mismo modo podemos comprobar la relación entre **velocidad\_código** y **viaje\_distancia**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Trazar #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>El muestreo de los datos en SQL

Al preparar datos para modelar edificio en [Azure máquina Learning Studio](https://studio.azureml.net), puede decidir en la **consulta SQL para utilizar directamente en el módulo de importación de datos** o conservar los datos de ingeniería y muestreados en una nueva tabla, que puede utilizar en los [Datos de importación] [ import-data] módulo con un simple * *Seleccione* FROM < su\_nueva\_tabla\_nombre >**.

En esta sección, crearemos una nueva tabla para alojar los datos muestreados y de ingeniería. En la sección de [exploración de datos y función de ingeniería en SQL Server](#dbexplore) se proporciona un ejemplo de una consulta SQL directa para la compilación del modelo.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Crear un ejemplo de la tabla y rellenar con un 1% de las tablas combinadas. Colocar tabla primero si existe.

En esta sección, nos unimos las tablas **nyctaxi\_viaje de** y **nyctaxi\_tarifa**, extraer una muestra aleatoria de 1% y conservar los datos muestreados en un nuevo nombre de tabla **nyctaxi\_una\_%**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Exploración de datos mediante consultas de SQL en el Bloc de notas de IPython

En esta sección, exploramos las distribuciones de datos utilizando los datos de % 1 muestreado que se conservan en la nueva tabla que creamos anteriormente. Tenga en cuenta que se pueden realizar exploraciones similares usando las tablas originales, utilizando opcionalmente la **cláusula TABLESAMPLE** para limitar la exploración muestra o limitando los resultados a un período de tiempo determinado mediante el **recogida\_datetime** particiones, como se muestra en la sección de [exploración de datos y función de ingeniería en SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Exploración: Distribución diaria de viajes

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploración: Distribución de ida y vuelta por medallón

    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Generación de función mediante consultas SQL en el Bloc de notas de IPython

En esta sección se generarán nuevas etiquetas y funcionando en la tabla de ejemplo % 1 características directamente mediante consultas SQL, hemos creado en la sección anterior.

#### <a name="label-generation-generate-class-labels"></a>Generación de etiquetas: Generar etiquetas de clase

En el siguiente ejemplo, se generan dos conjuntos de etiquetas que se utilice para el modelado:

1. Binario etiquetas de clase **superpuesto** (predicción si se dará una sugerencia)
2. Etiquetas multiclase **sugerencia\_clase** (predecir la ubicación de punta o rango)

        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''

        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()

        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Ingeniería de la característica: Características de recuento de columnas por categorías

En este ejemplo se transforma un campo por categorías en un campo numérico reemplazando cada categoría con el recuento de sus apariciones en los datos.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Ingeniería de la característica: Funciones Bin para columnas numéricas

En este ejemplo se transforma un campo numérico continuado en rangos de categoría preestablecida, es decir, transformación de campo numérico en un campo por categorías.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Ingeniería de la característica: Extraer las características de ubicación de latitud Decimal

Este ejemplo divide la representación decimal de un campo de longitud o la latitud en varios campos de la región de detalle diferente, como país, ciudad, ciudad, bloque, etcetera. Tenga en cuenta que los campos geo nuevos no están asignados a ubicaciones reales. Para obtener información sobre ubicaciones de código geográfico de asignación, consulte [Servicios de REST de Bing Maps](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Compruebe la forma final de la tabla featurized

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Ahora estamos listos para proceder a la compilación del modelo y la implementación de modelo de [Aprendizaje de la máquina de Azure](https://studio.azureml.net). Los datos están listos para cualquiera de los problemas de predicción identificados anteriormente, a saber:

1. Clasificación binaria: predecir si se pagó una sugerencia para un viaje.

2. Clasificación multiclase: para predecir el intervalo de propina, según los ramos definidos previamente.

3. Tarea de regresión: para predecir la cantidad de propina para un viaje.  


## <a name="mlmodel"></a>Modelos de edificio en aprendizaje de máquinas de Azure

Para iniciar el ejercicio de modelado, inicie sesión en su área de trabajo de aprendizaje de la máquina de Azure. Si aún no ha creado una área de trabajo de aprendizaje de la máquina, consulte [creación de un área de trabajo de máquina de aprendizaje de Azure](machine-learning-create-workspace.md).

1. Para empezar con el aprendizaje de la máquina de Azure, consulte [¿Qué es aprendizaje Studio de Azure máquina?](machine-learning-what-is-ml-studio.md)

2. Inicie sesión en la [máquina de Azure aprendizaje Studio](https://studio.azureml.net).

3. La página principal de Studio proporciona una gran cantidad de información, vídeos, tutoriales, vínculos a la referencia de módulos y otros recursos. Para obtener más información acerca del aprendizaje de máquinas de Azure, consulte el [Centro de documentación de aprendizaje de máquina de Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Un experimento de entrenamiento típico consta de lo siguiente:

1. Crear un experimento **+ nuevo** .
2. Obtener los datos para el aprendizaje de máquinas de Azure.
3. Preprocesar, transformar y manipular los datos según sea necesario.
4. Generar características según sea necesario.
5. Dividir los datos en conjuntos de datos de entrenamiento, validación y pruebas (o conjuntos de datos independientes para cada uno).
6. Seleccione uno o varios algoritmos de aprendizaje de máquina dependiendo del problema de aprendizaje a resolver. Por ejemplo, clasificación binaria, clasificación multiclase, regresión.
7. Entrenar modelos de uno o más mediante el conjunto de datos de entrenamiento.
8. Anotar el conjunto de datos de validación con los modelos capacitados.
9. Evaluar los modelos para calcular las métricas relevantes para el problema de aprendizaje.
10. Bien ajustar los modelos y seleccionar el mejor modelo para implementar.

En este ejercicio, hemos ya explorado y diseñado los datos de SQL Server y decidió, el tamaño de la muestra ingesta en aprendizaje de máquinas de Azure. Para crear uno o varios de los modelos de predicción decidimos:

1. Obtener los datos para el aprendizaje Azure utilizando los [Datos de importación] [ import-data] módulo, disponible en la sección de **datos de entrada y salida** . Para obtener más información, consulte los [Datos de importación] [ import-data] página de referencia del módulo.

    ![Importar datos de aprendizaje automático de Azure][17]

2. Como el **origen de datos** en el panel **Propiedades** , seleccione **Base de datos de SQL Azure** .

3. Escriba el nombre de la base de datos DNS en el campo **nombre del servidor de base de datos** . Formato:`tcp:<your_virtual_machine_DNS_name>,1433`

4. Escriba el **nombre de la base de datos** en el campo correspondiente.

5. Escriba el **nombre de usuario SQL** en el **aqccount nombre de usuario del servidor y la contraseña en el **contraseña de cuenta de usuario de servidor **.

6. Compruebe la opción **Aceptar cualquier certificado de servidor** .

7. En el área de texto de **consulta de base de datos** edición, pegar campos (incluidos los campos calculados, como las etiquetas) de la base de datos de la consulta que extrae la necesaria y abajo muestrea los datos con el tamaño de la muestra deseada.

Es un ejemplo de un experimento de clasificación binario leer datos directamente de la base de datos de SQL Server en la siguiente ilustración. Experimentos similares se pueden construir para problemas de regresión y clasificación multiclase.

![Tren de aprendizaje de máquina de Azure][10]

> [AZURE.IMPORTANT] En el modelado de datos proporcionan extracción y ejemplos de consultas de muestreo en las secciones anteriores, **se incluyen todos los rótulos de los ejercicios de tres modelos en la consulta**. Un paso importante (obligatorio) en cada uno de los ejercicios de modelado es **Excluir** las etiquetas para los dos problemas y cualquier otro **destino pérdidas**innecesarias. Para p. ej., cuando se utiliza la clasificación binaria, utilice la etiqueta **con punta** y excluir los campos **sugerencia\_clase**, **sugerencia\_importe**, y **total\_importe**. Estos últimos son pérdidas de destino ya que implican la punta pagado.
>
> Para excluir las columnas innecesarias o pérdidas de destino, puede utilizar las [Columnas Seleccione conjunto de datos] [ select-columns] módulo o los [Metadatos editar][edit-metadata]. Para obtener más información, vea [Seleccionar columnas en el conjunto de datos] [ select-columns] y [Editar metadatos] [ edit-metadata] páginas de referencia.

## <a name="mldeploy"></a>Implementar modelos de aprendizaje automático de Azure

Cuando el modelo esté listo, puede implementar fácilmente como servicio web directamente desde el experimento. Para obtener más información acerca de la implementación de servicios web de aprendizaje del equipo de Azure, vea [implementar un servicio web de aprendizaje del equipo de Azure](machine-learning-publish-a-machine-learning-web-service.md).

Para implementar un nuevo servicio web, debe:

1. Crear un experimento de puntuación.
2. Implementar el servicio web.

Para crear un experimento de resultados de un experimento de entrenamiento **finalizado** , haga clic en **Crear puntuación experimentar** en la barra inferior de la acción.

![Puntuación de Azure][18]

Aprendizaje automático Azure intentará crear un experimento de puntuación basado en los componentes de la experiencia de aprendizaje. En particular, hará lo siguiente:

1. Guardar el modelo capacitado y quite los módulos de capacitación de modelo.
2. Identificar un **puerto de entrada** lógico para representar el esquema de los datos de entrada esperada.
3. Identificar un **puerto de salida** lógico para representar el esquema de salida del servicio web esperado.

Cuando se crea el experimento puntuación, revisar y ajustar según sea necesario. Un ajuste típico consiste en reemplazar el conjunto de datos de entrada o consulta con que excluye los campos de etiqueta, como éstos no estarán disponibles cuando se llama al servicio. También es una buena práctica para reducir el tamaño del conjunto de datos de entrada o consulta a unos pocos registros, sólo lo suficiente para indicar el esquema de entrada. Para el puerto de salida, es común para excluir todas las entradas de los campos y sólo se incluyen **Etiquetas anotó** y **Probabilidades anotó** en los resultados utilizando las [Columnas Seleccione conjunto de datos] [ select-columns] módulo.

Un ejemplo de puntuación experimento es en la figura siguiente. Cuando esté listo para implementar, haga clic en el botón **Publicar servicio WEB** en la barra inferior de la acción.

![Publicar el aprendizaje automático de Azure][11]

Para recapitular, en este tutorial tutorial, ha creado un entorno de ciencia datos Azure, trabajado con un gran conjunto de datos pública desde la adquisición de datos para modelar la formación e implementación de un servicio web de aprendizaje del equipo de Azure.

### <a name="license-information"></a>Información de licencia

Este tutorial muestra y sus adjuntos notebook(s) IPython y secuencias de comandos compartidos por Microsoft bajo la licencia MIT. Registre el archivo LICENSE.txt en el directorio del ejemplo de código en GitHub para obtener más detalles.

### <a name="references"></a>Referencias

• La [página de descarga de viajes de Nueva York Taxi Andrés Monroy](http://www.andresmh.com/nyctaxitrips/)  
• [Frustrar datos de ida y vuelta de Taxi de Nueva York por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Nueva York Taxi y Limosina Comisión investigación y estadísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
