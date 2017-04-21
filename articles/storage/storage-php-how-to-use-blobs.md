<properties
    pageTitle="Cómo usar el almacenamiento de blobs (almacenamiento de objetos) de PHP | Microsoft Azure"
    description="Almacenar datos estructurados en la nube con el almacenamiento de blobs de Windows Azure (almacenamiento de objetos)."
    documentationCenter="php"
    services="storage"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-blob-storage-from-php"></a>Cómo usar el almacenamiento de blobs de PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

Almacenamiento de blobs de Windows Azure es un servicio que almacena datos estructurados en la nube como objetos o BLOB. Almacenamiento de blobs de Windows puede almacenar cualquier tipo de texto o datos binarios, como un documento, el archivo multimedia o el instalador de la aplicación. Almacenamiento de blobs también se conoce como almacenamiento de objetos.

Esta guía le muestra cómo llevar a cabo escenarios comunes mediante el servicio de blobs de Windows Azure. Los ejemplos se escriben en PHP y usar el [SDK de Azure para PHP] [download]. Los escenarios cubiertos incluyen **Cargando**, **listado**, **Descargar**y **Eliminar** BLOB. Para obtener más información sobre BLOB, vea la sección [pasos siguientes](#next-steps) .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Crear una aplicación PHP

El único requisito para crear una aplicación de PHP que tiene acceso al servicio de blobs de Windows Azure es la referencia de clases en el SDK de Azure para PHP desde dentro del código. Puede usar las herramientas de desarrollo para crear la aplicación, incluyendo el Bloc de notas.

En esta guía, utilice las características de servicio, que se pueden llamar dentro de una aplicación PHP localmente o en el código que se ejecuta dentro de una función de Azure web, trabajo o sitio Web.

## <a name="get-the-azure-client-libraries"></a>Obtener las bibliotecas de cliente de Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-blob-service"></a>Configurar la aplicación para tener acceso al servicio de blobs

Para usar las API del servicio de blobs de Windows Azure, debe:

1. Hacer referencia al archivo cargador automático mediante la instrucción [require_once] , y
2. Hacer referencia a las clases que puede usar.

En el ejemplo siguiente se muestra cómo incluir el archivo de cargador automático y hacer referencia a la clase **ServicesBuilder** .

> [AZURE.NOTE] En este ejemplo (y otros ejemplos de este artículo) se suponen que ha instalado las bibliotecas de cliente de PHP para Azure a través de compositor. Si ha instalado las bibliotecas de forma manual, debe hacer referencia a la `WindowsAzure.php` archivo cargador automático.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


En los ejemplos siguientes, el `require_once` instrucción siempre se muestran, pero solo las clases necesarias para ejecutar el ejemplo se hace referencia.

## <a name="set-up-an-azure-storage-connection"></a>Configurar una conexión de almacenamiento de Azure

Para crear una instancia de un cliente de servicios de blobs de Windows Azure, primero debe tener una cadena de conexión válida. El formato de la cadena de conexión de servicio de blob es:

Para obtener acceso a un servicio live:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para obtener acceso a la emulador de almacenamiento:

    UseDevelopmentStorage=true


Para crear a un cliente de servicio de Azure, debe usar la clase **ServicesBuilder** . Puedes:

* Pase la cadena de conexión directamente a él o
* Utilice la **CloudConfigurationManager (CCM)** para comprobar varios orígenes externos de la cadena de conexión:
    * De forma predeterminada, incluye compatibilidad para un origen externo - variables de entorno.
    * Puede agregar nuevos orígenes de ampliar la clase **ConnectionStringSource** .

Para los ejemplos descritos a continuación, se pasará directamente la cadena de conexión.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <a name="create-a-container"></a>Crear un contenedor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Un objeto **BlobRestProxy** le permite crear un contenedor de blobs con el método **createContainer** . Al crear un contenedor, puede establecer opciones en el contenedor, pero ello no es necesario. (En el ejemplo siguiente muestra cómo configurar la lista de control de acceso (ACL) de contenedor y los metadatos de contenedor).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
    use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    // OPTIONAL: Set public access policy and metadata.
    // Create container options object.
    $createContainerOptions = new CreateContainerOptions();

    // Set public access policy. Possible values are
    // PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
    // CONTAINER_AND_BLOBS:
    // Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous
    // request, but cannot enumerate containers within the storage account.
    //
    // BLOBS_ONLY:
    // Specifies public read access for blobs. Blob data within this
    // container can be read via anonymous request, but container data is not
    // available. proxys cannot enumerate blobs within the container via
    // anonymous request.
    // If this value is not specified in the request, container data is
    // private to the account owner.
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Llamar a **setPublicAccess (PublicAccessType::CONTAINER\_y\_BLOB)** hace que los datos de contenedor y blob accesible a través de solicitudes anónimas. Llamar a **setPublicAccess(PublicAccessType::BLOBS_ONLY)** asegura que solo los datos blob accesible a través de solicitudes anónimas. Para obtener más información sobre el contenedor ACL, vea [establecer contenedor ACL (API de REST)][container-acl].

Para obtener más información sobre códigos de error del servicio de blobs de Windows, vea [Códigos de Error del servicio de Blob][error-codes].

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Para cargar un archivo como un blob, use el método **BlobRestProxy -> createBlockBlob** . Esta operación crea el blob si no existe, o se sobrescribe en caso afirmativo. El ejemplo de código siguiente supone que el contenedor ya se ha creado y utiliza [fopen] [ fopen] para abrir el archivo como una secuencia.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Tenga en cuenta que el ejemplo anterior carga un blob como una secuencia. No obstante, un blob también se pueden cargar como una cadena mediante, por ejemplo, el [archivo\_obtener\_contenido] [ file_get_contents] función. Para hacer esto en el ejemplo anterior, cambiar `$content = fopen("c:\myfile.txt", "r");` a `$content = file_get_contents("c:\myfile.txt");`.

## <a name="list-the-blobs-in-a-container"></a>El BLOB en un contenedor de lista

Para mostrar los BLOB en un contenedor, utilice el método **BlobRestProxy -> listBlobs** con **foreach** bucle para recorrer el resultado. El código siguiente muestra el nombre de cada blob como resultado en un contenedor y muestra el URI correspondiente en el explorador.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // List blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();

        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="download-a-blob"></a>Descargar un blob

Para descargar un blob, llame al método **BlobRestProxy -> getBlob** , a continuación, llame al método **getContentStream** en el objeto **GetBlobResult** resultante.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Tenga en cuenta que el ejemplo anterior obtiene un blob como un recurso de secuencia (el comportamiento predeterminado). Sin embargo, puede usar el [secuencia\_obtener\_contenido] [ stream-get-contents] función para convertir la secuencia devuelta en una cadena.

## <a name="delete-a-blob"></a>Eliminar un blob

Para eliminar un objeto binario, pase el nombre de contenedor y blob a **BlobRestProxy -> deleteBlob**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete blob.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-a-blob-container"></a>Eliminar un contenedor de blob

Por último, para eliminar un contenedor de blob, pase el nombre del contenedor para **BlobRestProxy -> deleteContainer**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos del servicio de blobs de Windows Azure, siga estos vínculos para obtener información sobre las tareas de almacenamiento más complejas.

- Visite el [blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Vea el [ejemplo de blobs de bloque PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php).
- Vea el [ejemplo de blobs de página PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php).
- [Transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md)
 
Para obtener más información, vea también el [Centro para desarrolladores de PHP](/develop/php/).


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
