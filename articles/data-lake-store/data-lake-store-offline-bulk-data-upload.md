<properties
   pageTitle="Cargar grandes cantidades de datos en el almacén de datos lago con métodos sin conexión | Microsoft Azure"
   description="Use la herramienta AdlCopy para copiar los datos de Azure almacenamiento de Blobs al almacén de datos lago"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/07/2016"
   ms.author="nitinme"/>

# <a name="use-azure-import-export-service-for-offline-copy-of-data-to-data-lake-store"></a>Usar el servicio de importación y exportación de Azure para copia sin conexión de datos para el almacén de datos de lago

En este artículo, aprenderá cómo copiar grandes conjuntos de datos (> 200GB) en un almacén de lago de datos de Azure con los métodos de copia sin conexión, como [Servicio de importación o exportación de Azure](../storage/storage-import-export-service.md). Más concretamente, el archivo utilizado como ejemplo en este artículo es 339,420,860,416 bytes, es decir, aproximadamente 319GB en disco. Vamos a llamar 319GB.tsv de este archivo.

Servicio de importación o exportación de Azure le permite transferir con seguridad grandes cantidades de datos con el almacenamiento de blobs de Windows Azure enviando unidades de disco duro a un centro de datos de Azure.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Cuenta de almacenamiento de azure**.

- **Cuenta de azure datos lago análisis (opcional)** : vea [Introducción a análisis de lago de datos de Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obtener instrucciones sobre cómo crear una cuenta de almacén de datos lago


## <a name="preparing-the-data"></a>Preparar los datos

Antes de utilizar el servicio de importación o exportación, debemos interrumpir la el archivo de datos para que sea transferido **en copias que son menos de 200 GB** en tamaño. Esto es porque la herramienta de importación no funciona con más de 200GB de archivos. Para cumplir con esta, en este tutorial se divide el archivo en fragmentos de 100GB bytes. Puede hacerlo fácilmente con [Cygwin](https://cygwin.com/install.html). Cygwin es compatible con el comando Linux que permite a los usuarios hacer esto fácilmente. En nuestro caso, usamos el siguiente comando.

    split -b 100m 319GB.tsv

La operación de división crea archivos con los siguientes nombres.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Preparar discos con datos

Siga las instrucciones en [Utilizando el servicio de importación o exportación de Azure](../storage/storage-import-export-service.md) (en la sección **preparar las unidades** ) para preparar las unidades de disco duras. Aquí es el flujo general sobre cómo preparar la unidad.

1. Adquirir un disco duro que cumple los requisitos para usarse con el servicio de importación o exportación de Auzre.

2. Identificar una cuenta de almacenamiento de Azure donde los datos serán copiado una vez hueco enviado al centro de datos de Azure.

3. Use la [Herramienta de importación o exportación de Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), una herramienta de línea de comandos. Aquí tiene un fragmento de código de ejemplo sobre cómo usar la herramienta.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Para obtener más fragmentos de código de ejemplo sobre cómo usar la **Herramienta de importación o exportación de Azure**, vea [Utilizando el servicio de importación o exportación de Azure](../storage/storage-import-export-service.md) .

4. El comando anterior crea un archivo de diario en la ubicación especificada. Este archivo de diario que usa para crear un trabajo de importación desde el [Portal de Azure clásico](https://manage.windowsazure.com).

## <a name="create-an-import-job"></a>Crear un trabajo de importación

Ahora puede crear un trabajo de importación siguiendo las instrucciones en [Utilizando el servicio de importación o exportación de Azure](../storage/storage-import-export-service.md) (en la sección **crear el trabajo de importación** ). Para este trabajo de importación con otros detalles, también proporcionar el archivo de diario creado al preparar las unidades de disco.

## <a name="physically-ship-the-disks"></a>Enviar física de los discos

Ahora se pueden enviar física los discos a un centro de datos de Azure donde los datos se copian en el BLOB de almacenamiento de Azure proporcionan al crear el trabajo de importación. Además, al crear el trabajo, si elegido para proporcionar la información de seguimiento más tarde, ahora puede volver atrás para el trabajo de importación y actualice el número de seguimiento.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Copiar datos de Azure almacenamiento de Blobs al almacén de lago de datos de Azure

Una vez que se muestra el estado de la tarea de importación completado, puede comprobar si los datos están disponibles en el BLOB de almacenamiento de Azure había especificado. A continuación, puede utilizar una variedad de métodos para mover datos de los BLOB de almacenamiento de Azure a lago almacén de datos de Azure. Para todas las opciones disponibles para cargar los datos, vea [datos de Ingesting en el almacén de datos de lago](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

En esta sección, se proporcionan las definiciones de JSON que puede usar para crear una canalización de generador de datos de Azure para copiar los datos. Puede usar estas definiciones de JSON desde el [portal de Azure](../data-factory/data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](../data-factory/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Origen vinculado servicio de almacenamiento de Azure Blob)

````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Servicio (almacén de lago de datos de Azure) vinculado de destino

````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Conjunto de datos de entrada
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Conjunto de datos de salida
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Canalización (copiar actividad)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Para obtener más información sobre cómo usar el generador de datos de Azure para mover los datos de Azure almacenamiento de blobs y el almacenamiento de lago de datos de Azure, vea [mover datos de Azure almacenamiento de blobs al almacén de lago de datos de Azure mediante el generador de datos de Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Reconstruir los archivos de datos de almacén de lago de datos de Azure

Se inicia con un archivo que estaba 319GB en tamaño y dañó hacia abajo de en archivos de tamaño para que se puede transferir mediante el servicio de importación o exportación de Azure. Ahora que los datos están en la tienda de lago de datos de Azure, podemos reconstrcut el archivo a su tamaño original. Puede usar la siguiente cmldts de Azure PowerShell para hacerlo.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Pasos siguientes

- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)
- [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)
