<properties
    pageTitle="Detectar nominal y emoción con análisis multimedia de Azure | Microsoft Azure"
    description="Este tema muestra cómo detectar caras y emociones con Azure Media análisis."
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
 
#<a name="detect-face-and-emotion-with-azure-media-analytics"></a>Detectar nominal y emoción con análisis multimedia de Azure

##<a name="overview"></a>Información general

Procesador de medios **Detector de Azure Media nominal** (programa mínimo) le permite contar, realizar un seguimiento de los movimientos e incluso evaluar la participación de la audiencia y reacción a través de expresiones rostro. Este servicio contiene dos funciones: 

- **Detección de cara**

    Detección de cara que busca y realiza un seguimiento de caras humanas dentro de un vídeo. Varias caras se pueden detectar y posteriormente registrará mientras se desplazan, con los metadatos de hora y la ubicación devuelto en un archivo JSON. Durante el seguimiento, intentará dar un identificador coherente a la misma cara mientras la persona que está moviendo en pantalla, incluso si se obstruida o deje brevemente el marco.

    >[AZURE.NOTE]Este servicio no realiza reconocimiento rostro. Una persona deja el marco o esté obstruida para mucho tiempo se proporcionará un identificador nuevo cuando vuelvan.

- **Detección de emoción**
    
    Detección de emoción es un componente opcional de la cara detección procesador multimedia que devuelve análisis en varios atributos emoción de las caras detectadas, incluida la satisfacción, tristeza, miedo, enojo y mucho más. 

El **Detector de Azure Media nominal** de panel de administración está en vista previa.

Este tema proporciona información detallada sobre el **Detector de Azure Media nominal** y muestra cómo usar con Media Services SDK para .NET.

##<a name="face-detector-input-files"></a>Archivos de entrada del Detector de cara

Archivos de vídeo. Actualmente, se admiten los siguientes formatos: MP4, MOV y WMV.

##<a name="face-detector-output-files"></a>Boca Detector archivos de salida

La API de detección y seguimiento de cara proporciona detección de ubicación de cara de alta precisión y de seguimiento que puede detectar un máximo de 64 caras humanas en un vídeo. Caras frontal proporcionan los mejores resultados, mientras caras laterales y pequeñas caras (menor o igual a 24 x 24 píxeles) no sea tan precisos.

Se devuelven las caras detectadas y marcas con las coordenadas (izquierda, superior, ancho y alto) que indica la ubicación de caras de la imagen en píxeles, así como un número de identificación de cara que indica que el seguimiento de esa persona. Números de identificación de cara son susceptible a restablecer en circunstancias cuando se pierde o superpuesta en el marco, la cara frontal de algunas personas obtener asignados varios identificadores.

###<a id="output_elements"></a>Elementos del archivo de salida JSON

Para la detección de cara y seguimiento de la operación, el resultado de salida contiene los metadatos de las caras desde el archivo especificado en formato JSON.

La detección de cara y seguimiento JSON incluye los siguientes atributos:

Elemento|Descripción
---|---
Versión|Esta opción hace referencia a la versión de la API de vídeo.
Escala temporal|"Pasos" por segundo del vídeo.
Desplazamiento|Este es el desplazamiento de tiempo para las marcas de tiempo. En la versión 1.0 de las API de vídeo, siempre será 0. En un futuro escenarios que se admiten, puede cambiar este valor.
Velocidad de cuadros|Tramas por segundo del vídeo.
Fragmentos|Los metadatos se segmentado hacia arriba en diferentes segmentos denominados fragmentos. Cada fragmento contiene un inicio, duración, número de intervalo y eventos.
Inicio|Hora de inicio del primer evento en 'marcas'.
Duración|La longitud del fragmento, en "pasos".
Intervalo|El intervalo de cada entrada de evento en el fragmento en "pasos".
Eventos|Cada evento contiene las caras detectar y seguir dentro de esa duración. Es una matriz de la matriz de eventos. La matriz externa representa un intervalo de tiempo. La matriz interna consta de 0 o más de los eventos que ocurrieron en ese momento. Un corchete vacía a [] significa que no se detectan ninguna caras.
ID.| El identificador de la imagen que se realiza un seguimiento. Sin darse cuenta puede cambiar este número si una cara se convierte en no detectada. Una persona determinada debe tener el mismo identificador en toda la general del vídeo, pero esto no se puede garantizar debido a las limitaciones en el algoritmo de detección (oclusión, etcetera).
X, Y|La esquina superior izquierda X coordenadas e Y de la cara del rectángulo de selección en una escala de normalizado de 0,0 a 1,0. <br/>-X e Y las coordenadas son relativas a horizontal siempre, por lo que si tiene un retrato vídeo (o al revés, en el caso de iOS), deberá Transponer las coordenadas en consecuencia.
Ancho, alto|El ancho y el alto de la cara del rectángulo de selección en una escala de normalizado de 0,0 a 1,0.
facesDetected|Esto se encuentra al final de los resultados JSON y resume el número de caras que el algoritmo detectado durante el vídeo. Dado que los identificadores se pueden restablecer sin darse cuenta si una cara se convierte en no detectada (por ejemplo, cara queda fuera de la pantalla, parece ausente), este número no es posible igual siempre el número real de caras en el vídeo.

