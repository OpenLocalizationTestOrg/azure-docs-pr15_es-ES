<properties 
   pageTitle="Introducción a análisis de lago de datos de Azure con PowerShell de Azure | Azure" 
   description="Obtenga información sobre cómo usar PowerShell de Azure para crear una cuenta de la tienda de lago de datos, crear una tarea de análisis de datos lago mediante SQL U y enviar el trabajo. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Tutorial: Introducción a análisis de lago de datos de Azure con PowerShell de Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Obtenga información sobre cómo usar PowerShell de Azure para crear cuentas de análisis de lago de datos de Azure, definir los trabajos de análisis de datos lago en [SQL U](data-lake-analytics-u-sql-get-started.md)y enviar trabajos a datos lago analítico cuentas. Para obtener más información sobre el análisis de datos lago, vea [información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md).

En este tutorial, desarrollará un trabajo que lee una pestaña archivo de valores (TSV) separados y lo convierte en un archivo de separados por comas (CSV) de valores. Para pasar por el mismo tutorial utilizar otras herramientas compatibles, haga clic en las pestañas en la parte superior de esta sección.

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).
- **Una estación de trabajo con PowerShell de Azure**. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).
    
##<a name="create-data-lake-analytics-account"></a>Crear cuenta de análisis de datos lago

Debe tener una cuenta de análisis de datos lago antes de poder ejecutar los trabajos. Para crear una cuenta de análisis de datos lago, debe especificar lo siguiente:

- **Grupo de recursos de Azure**: cuenta A datos lago análisis debe crearse dentro de un grupo de recursos de Azure. [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) le permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos para la aplicación en una única operación coordinada.  

    Para enumerar los grupos de recursos en su suscripción:
    
        Get-AzureRmResourceGroup
    
    Para crear un nuevo grupo de recursos:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"

- **Nombre de cuenta de análisis de datos lago**
- **Ubicación**: uno de los centros de datos de Azure que admita el análisis de datos lago.
- **Cuenta de lago de datos predeterminado**: cada cuenta de análisis de datos lago tiene una cuenta de lago de datos predeterminado.

    Para crear una nueva cuenta de lago de datos:

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

    > [AZURE.NOTE] El nombre de cuenta de lago datos solo debe contener letras minúsculas y números.



**Para crear una cuenta de análisis de datos lago**

1. Abra su estación de trabajo de Windows PowerShell ISE.
2. Ejecute el siguiente script:

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
        
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
        
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
        
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
        
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

##<a name="upload-data-to-data-lake"></a>Cargar datos en datos lago

En este tutorial, procesará algunos registros de búsqueda.  El registro de búsqueda puede almacenarse en el almacén de datos lago o almacenamiento de blobs de Windows Azure. 

Un archivo de registro de la búsqueda de ejemplo se ha copiado en un contenedor de blobs de Windows Azure público. Use el siguiente script de PowerShell para descargar el archivo en su estación de trabajo y, a continuación, cargar el archivo en la cuenta de almacén de datos lago predeterminada de su cuenta de análisis de datos lago.

    $dataLakeStoreName = "<The default Data Lake Store account name>"
    
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

El siguiente script de PowerShell muestra cómo obtener el nombre de almacén de datos lago predeterminado para una cuenta de análisis de datos lago:


    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

>[AZURE.NOTE] El Portal de Azure ofrece una interfaz de usuario para copiar los archivos de datos de ejemplo en la cuenta predeterminada de almacén de datos lago. Para obtener instrucciones, consulte [Introducción a Azure datos lago análisis con el Portal de Azure](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Análisis de datos lago también puede acceder a almacenamiento de blobs de Windows Azure.  Para cargar los datos en el almacenamiento de blobs de Windows Azure, consulte [Usar PowerShell de Azure con el almacenamiento de Azure](../storage/storage-powershell-guide-full.md).

##<a name="submit-data-lake-analytics-jobs"></a>Enviar trabajos de análisis de datos lago

Los trabajos de análisis de datos lago se escriben en lenguaje SQL U. Para obtener más información acerca de SQL U, consulte [referencia de lenguaje SQL U](http://go.microsoft.com/fwlink/?LinkId=691348)y [empezar a trabajar con el lenguaje SQL U](data-lake-analytics-u-sql-get-started.md) .

**Para crear un script de trabajo de análisis de datos lago**

- Crear un archivo de texto con la siguiente secuencia de comandos de SQL U y guarde el archivo de texto en su estación de trabajo:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Esta secuencia de comandos SQL U lee el archivo de datos de origen con **Extractors.Tsv()**y, a continuación, crea un archivo csv con **Outputters.Csv()**. 
    
    No modifique las dos rutas a menos que copie el archivo de origen en una ubicación diferente.  Análisis de datos lago creará la carpeta de salida si no existe.
    
    Es más fácil usar rutas relativas de los archivos almacenan de forma predeterminada en cuentas de lago de datos. También puede usar rutas de acceso absolutas.  Por ejemplo 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Debe usar rutas de acceso absolutas para acceder a los archivos en las cuentas de almacenamiento vinculados.  La sintaxis de los archivos almacenados en cuenta vinculada de almacenamiento de Azure es:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Contenedor de blobs de Windows Azure con BLOB público o permisos de acceso de los contenedores público no se admiten actualmente.    
    
    
**Para enviar el trabajo**

1. Abra su estación de trabajo de Windows PowerShell ISE.
2. Ejecute el siguiente script:

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
        
        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    En la secuencia de comandos, el archivo de script SQL U se almacena en c:\tutorials\data-lake-analytics\copyFile.usql. Actualice la ruta de acceso del archivo.
 
Una vez completada la tarea, puede usar los siguientes cmdlets para el archivo de la lista y descargar el archivo:
    
    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
    
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
    
    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
    
    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Vea también

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestaña en la parte superior de la página.
- Para ver una consulta más compleja, vea [registros de sitio Web de analizar mediante el análisis de lago de datos de Azure](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones SQL U, vea [secuencias de comandos SQL de U desarrollar mediante datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener U SQL, consulte [Introducción a Azure datos lago análisis U-SQL idioma](data-lake-analytics-u-sql-get-started.md).
- Para las tareas de administración, vea [Administrar Azure datos lago el análisis con el Portal de Azure](data-lake-analytics-manage-use-portal.md).
- Para obtener una visión general de análisis de lago de datos, vea [información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md).

