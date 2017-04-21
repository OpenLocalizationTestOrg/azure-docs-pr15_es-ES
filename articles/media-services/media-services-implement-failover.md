<properties 
    pageTitle="Implementación de migración tras error transmisión escenario | Microsoft Azure" 
    description="Este tema muestra cómo implementar la transmisión de escenario de migración tras error." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>

#<a name="implementing-failover-streaming-scenario"></a>Implementación de migración tras error transmisión escenario

Este tutorial muestra cómo copiar contenido (BLOB) de un activo a otra con el fin de controlar la redundancia de transmisión a petición. Este escenario es muy útil para los clientes que desea configurar su CDN para conmutar por error entre dos centros de datos en el caso de una interrupción en uno de nuestros centros de datos.
Este tutorial utiliza Microsoft Azure Media Services SDK, API de REST de Microsoft Azure Media Services y SDK de almacenamiento de Azure para mostrar las siguientes tareas.

1. Configurar una cuenta de Media Services en el"Centro de datos".
1. Cargar un archivo mezzanine en un activo de origen.
1. Codificar activo en archivos de varias velocidades MP4. 
1. Crear un localizador de SA de solo lectura para el activo de origen tener acceso de lectura al contenedor de la cuenta de almacenamiento que está asociada con el recurso de origen.
1. Obtener el nombre del contenedor de los activos de origen desde el buscador de SA sólo lectura creado en el paso anterior. Se necesita esta información para copiar BLOB entre cuentas de almacenamiento (se explica más adelante en el tema).
1. Crear un localizador de origen para el activo que se creó la tarea de codificación. 

A continuación, para controlar la migración tras error:

1. Configurar una cuenta de Media Services en "B del centro de datos".
1. Crear un activo vacío de destino en la cuenta de servicios de Media de destino.
1. Crear un localizador de SA de escritura para el activo de destino vacío tener acceso de escritura al contenedor de la cuenta de almacenamiento que está asociado con el activo de destino de destino.
1. Usar el SDK de almacenamiento de Azure para copiar BLOB (archivos de activos) entre la cuenta de almacenamiento de origen en "Centro de datos A" y la cuenta de almacenamiento de destino en "B del centro de datos" (estas cuentas de almacenamiento están asociadas a los activos de interés).
1. Asociar BLOB (archivos de activos) que se copiaron en el contenedor de blob de destino con el activo de destino. 
1. Crear un localizador de origen para el activo en "B del centro de datos" y especifique el localizador identificador generado para el activo en el"Centro de datos". 
1. Esto le da las direcciones URL streaming donde las rutas de acceso relativas de las direcciones URL son iguales (solo la base de las direcciones URL son diferentes). 
 
A continuación, para controlar las interrupciones, puede crear un CDN sobre estos localizadores de origen. 

Aplica teniendo en cuenta lo siguiente:

- La versión actual de Media Services SDK no admite la creación de un localizador con un identificador de localizador especificado. Para realizar esta tarea, usaremos la API de REST de servicios de medios.
- La versión actual de Media Services SDK no admite mediante programación generar información de IAssetFile que desea asociar un activo a archivos de activos. Para realizar esta tarea, usamos la API de REST de CreateFileInfos Media Services. 
- Activos de almacenamiento cifrado (AssetCreationOptions.StorageEncrypted) no se admiten para la replicación (ya que la clave de cifrado debe ser diferente en las dos cuentas de Media Services). 
- Si desea sacar partido de embalaje dinámico, primero debe obtener al menos una transmisión unidades reservados a petición. Para obtener más información, vea [Dinámicamente embalaje activos](media-services-dynamic-packaging-overview.md).
 

