<properties 
    pageTitle="Analizar datos de retrasos de vuelos con subárbol en HDInsight de Linux | Microsoft Azure" 
    description="Obtenga información sobre cómo usar subárbol para analizar datos de vuelo en HDInsight de Linux, a continuación, exporte los datos a la base de datos SQL Sqoop." 
    services="hdinsight" 
    documentationCenter="" 
    authors="Blackmist" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="larryfr"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analizar datos de vuelo retraso usando subárbol en HDInsight

Obtenga información sobre cómo analizar datos de vuelo retraso usando subárbol en HDInsight de Linux, a continuación, exporte los datos a la base de datos de SQL Azure uso Sqoop.

> [AZURE.NOTE] Aunque se pueden utilizar piezas individuales de este documento con clústeres HDInsight basado en Windows (Python y subárbol por ejemplo), varios pasos dependen de en clústeres basados en Linux. Para conocer los pasos que funcionan con un clúster basado en Windows, vea [analizar datos de retrasos de vuelos con subárbol en HDInsight](hdinsight-analyze-flight-delay-data.md)

###<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __HDInsight un clúster__. Para conocer los pasos sobre cómo crear un nuevo clúster HDInsight basados en Linux, vea [Introducción al uso de Hadoop con subárbol en HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md) .

- __Base de datos SQL azure__. Una base de datos de SQL Azure que usa como un almacén de datos de destino. Si ya no tiene una base de datos de SQL, vea [tutorial de base de datos SQL: crear una base de datos SQL en minutos](../sql-database/sql-database-get-started.md).

- __CLI azure__. Si no ha instalado la CLI de Azure, vea [instalar y configurar la CLI Azure](../xplat-cli-install.md) para obtener más pasos.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]


##<a name="download-the-flight-data"></a>Descargar los datos de vuelo

1. Vaya a [referencia y administración de la tecnología innovadora, oficina de estadísticas de transporte][rita-website].
2. En la página, seleccione los valores siguientes:

  	| Nombre | Valor |
  	| ---- | ---- |
  	| Año de filtro | 2013 |
  	| Período de filtro | Enero |
  	| Campos | Año, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, origen, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Desactive todos los demás campos |

3. Haga clic en **Descargar**. 

##<a name="upload-the-data"></a>Cargar los datos