Cara Detector utiliza técnicas de fragmentación (donde los metadatos se pueden dividir en fragmentos de tiempo y puede descargar solo lo que necesita) y segmentación (donde los eventos están divididos en caso de que les demasiado grandes). Algunos cálculos sencillos pueden ayudarle a transformar los datos. Por ejemplo, si un evento iniciado en 6300 (pasos), con una escala de tiempo de 2997 (marcas/sec) y velocidad de 29,97 luego (marcos/sec):

- Inicio/escala temporal = segundos 2.1
- Segundos x (velocidad escala temporal) = 63 marcos

A continuación es un ejemplo sencillo de extraer la JSON en una por formato de marco de detección de cara y seguimiento:
    
    var faceDetectionResultJsonString = operationResult.ProcessingResult;
    var faceDetecionTracking = 
         JsonConvert.DeserializeObject<FaceDetectionResult>(faceDetectionResultJsonString, settings);


##<a name="face-detection-input-and-output-example"></a>Boca detección entrada y salida de ejemplo

###<a name="input-video"></a>Entrada de vídeo

[Entrada de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configuración de tareas (valor predeterminado)

Al crear una tarea con **Detector de cara multimedia de Azure**, debe especificar un valor de configuración. El valor de configuración siguiente es únicamente para la detección de cara.

    {"version":"1.0"}

###<a name="json-output"></a>Resultado JSON

El siguiente ejemplo de resultado JSON se trunca.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

##<a name="emotion-detection-input-and-output-example"></a>Detección de emoción de entrada y salida de ejemplo


###<a name="input-video"></a>Entrada de vídeo

[Entrada de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configuración de tareas (valor predeterminado)

Al crear una tarea con **Detector de cara multimedia de Azure**, debe especificar un valor de configuración. Especifica el siguiente valor de configuración para crear JSON basado en la detección de emoción.
    
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


####<a name="attribute-descriptions"></a>Descripciones de atributos

Nombre del atributo|Descripción
---|---
Modo|Caras: Solo orientará detección <br/>AggregateEmotion: Los valores de retorno emoción promedio para todas las caras en marco.
AggregateEmotionWindowMs|Use si selecciona el modo de AggregateEmotion. Especifica la longitud del vídeo que se usa para producir cada resultado agregado en milisegundos.
AggregateEmotionIntervalMs|Use si selecciona el modo de AggregateEmotion. Especifica con qué frecuencia para producir resultados agregados.

####<a name="aggregate-defaults"></a>Valores predeterminados de agregado

A continuación se recomiendan los valores de la configuración de la ventana y el intervalo de agregado. AggregateEmotionWindowMs debe tener más de AggregateEmotionIntervalMs.

   |Valores predeterminados (s)|Max(s)|Min.
---|---|---|---
AggregateEmotionWindowMs|0,5|2|0,25
AggregateEmotionIntervalMs|0,5|1|0,25

###<a name="json-output"></a>Resultado JSON

JSON de salida para emoción agregado (truncado):
 
    
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


##<a name="limitations"></a>Limitaciones

- Los formatos de vídeo de entrada compatibles incluyen MP4, MOV y WMV.
- El intervalo de tamaño nominal detectable es 24 x 24 a 2048 x 2048 píxeles. No se detectarán las caras fuera de este rango.
- Para cada vídeo, el número máximo de caras devuelto es 64.
- No puede detectar algunos caras debido a los retos técnicos; Por ejemplo, muy grandes ángulos de cara (cabeza pose) y oclusión grande. Caras frontal y cerca frontal tienen los mejores resultados.


## <a name="sample-code"></a>Código de ejemplo

El programa siguiente muestra cómo:

1. Crear un activo y cargar un archivo multimedia en el activo.
1. Crea una tarea con una tarea de detección de cara basada en un archivo de configuración que contiene el siguiente valor preestablecido de json. 
                    
        {
            "version": "1.0"
        }

1. Descarga los archivos JSON de salida. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceDetection
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
        
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
        
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
        
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
        
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

##<a name="related-links"></a>Vínculos relacionados

[Información general de análisis de servicios multimedia de Azure](media-services-analytics-overview.md)

[Azure demostraciones de análisis de medios](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)