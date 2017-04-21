<properties 
    pageTitle="Mover los datos de fuentes de OData | Generador de datos de Azure" 
    description="Obtenga información sobre cómo mover los datos de fuentes de OData con el generador de datos de Azure." 
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
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Mover los datos de origen de OData unos con el generador de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos de una fuente de OData a otro almacén de datos. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y combinaciones de almacén de datos compatibles.

> [AZURE.NOTE] Esta compatibilidad de conector de OData copiar datos de ambas nube OData y fuentes de OData en local. En el segundo caso, debe instalar Data Management Gateway. Consulte el artículo de [mover datos entre local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información acerca de Data Management Gateway.

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie los datos de una fuente de OData es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

Los ejemplos siguientes proporcionan definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos desde una fuente de OData a un almacenamiento de blobs de Windows Azure. Sin embargo, los datos se pueden copiar a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.


## <a name="sample-copy-data-from-odata-source-to-azure-blob"></a>Ejemplo: Copiar datos de origen de OData a blobs de Windows Azure

Este ejemplo muestra cómo copiar datos de una fuente de OData en el almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  
 
El ejemplo tiene las siguientes entidades de fábrica de datos:

1.  Un servicio vinculado de tipo [OData](#odata-linked-service-properties).
2.  Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [ODataResource](#odata-dataset-type-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [RelationalSource](#odata-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos de la consulta a una fuente de OData a un blobs de Windows Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

**Servicio de vinculado de OData** Este ejemplo usa la autenticación básica. Vea la sección [OData vinculado servicio](#odata-linked-service-properties) para distintos tipos de autenticación que se puede usar. 

    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
                "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
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

**Conjunto de datos de entrada de OData**

Establecer "externo": "true" informa de servicio del generador de datos que el conjunto de datos, el generador de datos externo y no se produce en una actividad en el generador de datos.
    
    {
        "name": "ODataDataset",
        "properties": 
        {
            "type": "ODataResource",
            "typeProperties": 
            {
                "path": "Products" 
            },
            "linkedServiceName": "ODataLinkedService",
            "structure": [],
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3               
            }
        }
    }

Definición y especificar la **ruta de acceso** en el conjunto de datos es opcional. 


**Conjunto de datos de salida de blobs de Windows Azure**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). La ruta de acceso para el blob se evalúa dinámicamente en función de la hora de inicio de la segmentación de datos que se está procesando. La ruta de acceso de la carpeta usa el año, mes, día y partes de horas de la hora de inicio.

    {
        "name": "AzureBlobODataDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **RelationalSource** y tipo de **receptor** se establece en **BlobSink**. La consulta SQL especificada para la propiedad de **consulta** selecciona los datos más recientes (más recientes) de la fuente de OData.
    
    {
        "name": "CopyODataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "?$select=Name, Description&$top=5",
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "ODataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobODataDataSet"
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
                    "name": "ODataToBlob"
                }
            ],
            "start": "2016-02-01T18:00:00Z",
            "end": "2016-02-03T19:00:00Z"
        }
    }


Especificar la **consulta** en la definición de canalización es opcional. Es la **dirección URL** que usa el servicio de generador de datos para recuperar datos: dirección URL especificada en el servicio vinculado (obligatorio) + ruta de acceso especificada en el conjunto de datos (opcional) + consulta en la canalización (opcional). 

## <a name="odata-linked-service-properties"></a>Propiedades del servicio de vinculado de OData

La tabla siguiente proporciona la descripción de los elementos JSON específicos al servicio de OData vinculado.

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| tipo | La propiedad tipo debe estar establecida en: **OData** | Sí |
| dirección URL| Dirección URL del servicio de OData. | Sí |
| authenticationType | Tipo de autenticación que se usa para conectarse al origen de OData. <br/><br/> Para la nube OData, los valores posibles son anónimo y Basic. Para OData local, los valores posibles son anónima, básica y Windows. | Sí | 
| nombre de usuario | Especifique el nombre de usuario si está utilizando la autenticación básica. | Sí (solo si está utilizando la autenticación básica) | 
| contraseña | Especifique la contraseña para la cuenta de usuario que haya especificado para el nombre de usuario. | Sí (solo si está utilizando la autenticación básica) | 
| gatewayName | Nombre de la puerta de enlace que debe utilizar el servicio de generador de datos para conectar con el servicio de OData en local. Especificar solo si va a copiar datos de origen de OData local. | No |

### <a name="using-basic-authentication"></a>Mediante la autenticación básica

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### <a name="using-anonymous-authentication"></a>Uso de autenticación anónima
    
    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Mediante la autenticación de Windows acceso a la fuente de OData local

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
                "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }



## <a name="odata-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos de OData

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección de typeProperties para el conjunto de datos de tipo **ODataResource** (que incluye el conjunto de datos de OData) tiene las siguientes propiedades

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| ruta de acceso | Ruta de acceso al recurso de OData | No | 

## <a name="odata-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de OData

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directiva están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Cuando el origen es de tipo **RelationalSource** (que incluye OData) las siguientes propiedades están disponibles en la sección typeProperties:

| (Propiedad) | Descripción | Ejemplo | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| consulta | Uso de la consulta personalizada para leer datos. | "? $select = nombre, descripción y $top = 5" | No | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odata"></a>Asignación de tipos de OData

Como se mencionó en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , actividad copia realiza las conversiones de tipo automática de tipos de origen para recibir tipos con el siguiente enfoque paso 2:

1. Convertir de tipos de origen nativo al tipo de .NET
2. Conversión de tipo de .NET a tipo de lavabo nativo

Cuando se almacena el movimiento de datos de datos de OData, se asignan los tipos de datos de OData a tipos de .NET.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.

