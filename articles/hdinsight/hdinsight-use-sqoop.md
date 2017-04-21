<properties
    pageTitle="Usar Hadoop Sqoop en HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo usar PowerShell Azure desde una estación de trabajo para ejecutar Sqoop importar y exportar entre un clúster de Hadoop y una base de datos de SQL Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight"></a>Usar Sqoop con Hadoop en HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Obtenga información sobre cómo usar Sqoop en HDInsight para importar y exportar entre HDInsight clúster y base de datos de SQL Azure o base de datos de SQL Server.

Aunque Hadoop es una elección natural para procesar datos estructurados y semistructured, como archivos de registro y, también puede ser necesario procesar datos estructurados que se almacenan en bases de datos relacionales.

[Sqoop] [ sqoop-user-guide-1.4.4] es una herramienta diseñada para transferir datos entre Hadoop clústeres y bases de datos relacionales. Puede usarlo para importar datos de un sistema de administración de la base de datos relacional (RDBMS), como SQL Server, MySQL u Oracle en el sistema de archivos distribuido Hadoop (HDFS), transformar los datos en Hadoop con MapReduce o subárbol y, a continuación, exporte los datos en un RDBMS. En este tutorial, se utiliza una base de datos de SQL Server para la base de datos relacional.

En las versiones de Sqoop son compatibles con clústeres HDInsight, consulte [Novedades en las versiones de clúster proporcionadas por HDInsight?] [hdinsight-versions].

##<a name="understand-the-scenario"></a>Comprender el escenario

HDInsight clúster viene con algunos datos de ejemplo. Que usa los dos ejemplos siguientes:

- Un archivo de registro log4j, que se encuentra en */example/data/sample.log*. Los siguientes registros se extraen desde el archivo:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

- Una tabla de la sección denominada *hivesampletable*, que hace referencia el archivo de datos que se encuentra en */hive/warehouse/hivesampletable*. La tabla contiene algunos datos del dispositivo móvil. 

  	| Campo | Tipo de datos |
  	| ----- | --------- |
  	| ClientID | cadena |
  	| querytime | cadena |
  	| mercado | cadena |
  	| deviceplatform | cadena |
  	| devicemake | cadena |
  	| devicemodel | cadena |
  	| estado | cadena |
  	| país | cadena |
  	| querydwelltime | doble |
  	| Id. de sesión | bigint |
  	| sessionpagevieworder | bigint |

Se exportar *sample.log* y *hivesampletable* para la base de datos de SQL Azure o SQL Server y, a continuación, importar la tabla que contiene los datos del dispositivo móvil a HDInsight mediante la siguiente ruta de acceso:

    /tutorials/usesqoop/importeddata

## <a name="create-cluster-and-sql-database"></a>Crear clúster y base de datos SQL

