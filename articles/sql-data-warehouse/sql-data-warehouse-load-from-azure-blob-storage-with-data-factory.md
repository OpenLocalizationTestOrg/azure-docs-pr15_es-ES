<properties
   pageTitle="Cargar datos de almacenamiento de blobs de Windows Azure en el almacén de datos de SQL Azure (generador de datos de Azure) | Microsoft Azure"
   description="Más información sobre cómo cargar los datos con el generador de datos de Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-from-azure-blob-storage-into-azure-sql-data-warehouse-azure-data-factory"></a>Cargar datos de almacenamiento de blobs de Windows Azure en el almacén de datos de SQL Azure (generador de datos de Azure)

> [AZURE.SELECTOR]
- [Generador de datos](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

 En este tutorial se muestra cómo crear una canalización en el generador de datos de Azure para mover datos de Azure almacenamiento de blobs al almacén de datos de SQL. Con los siguientes pasos, podrá:

+ Datos de ejemplo de configuración de un Blob de almacenamiento de Azure.
+ Conectar recursos con el generador de datos de Azure.
+ Crear una canalización para mover datos de almacenamiento de Blobs al almacén de datos de SQL.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## <a name="before-you-begin"></a>Antes de empezar

Para familiarizarse con el generador de datos de Azure, vea [Introducción al generador de datos de Azure][].

### <a name="create-or-identify-resources"></a>Crear o identificar recursos

Antes de comenzar este tutorial, debe tener los siguientes recursos.

   + **Azure almacenamiento de blobs**: este tutorial usa Azure almacenamiento de blobs como origen de datos para la canalización de generador de datos de Azure y, a continuación, así que debe tener uno disponible para almacenar los datos de ejemplo. Si aún no tiene una, obtenga información sobre cómo [crear una cuenta de almacenamiento][].

   + **Almacén de datos SQL**: este tutorial mueve los datos de Azure almacenamiento de blobs al almacén de datos de SQL y así que necesitan tener un almacén de datos en línea que se carga con los datos de ejemplo AdventureWorksDW. Si no dispone de un almacén de datos, obtenga información sobre cómo [aprovisionar uno][Create a SQL Data Warehouse]. Si tiene un almacén de datos, pero no la aprovisionar con los datos de ejemplo, puede [cargar manualmente][Load sample data into SQL Data Warehouse].

   + **Generador de datos de Azure**: generador de datos de Azure se completa la carga real y así que debe tener una que puede usar para generar la canalización de movimiento de datos. Si aún no tiene una, obtenga información sobre cómo crear una en el paso 1 de [empezar a trabajar con el generador de datos de Azure (Editor de fábrica de datos)][].

   + **AZCopy**: debe AZCopy para copiar los datos de ejemplo de cliente local a su almacenamiento de Azure Blob. Para obtener instrucciones de instalación, consulte la [documentación de AZCopy][].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Paso 1: Copiar datos de ejemplo en Azure almacenamiento de blobs

Una vez que todas las partes listos, está listo para copiar los datos de ejemplo para el almacenamiento de Azure Blob.

1. [Descargar datos de ejemplo][]. Estos datos agregará otra tres años de datos de ventas a los datos de ejemplo AdventureWorksDW.

2. Utilice este comando AZCopy para copiar los tres años de datos en el almacenamiento de Azure Blob.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## <a name="step-2-connect-resources-to-azure-data-factory"></a>Paso 2: Conectar recursos con el generador de datos de Azure

Ahora que los datos podemos crear la canalización de generador de datos de Azure para mover los datos desde el almacenamiento de blobs de Windows Azure en el almacén de datos de SQL.

Para empezar, abra el [portal de Azure][] y seleccione el generador de datos en el menú de la izquierda.

### <a name="step-21-create-linked-service"></a>Paso 2.1: Crear servicios vinculados

Vincular su cuenta de almacenamiento de Azure y el almacén de datos SQL en el generador de datos.  

1. En primer lugar, comenzar el proceso de registro, haga clic en la sección de la fábrica de datos 'Servicios vinculadas' y, a continuación, haga clic en 'almacén de datos nuevas'. Elija un nombre para registrar el almacenamiento de azure en el almacenamiento de Azure Active como tipo y, a continuación, escriba su nombre de cuenta y la clave de cuenta.

2. Almacén de datos de SQL para registrar, vaya a la sección 'Autor e implementar', seleccione 'Nuevo almacén de datos' y, a continuación, 'Almacén de datos de SQL Azure'. Copiar y pegar en esta plantilla y, a continuación, rellene la información específica.

```JSON
{
    "name": "<Linked Service Name>",
    "properties": {
        "description": "",
        "type": "AzureSqlDW",
        "typeProperties": {
             "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
         }
    }
}
```

### <a name="step-22-define-the-dataset"></a>Paso 2.2: Definir el conjunto de datos

Después de crear los servicios vinculados, tenemos definir los conjuntos de datos.  A continuación, esto significa definir la estructura de los datos que se ha movido desde el almacenamiento a su almacén de datos.  Puede obtener más información sobre la creación de

1. Iniciar este proceso, vaya a la sección 'Autor e implementar' de la fábrica de datos.

2. Haga clic en 'Nuevo conjunto de datos', 'Almacenamiento de blobs de Windows Azure' para vincular el almacenamiento con el generador de datos.  Puede usar la debajo de secuencia de comandos para definir los datos en el almacenamiento de blobs de Windows Azure:

```JSON
{
    "name": "<Dataset Name>",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "<linked storage name>",
        "typeProperties": {
            "folderPath": "<containter name>",
            "fileName": "FactInternetSales.csv",
            "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```


3. Ahora se también definir el conjunto de datos para el almacén de datos de SQL.  Empezaremos de la misma manera, haciendo clic en 'Nuevo conjunto de datos' y, a continuación, 'Almacén de datos de SQL Azure'.

```JSON
{
    "name": "DWDataset",
    "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "FactInternetSales"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

## <a name="step-3-create-and-run-your-pipeline"></a>Paso 3: Crear y ejecutar la canalización

Por último, se le configurar y ejecutar la canalización en el generador de datos de Azure.  Esta es la operación que finalizará el movimiento de datos real.  Puede encontrar una vista completa de las operaciones que se pueden completar con el generador de datos de Azure y almacén de datos SQL [aquí][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

En la sección 'Autor e implementar' Ahora haga clic en 'Más comandos' y, a continuación, en 'nueva canalización de'.  Después de crear la canalización, puede usar la debajo de código para transferir los datos a su almacén de datos:

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
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
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, empiece por la visualización:

- [Ruta de aprendizaje del generador de datos de azure][].
- [Conector de almacén de datos SQL azure][]. Este es el tema de referencia principales para usar el generador de datos de Azure con el almacén de datos de SQL Azure.


Estos temas proporcionan información detallada sobre el generador de datos de Azure. Hablan base de datos de SQL Azure o HDinsight, pero la información también se aplica al almacén de datos de SQL Azure.

- [Tutorial: Introducción a Azure datos fábrica][] Este es el tutorial de núcleo para el procesamiento de datos con el generador de datos de Azure. En este tutorial va a crear su primer canalización que utiliza HDInsight para transformar y analizar registros web mensualmente. Tenga en cuenta que no hay ninguna actividad de copia de este tutorial.
- [Tutorial: copiar datos de Azure almacenamiento de blobs a la base de datos de SQL Azure][]. En este tutorial, creará una canalización en el generador de datos de Azure para copiar datos de Azure almacenamiento de blobs a base de datos de SQL Azure.


<!--Image references-->

<!--Article references-->
[Documentación de AZCopy]: ../storage/storage-use-azcopy.md
[Conector de almacén de datos SQL Azure]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Crear una cuenta de almacenamiento]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Empezar a trabajar con el generador de datos de Azure (Editor de fábrica de datos)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introducción al generador de datos de Azure]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Tutorial: Copiar datos de Azure almacenamiento de blobs a base de datos de SQL Azure]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Tutorial: Introducción a generador de datos de Azure]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Ruta de aprendizaje de generador de datos de Azure]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Portal de Azure]: https://portal.azure.com
[Descargar datos de ejemplo]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv
