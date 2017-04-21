<properties 
    pageTitle="Cargar archivos a una cuenta de Media Services utilizando .NET | Microsoft Azure" 
    description="Aprenda a obtener contenido multimedia en Media Services creando y cargar activos." 
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
    ms.author="juliako"/>



# <a name="upload-files-into-a-media-services-account-using-net"></a>Cargar archivos a una cuenta de Media Services utilizando .NET

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [REST](media-services-rest-upload-files.md)
 - [Portal](media-services-portal-upload-files.md)

En servicios de medios, cargar (o recopilar) sus archivos digitales en un activo. Entidad de **activos** puede contener vídeo, audio, imágenes, colecciones de miniaturas, texto pistas y subtítulos archivos (y los metadatos acerca de estos archivos.)  Una vez que se cargan los archivos, el contenido se almacena de forma segura en la nube para el procesamiento de más y transmisión.

Los archivos en el activo se denominan **Archivos de activos**. La instancia de **AssetFile** y el archivo multimedia reales son dos objetos. La instancia de AssetFile contiene metadatos acerca del archivo multimedia, mientras que el archivo multimedia contiene el contenido multimedia real.

>[AZURE.NOTE]Al elegir un nombre de archivo de activos, se aplican las consideraciones siguientes:
>
>- Media Services usa el valor de la propiedad IAssetFile.Name al generar direcciones URL para la transmisión de contenido (por ejemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, no se permite la codificación de porcentaje. El valor de la propiedad **nombre** no puede tener cualquiera de los siguientes [caracteres por ciento codificación-reservados](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Además, sólo puede haber una '.' para la extensión de nombre de archivo.
>
>- La longitud del nombre no debe ser mayor que 260 caracteres.

Al crear activos, puede especificar las siguientes opciones de cifrado. 

- **Ninguno** : No cifrado se utiliza. Este es el valor predeterminado. Tenga en cuenta que al usar esta opción su contenido no está protegido al transmitirlos o al resto de almacenamiento.
Si va a ofrecer un MP4 mediante descarga progresiva, use esta opción. 
- **CommonEncryption** - Use esta opción si va a cargar contenido que ya se ha cifrado y protegido con cifrado comunes o DRM de PlayReady (por ejemplo, Smooth Streaming protegido con PlayReady DRM).
- **EnvelopeEncrypted** : Use esta opción si va a cargar HLS cifrada con AES. Tenga en cuenta que deben ha codificado y cifrado administrador transformar los archivos.
- **StorageEncrypted** - cifra su Borrar contenido localmente con cifrado AES-256 bits y, a continuación, cargas a donde está almacenado el almacenamiento de Azure cifrado al resto. Activos protegidos con cifrado de almacenamiento automáticamente sin cifrar y colocados en un sistema de archivos cifrados antes de codificación y, opcionalmente, cifrados de nuevo antes de cargarlo como un nuevo activo de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos de medios de entrada de alta calidad con cifrado almacenados en disco.

    Media Services proporciona cifrado de almacenamiento en disco para los activos, no sobre el cable como el Administrador de derechos digitales (DRM).

    Si el activo es almacenamiento cifrado, debe configurar Directiva de entrega de activos. Para obtener más información, vea [Directiva de entrega de activos de configuración](media-services-dotnet-configure-asset-delivery-policy.md).

Si especifica para que su activo cifrado con una opción de **CommonEncrypted** o un **EnvelopeEncypted** , debe asociar los activos con un **ContentKey**. Para obtener más información, vea [cómo crear un ContentKey](media-services-dotnet-create-contentkey.md). 

Si especifica para que su activo cifrado con la opción **StorageEncrypted** , el SDK de servicios de medios para .NET creará una **StorateEncrypted** **ContentKey** para el activo.


Este tema muestra cómo usar Media Services .NET SDK, así como las extensiones de .NET SDK de Media Services para cargar archivos en un activo de Media Services.

 
## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Cargar un único archivo con Media Services .NET SDK 

El código de ejemplo siguiente utiliza .NET SDK para realizar las siguientes tareas: 

- Crea un activo vacío.
- Crea una instancia de AssetFile que se desea asociar al activo.
- Crea una instancia de AccessPolicy que define los permisos y la duración de acceso al activo.
- Crea una instancia de localizador que proporciona acceso a los activos.
- Carga un solo archivo multimedia en Media Services. 

        
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
        }

##<a name="upload-multiple-files-with-media-services-net-sdk"></a>Cargar varios archivos con Media Services .NET SDK 

El código siguiente muestra cómo crear un activo y cargar varios archivos.

El código hace lo siguiente:
    
-   Crea un activo vacío del método CreateEmptyAsset definido en el paso anterior.
    
-   Crea una instancia de **AccessPolicy** que define los permisos y la duración de acceso al activo.
    
-   Crea una instancia de **localizador** que proporciona acceso a los activos.
    
-   Crea una instancia de **BlobTransferClient** . Este tipo representa a un cliente que opera en blobs de Azure. En este ejemplo, usamos al cliente para supervisar el progreso de carga. 
    
-   Enumera los archivos del directorio especificado y crea una instancia de **AssetFile** para cada archivo.
    
-   Carga los archivos en Media Services con el método **UploadAsync** . 
    
>[AZURE.NOTE] Use el método UploadAsync para asegurarse de que no están bloqueando las llamadas y los archivos cargados en paralelo.
    
    
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
    
    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }



