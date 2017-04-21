<properties 
    pageTitle="Codificar un activo con Media Encoder estándar con .NET | Microsoft Azure" 
    description="Este tema muestra cómo usar .NET para codificar un activo mediante Media Encoder Strandard." 
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
    ms.date="09/19/2016"
    ms.author="juliako;anilmur"/>


# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codificar un activo con Media Encoder estándar con .NET

Codificación de trabajos es una de las operaciones de transformación más comunes de Media Services. Crear tareas de codificación para convertir archivos multimedia de una codificación a otra. Cuando se codifica, puede usar el Codificador multimedia de Media Services integrados. También puede utilizar un codificador de un socio Media Services; codificadores de terceros están disponibles a través de Azure Marketplace. 

Este tema muestra cómo usar .NET para codificar los activos con Media Encoder estándar (MES). Media Encoder estándar está configurado con uno del codificador valores preestablecidos se describe [a continuación](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Se recomienda codificar siempre los archivos mezzanine en una velocidad adaptación MP4 establecer y, a continuación, convertir el conjunto en el formato deseado mediante el [Embalaje dinámico](media-services-dynamic-packaging-overview.md). Para aprovechar las ventajas de embalaje dinámico, primero debe obtener al menos una unidad de transmisión por secuencias a petición del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido. Para obtener más información, vea [Cómo escala Media Services](media-services-portal-manage-streaming-endpoints.md).

Si su activo de salida es almacenamiento cifrado, debe configurar Directiva de entrega de activos. Para obtener más información, vea [Directiva de entrega de activos de configuración](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]MES genera un archivo de salida con un nombre que contiene los 32 primeros caracteres del nombre del archivo de entrada. El nombre se basa en lo especificado en el archivo preestablecido. Por ejemplo, "nombredearchivo": "{nombre base} _ {Index} {extensión}". {Nombre base} se reemplaza por los 32 primeros caracteres del nombre del archivo de entrada.

###<a name="mes-formats"></a>Formatos MES

[Formatos y códecs](media-services-media-encoder-standard-formats.md)

###<a name="mes-presets"></a>Valores preestablecidos MES

Media Encoder estándar está configurado con uno del codificador valores preestablecidos se describe [a continuación](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Metadatos de entrada y salido

Al codificar un entrada activo (o activos) con el MES, ir de un activo de salida de la correcta codificar de realización de dicha tarea. Los activos de salida contiene vídeo, audio, miniaturas, manifiesto, etc., basado en la programación de codificación que utilice.

Los activos de salida también contiene un archivo con metadatos acerca de los activos de entrada. El nombre del archivo XML de metadatos tiene el siguiente formato: _metadata.xml < asset_id > (por ejemplo, 41114ad3-eb5e - 4c 57 8d 92-5354e2b7d4a4_metadata.xml), donde < asset_id > es el valor de idtema de entrada activo. El esquema de esta entrado metadatos XML se describe [a continuación](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Los activos de salida también contiene un archivo con metadatos acerca de los activos de salida. El nombre del archivo XML de metadatos tiene el siguiente formato: < source_file_name > _manifest.xml (por ejemplo, BigBuckBunny_manifest.xml). El esquema de este resultado los metadatos XML se describe [a continuación](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Si desea examinar cualquiera de los dos archivos de metadatos, puede crear un localizador SA y descargue el archivo en el equipo local. Puede encontrar un ejemplo sobre cómo crear un localizador SA y descargar un archivo mediante las extensiones de Media Services .NET SDK.

##<a name="download-sample"></a>Descargar ejemplo

Obtener y ejecutar un ejemplo desde [aquí](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##<a name="example"></a>Ejemplo

En el ejemplo siguiente se utiliza Media Services .NET SDK para realizar las siguientes tareas:

- Crear un trabajo de codificación.
- Obtenga una referencia al codificador multimedia codificador estándar.
- Especificar para usar la "H264 velocidad varias 720p" preestablecido. Puede ver todos los valores preestablecidos [aquí](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). También puede examinar el esquema al que deben cumplir estos valores preestablecidos tema [aquí](https://msdn.microsoft.com/library/mt269962.aspx) .
- Agregar una sola tarea de codificación para el trabajo. 
- Especifique la entrada activo para codificar.
- Crear un activo de salida que contendrá el codificado activo.
- Agregar un controlador de eventos para comprobar el progreso de tarea.
- Enviar el trabajo.
        
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        

            // Create a task with the encoding details, using a string preset.
            // In this case "H264 Multiple Bitrate 720p" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Multiple Bitrate 720p",
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


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vea también 

[Cómo generar miniatura utilizando Media Encoder estándar con .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Multimedia codificación información general sobre servicios](media-services-encode-asset.md)
