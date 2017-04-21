<properties 
    pageTitle="Avanzado de codificación con Media Encoder estándar" 
    description="Este tema muestra cómo llevar a cabo la codificación avanzada personalizando preestablecidos Media Encoder estándar de tareas. El tema muestra cómo usar .NET SDK de Media Services para crear una tarea y el trabajo de codificación. También se muestra cómo proporcionar valores personalizados para el trabajo de codificación." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/25/2016"   
    ms.author="juliako"/>


#<a name="advanced-encoding-with-media-encoder-standard"></a>Avanzado de codificación con Media Encoder estándar

##<a name="overview"></a>Información general

Este tema muestra cómo realizar tareas de codificación avanzadas con Media Encoder estándar. El tema muestra [cómo usar .NET para crear una tarea de codificación y un trabajo que se ejecute esta tarea](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet). También se muestra cómo proporcionar valores personalizados a la tarea de codificación. Para obtener una descripción de los elementos de los valores preestablecidos, vea [este documento](https://msdn.microsoft.com/library/mt269962.aspx). 

Se muestran los valores preestablecidos personalizados que realizan las siguientes tareas de codificación:

- [Generar miniaturas](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [Recortar un vídeo (recorte)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [Crear una superposición](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [Insertar un control de audio silencioso cuando la entrada no tiene audio](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [Deshabilitar automática de entrelazado](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
- [Valores preestablecidos solo de audio](media-services-custom-mes-presets-with-dotnet.md#audio_only)
- [Concatenar dos o más archivos de vídeo](media-services-custom-mes-presets-with-dotnet.md#concatenate)
- [Vídeos de recorte con Media Encoder estándar](media-services-custom-mes-presets-with-dotnet.md#crop)
- [Insertar un control de vídeo de entrada cuando no tiene vídeo](media-services-custom-mes-presets-with-dotnet.md#no_video)
- [Girar un vídeo](media-services-custom-mes-presets-with-dotnet.md#rotate_video)

##<a id="encoding_with_dotnet"></a>Codificación de Media Services .NET SDK

En el ejemplo siguiente se utiliza Media Services .NET SDK para realizar las siguientes tareas:

- Crear un trabajo de codificación.
- Obtenga una referencia al codificador multimedia codificador estándar.
- Cargar el formato XML personalizado o JSON preestablecidos. Puede guardar el archivo XML o JSON (por ejemplo, [XML](media-services-custom-mes-presets-with-dotnet.md#xml) o [JSON](media-services-custom-mes-presets-with-dotnet.md#json) en un archivo y use el código siguiente para cargar el archivo.

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
- Agregar una tarea de codificación para el trabajo. 
- Especifique la entrada activo para codificar.
- Crear un activo de salida que contiene el activo codificado.
- Agregar un controlador de eventos para comprobar el progreso de tarea.
- Enviar el trabajo.
    
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace CustomizeMESPresests
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
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();
        
                    // Encode and generate the output using custom presets.
                    EncodeToAdaptiveBitrateMP4Set(asset);
        
                    Console.ReadLine();
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
                
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");
                
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
                
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
                
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
                
                    return job.OutputMediaAssets[0];
                }
        
                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
        
                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes 
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;
        
                        af.Update();
                    }
        
                    return asset;
                }
        
        
                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);
        
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);
        
                    // Add an output asset to contain the results of the job. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        

                private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                            break;
                        default:
                            break;
                    }
                }
        
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
            }
        }


##<a name="support-for-relative-sizes"></a>Compatibilidad con tamaños relativos

Al generar las miniaturas de la misma, no necesitará especificar siempre salida ancho y alto en píxeles. Puede especificarlos en porcentajes, en el intervalo [% de 1,..., 100%].

###<a name="json-preset"></a>Valores predeterminados JSON 
    
    "Width": "100%",
    "Height": "100%"

###<a name="xml-preset"></a>Valores predeterminados XML
    
    <Width>100%</Width>
    <Height>100%</Height>
    
##<a id="thumbnails"></a>Generar miniaturas

Esta sección muestra cómo personalizar un valor preestablecido que genera miniaturas. La programación descrita a continuación contiene información sobre cómo desea codificar el archivo, así como la información necesaria para generar miniaturas. Puede realizar cualquiera de los MES preestablecidos documentado [aquí](https://msdn.microsoft.com/library/mt269960.aspx) y agregar el código que genera miniaturas.  

>[AZURE.NOTE]La configuración de **SceneChangeDetection** en solo las siguientes opciones puede preestablecida se establece en true si va a codificar a una velocidad de bits única vídeo. Si se codificación a un vídeo de velocidad de bits múltiple y **SceneChangeDetection** se establece en true, el codificador devuelve un error.  


Para obtener información acerca del esquema, vea [este](https://msdn.microsoft.com/library/mt269962.aspx) tema.

Asegúrese de revisar la sección [Consideraciones](media-services-custom-mes-presets-with-dotnet.md#considerations) .

###<a id="json"></a>Valores predeterminados JSON


    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
       
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a id="xml"></a>Valores predeterminados XML


    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

###<a name="considerations"></a>Consideraciones

Aplica teniendo en cuenta lo siguiente:

- El uso de las marcas de tiempo explícitas de inicio o paso o rango se supone que el origen de entrada larga al menos de 1 minuto.
- Jpg o Png/BmpImage elementos tienen inicio, paso y atributos de cadena de rango, estos se pueden interpretar como:

    - Número de marco si son enteros no negativos, por ejemplo "inicio": "120"
    - Relativa a la duración de origen si se expresa como sufijo de %, por ejemplo "inicio": "15%", o
    - Marca de hora si expresado como hh... formato, por ejemplo "inicio": "00: 01:00"

    Puede mezclar y coincidir con notaciones que vuelva.
    
    Además, inicio también es compatible con una Macro especial: {mejor}, que intenta determinar el primer marco "interesante" de la nota de contenido: (paso y rango se omiten cuando inicio es {mejor})
    
    - Valores predeterminados: Iniciar: {mejor}
- Formato de salida debe proporcionarse explícitamente para cada formato de imagen: Jpg, Png o BmpFormat. Si está presente, MES coincide con la JpgVideo a JpgFormat y así sucesivamente. OutputFormat presenta una nueva Macro específica de códec de imagen: "índice", que debe estar presente (una vez y solo una vez) para formatos de salida de la imagen.

##<a id="trim_video"></a>Recortar un vídeo (recorte)

En esta sección se habla acerca de cómo modificar los valores preestablecidos de codificador para recortar o recortar el vídeo de entrada cuando la entrada es un archivo mezzanine llamados o a petición. El codificador también puede usarse para recortar o recortar un activo, que es capturado o archivado desde una secuencia directo: los detalles de este están disponibles en [este blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Para recortar los vídeos, puede realizar cualquiera de los MES preestablecidos documentado [aquí](https://msdn.microsoft.com/library/mt269960.aspx) y modificar el elemento de **orígenes** (tal como se muestra a continuación). El valor de hora de inicio debe coincidir con las marcas de tiempo absolutas de la entrada de vídeo. Por ejemplo, si el primer marco del vídeo entrado tiene una marca de tiempo de 12:00:10.000, a continuación, hora de inicio debe ser al menos 12:00:10.000 y mayor. En el ejemplo siguiente, se supone que el vídeo de entrada tiene una marca de hora inicial de cero. **Orígenes** deben colocarse al principio de la programación. 
 
###<a id="json"></a>Valores predeterminados JSON
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    } 

###<a name="xml-preset"></a>Valores predeterminados XML
    
Para recortar los vídeos, puede realizar cualquiera de los MES preestablecidos documentado [aquí](https://msdn.microsoft.com/library/mt269960.aspx) y modificar el elemento de **orígenes** (tal como se muestra a continuación).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

##<a id="overlay"></a>Crear una superposición

Media Encoder estándar permite superposición de una imagen en un vídeo existente. Actualmente, se admiten los siguientes formatos: png, jpg, gif y bmp. El valor descrito a continuación es un ejemplo básico de una superposición de vídeo.

Además de definir un archivo preestablecido, también debe informar Media Services qué archivo activo es la imagen de superposición y cuál es el origen de vídeo en el que desea superponer la imagen. El archivo de vídeo tiene que ser el archivo **principal** . 

El ejemplo .NET anterior define dos funciones: **UploadMediaFilesFromFolder** y **EncodeWithOverlay**. La función UploadMediaFilesFromFolder cargas de archivos de una carpeta (por ejemplo, BigBuckBunny.mp4 y Image001.png) y establece el archivo mp4 ser el archivo principal en activo. La función **EncodeWithOverlay** usa el archivo preestablecido personalizado que se pasó a él (por ejemplo, la programación que sigue) para crear la tarea de codificación. 

>[AZURE.NOTE]Limitaciones actuales:
>
>No se admite el valor de opacidad de superposición.
>
>El archivo de vídeo de origen y el archivo de imagen de superposición tienen que estar en el mismo activo y el archivo de vídeo debe configurarse como archivo principal de este activo.

###<a name="json-preset"></a>Valores predeterminados JSON
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a name="xml-preset"></a>Valores predeterminados XML
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


##<a id="silent_audio"></a>Insertar un control de audio silencioso cuando la entrada no tiene audio

De forma predeterminada, si envía una entrada al codificador que contiene solo vídeo y sin audio activo de salida contiene los archivos que contienen datos de vídeo. Es podrán que algunos reproductores no pueda manejar estas secuencias de salida. Puede usar esta opción para forzar el codificador para agregar una pista de audio silenciosa a los resultados en ese caso.

Para forzar el codificador para generar un activo, que contiene una pista de audio silenciosa cuando entrada no tiene audio, especifique el valor de "InsertSilenceIfNoAudio".

Puede realizar cualquiera de los MES preestablecidos documentado [aquí](https://msdn.microsoft.com/library/mt269960.aspx)y realizar la modificación de los siguiente:

###<a name="json-preset"></a>Valores predeterminados JSON

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###<a name="xml-preset"></a>Valores predeterminados XML

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a id="deinterlacing"></a>Deshabilitar automática de entrelazado

Los clientes no es necesario hacer nada si le gusta el contenido entrelazadas ser automáticamente anule la entrelazado. Cuando la automática de entrelazado está activado (predeterminado) el MES no la detección automática de marcos entrelazados y solo anule la interlaces marcos marcados como exploración continua.

Puede desactivar la automática de entrelazado. Esta opción no se recomienda.

###<a name="json-preset"></a>Valores predeterminados JSON
    
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

###<a name="xml-preset"></a>Valores predeterminados XML
    
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


##<a id="audio_only"></a>Valores preestablecidos solo de audio

Esta sección muestra solo de audio MES preestablecidos: AAC buena calidad de sonido y Audio AAC.

###<a name="aac-audio"></a>Audio AAC 

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

###<a name="aac-good-quality-audio"></a>Audio de calidad AAC

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="concatenate"></a>Concatenar dos o más archivos de vídeo

En el ejemplo siguiente se muestra cómo puede generar un valor preestablecido para concatenar dos o más archivos de vídeo. El escenario más común es cuando desea agregar un encabezado o un final al vídeo principal. El uso es cuando los archivos de vídeo que se está editados juntos compartan propiedades (resolución de vídeo, velocidad, recuento de pista de audio, etcetera). Procure no para mezclar vídeos de velocidades diferentes, o con un número diferente de pistas de audio.

###<a name="requirements-and-considerations"></a>Consideraciones y requisitos

- Vídeos de entrada solo deben tener una pista de audio.
- Vídeos de entrada deben tener la misma velocidad de marco.
- Debe cargar vídeos en activos separados y establecer los vídeos como archivo principal de cada activo.
- Debe saber la duración de los vídeos.
- Los siguientes ejemplos preestablecidos, se supone que todos los vídeos entrados empiezan con una marca de tiempo de cero. Necesita modificar los valores de hora de inicio si los vídeos tienen marca de hora inicial diferentes, como sucede normalmente con archivos de almacenamiento directo.
- La programación JSON hace referencia explícita a los valores de idtema de los activos de entrada.
- El código de ejemplo se supone que la programación JSON se ha guardado en un archivo local, como "C:\supportFiles\preset.json". También se supone que se han creado dos activos cargando dos archivos de vídeo, y sabe los valores idtema resultantes.
- El fragmento de código y JSON preestablecidos muestran un ejemplo de concatenar dos archivos de vídeo. Se puede extender a más de dos vídeos por:

    1. Tarea de llamada. InputAssets.Add() varias veces para agregar más vídeos en orden.
    2. Realizar correspondiente modificaciones al elemento "Orígenes" en JSON, agregando más entradas en el mismo orden. 


###<a name="net-code"></a>Código de .NET

    
    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();
    
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");
    
    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);
    
    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    
    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

###<a name="json-preset"></a>Valores predeterminados JSON

Actualizar personalizado preestablecido con identificadores de los activos que desea concatenar y el segmento de hora adecuada para cada vídeo.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="crop"></a>Vídeos de recorte con Media Encoder estándar

Vea el tema de [vídeos de recorte con Media Encoder estándar](media-services-crop-video.md) .

##<a id="no_video"></a>Insertar un control de vídeo de entrada cuando no tiene vídeo

De forma predeterminada, si envía una entrada al codificador que contiene solo audio y sin vídeo, el activo de salida contiene los archivos que contienen datos solo de audio. Algunos reproductores, incluidos Reproductor de Azure (consulte [este](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) no puede manejar estas secuencias. Puede usar esta opción para forzar el codificador para agregar un control de vídeo monocromática a los resultados en ese caso. 

>[AZURE.NOTE]Forzar el codificador para insertar un control de vídeo de salida aumenta el tamaño del resultado activo, y, con ello, incurre el costo de la tarea de codificación. Debe ejecutar pruebas para comprobar que este aumento resultante tiene solo un pequeño impacto en sus gastos mensuales.

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Insertar vídeo en la menor velocidad de bits

Supongamos que usa una codificación de velocidad varias preestablecidos como ["H264 velocidad varias 720p"](https://msdn.microsoft.com/library/mt269960.aspx) para codificar el catálogo completo de entrada para la transmisión por secuencias, que contiene una combinación de archivos de vídeo y solo de audio. En este escenario, cuando la entrada no tiene vídeo, es podrán que desee forzar el codificador para insertar un control de vídeo monocromática a solo la velocidad de bits más bajo, en lugar de insertar vídeo en cada velocidad de salida. Para ello, debe especificar el marcador "InsertBlackIfNoVideoBottomLayerOnly".

Puede realizar cualquiera de los MES preestablecidos documentado [aquí](https://msdn.microsoft.com/library/mt269960.aspx)y realizar la modificación de los siguiente:

#### <a name="json-preset"></a>Valores predeterminados JSON

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Valores predeterminados XML

    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideoBottomLayerOnly</Condition>

### <a name="inserting-video-at-all-output-bitrates"></a>Insertar vídeo en absoluto velocidades de bits bajas de salida

Supongamos que usa una codificación de velocidad varias preestablecidos como ["H264 velocidad varias 720p](https://msdn.microsoft.com/library/mt269960.aspx) para codificar el catálogo completo de entrada para la transmisión por secuencias, que contiene una combinación de archivos de vídeo y solo de audio. En este escenario, cuando la entrada no tiene vídeo, es podrán que desee forzar el codificador para insertar una pista de vídeo monocromática en todo el velocidades de bits bajas de salida. Esto garantiza que los resultados son todos los homogéneos con respecto a la cantidad de pistas de vídeo y audio pistas activos. Para ello, debe especificar el marcador "InsertBlackIfNoVideo".

Puede realizar cualquiera de los MES preestablecidos documentado [aquí](https://msdn.microsoft.com/library/mt269960.aspx)y realizar la modificación de los siguiente:

#### <a name="json-preset"></a>Valores predeterminados JSON

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>Valores predeterminados XML
    
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideo</Condition>

##<a id="rotate_video"></a>Girar un vídeo

La [Media Encoder estándar](media-services-dotnet-encode-with-media-encoder-standard.md) es compatible con rotación de ángulos de 0 o 90/180 o 270. El comportamiento predeterminado es "Automático" cuando intenta detectar los metadatos de rotación en el archivo de vídeo entrante y compensación. El siguiente elemento de **orígenes** a uno de los valores preestablecidos definido [aquí](http://msdn.microsoft.com/library/azure/mt269960.aspx)se incluyen:

### <a name="json-preset"></a>Valores predeterminados JSON

    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...
### <a name="xml-preset"></a>Valores predeterminados XML

    <Sources>
        <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Asimismo, consulte [este](https://msdn.microsoft.com/library/azure/mt269962.aspx#PreserveResolutionAfterRotation) tema para obtener más información sobre cómo el codificador interpreta la configuración de ancho y alto en la programación, cuando se activa la compensación de giro.

Puede usar el valor "0" para indicar al codificador omitir metadatos de giro, si están presentes en la entrada de vídeo. 


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vea también 

[Información general de codificación de servicios multimedia](media-services-encode-asset.md)
