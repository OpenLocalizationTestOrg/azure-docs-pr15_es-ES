<properties
    pageTitle="Usar Hadoop Sqoop en HDInsight de Linux | Microsoft Azure"
    description="Obtenga información sobre cómo ejecutar Sqoop importar y exportar entre un Hadoop basados en Linux en clúster de HDInsight y una base de datos de SQL Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>Usar Sqoop con Hadoop en HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Obtenga información sobre cómo usar Sqoop para importar y exportar entre un clúster de HDInsight basados en Linux y la base de datos de SQL Server o de base de datos de SQL Azure.

> [AZURE.NOTE] Los pasos de este artículo usar SSH para conectarse a un clúster de HDInsight basados en Linux. También pueden usar los clientes de Windows Azure PowerShell y HDInsight .NET SDK para trabajar con Sqoop en clústeres basados en Linux. Use el selector de tabulaciones para abrir los artículos.

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Clúster A Hadoop en HDInsight** y una __Base de datos de SQL Azure__: los pasos de este documento se basan en el clúster y creados con el documento de [base de datos SQL y crear un clúster](hdinsight-use-sqoop.md#create-cluster-and-sql-database) de base de datos. Si ya tiene un clúster de HDInsight y la base de datos de SQL, puede sustituirlas de los valores usados en este documento.
- **Estación de trabajo**: un equipo con un cliente SSH.

##<a name="install-freetds"></a>Instalar FreeTDS

1. Usar SSH para conectarse al clúster HDInsight basados en Linux. La dirección para usar al conectarse es `CLUSTERNAME-ssh.azurehdinsight.net` y el puerto es `22`.

    Para obtener más información sobre el uso de SSH para conectarse a HDInsight, consulte los siguientes documentos:

    * **Clientes Linux, Unix o OS X**: vea [conectarse a un clúster basados en Linux HDInsight de Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Los clientes de Windows**: vea [conectarse a un clúster basados en Linux HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Use el comando siguiente para instalar FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    FreeTDS se utilizará en varios pasos para conectarse a la base de datos de SQL.

##<a name="create-the-table-in-sql-database"></a>Crear la tabla de base de datos de SQL

> [AZURE.IMPORTANT] Si está usando un clúster de HDInsight y creado siguiendo los pasos de la [base de datos SQL y crear un clúster](hdinsight-use-sqoop.md)de base de datos de SQL, pasar por alto los pasos descritos en esta sección como la base de datos y la tabla que se crearon como parte de los pasos en el documento.

1. Desde la conexión SSH HDInsight, utilice el comando siguiente para conectarse al servidor de base de datos SQL y creta la tabla que se utilizará en el resto de estos pasos:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Recibirá un resultado similar al siguiente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. En la `1>` pregunta, escriba las siguientes líneas:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    Cuando el `GO` instrucción se haya especificado, se evaluará las afirmaciones anteriores. En primer lugar, se crea la tabla **mobiledata** , a continuación, se agrega un índice agrupado a él (requiere la base de datos de SQL.)

    Para comprobar que se ha creado la tabla, utilice la siguiente:

        SELECT * FROM information_schema.tables
        GO

    Debería ver el resultado similar al siguiente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Escriba `exit` en la `1>` del mensaje salir de la utilidad de tsql.

##<a name="sqoop-export"></a>Exportación de Sqoop

3. Desde la conexión SSH a HDInsight, se el comando siguiente para comprobar que Sqoop pueden ver la base de datos de SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Debería devolver una lista de bases de datos, incluida la base de datos de **sqooptest** que creó anteriormente.

4. Use el comando siguiente para exportar los datos de **hivesampletable** a la tabla **mobiledata** :

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Esto indica a Sqoop para conectarse a la base de datos de SQL, la base de datos **sqooptest** y exportar datos de la **wasbs: / / / subárbol/almacén/hivesampletable** (archivos físicos para la *hivesampletable*,) a la tabla **mobiledata** .

5. Cuando finalice el comando, utilice la siguiente para conectarse a la base de datos usando TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Una vez conectado, utilice las siguientes instrucciones para comprobar que se exportan los datos a la tabla **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Verá una lista de datos en la tabla. Tipo de `exit` salir de la utilidad de tsql.

##<a name="sqoop-import"></a>Importar Sqoop

1. Utilice lo siguiente para importar datos de la tabla de **mobiledata** de base de datos de SQL, en la **wasbs: / / / tutoriales/usesqoop/importeddata** HDInsight de directorio:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Los datos importados tendrá campos separados por un carácter de tabulación y las líneas se terminará por un carácter de nueva línea.

2. Una vez completada la importación, use el comando siguiente a la lista de datos en el nuevo directorio:

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

##<a name="using-sql-server"></a>Uso de SQL Server

También puede usar Sqoop para importar y exportar datos de SQL Server, en el centro de datos o en una máquina Virtual hospedada en Azure. Las diferencias entre el uso de la base de datos de SQL y SQL Server son:

* HDInsight y SQL Server deben estar en la misma red Virtual de Azure

    > [AZURE.NOTE] HDInsight admite solo las redes virtuales según ubicación y actualmente no funcionan con redes virtuales basadas en el grupo afinidad.

    Cuando se usa SQL Server en el centro de datos, debe configurar la red virtual como *a sitios* o *sitio de punto*.

    > [AZURE.NOTE] **Sitio de punto de** redes virtuales, SQL Server debe ejecutar al cliente VPN la aplicación de configuración, que está disponible desde el **panel** de la configuración de red virtual Azure.

    Para obtener más información red Virtual de Azure, vea [Introducción a la red Virtual](../virtual-network/virtual-networks-overview.md).

* SQL Server debe estar configurado para permitir una autenticación de SQL. Para obtener más información, vea [Elegir un modo de autenticación](https://msdn.microsoft.com/ms144284.aspx)

* Debe configurar SQL Server para que acepte las conexiones remotas. Obtener más información, consulte [cómo solucionar problemas de conexión con el motor de base de datos de SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)

* Debe crear la base de datos **sqooptest** en SQL Server con una herramienta como **SQL Server Management Studio** o **tsql** : los pasos para usar la CLI Azure sólo funcionan para la base de datos de SQL Azure

    Las instrucciones de TSQL para crear la tabla de **mobiledata** son similares las utilizadas para la base de datos de SQL, con la excepción de creación de un clusterd índice - esto no es necesario para SQL Server:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Cuando se conecta a SQL Server desde HDInsight, debe usar la dirección IP de SQL Server a menos que haya configurado un sistema de nombres de dominio (DNS) para resolver los nombres en la red Virtual de Azure. Por ejemplo:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##<a name="limitations"></a>Limitaciones

* Exportación de masa - HDInsight de con Linux, el conector de Sqoop utilizado para exportar datos a Microsoft SQL Server o base de datos de SQL Azure no es compatible con las inserciones masivas.

* Procesamiento por lotes - con HDInsight basados en Linux, cuando se usa el `-batch` cambia cuando se realizan inserciones, Sqoop realizará varias inserciones en lugar de por lotes las operaciones de inserción.

##<a name="next-steps"></a>Pasos siguientes

Ahora ha aprendido a usar Sqoop. Para obtener más información, consulte:

- [Usar Oozie con HDInsight][hdinsight-use-oozie]: Sqoop de usar una acción en un flujo de trabajo Oozie.
- [Analizar datos de vuelo retraso usando HDInsight][hdinsight-analyze-flight-data]: usar subárbol para analizar vuelo retrasar datos y use Sqoop para exportar datos a una base de datos de SQL Azure.
- [Cargar datos a HDInsight][hdinsight-upload-data]: buscar otros métodos para cargar los datos con el almacenamiento de blobs de Windows Azure o HDInsight.



[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