Esta sección muestra cómo crear un clúster y los esquemas de base de datos SQL para ejecutar el tutorial con el portal de Azure y una plantilla de administrador de recursos de Azure.  Si prefiere usar PowerShell de Azure, consulte el [Apéndice A](#appendix-a---a-powershell-sample).

1. Haga clic en la siguiente imagen para abrir una plantilla de administrador de recursos en el Portal de Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    La plantilla de administrador de recursos se encuentra en un contenedor de blob público *https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*. 
    
    La plantilla de administrador de recursos llama a un paquete de mochila para implementar los esquemas de tabla a la base de datos SQL.  El paquete de mochila también se encuentra en un contenedor de blob público https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Si desea usar un contenedor privado para los archivos de mochila, use los siguientes valores en la plantilla:
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. Desde el módulo de parámetros, escriba lo siguiente:

    - **NombreDeClúster**: escriba un nombre para el clúster de Hadoop que se creará.
    - **Contraseña y nombre de inicio de sesión de clúster**: el nombre de inicio de sesión predeterminado es Admin.
    - **SSH nombre de usuario y contraseña**.
    - **Nombre de inicio de sesión de servidor de base de datos SQL y la contraseña**.

    Los valores siguientes están codificados en la sección de variables:
    
  	|Nombre de cuenta de almacenamiento predeterminada|<CluterName>almacenar|
  	|----------------------------|-----------------|
  	|Nombre del servidor de base de datos SQL Azure|<ClusterName>dbserver|
  	|Nombre de base de datos SQL Azure|<ClusterName>DB|
    
    Escriba estos valores.  Los necesitará más adelante en el tutorial.
    
3. Haga clic en **Aceptar** para guardar los parámetros.

4 desde el módulo de **implementación personalizada** , haga clic en cuadro de lista desplegable de **grupo de recursos** y, a continuación, haga clic en **nuevo** para crear un nuevo grupo de recursos. El grupo de recursos es un contenedor que agrupa el clúster, la cuenta de almacenamiento dependientes y otros recursos vinculados.

5. Haga clic en **condiciones legales**y, a continuación, haga clic en **crear**.

6. Haga clic en **crear**. Verá un nuevo mosaico titulado implementación Submitting para su implementación de la plantilla. Tarda aproximadamente 20 minutos para crear el clúster y la base de datos SQL.

Si decide usar la base de datos de SQL Azure existente o Microsoft SQL Server

- **Base de datos de SQL Azure**: debe configurar una regla de firewall para el servidor de base de datos de SQL Azure permitir el acceso desde su estación de trabajo. Para obtener instrucciones sobre la creación de SQL Azure de base de datos y configurar el firewall, vea [Introducción al uso de la base de datos de SQL Azure][sqldatabase-get-started]. 

    > [AZURE.NOTE] De forma predeterminada una base de datos de SQL Azure permite las conexiones de servicios de Azure, como HDInsight de Azure. Si se deshabilita esta configuración de firewall, debe habilitarlo desde el portal de Azure. Para obtener instrucciones sobre cómo crear una base de datos de SQL Azure y configuración de las reglas de firewall, vea [crear y configurar la base de datos de SQL][sqldatabase-create-configue].

- **SQL Server**: si su clúster de HDInsight se encuentra en la misma red virtual en Azure como SQL Server, puede usar los pasos de este artículo para importar y exportar datos a una base de datos de SQL Server.

    > [AZURE.NOTE] HDInsight admite solo las redes virtuales según ubicación y actualmente no funcionan con redes virtuales basadas en el grupo afinidad.

    * Para crear y configurar una red virtual, vea [crear una red virtual con el portal de Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

        * Cuando se usa SQL Server en el centro de datos, debe configurar la red virtual como *a sitios* o *sitio de punto*.

            > [AZURE.NOTE] **Sitio de punto de** redes virtuales, SQL Server debe ejecutar al cliente VPN la aplicación de configuración, que está disponible desde el **panel** de la configuración de red virtual Azure.

        * Cuando se usa SQL Server en una máquina virtual Azure, puede utilizarse cualquier configuración de red virtual si la máquina virtual de SQL Server de hospedaje es un miembro de la misma red virtual como HDInsight.

    * Para crear un clúster de HDInsight en una red virtual, vea [crear Hadoop clústeres en HDInsight mediante opciones personalizadas](hdinsight-provision-clusters.md)

    > [AZURE.NOTE] SQL Server también debe permitir la autenticación. Debe usar un inicio de sesión de SQL Server para completar los pasos de este artículo.


## <a name="run-sqoop-jobs"></a>Ejecutar trabajos Sqoop

HDInsight puede ejecutar trabajos Sqoop mediante una variedad de métodos. Utilice la siguiente tabla para decidir qué método es adecuado para usted y luego siga el vínculo para obtener un tutorial.

| **Use esta opción** si desea...                                   | .. .an shell **interactivo** | ... proceso **por lotes** | .. .with este **sistema operativo del clúster** | .. .de este **sistema operativo del cliente** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-use-sqoop-mac-linux.md)                        |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X o Windows        |
| [.NET SDK para Hadoop](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux o Windows                          | Windows (por ahora)                        |
| [Azure PowerShell](hdinsight-hadoop-use-sqoop-powershell.md)  |           &nbsp;            |            ✔            | Linux o Windows                          | Windows                                  |

##<a name="limitations"></a>Limitaciones

* Exportación de masa - HDInsight de con Linux, el conector de Sqoop utilizado para exportar datos a Microsoft SQL Server o base de datos de SQL Azure no es compatible con las inserciones masivas.

* Procesamiento por lotes - con HDInsight basados en Linux, cuando se usa el `-batch` cambia cuando se realizan inserciones, Sqoop realizará varias inserciones en lugar de por lotes las operaciones de inserción.

##<a name="next-steps"></a>Pasos siguientes

Ahora ha aprendido a usar Sqoop. Para obtener más información, consulte:

- [Usar subárbol con HDInsight](hdinsight-use-hive.md)
- [Usar cerdo con HDInsight](hdinsight-use-pig.md)
- [Usar Oozie con HDInsight][hdinsight-use-oozie]: Sqoop de usar una acción en un flujo de trabajo Oozie.
- [Analizar datos de vuelo retraso usando HDInsight][hdinsight-analyze-flight-data]: usar subárbol para analizar vuelo retrasar datos y use Sqoop para exportar datos a una base de datos de SQL Azure.
- [Cargar datos a HDInsight][hdinsight-upload-data]: buscar otros métodos para cargar los datos con el almacenamiento de blobs de Windows Azure o HDInsight.


## <a name="appendix-a---a-powershell-sample"></a>Apéndice A - una muestra de PowerShell

Ejemplo de PowerShell realiza los pasos siguientes:

1. Conectarse a Azure.
2. Crear un grupo de recursos de Azure. Para obtener más información, vea [con Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md)
3. Crear un servidor de base de datos de SQL Azure, una base de datos de SQL Azure y dos tablas. 

    Si utiliza SQL Server en su lugar, use las siguientes instrucciones para crear las tablas:
    
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))

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
         [sessionpagevieworder][bigint])

    Examine la base de datos y las tablas de la manera más sencilla es usar Visual Studio. Pueden examinar el servidor de base de datos y la base de datos con el portal de Azure.

