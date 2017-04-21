<properties 
    pageTitle="Mover los datos de Amazon Simple Storage Service con datos fábrica | Microsoft Azure" 
    description="Obtenga información sobre cómo mover datos de Amazon Simple Storage Service (S3) usando el generador de datos de Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Mover los datos de Amazon Simple Storage Service con el generador de datos de Azure

En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos de Amazon Simple Storage Service (S3) a otros datos almacenar. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una descripción general de movimiento de datos y una lista de comercios de datos de origen/receptor compatibles con la actividad de copia.  

Generador de datos admite actualmente solo mover los datos de Amazon S3 a otro almacena datos, pero no para mover datos desde otro almacena datos a Amazon S3.

## <a name="required-permissions"></a>Permisos necesarios

Para copiar datos de Amazon S3, asegúrese de que se le ha concedido siguientes permisos:

- **S3:GetObject** y **s3:GetObjectVersion** para operaciones de objeto de Amazon S3
- **S3:ListBucket** y **s3:ListAllMyBuckets** (que se usa en el Asistente para copiar únicamente) para operaciones de cubo de Amazon S3 

Puede encontrar la lista completa de permisos de Amazon S3 con detalles de la [Especificación de permisos en una directiva](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie datos de Amazon S3 es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

En el ejemplo siguiente se proporciona definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Muestra cómo copiar datos de Amazon S3 en el almacenamiento de blobs de Windows Azure. Sin embargo, los datos se pueden copiar a cualquiera de los receptores indicados [a continuación](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>Ejemplo: Copiar datos de Amazon S3 a blobs de Windows Azure
Este ejemplo muestra cómo copiar datos de un S3 Amazon en un almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  
 
El ejemplo tiene las siguientes entidades de fábrica de datos:

- Servicio de tipo [AwsAccessKey](#linked-service-properties)vinculado.
- Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
- Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [AmazonS3](#dataset-type-properties).
- Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [FileSystemSource](#copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia datos de Amazon S3 a una blobs de Windows Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

**Servicio de S3 Amazon vinculado**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
            }
        }
    }

**Servicio de almacenamiento vinculado de Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de datos de entrada de Amazon S3**

Establecer **"externo": true** informa al servicio del generador de datos que el conjunto de datos externo para el generador de datos y no se produce en una actividad en el generador de datos. Establecer esta propiedad en true en un conjunto de datos de entrada que no se produce en una actividad de la canalización.

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }



**Conjunto de datos de salida de blobs de Windows Azure**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). La ruta de acceso para el blob se evalúa dinámicamente en función de la hora de inicio de la segmentación de datos que se está procesando. La ruta de acceso de la carpeta usa el año, mes, día y partes de horas de la hora de inicio.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Canalización con actividad de copia**

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **FileSystemSource** y tipo de **receptor** se establece en **BlobSink**. 
    
    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
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
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

La tabla siguiente proporciona la descripción para elementos JSON específicos a Amazon S3 (**AwsAccessKey**) vinculado servicio.

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------- | ------- |  
| accessKeyID | Identificador de la tecla de acceso secreto. | cadena | Sí |
| secretAccessKey | Tecla de acceso secreto propiamente dicho. | Cadena secreta cifrada | Sí | 


## <a name="dataset-type-properties"></a>Propiedades de tipo de conjunto de datos

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección de typeProperties para el conjunto de datos de tipo **AmazonS3** (que incluye el conjunto de datos de Amazon S3) tiene las siguientes propiedades

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------- | ------ | 
| bucketName | El nombre de cubo S3. | Cadena | Sí |
| clave | La clave del objeto S3. | Cadena | No | 
| prefijo | Prefijo de la clave del objeto S3. Se seleccionan objetos cuyas claves comienzan por este prefijo. Se aplica solo cuando la clave está vacía. | Cadena | No | 
| Versión | La versión del objeto de S3 si está habilitado el control de versiones de S3. | Cadena | No |  
| formato | Se admiten los siguientes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Establezca la propiedad **tipo** en formato en uno de estos valores. Consulte [Especificar TextFormat](#specifying-textformat), [AvroFormat que especifica](#specifying-avroformat), [JsonFormat que especifica](#specifying-jsonformat), [OrcFormat que especifica](#specifying-orcformat), [ParquetFormat especificar](#specifying-parquetformat) secciones y para obtener más información. Si desea copiar archivos como-está entre stores basados en archivos (copia binario), puede omitir la sección formato de ambas definiciones de conjunto de datos de entrada y salida.| No
| compresión | Especifique el tipo y el nivel de compresión de los datos. Tipos compatibles son: **GZip**, **Deflate**y **BZip2** y niveles compatibles son: **óptima** y **más rápida**. Actualmente, no se admite la configuración de compresión de datos en **AvroFormat** o **OrcFormat**. Para obtener más información, vea la sección de [compatibilidad con la compresión](#compression-support) .  | No |

> [AZURE.NOTE] bucketName + tecla especifica la ubicación del objeto S3 donde depósito es el contenedor de raíz de objetos de S3 y clave es la ruta completa al objeto S3.

### <a name="sample-dataset-with-prefix"></a>Conjunto de datos de ejemplo con prefijo

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-with-version"></a>Conjunto de datos de ejemplo (con la versión)

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>Rutas de acceso dinámicas para S3

En el ejemplo, utilizamos valores fijos para las propiedades clave y bucketName en el conjunto de datos de Amazon S3. 

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

Puede tener datos fábrica calcular la clave y bucketName dinámicamente en tiempo de ejecución mediante variables de sistema como SliceStart.

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

Puede hacer lo mismo para la propiedad de prefijo de un conjunto de datos de Amazon S3. Para obtener una lista de variables y funciones admitidas, consulte [funciones del generador de datos y las variables del sistema](data-factory-functions-variables.md) . 


[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## <a name="copy-activity-type-properties"></a>Copiar las propiedades del tipo de actividad

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directivas están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección **typeProperties** de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Cuando el origen de actividad de copia es de tipo **FileSystemSource** (que incluye Amazon S3), las siguientes propiedades están disponibles en la sección typeProperties:

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- | 
| recursivas | Especifica si forma recursiva lista S3 objetos en el directorio. | Verdadero o falso | No | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes: 
- [Tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia. 
