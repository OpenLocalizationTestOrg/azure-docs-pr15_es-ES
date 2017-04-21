<properties 
    pageTitle="Mover los datos de almacenamiento de blobs de Windows Azure con el Explorador de almacenamiento de Azure | Microsoft Azure" 
    description="Mover los datos de almacenamiento de blobs de Windows Azure con el Explorador de almacenamiento de Azure" 
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
    ms.date="08/31/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mover los datos de almacenamiento de blobs de Windows Azure con el Explorador de almacenamiento de Azure

Explorador de almacenamiento Azure es una herramienta gratuita de Microsoft que le permite trabajar con datos de almacenamiento de Azure en Windows, Mac OS y Linux. Este tema describe cómo se usa para cargar y descargar datos desde el almacenamiento de blobs de Windows Azure. La herramienta puede descargarse desde [El Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com/).

Consejos sobre tecnologías utilizadas para mover datos a o desde el almacenamiento de blobs de Windows Azure están vinculadas a continuación:
 
[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]   

 
> [AZURE.NOTE] Si está utilizando la máquina virtual que se ha configurado con las secuencias de comandos proporcionadas por [máquinas virtuales de ciencias de datos en Azure](machine-learning-data-science-virtual-machines.md), Explorador de almacenamiento de Azure ya está instalado en la máquina virtual.
 
> [AZURE.NOTE] Para obtener una introducción completa con el almacenamiento de blobs de Windows Azure, consulte [Conceptos básicos de blobs de Windows Azure](../storage/storage-dotnet-how-to-use-blobs.md) y [Servicio de blobs de Windows Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   

## <a name="prerequisites"></a>Requisitos previos

Este documento se supone que tiene una suscripción de Azure, una cuenta de almacenamiento y la clave de almacenamiento correspondiente para la cuenta. Antes de cargar o descargar datos, debe conocer la clave de nombre y la cuenta de la cuenta de almacenamiento de Azure. 

- Para configurar una suscripción de Azure, vea la [versión de prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
- Para obtener instrucciones sobre cómo crear una cuenta de almacenamiento y para obtener información de cuenta y clave, vea [acerca de Azure cuentas de almacenamiento](../storage/storage-create-storage-account.md). Anote la clave de acceso para su cuenta de almacenamiento como necesite esta clave para conectarse a la cuenta con la herramienta Explorador de almacenamiento de Azure.
- La herramienta de explorador de almacenamiento de Azure puede descargarse desde [El Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com/). Acepte los valores predeterminados durante la instalación.


<a id="explorer"></a>
## <a name="use-azure-storage-explorer"></a>Use el Explorador de almacenamiento de Azure 

Los pasos siguientes describen la carga y descarga de datos con el Explorador de almacenamiento de Azure. 

1.  Iniciar el Explorador de almacenamiento de Microsoft Azure.
2.  Para abrir el Asistente para **iniciar sesión en su cuenta...** , seleccione el icono de **configuración de cuenta de Azure** , a continuación, **Agregar una cuenta** y escriba las credenciales. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3.  Para abrir el Asistente para **conectarse al almacenamiento de Azure** , seleccione el icono de **Conectar con el almacenamiento de Azure** . ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Escriba la clave de acceso desde su cuenta de almacenamiento de Azure en el Asistente para **conectarse al almacenamiento de Azure** y, a continuación, en **siguiente**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Escriba el nombre de la cuenta de almacenamiento en el cuadro **nombre de cuenta** y, a continuación, seleccione **siguiente**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Ahora debe aparecer la cuenta de almacenamiento agregada. Para crear un contenedor de blob en una cuenta de almacenamiento, haga clic en el nodo de **Contenedores de Blob** en esa cuenta, seleccione **Crear contenedor de Blob**y escriba un nombre.
7. Para cargar los datos en un contenedor, seleccione el contenedor de destino y haga clic en el botón **cargar** .![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Haga clic en el **...** a la derecha del cuadro de **archivos** , seleccione uno o varios archivos para cargar desde el sistema de archivos y haga clic en **cargar** para empezar a cargar los archivos.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
7. Para descargar los datos, seleccionar el blob en el contenedor correspondiente para descargar y haga clic en **Descargar**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)


