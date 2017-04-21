<properties 
   pageTitle="Introducción a análisis de lago de datos de Azure con .NET SDK | Azure" 
   description="Aprenda a usar .NET SDK para crear cuentas de la tienda de lago de datos, crear tareas de análisis de datos lago y enviar trabajos escritos en SQL U. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/26/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-net-sdk"></a>Tutorial: Introducción a análisis de lago de datos de Azure con .NET SDK

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Obtenga información sobre cómo usar el SDK de .NET Azure enviar trabajos escritos en [SQL U](data-lake-analytics-u-sql-get-started.md) para el análisis de datos lago. Para obtener más información sobre el análisis de datos lago, vea [información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md).

En este tutorial, desarrollará una aplicación de consola de C# para enviar un trabajo de SQL U que lee una pestaña archivo de valores (TSV) separados y lo convierte en un archivo de archivo (CSV) de valores separados por comas. Para pasar por el mismo tutorial utilizar otras herramientas compatibles, haga clic en las pestañas en la parte superior de este artículo.

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **2015 de visual Studio, Visual Studio de 2013 update 4, o Visual Studio 2012 con Visual C++ instalado**.
- **Microsoft Azure SDK para .NET versión 2.5 o posterior**.  Instalar mediante el [Instalador de plataforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **Cuenta de un análisis de lago de datos de Azure**. Consulte [Administrar datos lago análisis con Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md).

##<a name="create-console-application"></a>Crear la aplicación de consola

En este tutorial, procesar algunos registros de búsqueda.  El registro de búsqueda puede almacenarse en el almacén de datos lago o almacenamiento de blobs de Windows Azure. 

Ejemplo de registro de búsqueda puede encontrarse en un contenedor de blobs de Windows Azure público. En la aplicación, se descargue el archivo en su estación de trabajo y, a continuación, cargar el archivo en la cuenta de almacén de datos lago predeterminada de su cuenta de análisis de datos lago.

**Para crear una secuencia de comandos SQL U**

Tareas de análisis de datos lago se escriben en lenguaje SQL U. Para obtener más información acerca de SQL U, consulte [referencia de lenguaje SQL U](http://go.microsoft.com/fwlink/?LinkId=691348)y [empezar a trabajar con el lenguaje SQL U](data-lake-analytics-u-sql-get-started.md) .

Crear un archivo **SampleUSQLScript.txt** con el siguiente script U SQL y a continuación, coloque el archivo en el **C:\temp\* * ruta de acceso.  La ruta de acceso está codificado en la aplicación de .NET que cree en el procedimiento siguiente.  

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

En el programa de C#, debe preparar el archivo **/Samples/Data/SearchLog.tsv** y la carpeta **/Output/** .    

Es más fácil usar rutas relativas de los archivos almacenan de forma predeterminada en cuentas de lago de datos. También puede usar rutas de acceso absolutas.  Por ejemplo 

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
    
Debe usar rutas de acceso absolutas para acceder a los archivos en las cuentas de almacenamiento vinculados.  La sintaxis de los archivos almacenados en la cuenta de almacenamiento de Azure vinculada es:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

>[AZURE.NOTE] Actualmente no hay un problema conocido con el servicio de lago de datos de Azure.  Si la aplicación de ejemplo se interrumpe o encuentra un error, debe eliminar manualmente las cuentas de almacén de lago de datos y análisis de datos lago que crea la secuencia de comandos.  Si no está familiarizado con el portal de Azure, la Guía de [análisis de lago de datos de Azure administrar con Azure portal](data-lake-analytics-manage-use-portal.md) le ayudarán a trabajar.       

**Para crear una aplicación**

1. Abra Visual Studio.
2. Crear una aplicación de consola de C#.
3. Abra la consola de administración de paquetes de NuGet y ejecute los comandos siguientes:

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage


       
5. En Program.cs, pegue el siguiente código:

        using System;
        using System.IO;
        using System.Collections.Generic;
        using System.Threading;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.WindowsAzure.Storage.Blob;

        namespace SdkSample
        {
          class Program
          {
            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
            private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
        
            private static void Main(string[] args)
            {
                string localFolderPath = @"c:\temp\";

                // Connect to Azure
                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                SetupClients(creds, SUBSCRIPTIONID);

                // Transfer the source file from a public Azure Blob container to Data Lake Store.
                CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
                UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
                WaitForNewline("Source data file prepared.", "Submitting a job.");


                // Submit the job
                Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
                WaitForNewline("Job submitted.", "Waiting for job completion.");

                // Wait for job completion
                WaitForJob(jobId);
                WaitForNewline("Job completed.", "Downloading job output.");

                // Download job output
                DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
        
                WaitForNewline("Job output downloaded. You can now exit.");
            }
        
            public static ServiceClientCredentials AuthenticateAzure(
                string domainName,
                string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
            {
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
                _adlaClient.SubscriptionId = subscriptionId;

                _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }

            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
                var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                var uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.Execute();
            }

            public static void DownloadFile(string srcPath, string destPath)
            {
                var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
                var fileStream = new FileStream(destPath, FileMode.Create);

                stream.CopyTo(fileStream);
                fileStream.Close();
                stream.Close();
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                Console.WriteLine(reason + "\r\nPress ENTER to continue...");

                Console.ReadLine();

                if (!String.IsNullOrWhiteSpace(nextAction))
                    Console.WriteLine(nextAction);
            }


            // List all Data Lake Analytics accounts within the subscription
            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
                for (int i = 0; i < accounts.Count; i++)
                {
                    Console.WriteLine(accounts[i].Name);
                }

                return accounts;
            }
            public static Guid SubmitJobByPath(string scriptPath, string jobName)
            {
                var script = File.ReadAllText(scriptPath);

                var jobId = Guid.NewGuid();
                var properties = new USqlJobProperties(script);
                var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
                var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);

                return jobId;
            }

            public static JobResult WaitForJob(Guid jobId)
            {
                var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                while (jobInfo.State != JobState.Ended)
                {
                    jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                }
                return jobInfo.Result.Value;
            }
          }
        }

6. Presione **F5** para ejecutar la aplicación. El resultado es como:

    ![Trabajo de análisis de lago de datos de Azure SQL U .NET SDK de salida](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)

7. Compruebe el archivo de salida.  El nombre de archivo y la ruta de acceso predeterminada es c:\Temp\SearchLog-from-Data-Lake.csv.

## <a name="see-also"></a>Vea también

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestaña en la parte superior de la página.
- Para ver una consulta más compleja, vea [registros de sitio Web de analizar mediante el análisis de lago de datos de Azure](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones SQL U, vea [secuencias de comandos SQL de U desarrollar mediante datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener SQL U, consulte [referencia del lenguaje SQL U](http://go.microsoft.com/fwlink/?LinkId=691348)y [empezar a trabajar con el lenguaje de datos de Azure lago análisis U-SQL](data-lake-analytics-u-sql-get-started.md).
- Para las tareas de administración, vea [administrar el análisis de Azure datos lago con Azure portal](data-lake-analytics-manage-use-portal.md).
- Para obtener una visión general de análisis de lago de datos, vea [información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md).
