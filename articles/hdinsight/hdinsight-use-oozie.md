<properties
    pageTitle="Usar Hadoop Oozie en HDInsight | Microsoft Azure"
    description="Usar Hadoop Oozie en HDInsight de un servicio de datos grande. Obtenga información sobre cómo definir un flujo de trabajo Oozie y enviar un trabajo Oozie."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jgao"/>


# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-in-hdinsight"></a>Use Oozie con Hadoop para definir y ejecutar un flujo de trabajo en HDInsight

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Obtenga información sobre cómo usar Apache Oozie para definir un flujo de trabajo y ejecutar el flujo de trabajo HDInsight. Para obtener información sobre el coordinador Oozie, vea [Usar tiempo basadas en el coordinador Oozie Hadoop con HDInsight][hdinsight-oozie-coordinator-time]. Para obtener generador de datos de Azure, consulte [usar cerdo y subárbol con el generador de datos][azure-data-factory-pig-hive].

Apache Oozie es un sistema de flujo de trabajo o junto a la que administra Hadoop trabajos. Se integra con la pila Hadoop y admite Hadoop trabajos de Apache MapReduce, Apache cerdo, Apache subárbol y Apache Sqoop. También puede usarse para programar trabajos que son específicos de un sistema, como los programas de Java o secuencias de comandos de shell.

El flujo de trabajo que se implementará siguiendo las instrucciones de este tutorial contiene dos acciones:

![Diagrama de flujo de trabajo][img-workflow-diagram]

1. Una acción de la sección ejecuta una secuencia de comandos HiveQL para contar las apariciones de cada tipo de nivel de registro en un archivo de log4j. Cada archivo log4j consta de una línea de campos que contiene un campo [nivel de registro] que muestra el tipo y la gravedad, por ejemplo:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    Es similar a la salida de la secuencia de comandos de sección:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Para obtener más información acerca de la sección, vea [Usar subárbol con HDInsight][hdinsight-use-hive].

