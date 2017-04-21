<properties
    pageTitle="Indización de archivos multimedia con indizador multimedia de Azure"
    description="Azure indizador de Media permite que el contenido de los archivos multimedia que permiten búsquedas y generar una transcripción de texto para palabras clave y subtítulos cerrados. Este tema muestra cómo usar el indizador de Media."
    services="media-services"
    documentationCenter=""
    authors="Asolanki"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="adsolank;juliako;johndeu"/>


# <a name="indexing-media-files-with-azure-media-indexer"></a>Indización de archivos multimedia con indizador multimedia de Azure


Azure indizador de Media permite que el contenido de los archivos multimedia que permiten búsquedas y generar una transcripción de texto para palabras clave y subtítulos cerrados. Puede procesar archivo uno multimedia o varios archivos multimedia en un lote.  

>[AZURE.IMPORTANT] Cuando la indización de contenido, asegúrese de usar archivos multimedia que tienen muy clara voz (sin música de fondo, ruido, efectos o silbido micrófono). Algunos ejemplos de contenido adecuado son: grabado reuniones, conferencias o presentaciones. El siguiente contenido podría no ser adecuado para la indización: películas, programas de TV, cualquier cosa con audio mixto y efectos de sonido graban mal contenido con el ruido de fondo (hiss).


Una tarea de indización puede generar los resultados siguientes:

- Cerrar archivos de título en los siguientes formatos: **SAMI**, **TTML**y **WebVTT**.

    Archivos de subtítulos incluyen una etiqueta denominada Recognizability, que la puntuación de una tarea de indización en función de cómo reconocible es de la voz en el vídeo de origen.  Puede usar el valor de Recognizability para archivos de salida de la pantalla para su uso. Una puntuación baja significaría una mala resultados indización debido a la calidad de audio.