4. Crear un clúster de HDInsight.

    Para examinar el clúster, puede usar el portal de Azure o PowerShell de Azure.

5. Procesar previamente el archivo de datos de origen.

    En este tutorial, se exportar un archivo de registro log4j (un archivo delimitado) y una tabla subárbol a una base de datos de SQL Azure. El archivo delimitado se denomina */example/data/sample.log*. Más adelante en el tutorial, ha visto algunos ejemplos de log4j registros. En el archivo de registro, hay algunas líneas vacías y algunas líneas similares a estos:
    
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
    
    Esto está bien para obtener otros ejemplos que usan estos datos, pero debemos eliminar estas excepciones antes de importar en la base de datos de SQL Azure o SQL Server. Exportación de Sqoop no funcionará si hay una cadena vacía o una línea con un menor número de elementos que el número de campos definidos en la tabla de base de datos de SQL Azure. La tabla log4jlogs tiene 7 campos de tipo cadena.

    Este procedimiento crea un nuevo archivo en el clúster: tutorials/usesqoop/data/sample.log. Para examinar el archivo de datos modificado, puede usar el portal de Azure, una herramienta de explorador de almacenamiento de Azure o PowerShell de Azure. [Introducción a HDInsight] [ hdinsight-get-started] tiene un código de ejemplo para el uso de Azure PowerShell para descargar un archivo y mostrar el contenido del archivo.

