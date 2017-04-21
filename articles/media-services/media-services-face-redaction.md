<properties
    pageTitle="Boca redacción con análisis multimedia de Azure | Microsoft Azure"
    description="Este tema muestra cómo censurar caras con análisis multimedia de Azure."
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
    ms.date="09/12/2016"   
    ms.author="juliako;"/>
 
#<a name="face-redaction-with-azure-media-analytics"></a>Redacción de cara con análisis multimedia de Azure

##<a name="overview"></a>Información general

**Azure Media Redactor** es un procesador de multimedia de [Azure Media análisis](media-services-analytics-overview.md) (programa mínimo) que ofrece redacción scalable nominal en la nube. Redacción de cara le permite modificar el vídeo con el fin de desenfoque caras de los usuarios seleccionados. Desea usar el servicio de redacción nominal en escenarios de seguridad y medios de noticias públicos. Unos minutos de grabación que contiene varias caras pueden tardar horas en censurar manualmente, pero con este servicio, el proceso de redacción de cara requerirá unos sencillos pasos. Para obtener más información, consulte [este](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Este tema proporciona información detallada acerca de **Azure Media Redactor** y muestra cómo usar con Media Services SDK para .NET.

El **Redactor multimedia de Azure** en el panel de administración está en vista previa.

## <a name="face-redaction-modes"></a>Modos de redacción nominal

Redacción rostro funciona detectar caras en cada fotograma de vídeo y el seguimiento del objeto de cara ambos hacia adelante y hacia atrás en el tiempo, para que la misma persona puede ser borrosa desde otros ángulos también. El proceso automatizado de redacción es muy complejo y no siempre producir 100% de salida deseado, por este motivo multimedia Analytics proporciona con un par de maneras para modificar el resultado final.

Además de un modo completamente automático, hay un flujo de trabajo de dos pasos que permite la selección o de-selection de caras encontrados a través de una lista de identificadores. Además, para que arbitrario por los ajustes de marco del panel de administración utiliza un archivo de metadatos en formato JSON. Este flujo de trabajo se divide en los modos de **analizar** y **Redact** . Puede combinar los dos modos en un único paso que se ejecute ambas tareas en un trabajo; este modo se denomina **combinada**.

###<a name="combined-mode"></a>Modo combinado

Este modo generará automáticamente un mp4 redactado sin manuales de entrada.

Fase|Nombre de archivo|Notas
---|---|---
Entrada de activos|foo.bar|Vídeo en formato WMV, MOV o MP4
Configuración de entrada|Configuración del trabajo preestablecidos|{'versión':'1.0 ', 'opciones': {'modo': 'combinado'}}
Activo de salida|foo_redacted.mp4|Vídeo con desenfoque aplicado

####<a name="input-example"></a>Ejemplo de entrada:

[vea este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

####<a name="output-example"></a>Ejemplo de salida:

[vea este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

###<a name="analyze-mode"></a>Modo de análisis

El paso **analizar** del flujo de trabajo de dos pasos le lleva a una entrada de vídeo y genera un archivo JSON de ubicaciones nominal e imágenes jpg de cada cara detectado.

Fase|Nombre de archivo|Notas
---|---|----
Entrada de activos|foo.bar|Vídeo en formato WMV, MPV o MP4
Configuración de entrada|Configuración del trabajo preestablecidos|{'versión':'1.0 ', 'opciones': {'modo': 'analizar'}}
Activo de salida|foo_annotations.JSON|Datos de anotación de ubicaciones de cara en formato JSON. Esto se puede modificar el usuario para modificar el desenfoque cuadros delimitadores. Vea el ejemplo siguiente.
Activo de salida|foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg]|Un jpg recortado de cada detectado cara, donde el número indica la labelId de la cara

####<a name="output-example"></a>Ejemplo de salida:

    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

… trunca


###<a name="redact-mode"></a>Censurar modo

El segundo paso del flujo de trabajo tiene un mayor número de entradas que deben combinarse en un solo activo.

Esto incluye una lista de identificadores de desenfoque, el vídeo original y las anotaciones JSON. Este modo usa las anotaciones para aplicar desenfoque en la entrada de vídeo.

El resultado de la fase de analizar no incluye el vídeo original. El vídeo debe ser cargado en la entrada activo de la tarea de modo Redact y seleccionado como archivo principal.

Fase|Nombre de archivo|Notas
---|---|---
Entrada de activos|foo.bar|Vídeo en formato WMV, MPV o MP4. Mismo vídeo como en el paso 1.
Entrada de activos|foo_annotations.JSON|archivo de metadatos de anotaciones de la fase uno, con las modificaciones opcionales.
Entrada de activos|foo_IDList.txt (opcional)|Nueva línea opcional lista separada de cara identificadores de censurar. Si deja en blanco, esto desenfoca todas las caras.
Configuración de entrada|Configuración del trabajo preestablecidos|{'versión':'1.0 ', 'opciones': {'modo': 'censurar'}}
Activo de salida|foo_redacted.mp4|Vídeo con desenfoque aplicado en función de anotaciones

####<a name="example-output"></a>Ejemplo de salida

Este es el resultado de una Lista_id con un identificador seleccionado.

[vea este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

##<a name="attribute-descriptions"></a>Descripciones de atributos

El panel de administración de redacción proporciona detección de ubicación de cara de alta precisión y de seguimiento puede detectar caras humanos hasta 64 en un marco de vídeo. Caras frontal proporcionan los mejores resultados, mientras caras laterales y pequeñas caras (menor o igual a 24 x 24 píxeles) son difíciles.

Las caras detectadas y marcas se devuelven coordenadas que indica la ubicación de caras, así como una cara número de identificación que indica que el seguimiento de esa persona. Números de identificación de cara son susceptible a restablecer en circunstancias cuando se pierde o superpuesta en el marco, la cara frontal de algunas personas obtener asignados varios identificadores.

Para obtener más detalles para los atributos, vea el tema de [cara detectar y emoción con Azure Media análisis](media-services-face-and-emotion-detection.md) .

## <a name="sample-code"></a>Código de ejemplo

El programa siguiente muestra cómo:

1. Crear un activo y cargar un archivo multimedia en el activo.
1. Crear una tarea con una tarea de redacción de cara basada en un archivo de configuración que contiene el siguiente valor preestablecido de json. 
                    
        {'version':'1.0', 'options': {'mode':'combined'}}

1. Descargar los archivos JSON de salida. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceRedaction
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
        
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
        
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
        
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
        
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


##<a name="next-step"></a>Siguiente paso

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Vínculos relacionados

[Información general de análisis de servicios multimedia de Azure](media-services-analytics-overview.md)

[Azure demostraciones de análisis de medios](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
