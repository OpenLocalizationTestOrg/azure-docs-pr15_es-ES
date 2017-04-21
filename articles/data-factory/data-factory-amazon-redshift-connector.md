<properties 
    pageTitle="Mover los datos de Redshift de Amazon con datos fábrica | Microsoft Azure" 
    description="Obtenga información sobre cómo mover los datos de Redshift de Amazon con el generador de datos de Azure." 
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
    ms.date="08/23/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Mover los datos de Amazon Redshift con el generador de datos de Azure

Este artículo describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos de Amazon Redshift a otros datos almacenar. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con copia actividad y una lista de origen o receptor de almacenamiento de datos.  

Generador de datos admite actualmente solo mover los datos de Amazon Redshift a otro almacena datos, pero no para mover datos desde otro almacena datos a Redshift de Amazon.

## <a name="prerequisites"></a>Requisitos previos

- Si está moviendo datos a un almacén de datos local, conceda Data Management Gateway (Utilice la dirección IP del equipo) el acceso al clúster de Amazon Redshift. Para obtener instrucciones, consulte [autorizar el acceso al clúster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) . 
- Si está moviendo datos a un almacén de datos de Azure, vea [Intervalos de IP de centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) para los intervalos de direcciones de IP calcular (incluidos los intervalos SQL) utilizados los centros de datos de Microsoft Azure. 

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie datos de Amazon Redshift es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

En el ejemplo siguiente se proporciona definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Muestra cómo copiar datos de Amazon Redshift a almacenamiento de blobs de Windows Azure. Sin embargo, los datos se pueden copiar a cualquiera de los receptores indicados [a continuación](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-redshift-to-azure-blob"></a>Ejemplo: Copiar datos de Amazon Redshift a blobs de Windows Azure
En este ejemplo se muestra cómo copiar datos de una base de datos de Amazon Redshift en un almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  
 
El ejemplo tiene las siguientes entidades de fábrica de datos:

- Servicio de tipo [AmazonRedshift](#linked-service-properties)vinculado.
- Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
- Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [RelationalTable](#dataset-type-properties).
- Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [RelationalSource](#copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia datos de un resultado de consulta en Amazon Redshift a un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

**Servicio de Redshift de Amazon vinculado**

    {
        "name": "AmazonRedshiftLinkedService",
        "properties":
        {
            "type": "AmazonRedshift",
            "typeProperties":
            {
                "server": "< The IP address or host name of the Amazon Redshift server >",
                "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
                "database": "<The database name of the Amazon Redshift database>",
                "username": "<username>",
                "password": "<password>"
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

**Conjunto de datos de entrada de Redshift de Amazon**

Establecer **"externo": true** informa al servicio del generador de datos que el conjunto de datos externo para el generador de datos y no se produce en una actividad en el generador de datos. Establecer esta propiedad en true en un conjunto de datos de entrada que no se produce en una actividad de la canalización.

    {
        "name": "AmazonRedshiftInputDataset",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "AmazonRedshiftLinkedService",
            "typeProperties": {
                "tableName": "<Table name>"
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
                "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **RelationalSource** y tipo de **receptor** se establece en **BlobSink**. La consulta SQL especificada para la propiedad de **consulta** selecciona los datos en la última hora para copiar.
    
    {
        "name": "CopyAmazonRedshiftToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonRedshiftInputDataset"
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
                    "name": "AmazonRedshiftToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

La tabla siguiente proporciona la descripción para elementos JSON específicos al servicio Redshift de Amazon vinculado.

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| tipo | La propiedad tipo debe estar establecida en: **AmazonRedshift**. | Sí |
| servidor | Dirección IP o host nombre del servidor Redshift de Amazon. | Sí |
| puerto | El número de puerto TCP que utiliza el servidor de Amazon Redshift para escuchar las conexiones de cliente. | No, el valor de predeterminado: 5439 |
| base de datos | Nombre de la base de datos de Redshift de Amazon. | Sí |
| nombre de usuario | Nombre del usuario que tiene acceso a la base de datos.| Sí |
| contraseña | Contraseña para la cuenta de usuario.| Sí |


## <a name="dataset-type-properties"></a>Propiedades de tipo de conjunto de datos

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección de typeProperties para el conjunto de datos de tipo **RelationalTable** (que incluye el conjunto de datos de Amazon Redshift) tiene las siguientes propiedades

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| nombre de tabla | Nombre de la tabla en la base de datos de Amazon Redshift servicios vinculados hace referencia a. | No (si se especifica la **consulta** de **RelationalSource** ) | 

## <a name="copy-activity-type-properties"></a>Copiar las propiedades del tipo de actividad

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directivas están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección **typeProperties** de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Cuando el origen de la actividad de copia es de tipo **RelationalSource** (que incluye Redshift Amazon) las siguientes propiedades están disponibles en la sección typeProperties:

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| consulta | Uso de la consulta personalizada para leer datos. | Cadena de consulta SQL. Por ejemplo: seleccione * from MyTable. | No (si se especifica el **nombre de tabla** de **conjunto de datos** ) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-amazon-redshift"></a>Asignación de tipo para Redshift de Amazon

Como se mencionó en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , actividad copia realiza las conversiones de tipo automática de tipos de origen para recibir tipos con el enfoque de dos pasos siguientes:

1. Convertir de tipos de origen nativo al tipo de .NET
2. Conversión de tipo de .NET a tipo de lavabo nativo

Al mover los datos a Redshift de Amazon, se usará las siguientes asignaciones de tipos de Amazon Redshift a tipos de .NET.

Tipo de Redshift de Amazon | Tipo basado en .NET
-------------------- | ---------------
SMALLINT | Int16
ENTERO | Int32
BIGINT | Int64
DECIMAL | Decimal
REAL | Solo
PRECISIÓN DOBLE | Doble
VALOR BOOLEANO | Cadena
CHAR | Cadena
VARCHAR | Cadena
FECHA | Fecha y hora
MARCA DE TIEMPO | Fecha y hora
TEXTO | Cadena



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes: 
- [Tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia. 