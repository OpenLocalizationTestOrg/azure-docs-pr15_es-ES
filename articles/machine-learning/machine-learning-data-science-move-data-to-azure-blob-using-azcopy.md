<properties
    pageTitle="Mover los datos hacia y desde el almacenamiento de blobs de Azure con AzCopy | Microsoft Azure"
    description="Mover los datos hacia y desde el almacenamiento de blobs de Azure con AzCopy"
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

# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Mover los datos hacia y desde el almacenamiento de blobs de Azure con AzCopy

AzCopy es una herramienta de línea de comandos diseñada para cargar, descargar y copiar datos a y desde Microsoft Azure blob, archivo y almacenamiento de la tabla.

Para obtener instrucciones sobre cómo instalar AzCopy e información adicional sobre cómo usar con la plataforma Windows Azure, consulte [Introducción a la herramienta de línea de comandos de AzCopy](../storage/storage-use-azcopy.md).

Consejos sobre tecnologías utilizadas para mover datos a o desde el almacenamiento de blobs de Windows Azure están vinculadas a continuación:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Si está utilizando la máquina virtual que se ha configurado con las secuencias de comandos proporcionadas por [máquinas virtuales de ciencias de datos en Azure](machine-learning-data-science-virtual-machines.md), AzCopy ya está instalado en la máquina virtual.

> [AZURE.NOTE] Para obtener una introducción completa con el almacenamiento de blobs de Windows Azure, consulte [Conceptos básicos de blobs de Windows Azure](../storage/storage-dotnet-how-to-use-blobs.md) y [Servicio de blobs de Windows Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## <a name="prerequisites"></a>Requisitos previos

Este documento se supone que tiene una suscripción de Azure, una cuenta de almacenamiento y la clave de almacenamiento correspondiente para la cuenta. Antes de cargar o descargar datos, debe conocer la clave de nombre y la cuenta de la cuenta de almacenamiento de Azure.

- Para configurar una suscripción de Azure, vea la [versión de prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).

- Para obtener instrucciones sobre cómo crear una cuenta de almacenamiento y para obtener información de cuenta y clave, vea [acerca de Azure cuentas de almacenamiento](../storage/storage-create-storage-account.md).


## <a name="run-azcopy-commands"></a>Ejecute los comandos AzCopy

Para ejecutar comandos AzCopy, abra una ventana de comandos y navegue hasta el directorio de instalación de AzCopy en su equipo, donde se encuentra el ejecutable AzCopy.exe. 

La sintaxis básica para los comandos de AzCopy es:

    AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] Puede agregar la ubicación de instalación de AzCopy a la ruta del sistema y, a continuación, ejecute los comandos desde cualquier directorio. De forma predeterminada, se instala AzCopy *% ProgramFiles(x86) %\Microsoft SDKs\Azure\AzCopy* o *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.

## <a name="upload-files-to-an-azure-blob"></a>Cargar archivos en un blobs de Windows Azure

Para cargar un archivo, use el siguiente comando:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Descargar archivos de una blobs de Windows Azure

Para descargar un archivo desde un blobs de Windows Azure, utilice el siguiente comando:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Transferir BLOB entre contenedores de Azure

Para transferir BLOB entre Azure contenedores, use el siguiente comando:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Sugerencias para usar AzCopy

> [AZURE.TIP]   
> 1. Al **cargar** archivos, */S* carga los archivos de forma recursiva. Sin este parámetro, no se cargan archivos en subdirectorios.  
> 2. Cuando se **descarga** un archivo, */S* busca el contenedor de forma recursiva hasta que todos los archivos en el directorio especificado y sus subdirectorios o todos los archivos que coincidan con el patrón especificado en el directorio determinado y sus subdirectorios, se descargan.  
> 3.  No puede especificar un **archivo blob específicos** descargar mediante el parámetro */Source* . Para descargar un archivo específico, especifique el nombre de archivo de blobs descargar mediante el parámetro */Pattern* . Parámetro **/S** puede utilizarse para que AzCopy busque de forma recursiva un patrón de nombre de archivo. Sin el parámetro de trama, AzCopy descarga todos los archivos en el directorio.
