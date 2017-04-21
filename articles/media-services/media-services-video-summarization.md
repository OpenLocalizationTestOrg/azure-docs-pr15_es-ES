<properties
    pageTitle="Usar las miniaturas de vídeo multimedia de Azure para crear un resumen de vídeo | Microsoft Azure"
    description="Resumen de vídeo puede ayudarle a crear resúmenes de los vídeos largos seleccionando automáticamente fragmentos interesantes en el vídeo de origen. Esto es útil cuando desea proporcionar una descripción general de lo que pasará en un vídeo largo."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"   
    ms.author="milanga;juliako;"/>

#<a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Usar las miniaturas de vídeo multimedia de Azure para crear un resumen de vídeo
##<a name="overview"></a>Información general

Procesador de medios de **Miniaturas de vídeo de Azure Media** (programa mínimo) le permite crear un resumen de un vídeo que es muy útil para los clientes que solo desea obtener una vista previa de un resumen de un vídeo largo. Por ejemplo, podrían querer clientes ver una breve "resumen vídeo" cuando desplace el puntero sobre una miniatura. Ajustando los parámetros de **miniaturas de Azure Media Video** a través de un valor de configuración, puede usar eficaz captura detección y concatenación tecnología del panel de administración forma algorítmica generar un subclip descriptivo.  

La **Miniatura de vídeo multimedia de Azure** en el panel de administración está en vista previa.

Este tema proporciona información detallada sobre la **Miniatura de vídeo de Azure Media** y muestra cómo usarla con Media Services SDK para .NET

##<a name="video-summary-example"></a>Ejemplo de resumen de vídeo 

Estos son algunos ejemplos de lo que puede hacer el procesador de media de miniaturas de vídeo multimedia de Azure:

###<a name="original-video"></a>Vídeo original

[Vídeo original](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

###<a name="video-thumbnail-result"></a>Resultado de miniaturas de vídeo

[Resultado de miniaturas de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##<a name="task-configuration-preset"></a>Configuración de tareas (valor predeterminado)

Al crear una tarea en miniatura de vídeo con **Miniaturas de vídeo multimedia de Azure**, debe especificar un valor de configuración. El ejemplo anterior miniatura se creó con la configuración básica de JSON siguiente:

    {"version":"1.0"}

Actualmente, puede cambiar los parámetros siguientes:

Parámetro|Descripción
---|---
outputAudio|Especifica si el vídeo resultante contiene audio. <br/>Valores permitidos son: True o False. El valor predeterminado es verdadero.
fadeInFadeOut|Especifica si fundido transiciones se usan entre las miniaturas de movimiento independientes.  <br/>Valores permitidos son: True o False.  El valor predeterminado es verdadero.
maxMotionThumbnailDurationInSecs|Entero que especifica cuánto tiempo estará todo el vídeo resultante.  Predeterminado depende de la duración del vídeo original.


La siguiente tabla describe la duración predeterminada, cuando no se utiliza **maxMotionThumbnailInSecs** .

||||
---|---|---|---|---
Duración del vídeo|d < 3 min.|3 min. < d < 15 minutos|15 minutos < d < 30 minutos| 30 minutos < d.
Duración en miniatura|15 sec (escenas 2-3)| 30 segundos (3-5 escenas)|60 seg (escenas 5-10)|90 sec (10 y 15 escenas)


El siguiente JSON establece parámetros disponibles.
    
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }

## <a name="sample-code"></a>Código de ejemplo

El programa siguiente muestra cómo:

1. Crear un activo y cargar un archivo multimedia en el activo.
1. Crea una tarea con una tarea de miniaturas de vídeo basada en un archivo de configuración que contiene el siguiente valor preestablecido de json. 
        
        {               
            "version": "1.0",
            "options": {
                "outputAudio": "true",
                "maxMotionThumbnailDurationInSecs": "30",
                "fadeInFadeOut": "false"
            }
        }

1. Descarga los archivos de salida. 

###<a name="net-code"></a>Código de .NET
     
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;
    
    namespace VideoSummarization
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            static void Main(string[] args)
            {
    
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    

                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");
    
                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }
    
            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);
    
                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");
    
                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";
    
                var processor = GetLatestMediaProcessorByName(MediaProcessorName);
    
                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);
    
                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);
    
                // Specify the input asset.
                task.InputAssets.Add(asset);
    
                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);
    
                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
    
                // Launch the job.
                job.Submit();
    
                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    
                progressJobTask.Wait();
    
                // If job state is Error, the event handling
                // method for job progress should log errors.  Here we check
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                    Console.WriteLine(string.Format("Error: {0}. {1}",
                                                    error.Code,
                                                    error.Message));
                    return null;
                }
    
                return job.OutputMediaAssets[0];
            }
    
            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);
    
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);
    
                return asset;
            }
    
            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }
    
            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();
    
                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));
    
                return processor;
            }
    
            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);
    
                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
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
                        // LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }
    
        }
    }

###<a name="video-thumbnail-output"></a>Salida en miniatura de vídeo

[Salida en miniatura de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Vínculos relacionados

[Información general de análisis de servicios multimedia de Azure](media-services-analytics-overview.md)

[Azure demostraciones de análisis de medios](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)