1. Usar el comando siguiente para cargar el archivo zip en el nodo principal del clúster de HDInsight:

        scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Reemplace el __nombre de archivo__ con el nombre del archivo zip. Reemplace el __nombre de usuario__ con el inicio de sesión SSH para el clúster de HDInsight. Reemplazar NOMBREDECLÚSTER con el nombre del clúster HDInsight.
    
    > [AZURE.NOTE] Si usa una contraseña para autenticar su inicio de sesión SSH, se le pedirá la contraseña. Si utiliza una clave pública, tendrá que usar la `-i` parámetro y especifique la ruta de acceso a la clave privada correspondiente. Por ejemplo `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Una vez completada la carga, conectar al clúster mediante SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Para obtener más información sobre cómo utilizar SSH con HDInsight de Linux, consulte los siguientes artículos:
    
    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)
    
3. Una vez conectado, use las siguientes acciones para descomprima el archivo .zip:

        unzip FILENAME.zip
    
    Se extraerán un archivo .csv que es de aproximadamente 60MB de tamaño.
    
4. Utilice la siguiente para crear un nuevo directorio en WASB (el almacén de datos distribuidos usado por HDInsight) y copie el archivo:

    HDFS dfs - mkdir -p /tutorials/flightdelays/data hdfs dfs-colocar FILENAME.csv/tutoriales/flightdelays/datos /
    
##<a name="create-and-run-the-hiveql"></a>Crear y ejecutar la HiveQL

Realice los pasos siguientes para importar datos desde el archivo CSV en una tabla de la sección denominada __retrasos__.

1. Utilice la siguiente para crear y editar un nuevo archivo denominado __flightdelays.hql__:

        nano flightdelays.hql
        
    Utilice la siguiente como el contenido de este archivo:
    
        DROP TABLE delays_raw;
        -- Creates an external table over the csv file
        CREATE EXTERNAL TABLE delays_raw (
            YEAR string,
            FL_DATE string,
            UNIQUE_CARRIER string,
            CARRIER string,
            FL_NUM string,
            ORIGIN_AIRPORT_ID string,
            ORIGIN string,
            ORIGIN_CITY_NAME string,
            ORIGIN_CITY_NAME_TEMP string,
            ORIGIN_STATE_ABR string,
            DEST_AIRPORT_ID string,
            DEST string,
            DEST_CITY_NAME string,
            DEST_CITY_NAME_TEMP string,
            DEST_STATE_ABR string,
            DEP_DELAY_NEW float,
            ARR_DELAY_NEW float,
            CARRIER_DELAY float,
            WEATHER_DELAY float,
            NAS_DELAY float,
            SECURITY_DELAY float,
            LATE_AIRCRAFT_DELAY float)
        -- The following lines describe the format and location of the file
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE
        LOCATION '/tutorials/flightdelays/data';
        
        -- Drop the delays table if it exists
        DROP TABLE delays;
        -- Create the delays table and populate it with data
        -- pulled in from the CSV file (via the external table defined previously)
        CREATE TABLE delays AS
        SELECT YEAR AS year,
            FL_DATE AS flight_date,
            substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
            substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
            substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
            ORIGIN_AIRPORT_ID AS origin_airport_id,
            substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
            substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
            substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
            DEST_AIRPORT_ID AS dest_airport_id,
            substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
            substring(DEST_CITY_NAME,2) AS dest_city_name,
            substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
            DEP_DELAY_NEW AS dep_delay_new,
            ARR_DELAY_NEW AS arr_delay_new,
            CARRIER_DELAY AS carrier_delay,
            WEATHER_DELAY AS weather_delay,
            NAS_DELAY AS nas_delay,
            SECURITY_DELAY AS security_delay,
            LATE_AIRCRAFT_DELAY AS late_aircraft_delay
        FROM delays_raw;
        
2. Utilice __Ctrl + X__, a continuación, __Y__ para guardar el archivo.

3. Use las siguientes acciones para iniciar subárbol y ejecute el archivo __flightdelays.hql__ :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
        
    > [AZURE.NOTE] En este ejemplo, `localhost` se utiliza ya está conectado al nodo principal del clúster HDInsight, que es donde se está ejecutando HiveServer2.

4. Utilice el comando siguiente para abrir una sesión de Beeline interactiva:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

5. Cuando reciba el `jdbc:hive2://localhost:10001/>` preguntar, use las siguientes acciones para recuperar datos de los datos de vuelo importados retraso.

        INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        SELECT regexp_replace(origin_city_name, '''', ''),
            avg(weather_delay)
        FROM delays
        WHERE weather_delay IS NOT NULL
        GROUP BY origin_city_name;

    Esto recuperar una lista de ciudades que experimentan las demoras de tiempo, junto con el tiempo de retardo promedio y guardarlo en `/tutorials/flightdelays/output`. Más adelante, Sqoop leerá los datos desde esta ubicación y exportar a base de datos de SQL Azure.

6. Para salir de Beeline, escriba `!quit` cuando se le solicite.

## <a name="create-a-sql-database"></a>Crear una base de datos SQL

Si ya tiene una base de datos de SQL, debe obtener el nombre del servidor. Puede encontrar en el [Portal de Azure](https://portal.azure.com) seleccionar __Bases de datos de SQL__y filtrando en el nombre de la base de datos que desea usar. El nombre del servidor aparece en la columna del __servidor__ .

Si no dispone de una base de datos de SQL, use la información de [tutorial de base de datos SQL: crear una base de datos SQL en minutos](../sql-database/sql-database-get-started.md) para crear uno. Debe guardar el nombre de servidor que se utiliza para la base de datos.

##<a name="create-a-sql-database-table"></a>Crear una tabla de base de datos SQL

> [AZURE.NOTE] Hay muchas formas de conectarse a la base de datos de SQL para crear una tabla. Los siguientes pasos usar [FreeTDS](http://www.freetds.org/) desde el clúster HDInsight.

1. Utilizar SSH para conectar con el clúster basados en Linux HDInsight y ejecute los siguientes pasos de la sesión SSH.

3. Use el comando siguiente para instalar FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Una vez que se ha instalado FreeTDS, use el comando siguiente para conectarse al servidor de base de datos de SQL. Reemplazar __nombre_de_servidor__ con el nombre del servidor de base de datos de SQL. Reemplace __adminLogin__ y __adminPassword__ con el inicio de sesión de base de datos de SQL. Reemplazar __databaseName__ con el nombre de la base de datos.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>

    Recibirá un resultado similar al siguiente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. En la `1>` pregunta, escriba las siguientes líneas:

        CREATE TABLE [dbo].[delays](
        [origin_city_name] [nvarchar](50) NOT NULL,
        [weather_delay] float,
        CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
        ([origin_city_name] ASC))
        GO

    Cuando el `GO` instrucción se haya especificado, se evaluará las afirmaciones anteriores. Esta opción creará una nueva tabla denominada __retrasos__, con un índice agrupado (requerido la base de datos de SQL.)

    Para comprobar que se ha creado la tabla, utilice la siguiente:

        SELECT * FROM information_schema.tables
        GO

    Debería ver el resultado similar al siguiente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        databaseName       dbo     delays      BASE TABLE

8. Escriba `exit` en la `1>` del mensaje salir de la utilidad de tsql.
    
##<a name="export-data-with-sqoop"></a>Exportar datos con Sqoop

2. Utilice el comando siguiente para comprobar que Sqoop pueden ver la base de datos de SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Debería devolver una lista de bases de datos, incluida la base de datos que ha creado la tabla retrasos en una versión anterior.

3. Use el comando siguiente para exportar los datos de hivesampletable a la tabla mobiledata:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasbs:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

    Esto indica a Sqoop para conectarse a la base de datos de SQL, la base de datos que contiene la tabla retrasos y exportar datos de la wasbs: / / y tutoriales/flightdelays/salida (donde se almacenan los resultados de la consulta de la sección anterior,) a la tabla retrasos.

4. Cuando finalice el comando, utilice la siguiente para conectarse a la base de datos usando TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Una vez conectado, utilice las siguientes instrucciones para comprobar que se exportan los datos a la tabla mobiledata:
    
        SELECT * FROM delays
        GO

    Verá una lista de datos en la tabla. Tipo de `exit` salir de la utilidad de tsql.

##<a id="nextsteps"></a>Pasos siguientes

Ahora comprende cómo cargar un archivo con el almacenamiento de blobs de Windows Azure, cómo rellenar una tabla subárbol utilizando los datos de almacenamiento de blobs de Windows Azure, cómo ejecutar consultas de la sección y cómo usar Sqoop para exportar datos de HDFS a una base de datos de SQL Azure. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight][hdinsight-get-started]
* [Usar subárbol con HDInsight][hdinsight-use-hive]
* [Usar Oozie con HDInsight][hdinsight-use-oozie]
* [Usar Sqoop con HDInsight][hdinsight-use-sqoop]
* [Usar cerdo con HDInsight][hdinsight-use-pig]
* [Desarrollar programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]
* [Desarrollar Hadoop Python transmisión programas para HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 
