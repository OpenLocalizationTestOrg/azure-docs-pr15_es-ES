<properties
    pageTitle="Con la CLI Azure almacenamiento de Azure | Microsoft Azure"
    description="Aprenda a usar la interfaz de línea de comandos de Azure (Azure CLI) con el almacenamiento de Azure para crear y administrar cuentas de almacenamiento y trabajar con archivos y blobs de Azure. CLI Azure es una herramienta de varias plataformas "
    services="storage"
    documentationCenter="na"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="using-the-azure-cli-with-azure-storage"></a>Uso de la CLI Azure con almacenamiento de Azure

## <a name="overview"></a>Información general

CLI Azure proporciona un conjunto de código abierto, entre plataformas comandos para trabajar con la plataforma de Azure. Proporciona la mayor parte de la misma funcionalidad que se encuentra en el [portal de Azure](https://portal.azure.com) , así como la funcionalidad de acceso a datos enriquecidos.

En esta guía, veremos cómo usar [Azure de línea de comandos interfaz Azure](../xplat-cli-install.md) para realizar una gran variedad de tareas de administración y desarrollo con el almacenamiento de Azure. Se recomienda descargar e instalar o actualizar a la última CLI Azure antes de utilizar a esta guía.

Esta guía asume que entender los conceptos básicos de almacenamiento de Azure. La guía proporciona un número de secuencias de comandos para demostrar el uso de la CLI Azure con el almacenamiento de Azure. No olvide actualizar las variables de secuencia de comandos en función de su configuración antes de ejecutar cada secuencia de comandos.

> [AZURE.NOTE] La guía proporciona ejemplos de comando y script de CLI de Azure para cuentas de almacenamiento clásico. Vea [mediante la CLI de Azure para Mac, Linux y Windows con la administración de recursos de Azure](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) para Azure CLI comandos para cuentas de almacenamiento de administrador de recursos.

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Empezar a usar el almacenamiento de Azure y Azure CLI en 5 minutos

Esta guía utiliza Ubuntu para obtener ejemplos, pero otras plataformas de sistema operativo deben realizar de forma similar.

**Nuevo en Azure:** Obtener una suscripción de Microsoft Azure y una cuenta de Microsoft asociada con la suscripción. Para obtener información sobre las opciones de compra de Azure, vea [Prueba gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/)y [Miembro ofrece](https://azure.microsoft.com/pricing/member-offers/) (para los miembros de MSDN, Microsoft Partner Network, BizSpark y otros programas de Microsoft).

Para obtener más información acerca de las suscripciones de Azure, vea [asignación de roles de administrador de Azure Active Directory (AD Azure)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Después de crear una cuenta y la suscripción de Microsoft Azure:**

1. Descargar e instalar la CLI de Azure siguiendo las instrucciones descritas en [instalar CLI Azure](../xplat-cli-install.md).
2. Una vez que se ha instalado la CLI de Azure, podrá utilizar el comando azure de la interfaz de línea de comandos (fiesta, Terminal, símbolo del sistema) para obtener acceso a los comandos de CLI de Azure. Tipo de `azure` comando y debe ver el resultado siguiente.

    ![Resultado del comando de Azure][Image1]

3. En la interfaz de línea de comandos, escriba `azure storage` a la lista de todos los comandos de almacenamiento de azure y obtener una primera impresión de las funcionalidades de Azure CLI proporciona. Puede escribir el nombre del comando con el parámetro **-h** (por ejemplo, `azure storage share create -h`) para ver los detalles de la sintaxis de comando.
4. Ahora, le ofrecemos una secuencia de comandos simple que muestra los comandos básicos de Azure CLI para tener acceso al almacenamiento de Azure. La secuencia de comandos primero le pedirá que establezca dos variables para su cuenta de almacenamiento y la clave. A continuación, la secuencia de comandos creará un nuevo contenedor de esta nueva cuenta de almacenamiento y cargar un archivo de imagen existente (blob) en ese contenedor. Después de la secuencia de comandos enumera todos los blobs de ese contenedor, que descargue el archivo de imagen en el directorio de destino que existe en el equipo local.

        #!/bin/bash
        # A simple Azure storage example

        export AZURE_STORAGE_ACCOUNT=<storage_account_name>
        export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

        export container_name=<container_name>
        export blob_name=<blob_name>
        export image_to_upload=<image_to_upload>
        export destination_folder=<destination_folder>

        echo "Creating the container..."
        azure storage container create $container_name

        echo "Uploading the image..."
        azure storage blob upload $image_to_upload $container_name $blob_name

        echo "Listing the blobs..."
        azure storage blob list $container_name

        echo "Downloading the image..."
        azure storage blob download $container_name $blob_name $destination_folder

        echo "Done"

5. En el equipo local, abra el editor de texto preferido (vim por ejemplo). Escriba la secuencia de comandos anterior en el editor de texto.

6. Ahora, deberá actualizar las variables de secuencia de comandos en función de las opciones de configuración.

    - **< storage_account_name >** Use el nombre especificado en la secuencia de comandos o escriba un nombre nuevo para su cuenta de almacenamiento. **Importante:** El nombre de la cuenta de almacenamiento debe ser único en Azure. ¡Debe ser minúscula, demasiado!

    - **< storage_account_key >** Tecla de acceso de su cuenta de almacenamiento.

    - **< container_name >** Use el nombre especificado en la secuencia de comandos o escriba un nombre nuevo para el contenedor.

    - **< image_to_upload >** Escriba una ruta de acceso a una imagen en el equipo local, como por ejemplo: "~ / images/HelloWorld.png".

    - **< destination_folder >** Escriba una ruta de acceso a un directorio local para almacenar los archivos descargados desde el almacenamiento de Azure, como por ejemplo: "~/downloadImages".

7. Después de actualizar las variables necesarias en vim, presionar combinaciones de teclas "Esc,:, wq!" Para guardar la secuencia de comandos.

8. Para ejecutar esta secuencia de comandos, solo tiene que escribir el nombre de archivo de comandos en la consola de fiesta. Después de ejecuta esta secuencia de comandos, debe tener una carpeta de destino local que incluye el archivo de imagen descargado. La siguiente captura de pantalla muestra un ejemplo de salida:

Después de ejecuta la secuencia de comandos, debe tener una carpeta de destino local que incluye el archivo de imagen descargado.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Administrar cuentas de almacenamiento con la CLI de Azure

### <a name="connect-to-your-azure-subscription"></a>Conectarse a su suscripción de Azure

Mientras que la mayoría de los comandos de almacenamiento funcionará sin una suscripción de Azure, se recomienda conectarse a la suscripción desde la CLI de Azure. Para configurar la CLI de Azure al trabajar con su suscripción, siga los pasos en [Conectar con una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).

### <a name="create-a-new-storage-account"></a>Crear una nueva cuenta de almacenamiento

Para usar el almacenamiento de Azure, necesita una cuenta de almacenamiento. Puede crear una nueva cuenta de almacenamiento de Azure después de haber configurado el equipo para conectarse a su suscripción.

        azure storage account create <account_name>

El nombre de su cuenta de almacenamiento debe estar entre 3 y 24 caracteres de longitud y utilizar solo letras minúsculas y números.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Configurar una cuenta de Azure almacenamiento de forma predeterminada en variables de entorno

Puede tener varias cuentas de almacenamiento en su suscripción. Puede elegir uno de ellos y establecer en las variables de entorno para todos los comandos de almacenamiento en la misma sesión. Esto le permite ejecutar los comandos de almacenamiento de Azure CLI sin especificar la cuenta de almacenamiento y clave explícitamente.

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

Otra forma de configurar una cuenta de almacenamiento predeterminada está utilizando la cadena de conexión. En primer lugar para obtener la cadena de conexión, comando:

        azure storage account connectionstring show <account_name>

A continuación, copie la cadena de conexión de salida y establecer variable de entorno:

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## <a name="create-and-manage-blobs"></a>Crear y administrar BLOB

Almacenamiento de blobs de Windows Azure es un servicio para almacenar grandes cantidades de datos no estructurados, como texto o binario, que puede tener acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Esta sección se supone que ya está familiarizado con los conceptos de almacenamiento de blobs de Windows Azure. Para obtener información detallada, vea [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md) y [Conceptos de servicio de blobs de Windows](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Crear un contenedor

Cada blob en Azure almacenamiento debe estar en un contenedor. Puede crear un contenedor privada mediante la `azure storage container create` comando:

        azure storage container create mycontainer

> [AZURE.NOTE] Hay tres niveles de acceso de lectura anónimo: **desactivar**, **Blob**y el **contenedor**. Para evitar el acceso anónimo a BLOB, establezca el parámetro permiso en **desactivar**. De forma predeterminada, el nuevo contenedor es privado y se puede acceder sólo por el propietario de la cuenta. Para permitir el acceso de lectura público anónimo blob recursos, pero no a los metadatos de contenedor o a la lista de BLOB en el contenedor, establezca el parámetro de permiso a **Blob**. Para permitir el acceso de lectura público completa a recursos de blobs, metadatos de contenedor y la lista de BLOB en el contenedor, establezca el parámetro de permiso al **contenedor**. Para obtener más información, vea [administrar el acceso anónimo lectura contenedores y BLOB](storage-manage-access-to-resources.md).

### <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Almacenamiento de blobs de Windows Azure es compatible con BLOB bloque y blobs de página. Para obtener más información, vea [Descripción bloque BLOB, anexar Blobs y Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para cargar BLOB en un contenedor, puede usar el `azure storage blob upload`. De forma predeterminada, este comando cargará los archivos locales a un blob de bloque. Para especificar el tipo de blob, puede usar el `--blobtype` parámetro.

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### <a name="download-blobs-from-a-container"></a>Descargar BLOB desde un contenedor

En el ejemplo siguiente se muestra cómo descargar BLOB desde un contenedor.

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### <a name="copy-blobs"></a>Copiar BLOB

Puede copiar dentro o entre regiones y cuentas de almacenamiento de blobs de forma asincrónica.

En el ejemplo siguiente se muestra cómo copiar BLOB desde una cuenta de almacenamiento a otro. En este ejemplo, creamos un contenedor donde está públicamente, BLOB accesible de forma anónima.

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

En este ejemplo se realiza una copia asincrónica. Puede supervisar el estado de cada operación de copia, ejecute el `azure storage blob copy show` operación.

Tenga en cuenta que la dirección URL de origen proporcionada para la operación de copia debe ser accesibles públicamente o incluir un símbolo de SA (firma acceso compartido).

### <a name="delete-a-blob"></a>Eliminar un blob

Para eliminar un objeto binario, use el comando debajo de:

        azure storage blob delete mycontainer myBlockBlob2

## <a name="create-and-manage-file-shares"></a>Crear y administrar recursos compartidos de archivos

Almacenamiento de archivos Azure ofrece almacenamiento compartido de aplicaciones mediante el protocolo SMB estándar. Máquinas virtuales de Microsoft Azure y servicios en la nube, así como las aplicaciones locales, pueden compartir datos de archivos a través de recursos compartidos montados. Puede administrar recursos compartidos de archivos y los datos del archivo mediante la CLI de Azure. Para obtener más información sobre el almacenamiento de archivos de Azure, vea [Introducción a almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md) o [cómo usar el almacenamiento de archivos de Azure con Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Crear un archivo compartido

Un archivo de Azure es un recurso compartido de archivo SMB en Azure. Todos los directorios y archivos deben crearse en un archivo compartido. Una cuenta puede contener un número ilimitado de recursos compartidos y un recurso compartido puede almacenar una cantidad ilimitada de archivos hasta los límites de capacidad de la cuenta de almacenamiento. En el ejemplo siguiente se crea un archivo compartido denominado **recurso**.

        azure storage share create myshare

### <a name="create-a-directory"></a>Crear un directorio

Un directorio proporciona una estructura jerárquica opcional para un recurso compartido de archivos de Azure. En el ejemplo siguiente se crea un directorio denominado **Midir** en el recurso compartido de archivos.

        azure storage directory create myshare myDir

Nota la ruta del directorio puede incluir varios niveles, *por ejemplo*, **un / b**. Sin embargo, debe asegurarse de que todos los directorios principales existe. Por ejemplo, para la ruta de acceso **un / b**, debe crear directorio **un** primero y después crear directorio **b**.

### <a name="upload-a-local-file-to-directory"></a>Cargar un archivo local al directorio

En el ejemplo siguiente se carga un archivo de **~/temp/samplefile.txt** en el directorio **Midir** . Editar la ruta de acceso del archivo de modo que apunte a un archivo válido en el equipo local:

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

Tenga en cuenta que un archivo en el recurso compartido puede hasta 1 TB.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Lista de los archivos en la raíz de compartir o directorio

Puede mostrar los archivos y subdirectorios en una raíz de compartir o un directorio mediante el siguiente comando:

        azure storage file list myshare myDir

Tenga en cuenta que el nombre del directorio es opcional para la operación de entrada. Si se omite, el comando muestra el contenido del directorio raíz del recurso compartido.

### <a name="copy-files"></a>Copiar archivos

A partir de la versión 0.9.8 de Azure CLI, puede copiar un archivo en otro archivo, un archivo a un blob o un blob a un archivo. A continuación se explica cómo llevar a cabo estas operaciones de copia con los comandos CLI. Para copiar un archivo en el nuevo directorio:

    azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

Para copiar un blob en un directorio de archivo:

    azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

## <a name="next-steps"></a>Pasos siguientes

Estos son algunos artículos relacionados y recursos para obtener más información sobre el almacenamiento de Azure.

- [Documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Referencia de API de REST de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png