6. Exportar un archivo de datos a la base de datos de SQL Azure.

    El archivo de origen es tutorials/usesqoop/data/sample.log. La tabla donde se exportan los datos que se denomina log4jlogs.
    
    > [AZURE.NOTE] Distinto de información de la cadena de conexión, los pasos de esta sección deberían funcionar para una base de datos de SQL Azure o SQL Server. Estos pasos se han probado con la configuración siguiente:
    >
    > * **Configuración del sitio de punto de Azure red virtual**: una red virtual conectado el clúster HDInsight a un servidor de SQL Server en un centro de datos privado. Para obtener más información, vea [Configurar una VPN punto al sitio del Portal de administración](../vpn-gateway/vpn-gateway-point-to-site-create.md) .
    > * **3.1 HDInsight de Azure**: consulte [Hadoop crear clústeres en HDInsight mediante opciones personalizadas](hdinsight-provision-clusters.md) para obtener información sobre cómo crear un clúster en una red virtual.
    > * **SQL Server 2014**: está configurado para permitir la autenticación y la ejecución del cliente VPN paquete de configuración para conectarse a la red virtual de forma segura.

7. Exportar una tabla de la sección a la base de datos de SQL Azure.

8. Importar la tabla mobiledata al clúster HDInsight.

    Para examinar el archivo de datos modificado, puede usar el portal de Azure, una herramienta de explorador de almacenamiento de Azure o PowerShell de Azure.  [Introducción a HDInsight] [ hdinsight-get-started] tiene un código de ejemplo sobre el uso de Azure PowerShell para descargar un archivo y mostrar el contenido del archivo.


### <a name="the-powershell-sample"></a>Ejemplo de PowerShell

    # Prepare an Azure SQL database to be used by the Sqoop tutorial
    
    #region - provide the following values
    
    $subscriptionID = "<Enter your Azure Subscription ID>"
    
    $sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
    $sqlDatabasePassword = "<Enter a Password>"
    
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    
    # used for creating Azure service names
    $nameToken = "<Enter an alias>" 
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion
    
    #region - variables
    
    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial
    
    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10
    
    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"
    
    # Used for creating tables and clustered indexes
    $cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
        [t1] [nvarchar](50),
        [t2] [nvarchar](50),
        [t3] [nvarchar](50),
        [t4] [nvarchar](50),
        [t5] [nvarchar](50),
        [t6] [nvarchar](50),
        [t7] [nvarchar](50))"
    
    $cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
    
    $cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
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
    [sessionpagevieworder][bigint])"
    
    $cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"
    
    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion
    
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"
    
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion
    
    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion
    
    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
    
        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
    
        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $credential `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
    
        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress
    
        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    
    #endregion
    
    #region - Create and validate Azure SQL database
    Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green
    
    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    
    #endregion
    
    #region - Create tables
    Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green
    
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()
    
    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jTable
    $ret = $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateLog4jClusteredIndex
    $cmd.ExecuteNonQuery()
    
    # Create the mobiledata table and index
    $cmd.CommandText = $cmdCreateMobileTable
    $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateMobileDataClusteredIndex
    $cmd.ExecuteNonQuery()
    
    $conn.close()
    
    #endregion
    
    
    #region - Create HDInsight cluster
    
    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
    
    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    
    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultBlobContainerName 
    
    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion
    
    #region - pre-process the source file
    
    Write-Host "Preprocessing the source file ..." -ForegroundColor Green
    
    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"
    
    # Define the connection string
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
    
    # Create block blob objects referencing the source and destination blob.
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    
    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)
    
    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    
    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")
    
        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)
    
            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }
    
        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }
    
    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)
    
    #endregion
    
    #region - export a log file from the cluster to the SQL database
    
    Write-Host "Preprocessing the source file ..." -ForegroundColor Green
    
    $tableName_log4j = "log4jlogs"
    
    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    
    $exportDir_log4j = "/tutorials/usesqoop/data"
    
    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
    
    #endregion
    
    #region - export a Hive table
    
    $tableName_mobile = "mobiledata"
    $exportDir_mobile = "/hive/warehouse/hivesampletable"
    
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    
    #endregion
    
    #region - import a database
    
    $targetDir_mobile = "/tutorials/usesqoop/importeddata/"
    
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
    
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    
    #endregion



[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