Al cargar una gran cantidad de activos, tenga en cuenta lo siguiente.

- Crear un nuevo objeto **CloudMediaContext** por subproceso. La clase **CloudMediaContext** no es segura para subprocesos.
 
- Aumentar NumberOfConcurrentTransfers el valor predeterminado de 2 a un valor más alto como 5. Esta propiedad afecta a todas las instancias de **CloudMediaContext**. 
 
- Mantener ParallelTransferThreadCount en el valor predeterminado de 10.
 
##<a id="ingest_in_bulk"></a>Recopila los activos de forma masiva con Media Services .NET SDK 

Cargar archivos grandes activos puede ser una botella durante la creación de activos. Recopila los activos en masa o "Masa recopila", implica la disociación de creación de activos desde el proceso de carga. Para usar una masa recopila enfoque, cree un manifiesto (IngestManifest) que describa el activo y sus archivos asociados. A continuación, use el método de carga de su elección para cargar los archivos asociados al contenedor de blob del manifiesto. Microsoft Azure Media Services inspecciona el contenedor de blob asociado con el manifiesto. Una vez que se carga un archivo al contenedor de blob, Microsoft Azure Media Services completa la creación de activos según la configuración del activo en el manifiesto (IngestManifestAsset).


Para crear una nueva llamada IngestManifest el método Create expuesto por la colección de IngestManifests en la CloudMediaContext. Este método creará un nuevo IngestManifest con el nombre manifiesto que proporcione.

    IIngestManifest manifest = context.IngestManifests.Create(name);

Crear los activos que se asociará con masa IngestManifest. Configurar las opciones de cifrado que desee en el activo para recopila de forma masiva.

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Un IngestManifestAsset asocia un activo con una masa IngestManifest para recopila de forma masiva. También asocia el AssetFiles que conformarán cada activo. Para crear un IngestManifestAsset, use el método de crear en el contexto del servidor.

En el ejemplo siguiente se muestra Agregar IngestManifestAssets nuevos dos que asociar los dos activos creados previamente en masa recopilar manifiesto. Cada IngestManifestAsset también asocia un conjunto de archivos que se cargan para cada activo durante recopila de forma masiva.  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;
    
    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
    
Puede usar cualquier aplicación de cliente de alta velocidad capaz de cargar los archivos de activos en el contenedor de almacenamiento de blobs URI proporcionado por la propiedad **IIngestManifest.BlobStorageUriForUpload** de la IngestManifest. Uno de los servicios de carga de alta velocidad notables es [Aspera On Demand de aplicación de Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). También puede escribir código para cargar los archivos de activos, como se muestra en el siguiente ejemplo.
    
    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
    
            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
    
            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);
    
            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });
    
        copytask.Start();
    }

El código para cargar los archivos de activos para el ejemplo que se utilizan en este tema se muestra en el ejemplo siguiente.
    
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
    

Puede determinar el progreso de la recopila de forma masiva para todos los activos asociados con un **IngestManifest** por la propiedad de estadísticas de la **IngestManifest**de sondeo. Para actualizar la información de progreso, debe usar un nuevo **CloudMediaContext** cada vez que sondear la propiedad estadísticas.

En el ejemplo siguiente se muestra un IngestManifest por su **identificador**de sondeo.
    
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
    
          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
    
                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }
    
             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
    


##<a name="upload-files-using-net-sdk-extensions"></a>Cargar archivos con extensiones de .NET SDK 

El ejemplo siguiente muestra cómo cargar un único archivo con las extensiones de .NET SDK. En este caso se utiliza el método **CreateFromFile** , pero la versión asincrónica también está disponible (**CreateFromFileAsync**). El método **CreateFromFile** le permite especificar el nombre de archivo, opción de cifrado y una devolución de llamada para informar del progreso de carga del archivo.


    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });
    
        Console.WriteLine("Asset {0} created.", inputAsset.Id);
    
        return inputAsset;
    }

En el siguiente ejemplo se llama UploadFile función y se especifica el cifrado de almacenamiento como la opción de creación de activos.  


    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-step"></a>Siguiente paso

Ahora que ha cargado un activo Media Services, vaya al tema de [cómo obtener un procesador multimedia][] .

[Cómo obtener un procesador de medios]: media-services-get-media-processor.md
 
