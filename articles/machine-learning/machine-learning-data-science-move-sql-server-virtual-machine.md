<properties 
    pageTitle="Mover los datos a SQL Server en una máquina virtual Azure | Azure" 
    description="Mover datos de archivos sin formato o de un servidor local SQL Server a SQL Server en máquina virtual de Azure." 
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
    ms.date="09/14/2016" 
    ms.author="bradsev" /> 

# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Mover los datos a SQL Server en una máquina virtual Azure

En este tema se describen las opciones para mover datos desde archivos sin formato (formatos CSV o TSV) o desde un servidor local SQL Server a SQL Server en una máquina virtual Azure. Estas tareas para mover datos en la nube son parte del proceso de ciencias de datos del grupo.

Para un tema que describe las opciones para mover datos a una base de datos de SQL Azure para el aprendizaje, vea [mover datos a una base de datos de SQL Azure para el aprendizaje Azure](machine-learning-data-science-move-sql-azure.md).

El **menú** debajo de vínculos a temas que describen cómo recopilar datos en otros entornos de destino donde los datos se pueden almacenar y procesar durante el proceso de ciencias de datos de equipo (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


La siguiente tabla resume las opciones para mover datos a SQL Server en una máquina virtual Azure.

<b>ORIGEN</b> |<b>DESTINO: SQL Server en máquina virtual de Azure</b> |
------------------ |-------------------- |
<b>Archivo plano</b> |1. <a href="#insert-tables-bcp">utilidad de copia de la línea de comandos de forma masiva (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">Insertar SQL consulta en masa</a><br> 3. <a href="#sql-builtin-utilities">gráficas utilidades integradas de SQL Server</a>
<b>Implementación local de SQL Server</b> | 1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">implementar una base de datos de SQL Server a un asistente de Microsoft Azure VM</a><br> 2. <a href="#export-flat-file">Exportar a un archivo plano</a><br> 3. <a href="#sql-migration">Asistente para la migración de base de datos SQL</a> <br> 4. <a href="#sql-backup">base de datos back hacia arriba y restaurar</a><br>

Tenga en cuenta que este documento se supone que se ejecutan comandos SQL de SQL Server Management Studio o explorador de base de datos de Visual Studio.

> [AZURE.TIP] Como alternativa, puede usar el [Generador de datos de Azure](https://azure.microsoft.com/services/data-factory/) para crear y programar una canalización que se mover los datos a una máquina virtual de servidor de SQL Azure. Para obtener más información, consulte [Copiar datos con el generador de datos de Azure (copiar actividad)](../data-factory/data-factory-data-movement-activities.md).


## <a name="prereqs"></a>Requisitos previos
En este tutorial se supone que:

* Una **suscripción de Azure**. Si no tiene una suscripción, puede registrarse para una [prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Una **cuenta de almacenamiento de Azure**. Para almacenar los datos en este tutorial, usará una cuenta de almacenamiento de Azure. Si no tiene una cuenta de almacenamiento de Azure, vea el artículo [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) . Después de crear la cuenta de almacenamiento, debe obtener la clave de cuenta que utiliza para acceder al almacenamiento. Vea [la vista, copia y las teclas de acceso de almacenamiento regenerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Preparación de **SQL Server en una máquina virtual de Azure**. Para obtener instrucciones, vea [Configurar una máquina virtual de Azure SQL Server como un servidor de Bloc de notas de IPython para análisis avanzado](machine-learning-data-science-setup-sql-server-virtual-machine.md).
* Instalado y configurado **Azure PowerShell** localmente. Para obtener instrucciones, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).


## <a name="filesource_to_sqlonazurevm"></a>Mover datos de un origen de archivo plano a SQL Server en una máquina virtual de Azure

Si los datos están en un archivo plano (organizado en un formato de filas y columnas), se puede mover a SQL Server VM en Azure a través de los métodos siguientes:

1. [Herramienta de copia de línea de comandos masiva (BCP)](#insert-tables-bcp) 
2. [Consulta de SQL de inserción masiva](#insert-tables-bulkquery)
3. [Gráficos utilidades integradas de SQL Server (importar o exportar, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Herramienta de copia de línea de comandos masiva (BCP)

BCP es una herramienta de línea de comandos instalada con SQL Server y es una de las formas más rápidas para mover datos. Funciona en todas las tres variantes de SQL Server (local SQL Server, SQL Azure y SQL Server VM en Azure). 

> [AZURE.NOTE]**Donde deben ser Mis datos para BCP?**  
> Aunque no es necesario, archivos de datos de origen que se encuentra en el mismo equipo que el servidor de SQL de destino permite para las transferencias más rápidas (red velocidad vs disco local velocidad IO). Puede mover los archivos planos que contiene datos en el equipo donde está instalado SQL Server con herramientas como [AZCopy](../storage/storage-use-azcopy.md)de copia de varios archivos, [El Explorador de almacenamiento de Azure](http://storageexplorer.com/) o windows, copiar y pegar mediante el protocolo de escritorio remoto (RDP).

1. Asegúrese de que la base de datos y las tablas se crean en la base de datos de SQL Server de destino. Aquí tenemos un ejemplo de cómo realizar esta tarea con la `Create Database` y `Create Table` comandos:

        CREATE DATABASE <database_name>
    
        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        ) 
    
2. Generar el formato de archivo que describe el esquema de la tabla mediante el comando siguiente desde la línea de comandos del equipo donde está instalado bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n` 

3. Insertar los datos en la base de datos mediante el comando bcp como sigue. Esto debe trabajar desde la línea de comandos suponiendo que SQL Server está instalado en el mismo equipo:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimizar BCP inserta** Consulte el artículo siguiente ['Directrices para optimizar la importación masiva'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) optimizar dichas inserciones.


### <a name="insert-tables-bulkquery-parallel"></a>Poner en paralelo inserciones de movimiento de datos más rápido

Si los datos que está moviendo están grandes, puede acelerar cosas ejecutando simultáneamente varios comandos BCP en paralelo en una secuencia de comandos de PowerShell.

> [AZURE.NOTE]**Recopilación de datos grandes** Para optimizar la carga de conjuntos de datos grandes y muy grandes de datos, dividir las tablas de base de datos lógica y física con varias tablas de partición y grupos de archivos. Para obtener más información sobre cómo crear y cargar datos en tablas de partición, vea [Paralelo tablas de partición de SQL de carga](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


El script de PowerShell de ejemplo siguiente se muestran inserciones paralelas con bcp:
    
    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
      }
    

    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }
    

    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }
    
    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Consulta de SQL de inserción masiva

[Consulta de SQL de insertar masiva](https://msdn.microsoft.com/library/ms188365) puede usarse para importar datos en la base de datos de archivos en función de fila o columna (en los[Datos de prepararse para masa exportar o importar (SQL Server)](https://msdn.microsoft.com/library/ms188609), se tratan los tipos compatibles) tema. 

Estos son algunos ejemplos de comandos para insertar de forma masiva son como sigue:  

1. Analizar los datos y establecer las opciones personalizadas antes de importar para asegurarse de que la base de datos de SQL Server asume el mismo formato para todos los campos, como fechas especiales. Aquí tiene un ejemplo de cómo establecer el formato de fecha como día de mes del año (si los datos contienen la fecha en formato de día del mes de año):

        SET DATEFORMAT ymd; 
    
2. Importar datos con las instrucciones de importación masiva:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH 
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )
      

### <a name="sql-builtin-utilities"></a>Utilidades integradas de SQL Server

Puede usar servicios de integración de SQL Server (SSIS) para importar datos en SQL Server VM en Azure desde un archivo plano. SSIS está disponible en dos entornos studio. Para obtener información detallada, consulte [Integration Services (SSIS) y entornos de Studio](https://technet.microsoft.com/library/ms140028.aspx):

- Para obtener más información sobre las herramientas de datos de SQL Server, consulte [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
- Para obtener información detallada sobre el Asistente para importación y exportación, vea [importación de SQL Server y el Asistente para exportación](https://msdn.microsoft.com/library/ms141209.aspx)


## <a name="sqlonprem_to_sqlonazurevm"></a>Mover datos de implementación local de SQL Server a SQL Server en una máquina virtual de Azure

También puede utilizar las siguientes estrategias de migración:

1. [Implementar una base de datos de SQL Server en un asistente de Microsoft Azure VM](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportar a archivo plano](#export-flat-file) 
3. [Asistente para la migración de base de datos SQL](#sql-migration)
4. [Base de datos back hacia arriba y restaurar](#sql-backup)

Describimos cada uno de estos siguientes:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Implementar una base de datos de SQL Server en un asistente de Microsoft Azure VM

La **implementación de una base de datos de SQL Server a un asistente de Microsoft Azure VM** es una forma sencilla y recomendada para mover datos de una instancia de SQL Server local a SQL Server en una máquina virtual de Azure. Para conocer los pasos detallados así como una discusión de otras alternativas, vea [migrar una base de datos de SQL Server en una máquina virtual de Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportar a archivo plano

Pueden utilizarse varios métodos para exportar los datos de un servidor local de SQL Server de forma masiva tal como se describe en el tema de [masa importación y exportación de datos (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . Este documento trata el programa de copia masiva (BCP) como un ejemplo. Una vez que se exportan los datos en un archivo plano, se puede importar a otro servidor de SQL server mediante importación masiva. 

1. Exportar los datos del servidor de SQL Server local a un archivo con la herramienta bcp como sigue

    `bcp dbname..tablename out datafile.tsv -S  servername\sqlinstancename -T -t \t -t \n -c`

2. Crear la base de datos y la tabla de SQL Server VM sobre el uso de Azure la `create database` y `create table` para el esquema de la tabla se exportan en el paso 1.

3. Crear un archivo de formato para describir el esquema de la tabla de los datos que importar/exportar. Detalles del archivo de formato se describen en [crear un archivo de formato (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Aplicar el formato de generación de archivo cuando se ejecuta BCP desde el equipo de SQL Server 

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

    Aplicar el formato de generación de archivo cuando se ejecuta BCP remotamente en SQL Server 

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
        
    
4. Use cualquiera de los métodos descritos en la sección [Mover datos de origen de archivo](#filesource_to_sqlonazurevm) para mover los datos de archivos planos en un servidor de SQL.

### <a name="sql-migration"></a>Asistente para la migración de base de datos SQL

[Asistente para migración de base de datos de SQL Server](http://sqlazuremw.codeplex.com/) ofrece una manera fácil de usar para mover datos entre dos instancias de SQL server. Permite al usuario asignar el esquema de datos entre orígenes y tablas de destino, elija los tipos de columna y varias otras funcionalidades. Utiliza la copia masiva (BCP) en segundo plano. A continuación se muestra una captura de pantalla de la pantalla de bienvenida del Asistente para migración de base de datos de SQL.  

![Asistente para la migración de SQL Server][2]

### <a name="sql-backup"></a>Base de datos back hacia arriba y restaurar

Es compatible con SQL Server: 

1. [Base de datos back hacia arriba y restaure la funcionalidad](https://msdn.microsoft.com/library/ms187048.aspx) (ambos a un archivo local o mochila exportación a blob) y [Aplicaciones de nivel de datos](https://msdn.microsoft.com/library/ee210546.aspx) (con mochila). 
2. Capacidad para crear directamente máquinas virtuales de SQL Server en Azure con una base de datos copiado o copiar a una base de datos de SQL Azure. Para obtener más detalles, consulte [usar el Asistente para la base de datos de copia](https://msdn.microsoft.com/library/ms188664.aspx). 

Una captura de pantalla de la copia de la base de datos de seguridad y restauración opciones desde SQL Server Management Studio se muestra a continuación.

![Herramienta de importación SQL Server][1]

## <a name="resources"></a>Recursos

[Migrar una base de datos a SQL Server en una máquina virtual de Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md)

[SQL Server en máquinas virtuales de Azure Introducción](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png
