<properties
    pageTitle="Empezar a trabajar con blob almacenamiento y Visual Studio conectan (servicios de nube) | Microsoft Azure"
    description="Cómo empezar a usar el almacenamiento de blobs de Windows Azure en un proyecto de servicio de nube en Visual Studio después de conectar con una cuenta de almacenamiento mediante Visual Studio conectado servicios"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Empezar a usar el almacenamiento de blobs de Windows Azure y Visual Studio conectado servicios (proyectos de servicios de nube)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

En este artículo se describe cómo empezar a trabajar con el almacenamiento de blobs de Windows Azure después de que se creó o se hace referencia a una cuenta de almacenamiento de Azure mediante el cuadro de diálogo **Agregar servicios conectados** de Visual Studio en un proyecto de servicios de nube de Visual Studio. Le mostraremos cómo acceder y crear blob contenedores y cómo llevar a cabo tareas comunes, como cargar, listado y descargar BLOB. Los ejemplos están escritos en C\# y usar la [Biblioteca de cliente de Microsoft Azure almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Almacenamiento de blobs de Windows Azure es un servicio para almacenar grandes cantidades de datos no estructurados que pueden tener acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un blob único puede ser de cualquier tamaño. BLOB puede ser cosas como imágenes, archivos de audio y vídeo, datos sin formato y archivos de documento.

Como archivos live en carpetas, BLOB almacenamiento live en contenedores. Después de haber creado un almacenamiento, cree uno o varios contenedores en el almacenamiento. Por ejemplo, en un almacenamiento denominado "Recortes", puede crear contenedores en el almacenamiento de "imágenes" para almacenar imágenes y otra denominada "audio" para almacenar archivos de audio. Después de crear los contenedores, puedes cargar archivos individuales blob en ellos.

- Para obtener más información sobre manipular mediante programación BLOB, vea [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md).
- Para obtener información general sobre el almacenamiento de Azure, consulte la [documentación de almacenamiento](https://azure.microsoft.com/documentation/services/storage/).
- Para obtener información general sobre servicios de nube de Azure, consulte la [documentación de servicios en la nube](https://azure.microsoft.com/documentation/services/cloud-services/).
- Para obtener más información sobre la programación de aplicaciones de ASP.NET, consulte [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Contenedores de blobs de acceso en el código

Para obtener acceso a BLOB en proyectos de servicio de nube, debe agregar los elementos siguientes, si ya no están presentes.

1. Agregue las siguientes declaraciones de espacio de nombres de código a la parte superior de cualquier archivo de C# en la que desea acceder mediante programación el almacenamiento de Azure.

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenga un objeto **CloudStorageAccount** que representa la información de su cuenta de almacenamiento. Utilice el siguiente código para obtener la su cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Obtener un objeto de **CloudBlobClient** para hacer referencia a un contenedor existente en su cuenta de almacenamiento.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

4. Obtener un objeto de **CloudBlobContainer** para hacer referencia a un contenedor de blob específicos.

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] Utilizar todo el código que se muestra en el procedimiento anterior delante del código que se muestra en las secciones siguientes.

## <a name="create-a-container-in-code"></a>Crear un contenedor en código

> [AZURE.NOTE] Algunas API que realiza llamadas fuera con el almacenamiento de Azure en ASP.NET es asincrónica. Para obtener más información, vea [programación asincrónico con asincrónico y espera](http://msdn.microsoft.com/library/hh191443.aspx) . El código en el ejemplo siguiente se supone que está usando métodos de programación asincrónica.

Para crear un contenedor en su cuenta de almacenamiento, todo lo que debe hacer es agregar una llamada a **CreateIfNotExistsAsync** como en el siguiente código:

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


Para que los archivos dentro del contenedor esté disponible para todos los usuarios, puede establecer el contenedor para ser público mediante el siguiente código.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Cualquier usuario de Internet puede ver BLOB en un contenedor público, pero se puede modificar o eliminar solo si tiene la clave de acceso adecuado.

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Almacenamiento de Azure admite bloque BLOB y blobs de página. En la mayoría de los casos, blobs de bloque es el tipo recomendado para usar.

Para cargar un archivo a un blob bloque, obtener una referencia de contenedor y usarlo para obtener una referencia de blobs de bloque. Una vez que tenga una referencia de blobs de Windows, puede cargar cualquier secuencia de datos a él llamando al método **UploadFromStream** . Esta operación crea el blob si previamente no existe, o se sobrescribe si existe. En el ejemplo siguiente se muestra cómo cargar un blob en un contenedor y se supone que ya se ha creado el contenedor.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>El BLOB en un contenedor de lista

Para mostrar los BLOB en un contenedor, obtener una referencia de contenedor. A continuación, puede usar el método de **ListBlobs** del contenedor para recuperar los BLOB o directorios dentro de ella. Para obtener acceso amplio conjunto de propiedades y métodos para un devuelto **IListBlobItem**, debe convertirlo en un objeto **CloudBlockBlob**, **CloudPageBlob**o **CloudBlobDirectory** . Si se desconoce el tipo, puede usar una comprobación de tipo para determinar qué convertirlo a. El código siguiente muestra cómo recuperar y el URI de cada elemento en el contenedor de **fotos** de salida:

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

Como se muestra en el ejemplo anterior, el servicio de blobs tiene el concepto de directorios dentro de contenedores, también. Se trata de modo que puede organizar su BLOB en una estructura más similares a las carpetas. Por ejemplo, considere el siguiente conjunto de blobs de bloque en un contenedor denominado **fotos**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Cuando llame a **ListBlobs** en el contenedor (como en el ejemplo anterior), la colección devuelta contiene objetos de **CloudBlobDirectory** y **CloudBlockBlob** representan los directorios y BLOB contenida en el nivel superior. Esto es el resultado:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Si lo desea, puede establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** en **true**. El resultado es cada blob que se devuelven como un **CloudBlockBlob**, independientemente del directorio. Aquí es la llamada a **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

y estos son los resultados:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para obtener más información, consulte [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Descargar BLOB

Para descargar BLOB, primero recuperar una referencia de blobs de Windows y, a continuación, llame al método **DownloadToStream** . En el ejemplo siguiente se utiliza el método **DownloadToStream** para transferir el contenido de blobs de Windows a un objeto de secuencia, a continuación, puede conservar en un archivo local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

También puede usar el método **DownloadToStream** para descargar el contenido de un blob como una cadena de texto.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminar BLOB

Para eliminar un objeto binario, obtener una referencia de blobs de Windows y, a continuación, llame al método **Eliminar** .

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Lista BLOB en las páginas de forma asincrónica

Si se enumerar un gran número de BLOB, o que desee para controlar el número de resultados que devuelto en una operación de entrada, puede mostrar BLOB en páginas de resultados. Este ejemplo muestra cómo devolver resultados en las páginas de forma asincrónica, para que no se bloquea la ejecución mientras se espera para devolver un amplio conjunto de resultados.

Este ejemplo muestra un plano blob listado, pero también puede realizar una lista jerárquica, estableciendo el parámetro **useFlatBlobListing** del método **ListBlobsSegmentedAsync** en **false**.

Dado que el método de ejemplo llama un método asincrónico, debe ir precedido de la **palabra** clave y debe devolver un objeto de **tarea** . La palabra clave de espera especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método de ejemplo hasta que se complete la tarea de la lista.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
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

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
