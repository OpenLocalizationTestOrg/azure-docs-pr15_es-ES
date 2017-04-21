<properties
    pageTitle="Cómo usar el almacenamiento de blobs (almacenamiento de objetos) de rubí | Microsoft Azure"
    description="Almacenar datos estructurados en la nube con el almacenamiento de blobs de Windows Azure (almacenamiento de objetos)."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-blob-storage-from-ruby"></a>Cómo usar el almacenamiento de blobs de rubí

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

Almacenamiento de blobs de Windows Azure es un servicio que almacena datos estructurados en la nube como objetos o BLOB. Almacenamiento de blobs de Windows puede almacenar cualquier tipo de texto o datos binarios, como un documento, el archivo multimedia o el instalador de la aplicación. Almacenamiento de blobs también se conoce como almacenamiento de objetos.

Esta guía le mostrará cómo llevar a cabo con el almacenamiento de blobs de escenarios comunes. Los ejemplos se escriben mediante la API de Ruby. Los escenarios cubiertos incluyen BLOB **cargando, listado, descargar** y **Eliminar** .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Crear una aplicación de ideogramas y transcripción fonética

Crear una aplicación Ruby. Para obtener instrucciones, consulte [Ruby en la aplicación Web de guías en una máquina virtual de Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)

## <a name="configure-your-application-to-access-storage"></a>Configurar la aplicación para tener acceso al almacenamiento

Para usar el almacenamiento de Azure, debe descargar y usar el paquete de azure ideogramas y transcripción fonética, que incluye un conjunto de bibliotecas de comodidad que comunicarse con los servicios del resto de almacenamiento.

### <a name="use-rubygems-to-obtain-the-package"></a>Usar RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba "azure de instalación de indicador" en la ventana de comandos para instalar el indicador y dependencias.

### <a name="import-the-package"></a>Importar el paquete

Usar el editor de texto, agregue lo siguiente a la parte superior del archivo Ruby donde vaya a usar el almacenamiento:

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>Configurar una conexión de almacenamiento de Azure

El módulo azure leerá las variables de entorno **AZURE\_almacenamiento\_cuenta** y **AZURE\_almacenamiento\_ACCESS_KEY** información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se establecen estas variables de entorno, debe especificar la información de cuenta antes de utilizar **Azure::Blob::BlobService** con el siguiente código:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"


Para obtener los valores de un clásico o cuenta de almacenamiento de administrador de recursos en el portal de Azure:

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
2. Vaya a la cuenta de almacenamiento que desee usar.
3. En el módulo de configuración de la derecha, haga clic en **Las teclas de acceso**.
4. En el módulo de teclas de acceso que aparece, verá la tecla de acceso 1 y 2 de tecla de acceso. Puede usar cualquiera de estos. 
5. Haga clic en el icono de copiar para copiar la clave en el Portapapeles. 

Para obtener los valores de una cuenta de almacenamiento clásico en el portal de Azure clásica:

1. Inicie sesión en el [portal de Azure clásico](https://manage.windowsazure.com).
2. Vaya a la cuenta de almacenamiento que desee usar.
3. Haga clic en **Administrar las teclas de acceso** en la parte inferior del panel de navegación.
4. En el cuadro de diálogo emergente, verá el nombre de la cuenta de almacenamiento, la tecla de acceso principal y la clave de acceso secundario. De tecla de acceso, puede usar principal o el elemento secundario. 
5. Haga clic en el icono de copiar para copiar la clave en el Portapapeles.

## <a name="create-a-container"></a>Crear un contenedor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

El objeto **Azure::Blob::BlobService** le permite trabajar con contenedores y BLOB. Para crear un contenedor, utilice la **crear\_container()** método.

En el ejemplo siguiente se crea un contenedor o imprimir el error si hay alguna.

    azure_blob_service = Azure::Blob::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

Si desea que los archivos en el contenedor público, puede establecer permisos del contenedor.

Solo puede modificar la <strong>crear\_container()</strong> llamada al pasar el **: público\_access\_nivel** opción:

    container = azure_blob_service.create_container("test-container",
      :public_access_level => "<public access level>")


Los valores válidos para la **: público\_access\_nivel** opción son:

* **blobs:** Especifica el acceso de lectura público completa para datos contenedor y blob. Los clientes pueden enumerar BLOB dentro del contenedor a través de la solicitud anónima, pero no pueden enumerar los contenedores dentro de la cuenta de almacenamiento.

* **contenedor:** Especifica el público acceso de lectura para blobs. Pueden leer los datos de BLOB dentro de este contenedor a través de la solicitud anónima, pero los datos de contenedor no están disponibles. Los clientes no pueden enumerar BLOB dentro del contenedor a través de la solicitud anónima.

Como alternativa, puede modificar el nivel de acceso del público de un contenedor mediante **establecer\_contenedor\_acl()** método para especificar el nivel de acceso del público.

En el ejemplo siguiente se cambia el nivel de acceso del público al **contenedor**:

    azure_blob_service.set_container_acl('test-container', "container")

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Para cargar el contenido a un blob, use la **crear\_bloque\_blob()** método para crear el blob, se debe usar un archivo o una cadena como el contenido del objeto binario.

El código siguiente carga el archivo **test.png** como un blob nuevo llamado "imagen-blob" en el contenedor.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <a name="list-the-blobs-in-a-container"></a>El BLOB en un contenedor de lista

Para mostrar los contenedores, use el método de **list_containers()** .
Para mostrar los BLOB dentro de un contenedor, utilice **lista\_blobs()** método.

Esto muestra las direcciones URL de todos los BLOB en todos los contenedores de la cuenta.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <a name="download-blobs"></a>Descargar BLOB

Para descargar BLOB, use la **obtener\_blob()** método para recuperar el contenido.

En el ejemplo siguiente se muestra cómo utilizar **obtener\_blob()** para descargar el contenido de "imagen blob" y escribir en un archivo local.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <a name="delete-a-blob"></a>Eliminar un Blob
Por último, para eliminar un objeto binario, use la **Eliminar\_blob()** método. En el ejemplo siguiente se muestra cómo eliminar un blob.

    azure_blob_service.delete_blob(container.name, "image-blob")

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre las tareas más complejas de almacenamiento, siga estos vínculos:

- [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Repositorio de [Azure SDK de rubí](https://github.com/WindowsAzure/azure-sdk-for-ruby) en GitHub
- [Transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md)
