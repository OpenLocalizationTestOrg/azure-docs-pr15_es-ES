<properties
    pageTitle="Detectar movimientos con análisis multimedia de Azure | Microsoft Azure"
    description="Procesador de medios Detector de movimiento de Azure Media (programa mínimo) le permite identificar eficazmente secciones de interés dentro de un vídeo en caso contrario, largo y sin incidentes."
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
    ms.date="10/10/2016"  
    ms.author="milanga;juliako;"/>
 
# <a name="detect-motions-with-azure-media-analytics"></a>Detectar movimientos con análisis multimedia de Azure

##<a name="overview"></a>Información general

Procesador de medios **Detector de movimiento de Azure Media** (programa mínimo) le permite identificar eficazmente secciones de interés dentro de un vídeo en caso contrario, largo y sin incidentes. Detección de movimiento puede usarse en grabación de cámara estática identificar las secciones del vídeo donde se produce la animación. Genera un archivo JSON que contiene un metadatos con las marcas de tiempo y el delimitador región donde se produjo el evento.

Esta tecnología y destinada a fuentes de vídeo de seguridad, es posible clasificar movimiento en eventos relevantes y falsos como sombras y los cambios de iluminación. Esto le permite generar las alertas de seguridad de cámara fuentes sin ser spam con infinitas eventos relevante, mientras se pueden extraer momentos de interés de vídeos de supervisión muy largo.

El **Detector de movimiento de elementos multimedia de Azure** en el panel de administración está en vista previa.

Este tema proporciona información detallada sobre el **Detector de movimiento de Azure Media** y muestra cómo usarla con Media Services SDK para .NET


##<a name="motion-detector-input-files"></a>Archivos de entrada de Detector de movimiento

Archivos de vídeo. Actualmente, se admiten los siguientes formatos: MP4, MOV y WMV.

##<a name="task-configuration-preset"></a>Configuración de tareas (valor predeterminado)

Al crear una tarea con **Detector de movimiento de elementos multimedia de Azure**, debe especificar un valor de configuración. 

###<a name="parameters"></a>Parámetros

Puede usar los siguientes parámetros:

Nombre|Opciones|Descripción|Predeterminado
---|---|---|---
sensitivityLevel|Cadena: 'baja', 'Media', 'alta'|Establece el nivel de confidencialidad en qué movimientos es registrado. Ajustar esta opción para ajustar la cantidad de falsos positivos.|'Media'
frameSamplingValue|Entero positivo|Establece la frecuencia en que se ejecuta el algoritmo. 1 es igual a cada marco, 2 significa cada marco 2 º y así sucesivamente.|1
detectLightChange|Booleana: 'true', 'false'|Establece si se muestran los cambios de luz en los resultados|'False'
mergeTimeThreshold|Hora de x: Hh<br/>Ejemplo: 00:00:03|Especifica la ventana de tiempo entre eventos de movimiento donde 2 eventos se combinarán y referir como 1.|00:00:00
detectionZones|Matriz de las zonas de detección:<br/>-Zona de detección es una matriz de 3 o más puntos<br/>-Punto es una x e y coordenadas de 0 a 1.|Describe la lista de las zonas de detección de forma de polígono para usarse.<br/>Los resultados se notificará con las zonas como un ID, con el primer 'id': 0|Zona único que cubra todo el marco.

