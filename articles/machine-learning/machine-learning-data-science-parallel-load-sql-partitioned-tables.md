<properties 
    pageTitle="Paralelo masiva importación de datos mediante tablas de partición SQL | Microsoft Azure" 
    description="Importación de datos masiva paralela utilizando tablas de partición SQL" 
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
    ms.author="bradsev" /> 

# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Importación de datos masiva paralela utilizando tablas de partición SQL

Este documento describe cómo crear tablas con particiones de importación masiva rápida paralelo de datos a una base de datos de SQL Server. Para carga/transferencia de datos grande en una base de datos SQL, la importación de datos a la base de datos de SQL y consultas posteriores puede mejorarse mediante _tablas con particiones y vistas_. 


## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Crear una nueva base de datos y un conjunto de grupos de archivos

- [Crear una nueva base de datos](https://technet.microsoft.com/library/ms176061.aspx) (si no existe)
- Agregar grupos de archivos de base de datos a la base de datos que contendrá los archivos físicos con particiones

  Nota: Esto puede realizarse con [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) si es nuevo o [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) si la base de datos ya existe

- Agregar uno o más archivos (según sea necesario) para cada grupo de archivos de base de datos

 > [AZURE.NOTE] Especificar el grupo de archivos de destino que contiene los datos de esta partición y los nombres de archivo de base de datos física donde se almacenarán los datos del grupo de archivos.
 
En el ejemplo siguiente se crea una nueva base de datos con tres grupos de archivos que no sea el principal y los grupos de registro, que contiene un archivo físico en cada uno. Los archivos de base de datos se crean en la carpeta de datos de SQL Server de forma predeterminada, tal como está configurado en la instancia de SQL Server. Para obtener más información acerca de las ubicaciones de archivo predeterminadas, vea [Ubicaciones de archivos para predeterminadas y con nombre instancias de de SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);
    
    EXECUTE ('
        CREATE DATABASE <database_name>
        ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
        LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
    
## <a name="create-a-partitioned-table"></a>Crear una tabla con particiones

Crear tablas con particiones según el esquema de datos, que se asignan a los grupos de archivos de base de datos que creó en el paso anterior. Cuando datos masiva importada a las tablas con particiones, los registros se distribuirá entre los grupos de archivos según un esquema de partición, como se describe a continuación.

**Para crear una tabla de partición, debe:**

- [Crear una función de partición](https://msdn.microsoft.com/library/ms187802.aspx) que define el rango de valores y límites que se incluirán en cada tabla de particiones individuales, por ejemplo, para limitar las particiones por mes (algunos\_datetime\_campo) en el año 2013:

        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

- [Crear un esquema de partición](https://msdn.microsoft.com/library/ms179854.aspx) que cada intervalo de la partición de la función de partición se asigna a un grupo de archivos físico, por ejemplo:

        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )

  Sugerencia: Para comprobar los rangos vigentes en cada partición según el esquema de la función, ejecute la siguiente consulta:

        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

- [Crear una tabla con particiones](https://msdn.microsoft.com/library/ms174979.aspx) (s) acuerdo con el esquema de datos, y especifique el campo de combinación y restricción de partición utilizado para la partición de la tabla, por ejemplo:

        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Para obtener más información, vea [crear tablas con particiones e índices](https://msdn.microsoft.com/library/ms188730.aspx).


## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Los datos de cada tabla de particiones individuales de importación masiva

- Puede utilizar BCP, BULK INSERT u otros métodos, como el [Asistente para migración de SQL Server](http://sqlazuremw.codeplex.com/). El ejemplo proporcionado, utiliza el método BCP.

- [Modificar la base de datos](https://msdn.microsoft.com/library/bb522682.aspx) para cambiar el esquema de registro de transacciones a BULK_LOGGED para minimizar la sobrecarga de registro, por ejemplo:

        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED

- Para acelerar la carga de datos, iniciar las operaciones de importación masiva en paralelo. Para obtener sugerencias sobre acelerar la importación masiva de datos grandes en bases de datos de SQL Server, consulte [carga de 1 TB en menos de 1 hora](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

El siguiente script de PowerShell es un ejemplo de uso de BCP de cargar datos en paralelo.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0
    
    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>
       
    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"
   
    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir
      
    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }
    
    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }
    
    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }
    
    Get-Job
    
    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Crear índices para optimizar las combinaciones y rendimiento de las consultas

- Si se extraen los datos para el modelado de varias tablas, crear índices en las claves de combinación para mejorar el rendimiento de la combinación.

- [Crear índices](https://technet.microsoft.com/library/ms188783.aspx) (agrupado o no agrupado) destinada al mismo grupo de archivos para cada partición, por ejemplo:

        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
o bien,

        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)

 > [AZURE.NOTE] Puede crear los índices antes de importar los datos de forma masiva. Creación de índice antes de la importación masiva ralentizará la carga de datos.


## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Proceso de análisis avanzado y la tecnología en ejemplo de acción

Para obtener un ejemplo de tutorial de end-to-end mediante el proceso de análisis de Cortana con un conjunto de datos pública, vea [Cortana proceso de análisis en acción: uso de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
 