- Archivo de palabras clave (XML).
- Audio indización archivo blob (AIB) para su uso con SQL server.

    Para obtener más información, consulte [Usar archivos de AIB con indizador de Media de Azure y SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Este tema muestra cómo crear trabajos de indexación al **índice de un activo** y **varios archivos de índice**.

Las actualizaciones más recientes de indizador de Media de Azure, consulte [blogs Media Services](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Usar archivos de configuración y manifiesto para la indización de tareas

Puede especificar más detalles para las tareas de indización mediante una configuración de la tarea. Por ejemplo, puede especificar los metadatos que se use para el archivo multimedia. Estos metadatos se utilizan el motor de idioma para expandir su vocabulario y mejora considerablemente la precisión del reconocimiento de voz.  También es posible especificar los archivos de salida deseado.

También puede procesar varios archivos multimedia a la vez mediante un archivo de manifiestos.

Para obtener más información, vea [Tareas preestablecidos para indizador de Media de Azure](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Índice de un activo

El método siguiente carga un archivo multimedia como activo y crea un trabajo para indizar activo.

Tenga en cuenta que si no se especifica ningún archivo de configuración, el archivo multimedia se pueden indizar con todos los valores predeterminados.

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
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
<!-- __ -->
### <a id="output_files"></a>Archivos de salida

De forma predeterminada, una tarea de indización genera los siguientes archivos de salida. Los archivos se almacenarán en el primer activo de salida.

Cuando hay más de un archivo de medios de entrada, indizador generará un archivo de manifiestos para los resultados del trabajo, denominado 'JobResult.txt'. Para cada archivo multimedia de entrada, el AIB resultante, SAMI, TTML, WebVTT y archivos de palabra clave, se secuencialmente numerados y un nombre mediante el Alias de"".

Nombre de archivo | Descripción
----------|------------
__InputFileName.aib__ | Archivo de blobs indización de audio. <br /><br /> Archivo de audio Blob indización (AIB) es un archivo binario que se puede buscar en Microsoft SQL server usando la búsqueda de texto completo.  El archivo AIB es más eficaz que los archivos de título simple, porque contiene alternativas para cada palabra, lo que permite una experiencia de búsqueda mucho más rica. <br/> <br/>Requiere la instalación del complemento indizador SQL en un equipo está ejecutando Microsoft SQL server 2008 o posterior. Buscar la AIB con Microsoft SQL búsqueda de texto completo de servidor proporciona los resultados de búsqueda más precisos que buscar los archivos de subtítulos generados por WAMI. Esto es porque la AIB contiene palabras alternativas que suenen de forma similar mientras que los archivos de subtítulos contienen la palabra más alta de confianza para cada segmento del audio. Si busca oral es de importancia upmost, se recomienda usar el AIB en junto con Microsoft SQL Server.<br/><br/> Para descargar el complemento, haga clic en <a href="http://aka.ms/indexersql">Complemento de Azure Media indizador SQL</a>. <br/><br/>También es posible utilizar otros motores de búsqueda como Apache Lucene/Solr simplemente indizar el vídeo basándose en los subtítulos y archivos XML de palabra clave, pero el resultado será menos precisos resultados de búsqueda.
__InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__ |Archivos de título (CC) cerrados en formatos SAMI, TTML y WebVTT.<br/><br/>Se puede utilizar para hacer que los archivos de audio y vídeo accesibles para personas con discapacidades auditivas.<br/><br/>Archivos de títulos cerrados incluyen una etiqueta denominada <b>Recognizability</b> que la puntuación de una tarea de indización en función de cómo reconocer la voz en el vídeo de origen es.  Puede usar el valor de <b>Recognizability</b> para archivos de salida de la pantalla para su uso. Una puntuación baja significaría una mala resultados indización debido a la calidad de audio.
__InputFileName.kw.xml<br />InputFileName.info__ |Archivos de información y palabras clave. <br/><br/>Archivo de palabras clave es un archivo XML que contiene palabras clave que extrae el contenido de voz con frecuencia y la información de desplazamiento. <br/><br/>Archivo de información es un archivo de texto que contiene información detallada sobre cada término reconocido. La primera línea es especial y contiene la puntuación de Recognizability. Cada línea de las siguientes es una lista de los siguientes datos separados de pestaña: iniciar la hora, hora de finalización, palabra o frase, confianza. Reciben los tiempos en segundos y la confianza se expresa como un número de 0 a 1. <br/><br/>Línea de ejemplo: "1,20 1,45 word 0,67" <br/><br/>Estos archivos pueden ser usados para una serie de propósitos, como por ejemplo, para realizar análisis de voz, o exponen motores de búsqueda como Bing, Google o Microsoft SharePoint para que los archivos multimedia más reconocible o incluso entregar anuncios más relevantes.
__JobResult.txt__ |Manifiesto de salida, sólo aparece cuando la indización de varios archivos, que contiene la información siguiente:<br/><br/><table border="1"><tr><th>ArchivoDeEntrada</th><th>Alias</th><th>MediaLength</th><th>Error</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/>



Si no todos los archivos multimedia de entrada estén indizados correctamente, el trabajo de indización se producirá un error con código de error 4000. Para obtener más información, consulte [códigos de Error](#error_codes).

## <a name="index-multiple-files"></a>Varios archivos de índice

El siguiente método cargas varios archivos multimedia como activo y crea una tarea para todos estos archivos en un lote de índice.

Un archivo con la extensión .lst manifiesto se creen y cargar el contenido en el activo. El archivo manifiesto contiene la lista de todos los archivos de activos. Para obtener más información, vea [Tareas preestablecidos para indizador de Media de Azure](https://msdn.microsoft.com/library/dn783454.aspx).

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

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
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }

### <a name="partially-succeeded-job"></a>Tarea completado parcialmente

Si no todos los archivos multimedia de entrada estén indizados correctamente, el trabajo de indización se producirá un error con código de error 4000. Para obtener más información, consulte [códigos de Error](#error_codes).


Se generan los mismos resultados (como trabajos correctos). Puede hacer referencia al archivo manifiestos de salida para averiguar qué archivos de entrada erróneos, según los valores de columna del Error. Archivos no se generará para archivos de entrada que no se pudo, la AIB resultante, SAMI, TTML, WebVTT y palabras clave.

### <a id="preset"></a>Programación de tareas para indizador multimedia de Azure

Puede personalizar el procesamiento de Azure Media indizador proporcionando una tarea opcional preestablecida junto a la tarea.  A continuación describe el formato de xml de esta configuración.

Nombre | Requerir | Descripción
----|----|---
__entrada__ | falso | Archivos de activos que desea indizar.</p><p>Azure indizador de Media es compatible con los siguientes formatos de archivo multimedia: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Puede especificar el nombre de archivo (s) en el atributo de **lista** o el **nombre** del elemento de **entrada** (como se muestra a continuación). Si no especifica qué archivo activo de índice, se selecciona el archivo principal. Si no se establece ningún archivo activo principal, se indiza el primer archivo de entrada activo.</p><p>Para especificar explícitamente el nombre de archivo activo, realice:<br />`<input name="TestFile.wmv">`<br /><br />También puede indizar varios activos archivos a la vez (hasta 10). Para hacer esto:<br /><br /><ol class="ordered"><li><p>Crear un archivo de texto (archivo manifiesto) y asígnele una extensión .lst. </p></li><li><p>Agregar una lista de todos los nombres de archivo de activos en su entrada activo a este archivo manifiesto. </p></li><li><p>Agregar (cargar) thanifest archivo al activo.  </p></li><li><p>Especifique el nombre del archivo de manifiestos en atributo de la lista de la entrada.<br />`<input list="input.lst">`</li></ol><br /><br />Nota: Si agrega más de 10 archivos al archivo manifiesto, con el código de error de 2006 provocarán el trabajo de indización.
__metadatos__ | falso | Metadatos de los archivos de activos especificado utilizados para adaptación de vocabulario.  Es útil para preparar indizador reconozca palabras vocabulario no estándar como nombres propios.<br />`<metadata key="..." value="..."/>` <br /><br />Puede proporcionar __valores__ para __claves__de predefinido. Actualmente se admiten las siguientes claves:<br /><br />"título" y "Descripción" - utilizada para adaptación vocabulario para ajustar el idioma del modelo para su trabajo y mejoran la precisión del reconocimiento de voz.  Los valores de inicialización de búsquedas de Internet para buscar documentos de texto relevante contexto, utilizando el contenido para aumentar el diccionario interno para la duración de la tarea de indización.<br />`<metadata key="title" value="[Title of the media file]" />`<br />`<metadata key="description" value="[Description of the media file] />"`
__características__ <br /><br /> Agregar en la versión 1.2. Actualmente, la única característica compatible es el reconocimiento de voz ("ASR").| falso | La característica de reconocimiento de voz tiene las siguientes claves de configuración:<table><tr><th><p>Clave</p></th>     <th><p>Descripción</p></th><th><p>Valor de ejemplo</p></th></tr><tr><td><p>Idioma</p></td><td><p>El lenguaje natural reconozca en el archivo multimedia.</p></td><td><p>Inglés, español</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>una lista de valores separados por punto y coma de los formatos de título de salida deseado (si existe)</p></td><td><p>ttml; sami; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>Indicador booleano que especifica si no es necesario (para su uso con SQL Server y el cliente indizador IFilter) un archivo AIB.  Para obtener más información, consulte <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Usar archivos de AIB con indizador de Media de Azure y SQL Server</a>.</p></td><td><p>Verdadero; Falso</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Indicador booleano que especifica si no se requiere un archivo XML de palabra clave.</p></td><td><p>Verdadero; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Indicador booleano que especifica si o no forzar títulos completos (independientemente del nivel de confianza).  </p><p>Valor predeterminado es false, en cuyo caso palabras o frases que tienen un nivel de confianza de 50% menos se omiten de los resultados de título final y reemplazadas por puntos suspensivos ("...").  Los puntos suspensivos son útiles para auditoría y control de calidad de título.</p></td><td><p>Verdadero; False. </p></td></tr></table>

### <a id="error_codes"></a>Códigos de error

En el caso de un error, debe notificar Azure Media indizador nuevo uno de los siguientes códigos de error:

Código | Nombre | Posibles motivos
-----|------|------------------
2000 | Configuración no válida | Configuración no válida
2001 | Activos de entrada no válidos | Falta entrados activos o activo vacío.
2002 | Manifiesto no válido | Manifiesto está vacío o manifiesto contiene elementos no válidos.
2003 | Error al descargar el archivo multimedia | Dirección URL no válida en el archivo de manifiestos.
2004 | Protocolo no compatible | No se admite el protocolo de dirección URL de medios.
2005 | Tipo de archivo no admitidos | No se admite el tipo de archivo multimedia de entrada.
2006 | Hay demasiados archivos de entrada | Hay más de 10 archivos en el manifiesto de entrada.
3000 | No se pudo descodificar archivo multimedia | Códec media no admitidas <br/>o<br/> Archivo multimedia dañado <br/>o<br/> No hay ninguna secuencia de audio en medios de entrada.
4000 | Indización lote parcialmente correcta | Algunos de los archivos de medios de entrada no se pudo indizar. Para obtener más información, vea <a href="#output_files">archivos de salida</a>.
otros | Errores internos | Póngase en contacto con soporte técnico. indexer@microsoft.com


## <a id="supported_languages"></a>Idiomas admitidos

Actualmente, se admiten los idiomas inglés y en español. Para obtener más información, consulte [la entrada de blog de versión v1.2](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Vínculos relacionados

[Información general de análisis de servicios multimedia de Azure](media-services-analytics-overview.md)

[Usar archivos AIB con indizador multimedia de Azure y SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indización de archivos multimedia con vista previa de indizador 2 multimedia de Azure](media-services-process-content-with-indexer2.md)
