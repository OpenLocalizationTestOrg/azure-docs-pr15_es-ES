<properties
    pageTitle="Cómo usar el almacenamiento de archivos de Java | Microsoft Azure"
    description="Obtenga información sobre cómo usar el servicio de Azure archivo para cargar, descargar, lista y eliminar archivos. Ejemplos escritos en Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-file-storage-from-java"></a>Cómo usar el almacenamiento de archivos de Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Información general

En esta guía aprenderá cómo llevar a cabo operaciones básicas en el servicio de almacenamiento de archivos de Microsoft Azure. A través de ejemplos escritos en Java aprenderá a crear recursos compartidos y directorios, cargar, de lista y eliminar archivos. Si es nuevo en el servicio de almacenamiento de archivos de Microsoft Azure, atravesando los conceptos en las secciones siguientes será muy útil entender los ejemplos.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Crear una aplicación de Java

Para generar los ejemplos, necesitará el Kit de desarrollo de Java (JDK) y el [[SDK de Azure almacenamiento de Java]]. También debe crear una cuenta de almacenamiento de Azure.

## <a name="setup-your-application-to-use-file-storage"></a>Configurar la aplicación para utilizar el almacenamiento de archivos

Para usar el almacenamiento de Azure API, agregue la instrucción siguiente a la parte superior del archivo Java donde vaya a obtener acceso al servicio de almacenamiento de.

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.file.*;

## <a name="setup-an-azure-storage-connection-string"></a>Configuración de una cadena de conexión de almacenamiento de Azure

Para usar el almacenamiento de archivos, debe conectarse a su cuenta de almacenamiento de Azure. Sería el primer paso configurar una cadena de conexión que se usará para conectarse a su cuenta de almacenamiento. Vamos a definir una variable estática para hacerlo.

    // Configure the connection-string with your values
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account_name;" +
        "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Reemplazar your_storage_account_name y your_storage_account_key con los valores reales de la cuenta de almacenamiento.

## <a name="connecting-to-an-azure-storage-account"></a>Conectarse a una cuenta de almacenamiento de Azure

Para conectarse a su cuenta de almacenamiento, debe usar el objeto **CloudStorageAccount** , pasando una cadena de conexión a su método de **analizar** .

    // Use the CloudStorageAccount object to connect to your storage account
    try {
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
    } catch (InvalidKeyException invalidKey) {
        // Handle the exception
    }

**CloudStorageAccount.parse** produce una InvalidKeyException, así que deberá colocarlo dentro de un bloque try o capturas.

## <a name="how-to-create-a-share"></a>Cómo: crear un recurso compartido

Todos los archivos y directorios de almacenamiento de archivos residen en un contenedor de un **recurso compartido**. Su cuenta de almacenamiento puede tener tantos recursos compartidos como permite la capacidad de su cuenta. Para obtener acceso a un recurso compartido y su contenido, debe usar a un cliente de almacenamiento de archivos.

    // Create the file storage client.
    CloudFileClient fileClient = storageAccount.createCloudFileClient();

Usa al cliente de almacenamiento de archivos, puede obtener una referencia a un recurso compartido.

    // Get a reference to the file share
    CloudFileShare share = fileClient.getShareReference("sampleshare");

Para crear el recurso compartido, utilice el método **createIfNotExists** del objeto CloudFileShare.

    if (share.createIfNotExists()) {
        System.out.println("New share created");
    }

En este momento, **Compartir** contiene una referencia a un recurso compartido denominado **sampleshare**.

## <a name="how-to-upload-a-file"></a>Cómo: cargar un archivo

Una cuota de almacenamiento de Azure archivo contiene al menos, un directorio raíz donde pueden residen los archivos. En esta sección, aprenderá cómo cargar un archivo de almacenamiento local en el directorio raíz de un recurso compartido.

Es el primer paso para cargar un archivo obtener una referencia al directorio donde debe residen. Para ello, llamando al método **getRootDirectoryReference** del objeto de compartir.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

