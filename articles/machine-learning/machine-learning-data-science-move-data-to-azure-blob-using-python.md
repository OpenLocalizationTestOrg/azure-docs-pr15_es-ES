<properties
    pageTitle="Mover los datos hacia y desde el almacenamiento de blobs de Azure con Python | Microsoft Azure"
    description="Mover los datos hacia y desde el almacenamiento de blobs de Azure con Python"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Mover los datos hacia y desde el almacenamiento de blobs de Azure con Python

Este tema describe cómo mostrar, cargar y descargar BLOB con la API de Python. Con la API de Python que se proporcionan en Azure SDK, puede:

- Crear un contenedor
- Cargar un blob en un contenedor
- Descargar BLOB
- El BLOB en un contenedor de lista
- Eliminar un blob

Para obtener más información sobre el uso de la API de Python, vea [cómo usar el servicio de almacenamiento de blobs de Python](../storage/storage-python-how-to-use-blob-storage.md).

Consejos sobre tecnologías utilizadas para mover datos a o desde el almacenamiento de blobs de Windows Azure están vinculadas a continuación:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Si está utilizando la máquina virtual que se ha configurado con las secuencias de comandos proporcionadas por [máquinas virtuales de ciencias de datos en Azure](machine-learning-data-science-virtual-machines.md), AzCopy ya está instalado en la máquina virtual.

> [AZURE.NOTE] Para obtener una introducción completa con el almacenamiento de blobs de Windows Azure, consulte [Conceptos básicos de blobs de Windows Azure](../storage/storage-dotnet-how-to-use-blobs.md) y [Servicio de blobs de Windows Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## <a name="prerequisites"></a>Requisitos previos

Este documento se supone que tiene una suscripción de Azure, una cuenta de almacenamiento y la clave de almacenamiento correspondiente para la cuenta. Antes de cargar o descargar datos, debe conocer la clave de nombre y la cuenta de la cuenta de almacenamiento de Azure.

- Para configurar una suscripción de Azure, vea la [versión de prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).

- Para obtener instrucciones sobre cómo crear una cuenta de almacenamiento y para obtener información de cuenta y clave, vea [acerca de Azure cuentas de almacenamiento](../storage/storage-create-storage-account.md).


## <a name="upload-data-to-blob"></a>Cargar datos en Blob

Agregar el fragmento de código siguiente cerca de la parte superior de cualquier código Python en la que desea tener acceso mediante programación el almacenamiento de Azure:

    from azure.storage.blob import BlobService

El objeto **BlobService** le permite trabajar con contenedores y BLOB. El código siguiente crea un objeto BlobService con la clave de nombre y la cuenta de la cuenta de almacenamiento. Reemplace el nombre de cuenta y clave de cuenta con su cuenta real y la clave.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Para cargar los datos en un blob, utilice los métodos siguientes:

1. poner\_bloque\_blob\_de\_ruta de acceso (carga el contenido de un archivo desde la ruta de acceso)
2. poner\_block_blob\_de\_archivo (carga el contenido de un archivo o secuencia ya está abierto)
3. poner\_bloque\_blob\_de\_bytes (cargas una matriz de bytes)
4. poner\_bloque\_blob\_de\_texto (el valor de texto especificado mediante la codificación especificada las cargas)

El código de ejemplo siguiente carga un archivo local a un contenedor:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

El siguiente código de ejemplo carga todos los archivos (excepto los directorios) en un directorio local para el almacenamiento de blobs:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Descargar datos de Blob

Descargar datos desde un blob, utilice los métodos siguientes:
1. obtener\_blob\_a\_ruta de acceso
2. obtener\_blob\_a\_archivo
3. obtener\_blob\_a\_bytes
4. obtener\_blob\_a\_texto

Estos métodos que lleve a cabo el agrupamiento es necesario cuando el tamaño de los datos supera 64 MB.

El siguiente código de ejemplo descarga el contenido de un blob en un contenedor en un archivo local:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

El siguiente código descarga todos los blobs desde un contenedor. Usa lista\_blobs para obtener una lista de BLOB disponible en el contenedor y las descarga en un directorio local.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
