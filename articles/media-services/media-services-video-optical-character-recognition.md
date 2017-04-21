<properties
    pageTitle="Usar el análisis de Azure Media para convertir el contenido de texto en archivos de vídeo en texto digital | Microsoft Azure"
    description="OCR de análisis de Azure Media (reconocimiento óptico de caracteres) le permite convertir el contenido de texto en archivos de vídeo en editable, que permiten búsqueda texto digital.  Esto le permite automatizar la extracción de metadatos significativo de señal de vídeo de su media."
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
    ms.author="juliako"/>
 
#<a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Usar el análisis de Azure Media para convertir el contenido de texto en archivos de vídeo en texto digital 

##<a name="overview"></a>Información general

Si necesita extraer contenido de texto de los archivos de vídeo y generar un texto digital editable, que permiten búsquedo, debe usar Azure Media análisis OCR (reconocimiento óptico de caracteres). Este procesador multimedia de Azure detecta el contenido de texto en el que los archivos de vídeo y genera archivos de texto para su uso. OCR le permiten automatizar la extracción de metadatos significativo de señal de vídeo de su media.

Cuando se usa junto con un motor de búsqueda, fácilmente su medio de índice por texto y mejorar la detectabilidad de contenido. Esto es muy útil en vídeo muy textual, como una grabación de vídeo o la captura de pantalla de una presentación de diapositivas. Procesador de multimedia de Azure OCR está optimizado para texto digital.

Procesador de medios de **Azure Media OCR** está en vista previa.

Este tema proporciona información detallada acerca de **Azure Media OCR** y muestra cómo usar con Media Services SDK para .NET. Para obtener más información y ejemplos, consulte [este blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

##<a name="ocr-input-files"></a>Archivos de entrada de reconocimiento óptico de caracteres

Archivos de vídeo. Actualmente, se admiten los siguientes formatos: MP4, MOV y WMV.

##<a name="task-configuration"></a>Configuración de la tarea 

Configuración de tareas (valor predeterminado). Al crear una tarea con **OCR multimedia de Azure**, debe especificar una configuración preestablecida con JSON o XML. 

###<a name="attribute-descriptions"></a>Descripciones de atributos

Nombre del atributo|Descripción
---|---
Idioma|(opcional) describe el idioma del texto que desea buscar. Uno de los siguientes: detección automática (predeterminada), árabe, chino, chino tradicional, checo danés, neerlandés, inglés, finés, francés, alemán, griego, húngaro, italiano, japonés, coreano, noruego, polaco, portugués, rumano, ruso, SerbianCyrillic, SerbianLatin, eslovaco, español, sueco, turco.
TextOrientation|(opcional), describe la orientación del texto que desea buscar.  "Izquierda" significa que la parte superior de todas las letras se apunta hacia la izquierda.  Texto predeterminado (como el que se encuentra en un libro) se llama "" orientación.  Uno de los siguientes: detección automática (predeterminada), hacia arriba, hacia la derecha, hacia abajo, hacia la izquierda.
TimeInterval|(opcional), describe la velocidad de muestreo.  El valor predeterminado es cada 1/2 segundo.<br/>Formato JSON: hh. SSS (00:00:00.500 de forma predeterminada)<br/>Formato XML: W3C XSD duración simple (el valor predeterminado es PT0.5)
DetectRegions|(opcional) Matriz de objetos DetectRegion que especifica regiones dentro del marco del vídeo en el que se va a detectar el texto.<br/>Un objeto DetectRegion consta de los siguientes valores de entero de cuatro:<br/>Izquierda: píxeles desde el margen izquierdo<br/>Parte superior: píxeles desde el margen superior<br/>Ancho: ancho de la región en píxeles<br/>Alto: alto de la región en píxeles

####<a name="json-preset-example"></a>Ejemplo JSON preestablecido
        
    {
        'Version':'1.0', 
        'Options': 
        {
        'Language':'English', 
            'TimeInterval':'00:00:01.5',
            'DetectRegions': 
             [
                {'Left':'1','Top':'1','Width':'1','Height':'1'},
                {'Left':'2','Top':'2','Width':'2','Height':'2'}
             ],
            'TextOrientation':'Up'
        }
    }

####<a name="xml-preset-example"></a>Ejemplo preestablecido de XML

    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>1</Left>
                   <Top>1</Top>
                   <Width>1</Width>
                   <Height>1</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>

##<a name="ocr-output-files"></a>Archivos de resultados de OCR

El resultado del procesador multimedia OCR es un archivo JSON.

###<a name="elements-of-the-output-json-file"></a>Elementos del archivo de salida JSON

El resultado de OCR vídeo proporciona datos segmentado de tiempo en los caracteres que se encuentra en el vídeo.  Puede usar atributos como la orientación o el idioma al teléfono de exactamente las palabras que le interesa analizar. 

El resultado contiene los siguientes atributos:

Elemento|Descripción
---|---
Escala temporal|"pasos" por segundo del vídeo
Desplazamiento|tiempo de desplazamiento de las marcas de tiempo. En la versión 1.0 de las API de vídeo, siempre será 0.
Velocidad de cuadros|Cuadros por segundo del vídeo
Ancho|ancho del vídeo en píxeles
alto|alto del vídeo en píxeles
Fragmentos|matriz de fragmentos de tiempo de vídeo en el que se segmentado los metadatos
Inicio|hora de inicio de un fragmento de "marcas"
duración|longitud de un fragmento de "marcas"
intervalo|intervalo de cada evento en el fragmento determinado
eventos|matriz que contiene las regiones
región|objeto que representa detectado palabras o frases
idioma|idioma del texto que se encuentran dentro de una región
orientación|orientación del texto que se encuentran dentro de una región
líneas|matriz de líneas de texto que se encuentran dentro de una región
texto|el texto actual

###<a name="json-output-example"></a>Ejemplo de salida JSON

En el siguiente ejemplo de salida contiene la información general de vídeo y varios fragmentos de vídeo. En cada fragmento de vídeo, que contiene todas las regiones que se ha detectado por el panel de administración de OCR con el idioma y la orientación del texto. El área también contiene todas las líneas de word en esta área con texto de la línea, posición de la línea y cada información de word (contenido de word, posición y confianza) en esta línea. El siguiente es un ejemplo y coloqué algunos comentarios entre líneas.

    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }

## <a name="sample-code"></a>Código de ejemplo

El programa siguiente muestra cómo:

1. Crear un activo y cargar un archivo multimedia en el activo.
1. Crea un trabajo con un archivo de configuración y valores predeterminados de reconocimiento óptico de caracteres.
1. Descarga los archivos JSON de salida. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace OCR
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
        
                    // Run the OCR job.
                    var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                                @"C:\supportFiles\OCR\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
                }
        
                static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My OCR Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My OCR Job");
        
                    // Get a reference to Azure Media OCR.
                    string MediaProcessorName = "Azure Media OCR";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My OCR Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);
        
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
