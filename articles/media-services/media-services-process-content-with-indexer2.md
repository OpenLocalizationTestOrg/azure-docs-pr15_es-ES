<properties
    pageTitle="Indización de archivos multimedia con vista previa de indizador 2 multimedia de Azure | Microsoft Azure"
    description="Azure indizador de Media permite que el contenido de los archivos multimedia que permiten búsquedas y generar una transcripción de texto para palabras clave y subtítulos cerrados. Este tema muestra cómo usar la vista previa de medios indizador 2."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016" 
    ms.author="adsolank;juliako;"/>


# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indización de archivos multimedia con vista previa de indizador 2 multimedia de Azure

##<a name="overview"></a>Información general

Procesador de media de **Vista previa de Azure Media indizador 2** (programa mínimo) le permite hacer posible la búsqueda de contenido y archivos multimedia, así como para generar pistas de subtítulos cerradas. En comparación con la versión anterior del [Indizador de Azure Media](media-services-index-content.md), **Vista previa de Azure Media indizador 2** realiza la indización más rápido y ofrece soporte más amplio de idioma. Idiomas admitidos incluyen inglés, español, francés, alemán, italiano, chino, portugués y árabe.

**Vista previa de Azure Media indizador 2** panel de administración está en vista previa.

Este tema muestra cómo crear trabajos de indexación con **Vista previa de Azure Media indizador 2**.

>[AZURE.NOTE]Aplica teniendo en cuenta lo siguiente:
>
>Indizador 2 no es compatible con Azure China y administración pública de Azure.
>
>La vista previa se limita a unos 10 minutos de procesamiento, pero es gratuita a todos los clientes.
>
>Cuando la indización de contenido, asegúrese de usar archivos multimedia que tienen muy clara voz (sin música de fondo, ruido, efectos o silbido micrófono). Algunos ejemplos de contenido adecuado son: grabado reuniones, conferencias o presentaciones. El siguiente contenido podría no ser adecuado para la indización: películas, programas de TV, cualquier cosa con audio mixto y efectos de sonido graban mal contenido con el ruido de fondo (hiss).


Este tema proporciona información detallada acerca de la **Vista previa de Azure Media indizador 2** y muestra cómo usarla con Media Services SDK para .NET

##<a name="input-and-output-files"></a>Archivos de entrada y salidos

###<a name="input-files"></a>Archivos de entrada

Archivos de audio o vídeo

###<a name="output-files"></a>Archivos de salida

Una tarea de indización puede generar archivos de subtítulos en los siguientes formatos:  

- **Sami**
- **TTML**
- **WebVTT**

Cerrado título (CC) archivos en estos formatos pueden utilizarse para hacer que los archivos de audio y vídeo accesibles para personas con discapacidades auditivas.

##<a name="task-configuration-preset"></a>Configuración de tareas (valor predeterminado)

Al crear una tarea de indización con **Azure Media indizador 2 Preview**, debe especificar un valor de configuración.

El siguiente JSON establece parámetros disponibles.

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

##<a name="supported-languages"></a>Idiomas admitidos  

Azure Media indizador 2 Preview admite texto a voz para los siguientes idiomas (al especificar el nombre del idioma en la configuración de la tarea, usar el código de 4 caracteres entre corchetes como se muestra a continuación):

- Inglés [enús]
- Español [EsEs]
- Chino [ZhCn]
- Francés [FrFr]
- Alemán [DeDe]
- Italiano [ItIt]
- Portugués [PtBr]
- Árabe (egipcio) [ArEg]


## <a name="sample-code"></a>Código de ejemplo

El programa siguiente muestra cómo:

1. Crear un activo y cargar un archivo multimedia en el activo.
1. Crea una tarea con una tarea basada en un archivo de configuración que contiene el siguiente valor preestablecido de json.
            
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }

1. Descarga los archivos de salida. 
    
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace IndexContent
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
        
                    // Run indexing job.
                    var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                                @"C:\supportFiles\Indexer\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
                }
        
                static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Indexing Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Indexing Job");
        
                    // Get a reference to Azure Media Indexer 2 Preview.
                    string MediaProcessorName = "Azure Media Indexer 2 Preview";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Indexing Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset to be indexed.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
        
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


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Vínculos relacionados

[Información general de análisis de servicios multimedia de Azure](media-services-analytics-overview.md)

[Azure demostraciones de análisis de medios](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)