###<a name="json-example"></a>Ejemplo JSON

    
    {
      'version': '1.0',
      'options': {
        'sensitivityLevel': 'medium',
        'frameSamplingValue': 1,
        'detectLightChange': 'False',
        "mergeTimeThreshold":
        '00:00:02',
        'detectionZones': [
          [
            {'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}
           ],
          [
            {'x': 0.3, 'y': 0.3},
            {'x': 0.55, 'y': 0.3},
            {'x': 0.8, 'y': 0.3},
            {'x': 0.8, 'y': 0.55},
            {'x': 0.8, 'y': 0.8},
            {'x': 0.55, 'y': 0.8},
            {'x': 0.3, 'y': 0.8},
            {'x': 0.3, 'y': 0.55}
          ]
        ]
      }
    }


##<a name="motion-detector-output-files"></a>Archivos de salida del Detector de movimiento

Un trabajo de detección de movimiento devolverá un archivo JSON en los activos de salida que describe las alertas de movimiento y sus categorías, dentro del vídeo. El archivo contiene información sobre la hora y la duración de movimiento que se encuentran en el vídeo.

La API de Detector de movimiento proporciona indicadores cuando hay objetos en movimiento en un fondo fijo vídeo (por ejemplo, un control vídeo). El Detector de movimiento está capacitado para reducir falsas alarmas, como iluminación y los cambios de la sombra. Limitaciones actuales de los algoritmos incluyen vídeos de visión noche, objetos semitransparentes y objetos pequeños.

###<a id="output_elements"></a>Elementos del archivo de salida JSON

>[AZURE.NOTE]En la versión más reciente, el formato de salida JSON ha cambiado y puede representar un cambio importante para algunos clientes.

La siguiente tabla describe los elementos del archivo de salida JSON.

Elemento|Descripción
---|---
Versión|Esta opción hace referencia a la versión de la API de vídeo. La versión actual es 2.
Escala temporal|"Pasos" por segundo del vídeo.
Desplazamiento|El desplazamiento de tiempo para las marcas de tiempo en pasos de"". En la versión 1.0 de las API de vídeo, siempre será 0. En un futuro escenarios que se admiten, puede cambiar este valor.
Velocidad de cuadros|Tramas por segundo del vídeo.
Ancho, alto|Se refiere al ancho y alto del vídeo en píxeles.
Inicio|La marca de hora de inicio en "pasos".
Duración|La longitud del evento, en "pasos".
Intervalo|El intervalo de cada entrada en el evento, en "pasos".
Eventos|Cada fragmento de evento contiene el movimiento detectado dentro de esa duración.
Tipo|En la versión actual, siempre es '2' para movimiento genérico. Este vídeo API la flexibilidad para clasificar de etiqueta ofrece movimiento en futuras versiones.
RegionID|Como se explica anteriormente, siempre será 0 en esta versión. Esta etiqueta proporciona API de vídeo la flexibilidad para buscar la animación en distintas regiones en futuras versiones.
Regiones|Hace referencia a la sección en el vídeo que le interese movimiento. <br/><br/>-"id" representa el área de región: en esta versión sólo hay una, ID 0. <br/>-"tipo" representa la forma de la región que le interesa para el movimiento. Actualmente, "rectángulo" y "polígono" son compatibles.<br/> Si especifica "rectángulo", la región tiene dimensiones en X, Y, ancho y alto. Las coordenadas X e Y representan las coordenadas XY superior izquierda de la región en una escala normalizada de 0,0 a 1,0. El ancho y alto representan el tamaño de la región en una escala normalizada de 0,0 a 1,0. En la versión actual, X, Y, ancho y alto siempre fijo en 0, 0 y 1, 1. <br/>Si especifica "polígono", la región tiene dimensiones en puntos. <br/>
Fragmentos|Los metadatos se segmentado hacia arriba en diferentes segmentos denominados fragmentos. Cada fragmento contiene un inicio, duración, número de intervalo y eventos. Un fragmento con ningún eventos significa que se ha detectado ninguna animación durante la hora de inicio y la duración.
Corchetes]|Cada corchete representa un intervalo en el evento. Vaciar corchetes para ese intervalo significa que se ha detectado ninguna animación.
ubicaciones|Esta nueva entrada en eventos muestra la ubicación donde se produjo el movimiento. Esto es más específico que las zonas de detección.

El siguiente es un ejemplo de salida JSON

    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
    
    …
##<a name="limitations"></a>Limitaciones

- Los formatos de vídeo de entrada compatibles incluyen MP4, MOV y WMV.
- Detección de movimiento está optimizada para ver vídeos de fondo fija. El algoritmo se centra en reducir falsas alarmas, como los cambios de iluminación y sombras.
- No se puede detectar algunos movimiento debido a los retos técnicos; Por ejemplo, vídeos de visión noche, objetos semitransparentes y objetos pequeños.


## <a name="sample-code"></a>Código de ejemplo

El programa siguiente muestra cómo:

1. Crear un activo y cargar un archivo multimedia en el activo.
1. Crea una tarea con una tarea de detección de movimiento de vídeo basada en un archivo de configuración que contiene el siguiente valor preestablecido de json. 
                    
        {
          'Version': '1.0',
          'Options': {
            'SensitivityLevel': 'medium',
            'FrameSamplingValue': 1,
            'DetectLightChange': 'False',
            "MergeTimeThreshold":
            '00:00:02',
            'DetectionZones': [
              [
                {'x': 0, 'y': 0},
                {'x': 0.5, 'y': 0},
                {'x': 0, 'y': 1}
               ],
              [
                {'x': 0.3, 'y': 0.3},
                {'x': 0.55, 'y': 0.3},
                {'x': 0.8, 'y': 0.3},
                {'x': 0.8, 'y': 0.55},
                {'x': 0.8, 'y': 0.8},
                {'x': 0.55, 'y': 0.8},
                {'x': 0.3, 'y': 0.8},
                {'x': 0.3, 'y': 0.55}
              ]
            ]
          }
        }

1. Descarga los archivos JSON de salida. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace VideoMotionDetection
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
        
                    // Run the VideoMotionDetection job.
                    var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\VideoMotionDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
                }
        
                static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Video Motion Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Video Motion Detection Job");
        
                    // Get a reference to Azure Media Motion Detector.
                    string MediaProcessorName = "Azure Media Motion Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
        
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
[Blog de Detector de movimiento de servicios multimedia de Azure](https://azure.microsoft.com/blog/motion-detector-update/)

[Información general de análisis de servicios multimedia de Azure](media-services-analytics-overview.md)

[Azure demostraciones de análisis de medios](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
