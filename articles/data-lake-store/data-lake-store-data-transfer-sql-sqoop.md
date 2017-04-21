<properties 
   pageTitle="Copiar datos entre la tienda de lago datos y la base de datos de SQL Azure con Sqoop | Microsoft Azure"
   description="Usar Sqoop para copiar datos entre la base de datos de SQL Azure y el almacenamiento de lago de datos" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Copiar datos entre la tienda de lago datos y la base de datos de SQL Azure con Sqoop

Obtenga información sobre cómo usar Apache Sqoop para importar y exportar datos entre la base de datos de SQL Azure y el almacenamiento de lago de datos.
 

## <a name="what-is-sqoop"></a>¿Qué es Sqoop?

Aplicaciones de datos grande son una opción natural para procesar datos estructurados y semiestructurados, como registros y archivos. Sin embargo, también puede haber necesario procesar datos estructurados que se almacenan en bases de datos relacionales.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) es una herramienta diseñada para transferir datos entre bases de datos relacionales y un repositorio de datos grande, como el almacén de datos de lago. Puede usarlo para importar datos desde un sistema de administración de base de datos relacional (RDBMS) como base de datos de SQL Azure en el almacén de datos de lago. Puede, a continuación, transformar y analizar los datos usando las cargas de trabajo de datos grande y, a continuación, exporte los datos en un RDBMS. En este tutorial, use una base de datos de SQL Azure como la base de datos relacional para la importación y exportación desde.
 

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).
- **Habilitar la suscripción Azure** de vista previa de datos lago almacén público. Vea [las instrucciones](data-lake-store-get-started-portal.md#signup). 
- **Clúster de azure HDInsight** con acceso a una cuenta de almacén de datos lago. Consulte [crear un clúster de HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md). En este artículo se supone que tiene un clúster de HDInsight Linux con acceso al almacén de datos lago.
- **Base de datos SQL azure**. Para obtener instrucciones sobre cómo crear uno, consulte [crear una base de datos de SQL Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>¿Puede obtener información rápidamente con vídeos?

[Vea este vídeo](https://mix.office.com/watch/1butcdjxmu114) sobre cómo copiar datos entre Azure almacenamiento de Blobs y el almacenamiento de lago de datos usando DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Crear tablas de ejemplo en la base de datos de SQL Azure

1. Para empezar a crear dos tablas de ejemplo en la base de datos de SQL Azure. Usar [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio para conectarse a la base de datos de SQL Azure y, a continuación, ejecute las consultas siguientes.

    **Crear Tabla1**

        CREATE TABLE [dbo].[Table1]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

    **Crear tabla2**

        CREATE TABLE [dbo].[Table2]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

2. En **tabla1**, agregue algunos datos de ejemplo. Deje en blanco **tabla2** . Se importará datos from **tabla1** en el almacén de datos de lago. A continuación, se exportar datos del almacén de datos lago into **tabla2**. Ejecutar el fragmento de código siguiente.

         
        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Usar Sqoop desde un clúster de HDInsight con acceso al almacén de datos lago

Un clúster de HDInsight ya tiene los paquetes de Sqoop disponibles. Si ha configurado el clúster HDInsight para usar el almacén de datos de lago como un almacenamiento adicional, puede usar Sqoop (sin cambios en la configuración) para importar o exportar datos entre una base de datos relacional (en este ejemplo, la base de datos de SQL Azure) y una cuenta de almacén de datos lago. 

1. Para este tutorial, se supone que creó un clúster de Linux para SSH debe usar para conectarse al clúster. Consulte [conectarse a un clúster de HDInsight basados en Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Compruebe si puede acceder a la cuenta de almacén de datos lago desde el clúster. Ejecute el siguiente comando desde el símbolo de SSH:

        
        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Esto le ofrece una lista de archivos o carpetas en la cuenta de almacén de datos lago.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importar datos desde base de datos de SQL Azure en el almacén de datos de lago

3. Desplácese hasta el directorio donde están disponibles los paquetes de Sqoop. Normalmente, se trata en `/usr/hdp/<version>/sqoop/bin`. 

4. Importar los datos from **tabla1** en la cuenta de almacén de datos lago. Utilice la siguiente sintaxis:

        
        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Tenga en cuenta que ese marcador de posición de **servidor de nombre sql-base de datos** representa el nombre del servidor donde se ejecuta la base de datos de SQL Azure. marcador de posición del **nombre de la base de datos de SQL** representa el nombre de la base de datos real.

    Por ejemplo,

        
        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Compruebe que los datos ha sido transferidos a la cuenta de almacén de datos lago. Ejecute el siguiente comando:

        
        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Verá el siguiente resultado.

        
        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Cada **parte-m -** * archivo corresponde a una fila de la tabla de origen, * *tabla1**. Puede ver el contenido del elemento-m -* archivos que desea comprobar.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Exportar datos del almacén de datos lago en base de datos de SQL Azure

6. Exportar los datos de la cuenta de la tienda de lago de datos a la tabla vacía, **tabla2**, en la base de datos de SQL Azure. Use la siguiente sintaxis.

        
        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Por ejemplo,

        
        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Compruebe que los datos se ha cargado en la tabla de base de datos de SQL. Usar [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio para conectarse a la base de datos de SQL Azure y, a continuación, ejecute la consulta siguiente.

        
        SELECT * FROM TABLE2

    Debe tener el siguiente resultado.

        ID  FName   LName
        ------------------
        1   Neal    Kell
        2   Lila    Fulton
        3   Erna    Myers
        4   Annette Simpson

## <a name="see-also"></a>Vea también

- [Copiar datos de Azure almacenamiento de Blobs al almacén de datos lago](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)
- [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)