Ahora que ya tiene una referencia al directorio raíz del recurso compartido, puede cargar un archivo en él con el siguiente código.

    // Define the path to a local file.
    final String filePath = "C:\\temp\\Readme.txt";

    CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
    cloudFile.uploadFromFile(filePath);

## <a name="how-to-create-a-directory"></a>Cómo: crear un directorio

También puede organizar el almacenamiento colocando archivos de subdirectorios en lugar de tener todos ellos en el directorio raíz. El servicio de almacenamiento de archivos de Azure le permite crear tantos directorios que le permitirá a su cuenta. El código siguiente creará un directorio subcarpetas denominado **sampledir** en el directorio raíz.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the sampledir directory
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    if (sampleDir.createIfNotExists()) {
        System.out.println("sampledir created");
    } else {
        System.out.println("sampledir already exists");
    }

## <a name="how-to-list-files-and-directories-in-a-share"></a>Cómo: lista de archivos y directorios de un recurso compartido

Obtener una lista de archivos y directorios dentro de un recurso compartido se realiza fácilmente mediante una llamada **listFilesAndDirectories** en una referencia de CloudFileDirectory. El método devuelve una lista de objetos de ListFileItem que puede trabajemos en. Por ejemplo, el siguiente código mostrará archivos y directorios dentro del directorio raíz.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
        System.out.println(fileItem.getUri());
    }


## <a name="how-to-download-a-file"></a>Cómo: descargar un archivo

Una de las operaciones más frecuentes que realizará contra el almacenamiento de archivos es descargar los archivos. En el ejemplo siguiente, el código de descarga SampleFile.txt y muestra su contenido.

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the directory that contains the file
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    //Get a reference to the file you want to download
    CloudFile file = sampleDir.getFileReference("SampleFile.txt");

    //Write the contents of the file to the console.
    System.out.println(file.downloadText());

## <a name="how-to-delete-a-file"></a>Cómo: eliminar un archivo

Otra operación de almacenamiento de archivo común es la eliminación de archivos. El siguiente código elimina un archivo llamado SampleFile.txt almacenado dentro de un directorio denominado **sampledir**.


    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory where the file to be deleted is in
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    String filename = "SampleFile.txt"
    CloudFile file;

    file = containerDir.getFileReference(filename)
    if ( file.deleteIfExists() ) {
        System.out.println(filename + " was deleted");
    }


## <a name="how-to-delete-a-directory"></a>Cómo: eliminar un directorio

Eliminar un directorio es una tarea bastante sencilla, aunque se debe tener en cuenta que no se puede eliminar un directorio que contenga archivos o en otros directorios.

    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory you want to delete
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    // Delete the directory
    if ( containerDir.deleteIfExists() ) {
        System.out.println("Directory deleted");
    }


## <a name="how-to-delete-a-share"></a>Cómo: eliminar un recurso compartido

Eliminar un recurso compartido se realiza llamando al método de **deleteIfExists** en un objeto CloudFileShare. Aquí es el código de ejemplo que lo hace.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the file client.
       CloudFileClient fileClient = storageAccount.createCloudFileClient();

       // Get a reference to the file share
       CloudFileShare share = fileClient.getShareReference("sampleshare");

       if (share.deleteIfExists()) {
           System.out.println("sampleshare deleted");
       }
    } catch (Exception e) {
        e.printStackTrace();
    }

## <a name="next-steps"></a>Pasos siguientes

Si desea obtener más información sobre otra API de Azure almacenamiento, siga estos vínculos.

- [Centro para desarrolladores de Java](http://azure.microsoft.com/develop/java/)
- [Almacenamiento de Azure SDK de Java](https://github.com/azure/azure-storage-java)
- [Almacenamiento de Azure SDK para Android](https://github.com/azure/azure-storage-android)
- [Referencia SDK de cliente de almacenamiento de Azure](http://dl.windowsazure.com/storage/javadoc/)
- [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [Transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md)
