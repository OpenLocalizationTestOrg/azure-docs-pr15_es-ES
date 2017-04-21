<properties 
    pageTitle="Administración de medios servicios activos entre varias cuentas de almacenamiento | Microsoft Azure" 
    description="En este artículo le ofrecen instrucciones sobre cómo administrar activos de medios servicios entre varias cuentas de almacenamiento." 
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


#<a name="managing-media-services-assets-across-multiple-storage-accounts"></a>Administración de medios servicios activos entre varias cuentas de almacenamiento

Comenzando con Microsoft Azure Media Services 2.2, puede adjuntar varias cuentas de almacenamiento para una sola cuenta de Media Services. Capacidad de conectar varias cuentas de almacenamiento a una cuenta de Media Services proporciona las siguientes ventajas:

- Equilibrio de carga los activos entre varias cuentas de almacenamiento.
- Escala Media Services para grandes cantidades de procesamiento de contenido (como una cuenta de almacenamiento única tiene un límite máximo de 500 TB actualmente). 

Este tema muestra cómo adjuntar varias cuentas de almacenamiento a una cuenta de Media Services mediante la API de REST de administración de servicio de Azure. También se muestra cómo especificar las cuentas de almacenamiento diferente al crear activos con el SDK de servicios de medios. 

##<a name="considerations"></a>Consideraciones

Cuando vincula varias cuentas de almacenamiento a su cuenta de Media Services, se aplican las consideraciones siguientes:

- Todas las cuentas de almacenamiento conectadas a una cuenta de Media Services deben estar en el centro de datos de la misma que la cuenta de Media Services.
- Actualmente, una vez que una cuenta de almacenamiento está conectada a la cuenta especificada de Media Services, no se pueden separar.
- Cuenta de almacenamiento principal es el indicado durante la hora de creación de cuenta de Media Services. Actualmente, no puede cambiar la cuenta de almacenamiento predeterminada. 

Otras consideraciones:

Media Services usa el valor de la propiedad **IAssetFile.Name** al generar direcciones URL para la transmisión de contenido (por ejemplo, http://{WAMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, no se permite la codificación de porcentaje. El valor de la propiedad nombre no puede tener cualquiera de los siguientes [caracteres por ciento codificación-reservados](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Además, sólo puede haber una '.' para la extensión de nombre de archivo.

##<a name="to-attach-a-storage-account-with-azure-service-management-rest-api"></a>Para adjuntar una cuenta de almacenamiento con la API de REST de administración de servicio de Azure

Actualmente, la única manera de adjuntar varias cuentas de almacenamiento está usando la [API de REST de administración de servicio de Azure](http://msdn.microsoft.com/library/azure/dn167014.aspx). El código de ejemplo en el [Cómo: API de REST de administración de servicios de uso multimedia](https://msdn.microsoft.com/library/azure/dn167656.aspx) tema define el método de **AttachStorageAccountToMediaServiceAccount** que se une a una cuenta de almacenamiento a la cuenta de Media Services especificada. El código en el mismo tema define el método de **ListStorageAccountDetails** que muestra todas las cuentas de almacenamiento vinculadas a la cuenta de Media Services especificada.


##<a name="to-manage-media-services-assets-across-multiple-storage-accounts"></a>Para administrar los activos de Media Services entre varias cuentas de almacenamiento

El código siguiente utiliza el último SDK de servicios de medios para realizar las siguientes tareas:

1. Mostrar todas las cuentas de almacenamiento asociadas con la cuenta de Media Services especificada.
1. Recuperar el nombre de la cuenta de almacenamiento predeterminada.
1. Crear un nuevo activo en la cuenta de almacenamiento predeterminada.
1. Crear un activo de salida de la tarea de codificación en la cuenta de almacenamiento especificado.
    
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace MultipleStorageAccounts
        {
            class Program
            {
                // Location of the media file that you want to encode. 
                private static readonly string _singleInputFilePath =
                    Path.GetFullPath(@"../..\supportFiles\multifile\interview2.wmv");
        
                private static readonly string MediaServicesAccountName = 
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string MediaServicesAccountKey = 
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static CloudMediaContext _context;
                private static MediaServicesCredentials _cachedCredentials = null;
    
                static void Main(string[] args)
                {
    
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    MediaServicesAccountName,
                                    MediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
    
        
                    // Display the storage accounts associated with 
                    // the specified Media Services account:
                    foreach (var sa in _context.StorageAccounts)
                        Console.WriteLine(sa.Name);
        
                    // Retrieve the name of the default storage account.
                    var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
                    Console.WriteLine("Name: {0}", defaultStorageName.Name);
                    Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);
        
                    // Retrieve the name of a storage account that is not the default one.
                    var notDefaultStroageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
                    Console.WriteLine("Name: {0}", notDefaultStroageName.Name);
                    Console.WriteLine("IsDefault: {0}", notDefaultStroageName.IsDefault);
                    
                    // Create the original asset in the default storage account.
                    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, 
                        defaultStorageName.Name, _singleInputFilePath);
                    Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);
                    
                    // Create an output asset of the encoding job in the other storage account.
                    IAsset outputAsset = CreateEncodingJob(asset, notDefaultStroageName.Name, _singleInputFilePath);
                    if(outputAsset!=null)
                        Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);
        
                }
        
                static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
                {
                    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
                    
                    // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
                    var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);
        
                    var fileName = Path.GetFileName(singleFilePath);
        
                    var assetFile = asset.AssetFiles.Create(fileName);
        
                    Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    return asset;
                }
        
                static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My encoding job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);
        
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset", storageName,
                        AssetCreationOptions.None);
        
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new
                            EventHandler<JobStateChangedEventArgs>(StateChanged);
        
                    // Launch the job.
                    job.Submit();
        
                    // Check job execution and wait for job to finish. 
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                    progressJobTask.Wait();
        
                    // Get an updated job reference.
                    job = GetJob(job.Id);
        
                    // If job state is Error the event handling 
                    // method for job progress should log errors.  Here we check 
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        Console.WriteLine("\nExiting method due to job error.");
                        return null;
                    }
        
                    // Get a reference to the output asset from the job.
                    IAsset outputAsset = job.OutputMediaAssets[0];
        
                    return outputAsset;
                }
        
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
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
                            Console.WriteLine("An error occurred in {0}", job.Id);
                            break;
                        default:
                            break;
                    }
                }
        
                static IJob GetJob(string jobId)
                {
                    // Use a Linq select query to get an updated 
                    // reference by Id. 
                    var jobInstance =
                        from j in _context.Jobs
                        where j.Id == jobId
                        select j;
                    // Return the job reference as an Ijob. 
                    IJob job = jobInstance.FirstOrDefault();
        
                    return job;
                }
            }
        }
 

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
