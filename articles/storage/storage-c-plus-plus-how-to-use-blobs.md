<properties
    pageTitle="Cómo usar el almacenamiento de blobs (almacenamiento de objeto) desde C++ | Microsoft Azure"
    description="Almacenar datos estructurados en la nube con el almacenamiento de blobs de Windows Azure (almacenamiento de objetos)."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-blob-storage-from-c"></a>Cómo usar el almacenamiento de blobs de C++  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

Almacenamiento de blobs de Windows Azure es un servicio que almacena datos estructurados en la nube como objetos o BLOB. Almacenamiento de blobs de Windows puede almacenar cualquier tipo de texto o datos binarios, como un documento, el archivo multimedia o el instalador de la aplicación. Almacenamiento de blobs también se conoce como almacenamiento de objetos.

Esta guía le muestran cómo realizar escenarios comunes mediante el servicio de almacenamiento de blobs de Windows Azure. Los ejemplos están escritos en C++ y usar la [Biblioteca de cliente de almacenamiento de Azure para C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Los escenarios cubiertos incluyen **Cargando**, **listado**, **Descargar**y **Eliminar** BLOB.  

>[AZURE.NOTE] Esta guía está destinado a la biblioteca de cliente de almacenamiento de Azure para C++ versión 1.0.0 y superiores. La versión recomendada es biblioteca de cliente de almacenamiento 2.2.0, que está disponible a través de [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Crear una aplicación de C++
En esta guía, que usa las funciones de almacenamiento que se pueden ejecutar en una aplicación de C++.  

Para ello, debe instalar la biblioteca de cliente de almacenamiento de Azure para C++ y cree una cuenta de almacenamiento de Azure en la suscripción de Azure.   

Para instalar la biblioteca de cliente de almacenamiento de Azure para C++, puede usar los métodos siguientes:

-   **Linux:** Siga las instrucciones proporcionadas en la página de la [Biblioteca de cliente de almacenamiento de Azure para C++ Léame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows:** En Visual Studio, haga clic en **Herramientas > Administrador de paquetes de NuGet > consola del Administrador de paquete**. Escriba el comando siguiente en la [consola del Administrador de paquetes de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) y presione **ENTRAR**.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Configurar la aplicación para tener acceso al almacenamiento de blobs  
Agregue que las siguientes instrucciones a la parte superior del archivo C++ donde desea usar el almacenamiento de Azure API para tener acceso a BLOB include:  

    #include "was/storage_account.h"
    #include "was/blob.h"

## <a name="setup-an-azure-storage-connection-string"></a>Configuración de una cadena de conexión de almacenamiento de Azure
Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar los extremos y las credenciales para acceder a los servicios de administración de datos. Cuando se ejecuta en una aplicación de cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, con el nombre de su cuenta de almacenamiento y la tecla de acceso de almacenamiento de la cuenta de almacenamiento aparece en el [Portal de Azure](https://portal.azure.com) para los valores de *nombre de la cuenta* y *AccountKey* . Para obtener información sobre las cuentas de almacenamiento y las teclas de acceso, vea [Acerca de cuentas de almacenamiento de Azure](storage-create-storage-account.md). Este ejemplo muestra cómo puede declarar un campo estático para almacenar la cadena de conexión:  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para probar la aplicación en el equipo local de Windows, puede usar Microsoft Azure [emulador de almacenamiento](storage-use-emulator.md) que se instala con el [SDK de Azure](https://azure.microsoft.com/downloads/). El emulador de almacenamiento es una herramienta que simula los servicios de blobs, cola y tabla disponibles en Azure en el equipo de desarrollo local. En el ejemplo siguiente se muestra cómo puede declarar un campo estático para almacenar la cadena de conexión para su emulador almacenamiento local:

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar el emulador de almacenamiento de Azure, seleccione el botón **Inicio** o presione la tecla de **Windows** . Empiece a escribir **Emulador de almacenamiento de Azure**y seleccione **Emulador de Microsoft Azure almacenamiento** de la lista de aplicaciones.  

Los ejemplos siguientes, se supone que ha utilizado uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.  

## <a name="retrieve-your-connection-string"></a>Recuperar la cadena de conexión
Puede usar la clase **cloud_storage_account** para representar la información de su cuenta de almacenamiento. Para recuperar información de su cuenta de almacenamiento de la cadena de conexión de almacenamiento, puede usar el método **analizar** .  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

A continuación, obtener una referencia a una clase **cloud_blob_client** como le permite recuperar objetos que representan los contenedores y BLOB almacenado en el servicio de almacenamiento de blobs de Windows. El código siguiente crea un objeto de **cloud_blob_client** mediante el objeto de la cuenta de almacenamiento que se recuperan anteriormente:  

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## <a name="how-to-create-a-container"></a>Cómo: crear un contenedor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Este ejemplo muestra cómo crear un contenedor si aún no existe:  

    try
    {
        // Retrieve storage account from connection string.
        azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

        // Create the blob client.
        azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

        // Retrieve a reference to a container.
        azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

        // Create the container if it doesn't already exist.
        container.create_if_not_exists();
    }
    catch (const std::exception& e)
    {
        std::wcout << U("Error: ") << e.what() << std::endl;
    }  

De forma predeterminada, el nuevo contenedor es privado y debe especificar la clave de acceso de almacenamiento para descargar BLOB de este contenedor. Si desea que los archivos (BLOB) dentro del contenedor disponible para todos los usuarios, puede establecer el contenedor sea público con el siguiente código:  

    // Make the blob container publicly accessible.
    azure::storage::blob_container_permissions permissions;
    permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
    container.upload_permissions(permissions);  

Cualquier usuario de Internet puede ver BLOB en un contenedor público, pero se puede modificar o eliminar solo si tiene la clave de acceso adecuado.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Cómo: cargar un blob en un contenedor
Almacenamiento de blobs de Windows Azure es compatible con BLOB bloque y blobs de página. En la mayoría de los casos, blobs de bloque es el tipo recomendado para usar.  

Para cargar un archivo a un blob bloque, obtener una referencia de contenedor y usarlo para obtener una referencia de blobs de bloque. Una vez que tenga una referencia de blobs de Windows, puede cargar cualquier secuencia de datos a él llamando al método **upload_from_stream** . Esta operación creará el blob si no existe previamente o sobrescribir si existe. En el ejemplo siguiente se muestra cómo cargar un blob en un contenedor y se supone que ya se ha creado el contenedor.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Create or overwrite the "my-blob-1" blob with contents from a local file.
    concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
    blockBlob.upload_from_stream(input_stream);
    input_stream.close().wait();

    // Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
    // Retrieve a reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
    blob2.upload_text(U("more text"));

    // Retrieve a reference to a blob named "my-blob-3".
    azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
    blob3.upload_text(U("other text"));  

Como alternativa, puede usar el método **upload_from_file** para cargar un archivo a un blob de bloque.

## <a name="how-to-list-the-blobs-in-a-container"></a>Cómo: el BLOB en un contenedor de lista
Para mostrar los BLOB en un contenedor, obtener una referencia de contenedor. A continuación, puede usar el método de **list_blobs** del contenedor para recuperar los BLOB o directorios dentro de ella. Para obtener acceso amplio conjunto de métodos y propiedades para un devuelto **list_blob_item**, debe llamar al método **list_blob_item.as_blob** para obtener un objeto **cloud_blob** o el método **list_blob.as_directory** para obtener un objeto cloud_blob_directory. El código siguiente muestra cómo recuperar y salida URI de cada elemento en el contenedor de **Mi contenedor de ejemplo** :

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Output URI of each item.
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
        }
        else
        {
            std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
    }

Para obtener más detalles acerca de cómo enumerar las operaciones, consulte la [Lista de recursos de almacenamiento de Azure en C++](storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Cómo: Descargar BLOB
Para descargar BLOB, primero recuperar una referencia de blobs de Windows y, a continuación, llame al método **download_to_stream** . En el ejemplo siguiente se utiliza el método **download_to_stream** para transferir el contenido de blobs de Windows a un objeto de secuencia, a continuación, puede conservar en un archivo local.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Save blob contents to a file.
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    blockBlob.download_to_stream(output_stream);

    std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();

    outfile.write((char *)&data[0], buffer.size());
    outfile.close();  

Como alternativa, puede usar el método **download_to_file** para descargar el contenido de un blob en un archivo.
Además, también puede usar el método **download_text** para descargar el contenido de un blob como una cadena de texto.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

    // Download the contents of a blog as a text string.
    utility::string_t text = text_blob.download_text();

## <a name="how-to-delete-blobs"></a>Cómo: eliminar BLOB
Para eliminar un objeto binario, obtener una referencia de blobs de Windows y, a continuación, llame al método **delete_blob** en él.  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Delete the blob.
    blockBlob.delete_blob();

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido los conceptos básicos de almacenamiento de blobs de Windows, siga estos vínculos para obtener más información sobre el almacenamiento de Azure.  

-   [Cómo usar el almacenamiento de la cola de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Cómo usar el almacenamiento de tablas desde C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Lista de recursos de almacenamiento de Azure en C++](storage-c-plus-plus-enumeration.md)
-   [Biblioteca de cliente de almacenamiento para referencia de C++](http://azure.github.io/azure-storage-cpp)
-   [Documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Transferir datos con la utilidad de línea de comandos de AzCopy](storage-use-azcopy.md)
