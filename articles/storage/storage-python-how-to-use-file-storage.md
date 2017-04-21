<properties
    pageTitle="Cómo usar el almacenamiento de archivos de Azure de Python | Microsoft Azure"
    description="Aprenda a usar el almacenamiento de archivos de Azure de Python para cargar, lista, descargar y eliminar archivos."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-azure-file-storage-from-python"></a>Cómo usar el almacenamiento de archivos de Azure de Python

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Información general

En este artículo le mostrará cómo llevar a cabo escenarios comunes mediante el almacenamiento de archivos. Los ejemplos se escriben en Python y utilizan el [SDK de Microsoft Azure almacenamiento de Python]. Los escenarios cubiertos incluyen cargando, listado, descargar y eliminar archivos.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-share"></a>Crear un recurso compartido

El objeto **FileService** le permite trabajar con recursos compartidos, directorios y archivos. El código siguiente crea un objeto **FileService** . Agregue lo siguiente en la parte superior de cualquier archivo Python en la que desea acceder mediante programación el almacenamiento de Azure.

    from azure.storage.file import FileService

El código siguiente crea un objeto **FileService** con la clave de nombre y la cuenta de la cuenta de almacenamiento.  Reemplazar 'myaccount' y 'MiClave' con el nombre de la cuenta y la clave.

    file_service = **FileService** (account_name='myaccount', account_key='mykey')

En el ejemplo siguiente, puede usar un objeto **FileService** para crear el recurso compartido si no existe.

    file_service.create_share('myshare')

## <a name="upload-a-file-into-a-share"></a>Cargar un archivo en un recurso compartido

Una cuota de almacenamiento de Azure archivo contiene al menos, un directorio raíz donde pueden residen los archivos. En esta sección, aprenderá cómo cargar un archivo de almacenamiento local en el directorio raíz de un recurso compartido.

Para crear un archivo y cargue los datos, use la **crear\_archivo\_de\_ruta**, **crear\_archivo\_de\_secuencia**, **crear\_archivo\_de\_bytes** o **crear\_archivo\_de\_texto** métodos. Son métodos de alto nivel que lleve a cabo el agrupamiento es necesario cuando el tamaño de los datos supera 64 MB.

**crear\_archivo\_de\_ruta** el contenido de un archivo desde la ruta de acceso, las cargas y **crear\_archivo\_de\_secuencia** carga el contenido de un archivo o secuencia ya está abierto. **crear\_archivo\_de\_bytes** una matriz de bytes, las cargas y **crear\_archivo\_de\_texto** carga el valor de texto especificado mediante la codificación especificada (UTF-8 de forma predeterminada).

En el ejemplo siguiente se carga el contenido del archivo **sunset.png** en el archivo **MiArchivo** .

    from azure.storage.file import ContentSettings
    file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## <a name="how-to-create-a-directory"></a>Cómo: crear un directorio

También puede organizar el almacenamiento colocando archivos de subdirectorios en lugar de tener todos ellos en el directorio raíz. El servicio de almacenamiento de archivos de Azure le permite crear todos los directorios que le permitirá a su cuenta. El código siguiente creará un directorio subcarpetas denominado **sampledir** en el directorio raíz.

    file_service.create_directory('myshare', 'sampledir')

## <a name="how-to-list-files-and-directories-in-a-share"></a>Cómo: lista de archivos y directorios de un recurso compartido

Para mostrar los archivos y directorios de un recurso compartido, utilice la **lista\_directorios\_y\_archivos** método. Este método devuelve un generador. El código siguiente genera el **nombre** de cada archivo y directorio de un recurso compartido en la consola.

    generator = file_service.list_directories_and_files('myshare')
    for file_or_dir in generator:
        print(file_or_dir.name)

## <a name="download-files"></a>Descargar archivos

Para descargar datos desde un archivo, use **obtener\_archivo\_a\_ruta**, **obtener\_archivo\_a\_secuencia**, **obtener\_archivo\_a\_bytes**, o **obtener\_archivo\_a\_texto**. Son métodos de alto nivel que lleve a cabo el agrupamiento es necesario cuando el tamaño de los datos supera 64 MB.

En el ejemplo siguiente se muestra cómo utilizar **obtener\_archivo\_a\_ruta** para descargar el contenido del archivo **MiArchivo** y almacenar el archivo de **salida de sunset.png** .

    file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## <a name="delete-a-file"></a>Eliminar un archivo

Por último, para eliminar un archivo, llame a **delete_file**.

    file_service.delete_file('myshare', None, 'myfile')

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de archivos, siga estos vínculos para obtener más información.

- [Centro para desarrolladores de Python](/develop/python/)
- [API de REST de servicios de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del equipo de almacenamiento de Azure]
- [Almacenamiento de Microsoft Azure SDK para Python]

[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Almacenamiento de Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python
