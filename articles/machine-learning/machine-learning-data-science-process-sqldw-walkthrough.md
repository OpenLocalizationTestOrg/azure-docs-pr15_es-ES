<properties
    pageTitle="El proceso de ciencias de datos de grupo de acción: usar el almacén de datos SQL | Microsoft Azure"
    description="Proceso de análisis avanzadas y tecnología en acción"  
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
    ms.date="06/24/2016"
    ms.author="bradsev;hangzh;weig"/>


# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>El proceso de ciencias de datos de grupo de acción: usar el almacén de datos SQL

En este tutorial, le guiaremos a través de la creación e implementación de una máquina de modelo con el almacén de datos de SQL (SQL DW) de aprendizaje para un conjunto de datos esté disponible públicamente--el conjunto de datos de [Nueva York Taxi viajes](http://www.andresmh.com/nyctaxitrips/) . El modelo de clasificación binario construido prevé si no se paga una sugerencia para un viaje y modelos de clasificación multiclass y regresión también se tratan que predecir la distribución de los importes de sugerencia de pago.

El procedimiento sigue el flujo de trabajo del [Proceso de ciencias de datos de equipo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) . Le mostraremos cómo configurar un entorno de ciencias datos, características de cómo cargar los datos en DW SQL y cómo usar DW SQL o en un bloc de notas de IPython para explorar los datos e ingeniería y modelo. A continuación, le mostraremos cómo crear e implementar un modelo de aprendizaje del equipo de Azure.


## <a name="dataset"></a>El conjunto de datos de Nueva York Taxi viajes

Los datos de Nueva York Taxi viaje constan de aproximadamente 20GB de archivos CSV comprimidos (GB de ~ 48 sin comprimir), cada viaje de más de 173 millones de viajes individuales y las tarifas de las grabaciones de pago. Cada registro de viaje incluye las ubicaciones de entrega y recogida y horas, el número de licencias piratería anonymized (controlador) y el número de medallion (identificador exclusivo de taxi). Los datos cubre todos los viajes del año 2013 y está disponible en los dos conjuntos de datos siguientes para cada mes:

1. El archivo **trip_data.csv** contiene los detalles de viaje, como el número de personas, recogida y puntos de caída, duración de viaje y longitud de viaje. Estos son algunos registros de ejemplo:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. El archivo **trip_fare.csv** contiene los detalles de la tarifa de pago para cada viaje, como el tipo de pago, importe tarifa, suplemento y los impuestos, sugerencias y peajes y la cantidad total pagada. Estos son algunos registros de ejemplo:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La **clave única** utilizada para unirse a viajes\_datos y viaje\_tarifa se compone de los tres campos siguientes:

- Medallion,
- ataques\_licencia y
- recogida\_datetime.

## <a name="mltasks"></a>Tres tipos de tareas de predicción de direcciones

Nos formular tres problemas de predicción basándose en los *sugerencia\_importe* para ilustrar tres tipos de tareas de modelado:

1. **Clasificación binario**: predecir o no una sugerencia se pagó por un viaje, es decir, un *sugerencia\_importe* que sea mayor de 0 $ es un ejemplo positivo, mientras un *sugerencia\_importe* $ 0 es un ejemplo negativo.

2. **Clasificación multiclass**: predecir el rango de sugerencia de pago para el viaje. Dividimos el *sugerencia\_importe* en cinco clases o ubicaciones:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Tarea de regresión**: predecir la cantidad de sugerencia de pago para un viaje.  


## <a name="setup"></a>Configurar el entorno de ciencias datos Azure para funciones analíticas avanzadas

Para configurar su entorno de ciencias de datos de Azure, siga estos pasos.

**Crear su propia cuenta de almacenamiento de blobs de Windows Azure**

- Cuando se aprovisiona su propio almacenamiento de blobs de Windows Azure, elija una ubicación geográfica para el almacenamiento de blobs de Windows Azure en o más cerca posible a **Sur Central US**, que es donde se almacenan los datos de Nueva York Taxi. Se copiarán los datos de uso AzCopy desde el contenedor de almacenamiento de blobs público a un contenedor en su cuenta de almacenamiento. Más cerca el almacenamiento de blobs de Windows Azure es Sur Central de EE, más rápido se completará esta tarea (paso 4).
- Para crear su propia cuenta de almacenamiento de Azure, siga los pasos indicados en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md). Asegúrese de tomar notas sobre los valores de las siguientes credenciales de cuenta de almacenamiento, tal como se necesitará más adelante en este tutorial.

  - **Nombre de la cuenta de almacenamiento**
  - **Clave de cuenta de almacenamiento**
  - **Nombre del contenedor** (a la que desea que los datos se almacenan en el almacenamiento de blobs de Windows Azure)