>[AZURE.NOTE]Puede usar la [Herramienta de Replicator](http://replicator.codeplex.com/) de Media Services como una alternativa a implementar un escenario de transmisión de migración tras error manualmente. Esta herramienta permite replicar activos entre dos cuentas de Media Services.

##<a name="prerequisites"></a>Requisitos previos
 
- Dos cuentas de Media Services en una suscripción de Azure nueva o existente. Vea [cómo crear una cuenta de servicios de medios](media-services-portal-create-account.md).
- Sistemas operativos: Windows 7, Windows 2008 R2 o Windows 8.
- .NET framework 4.5 o .NET Framework 4.
- Visual Studio 2010 SP1 o una versión posterior (Professional, Premium, Ultimate o Express).
 
##<a name="set-up-your-project"></a>Configurar el proyecto

En esta sección se crear y configurar un proyecto de aplicación de consola de C#.

1. Use Visual Studio para crear una solución que contiene el proyecto de aplicación de consola de C#. Escriba HandleRedundancyForOnDemandStreaming para el nombre y, a continuación, haga clic en Aceptar.
1. Cree la carpeta SupportFiles en el mismo nivel que el archivo de proyecto HandleRedundancyForOnDemandStreaming.csproj. En la carpeta SupportFiles crear las carpetas inicioarchivos y MP4Files. Copiar un archivo. mp4 en la carpeta MP4Files (en este ejemplo que se usa el archivo BigBuckBunny.mp4). 
1. Use **Nuget** para agregar referencias a archivos DLL relacionadas de Media Services. En el menú de Visual Studio principales, seleccione Herramientas -> Administrador de paquetes de biblioteca -> consola del Administrador de paquetes. En el tipo de ventana de consola paquete de instalación windowsazure.mediaservices y presione ENTRAR.
1. Agregar otras referencias necesarios para este proyecto: System.Configuration, System.Runtime.Serialization y System.Web.
1. Reemplazar con instrucciones que se han agregado al archivo Programs.cs de forma predeterminada con los siguientes:

        using System;
        using System.Configuration;
        using System.Globalization;
        using System.IO;
        using System.Net;
        using System.Runtime.Serialization.Json;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Web;
        using System.Xml;
        using System.Linq;
        using Microsoft.WindowsAzure;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Auth;



1. Agregue la sección appSettings al archivo config y actualizar los valores basados en la Media Services y almacenamiento de la clave y los valores de nombre. 

        <appSettings>
          <add key="MediaServicesAccountNameSource" value="Media-Services-Account-Name-Source"/>
          <add key="MediaServicesAccountKeySource" value="Media-Services-Account-Key-Source"/>
          <add key="MediaServicesStorageAccountNameSource" value="Media-Services-Storage-Account-Name-Source"/>
          <add key="MediaServicesStorageAccountKeySource" value="Media-Services-Storage-Account-Key-Source"/>
          <add key="MediaServicesAccountNameTarget" value="Media-Services-Account-Name-Target" />
          <add key="MediaServicesAccountKeyTarget" value=" Media-Services-Account-Key-Target" />
          <add key="MediaServicesStorageAccountNameTarget" value=" Media-Services-Storage-Account-Name-Target" />
          <add key="MediaServicesStorageAccountKeyTarget" value=" Media-Services-Storage-Account-Key-Target" />
        </appSettings>

##<a name="add-code-that-handles-redundancy-for-on-demand-streaming"></a>Agregar código que controle la redundancia de transmisión a petición.



1. Agregue los siguientes campos de nivel de clase para la clase de programa.
    
        // Read values from the App.config file.
        private static readonly string MediaServicesAccountNameSource = ConfigurationManager.AppSettings["MediaServicesAccountNameSource"];
        private static readonly string MediaServicesAccountKeySource = ConfigurationManager.AppSettings["MediaServicesAccountKeySource"];
        private static readonly string StorageNameSource = ConfigurationManager.AppSettings["MediaServicesStorageAccountNameSource"];
        private static readonly string StorageKeySource = ConfigurationManager.AppSettings["MediaServicesStorageAccountKeySource"];
        
        private static readonly string MediaServicesAccountNameTarget = ConfigurationManager.AppSettings["MediaServicesAccountNameTarget"];
        private static readonly string MediaServicesAccountKeyTarget = ConfigurationManager.AppSettings["MediaServicesAccountKeyTarget"];
        private static readonly string StorageNameTarget = ConfigurationManager.AppSettings["MediaServicesStorageAccountNameTarget"];
        private static readonly string StorageKeyTarget = ConfigurationManager.AppSettings["MediaServicesStorageAccountKeyTarget"];
        
        // Base support files path.  Update this field to point to the base path  
        // for the local support files folder that you create. 
        private static readonly string SupportFiles = Path.GetFullPath(@"../..\SupportFiles");
        
        // Paths to support files (within the above base path). 
        private static readonly string SingleInputMp4Path = Path.GetFullPath(SupportFiles + @"\MP4Files\BigBuckBunny.mp4");
        private static readonly string OutputFilesFolder = Path.GetFullPath(SupportFiles + @"\OutputFiles");
        
        // Class-level field used to keep a reference to the service context.
        static private CloudMediaContext _contextSource = null;
        static private CloudMediaContext _contextTarget = null;
        static private MediaServicesCredentials _cachedCredentialsSource = null;
        static private MediaServicesCredentials _cachedCredentialsTarget = null;



1. Reemplazar la definición del método predeterminado principal con el siguiente:
        
        static void Main(string[] args)
        {
            _cachedCredentialsSource = new MediaServicesCredentials(
                            MediaServicesAccountNameSource,
                            MediaServicesAccountKeySource);
        
            _cachedCredentialsTarget = new MediaServicesCredentials(
                            MediaServicesAccountNameTarget,
                            MediaServicesAccountKeyTarget);
        
            // Get server context.    
            _contextSource = new CloudMediaContext(_cachedCredentialsSource);
            _contextTarget = new CloudMediaContext(_cachedCredentialsTarget);
        
        
            IAsset assetSingleFile = CreateAssetAndUploadSingleFile(_contextSource,
                                        AssetCreationOptions.None,
                                        SingleInputMp4Path);
        
            IJob job = CreateEncodingJob(_contextSource, assetSingleFile);
        
            if (job.State != JobState.Error)
            {
                IAsset sourceOutputAsset = job.OutputMediaAssets[0];
                // Get the locator for Smooth Streaming
                var sourceOriginLocator = GetStreamingOriginLocator(_contextSource, sourceOutputAsset);
        
                Console.WriteLine("Locator Id: {0}", sourceOriginLocator.Id);
        
        
                // 1.Create a read-only SAS locator for the source asset to have read access to the container in the source Storage account (associated with the source Media Services account)
                var readSasLocator = GetSasReadLocator(_contextSource, sourceOutputAsset);
        
        
                // 2.Get the container name of the source asset from the read-only SAS locator created in the previous step
                string containerName = (new Uri(readSasLocator.Path)).Segments[1];
        
        
                // 3.Create a target empty asset in the target Media Services account
                var targetAsset = CreateTargetEmptyAsset(_contextTarget, containerName);
        
                // 4.Create a write SAS locator for the target empty asset to have write access to the container in the target Storage account (associated with the target Media Services account)
                ILocator writeSasLocator = CreateSasWriteLocator(_contextTarget, targetAsset);
        
                // Get asset container name.
                string targetContainerName = (new Uri(writeSasLocator.Path)).Segments[1];
        
        
                // 5.Copy the blobs in the source container (source asset) to the target container (target empty asset)
                CopyBlobsFromDifferentStorage(containerName, targetContainerName, StorageNameSource, StorageKeySource, StorageNameTarget, StorageKeyTarget);
        
        
                // 6.Use the CreateFileInfos Media Services REST API to automatically generate all the IAssetFile’s for the target asset. 
                //      This API call is not supported in the current Media Services SDK for .NET. 
                CreateFileInfosForAssetWithRest(_contextTarget, targetAsset, MediaServicesAccountNameTarget, MediaServicesAccountKeyTarget);
        
                // Check if the AssetFiles are now  associated with the asset.
                Console.WriteLine("Asset files assocated with the {0} asset:", targetAsset.Name);
                foreach (var af in targetAsset.AssetFiles)
                {
                    Console.WriteLine(af.Name);
                }
        
                // 7.Copy the Origin locator of the source asset to the target asset by using the same Id
                var replicatedLocatorPath = CreateOriginLocatorWithRest(_contextTarget,
                            MediaServicesAccountNameTarget, MediaServicesAccountKeyTarget,
                            sourceOriginLocator.Id, targetAsset.Id);
        
                // Create a full URL to the manifest file. Use this for playback
                // in streaming media clients. 
                string originalUrlForClientStreaming = sourceOriginLocator.Path + GetPrimaryFile(sourceOutputAsset).Name + "/manifest";
        
                Console.WriteLine("Original Locator Path: {0}\n", originalUrlForClientStreaming);
        
                string replicatedUrlForClientStreaming = replicatedLocatorPath + GetPrimaryFile(sourceOutputAsset).Name + "/manifest";
        
                Console.WriteLine("Replicated Locator Path: {0}", replicatedUrlForClientStreaming);
        
                readSasLocator.Delete();
                writeSasLocator.Delete();
        }

1. Definiciones de método que se llaman desde principales se definen a continuación.
        
        public static IAsset CreateAssetAndUploadSingleFile(CloudMediaContext context,
                                                        AssetCreationOptions assetCreationOptions,
                                                        string singleFilePath)
        {
            // For the AssetCreationOptions you can specify 
            // encryption options.
            //      None:  no encryption. By default, storage encryption is used. If you want to 
            //        create an unencrypted asset, you must set this option.
            //      StorageEncrypted:  storage encryption. Encrypts a clear input file 
            //        before it is uploaded to Azure storage. This is the default if not specified
            //      CommonEncryptionProtected:  for Common Encryption Protected (CENC) files. An 
            //        example is a set of files that are already PlayReady encrypted. 
        
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
        
            var asset = context.Assets.Create(assetName, assetCreationOptions);
        
            Console.WriteLine("Asset name: " + asset.Name);
        
            var fileName = Path.GetFileName(singleFilePath);
        
            var assetFile = asset.AssetFiles.Create(fileName);
        
            Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
            Console.WriteLine("Upload {0}", assetFile.Name);
        
            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading of {0}", assetFile.Name);
        
            return asset;
        }
        
        public static IJob CreateEncodingJob(CloudMediaContext context, IAsset asset)
        {
            // Declare a new job.
            IJob job = context.Jobs.Create("My encoding job");
        
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName(context,
                                                    "Media Encoder Standard");
        
            // Create a task with the encoding details, using a string preset.
            // In this case "H264 Multiple Bitrate 720p" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Multiple Bitrate 720p",
                TaskOptions.ProtectedConfiguration);
        
            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
        
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is in the clear (unencrypted). 
            var outputAssetName = "OutputAsset_" + Guid.NewGuid();
            task.OutputAssets.AddNew(outputAssetName,
                AssetCreationOptions.None);
        
            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);
        
            // Launch the job.
            job.Submit();
        
            // Optionally log job details. This displays basic job details
            // to the console and saves them to a JobDetails-{JobId}.txt file 
            // in your output folder.
            LogJobDetails(context, job.Id);
        
            // Check job execution and wait for job to finish. 
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();
        
            // Get an updated job reference.
            job = GetJob(context, job.Id);
        
            // Since we the output asset contains a set of Smooth Streaming files,
            // set the .ism file to be the primary file
            if (job.State != JobState.Error)
                SetPrimaryFile(job.OutputMediaAssets[0]);
        
            return job;
        }
        
        // Create a locator URL to a streaming media asset 
        // on an origin server.
        public static ILocator GetStreamingOriginLocator(CloudMediaContext context, IAsset assetToStream)
        {
            // Get a reference to the streaming manifest file from the  
            // collection of files in the asset. 
            IAssetFile manifestFile = GetPrimaryFile(assetToStream);
        
            // Create a 30-day readonly access policy. 
            // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
        
            IAccessPolicy policy = context.AccessPolicies.Create("Streaming policy",
                TimeSpan.FromDays(30),
                AccessPermissions.Read);
        
            // Create a locator to the streaming content on an origin. 
            ILocator originLocator = context.Locators.CreateLocator(LocatorType.OnDemandOrigin,
                assetToStream,
                policy,
                DateTime.UtcNow.AddMinutes(-5));
        
            // Return the locator. 
            return originLocator;
        }
        
        public static ILocator GetSasReadLocator(CloudMediaContext context, IAsset asset)
        {
            IAccessPolicy accessPolicy = context.AccessPolicies.Create("File Download Policy",
                TimeSpan.FromDays(30), AccessPermissions.Read);
        
            ILocator sasLocator = context.Locators.CreateLocator(LocatorType.Sas,
                asset, accessPolicy);
        
            return sasLocator;
        }
        
        public static ILocator CreateSasWriteLocator(CloudMediaContext context, IAsset asset)
        {
        
            IAccessPolicy writePolicy = context.AccessPolicies.Create("Write Policy",
                TimeSpan.FromDays(30), AccessPermissions.Write);
        
            ILocator sasLocator = context.Locators.CreateLocator(LocatorType.Sas,
                asset, writePolicy);
        
            return sasLocator;
        }
        
        public static IAsset CreateTargetEmptyAsset(CloudMediaContext context, string containerName)
        {
            // Create a new asset.
            IAsset assetToBeProcessed = context.Assets.Create(containerName,
                AssetCreationOptions.None);
        
            return assetToBeProcessed;
        }
        
        public static void CreateFileInfosForAssetWithRest(CloudMediaContext context, IAsset asset, string mediaServicesAccountNameTarget,
            string mediaServicesAccountKeyTarget)
        {
            string apiServer = "";
            string scope = "";
            string acsBaseAddress = "";
        
            string acsToken = GetAcsBearerToken(mediaServicesAccountNameTarget,
                                    mediaServicesAccountKeyTarget, scope, acsBaseAddress);
        
            if (!string.IsNullOrEmpty(acsToken))
            {
                CreateFileInfos(apiServer, acsToken, asset.Id);
            }
        }
        
        public static string CreateOriginLocatorWithRest(CloudMediaContext context, string mediaServicesAccountNameTarget,
            string mediaServicesAccountKeyTarget, string locatorIdToReplicate, string targetAssetId)
        {
            //Make sure we are not asking for a duplicate:
            var locator = context.Locators.Where(l => l.Id == locatorIdToReplicate).FirstOrDefault();
            if (locator != null)
                return "";
        
            string locatorNewPath = "";
            string apiServer = "";
            string scope = "";
            string acsBaseAddress = "";
        
            string acsToken = GetAcsBearerToken(mediaServicesAccountNameTarget,
                                    mediaServicesAccountKeyTarget, scope, acsBaseAddress);
        
            if (!string.IsNullOrEmpty(acsToken))
            {
                var asset = context.Assets.Where(a => a.Id == targetAssetId).FirstOrDefault();

                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var accessPolicy = context.AccessPolicies.Create("RestTest", TimeSpan.FromDays(100),
                                                                    AccessPermissions.Read);
                if (asset != null)
                {
                    string redirectedServiceUri = null;
        
                    var xmlResponse = CreateLocator(apiServer, out redirectedServiceUri, acsToken,
                                                                asset.Id, accessPolicy.Id,
                                                                (int)LocatorType.OnDemandOrigin,
                                                                DateTime.UtcNow.AddMinutes(-10), locatorIdToReplicate);
        
                    Console.WriteLine("Redirected to: " + redirectedServiceUri);
                    if (xmlResponse != null)
                    {
                        Console.WriteLine(String.Format("Locator Id: {0}",
                                                        xmlResponse.GetElementsByTagName("Id")[0].InnerText));
                        Console.WriteLine(String.Format("Locator Path: {0}",
                                xmlResponse.GetElementsByTagName("Path")[0].InnerText));
        
        
                        locatorNewPath = xmlResponse.GetElementsByTagName("Path")[0].InnerText;
                    }
                }
            }
        
            return locatorNewPath;
        }
        
        
        public static void SetPrimaryFile(IAsset asset)
        {
        
            var ismAssetFiles = asset.AssetFiles.ToList().
                        Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase))
                        .ToArray();
        
            if (ismAssetFiles.Count() != 1)
                throw new ArgumentException("The asset should have only one, .ism file");
        
            ismAssetFiles.First().IsPrimary = true;
            ismAssetFiles.First().Update();
        }
        
        public static IAssetFile GetPrimaryFile(IAsset asset)
        {
            var theManifest =
                    from f in asset.AssetFiles
                    where f.Name.EndsWith(".ism")
                    select f;
        
            // Cast the reference to a true IAssetFile type. 
            IAssetFile manifestFile = theManifest.First();
        
            return manifestFile;
        }
        
        public static IAsset RefreshAsset(CloudMediaContext context, IAsset asset)
        {
            asset = context.Assets.Where(a => a.Id == asset.Id).FirstOrDefault();
            return asset;
        }
        
        
        public static void CopyBlobsFromDifferentStorage(string sourceContainerName, string targetContainerName,
                                            string srcAccountName, string srcAccountKey,
                                            string destAccountName, string destAccountKey)
        {
            var srcAccount = new CloudStorageAccount(new StorageCredentials(srcAccountName, srcAccountKey), true);
            var destAccount = new CloudStorageAccount(new StorageCredentials(destAccountName, destAccountKey), true);
        
            var cloudBlobClient = srcAccount.CreateCloudBlobClient();
            var targetBlobClient = destAccount.CreateCloudBlobClient();
        
            var sourceContainer = cloudBlobClient.GetContainerReference(sourceContainerName);
            var targetContainer = targetBlobClient.GetContainerReference(targetContainerName);
            targetContainer.CreateIfNotExists();
        
        
            string blobToken = sourceContainer.GetSharedAccessSignature(new SharedAccessBlobPolicy()
            {
                // Specify the expiration time for the signature.
                SharedAccessExpiryTime = DateTime.Now.AddDays(1),
                // Specify the permissions granted by the signature.
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read
            });
        
        
            foreach (var sourceBlob in sourceContainer.ListBlobs())
            {
                string fileName = (sourceBlob as ICloudBlob).Name;
                var sourceCloudBlob = sourceContainer.GetBlockBlobReference(fileName);
                sourceCloudBlob.FetchAttributes();
        
                if (sourceCloudBlob.Properties.Length > 0)
                {
                    var destinationBlob = targetContainer.GetBlockBlobReference(fileName);
                    destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + blobToken));
        
                    while (true)
                    {
                        // The StartCopyFromBlob is an async operation, 
                        // so we want to check if the copy operation is completed before proceeding. 
                        // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                        destinationBlob.FetchAttributes();
                        if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                        {
                            break;
                        }
                        //It's still not completed. So wait for some time.
                        System.Threading.Thread.Sleep(1000);
                    }
                }
        
                Console.WriteLine(fileName);
            }
        
            Console.WriteLine("Done copying.");
        }
        private static IMediaProcessor GetLatestMediaProcessorByName(CloudMediaContext context, string mediaProcessorName)
        {
    
            var processor = context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
            return processor;
        }
        
        // This method is a handler for events that track job progress.   
        private static void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
        
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("********************");
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine("Please wait while local tasks or downloads complete...");
                    Console.WriteLine("********************");
                    Console.WriteLine();
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    LogJobStop(null, job.Id);
                    break;
                default:
                    break;
            }
        }
        
        private static void LogJobStop(CloudMediaContext context, string jobId)
        {
            StringBuilder builder = new StringBuilder();
            IJob job = GetJob(context, jobId);
        
            builder.AppendLine("\nThe job stopped due to cancellation or an error.");
            builder.AppendLine("***************************");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Job Name: " + job.Name);
            builder.AppendLine("Job State: " + job.State.ToString());
            builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
            // Log job errors if they exist.  
            if (job.State == JobState.Error)
            {
                builder.Append("Error Details: \n");
                foreach (ITask task in job.Tasks)
                {
                    foreach (ErrorDetail detail in task.ErrorDetails)
                    {
                        builder.AppendLine("  Task Id: " + task.Id);
                        builder.AppendLine("    Error Code: " + detail.Code);
                        builder.AppendLine("    Error Message: " + detail.Message + "\n");
                    }
                }
            }
            builder.AppendLine("***************************\n");
            // Write the output to a local file and to the console. The template 
            // for an error output file is:  JobStop-{JobId}.txt
            string outputFile = OutputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
            WriteToFile(outputFile, builder.ToString());
            Console.Write(builder.ToString());
        }
        
        private static void LogJobDetails(CloudMediaContext context, string jobId)
        {
            StringBuilder builder = new StringBuilder();
            IJob job = GetJob(context, jobId);
        
            builder.AppendLine("\nJob ID: " + job.Id);
            builder.AppendLine("Job Name: " + job.Name);
            builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());
        
            // Write the output to a local file and to the console. The template 
            // for an error output file is:  JobDetails-{JobId}.txt
            string outputFile = OutputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
            WriteToFile(outputFile, builder.ToString());
            Console.Write(builder.ToString());
        }
        
        // Replace ":" with "_" in Job id values so they can 
        // be used as log file names.  
        private static string JobIdAsFileName(string jobID)
        {
            return jobID.Replace(":", "_");
        }
        
        // Write method output to the output files folder.
        private static void WriteToFile(string outFilePath, string fileContent)
        {
            StreamWriter sr = File.CreateText(outFilePath);
            sr.WriteLine(fileContent);
            sr.Close();
        }
        
        private static IJob GetJob(CloudMediaContext context, string jobId)
        {
            // Use a Linq select query to get an updated 
            // reference by Id. 
            var jobInstance =
                from j in context.Jobs
                where j.Id == jobId
                select j;
        
            // Return the job reference as an Ijob. 
            IJob job = jobInstance.FirstOrDefault();
        
            return job;
        }
        
        private static IAsset GetAsset(CloudMediaContext context, string assetId)
        {
            // Use a LINQ Select query to get an asset.
            var assetInstance =
                from a in context.Assets
                where a.Id == assetId
                select a;
        
            // Reference the asset as an IAsset.
            IAsset asset = assetInstance.FirstOrDefault();
        
            return asset;
        }
        
        public static void DeleteAllAssets(CloudMediaContext context)
        {
            // Loop through and delete all assets.
            foreach (IAsset asset in context.Assets)
            {
                DeleteLocatorsForAsset(context, asset);
        
                asset.Delete();
            }
        }
        
        public static void DeleteLocatorsForAsset(CloudMediaContext context, IAsset asset)
        {
            string assetId = asset.Id;
            var locators = from a in context.Locators
                            where a.AssetId == assetId
                            select a;
            foreach (var locator in locators)
            {
                Console.WriteLine("Deleting locator {0} for asset {1}", locator.Path, assetId);
        
                locator.Delete();
            }
        }
        
        public static void DeleteAccessPolicy(CloudMediaContext context, string existingPolicyId)
        {
            // To delete a specific access policy, get a reference to the policy.  
            // based on the policy Id passed to the method.
            var policyInstance =
                    from p in context.AccessPolicies
                    where p.Id == existingPolicyId
                    select p;
        
            IAccessPolicy policy = policyInstance.FirstOrDefault();
        
            policy.Delete();
        
        }
        
        //////////////////////////////////////////////////////
        /// The following methods use REST calls.
        //////////////////////////////////////////////////////
        
        public static string GetAcsBearerToken(string clientId, string clientSecret, string scope, string accessControlServiceUri)
        {
            if (string.IsNullOrEmpty(clientId))
                throw new ArgumentNullException("clientId");
        
            if (string.IsNullOrEmpty(clientSecret))
                throw new ArgumentNullException("clientSecret");
        
            if (string.IsNullOrEmpty(scope))
            {
                scope = "urn:WindowsAzureMediaServices";
            }
            else if (!scope.ToLower().StartsWith("urn:"))
            {
                scope = "urn:" + scope;
            }
        
            if (string.IsNullOrEmpty(accessControlServiceUri))
            {
                accessControlServiceUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
            }
            else if (!accessControlServiceUri.ToLower().EndsWith("/v2/oauth2-13"))
            {
                accessControlServiceUri = accessControlServiceUri.TrimEnd('/') + "/v2/OAuth2-13";
            }
        
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(accessControlServiceUri);
            request.Method = "POST";
            request.ContentType = "application/x-www-form-urlencoded";
            request.KeepAlive = true;
            string acsBearerToken = null;
        
            var requestBytes = Encoding.ASCII.GetBytes("grant_type=client_credentials&client_id=" +
                clientId + "&client_secret=" + HttpUtility.UrlEncode(clientSecret) +
                "&scope=" + HttpUtility.UrlEncode(scope));
            request.ContentLength = requestBytes.Length;
        
            var requestStream = request.GetRequestStream();
            requestStream.Write(requestBytes, 0, requestBytes.Length);
            requestStream.Close();
        
            var response = (HttpWebResponse)request.GetResponse();
        
            if (response.StatusCode == HttpStatusCode.OK)
            {
                using (Stream responseStream = response.GetResponseStream())
                {
                    using (StreamReader stream = new StreamReader(responseStream))
                    {
                        string responseString = stream.ReadToEnd();
                        var reader = JsonReaderWriterFactory.CreateJsonReader(Encoding.UTF8.GetBytes(responseString),
                            new XmlDictionaryReaderQuotas());
        
                        while (reader.Read())
                        {
                            if ((reader.Name == "access_token") && (reader.NodeType == XmlNodeType.Element))
                            {
                                if (reader.Read())
                                {
                                    acsBearerToken = reader.Value;
                                    break;
                                }
                            }
                        }
                    }
                }
            }
        
            return acsBearerToken;
        }
        
        public static XmlDocument CreateLocator(string mediaServicesApiServerUri,
                                                out string redirectedMediaServicesApiServerUri,
                                                string acsBearerToken, string assetId,
                                                string accessPolicyId, int locatorType,
                                                DateTime startTime, string locatorIdToReplicate = null,
                                                bool autoRedirect = true)
        {
            if (string.IsNullOrEmpty(mediaServicesApiServerUri))
            {
                mediaServicesApiServerUri = "https://media.windows.net/api/";
            }
            if (!mediaServicesApiServerUri.EndsWith("/"))
                mediaServicesApiServerUri = mediaServicesApiServerUri + "/";
        
            if (string.IsNullOrEmpty(acsBearerToken)) throw new ArgumentNullException("acsBearerToken");
            if (string.IsNullOrEmpty(assetId)) throw new ArgumentNullException("assetId");
            if (string.IsNullOrEmpty(accessPolicyId)) throw new ArgumentNullException("accessPolicyId");
        
            redirectedMediaServicesApiServerUri = null;
            XmlDocument xmlResponse = null;
        
            StringBuilder sb = new StringBuilder();
            sb.Append("{ \"AssetId\" : \"" + assetId + "\"");
            sb.Append(", \"AccessPolicyId\" : \"" + accessPolicyId + "\"");
            sb.Append(", \"Type\" : \"" + locatorType + "\"");
            if (startTime != DateTime.MinValue)
                sb.Append(", \"StartTime\" : \"" + startTime.ToString("G", CultureInfo.CreateSpecificCulture("en-us")) + "\"");
            if (!string.IsNullOrEmpty(locatorIdToReplicate))
                sb.Append(", \"Id\" : \"" + locatorIdToReplicate + "\"");
            sb.Append("}");
        
            string requestbody = sb.ToString();
        
            try
            {
                var request = GenerateRequest("POST", mediaServicesApiServerUri, "Locators",
                    null, acsBearerToken, requestbody);
                var response = (HttpWebResponse)request.GetResponse();
        
                switch (response.StatusCode)
                {
                    case HttpStatusCode.MovedPermanently:
                        //Recurse once with the mediaServicesApiServerUri redirect Location:
                        if (autoRedirect)
                        {
                            redirectedMediaServicesApiServerUri = response.Headers["Location"];
                            string secondRedirection = null;
                            xmlResponse = CreateLocator(redirectedMediaServicesApiServerUri,
                                                        out secondRedirection, acsBearerToken,
                                                        assetId, accessPolicyId, locatorType,
                                                        startTime, locatorIdToReplicate, false);
                        }
                        else
                        {
                            Console.WriteLine("Redirection to {0} failed.",
                                mediaServicesApiServerUri);
                            return null;
                        }
                        break;
                    case HttpStatusCode.Created:
                        using (Stream responseStream = response.GetResponseStream())
                        {
                            using (StreamReader stream = new StreamReader(responseStream))
                            {
                                string responseString = stream.ReadToEnd();
                                var reader = JsonReaderWriterFactory.
                                    CreateJsonReader(Encoding.UTF8.GetBytes(responseString),
                                        new XmlDictionaryReaderQuotas());
        
                                xmlResponse = new XmlDocument();
                                reader.Read();
                                xmlResponse.LoadXml(reader.ReadInnerXml());
                            }
                        }
                        break;
        
                    default:
                        Console.WriteLine(response.StatusDescription);
                        break;
                }
            }
            catch (WebException ex)
            {
                Console.WriteLine(ex.Message);
            }
        
            return xmlResponse;
        }
        
        public static void CreateFileInfos(string mediaServicesApiServerUri,
                                    string acsBearerToken,
                                    string assetId
                                    )
        {
            if (String.IsNullOrEmpty(mediaServicesApiServerUri))
            {
                mediaServicesApiServerUri = "https://media.windows.net/api/";
            }
            if (!mediaServicesApiServerUri.EndsWith("/"))
                mediaServicesApiServerUri = mediaServicesApiServerUri + "/";
        
            if (String.IsNullOrEmpty(acsBearerToken)) throw new ArgumentNullException("acsBearerToken");
            if (String.IsNullOrEmpty(assetId)) throw new ArgumentNullException("assetId");
        
        
            string id = assetId.Replace(":", "%");
        
            UriBuilder builder = new UriBuilder(mediaServicesApiServerUri);
            builder.Path = Path.Combine(builder.Path, "CreateFileInfos");
            builder.Query = String.Format(CultureInfo.InvariantCulture, "assetid='{0}'", assetId);
        
            try
            {
                var request = GenerateRequest("GET", mediaServicesApiServerUri, "CreateFileInfos",
                    String.Format(CultureInfo.InvariantCulture, "assetid='{0}'", assetId), acsBearerToken, null);
        
                using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
                {
                    if (response.StatusCode == HttpStatusCode.MovedPermanently)
                    {
                        string redirectedMediaServicesApiUrl = response.Headers["Location"];
        
                        CreateFileInfos(redirectedMediaServicesApiUrl, acsBearerToken, assetId);
                    }
                    else if ((response.StatusCode != HttpStatusCode.OK) &&
                        (response.StatusCode != HttpStatusCode.Accepted) &&
                        (response.StatusCode != HttpStatusCode.Created) &&
                        (response.StatusCode != HttpStatusCode.NoContent))
                    {
                        // TODO: Throw a more specific exception.
                        throw new Exception("Invalid response received ");
                    }
                }
            }
            catch (WebException ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        
        
        private static HttpWebRequest GenerateRequest(string verb,
                                                        string mediaServicesApiServerUri,
                                                        string resourcePath, string query,
                                                        string acsBearerToken, string requestbody)
        {
            var uriBuilder = new UriBuilder(mediaServicesApiServerUri);
            uriBuilder.Path += resourcePath;
            if (query != null)
            {
                uriBuilder.Query = query;
            }
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uriBuilder.Uri);
            request.AllowAutoRedirect = false; //We manage our own redirects.
            request.Method = verb;
        
            if (resourcePath == "$metadata")
                request.MediaType = "application/xml";
            else
            {
                request.ContentType = "application/json;odata=verbose";
                request.Accept = "application/json;odata=verbose";
            }
        
            request.Headers.Add("DataServiceVersion", "3.0");
            request.Headers.Add("MaxDataServiceVersion", "3.0");
            request.Headers.Add("x-ms-version", "2.1");
            request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + acsBearerToken);
        
            if (requestbody != null)
            {
                var requestBytes = Encoding.ASCII.GetBytes(requestbody);
                request.ContentLength = requestBytes.Length;
        
                var requestStream = request.GetRequestStream();
                requestStream.Write(requestBytes, 0, requestBytes.Length);
                requestStream.Close();
            }
            else
            {
                request.ContentLength = 0;
            }
            return request;
        }
        

##<a name="next-steps"></a>Pasos siguientes

Ahora puede utilizar un administrador de tráfico para enrutar las solicitudes entre los dos centros de datos y, por tanto, migración tras error en el caso de los que las interrupciones.


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
