<properties
    pageTitle="Cómo usar el almacenamiento de blobs de Windows Azure (objeto almacenamiento) de Python | Microsoft Azure"
    description="Almacenar datos estructurados en la nube con el almacenamiento de blobs de Windows Azure (almacenamiento de objetos)."
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-azure-blob-storage-from-python"></a>Cómo usar el almacenamiento de blobs de Windows Azure de Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general

Almacenamiento de blobs de Windows Azure es un servicio que almacena datos estructurados en la nube como objetos o BLOB. Almacenamiento de blobs de Windows puede almacenar cualquier tipo de texto o datos binarios, como un documento, el archivo multimedia o el instalador de la aplicación. Almacenamiento de blobs también se conoce como almacenamiento de objetos.

En este artículo le mostrará cómo llevar a cabo con el almacenamiento de blobs de escenarios comunes. Los ejemplos se escriben en Python y utilizan el [SDK de Microsoft Azure almacenamiento de Python]. Los escenarios cubiertos incluyen cargando, listado, descargar y eliminar BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-container"></a>Crear un contenedor

En función del tipo de blob que le gustaría usar, crear un objeto **BlockBlobService**, **AppendBlobService**o **PageBlobService** . El código siguiente utiliza un objeto **BlockBlobService** . Agregue lo siguiente en la parte superior de cualquier archivo Python en la que desea acceder mediante programación almacenamiento de blobs de Windows Azure bloque.

    from azure.storage.blob import BlockBlobService

El código siguiente crea un objeto **BlockBlobService** con la clave de nombre y la cuenta de la cuenta de almacenamiento.  Reemplazar 'myaccount' y 'MiClave' con el nombre de la cuenta y la clave.

    block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

En el ejemplo siguiente, puede usar un objeto **BlockBlobService** para crear el contenedor si no existe.

    block_blob_service.create_container('mycontainer')

De forma predeterminada, el nuevo contenedor es privado, así que deberá especificar la clave de acceso de almacenamiento (tal como lo hizo anteriormente) para descargar BLOB de este contenedor. Si desea hacer que el BLOB dentro del contenedor esté disponible para todos los usuarios, puede crear el contenedor y pasar el nivel de acceso del público con el siguiente código.

    from azure.storage.blob import PublicAccess
    block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)

Como alternativa, puede modificar un contenedor después de haber creado mediante el siguiente código.

    block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)

Después de este cambio, cualquier usuario de Internet puede ver BLOB en un contenedor público, pero solo se puede modificar o eliminar.

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Para crear un blob bloque y cargar los datos, use la **crear\_blob\_de\_ruta de acceso**, **crear\_blob\_de\_secuencia**, **crear\_blob\_de\_bytes** o **crear\_blob\_desde\_texto** métodos. Son métodos de alto nivel que lleve a cabo el agrupamiento es necesario cuando el tamaño de los datos supera 64 MB.

**crear\_blob\_de\_ruta** el contenido de un archivo desde la ruta de acceso, las cargas y **crear\_blob\_de\_secuencia** carga el contenido de un archivo o secuencia ya está abierto. **crear\_blob\_de\_bytes** una matriz de bytes, las cargas y **crear\_blob\_de\_texto** carga el valor de texto especificado mediante la codificación especificada (UTF-8 de forma predeterminada).

En el ejemplo siguiente se carga el contenido del archivo **sunset.png** en el blob **myblob** .

    from azure.storage.blob import ContentSettings
    block_blob_service.create_blob_from_path(
        'mycontainer',
        'myblockblob',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png')
                )

## <a name="list-the-blobs-in-a-container"></a>El BLOB en un contenedor de lista

Para mostrar los BLOB en un contenedor, utilice la **lista\_BLOB** método. Este método devuelve un generador. El código siguiente genera el **nombre** de cada blob en un contenedor de la consola.

    generator = block_blob_service.list_blobs('mycontainer')
    for blob in generator:
        print(blob.name)

## <a name="download-blobs"></a>Descargar BLOB

Para descargar datos desde un blob, use **obtener\_blob\_a\_ruta**, **obtener\_blob\_a\_secuencia**, **obtener\_blob\_a\_bytes**, o **obtener\_blob\_a\_texto**. Son métodos de alto nivel que lleve a cabo el agrupamiento es necesario cuando el tamaño de los datos supera 64 MB.

En el ejemplo siguiente se muestra cómo utilizar **obtener\_blob\_a\_ruta** para descargar el contenido de blobs de **myblob** y almacenar el archivo de **salida de sunset.png** .

    block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')

## <a name="delete-a-blob"></a>Eliminar un blob

Por último, para eliminar un objeto binario, llame a **delete_blob**.

    block_blob_service.delete_blob('mycontainer', 'myblockblob')

## <a name="writing-to-an-append-blob"></a>Escribir en un objeto binario de datos anexados

Un blob de datos anexados está optimizado para operaciones de datos anexados, como registro. Como un blob bloque, un blob de datos anexados se compone de bloques, pero cuando agrega un nuevo bloque a un blob de datos anexados, siempre se anexan al final del blob. No se puede actualizar o eliminar un bloque existente en un objeto binario de datos anexados. El bloque de identificadores para un blob de datos anexados no se exponen como están para un blob de bloque.

Cada bloque de un blob de datos anexados puede tener un tamaño diferente, hasta un máximo de 4 MB y un blob de datos anexados puede incluir un máximo de 50.000 bloques. El tamaño máximo de un objeto binario de datos anexados, por tanto, es algo más de 195 GB (4 MB X 50.000 bloques).

El ejemplo siguiente crea un nuevo blob de datos anexados y anexe algunos datos, simular una operación de registro simple.

    from azure.storage.blob import AppendBlobService
    append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

    # The same containers can hold all types of blobs
    append_blob_service.create_container('mycontainer')

    # Append blobs must be created before they are appended to
    append_blob_service.create_blob('mycontainer', 'myappendblob')
    append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

    append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de blobs de Windows, siga estos vínculos para obtener más información.

- [Centro para desarrolladores de Python](/develop/python/)
- [API de REST de servicios de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del equipo de almacenamiento de Azure]
- [Almacenamiento de Microsoft Azure SDK para Python]

[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Almacenamiento de Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python