**Aprovisionar la instancia de DW de SQL Azure.**
Siga la documentación en [crear un almacén de datos de SQL](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) proporcionando una instancia de almacén de datos de SQL. Asegúrese de que hacer anotaciones en las siguientes credenciales de almacenamiento de datos de SQL que se utilizará en los pasos siguientes.

  - **Nombre del servidor**: <server Name>. database.windows.net
  - **Nombre SQLDW (base de datos)**
  - **Nombre de usuario**
  - **Contraseña**

**Instale 2015 de Visual Studio y las herramientas de datos SQL Server.** Para obtener instrucciones, consulte [instalar 2015 de Visual Studio o SSDT (herramientas de datos de SQL Server) para el almacén de datos de SQL](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Conectarse a su DW SQL Azure con Visual Studio.** Para obtener instrucciones, consulte los pasos 1 y 2 en [Conectar con el almacén de datos de SQL Azure con Visual Studio](../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

>[AZURE.NOTE] Ejecutar la siguiente consulta SQL en la base de datos que creó en el almacén de datos de SQL (en lugar de la consulta proporcionada en el paso 3 del tema conectar,) para **crear una clave principal**.

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Crear un área de trabajo de aprendizaje del equipo de Azure en la suscripción de Azure.** Para obtener instrucciones, vea [crear un área de trabajo de aprendizaje del equipo de Azure](machine-learning-create-workspace.md).

## <a name="getdata"></a>Cargar los datos en el almacén de datos SQL

Abra la consola de comandos de Windows PowerShell. Ejecute el siguiente PowerShell archivos que se comparten con usted en Github en un directorio local que especifique con el parámetro de comandos de comandos para descargar el ejemplo SQL *- DestDir*. Puede cambiar el valor del parámetro *-DestDir* a cualquier directorio local. Si no existe *- DestDir* , se creará la secuencia de comandos de PowerShell.

>[AZURE.NOTE] Debe **Ejecutar como administrador** cuando ejecutando el siguiente script de PowerShell si su directorio *DestDir* necesita privilegios de administrador para crear o escribir en él.

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Después de ejecutar correctamente, su directorio de trabajo actual cambia a *- DestDir*. Debería poder ver pantalla como las siguientes:

![][19]

En su *-DestDir*, ejecute el siguiente script de PowerShell en modo de administrador:

    ./SQLDW_Data_Import.ps1

Cuando se ejecuta la secuencia de comandos de PowerShell por primera vez, le pedirá que introduzca la información de su DW de SQL Azure y su cuenta de almacenamiento de blobs de Windows Azure. Cuando termine de esta secuencia de comandos de PowerShell ejecuta por primera vez, las credenciales de entrada habrá ha escrito a un archivo de configuración SQLDW.conf en el directorio de trabajo actual. La ejecución de este archivo de script de PowerShell futura tiene la opción leer que todos los parámetros de este archivo de configuración de necesarios. Si necesita cambiar algunos parámetros, puede elegir para los parámetros en la pantalla al mensaje de entrada eliminando este archivo de configuración y los valores de parámetros de entrada cuando se le solicite o cambiar los valores de parámetro editando el archivo SQLDW.conf en su directorio *- DestDir* .

>[AZURE.NOTE] Para evitar conflictos de nombres de esquema con los que ya existen en el DW de SQL Azure, al leer parámetros directamente desde el archivo SQLDW.conf, un número aleatorio de 3 dígitos se agrega el nombre de esquema desde el archivo SQLDW.conf como el nombre del esquema de forma predeterminada para cada ejecución. La secuencia de comandos de PowerShell puede pedirle un nombre de esquema: puede especificar el nombre a discreción del usuario.

Este archivo de **script de PowerShell** realiza las siguientes tareas:

- **Descarga e instala AzCopy**, si ya no está instalado AzCopy

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
            Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }

- **Copia de los datos a su cuenta de almacenamiento de blobs privado** desde el público blob con AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- **Carga datos mediante Polybase (al ejecutar LoadDataToSQLDW.sql) a su DW de SQL Azure** desde su cuenta de almacenamiento de blobs privada con los comandos siguientes.

    - Crear un esquema

            EXEC (''CREATE SCHEMA {schemaname};'');

    - Crear una credencial de ámbito de la base de datos

            CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
            WITH IDENTITY = ''asbkey'' ,
            Secret = ''{StorageAccountKey}''

    - Crear un origen de datos externo para una blobs de Windows Azure almacenamiento

            CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

            CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

    - Crear un formato de archivo externo para un archivo csv. Sin comprimir datos y los campos están separados por el carácter de barra vertical.

            CREATE EXTERNAL FILE FORMAT {csv_file_format}
            WITH
            (   
                FORMAT_TYPE = DELIMITEDTEXT,
                FORMAT_OPTIONS  
                (
                    FIELD_TERMINATOR ='','',
                    USE_TYPE_DEFAULT = TRUE
                )
            )
            ;

    - Crear tablas de viaje para el conjunto de datos de Nueva York taxi y tarifa externo en el almacenamiento de blobs de Windows Azure.

            CREATE EXTERNAL TABLE {external_nyctaxi_fare}
            (
                medallion varchar(50) not null,
                hack_license varchar(50) not null,
                vendor_id char(3),
                pickup_datetime datetime not null,
                payment_type char(3),
                fare_amount float,
                surcharge float,
                mta_tax float,
                tip_amount float,
                tolls_amount float,
                total_amount float
            )
            with (
                LOCATION    = ''/nyctaxifare/'',
                DATA_SOURCE = {nyctaxi_fare_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12     
            )  


            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                medallion varchar(50) not null,
                hack_license varchar(50)  not null,
                vendor_id char(3),
                rate_code char(3),
                store_and_fwd_flag char(3),
                pickup_datetime datetime  not null,
                dropoff_datetime datetime,
                passenger_count int,
                trip_time_in_secs bigint,
                trip_distance float,
                pickup_longitude varchar(30),
                pickup_latitude varchar(30),
                dropoff_longitude varchar(30),
                dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12         
            )

    - Cargar datos de tablas externas en el almacenamiento de blobs de Windows Azure al almacén de datos de SQL

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Crear una tabla de datos de ejemplo (NYCTaxi_Sample) e insertar datos a ella desde la selección de consultas SQL en las tablas de ida y vuelta y tarifa. (Algunos pasos de este tutorial debe usar esta tabla de ejemplo).

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

La ubicación geográfica de sus cuentas de almacenamiento afecta a los tiempos de carga.

>[AZURE.NOTE] Según la ubicación geográfica de su cuenta de almacenamiento de blobs privado, el proceso de copiar datos de un blob pública a su cuenta de almacenamiento privado puede tardar unos 15 minutos, o incluso más tiempo, y el proceso de carga de datos de su cuenta de almacenamiento a su DW de SQL Azure podría tardar 20 minutos o más.  

Debe decidir qué hacer si tiene archivos de origen y destino duplicados.

>[AZURE.NOTE] Si el archivo .csv archivos para copiar desde el almacenamiento de blobs público a su cuenta de almacenamiento de blobs privada ya existen en su cuenta de almacenamiento de blobs privado, AzCopy le preguntará si desea sobrescribirlos. Si no desea sobrescribirlos, entrada **n** cuando se le solicite. Si desea sobrescribir **todos** ellos, entrada **a** cuando se le solicite. También puede ver la entrada **y** para sobrescribir archivos .csv individualmente.

![Trazar #21][21]

Puede usar sus propios datos. Si los datos están en el equipo local en la aplicación de la vida real, aún puede usar AzCopy para cargar los datos locales en el almacenamiento de blobs de Windows Azure privado. Solo tiene que cambiar la ubicación de **origen** , `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, en el comando AzCopy del archivo de script de PowerShell en el directorio local que contenga los datos.


>[AZURE.TIP] Si los datos ya están en el almacenamiento de blobs de Windows Azure privado en la aplicación de la vida real, puede omitir el paso de AzCopy en la secuencia de comandos de PowerShell y cargar directamente los datos a DW de SQL Azure. Esto requerirá ediciones adicionales de la secuencia de comandos para ajustar al formato de los datos.


Esta secuencia de comandos de Powershell también conecta la información de DW de SQL Azure en los archivos de ejemplo de exploración de datos SQLDW_Explorations.sql, SQLDW_Explorations.ipynb y SQLDW_Explorations_Scripts.py para que estos tres archivos están listos para ser intentado inmediatamente después de que finalice la secuencia de comandos de PowerShell.

Después de una ejecución correcta, aparecerá la pantalla como las siguientes:

![][20]

## <a name="dbexplore"></a>Exploración de datos y de ingeniería de la característica en el almacén de datos de SQL Azure

En esta sección, realizar la exploración de datos y generación de característica ejecutando consultas SQL contra DW de SQL Azure directamente con **Datos de Visual Studio Tools**. Todas las consultas SQL que se utilizan en esta sección se pueden encontrar en la secuencia de comandos denominada *SQLDW_Explorations.sql*. Este archivo ya se ha descargado en su directorio local, la secuencia de comandos de PowerShell. También puede recuperar desde [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Pero el archivo en Github no tiene la información de DW de SQL Azure conectada.

Conectarse a su DW de SQL Azure con Visual Studio con el nombre de inicio de sesión de DW SQL y la contraseña y abra el **Explorador de objetos de SQL** para confirmar que la base de datos y las tablas se han importado. Recuperar el archivo *SQLDW_Explorations.sql* .

>[AZURE.NOTE] Para abrir un editor de consultas de almacén de datos paralelo (PDW), use el comando **Nueva consulta** mientras el PDW está seleccionado en el **Explorador de objetos de SQL**. El editor de consultas SQL estándar no es compatible con PDW.

Estas son el tipo de datos realizan tareas de generación de exploración y características en esta sección:

- Explore las distribuciones de datos de algunos campos en diferentes ventanas de tiempo.
- Investigar la calidad de los datos de los campos de longitud y latitud.
- Generar etiquetas de clasificación binario y multiclass en función de la **sugerencia\_cantidad**.
- Generar características y calcular o comparar distancia de viaje.
- Combine las dos tablas y extraer una muestra aleatoria que se utilizará para generar modelos.

### <a name="data-import-verification"></a>Comprobación de importación de datos

Estas consultas proporcionan una comprobación rápida del número de filas y columnas de las tablas que se rellena con una versión anterior bloque paralelo de Polybase importan,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Salida:** Debe obtener 173,179,759 filas y columnas de 14.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploración: Distribución de ida y vuelta por medallion

Esta consulta de ejemplo identifica medallions (taxi números) que completado viajes más de 100 en un período de tiempo especificado. La consulta debería beneficiarse el acceso de la tabla con particiones puesto que está sujeto a por el esquema de partición de **recogida\_datetime**. Consultar el conjunto de datos completa también hará que el uso de la tabla con particiones o análisis de índice.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Salida:** La consulta debe devolver una tabla con filas que especifica la 13,369 medallions (taxis) y el número de viajes realizarla de 2013. La última columna contiene el recuento del número de viajes completado.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploración: Distribución de ida y vuelta medallion y hack_license

Este ejemplo identifica la medallions (taxi números) y hack_license números (controladores) que completado más de 100 viajes dentro de un período de tiempo especificado.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Salida:** La consulta debe devolver una tabla con 13,369 filas que especifica el automóvil 13,369 o controlador de identificadores que se ha completado más que viajes 100 de 2013. La última columna contiene el recuento del número de viajes completado.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Evaluación de la calidad de datos: comprobar registros con incorrecto de longitud o latitud

En este ejemplo investiga si cualquiera de los campos de longitud o latitud contener un valor no válido (radianes grados deben estar comprendido entre -90 y 90), o tiene (0, 0) coordenadas.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Salida:** La consulta devuelve 837,467 viajes que tienen los campos de longitud o latitud no válidos.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Obtener información detallada: Superpuesto frente a la distribución de viajes no superpuesto

Este ejemplo busca el número de viajes que se han superpuesto frente el número que no se han superpuesto en un período de tiempo especificado (o en el conjunto de datos completa si cubra todo el año como configurarla aquí). Esta distribución refleja la distribución de etiqueta binario que más adelante se utilizará para el modelo de clasificación binario.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Salida:** La consulta debe devolver las siguientes frecuencias de sugerencia para el año 2013: 90,447,622 superpuesto y 82,264,709 no punta.

### <a name="exploration-tip-classrange-distribution"></a>Exploración: Distribución de clase o rango de sugerencia

En este ejemplo se calcula la distribución de rangos de sugerencia en un período de tiempo (o en el conjunto de datos completa si cubra todo el año). Esta es la distribución de las clases de etiqueta que se usará más adelante para un modelado de clasificación multiclass.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Resultado:**

|tip_class  | tip_freq |
| --------- | -------|
|1  | 82230915 |
|2  | 6198803 |
|3  | 1932223 |
|0  | 82264625 |
|4  | 85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Obtener información detallada: Calcular y comparar distancia de viaje

Este ejemplo convierte la entrega y recogida de longitud y latitud geografía de SQL de destino, calcula la distancia de viaje con diferencia de puntos de geografía SQL y devuelve una muestra aleatoria de los resultados de la comparación. En el ejemplo se limita los resultados a coordenadas válidas usando solo la consulta de evaluación de calidad de datos tratada anteriormente.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Ingeniería de la característica mediante funciones de SQL

A veces funciones SQL pueden ser una opción eficaz de ingeniería de la característica. En este tutorial, se ha definido una función SQL para calcular la distancia entre las ubicaciones de caída y recogida directa. Puede ejecutar las siguientes secuencias de comandos SQL en **Visual Studio Tools de datos**.

Aquí tiene el script SQL que define la función de la distancia.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

Aquí tiene un ejemplo llamar a esta función para generar características en la consulta SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Salida:** Esta consulta genera una tabla (con 2,803,538 filas) con recogida y caída latitudes y longitudes y la distancia directa correspondiente en millas. Estos son los resultados de las 3 primeras filas:

||pickup_latitude | pickup_longitude    | dropoff_latitude |dropoff_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1  | 40.731804 | -74.001083 | 40.736622 | -73.988953 | .7169601222 |
|2  | 40.715794 | -74,010635 | 40.725338 | -74.00399 | .7448343721 |
|3  | 40.761456 | -73.999886 | 40.766544 | -73.988228 | 0.7037227967 |



### <a name="prepare-data-for-model-building"></a>Preparar los datos para la creación de modelo

La siguiente consulta combinaciones la **nyctaxi\_viaje** y **nyctaxi\_tarifa** tablas, genera una clasificación binario etiqueta **superpuesto**, una etiqueta de clasificación de clase múltiples **sugerencia\_clase**y extrae una muestra el conjunto de datos combinado completa. El muestreo se realiza recuperar un subconjunto de los viajes en función de la hora de recopilación.  Esta consulta se puede copiar y pegar directamente en el [Aprendizaje Studio de Azure máquina](https://studio.azureml.net) [Importar datos] [ import-data] módulo de recopilación de datos directamente desde la instancia de base de datos SQL en Azure. Excluye los registros con incorrecto (0, 0) coordenadas.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Cuando esté listo para continuar con el aprendizaje Azure, puede:  

1. Guardar la consulta SQL final para extraer y los datos de ejemplo y copiar y pegar la consulta directamente en un [Importar datos] [ import-data] módulo en Azure aprendizaje del equipo, o
2. Conservar los datos muestras e ingeniería planea usar para el modelo de creación en una nueva tabla de SQL DW y use la nueva tabla en los [Datos de importación] [ import-data] módulo de aprendizaje del equipo de Azure. La secuencia de comandos de PowerShell en el paso anterior ha hecho esto. Puede leer directamente desde esta tabla en el módulo de importar datos.


## <a name="ipnb"></a>Exploración de datos y de ingeniería de la característica en el Bloc de notas de IPython

En esta sección, se realizará la exploración de datos y generación de característica con ambos Python y consultas SQL en el DW SQL crean anteriormente. Un bloc de notas de ejemplo IPython denominado **SQLDW_Explorations.ipynb** y un archivo de script de Python **SQLDW_Explorations_Scripts.py** se han descargado en su directorio local. También están disponibles en [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Estos dos archivos son idénticos en secuencias de comandos de Python. El archivo de script de Python se proporciona en caso de que no tiene un servidor de Bloc de notas de IPython. Estos dos archivos Python de muestra están diseñados en **Python 2.7**.

La información necesaria de DW de SQL Azure en el ejemplo IPython Bloc de notas y en el archivo de script de Python descargan en su equipo local está conectada la secuencia de comandos de PowerShell previamente. Son ejecutables sin ninguna modificación.

Si ya ha configurado un área de trabajo AzureML, puede cargar el Bloc de notas de IPython de ejemplo en el servicio de Bloc de notas de AzureML IPython directamente e iniciar ejecutarlo. Estos son los pasos para cargar el Bloc de notas de AzureML IPython servicio:

1. Inicie sesión en el área de trabajo de AzureML, haga clic en "Studio" en la parte superior y haga clic en "" blocs de notas en el lado izquierdo de la página web.

    ![Trazar #22][22]

2. Haga clic en "Nuevo" en la esquina inferior izquierda de la página web y a continuación, seleccione "Python 2". A continuación, proporcione un nombre para el Bloc de notas y haga clic en la marca de verificación para crear el nuevo bloc de notas de IPython en blanco.

    ![Trazar #23][23]

3. Haga clic en el símbolo "Jupyter" en la esquina superior izquierda del nuevo bloc de notas de IPython.

    ![Trazar #24][24]

4. Arrastre y coloque el ejemplo IPython Bloc de notas a la página de **árbol** de su servicio de Bloc de notas de AzureML IPython y haga clic en **cargar**. A continuación, en el ejemplo que se cargarán IPython Bloc de notas en el servicio de Bloc de notas de AzureML IPython.

    ![Trazar #25][25]

Para ejecutar el ejemplo IPython Bloc de notas o la Python script de archivo, el siguiente Python paquetes son necesarias. Si está utilizando el servicio de Bloc de notas de AzureML IPython, estos paquetes se han preinstalado.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

La secuencia recomendada al generar las soluciones analíticas avanzadas en AzureML con datos de gran tamaño es la siguiente:

- Leer en una pequeña muestra de los datos en un marco de datos en memoria.
- Realizar algunas visualizaciones y exploraciones usando los datos muestras.
- Experimentar con ingeniería característica usando los datos muestras.
- Para la exploración de datos más grande, manipulación de datos e ingeniería de la característica, use Python para emitir consultas de SQL directamente en el DW SQL.
- Decida el tamaño de la muestra sea adecuado para la creación del modelo de aprendizaje del equipo de Azure.

Las siguientes son unos característica ejemplos de ingeniería, visualización de datos y la exploración de datos. Encontrará más exploraciones de datos en el Bloc de notas de IPython de ejemplo y el archivo de script de ejemplo Python.

### <a name="initialize-database-credentials"></a>Inicializar las credenciales de la base de datos

Inicializar la configuración de conexión de base de datos en las variables siguientes:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Crear la conexión de base de datos

Aquí es la cadena de conexión que crea la conexión a la base de datos.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Número de filas y columnas de tabla < nyctaxi_trip >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de filas = 173179759  
- Número total de columnas = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Número de filas y columnas de tabla < nyctaxi_fare >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de filas = 173179759  
- Número total de columnas = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Lectura de una muestra de datos pequeña de la base de datos del almacén de datos de SQL

    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tiempo para leer que la tabla de ejemplo es 14.096495 segundos.  
Número de filas y columnas recuperados = (1000, 21).

### <a name="descriptive-statistics"></a>Estadística descriptiva

Ahora ya está listo para explorar los datos muestras. Empezaremos con examina algunas Estadística descriptiva para el **viaje\_distancia** (o cualquier otro campo decide especificar).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualización: Ejemplo de trazado del cuadro

A continuación, veremos el trazado del cuadro de la distancia de ida y vuelta visualizar la quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Trazar #1][1]

### <a name="visualization-distribution-plot-example"></a>Visualización: Ejemplo de trazado de distribución

Trazados que visualización la distribución y la distancia de viaje muestra un histograma.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Trazar #2][2]

### <a name="visualization-bar-and-line-plots"></a>Barras de visualización: Y líneas trazados

En este ejemplo, hemos la distancia de viaje en cinco clases de clase y visualizar los resultados binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos trazar la distribución de ubicación anterior en una barra o línea de trazado con:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Trazar #3][3]

y

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Trazar #4][4]

### <a name="visualization-scatterplot-examples"></a>Visualización: Ejemplos de Scatterplot

Le mostraremos gráfico de dispersión entre **viaje\_tiempo\_en\_seg** y **viaje\_distancia** para ver si hay cualquier correlación

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Trazar #6][6]

Del mismo modo podemos comprobar la relación entre **tasa\_código** y **viaje\_distancia**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Trazar #8][8]


### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Exploración de datos en datos muestras el uso de consultas SQL en el Bloc de notas de IPython

En esta sección, nos explorar distribuciones de datos usando los datos muestras que se conservan en la nueva tabla que se creó anteriormente. Tenga en cuenta que se pueden realizar exploraciones similar con las tablas originales.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploración: Número de filas y columnas de la tabla muestra de informe

    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Obtener información detallada: Distribución de recorrido y no superpuesto

    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Exploración: Distribución de clase de sugerencia

    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Obtener información detallada: Trazar la distribución de sugerencia por clase

    tip_class_dist['tip_freq'].plot(kind='bar')

![Trazar #26][26]


#### <a name="exploration-daily-distribution-of-trips"></a>Obtener información detallada: Distribución diaria de viajes

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploración: Distribución de ida y vuelta por medallion

    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploración: Distribución de ida y vuelta por licencia medallion y piratería

    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Exploración: Distribución de tiempo de ida y vuelta

    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Exploración: Distribución de distancia de viaje

    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Exploración: Distribución de tipo de pago

    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Compruebe la forma final de la tabla featurized

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Crear modelos en aprendizaje de máquina de Azure

Estamos listos para pasar a la compilación del modelo e implementación de modelo de [Aprendizaje del equipo de Azure](https://studio.azureml.net). Los datos están listos para usarse en cualquiera de los problemas de predicción identificados anteriormente, es decir:

1. **Clasificación binario**: predecir o no se pagó una sugerencia para un viaje.

2. **Clasificación multiclass**: predecir el rango de sugerencia de pago, según las clases definidas previamente.

3. **Tarea de regresión**: predecir la cantidad de sugerencia de pago para un viaje.  



Para iniciar el ejercicio de modelado, inicie sesión en el área de trabajo de **Aprendizaje del equipo de Azure** . Si aún no ha creado una máquina de aprendizaje de área de trabajo, consulte [crear un área de trabajo de Azure m](machine-learning-create-workspace.md).

1. Para empezar con el aprendizaje Azure, consulte [¿Qué es Studio de aprendizaje del equipo de Azure?](machine-learning-what-is-ml-studio.md)

2. Inicie sesión en la [máquina Azure Studio de aprendizaje](https://studio.azureml.net).

3. La página de inicio de Studio proporciona un amplio abanico de información, vídeos, tutoriales, vínculos a la referencia de módulos y otros recursos. Para obtener más información sobre el aprendizaje Azure, consulte el [Centro de documentación de aprendizaje de máquina de Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Un ensayo formación típico consta de los siguientes pasos:

1. Crear un ensayo **+ nuevo** .
2. Obtener los datos en Azure m.
3. Procesar previamente, transformar y manipular los datos según sea necesario.
4. Generar características según sea necesario.
5. Dividir los datos en curso, validación o comprobación de conjuntos de datos (o tiene conjuntos de datos independiente para cada uno).
6. Seleccione uno o varios algoritmos de aprendizaje de máquina según el problema de aprendizaje para resolver. Por ejemplo, clasificación binario, clasificación multiclass, regresión.
7. Entrenar a uno o varios de los modelos con el conjunto de datos de aprendizaje.
8. Puntuación del conjunto de datos de validación con los modelos capacitados.
9. Evaluar los modelos para calcular la métrica relevante para el problema de aprendizaje.
10. Bien ajustar los modelos y seleccione el modelo mejor para implementar.

En este ejercicio, hemos hecho explorado e ingeniería los datos en el almacén de datos de SQL y decidió recopilar en Azure m el tamaño de la muestra. Aquí tenemos el procedimiento para crear uno o varios de los modelos de predicción:

1. Obtener los datos en m Azure con los [Datos de importación] [ import-data] módulo, disponible en la sección **datos de entrada y salida** . Para obtener más información, vea el [Importar datos] [ import-data] página de referencia de módulo.

    ![Datos de Azure importar v][17]

2. Seleccione la **Base de datos de SQL Azure** como el **origen de datos** en el panel **Propiedades** .

3. Escriba el nombre DNS de la base de datos en el campo **nombre de servidor de base de datos** . Formato:`tcp:<your_virtual_machine_DNS_name>,1433`

4. Escriba el **nombre de la base de datos** en el campo correspondiente.

5. Escriba el *nombre de usuario SQL* en el **nombre de cuenta de usuario de servidor**y la *contraseña* en la **contraseña de cuenta de usuario de servidor**.

6. Active la opción **Aceptar cualquier certificado de servidor** .

7. En el área de texto de edición de **consulta de base de datos** , pegue la consulta que extrae el necesario campos (incluidos los campos calculados, como las etiquetas) de la base de datos y hacia abajo ejemplos de los datos que el tamaño de la muestra que desee.

Es un ejemplo de un ensayo de clasificación binario leer datos directamente de la base de datos de almacén de datos SQL en la figura siguiente (recuerde para reemplazar la tabla nombres nyctaxi_trip y nyctaxi_fare por el nombre de esquema y los nombres de tabla que se utiliza en el tutorial). Puede construir experimentación similar para clasificación multiclass y problemas de regresión.

![Azure tren m][10]

> [AZURE.IMPORTANT] En el modelo de datos extracción y muestreo ejemplos de consulta se muestran en las secciones anteriores, **todas las etiquetas para los ejercicios tres modelos se incluyen en la consulta**. Un paso importante (obligatorio) en cada uno de los ejercicios de modelado es **Excluir** las etiquetas innecesarias para los otros dos problemas y cualquier otro **pérdidas de destino**. Por ejemplo, cuando se usa la clasificación binario, use la etiqueta **superpuesto** y excluir los campos **sugerencia\_clase**, **sugerencia\_importe**, y **total\_cantidad**. Los últimos son pérdidas de destino ya que implican la sugerencia de pago.
>
> Para excluir las columnas innecesarias o pérdidas de destino, puede usar las [Seleccionar columnas de conjunto de datos] [ select-columns] módulo o los [Metadatos de editar][edit-metadata]. Para obtener más información, vea [Seleccionar columnas de conjunto de datos] [ select-columns] y [Editar los metadatos] [ edit-metadata] hacen referencia a las páginas.

## <a name="mldeploy"></a>Implementar modelos en aprendizaje de máquina de Azure

Cuando el modelo esté listo, puede implementar fácilmente como un servicio web directamente desde el ensayo. Para obtener más información sobre cómo implementar los servicios web de Azure m, vea [implementar un servicio web de aprendizaje del equipo de Azure](machine-learning-publish-a-machine-learning-web-service.md).

Para implementar un servicio web nuevo, debe:

1. Crear un ensayo puntuación.
2. Implementar el servicio web.

Para crear un ensayo puntuación desde un ensayo de formación **terminado** , haga clic en **Crear puntuación experimentar** en la barra de acción inferior.

![Puntuación de Azure][18]

Aprendizaje de máquina Azure intentará crear un ensayo puntuación en función de los componentes de ensayo de aprendizaje. En particular, podrá:

1. Guarde el modelo capacitado y quite los módulos de formación de modelo.
2. Identificar un lógico **puerto de entrada** para representar el esquema de datos de entrada esperado.
3. Identificar un lógico **puerto de salida** para representar el esquema de salida del servicio web esperado.

Cuando se crea el ensayo puntuación, revise y realice ajustar según sea necesario. Un ajuste típico es reemplazar el conjunto de datos de entrada o la consulta con que excluye los campos de etiqueta, como estos no estará disponibles cuando llame al servicio. También es una buena práctica para reducir el tamaño de la entrada del conjunto de datos o una consulta a algunos registros, suficiente para indicar el esquema de entrada. Para el puerto de salida, es común para incluir solo las **Etiquetas de puntuación** y **Las probabilidades de puntuación** en el resultado con las [Seleccionar columnas de conjunto de datos] y excluir todas las entradas de campos[ select-columns] módulo.

Se proporciona un ejemplo puntuación ensayo en la siguiente ilustración. Cuando esté listo para implementar, haga clic en el botón **Publicar el servicio de WEB** en la barra de acción inferior.

![Publicar Azure m][11]


## <a name="summary"></a>Resumen
Resumen de lo que hemos realizado en este tutorial tutorial, ha creado un entorno de ciencias datos Azure trabajado con un gran conjunto de datos público, teniendo a través del proceso de ciencias de datos de equipo, desde adquisición de datos al modelo de aprendizaje y luego a la implementación de un servicio web de aprendizaje del equipo de Azure.

### <a name="license-information"></a>Información de licencia

Este tutorial de ejemplo y su adjunta secuencias de comandos y IPython notebook(s) están compartidos por Microsoft en la licencia MIT. Compruebe el archivo LICENSE.txt el directorio del código de ejemplo en GitHub para obtener más detalles.

## <a name="references"></a>Referencias

• La [página de descarga de viajes de Nueva York Taxi Andrés Monroy](http://www.andresmh.com/nyctaxitrips/)  
• [Frustrados Taxi viaje datos de Nueva York por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Taxi de Nueva York y Limousine Comisión investigación y estadísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
