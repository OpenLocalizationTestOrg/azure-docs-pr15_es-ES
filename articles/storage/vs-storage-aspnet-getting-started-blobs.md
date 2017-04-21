<properties
    pageTitle="Empezar a trabajar con blob almacenamiento y Visual Studio conectan servicios (ASP.NET) | Microsoft Azure"
    description="Cómo empezar a usar el almacenamiento de blobs de Windows Azure en un proyecto ASP.NET en Visual Studio después de conectar con una cuenta de almacenamiento mediante Visual Studio conectado servicios"
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

# <a name="get-started-with-blob-storage-and-visual-studio-connected-services-aspnet"></a>Empezar a trabajar con blob almacenamiento y Visual Studio conectan servicios (ASP.NET)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

En este artículo se describe cómo empezar a usar el almacenamiento de blobs de Windows Azure después de que haya creado o hace referencia a una cuenta de almacenamiento de Azure en una aplicación de ASP.NET mediante el cuadro de diálogo de Visual Studio **Agregar servicios conectados** . El artículo le muestra cómo crear contenedores de blob y realizar otras tareas comunes como cargar, listado, descargar y eliminar BLOB. Los ejemplos están escritos en C\# y usar la [Biblioteca de cliente de Microsoft Azure almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

 - Para obtener información general sobre el uso de almacenamiento de blobs de Windows Azure, consulte [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md).
 - Para obtener más información acerca de los proyectos ASP.NET, consulte [ASP.NET](http://www.asp.net).


Almacenamiento de blobs de Windows Azure es un servicio para almacenar grandes cantidades de datos no estructurados que pueden tener acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un blob único puede ser de cualquier tamaño. BLOB puede ser cosas como imágenes, archivos de audio y vídeo, datos sin formato y archivos de documento.

Como archivos live en carpetas, BLOB almacenamiento live en contenedores. Después de haber creado una cuenta de almacenamiento, cree uno o varios contenedores en el almacenamiento. Por ejemplo, en un almacenamiento denominado "Recortes", puede crear contenedores de blob en el almacenamiento de "imágenes" para almacenar imágenes y otra denominada "audio" para almacenar archivos de audio. Después de crear los contenedores, puedes cargar archivos individuales blob en ellos.




## <a name="access-blob-containers-in-code"></a>Contenedores de blobs de acceso en el código

Para obtener acceso a BLOB en proyectos ASP.NET, debe agregar los elementos siguientes, si ya no están presentes.

1. Agregue las siguientes declaraciones de espacio de nombres de código a la parte superior de cualquier archivo de C# en la que desea acceder mediante programación el almacenamiento de Azure.

        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;


2. Obtenga un objeto **CloudStorageAccount** que representa la información de su cuenta de almacenamiento. Utilice el siguiente código para obtener la su cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    > [AZURE.NOTE] Utilizar todo el código anterior delante del código en las secciones siguientes.

3. Obtener un objeto de **CloudBlobClient** para hacer referencia a un contenedor existente en su cuenta de almacenamiento.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] Algunas de las API que realizan llamadas fuera con el almacenamiento de Azure en 5 ASP.NET son asincrónicas. Para obtener más información, vea [Programación asincrónica con asincrónico y espera](http://msdn.microsoft.com/library/hh191443.aspx) .


## <a name="create-a-blob-container-in-code"></a>Crear un contenedor de blob en código

También puede usar el objeto **CloudBlobClient** para crear un contenedor en su cuenta de almacenamiento. Todo lo que necesita hacer es agregar una llamada a **CreateIfNotExistsAsync** para el código anterior tal como se muestra en el ejemplo siguiente.

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Admite de almacenamiento de blobs de Windows Azure bloquea BLOB y blobs de página.  En la mayoría de los casos, blobs de bloque es el tipo recomendado para usar.

Para cargar un archivo a un blob bloque, obtener una referencia de contenedor y usarlo para obtener una referencia de blobs de bloque. Una vez que tenga una referencia de blobs de Windows, puede cargar cualquier secuencia de datos a él llamando al método **UploadFromStream** . Esta operación creará el blob si no existe previamente o sobrescribir si existe. En el ejemplo siguiente se muestra cómo cargar un blob en un contenedor y se supone que ya se ha creado el contenedor.

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>El BLOB en un contenedor de lista

Para mostrar los BLOB en un contenedor, utilice el método **ListBlobs** para recuperar los BLOB o directorios dentro de ella. Para obtener acceso amplio conjunto de propiedades y métodos para un devuelto **IListBlobItem**, debe convertirlo en un objeto **CloudBlockBlob**, **CloudPageBlob**o **CloudBlobDirectory** .  Si se desconoce el tipo, puede usar una comprobación de tipo para determinar qué convertirlo a.  El código siguiente muestra cómo recuperar y salida URI de cada elemento en el contenedor de **fotos** .

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

Como se muestra en el ejemplo anterior, el servicio de blobs tiene el concepto de directorios dentro de contenedores, también. Se trata de modo que puede organizar su BLOB en una estructura más similares a las carpetas. Por ejemplo, considere el siguiente conjunto de blobs de bloque en un contenedor denominado **fotos**.

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Cuando llame a **ListBlobs** en el contenedor 'fotografías' (como se muestra en el ejemplo anterior), la colección devuelta contendrá objetos **CloudBlobDirectory** y **CloudBlockBlob** que representan los directorios y BLOB contenida en el nivel superior. En el ejemplo siguiente se muestra el resultado.

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Si lo desea, puede establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** en **true**. El resultado sería cada blob que se devuelven como un **CloudBlockBlob**, independientemente del directorio.  En el ejemplo siguiente se muestra la llamada a **ListBlobs**.

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

Y, en el ejemplo siguiente se muestra los resultados.

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg



## <a name="download-blobs"></a>Descargar BLOB

Para descargar BLOB, use el método de **DownloadToStream** . En el ejemplo siguiente se utiliza el método **DownloadToStream** para transferir el contenido de blobs de Windows a un objeto de secuencia, a continuación, puede conservar en un archivo local.

    // Retrieve a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

También puede usar el método **DownloadToStream** para descargar el contenido de un blob como una cadena de texto.

    // Retrieve a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminar BLOB

Para eliminar un blob, use el método **Delete** .

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Lista BLOB en las páginas de forma asincrónica

Si se enumerar un gran número de BLOB, o que desee para controlar el número de resultados que devuelto en una operación de entrada, puede mostrar BLOB en páginas de resultados. En el ejemplo siguiente se muestra cómo devolver resultados en las páginas de forma asincrónica, para que no se bloquea la ejecución mientras se espera para devolver un amplio conjunto de resultados.

Este ejemplo muestra un plano blob listado, pero también puede realizar una lista jerárquica, estableciendo el parámetro **useFlatBlobListing** del método **ListBlobsSegmentedAsync** en **false**.

Dado que el método de ejemplo llama un método asincrónico, debe ir precedido de la **palabra** clave y debe devolver un objeto de **tarea** . La palabra clave de espera especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método de ejemplo hasta que se complete la tarea de la lista.

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

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
