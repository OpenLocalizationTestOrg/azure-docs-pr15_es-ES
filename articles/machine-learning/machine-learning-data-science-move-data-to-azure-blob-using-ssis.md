<properties
    pageTitle="Mover datos a o de almacenamiento de blobs de Windows Azure con conectores SSIS | Microsoft Azure"
    description="Mover los datos a o desde el almacenamiento de blobs de Azure con conectores SSIS."
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

# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Mover datos a o de almacenamiento de blobs de Windows Azure con conectores SSIS

El [SQL Server Integration Services Feature Pack para Azure](https://msdn.microsoft.com/library/mt146770.aspx) proporciona componentes para conectarse a Azure, transferir datos entre Azure y orígenes de datos locales y procesar datos almacenados en Azure.

Consejos sobre tecnologías utilizadas para mover datos a o desde el almacenamiento de blobs de Windows Azure están vinculadas a continuación:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


Los clientes han movido datos locales a la nube, podrán tener acceso desde cualquier servicio de Azure para sacar el máximo partido del conjunto de tecnologías de Azure. Se puede utilizar, por ejemplo, en el aprendizaje Azure o en un clúster de HDInsight.

Esto suele ser el primer paso para los tutoriales [SQL](machine-learning-data-science-process-sql-walkthrough.md) y [HDInsight](machine-learning-data-science-process-hive-walkthrough.md) .

Para ver una discusión de canónicos escenarios que usan SSIS para llevar a cabo necesidades empresariales comunes en escenarios de integración de datos híbridos, consulte blog de [hacer más con SQL Server Integration Services Feature Pack para Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) .

> [AZURE.NOTE] Para obtener una introducción completa con el almacenamiento de blobs de Windows Azure, consulte [Conceptos básicos de blobs de Windows Azure](../storage/storage-dotnet-how-to-use-blobs.md) y [Servicio de blobs de Windows Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## <a name="prerequisites"></a>Requisitos previos

Para realizar las tareas descritas en este artículo, debe tener una suscripción de Azure y configurar una cuenta de almacenamiento de Azure. Debe conocer la clave de nombre y la cuenta de la cuenta de almacenamiento de Azure en cargar o descargar datos.

- Para configurar una **suscripción de Azure**, vea la [versión de prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).

- Para obtener instrucciones sobre cómo crear una **cuenta de almacenamiento** y para obtener información de cuenta y clave, vea [acerca de Azure cuentas de almacenamiento](../storage/storage-create-storage-account.md).


Para usar los **conectores SSIS**, debe descargar:

- **SQL Server 2014 o 2016 estándar (o superior)**: instalar incluye SQL Server Integration Services.
- **Microsoft SQL Server 2014 o 2016 Integration Services Feature Pack de Azure**: estos pueden descargarse, respectivamente, de las páginas [De 2014 de SQL Server Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) y [Servicios de integración de SQL Server 2016](https://www.microsoft.com/download/details.aspx?id=49492) .

> [AZURE.NOTE] SSIS se ha instalado con SQL Server, pero no está incluido en la versión Express. Para obtener información sobre qué aplicaciones se incluyen en diversas ediciones de SQL Server, vea [Ediciones de SQL Server](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)

Para los materiales de aprendizaje de SSIS, vea [Manos en aprendizaje de SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Para obtener información sobre cómo ponerse en marcha y uso SISS para compilar simple extracción, transformación y paquetes de carga (ETL), consulte [SSIS Tutorial: crear un paquete de ETL Simple](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Descargar el conjunto de datos de Nueva York Taxi  
En el ejemplo que se describe a continuación use un conjunto de datos esté disponible públicamente--el conjunto de datos de [Nueva York Taxi viajes](http://www.andresmh.com/nyctaxitrips/) . El conjunto de datos consiste en llevar a unos 173 millones de Nueva York taxi en el año de 2013. Existen dos tipos de datos: datos e importe datos de detalles de viaje. Como un archivo para cada mes, tenemos 24 archivos en todos, cada una de las cuales es de aproximadamente 2GB sin comprimir.


## <a name="upload-data-to-azure-blob-storage"></a>Cargar datos con el almacenamiento de blobs de Windows Azure
Para mover el feature pack de local con el almacenamiento de blobs de Windows Azure de datos con SSIS, usamos una instancia de la [**Tarea de cargar blobs de Azure**](https://msdn.microsoft.com/library/mt146776.aspx), se muestra aquí:

![configurar datos-ciencia-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


A continuación, se describen los parámetros que usa la tarea:


Campo|Descripción|
----------------------|----------------|
**AzureStorageConnection**|Especifica un administrador de conexión de almacenamiento de Azure existente o crea uno nuevo que hace referencia a una cuenta de almacenamiento de Azure donde se hospedan los archivos de blobs de destino.|
**BlobContainer**|Especifica el nombre del contenedor de blob que contienen los archivos cargados como BLOB.|
**BlobDirectory**|Especifica el directorio de blobs de Windows donde se almacena el archivo cargado como un blob de bloque. El directorio de blob es una estructura jerárquica virtual. Si ya existe el blob, TI ia reemplazados.|
**LocalDirectory**|Especifica el directorio local que contiene los archivos que va a cargar.|
**Nombre de archivo**|Especifica un filtro de nombre para seleccionar archivos con el patrón de nombre especificado. Por ejemplo, MySheet\*.xls\* incluye archivos como MySheet001.xls y MySheetABC.xlsx|
**TimeRangeFrom/TimeRangeTo**|Especifica un filtro de intervalo de tiempo. Archivos modificación después *TimeRangeFrom* y *TimeRangeTo* antes de que se incluyen.|


> [AZURE.NOTE] Las credenciales de **AzureStorageConnection** deban ser correcta y la **BlobContainer** debe existir antes de intenta la transferencia.

## <a name="download-data-from-azure-blob-storage"></a>Descargar datos desde el almacenamiento de blobs de Windows Azure

Para descargar datos desde el almacenamiento de blobs de Windows Azure a almacenamiento con SSIS local, use una instancia de la [Tarea de cargar blobs de Azure](https://msdn.microsoft.com/library/mt146779.aspx).

##<a name="more-advanced-ssis-azure-scenarios"></a>Escenarios de SSIS Azure más avanzados
Aquí observamos que el feature pack de SSIS permite más complejos flujos controle empaquetar tareas entre sí. Por ejemplo, los datos blob podrían fuente directamente en un clúster de HDInsight, cuyos resultados se pueden descargar a un blob y, a continuación, al almacenamiento local. SSIS puede ejecutar trabajos de sección y cerdo en un clúster de HDInsight con conectores SSIS adicionales:

- Para ejecutar una secuencia de comandos de la sección en un clúster de Azure HDInsight con SSIS, use [Azure HDInsight subárbol tareas](https://msdn.microsoft.com/library/mt146771.aspx).
- Para ejecutar una secuencia de comandos de cerdo en un clúster de Azure HDInsight con SSIS, use [Azure HDInsight cerdo tareas](https://msdn.microsoft.com/library/mt146781.aspx).