2.  Una acción Sqoop exporta el resultado de HiveQL a una tabla en una base de datos de SQL Azure. Para obtener más información sobre Sqoop, vea [Usar Hadoop Sqoop con HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Para versiones Oozie compatibles en clústeres HDInsight, consulte [Novedades en las versiones de clúster Hadoop proporcionadas por HDInsight?] [hdinsight-versions].

###<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Una estación de trabajo con PowerShell de Azure**. 

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
    
    Para ejecutar las secuencias de comandos de Windows PowerShell, debe ejecutar como administrador y establecer la directiva de ejecución *RemoteSigned*. Para obtener más información, vea [secuencias de comandos de ejecutar Windows PowerShell][powershell-script].

##<a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definir flujo de trabajo de Oozie y la secuencia de comandos de HiveQL relacionado

Definiciones de Oozie flujos de trabajo se escriben en hPDL (un lenguaje de definición de proceso XML). El nombre de archivo de flujo de trabajo predeterminado es *workflow.xml*. A continuación se muestra el archivo de flujo de trabajo que se usa en este tutorial.

    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>

Hay dos acciones definidas en el flujo de trabajo. La acción de inicio es *RunHiveScript*. Si la acción se ejecuta correctamente, la siguiente acción es *RunSqoopExport*.

El RunHiveScript tiene varias variables. Al enviar el trabajo Oozie desde su estación de trabajo con Azure PowerShell pasará los valores.

<table border = "1">
<tr><th>Variables de flujo de trabajo</th><th>Descripción</th></tr>
<tr><td>${jobTracker}</td><td>Especifica la dirección URL de la herramienta de seguimiento del trabajo de Hadoop. Use <strong>jobtrackerhost:9010</strong> en HDInsight versión 3.0 y 2.1.</td></tr>
<tr><td>${nameNode}</td><td>Especifica la dirección URL del nodo de nombre Hadoop. Usar la dirección de sistema de archivo predeterminada, por ejemplo, <i>wasbs: / /&lt;nombre del contenedor&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
<tr><td>${Nombre}</td><td>Especifica el nombre de cola que el trabajo se enviarán a. Use el <strong>valor predeterminado</strong>.</td></tr>
</table>

<table border = "1">
<tr><th>Sección variable de acción</th><th>Descripción</th></tr>
<tr><td>${hiveDataFolder}</td><td>Especifica el directorio de origen para el comando de la sección Crear tabla.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Especifica la carpeta de salida de la instrucción de insertar SOBRESCRIBIR.</td></tr>
<tr><td>${hiveTableName}</td><td>Especifica el nombre de la tabla de la sección que hace referencia a los archivos de datos de log4j.</td></tr>
</table>

<table border = "1">
<tr><th>Variable de acción Sqoop</th><th>Descripción</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Especifica la cadena de conexión de base de datos de SQL Azure.</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>Especifica la tabla de base de datos de SQL Azure donde se exportarán los datos.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Especifica la carpeta de salida de la instrucción subárbol insertar SOBRESCRIBIR. Esta es la misma carpeta para la exportación de Sqoop (exportación dir).</td></tr>
</table>

Para obtener más información sobre el flujo de trabajo de Oozie y usar acciones de flujo de trabajo, consulte la [documentación de Apache Oozie 4.0] [ apache-oozie-400] (para HDInsight versión 3.0) o la [documentación de Apache Oozie 3.3.2] [ apache-oozie-332] (para HDInsight versión 2.1).


La acción de la sección del flujo de trabajo llama a un archivo de script de HiveQL. Este archivo de comandos contiene tres HiveQL instrucciones:

    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **Quitar tabla de la instrucción** elimina la tabla subárbol log4j si existe.
2. **Instrucción CREATE TABLE de la** crea una tabla externa de sección de log4j que apunta a la ubicación del archivo de registro log4j. El delimitador de campo es ",". El delimitador de línea predeterminado es "\n". Una tabla de subárbol externa se utiliza para evitar el archivo de datos que se quita de la ubicación original si desea ejecutar el flujo de trabajo Oozie varias veces.
3. **Insertar SOBRESCRIBIR la instrucción** cuenta las apariciones de cada tipo de nivel de registro de la tabla de la sección log4j y guarda el resultado en un objeto binario en el almacenamiento de Azure.


Hay tres variables utilizadas en la secuencia de comandos:

- ${hiveTableName}
- ${hiveDataFolder}
- ${hiveOutputFolder}

El archivo de definición de flujo de trabajo (workflow.xml en este tutorial) pasa estos valores para esta secuencia de comandos de HiveQL en tiempo de ejecución.

El archivo de flujo de trabajo y el archivo HiveQL se almacenan en un contenedor de blob.  La secuencia de comandos de PowerShell que se usa más adelante en este tutorial copiará ambos archivos en la cuenta de almacenamiento predeterminada. 

##<a name="submit-oozie-jobs-using-powershell"></a>Enviar trabajos de Oozie con PowerShell

PowerShell Azure actualmente no proporciona los cmdlets para definir Oozie trabajos. Puede usar el cmdlet **Invocar RestMethod** invocar servicios web de Oozie. La API de servicios web Oozie es una API de JSON del resto de HTTP. Para obtener más información acerca de los servicios web de Oozie API, consulte la [documentación Apache Oozie 4.0] [ apache-oozie-400] (para HDInsight versión 3.0) o la [documentación de Apache Oozie 3.3.2] [ apache-oozie-332] (para HDInsight versión 2.1).

La secuencia de comandos de PowerShell en esta sección realiza los pasos siguientes:

1. Conectarse a Azure.
2. Crear un grupo de recursos de Azure. Para obtener más información, consulte [Usar PowerShell de Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).
3. Crear un servidor de base de datos de SQL Azure, una base de datos de SQL Azure y dos tablas. Se utilizan por la acción Sqoop del flujo de trabajo.

    El nombre de tabla es *log4jLogCount*.

4. Crear un clúster de HDInsight que se usa para ejecutar trabajos Oozie.

    Para examinar el clúster, puede usar el portal de Azure o PowerShell de Azure.

5. Copie el archivo de flujo de trabajo de oozie y el archivo de comandos HiveQL en el sistema de archivos de forma predeterminada.

    Ambos archivos se almacenan en un contenedor de Blob público.
    
    - Copiar la secuencia de comandos de HiveQL (useoozie.hql) en el almacenamiento de Azure (wasbs:///tutorials/useoozie/useoozie.hql).
    - Copie el archivo workflow.xml en wasbs:///tutorials/useoozie/workflow.xml.
    - Copie el archivo de datos (o example/data/sample.log) a wasbs:///tutorials/useoozie/data/sample.log.
     
6. Enviar un trabajo de Oozie.

    Para examinar los resultados de trabajo OOzie, use Visual Studio u otras herramientas para conectarse a la base de datos de SQL Azure.

Aquí tiene el script.  Puede ejecutar la secuencia de comandos de Windows PowerShell ISE. Solo debe configurar las variables primeros 7.

    #region - provide the following values
    
    $subscriptionID = "<Enter your Azure subscription ID>"
    
    # SQL Database server login credentials used for creating and connecting
    $sqlDatabaseLogin = "<Enter SQL Database Login Name>"
    $sqlDatabasePassword = "<Enter SQL Database Login Password>"
    
    # HDInsight cluster HTTP user credential used for creating and connectin
    $httpUserName = "admin"  # The default name is "admin"
    $httpPassword = "<Enter HDInsight Cluster HTTP User Password>"
    
    # Used for creating Azure service names
    $nameToken = "<Enter an Alias>"
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
    catch{
        Login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
    }
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
        $sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
    
        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $sqlLoginCredentials `
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
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    
    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    #endregion
    
    #region - Create SQL database tables
    Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green
    
    $sqlDatabaseTableName = "log4jLogsCount"
    $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
            [Level] [nvarchar](10) NOT NULL,
            [Total] float,
        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
        (
        [Level] ASC
        )
        )"
    
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()
    
    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jCountTable
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
    
    #region - copy Oozie workflow and HiveQL files
    
    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
    
    # Both files are stored in a public Blob
    $publicBlobContext = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
    
    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    
    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "useooziewf.hql"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/useooziewf.hql" `
        -Force
    
    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "workflow.xml"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/workflow.xml" `
        -Force
    
    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/workflow.xml
    
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/useooziewf.hql
    
    #endregion
    
    #region - copy the sample.log file
    
    Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
    
    Start-CopyAzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -SrcContainer $defaultBlobContainerName `
        -SrcBlob "example/data/sample.log"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -destBlob "$destFolder/data/sample.log" 
    
    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/data/sample.log
    
    #endregion
    
    #region - submit Oozie job
    
    $storageUri="wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"
    
    $oozieJobName = $namePrefix + "OozieJob"
    
    #Oozie WF variables
    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10
    
    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
    
    #Sqoop action variables
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    
    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
    
    <property>
        <name>nameNode</name>
        <value>$storageUrI</value>
    </property>
    
    <property>
        <name>jobTracker</name>
        <value>jobtrackerhost:9010</value>
    </property>
    
    <property>
        <name>queueName</name>
        <value>default</value>
    </property>
    
    <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
    </property>
    
    <property>
        <name>hiveScript</name>
        <value>$hiveScript</value>
    </property>
    
    <property>
        <name>hiveTableName</name>
        <value>$hiveTableName</value>
    </property>
    
    <property>
        <name>hiveDataFolder</name>
        <value>$hiveDataFolder</value>
    </property>
    
    <property>
        <name>hiveOutputFolder</name>
        <value>$hiveOutputFolder</value>
    </property>
    
    <property>
        <name>sqlDatabaseConnectionString</name>
        <value>&quot;$sqlDatabaseConnectionString&quot;</value>
    </property>
    
    <property>
        <name>sqlDatabaseTableName</name>
        <value>$SQLDatabaseTableName</value>
    </property>
    
    <property>
        <name>user.name</name>
        <value>$httpUserName</value>
    </property>
    
    <property>
        <name>oozie.wf.application.path</name>
        <value>$oozieWFPath</value>
    </property>
    
    </configuration>
    "@
    
    Write-Host "Checking Oozie server status..." -ForegroundColor Green
    $clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus
    
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieServerSatus = $jsonResponse[0].("systemMode")
    Write-Host "Oozie server status is $oozieServerSatus."
    
    # create Oozie job
    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
    Write-Host "`n--------`n$OoziePayload`n--------"
    $clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug
    
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieJobId = $jsonResponse[0].("id")
    Write-Host "Oozie job id is $oozieJobId..."
    
    # start Oozie job
    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
    $clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug
    
    # get job status
    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
    
    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
    $clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $JobStatus = $jsonResponse[0].("status")
    
    while($JobStatus -notmatch "SUCCEEDED|KILLED")
    {
        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")
        $jobStatus
    }
    
    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green
    
    #endregion


**Para volver a ejecutar el tutorial**

Para volver a ejecutar el flujo de trabajo, debe eliminar la siguiente:

- El archivo de salida de la secuencia de comandos de sección
- Los datos de la tabla log4jLogsCount

Aquí es una secuencia de comandos de PowerShell de muestra que puede usar:

    $resourceGroupName = "<AzureResourceGroupName>"
    
    $defaultStorageAccountName = "<AzureStorageAccountName>"
    $defaultBlobContainerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServerName = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

##<a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo definir un flujo de trabajo Oozie y cómo ejecutar un trabajo de Oozie con PowerShell. Para obtener más información, consulte los artículos siguientes:

- [Usar tiempo basadas en el coordinador Oozie con HDInsight][hdinsight-oozie-coordinator-time]
- [Empezar a usar Hadoop con subárbol en HDInsight para analizar el uso de teléfono móvil][hdinsight-get-started]
- [Usar el almacenamiento de blobs de Windows Azure con HDInsight][hdinsight-storage]
- [Administrar HDInsight con PowerShell][hdinsight-admin-powershell]
- [Cargar datos para trabajos de Hadoop en HDInsight][hdinsight-upload-data]
- [Usar Sqoop con Hadoop en HDInsight][hdinsight-use-sqoop]
- [Usar subárbol con Hadoop en HDInsight][hdinsight-use-hive]
- [Usar cerdo con Hadoop en HDInsight][hdinsight-use-pig]
- [Desarrollar programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
