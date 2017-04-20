<properties
    pageTitle="Introducción a almacenamiento de blobs de Windows Azure (almacenamiento de objetos) mediante .NET | Microsoft Azure"
    description="Almacenar datos estructurados en la nube con el almacenamiento de blobs de Windows Azure (almacenamiento de objetos)."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-blob-storage-using-net"></a>Introducción a almacenamiento de blobs de Windows Azure con .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

Almacenamiento de blobs de Windows Azure es un servicio que almacena datos estructurados en la nube como objetos o BLOB. Almacenamiento de blobs de Windows puede almacenar cualquier tipo de texto o datos binarios, como un documento, el archivo multimedia o el instalador de la aplicación. Almacenamiento de blobs también se conoce como almacenamiento de objetos.

### <a name="about-this-tutorial"></a>Acerca de este tutorial

Este tutorial muestra cómo escribir código .NET para algunos escenarios comunes mediante el almacenamiento de blobs de Windows Azure. Escenarios cubiertos incluyen cargando, listado, descargar y eliminar BLOB.

**Tiempo estimado para completar:** 45 minutos

**Requisitos previos:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Biblioteca de cliente de almacenamiento de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Administrador de configuración de Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Una [cuenta de almacenamiento de Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Más muestras

Para obtener más ejemplos de uso de almacenamiento de blobs, vea [Introducción a almacenamiento de blobs de Windows Azure en .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Puede descargar la aplicación de ejemplo y ejecutarla o examinar el código en GitHub.


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Agregue las declaraciones de espacio de nombres

Agregue las siguientes `using` instrucciones a la parte superior de la `program.cs` archivo:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### <a name="parse-the-connection-string"></a>Analizar la cadena de conexión

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Crear al cliente de servicio de blobs

La clase **CloudBlobClient** permite recuperar contenedores y BLOB almacenado en este servicio. Esta es una forma para crear al cliente de servicio:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Ahora está listo para escribir código que lee y escribe datos en el almacenamiento de blobs.

## <a name="create-a-container"></a>Crear un contenedor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Este ejemplo muestra cómo crear un contenedor si aún no existe:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

De forma predeterminada, el nuevo contenedor es privado, lo que significa que debe especificar la clave de acceso de almacenamiento para descargar BLOB de este contenedor. Si desea que los archivos dentro del contenedor disponible para todos los usuarios, puede establecer el contenedor sea público con el siguiente código:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Cualquier usuario de Internet puede ver BLOB en un contenedor público, pero se puede modificar o eliminar solo si tiene la tecla de acceso de la cuenta correspondiente o una firma de acceso compartido.

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Almacenamiento de blobs de Windows Azure es compatible con BLOB bloque y blobs de página.  En la mayoría de los casos, blobs de bloque es el tipo recomendado para usar.

Para cargar un archivo a un blob bloque, obtener una referencia de contenedor y usarlo para obtener una referencia de blobs de bloque. Una vez que tenga una referencia de blobs de Windows, puede cargar cualquier secuencia de datos a él llamando al método **UploadFromStream** . Esta operación creará el blob si no existe previamente o sobrescribir si existe.

En el ejemplo siguiente se muestra cómo cargar un blob en un contenedor y se supone que ya se ha creado el contenedor.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>El BLOB en un contenedor de lista

Para mostrar los BLOB en un contenedor, obtener una referencia de contenedor. A continuación, puede usar el método de **ListBlobs** del contenedor para recuperar los BLOB o directorios dentro de ella. Para obtener acceso amplio conjunto de propiedades y métodos para un devuelto **IListBlobItem**, debe convertirlo en un objeto **CloudBlockBlob**, **CloudPageBlob**o **CloudBlobDirectory** .  Si se desconoce el tipo, puede usar una comprobación de tipo para determinar qué convertirlo a.  El código siguiente muestra cómo recuperar y salida URI de cada elemento de la `photos` contenedor:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Como se indicó anteriormente, puede dar nombre BLOB con información de la ruta de acceso en sus nombres. Esto crea una estructura de directorio virtual que se puede organizar y recorrer como lo haría con un sistema de archivos tradicional. Tenga en cuenta que la estructura de directorios es virtual solo, los únicos recursos disponibles en el almacenamiento de blobs son contenedores y BLOB. Sin embargo, la biblioteca de cliente de almacenamiento ofrece un objeto **CloudBlobDirectory** para hacer referencia a un directorio virtual y simplificar el proceso de trabajar con BLOB que se organiza en este modo.

Por ejemplo, considere el siguiente conjunto de blobs de bloque en un contenedor denominado `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Cuando llame a **ListBlobs** en el contenedor 'fotografías' (como en el ejemplo anterior), se devuelve una lista jerárquica. Contiene objetos **CloudBlobDirectory** y **CloudBlockBlob** , que representan los directorios y BLOB en el contenedor, respectivamente. El resultado es similar:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Si lo desea, puede establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** en **true**. En este caso, cada blob en el contenedor se devuelve como un objeto **CloudBlockBlob** . La llamada a **ListBlobs** para devolver una lista plana es similar a esta:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

y los resultados tiene este aspecto:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## <a name="download-blobs"></a>Descargar BLOB

Para descargar BLOB, primero recuperar una referencia de blobs de Windows y, a continuación, llame al método **DownloadToStream** . En el ejemplo siguiente se utiliza el método **DownloadToStream** para transferir el contenido de blobs de Windows a un objeto de secuencia, a continuación, puede conservar en un archivo local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

También puede usar el método **DownloadToStream** para descargar el contenido de un blob como una cadena de texto.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminar BLOB

Para eliminar un objeto binario, obtener una referencia de blobs de Windows y, a continuación, llame al método **Eliminar** en él.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Lista BLOB en las páginas de forma asincrónica

Si se enumerar un gran número de BLOB, o que desee para controlar el número de resultados que devuelto en una operación de entrada, puede mostrar BLOB en páginas de resultados. Este ejemplo muestra cómo devolver resultados en las páginas de forma asincrónica, para que no se bloquea la ejecución mientras se espera para devolver un amplio conjunto de resultados.

Este ejemplo muestra un plano blob listado, pero también puede realizar una lista jerárquica, estableciendo la `useFlatBlobListing` parámetro del método **ListBlobsSegmentedAsync** a `false`.

Dado que el método de ejemplo llama un método asincrónico, debe ir precedido del `async` palabra clave y deben devolver un objeto de **tarea** . La palabra clave de espera especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método de ejemplo hasta que se complete la tarea de la lista.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="writing-to-an-append-blob"></a>Escribir en un objeto binario de datos anexados

Un blob de datos anexados es un nuevo tipo de blob, introducido con versión 5.x de la biblioteca de cliente de almacenamiento de Azure para .NET. Un blob de datos anexados está optimizado para operaciones de datos anexados, como registro. Como un blob bloque, un blob de datos anexados se compone de bloques, pero cuando agrega un nuevo bloque a un blob de datos anexados, siempre se anexan al final del blob. No se puede actualizar o eliminar un bloque existente en un objeto binario de datos anexados. El bloque de identificadores para un blob de datos anexados no se exponen como están para un blob de bloque.

Cada bloque de un blob de datos anexados puede tener un tamaño diferente, hasta un máximo de 4 MB y un blob de datos anexados puede incluir un máximo de 50.000 bloques. El tamaño máximo de un objeto binario de datos anexados, por tanto, es algo más de 195 GB (4 MB X 50.000 bloques).

El ejemplo siguiente crea un nuevo blob de datos anexados y anexe algunos datos, simular una operación de registro simple.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Vea [BLOB de bloque de descripción, Blobs de página y anexar BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx) para obtener más información sobre las diferencias entre los tres tipos de BLOB.

## <a name="managing-security-for-blobs"></a>Administrar la seguridad para blobs

De forma predeterminada, el almacenamiento de Azure protege los datos y limitar el acceso al propietario de la cuenta, que se encuentra en poder de las teclas de acceso de la cuenta. Cuando necesite compartir datos blob en su cuenta de almacenamiento, es importante hacerlo sin afectar a la seguridad de las claves de acceso de la cuenta. Además, puede cifrar datos blob para asegurarse de que es seguro va a través de la red y el almacenamiento de Azure.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Controlar el acceso a los datos blob

De forma predeterminada, los datos blob en su cuenta de almacenamiento están accesibles solo para el propietario de la cuenta de almacenamiento. Autenticación de solicitudes de almacenamiento de blobs requiere la tecla de acceso de la cuenta predeterminada. Sin embargo, puede que algunos datos blob disponible a otros usuarios. Tiene dos opciones:

- **El acceso anónimo:** Puede hacer un contenedor o su BLOB esté disponible públicamente para el acceso anónimo. Para obtener más información, vea [administrar el acceso anónimo lectura contenedores y BLOB](storage-manage-access-to-resources.md) .
- **Compartido acceso firmas:** Proporcionar a los clientes con una firma de acceso compartido (SA), que proporciona acceso delegado a un recurso en su cuenta de almacenamiento, con los permisos que especifique y en un intervalo que especifique. Para obtener más información, vea [Usar firmas de acceso compartido (SA)](storage-dotnet-shared-access-signature-part-1.md) .

### <a name="encrypting-blob-data"></a>Cifrar datos blob

Almacenamiento de Azure admite el cifrado de los datos de blob en el cliente y en el servidor:

- **Cifrado de cliente:** La biblioteca de cliente de almacenamiento para .NET admite el cifrado de datos en aplicaciones cliente antes de cargar con el almacenamiento de Azure y descifrar datos mientras se descarga al cliente. La biblioteca también admite la integración con Azure clave depósito de administración de claves de cuenta de almacenamiento. Para obtener más información, vea [Cifrado de cliente con .NET de Microsoft Azure almacenamiento](storage-client-side-encryption.md) . Consulte también [Tutorial: cifrar y descifrar BLOB en Microsoft Azure almacenamiento con Azure clave depósito](storage-encrypt-decrypt-blobs-key-vault.md).
- **Cifrado de servidor**: el almacenamiento de Azure ahora admite el cifrado de servidor. Consulte [cifrado de servicio de almacenamiento de Azure de datos en el resto (vista preliminar)](storage-service-encryption.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de blobs de Windows, siga estos vínculos para obtener más información.

### <a name="microsoft-azure-storage-explorer"></a>Explorador de almacenamiento de Microsoft Azure
- [Microsoft Azure almacenamiento Explorer (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente gratuito de Microsoft que le permite trabajar visualmente con datos de almacenamiento de Azure en Windows, OS X y Linux.

### <a name="blob-storage-samples"></a>Ejemplos de almacenamiento de blobs

- [Introducción a almacenamiento de blobs de Windows Azure en .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Referencia de almacenamiento de blobs

- [Biblioteca de cliente de almacenamiento para la referencia de .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Referencia de la API de REST](http://msdn.microsoft.com/library/azure/dd179355)

### <a name="conceptual-guides"></a>Guías conceptuales

- [Transferir datos con la utilidad de línea de comandos de AzCopy](storage-use-azcopy.md)
- [Introducción a almacenamiento de archivos para .NET](storage-dotnet-how-to-use-files.md)
- [Cómo usar el almacenamiento de blobs de Windows Azure con el SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
