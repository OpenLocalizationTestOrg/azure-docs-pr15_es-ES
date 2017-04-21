<properties
    pageTitle="El proceso de ciencias de datos de grupo de acción: usar Hadoop clústeres | Microsoft Azure"
    description="Mediante el proceso de ciencias de datos de grupo para un escenario de llevar a cabo empleando un clúster de HDInsight Hadoop Compile e implemente un modelo con un conjunto de datos esté disponible públicamente."
    services="machine-learning,hdinsight"
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
    ms.author="hangzh;bradsev" />


# <a name="the-team-data-science-process-in-action-using-hdinsight-hadoop-clusters"></a>El proceso de ciencias de datos de grupo de acción: usar clústeres HDInsight Hadoop

En este tutorial, se utiliza el [Proceso de ciencias de datos de equipo (TDSP)](data-science-process-overview.md) en un escenario de llevar a cabo mediante un [clúster de Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para almacenar, explorar y datos de ingeniería del conjunto de datos de [Nueva York Taxi viajes](http://www.andresmh.com/nyctaxitrips/) disponible públicamente de características y a los datos de ejemplo de abajo. Modelos de datos se crean con Azure el aprendizaje para controlar binarias y multiclass clasificación y regresión predictivas tareas.

Para obtener un tutorial que muestra cómo tratar un conjunto de datos (1 terabyte) más grande para un escenario similar con HDInsight Hadoop clústeres para procesamiento de datos, vea el [Proceso de ciencias datos del equipo - usar Azure HDInsight Hadoop clústeres en un conjunto de datos de 1 TB](machine-learning-data-science-process-hive-criteo-walkthrough.md).

También es posible usar un bloc de notas de IPython para llevar a cabo las tareas que se presenta el tutorial con el conjunto de datos de 1 TB. Los usuarios que le gustaría probar este enfoque deberían consultar el tema del [tutorial Criteo mediante una conexión ODBC subárbol](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .


## <a name="dataset"></a>Descripción del conjunto de datos de Nueva York Taxi viajes

Los datos de Nueva York Taxi viaje están de aproximadamente 20GB comprimidos valores separados por comas (CSV) de archivos de (~ 48GB sin comprimir), que incluyen más de 173 millones de viajes individuales y las tarifas de pago para cada viaje. Cada registro de viaje incluye la ubicación de entrega y recogida y hora, piratería anonymized (controlador) licencia número y medallion (identificador exclusivo de taxi). Los datos cubre todos los viajes del año 2013 y está disponible en los dos conjuntos de datos siguientes para cada mes:

1. Archivos CSV 'trip_data' contienen detalles de viaje, como el número de personas, recogida y puntos de caída, duración de viaje y longitud de viaje. Estos son algunos registros de ejemplo:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Los archivos CSV de 'trip_fare' contienen detalles de la tarifa de pago para cada viaje, como el tipo de pago, importe tarifa, suplemento y los impuestos, sugerencias y peajes y la cantidad total pagada. Estos son algunos registros de ejemplo:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La clave única para unirse a viajes\_datos y viaje\_tarifa se compone de los campos: medallion, prueba\_licencia y recogida\_datetime.

Para obtener todos los detalles pertinentes para un viaje determinado, es suficiente para unirse con tres claves: "medallion", "ataques\_licencia" y "recogida\_datetime".

Se describen algunos detalles más de los datos cuando se almacenan en tablas de la sección breve.

## <a name="mltasks"></a>Ejemplos de tareas de predicción
Cuando acercando a datos, determinar el tipo de predicciones para realizar en función de su análisis le ayuda a aclarar las tareas que debe incluir en el proceso.
Aquí se muestran tres ejemplos de problemas de predicción que se soluciona en este tutorial cuyo formulación se basa en el *sugerencia\_cantidad*:

1. **Clasificación binario**: predecir o no una sugerencia se pagó un viaje, es decir, un *sugerencia\_cantidad* que es mayor que $0 es un ejemplo positivo, mientras un *sugerencia\_cantidad* $ 0 es un ejemplo negativo.

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0

2. **Clasificación multiclass**: predecir el intervalo de importes de sugerencia de pago para el viaje. Dividimos el *sugerencia\_importe* en cinco clases o ubicaciones:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Tarea de regresión**: predecir la cantidad de la punta de un viaje de pago.  


## <a name="setup"></a>Configurar un clúster de HDInsight Hadoop de análisis avanzado

>[AZURE.NOTE] Suele ser un **Administrador** de tareas.

Puede configurar un entorno de Azure para análisis avanzado que emplea un clúster de HDInsight en tres pasos:

1. [Crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md): esta cuenta de almacenamiento se utiliza para almacenar los datos en el almacenamiento de blobs de Windows Azure. Los datos utilizados en clústeres HDInsight también se encuentran aquí.

2. [Personalizar HDInsight Hadoop de Azure clústeres para el proceso de análisis avanzadas y tecnología](machine-learning-data-science-customize-hadoop-cluster.md). Este paso crea un HDInsight Hadoop de Azure clúster de 64 bits Anaconda Python 2.7 instalado en todos los nodos. Hay dos pasos importantes que debe recordar mientras personaliza el clúster HDInsight.

    * No olvide vincular creada en el paso 1 con el clúster de HDInsight cuando se crea la cuenta de almacenamiento. Esta cuenta de almacenamiento se usa para acceder a los datos que se procesan en el clúster.

    * Una vez creado el clúster, habilitar el acceso remoto al nodo principal del clúster. Vaya a la ficha **configuración** y haga clic en **Habilitar remoto**. Este paso especifica las credenciales de usuario que se usa para el inicio de sesión remota.

3. [Crear un área de trabajo de máquina de Azure aprendizaje](machine-learning-create-workspace.md): aprendizaje del equipo de Azure esta área de trabajo se usa para crear modelos de aprendizaje del equipo. Esta tarea se destina después de completar una exploración de datos iniciales y abajo muestreo utilizando el clúster HDInsight.

## <a name="getdata"></a>Obtener los datos de un origen de público

>[AZURE.NOTE] Suele ser un **Administrador** de tareas.

Para obtener el conjunto de datos de [Nueva York Taxi viajes](http://www.andresmh.com/nyctaxitrips/) desde su ubicación pública, puede utilizar cualquiera de los métodos descritos en [Mover los datos hacia y desde el almacenamiento de blobs de Windows Azure](machine-learning-data-science-move-azure-blob.md) para copiar los datos en el equipo.

Aquí se describe cómo usar AzCopy para transferir los archivos que contiene datos. Para descargar e instalar AzCopy siga las instrucciones en [Introducción a la herramienta de línea de comandos de AzCopy](../storage/storage-use-azcopy.md).

1. Desde una ventana de símbolo del sistema, emita los siguientes comandos de AzCopy reemplazando *< path_to_data_folder >* con el destino deseado:


        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. Cuando se complete la copia, un total de 24 archivos comprimidos se encuentran en la carpeta de datos elegida. Descomprima los archivos descargados en el mismo directorio en el equipo local. Anote la carpeta donde residen los archivos sin comprimir. Esta carpeta se denominan el *< ruta\_a\_unzipped_data\_archivos\> * es lo que sigue.


## <a name="upload"></a>Cargar los datos en el contenedor predeterminado de clúster de HDInsight Hadoop de Azure

>[AZURE.NOTE] Suele ser un **Administrador** de tareas.

En los siguientes comandos de AzCopy, reemplace los siguientes parámetros con los valores reales que especificó al crear el clúster de Hadoop y descomprimir los archivos de datos.

* ***& #60; path_to_data_folder >*** el directorio (junto con la ruta de acceso) en el equipo que contienen los archivos de datos descomprimido  
* ***& #60; el nombre de la cuenta de almacenamiento de clúster Hadoop >*** la cuenta de almacenamiento asociada con el clúster HDInsight
* ***& #60; contenedor predeterminado de clúster Hadoop >*** el contenedor predeterminado utilizado por el clúster. Tenga en cuenta que el nombre del contenedor predeterminado suele ser el mismo nombre que el clúster propiamente dicho. Por ejemplo, si el clúster se llama "abc123.azurehdinsight.net", el contenedor predeterminado es abc123.
* ***& #60; clave de cuenta de almacenamiento >*** la clave para la cuenta de almacenamiento utilizada por el clúster

Desde un símbolo o una ventana de Windows PowerShell en el equipo, ejecute los siguientes comandos AzCopy dos.

Este comando carga los datos de ida y vuelta al directorio ***nyctaxitripraw*** en el contenedor predeterminado del clúster Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Este comando carga los datos de tarifa al directorio ***nyctaxifareraw*** en el contenedor predeterminado del clúster Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Los datos deben ahora en el almacenamiento de blobs de Windows Azure y listo para usarlo en el clúster HDInsight.

## <a name="#download-hql-files"></a>Inicie sesión en el nodo principal del clúster de Hadoop y y preparar los análisis de datos de exploración

>[AZURE.NOTE] Suele ser un **Administrador** de tareas.

Para obtener acceso el nodo principal del clúster para analizar los datos de exploración y hacia abajo de la muestra de los datos, siga el procedimiento descrito en [Access Hadoop clúster cabeza nodos](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

En este tutorial, usamos principalmente consultas escritas en [subárbol](https://hive.apache.org/), un lenguaje de consulta SQL similares, para realizar exploraciones datos preliminares. Las consultas de la sección se almacenan en archivos de .hql. Nos, a continuación, hacia abajo este datos de ejemplo para usarlo en Azure máquina aprendizaje para la creación de modelos.

Para preparar el clúster para analizar los datos de exploración, descargar los archivos de .hql que contiene la sección secuencias de comandos relevantes de [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) en un directorio local (C:\temp) en el nodo principal. Para ello, abra el **símbolo del sistema** desde el nodo principal del clúster y emita los siguientes dos comandos:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Estos dos comandos descargarán todos los archivos de .hql necesarios en este tutorial en el directorio local ***C:\temp & #92;*** en el nodo principal.

## <a name="#hive-db-tables"></a>Crear base de datos de sección y tablas divididas por mes

>[AZURE.NOTE] Suele ser un **Administrador** de tareas.

Se está listos crear tablas de la sección de Nueva York taxi dataset.
En el nodo principal del clúster Hadoop, abra la ***línea de comandos de Hadoop*** en el escritorio del nodo principal y escriba el directorio de la sección escribiendo el comando

    cd %hive_home%\bin

>[AZURE.NOTE] **Ejecutar todos los comandos de la sección de este tutorial desde la Papelera subárbol anterior o indicador del directorio. Esto se ocupará de cualquier problema de ruta de acceso automáticamente. Se utilizan los términos "Aviso de directorio subárbol", "sección Papelera / indicador del directorio" y "línea de comandos de Hadoop" indistintamente en este tutorial.**

Desde el símbolo de directorio de la sección, escriba el comando siguiente en Hadoop línea de comandos de nodo principal para enviar la consulta de sección para crear tablas y base de datos de sección:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Este es el contenido de la ***C:\temp\sample\_subárbol\_crear\_db\_y\_tables.hql*** archivo que crea subárbol base de datos ***nyctaxidb*** y tablas de ***viaje*** y ***tarifa***.

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Esta secuencia de comandos de la sección crea dos tablas:

* la tabla de "viaje" contiene detalles de ida y vuelta de cada carrera (detalles del controlador, hora de recopilación, distancia de viaje y veces)
* la tabla de "tarifa" contiene detalles de billete (cantidad de tarifa sugerencia, peajes e importe suplementos).

Si necesita ayuda adicional con estos procedimientos o si desea investigar los alternativos, vea la sección [Enviar subárbol consultas directamente desde la línea de comandos de Hadoop ](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="#load-data"></a>Cargar datos a las tablas de la sección particiones

>[AZURE.NOTE] Suele ser un **Administrador** de tareas.

El conjunto de datos de Nueva York taxi tiene una partición natural por mes, que se utilizan para habilitar los tiempos de procesamiento y la consulta. Los siguientes comandos de PowerShell (emitidos desde el directorio de sección mediante la **línea de comandos de Hadoop**) cargar datos a las tablas de subárbol "viaje" y "tarifa" divididas por mes.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

La *muestra\_subárbol\_cargar\_datos\_por\_partitions.hql* archivo contiene los siguientes comandos **cargar** .

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Tenga en cuenta que un número de consultas de la sección que aquí usamos en el proceso de exploración implica la búsqueda en una sola partición o solo un par de particiones. Pero estas consultas se podrían ejecutar en todos los datos.

### <a name="#show-db"></a>Mostrar bases de datos en el clúster HDInsight Hadoop

Para mostrar las bases de datos creados en clúster HDInsight Hadoop dentro de la ventana de la línea de comandos de Hadoop, ejecute el comando siguiente en la línea de comandos Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Mostrar las tablas de la sección en la base de datos nyctaxidb

Para mostrar las tablas de la base de datos nyctaxidb, ejecute el comando siguiente en la línea de comandos Hadoop:

    hive -e "show tables in nyctaxidb;"

Es posible confirmar que las tablas se dividen mediante el comando siguiente:

    hive -e "show partitions nyctaxidb.trip;"

A continuación se muestra el resultado esperado:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

Asimismo, es posible garantizar que se divide la tabla Tarifa mediante el comando siguiente:

    hive -e "show partitions nyctaxidb.fare;"

A continuación se muestra el resultado esperado:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Exploración de datos y de ingeniería de la característica de sección

>[AZURE.NOTE] Normalmente, esto es una tarea de **Científico de datos** .

La exploración de datos y la característica tareas para los datos cargados en las tablas de la sección de ingeniería se pueden utilizar las consultas de la sección. Estos son algunos ejemplos de tareas que le guiaremos a través de esta sección:

- Ver los registros de 10 mejores en ambas tablas.
- Explore las distribuciones de datos de algunos campos en diferentes ventanas de tiempo.
- Investigar la calidad de los datos de los campos de longitud y latitud.
- Generar etiquetas de clasificación binario y multiclass en función de la **sugerencia\_cantidad**.
- Generar características calculando la distancia de viaje directa.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Obtener información detallada: Ver los registros de 10 mejores de viaje de la tabla

>[AZURE.NOTE] Normalmente, esto es una tarea de **Científico de datos** .

Para ver el aspecto de los datos, se analiza 10 registros de cada tabla. Ejecute las siguientes dos consultas por separado desde el símbolo de directorio de sección en la consola de línea de comandos de Hadoop que inspeccionar los registros.

Para obtener los registros de 10 mejores en la tabla "viaje" del primer mes:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Para obtener los registros de 10 principales de la tabla "tarifa" del primer mes:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

A menudo es útil guardar los registros en un archivo para la visualización adecuada. Un pequeño cambio en la consulta anterior para ello:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Obtener información detallada: Ver el número de registros en cada una de las 12 particiones

>[AZURE.NOTE] Normalmente, esto es una tarea de **Científico de datos** .

De interés es cómo el número de viajes varía durante el año natural. Agrupar por mes nos permite ver el aspecto de esta distribución de viajes.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Nos ofrece el resultado:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

En este caso, la primera columna es el mes y la segunda es el número de viajes de ese mes.

Nos podemos contar el número total de registros en nuestro conjunto de datos de viaje mediante el comando siguiente en el símbolo de directorio subárbol.

    hive -e "select count(*) from nyctaxidb.trip;"

Da como resultado:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Con los comandos similares a los que se muestran para el conjunto de datos de viaje, podemos emitir consultas de la sección desde el indicador de directorio subárbol tarifa conjunto de datos validar el número de registros.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Nos ofrece el resultado:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Tenga en cuenta que se devuelve el mismo número exacto de viajes por mes para ambos conjuntos de datos. Proporciona la validación primera que ha cargado los datos correctamente.

Contar el número total de registros en el conjunto de datos de tarifa puede hacerse mediante el comando debajo de la pregunta de directorio de la sección:

    hive -e "select count(*) from nyctaxidb.fare;"

Da como resultado:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

El número total de registros de ambas tablas también es el mismo. Esto proporciona una segunda validación que ha cargado los datos correctamente.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploración: Distribución de ida y vuelta por medallion

>[AZURE.NOTE] Normalmente, esto es una tarea de **Científico de datos** .

En este ejemplo se identifica la medallion (taxi números) con más de 100 viajes dentro de un período de tiempo. Las ventajas de la consulta desde el acceso de la tabla con particiones ya que está sujeto a por partición variable **mes**. Resultados de la consulta se escriben en un queryoutput.tsv archivo local en `C:\temp` en el nodo principal.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Este es el contenido de *ejemplo\_subárbol\_viaje\_recuento\_por\_medallion.hql* archivo para inspección.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

El medallion en el conjunto de datos de Nueva York taxi identifica un único archivo cab. Podemos identificar qué organismos son "ocupados" preguntando cuáles más de un número determinado de viajes realizan en un período de tiempo determinado. En el ejemplo siguiente se identifica contenedores que realizan más de cien viajes en las primeras tres meses y guarda los resultados de la consulta en un archivo local, C:\temp\queryoutput.tsv.

Este es el contenido de *ejemplo\_subárbol\_viaje\_recuento\_por\_medallion.hql* archivo para inspección.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Desde el símbolo de directorio subárbol, emita el siguiente comando:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploración: Distribución de ida y vuelta medallion y hack_license

>[AZURE.NOTE] Normalmente, esto es una tarea de **Científico de datos** .

Al explorar un conjunto de datos, queremos con frecuencia examinar el número de apariciones de co de grupos de valores. Esta sección proporciona un ejemplo de cómo hacer esto para organismos y controladores.

La *muestra\_subárbol\_viaje\_recuento\_por\_medallion\_license.hql* archivo agrupa el conjunto de datos de tarifa en "medallion" y "hack_license" y devuelve los recuentos de cada combinación. A continuación se muestran su contenido.

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Esta consulta devuelve cab y combinaciones de controlador determinado ordenados por número descendente de viajes.

Desde el símbolo de directorio subárbol, ejecute:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Resultados de la consulta se escriben en un archivo local C:\temp\queryoutput.tsv.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitudelatitude-records"></a>Obtener información detallada: Evaluar la calidad de datos mediante la búsqueda de registros de latitud y longitud no válida

>[AZURE.NOTE] Normalmente, esto es una tarea de **Científico de datos** .

Un objetivo común de análisis de datos de exploración es Descartar registros no válidos o incorrectos. En el ejemplo de esta sección determina si la longitud o latitud campos contienen un valor extremo fuera del área de Nueva York. Dado que es probable que los valores de latitud y longitud incorrectos de estos registros, queremos eliminarlos de los datos que se utiliza para un modelado.

Este es el contenido de *ejemplo\_subárbol\_calidad\_assessment.hql* archivo para inspección.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Desde el símbolo de directorio subárbol, ejecute:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

El argumento *-S* incluido en este comando suprime la copia impresa de pantalla de estado de los trabajos de mapa/reducir subárbol. Esto es útil porque hace la pantalla de impresión de los resultados de consulta de la sección más legible.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Exploración: Distribuciones clase binario de sugerencias de viaje

> [AZURE.NOTE] Normalmente, esto es una tarea de **Científico de datos** .

Para el problema de clasificación binario descrito en la sección de [ejemplos de tareas de predicción](machine-learning-data-science-process-hive-walkthrough.md#mltasks) , resulta útil saber si se ha proporcionado una sugerencia o no. Esta distribución de sugerencias es binaria:

* Sugerencia dado (clase 1, sugerencia\_importe > $0)  
* ninguna sugerencia (clase 0, sugerencia\_cantidad = $0).

La *muestra\_subárbol\_superpuesto\_frequencies.hql* archivo se muestra a continuación para ello.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Desde el símbolo de directorio subárbol, ejecute:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Exploración: Distribuciones de clase en la configuración de multiclass

> [AZURE.NOTE] Normalmente, esto es una tarea de **Científico de datos** .

Para el problema de clasificación multiclass descrito en la sección de [ejemplos de tareas de predicción](machine-learning-data-science-process-hive-walkthrough.md#mltasks) este conjunto de datos también permite a una clasificación natural donde queremos predecir la cantidad de las sugerencias dado. Podemos utilizar clases para definir rangos de sugerencia en la consulta. Para obtener las distribuciones de clase para los distintos intervalos de ideas, usamos la *ejemplo\_subárbol\_sugerencia\_rango\_frequencies.hql* archivo. A continuación se muestran su contenido.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Desde la consola de línea de comandos de Hadoop, ejecute el siguiente comando:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-direct-distance-between-two-longitude-latitude-locations"></a>Obtener información detallada: Calcular la distancia directa entre dos ubicaciones de latitud y longitud

> [AZURE.NOTE] Normalmente, esto es una tarea de **Científico de datos** .

Tener una medida de la distancia directa nos permite averiguar la diferencia entre esta y la distancia de viaje real. Esta característica se motivar, señale que un viajero pueda reduce la probabilidad de sugerencia si averiguar que el controlador deliberadamente ha tomado por una ruta mucho más.

Para ver la comparación entre distancia de viaje real y la [Haversine distancia](http://en.wikipedia.org/wiki/Haversine_formula) entre dos puntos de latitud y longitud (la "" distancia), usamos la funciones trigonométricas disponibles dentro de la sección, así:

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

En la consulta anterior, R es el radio de la tierra en millas y pi se convierte en radianes. Tenga en cuenta que los puntos de latitud latitud se "filtran" para quitar los valores que están lejos del área de Nueva York.

En este caso, escribimos nuestros resultados en un directorio denominado "queryoutputdir". La secuencia de comandos que se muestra a continuación en primer lugar crea este directorio de resultados y, a continuación, ejecuta el comando de la sección.

Desde el símbolo de directorio subárbol, ejecute:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Resultados de la consulta se escriben en 9 blobs de Azure ***queryoutputdir/000000\_0*** a ***queryoutputdir/000008\_0*** en el contenedor predeterminado del clúster Hadoop.

Para ver el tamaño de los objetos binarios individuales, se ejecute el siguiente comando desde el símbolo del sistema de subárbol directorio:

    hdfs dfs -ls wasb:///queryoutputdir

Para ver el contenido de un archivo determinado, diga 000000\_0, usamos de Hadoop `copyToLocal` comando, por lo tanto.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [AZURE.WARNING] `copyToLocal`puede ser muy lento para archivos grandes y no se recomienda para su uso con ellos.  

Una ventaja clave de tener estos datos residen en una blobs de Windows Azure es que nos podemos explorar los datos en Azure máquina aprendizaje usando los [Datos de importación] [ import-data] módulo.


## <a name="#downsample"></a>Hacia abajo de modelos de datos y de compilación de ejemplo en aprendizaje de máquina de Azure

> [AZURE.NOTE] Normalmente, esto es una tarea de **Científico de datos** .

Después de la fase de análisis de datos de exploración, estamos listos para abajo muestra los datos para crear modelos en Azure el aprendizaje. En esta sección, le mostraremos cómo usar una consulta de la sección a hacia abajo muestra los datos, que se puede obtener acceso desde los [Datos de importación] [ import-data] módulo de aprendizaje del equipo de Azure.

### <a name="down-sampling-the-data"></a>Hacia abajo muestreo de los datos

Hay dos pasos de este procedimiento. En primer lugar, unirse a las tablas **nyctaxidb.trip** y **nyctaxidb.fare** tres de las teclas que se encuentran en todos los registros de: "medallion", "ataques\_licencia", y "recogida\_datetime". Nos generar una etiqueta de clasificación binario **superpuesto** y una etiqueta de clasificación de clase múltiples **sugerencia\_clase**.

Para que pueda usar la abajo muestra datos directamente de los [Datos de importación] [ import-data] módulo Azure el aprendizaje, es necesario almacenar los resultados de la consulta a una tabla interna de la sección anterior. En lo que sigue, creamos una tabla subárbol interna y rellenar su contenido con el combinados y datos de muestra.

La consulta aplica estándar subárbol funciones directamente para generar la hora del día, semana del año, día de la semana (el lunes significa 1 y 7 significa el domingo) de la "recogida\_datetime" campo y la distancia entre las ubicaciones de caída y recogida directa. Los usuarios pueden consultar [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) para obtener una lista completa de estas funciones.

La consulta, a continuación, hacia abajo ejemplos de los datos para que los resultados de la consulta pueden caber en el estudio de aprendizaje de máquina de Azure. Sólo 1% del conjunto de datos original se importa la Studio.

A continuación se muestran el contenido de *ejemplo\_subárbol\_preparar\_para\_aml\_full.hql* archivo que prepara los datos para el modelo de creación de aprendizaje del equipo de Azure.

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Para ejecutar esta consulta desde el símbolo del sistema de subárbol directorio:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Ahora tenemos una tabla interna "nyctaxidb.nyctaxi_downsampled_dataset" que se puede acceder usando los [Datos de importación] [ import-data] módulo de aprendizaje del equipo de Azure. Además, podemos utilizar este conjunto de datos para crear modelos de aprendizaje del equipo.  

### <a name="use-the-import-data-module-in-azure-machine-learning-to-access-the-down-sampled-data"></a>Utilizar el módulo de importar datos en Azure el aprendizaje para tener acceso a los datos muestras abajo

Como los requisitos previos para emitir consultas de sección en los [Datos de importación] [ import-data] módulo de aprendizaje del equipo de Azure, se necesitan acceso a un área de trabajo de aprendizaje de máquina Azure y acceso a las credenciales del clúster y su cuenta de almacenamiento asociado.

Algunos detalles de los [Datos de importación] [ import-data] módulo y los parámetros de entrada:

**URI del servidor HCatalog**: si el nombre de clúster es abc123, esto es simplemente: https://abc123.azurehdinsight.net

**Nombre de cuenta de usuario de Hadoop** : el nombre del usuario seleccionado para el clúster (**no** el nombre de usuario de acceso remoto)

**Hadoop contraseña de la cuenta de usuario** : la contraseña elegida para el clúster (**no** la contraseña de acceso remoto)

**Ubicación de datos de salida** : Esto es elegida como Azure.

**Nombre de la cuenta de almacenamiento de Azure** : nombre de la cuenta de almacenamiento predeterminado asociado con el clúster.

**Nombre del contenedor de Azure** : este es el nombre del contenedor predeterminado para el clúster y normalmente es el mismo que el nombre del clúster. En un clúster denominado "abc123", esto es simplemente abc123.

> [AZURE.IMPORTANT] **Cualquier tabla queremos consulta usando los [Datos de importación] [ import-data] módulo en Azure el aprendizaje debe ser una tabla interna.** Sugerencia para determinar si una tabla T en una base de datos D.db es una tabla interna es la siguiente.

Desde el símbolo de directorio subárbol, ejecute el comando:

    hdfs dfs -ls wasb:///D.db/T

Si la tabla es una tabla interna y se rellena, su contenido se debe mostrar aquí. Otra forma de determinar si una tabla es una tabla interna es usar el Explorador de almacenamiento de Azure. Utilizar para navegar hasta el nombre del contenedor predeterminado del clúster y, a continuación, filtrar por el nombre de tabla. Si la tabla y su contenido aparece, Esto confirma que es una tabla interna.

Esto es una instantánea de la consulta de sección y los [Datos de importación] [ import-data] módulo:

![](./media/machine-learning-data-science-process-hive-walkthrough/1eTYf52.png)

Observe que desde nuestro abajo muestras datos residen en el contenedor de forma predeterminada, la consulta de subárbol resultante de Azure máquina Learning es muy sencilla y es simplemente un "Seleccione * de nyctaxidb.nyctaxi\_reduce\_datos".

El conjunto de datos ahora puede utilizarse como punto de partida para crear modelos de aprendizaje del equipo.

### <a name="mlmodel"></a>Crear modelos en aprendizaje de máquina de Azure

Ahora es posible continuar con la creación del modelo e implementación de modelo de [Aprendizaje del equipo de Azure](https://studio.azureml.net). Los datos están listos para utilizar para resolver los problemas de predicción identificados anteriormente:

**1. clasificación binario**: predecir o no se pagó una sugerencia para un viaje.

**Estudiante utilizado:** Regresión logística de clase de dos

una. Para este problema, nuestro etiqueta de destino (o clase) es "superpuesto". Original muestra abajo dataset tiene algunas de las columnas que están pérdidas de destino para este ensayo de clasificación. En particular: sugerencia\_clase, sugerencia\_importe y total\_importe revelar información acerca de la etiqueta de destino que no está disponible en tiempo de pruebas. Se quita estas columnas consideración utilizando las [Seleccionar columnas de conjunto de datos] de[ select-columns] módulo.

La siguiente instantánea muestra nuestra ensayo predecir si no se pagó una sugerencia para un viaje determinado.

![](./media/machine-learning-data-science-process-hive-walkthrough/QGxRz5A.png)

b. Para este ensayo nuestras distribuciones de etiqueta de destino eran aproximadamente 1:1.

La siguiente instantánea muestra la distribución de sugerencia etiquetas de clase para el problema de clasificación binario.

![](./media/machine-learning-data-science-process-hive-walkthrough/9mM4jlD.png)

Como resultado, obtenemos un AUC de 0.987 como se muestra en la siguiente ilustración.

![](./media/machine-learning-data-science-process-hive-walkthrough/8JDT0F8.png)

**2. multiclass clasificación**: predecir el intervalo de importes de sugerencia de pago para el viaje, utilizando las clases definidas previamente.

**Estudiante utilizado:** Regresión logística multiclass

una. Para este problema es nuestra etiqueta de destino (o clase) "sugerencia\_clase" que puede realizar uno de cinco valores (0,1,2,3,4). Como en el caso de clasificación binario, tenemos algunas de las columnas que están pérdidas de destino para este ensayo. En particular: superpuesto, sugerencia\_cantidad, total\_importe revelar información acerca de la etiqueta de destino que no está disponible en tiempo de pruebas. Eliminamos estas columnas utilizando la [Seleccionar columnas de conjunto de datos] [ select-columns] módulo.

La siguiente instantánea muestra nuestra ensayo predecir en qué Papelera es probable que se dividen una sugerencia (clase 0: sugerencia = $0, clase 1: Sugerencia > $0 y sugerencia < = $5, clase 2: Sugerencia > $5 y sugerencia < = $10, clase 3: Sugerencia > $10 y sugerencia < = 20 $, clase 4: Sugerencia > $20)

![](./media/machine-learning-data-science-process-hive-walkthrough/5ztv0n0.png)

Ahora, le mostraremos el aspecto de nuestra distribución de clase de prueba real. Vemos que mientras prevalecen clase 0 y 1 de clase, las clases son poco frecuentes.

![](./media/machine-learning-data-science-process-hive-walkthrough/Vy1FUKa.png)

b. Para este ensayo, usamos una matriz confusión mirar nuestro precisión de predicción. Esto se muestra a continuación.

![](./media/machine-learning-data-science-process-hive-walkthrough/cxFmErM.png)

Tenga en cuenta que mientras la precisión de clase en las clases extendidas es bastante buena, el modelo no hace un buen trabajo de "aprendizaje" en las clases menos.


**3. tareas de regresión**: predecir la cantidad de sugerencia de pago para un viaje.

**Estudiante utilizado:** Árbol de decisión aumentada

una. Para este problema es nuestra etiqueta de destino (o clase) "sugerencia\_importe". En este caso son las pérdidas de nuestro destino: superpuesto, sugerencia\_clase, total\_importe; todas estas variables revelan información sobre la cantidad de sugerencia que no está disponible normalmente en tiempo de pruebas. Eliminamos estas columnas utilizando la [Seleccionar columnas de conjunto de datos] [ select-columns] módulo.

La belows instantánea muestra nuestra ensayo predecir la cantidad de la sugerencia dada.

![](./media/machine-learning-data-science-process-hive-walkthrough/11TZWgV.png)

b. Problemas de regresión, evaluar la precisión de nuestra predicción consultando el error al cuadrado en las predicciones, el coeficiente de determinación y similares. Se muestran a continuación.

![](./media/machine-learning-data-science-process-hive-walkthrough/Jat9mrz.png)

Vemos que sobre el coeficiente de determinación es 0.709, lo que implica aproximadamente el 71% de la varianza se explica por nuestros coeficientes modelo.

> [AZURE.IMPORTANT] Para obtener más información sobre el aprendizaje Azure y cómo acceder y usarlo, consulte [¿Qué es el aprendizaje?](machine-learning-what-is-machine-learning.md). Un recurso muy útil para la reproducción de un conjunto de aprendizaje experimentación en Azure el aprendizaje es la [Galería de inteligencia de Cortana](https://gallery.cortanaintelligence.com/). La Galería cubre una gama de experimentación y proporciona una introducción exhaustiva en la gama de capacidades de aprendizaje del equipo de Azure.

## <a name="license-information"></a>Información de licencia

Microsoft compartir este tutorial de ejemplo y sus correspondientes secuencias de comandos en la licencia MIT. Compruebe el archivo LICENSE.txt el directorio del código de ejemplo en GitHub para obtener más detalles.

## <a name="references"></a>Referencias

• La [página de descarga de viajes de Nueva York Taxi Andrés Monroy](http://www.andresmh.com/nyctaxitrips/)  
• [Frustrados Taxi viaje datos de Nueva York por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Taxi de Nueva York y Limousine Comisión investigación y estadísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
