<properties
    pageTitle="Analizar datos de retrasos de vuelos con Hadoop en HDInsight | Microsoft Azure"
    description="Obtenga información sobre cómo usar un script de Windows PowerShell para crear un clúster de HDInsight, ejecutar un trabajo de sección, ejecutar un trabajo Sqoop y eliminar el clúster."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analizar datos de vuelo retraso usando subárbol en HDInsight

Sección proporciona un medio de trabajos en ejecución Hadoop MapReduce a través de un lenguaje de secuencias de comandos de SQL como denominado * [HiveQL][hadoop-hiveql]*, que se pueden aplicar a resumir, consultar y analizar grandes volúmenes de datos.

> [AZURE.NOTE] Los pasos de este documento requieren un clúster basado en Windows HDInsight. Para conocer los pasos que se trabaja con un clúster de Linux, consulte [analizar los datos de retrasos de vuelo utilizando subárbol en HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

Una de las ventajas principales de Azure HDInsight es la separación de almacenamiento de datos y calcular. HDInsight usa almacenamiento de blobs de Windows Azure almacenamiento de datos. Un trabajo típico consiste en tres partes:

1. **Almacenar los datos en el almacenamiento de blobs de Windows Azure.**  Por ejemplo, weather datos, datos de sensor, registros web y, a continuación, en este caso, se guardan los datos de vuelo retraso en el almacenamiento de blobs de Windows Azure.
2. **Ejecutar trabajos.** Cuando es el momento de procesar los datos, ejecute una secuencia de comandos de Windows PowerShell (o una aplicación de cliente) para crear un clúster de HDInsight, ejecute trabajos y eliminar el clúster. Los trabajos de guardar los datos en el almacenamiento de blobs de Windows Azure. Los datos de salida se conservan incluso después de que el clúster se elimina. De este modo, usted paga por solo lo que han consumido.
3. **Recuperar el resultado de almacenamiento de blobs de Windows Azure**, o en este tutorial, exporte los datos a una base de datos de SQL Azure.

El diagrama siguiente muestra el escenario y la estructura de este tutorial:

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

**Nota**: los números en el diagrama corresponden a los títulos de sección. **M** significa el proceso principal. Significa el contenido en el apéndice **A** .

La parte principal del tutorial muestra cómo usar un script de Windows PowerShell para realizar las siguientes tareas:

- Crear un clúster de HDInsight.
- Ejecutar una tarea de sección en el clúster para calcular el promedio retrasos en los aeropuertos. Los datos de retrasos de vuelo se almacenan en una cuenta de almacenamiento de blobs de Windows Azure.
- Ejecutar una tarea de Sqoop para exportar la salida de trabajo de la sección a una base de datos de SQL Azure.
- Eliminar el clúster de HDInsight.

En los apéndices, puede encontrar las instrucciones para cargar los datos de retrasos de vuelo, crear o cargar una cadena de consulta de la sección y preparar la base de datos de SQL Azure para el trabajo Sqoop.

> [AZURE.NOTE] Los pasos de este documento son específicos para clústeres HDInsight basado en Windows. Para conocer los pasos que se trabaja con un clúster de Linux, vea [analizar datos de retrasos de vuelos con subárbol en HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

###<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener los siguientes elementos:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con PowerShell de Azure**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

**Archivos que se utilizan en este tutorial**

Este tutorial utiliza el rendimiento en tiempo de datos de vuelo aéreo de [investigación y administración de tecnología innovadora, centro de estadísticas de transporte o RITA] [rita-website]. Una copia de los datos se ha cargado en un contenedor de almacenamiento de blobs de Windows Azure con el permiso de acceso del público Blob. Un elemento de la secuencia de comandos de PowerShell copia los datos desde el contenedor de blob público al contenedor de blob predeterminado del clúster. La secuencia de comandos de HiveQL también se copia en el mismo contenedor de Blob. Si desea aprender cómo obtener/carga los datos a su cuenta de almacenamiento y cómo crear y cargar el archivo de comandos HiveQL, vea [Apéndice A](#appendix-a) y [Apéndice B](#appendix-b).

La siguiente tabla enumera los archivos que se utilizan en este tutorial:

<table border="1">
<tr><th>Archivos</th><th>Descripción</th></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Archivo de script de HiveQL usado por el trabajo de la sección. Esta secuencia de comandos se ha cargado en una cuenta de almacenamiento de blobs de Windows Azure con acceso público. <a href="#appendix-b">Apéndice B</a> contiene instrucciones sobre la preparación y cargar este archivo en su cuenta de almacenamiento de blobs de Windows Azure.</td></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Datos de entrada para el trabajo de la sección. Se ha cargado los datos a una cuenta de almacenamiento de blobs de Windows Azure con acceso público. <a href="#appendix-a">Apéndice A</a> tiene instrucciones en obtener los datos y cargar los datos a su cuenta de almacenamiento de blobs de Windows Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>La ruta de salida para el trabajo de la sección. El contenedor predeterminado se utiliza para almacenar los datos de salida.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>La carpeta de estado del trabajo de sección en el contenedor predeterminado.</td></tr>
</table>


##<a name="create-cluster-and-run-hivesqoop-jobs"></a>Crear clúster y ejecutar trabajos subárbol/Sqoop

Hadoop MapReduce es el proceso por lotes. La manera más rentable para ejecutar un trabajo subárbol es crear un clúster para el trabajo y a continuación, elimine el trabajo una vez completada la tarea. La siguiente secuencia de comandos cubra todo el proceso. Para obtener más información sobre cómo crear un clúster de HDInsight y ejecución de trabajos de sección, vea [crear Hadoop clústeres en HDInsight] [ hdinsight-provision] y [Usar subárbol con HDInsight][hdinsight-use-hive].

**Para ejecutar las consultas de la sección PowerShell de Azure**

1. Crear una base de datos de SQL Azure y la tabla para la salida de trabajo Sqoop siguiendo las instrucciones de [Apéndice C](#appendix-c).
3. Abrir Windows PowerShell ISE y ejecute el siguiente script:

        $subscriptionID = "<Azure Subscription ID>"
        $nameToken = "<Enter an Alias>" 
        
        ###########################################
        # You must configure the follwing variables
        # for an existing Azure SQL Database
        ###########################################
        $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
        $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
        $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
        $existingSqlDatabaseName = "<Azure SQL Database name>"
        
        $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files. 
        $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
        
        ###########################################
        # (Optional) configure the following variables
        ###########################################
        
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $location = "EAST US 2"
        
        $HDInsightClusterName = $namePrefix + "hdi"
        $httpUserName = "admin"
        $httpPassword = "<Enter the Password>"
        
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $HDInsightClusterName # use the cluster name
        
        $existingSqlDatabaseTableName = "AvgDelays"
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
        
        $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
        
        $jobStatusFolder = "/tutorials/flightdelays/jobstatus"
        
        ###########################################
        # Login
        ###########################################
        try{
            $acct = Get-AzureRmSubscription
        }
        catch{
            Login-AzureRmAccount
        }
        Select-AzureRmSubscription -SubscriptionID $subscriptionID
        
        ###########################################
        # Create a new HDInsight cluster
        ###########################################
        
        # Create ARM group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
        # Create the default storage account
        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
        
        # Create the default Blob container
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
        New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
        
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
            -DefaultStorageContainer $existingDefaultBlobContainerName 
        
        
        ###########################################
        # Prepare the HiveQL script and source data
        ###########################################
        
        # Create the temp location  
        New-Item -Path $localFolder -ItemType Directory -Force 
        
        # Download the sample file from Azure Blob storage
        $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
        $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
        #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
        
        # Upload data to default container
        
        $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
        
        Invoke-Expression -Command:$azcopycmd
        
        ###########################################
        # Submit the Hive job
        ###########################################
        Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
        $response = Invoke-AzureRmHDInsightHiveJob `
                        -Files $hqlScriptFile `
                        -DefaultContainer $defaultBlobContainerName `
                        -DefaultStorageAccountName $defaultStorageAccountName `
                        -DefaultStorageAccountKey $defaultStorageAccountKey `
                        -StatusFolder $jobStatusFolder 
        
        write-Host $response
        
        
        ###########################################
        # Submit the Sqoop job
        ###########################################
        $exportDir = "wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
        
        $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                        -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
        $sqoopJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $hdinsightClusterName `
                        -HttpCredential $httpCredential `
                        -JobDefinition $sqoopDef #-Debug -Verbose
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -HttpCredential $httpCredential `
            -WaitTimeoutInSeconds 3600 `
            -Job $sqoopJob.JobId
        
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -HttpCredential $httpCredential `
            -DefaultContainer $existingDefaultBlobContainerName `
            -DefaultStorageAccountName $defaultStorageAccountName `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -JobId $sqoopJob.JobId `
            -DisplayOutputType StandardError
        
        ###########################################
        # Delete the cluster
        ###########################################
        Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. Conectarse a la base de datos SQL y ver las demoras de vuelo promedio por ciudad en la tabla AvgDelays:

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Apéndice A - carga los datos de retrasos de vuelos con el almacenamiento de blobs de Windows Azure
Cargar el archivo de datos y los archivos de comandos de HiveQL (consulte [Apéndice B](#appendix-b)) requiere la planeación. La idea es almacenar los archivos de datos y el archivo HiveQL antes de crear un clúster de HDInsight y ejecutar el trabajo de la sección. Tiene dos opciones:

- **Use la misma cuenta de almacenamiento de Azure que utilizará el clúster HDInsight como el sistema de archivos de forma predeterminada.** Debido a que el clúster HDInsight tiene la tecla de acceso de la cuenta de almacenamiento, no es necesario realizar cambios adicionales.
- **Usar una cuenta diferente de almacenamiento de Azure desde el sistema de archivos de HDInsight clúster predeterminado.** Si este es el caso, debe modificar la parte de la creación de la secuencia de comandos de Windows PowerShell que se encuentra en el [clúster de HDInsight crear y ejecutar trabajos subárbol/Sqoop](#runjob) para vincular la cuenta de almacenamiento como una cuenta de almacenamiento adicional. Para obtener instrucciones, vea [crear Hadoop clústeres en HDInsight][hdinsight-provision]. El clúster HDInsight sabe la tecla de acceso para la cuenta de almacenamiento.

>[AZURE.NOTE] La ruta de almacenamiento de blobs para el archivo de datos está codificado en el archivo de script de HiveQL. Debe actualizar según corresponda.

**Para descargar los datos de vuelo**

1. Vaya a [referencia y administración de la tecnología innovadora, oficina de estadísticas de transporte][rita-website].
2. En la página, seleccione los valores siguientes:

    <table border="1">
    <tr><th>Nombre</th><th>Valor</th></tr>
    <tr><td>Año de filtro</td><td>2013 </td></tr>
    <tr><td>Período de filtro</td><td>Enero</td></tr>
    <tr><td>Campos</td><td>*Año*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *origen*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (desactive todos los demás campos)</td></tr>
    </table>

3. Haga clic en **Descargar**.
4. Descomprima el archivo a la carpeta **C:\Tutorials\FlightDelay\2013Data** . Cada archivo es un archivo CSV y es de aproximadamente 60GB de tamaño.
5.  Cambie el nombre en el nombre del mes que contiene datos. Por ejemplo, el archivo que contiene los datos de enero podría denominarse *January.csv*.
6. Repita los pasos 2 y 5 para descargar un archivo para cada uno de los 12 meses de 2013. Se necesita un mínimo de un archivo para ejecutar el tutorial.  

**Para cargar los datos de vuelo retraso en el almacenamiento de blobs de Windows Azure**

1. Preparar los parámetros:

    <table border="1">
    <tr><th>Nombre de la variable</th><th>Notas</th></tr>
    <tr><td>$storageAccountName</td><td>La cuenta de almacenamiento de Azure donde desea cargar los datos.</td></tr>
    <tr><td>$blobContainerName</td><td>El contenedor de blobs de Windows donde desea cargar los datos.</td></tr>
    </table>
2. Abra Azure PowerShell ISE.
3. Pegue la siguiente secuencia de comandos en el panel de secuencia de comandos:

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
        $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
        #EndRegion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"
        
                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
        
                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }
        
        # List the uploaded files on HDInsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion




4. Presione **F5** para ejecutar la secuencia de comandos.

Si decide usar otro método para cargar los archivos, asegúrese de que la ruta de acceso del archivo es flightdelay/tutoriales/datos. La sintaxis para tener acceso a los archivos es:

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Los tutoriales, flightdelay/datos ruta es la carpeta virtual que creó al cargar los archivos. Compruebe que hay 12 archivos, uno para cada mes.

>[AZURE.NOTE] Debe actualizar la consulta de la sección lectura desde la nueva ubicación.

> Debe configurar el permiso de acceso de contenedor público o enlazar la cuenta de almacenamiento al clúster HDInsight. En caso contrario, la cadena de consulta de la sección no podrá tener acceso a los archivos de datos.

---
##<a id="appendix-b"></a>Apéndice B: crear y cargar una secuencia de comandos de HiveQL

Usando PowerShell de Azure, puede ejecutar varias instrucciones HiveQL uno a la vez o empaquetar la instrucción HiveQL en un archivo de script. Esta sección muestra cómo crear una secuencia de comandos de HiveQL y cargar la secuencia de comandos con el almacenamiento de blobs de Windows Azure con PowerShell de Azure. Sección requiere las secuencias de comandos de HiveQL se almacenan en el almacenamiento de blobs de Windows Azure.

La secuencia de comandos de HiveQL realizará las siguientes acciones:

1. **Eliminar la tabla delays_raw**, en caso de la tabla ya se encuentra.
2. **Crear la tabla subárbol externa delays_raw** apuntando a la ubicación de almacenamiento de blobs con los archivos de retraso de vuelo. Esta consulta especifica que los campos están delimitados por "," y que las líneas terminan por "\n". Esto supone un problema cuando los valores de campo contienen comas porque subárbol no distingue una coma que es un delimitador de campo y un elemento que forma parte de un valor de campo (que es el caso de los valores de campo de origen en\_Ciudad\_nombre y DEST\_Ciudad\_nombre). Para solucionar esto, la consulta crea columnas TEMP para que contenga los datos que se divida incorrectamente en columnas.  
3. **Eliminar la tabla retrasos**, en caso de la tabla ya se encuentra.
4. **Crear la tabla retrasos**. Resulta útil limpiar los datos antes de procesamiento. Esta consulta crea una nueva tabla, *retrasos*, de la tabla delays_raw. Tenga en cuenta que no se copian las columnas TEMP (como se mencionó anteriormente) y que se usa la función de **subcadena** para quitar las comillas de los datos.
5. **Nombre de la ciudad para calcular el retraso de tiempo promedio y grupos los resultados.** También mostrará los resultados para el almacenamiento de blobs. Observe que la consulta quitará apóstrofos de los datos y excluir filas donde el valor de **weather_delay** es nulo. Esto es necesario porque Sqoop, que se utilizan más adelante en este tutorial, no controla estos valores correctamente de forma predeterminada.

Para obtener una lista completa de los comandos HiveQL, vea [Subárbol lenguaje de definición de datos][hadoop-hiveql]. Cada comando HiveQL debe finalizar con un punto y coma.

**Para crear un archivo de script HiveQL**

1. Preparar los parámetros:

    <table border="1">
    <tr><th>Nombre de la variable</th><th>Notas</th></tr>
    <tr><td>$storageAccountName</td><td>La cuenta de almacenamiento de Azure donde desea cargar la secuencia de comandos HiveQL.</td></tr>
    <tr><td>$blobContainerName</td><td>El contenedor de blobs de Windows donde desea cargar la secuencia de comandos HiveQL.</td></tr>
    </table>
2. Abra Azure PowerShell ISE.

3. Copie y pegue la siguiente secuencia de comandos en el panel de secuencia de comandos:

        [CmdletBinding()]
        Param(

            # Azure Blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #region - Define variables
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        # The HiveQL script file is exported as this file before it's uploaded to Blob storage
        $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
        
        # The HiveQL script file will be uploaded to Blob storage as this blob name
        $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
        
        # These two constants are used by the HiveQL script file
        #$srcDataFolder = "tutorials/flightdelay/data"
        $dstDataFolder = "/tutorials/flightdelay/output"
        #endregion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #region - Validate the file and file path
        
        # Check if a file with the same file name already exists on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){
        
            $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
        
            if ($isDelete.ToLower() -ne "y")
            {
                Exit
            }
        }
        
        # Create the folder if it doesn't exist
        $folder = split-path $hqlLocalFileName
        if (-not (test-path $folder))
        {
            Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
        
            new-item $folder -ItemType directory  
        }
        #end region
        
        #region - Write the Hive script into a local file
        Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green
        
        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"
        
        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"
        
        $hqlDropDelays = "DROP TABLE delays;"
        
        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;"
        
        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"
        
        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
        
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
        #endregion
        
        #region - Upload the Hive script to the default Blob container
        Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
        
        # Create a storage context object
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        # Upload the file from local workstation to Blob storage
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
        #endregion
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    Estas son las variables utilizadas en la secuencia de comandos:

    - **$hqlLocalFileName** - la secuencia de comandos guarda localmente el archivo de comandos de HiveQL antes de cargarlo al almacenamiento de blobs de Windows. Este es el nombre de archivo. El valor predeterminado es <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
    - **$hqlBlobName** - este es el nombre de blobs de archivo de script de HiveQL utilizado en el almacenamiento de blobs de Windows Azure. El valor predeterminado es tutorials/flightdelay/flightdelays.hql. Dado que el archivo se escribirá directamente al almacenamiento de blobs de Windows Azure, no hay un "o" al principio del nombre del blob. Si desea tener acceso al archivo de almacenamiento de blobs, deberá agregar una "y" al principio del nombre de archivo.
    - **$srcDataFolder** y **$dstDataFolder** - = "tutoriales/flightdelay/data" = "tutoriales y flightdelay/salida"


---
##<a id="appendix-c"></a>Apéndice C - preparar una base de datos de SQL Azure para la salida de trabajo Sqoop
**Preparar la base de datos SQL (esta opción se puede combinar con la secuencia de comandos de Sqoop)**

1. Preparar los parámetros:

    <table border="1">
    <tr><th>Nombre de la variable</th><th>Notas</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>El nombre del servidor de base de datos de SQL Azure. Escriba nada para crear un nuevo servidor.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>El nombre de inicio de sesión para el servidor de base de datos de SQL Azure. Si $sqlDatabaseServerName es un servidor existente, el inicio de sesión y la contraseña de inicio de sesión se usan para autenticar con el servidor. En caso contrario, se utilizan para crear un nuevo servidor.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>La contraseña de inicio de sesión para el servidor de base de datos de SQL Azure.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Este valor se utiliza solo cuando se crea un nuevo servidor de base de datos de Azure.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>La base de datos SQL utilizado para crear la tabla de AvgDelays para el trabajo Sqoop. Si deja en blanco, se creará una base de datos denominado HDISqoop. El nombre de tabla para la salida de trabajo Sqoop es AvgDelays. </td></tr>
    </table>
2. Abra Azure PowerShell ISE.
3. Copie y pegue la siguiente secuencia de comandos en el panel de secuencia de comandos:

        [CmdletBinding()]
        Param(
        
            # Azure Resource group variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
            [String]$resourceGroupName,
        
            # SQL database server variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseServer, 
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseLogin,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.
        
            # SQL database variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
        )
        
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        #region - Constants and variables
        
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"
        
        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10
        
        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                    [origin_city_name] ASC
                )
                )"
        #endregion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #region - Create and validate Azure resouce group
        try{
            Get-AzureRmResourceGroup -Name $resourceGroupName
        }
        catch{
            New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
        }
        
        #EndRegion
        
        #region - Create and validate Azure SQL database server
        try{
            Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
        catch{
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
        
            $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
            $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
        
            $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
        
            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
        
            #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
        }
        
        #endregion
        
        #region - Create and validate Azure SQL database
        
        try {
            Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
        }
        catch {
            Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
            New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
        }
        
        #endregion
        
        #region -  Execute an SQL command to create the AvgDelays table
        
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()
        
        $conn.close()
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    >[AZURE.NOTE] La secuencia de comandos utiliza un servicio de transferencia (REST) de estado de representación, http://bot.whatismyipaddress.com, para recuperar la dirección IP externa. La dirección IP se usa para crear una regla de firewall para el servidor de base de datos SQL.  

    Estas son algunas variables utilizadas en la secuencia de comandos:

    - **$ipAddressRestService** - el valor predeterminado es http://bot.whatismyipaddress.com. Es una dirección IP pública servicio REST para obtener la dirección IP externa. Si lo desea puede usar otros servicios. La dirección IP externa recuperada a través del servicio se utilizará para crear una regla de firewall para el servidor de base de datos de SQL Azure, por lo que puede tener acceso a la base de datos desde su estación de trabajo (mediante un script de Windows PowerShell).
    - **$fireWallRuleName** - este es el nombre de la regla de firewall para el servidor de base de datos de SQL Azure. El nombre predeterminado es <u>FlightDelay</u>. Puede cambiar el nombre si lo desea.
    - **$sqlDatabaseMaxSizeGB** - este valor se utiliza solo cuando se crea un nuevo servidor de base de datos de SQL Azure. El valor predeterminado es 10GB. 10GB es suficiente para este tutorial.
    - **$sqlDatabaseName** - este valor se utiliza solo cuando está creando una nueva base de datos de SQL Azure. El valor predeterminado es HDISqoop. Si cambia el nombre, deberá actualizar el script Sqoop Windows PowerShell en consecuencia.

4. Presione **F5** para ejecutar la secuencia de comandos.
5. Valide el resultado de la secuencia de comandos. Asegúrese de que la secuencia de comandos se ha ejecutado correctamente.

##<a id="nextsteps"></a>Pasos siguientes
Ahora comprende cómo cargar un archivo con el almacenamiento de blobs de Windows Azure, cómo rellenar una tabla subárbol utilizando los datos de almacenamiento de blobs de Windows Azure, cómo ejecutar consultas de la sección y cómo usar Sqoop para exportar datos de HDFS a una base de datos de SQL Azure. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight][hdinsight-get-started]
* [Usar subárbol con HDInsight][hdinsight-use-hive]
* [Usar Oozie con HDInsight][hdinsight-use-oozie]
* [Usar Sqoop con HDInsight][hdinsight-use-sqoop]
* [Usar cerdo con HDInsight][hdinsight-use-pig]
* [Desarrollar programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
