<properties
    pageTitle="Empezar a trabajar con blob almacenamiento y Visual Studio conectan servicios (5 ASP.NET) | Microsoft Azure"
    description="Cómo empezar a usar el almacenamiento de blobs de Windows Azure en un proyecto de Visual Studio, 5 ASP.NET después de haber creado una cuenta de almacenamiento mediante servicios de Visual Studio conectado"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-5"></a>Introducción a blobs de Windows Azure almacenamiento y Visual Studio conectan servicios (5 ASP.NET)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

##<a name="overview"></a>Información general

En este artículo se describe cómo empezar a usar el almacenamiento de blobs de Windows Azure en Visual Studio después de haber creado o hace referencia a una cuenta de almacenamiento de Azure en un proyecto de 5 ASP.NET mediante el cuadro de diálogo Servicios de conectados agregar de Visual Studio.

Almacenamiento de blobs de Windows Azure es un servicio para almacenar grandes cantidades de datos no estructurados que pueden tener acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un blob único puede ser de cualquier tamaño. BLOB puede ser cosas como imágenes, archivos de audio y vídeo, datos sin formato y archivos de documento. En este artículo se describe cómo empezar a trabajar con el almacenamiento de blobs después de crear una cuenta de almacenamiento de Azure mediante el cuadro de diálogo de Visual Studio **Agregar servicios conectados** en un proyecto de 5 ASP.NET.

Como archivos live en carpetas, BLOB almacenamiento live en contenedores. Después de haber creado un almacenamiento, cree uno o varios contenedores en el almacenamiento. Por ejemplo, en un almacenamiento denominado "Recortes", puede crear contenedores en el almacenamiento de "imágenes" para almacenar imágenes y otra denominada "audio" para almacenar archivos de audio. Después de crear los contenedores, puedes cargar archivos individuales blob en ellos. Para obtener más información sobre manipular mediante programación BLOB, vea [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md) .

##<a name="access-blob-containers-in-code"></a>Contenedores de blobs de acceso en el código

Para obtener acceso a BLOB en proyectos de 5 ASP.NET, debe agregar los elementos siguientes, si ya no están presentes.

1. Agregue las siguientes declaraciones de espacio de nombres de código a la parte superior de cualquier archivo de C# en el que desea tener acceso mediante programación almacenamiento de Azure.

        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;

2. Obtenga un objeto **CloudStorageAccount** que representa la información de su cuenta de almacenamiento. Utilice el siguiente código para obtener la su cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **Nota:** Utilizar todo el código anterior delante del código en las secciones siguientes.


3. Use un objeto **CloudBlobClient** para obtener una referencia de **CloudBlobContainer** a un contenedor existente en su cuenta de almacenamiento.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##<a name="create-a-container-in-code"></a>Crear un contenedor en código

También puede usar el **CloudBlobClient** para crear un contenedor en su cuenta de almacenamiento. Todo lo que debe hacer es agregar una llamada a **CreateIfNotExistsAsync** como en el siguiente código:

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**Nota:** Las API que realizan llamadas a almacenamiento de Azure en 5 ASP.NET son asincrónicas. Para obtener más información, vea [programación asincrónico con asincrónico y espera](http://msdn.microsoft.com/library/hh191443.aspx) . El código siguiente asume que se utilizan métodos de programación asincrónica.

Para que los archivos dentro del contenedor esté disponible para todos los usuarios, puede establecer el contenedor para ser público mediante el siguiente código.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##<a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Para cargar un archivo blob en un contenedor, obtener una referencia de contenedor y usarlo para obtener una referencia de blobs de Windows. Una vez una referencia de blobs de Windows, puede cargar cualquier secuencia de datos a él llamando al método **UploadFromStreamAsync** . Esta operación crea el blob si no está allí, o lo sobrescribe si existe. En el ejemplo siguiente se muestra cómo cargar un blob en un contenedor y se supone que ya se ha creado el contenedor.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##<a name="list-the-blobs-in-a-container"></a>El BLOB en un contenedor de lista
Para mostrar los BLOB en un contenedor, obtener una referencia de contenedor. A continuación, puede llamar método de **ListBlobsSegmentedAsync** del contenedor para recuperar los BLOB o directorios dentro de ella. Para obtener acceso amplio conjunto de propiedades y métodos para un devuelto **IListBlobItem**, debe convertirlo en un objeto **CloudBlockBlob**, **CloudPageBlob**o **CloudBlobDirectory** . Si no conoce el tipo de blob, puede usar una comprobación de tipo para determinar qué convertirlo a. El código siguiente muestra cómo recuperar y el URI de cada elemento en un contenedor de salida.

    BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
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
    } while (token != null);

Existen de otras formas de mostrar el contenido de un contenedor de blob. Para obtener más información, vea [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) .

##<a name="download-a-blob"></a>Descargar un blob
Para descargar un blob, obtener una referencia al objeto binario y, a continuación, llame al método de **DownloadToStreamAsync** . En el ejemplo siguiente se utiliza el método **DownloadToStreamAsync** para transferir el contenido de blobs de Windows a un objeto de secuencia, a continuación, puede guardar como un archivo local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Hay otras maneras de guardar BLOB como archivos. Para obtener más información, vea [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md#download-blobs) .

##<a name="delete-a-blob"></a>Eliminar un blob
Para eliminar un objeto binario, obtener una referencia al objeto binario y, a continuación, llame al método de **DeleteAsync** en él.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
