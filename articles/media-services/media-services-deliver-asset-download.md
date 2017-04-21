<properties 
    pageTitle="Descargar activos de medios" 
    description="Aprenda a descargar activos en el equipo. Ejemplos de código están escritos en C# y usan el SDK de servicios de medios para .NET." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>

#<a name="how-to-deliver-an-asset-by-download"></a>Cómo: ofrecer un activo mediante descarga

Este tema describe las opciones para proporcionar activos de medios cargados a Media Services. Puede proporcionar contenido de Media Services en numerosos escenarios de aplicación. Puede descargar activos multimedia o tener acceso a ellas mediante un localizador. Puede enviar contenido multimedia en otra aplicación o a otro proveedor de contenido. Para mejorar el rendimiento y escalabilidad, también puede ofrecer contenido mediante el uso de una red de entrega de contenido (CDN).

Este ejemplo muestra cómo descargar activos multimedia de Media Services en el equipo local. El código de consulta de los trabajos asociados con la cuenta de Media Services por ID de trabajo y tiene acceso a su colección de **OutputMediaAssets** (que es el conjunto de uno o más activos de medios de salida que resulta de una tarea). Este ejemplo muestra cómo descargar activos de medios de salida de un trabajo, pero puede aplicar el mismo enfoque para descargar otros activos.

    
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 
    
        // Get a reference to the job. 
        IJob job = GetJob(jobId);
    
        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);
    
        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };
    
        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;
    
            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);
    
            Console.WriteLine("File download path:  " + localDownloadPath);
    
            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));
    
            outputFile.DownloadProgressChanged -= DownloadProgress;
        }
    
        Task.WaitAll(downloadTasks.ToArray());
    
        return outputAsset;
    }
    
    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

   
##<a name="see-also"></a>Vea también 

[Entregar transmisiones de contenido](media-services-deliver-streaming-content.md)

