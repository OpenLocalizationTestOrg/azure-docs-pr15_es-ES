<properties
    pageTitle="Archivos multimedia de Hyperlapse con Hyperlapse multimedia de Azure | Microsoft Azure"
    description="Azure Hyperlapse multimedia crea suaves vídeos de tiempo transcurrido desde el contenido de la primera persona o una cámara de acción. Este tema muestra cómo usar el indizador de Media."
    services="media-services"
    documentationCenter=""
    authors="asolanki"
    manager="johndeu"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/19/2016"  
    ms.author="adsolank"/>


# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Archivos multimedia de Hyperlapse con Hyperlapse multimedia de Azure

Azure Hyperlapse Media es una Media procesador (programa mínimo) que cree vídeos de tiempo transcurrido suaves del contenido de la primera persona o una cámara de acción.  El relacionado en la nube [de Microsoft Research escritorio Hyperlapse Pro](http://aka.ms/hyperlapse)y telefónica Hyperlapse Mobile, Microsoft Hyperlapse para Azure Media Services utiliza la escala masiva de la plataforma de procesamiento de Azure Media Services multimedia para escalar horizontalmente y ejecutar en paralelo de forma masiva Hyperlapse procesamiento.

>[AZURE.IMPORTANT]Microsoft Hyperlapse está diseñado para funcionar mejor en contenido de la primera persona con un movimiento de la cámara.  Aunque todavía puede trabajar grabación de la cámara fija, no se puede garantizar el rendimiento y la calidad del procesador Azure Media Hyperlapse Media para otros tipos de contenido.  Para más información sobre Microsoft Hyperlapse para Azure Media Services y vea algunos vídeos de ejemplo, consulte la [entrada de blog Introducción](http://aka.ms/azurehyperlapseblog) de la versión preliminar pública.

Un Hyperlapse multimedia de Azure trabajo toma como entrada de un archivo de activos MP4, MOV o WMV junto con un archivo de configuración que especifica las tramas de vídeo deben ser el tiempo transcurrido y con qué velocidad (por ejemplo, primeras 10.000 marcos en x 2).  El resultado es una representación estabilizada y tiempo transcurrido de la entrada de vídeo.

Para obtener las últimas actualizaciones de Azure Media Hyperlapse, consulte [blogs Media Services](https://azure.microsoft.com/blog/topics/media-services/).

## <a name="hyperlapse-an-asset"></a>Hyperlapse un activo

En primer lugar debe cargue el archivo de entrada que desee en Azure Media Services.  Para obtener más información sobre los conceptos relacionados con la carga y administración de contenido, lea el [artículo de la administración de contenido](media-services-portal-vod-get-started.md).

###  <a id="configuration"></a>Valores predeterminados de configuración para Hyperlapse

Una vez que el contenido está en su cuenta de Media Services, deberá crear la programación de la configuración.  La siguiente tabla recogen los campos especificados por el usuario:

 Campo | Descripción
-------|-------------
StartFrame|El marco en el que debe comenzar el procesamiento de Microsoft Hyperlapse.
NumFrames|El número de marcos para procesar
Velocidad|El factor que acelerar el vídeo de entrada.

A continuación se muestra un ejemplo de un archivo de configuración de compatibilidad en XML y JSON:

**Valores predeterminados XML:**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**JSON preestablecidos:**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

###  <a id="sample_code"></a>Microsoft Hyperlapse con el SDK de .NET AMS

El método siguiente carga un archivo multimedia como activo y crea un trabajo con el procesador multimedia de Azure Media Hyperlapse.

> [AZURE.NOTE] Ya que debería tener un CloudMediaContext en el ámbito con el nombre "contexto" para este código para que funcione.  Para obtener más información sobre esto, lea el [artículo de la administración de contenido](media-services-dotnet-get-started.md).

> [AZURE.NOTE] El argumento de la cadena "hyperConfig" debe ser una configuración conforme preestablecida en JSON o XML, como se describió anteriormente.

bool estático RunHyperlapseJob (entrada de cadena, los resultados de cadena, cadena hyperConfig) {/ / crear activos con activos de archivo de entrada IAsset = contexto. Activos. CreateAssetAndUploadSingleFile (entrada, "Mi entrada de Hyperlapse", AssetCreationOptions.None);

captar instancias de administración de Azure Media Hyperlapse panel de administración IMediaProcessor = context. MediaProcessors. GetLatestMediaProcessorByName ("multimedia de Azure Hyperlapse");

crear el trabajo con el trabajo de la tarea IJob Hyperlapse = context. Trabajos. Crear (String.Format (entrada de "Hyperlapse {0}"));

Si (String.IsNullOrEmpty(hyperConfig)) {/ / config no puede estar vacía devuelva false;}

hyperConfig = File.ReadAllText(hyperConfig);

ITask hyperlapseTask = trabajo. Tasks.AddNew ("Hyperlapse tareas", panel de administración, hyperConfig, TaskOptions.None); hyperlapseTask.InputAssets.Add(asset); hyperlapseTask.OutputAssets.AddNew ("Hyperlapse salida", AssetCreationOptions.None);


trabajo. Submit();

Crear la impresión de progreso y consultar tareas tareas progressPrintTask = nuevo Task(() = > {

IJob jobQuery = null; hacer {var progressContext = contexto; jobQuery = progressContext.Jobs. Donde (j = > j.Id == trabajo. Id.). First(); Console.WriteLine (cadena. Formato ("{0} \t {1} \t {2}", DateTime.Now, jobQuery.State, jobQuery.Tasks[0]. Progreso)); Thread.Sleep(10000); } mientras (jobQuery.State! = JobState.Finished & & jobQuery.State! = JobState.Error & & jobQuery.State! = JobState.Canceled); }); progressPrintTask.Start();

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }

### <a id="file_types"></a>Tipos de archivo admitidos

- MP4
- MOV
- WMV



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-links"></a>Vínculos relacionados

[Información general de análisis de servicios multimedia de Azure](media-services-analytics-overview.md)

[Azure demostraciones de análisis de medios](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
