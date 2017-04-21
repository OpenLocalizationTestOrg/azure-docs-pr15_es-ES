<properties 
    pageTitle="Mover los datos de Sybase | Generador de datos de Azure" 
    description="Obtenga información sobre cómo mover los datos de la base de datos de Sybase mediante el generador de datos de Azure." 
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
    ms.date="09/20/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-sybase-using-azure-data-factory"></a>Mover los datos de Sybase con el generador de datos de Azure 

En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos de Sybase a otro almacén de datos. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y combinaciones de almacén de datos compatibles.

Servicio de generador de datos admite la conexión a orígenes de Sybase locales con Data Management Gateway. Consulte el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway e instrucciones paso a paso sobre la configuración de la puerta de enlace. 

> [AZURE.NOTE]
> Puerta de enlace es necesario incluso si la base de datos de Sybase se hospeda en una máquina virtual IaaS de Azure. Puede instalar la puerta de enlace en la misma VM IaaS como el almacén de datos o en una máquina virtual diferente como la puerta de enlace puede conectarse a la base de datos. 

Generador de datos admite actualmente solo mover datos de Sybase a otro almacena datos, no de otro almacena datos a Sybase.

## <a name="installation"></a>Instalación

Data Management Gateway para conectarse a la base de datos de Sybase, debe instalar el [proveedor de datos de Sybase](http://go.microsoft.com/fwlink/?linkid=324846) en el mismo sistema que Data Management Gateway.

> [AZURE.NOTE] Consulte [problemas de puerta de enlace de solución de problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace problemas relacionados con. 

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie datos de una base de datos de Sybase a ninguna el receptor compatibles de almacenamiento de datos es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

En el ejemplo siguiente se proporciona definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos desde base de datos de Sybase a almacenamiento de blobs de Windows Azure. Sin embargo, los datos se pueden copiar a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.   

## <a name="sample-copy-data-from-sybase-to-azure-blob"></a>Ejemplo: Copiar datos de Sybase a blobs de Windows Azure
En este ejemplo se muestra cómo copiar datos desde una base de datos de Sybase a un almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  
 
El ejemplo tiene las siguientes entidades de fábrica de datos:

1.  Servicio de tipo [OnPremisesSybase](data-factory-onprem-sybase-connector.md#sybase-linked-service-properties)vinculado.
2.  Un servicio querido del tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [RelationalTable](data-factory-onprem-sybase-connector.md#sybase-dataset-type-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [RelationalSource](data-factory-onprem-sybase-connector.md#sybase-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia datos de un resultado de consulta de base de datos de Sybase a un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

Como primer paso, configure la puerta de enlace de administración de datos. Las instrucciones incluidas en el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio de Sybase vinculada:**

    {
        "name": "OnPremSybaseLinkedService",
        "properties": {
            "type": "OnPremisesSybase",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }

**Almacenamiento de blobs de Windows Azure vinculado servicio:**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorageLinkedService",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
            }
        }
    }


**El conjunto de datos de entrada Sybase:**

En el ejemplo se supone que haya creado una tabla "MyTable" en Sybase y contiene una columna denominada "fecha y hora" para los datos de la serie de tiempo.

Establecer "externo": verdadero informa servicio del generador de datos que este conjunto de datos externo para el generador de datos y no se produce en una actividad en el generador de datos. Observe que el **tipo** de servicio vinculado se establece en: **RelationalTable**. 
    
    {
        "name": "SybaseDataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremSybaseLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }


**Conjunto de datos de salida de blobs de Windows Azure:**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). La ruta de acceso para el blob se evalúa dinámicamente en función de la hora de inicio de la segmentación de datos que se está procesando. La ruta de acceso de la carpeta usa el año, mes, día y partes de horas de la hora de inicio.

    {
        "name": "AzureBlobSybaseDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Canalización con actividad de copia:**

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **RelationalSource** y tipo de **receptor** se establece en **BlobSink**. La consulta SQL especificada para la propiedad de **consulta** selecciona los datos desde el administrador. Tabla pedidos en la base de datos.


    {
        "name": "CopySybaseToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from DBA.Orders"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "SybaseDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobSybaseDataSet"
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
                    "name": "SybaseToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="sybase-linked-service-properties"></a>Propiedades del servicio de Sybase vinculado

La tabla siguiente proporciona la descripción de los elementos JSON específicos al servicio de Sybase vinculado.

(Propiedad) | Descripción | Obligatorio
-------- | ----------- | --------
tipo | La propiedad tipo debe estar establecida en: **OnPremisesSybase** | Sí
servidor | Nombre del servidor de Sybase. | Sí
base de datos | Nombre de la base de datos de Sybase. | Sí 
esquema  | Nombre del esquema de la base de datos. | No
authenticationType | Tipo de autenticación que se usa para conectarse a la base de datos de Sybase. Los valores posibles son: anónimo, Basic y Windows. | Sí
nombre de usuario | Especifique el nombre de usuario si está utilizando autenticación básica o Windows. | No
contraseña | Especifique la contraseña para la cuenta de usuario que haya especificado para el nombre de usuario. |  No
gatewayName | Nombre de la puerta de enlace que debe usar el servicio de generador de datos para conectarse a la base de datos de Sybase local. | Sí 

Para obtener más información sobre cómo establecer credenciales para un origen de datos de Sybase local, consulte [configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="sybase-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos de Sybase

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección typeProperties es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección de **typeProperties** para el conjunto de datos de tipo **RelationalTable** (que incluye el conjunto de datos de Sybase) tiene las siguientes propiedades:

(Propiedad) | Descripción | Obligatorio
-------- | ----------- | --------
nombre de tabla | Nombre de la tabla de la instancia de base de datos de Sybase servicios vinculados hace referencia a. | No (si se especifica la **consulta** de **RelationalSource** )

## <a name="sybase-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de Sybase 

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo [crear tuberías](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directiva están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Cuando el origen es de tipo **RelationalSource** (que incluye Sybase) las siguientes propiedades están disponibles en la sección **typeProperties** :

(Propiedad) | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
consulta | Uso de la consulta personalizada para leer datos. | Cadena de consulta SQL. Por ejemplo: seleccione * from MyTable. | No (si se especifica el **nombre de tabla** de **conjunto de datos** )

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-sybase"></a>Asignación de tipos de Sybase

Como se mencionó en el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza las conversiones de tipo automática de tipos de origen para recibir tipos con el siguiente enfoque paso 2:

1. Convertir de tipos de origen nativo al tipo de .NET
2. Conversión de tipo de .NET a tipo de lavabo nativo

Sybase admite T SQL y tipos de T SQL. Para una tabla de asignación de tipos de sql en tipo de .NET, vea [Conector de SQL Azure](data-factory-azure-sql-connector.md) artículo.

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.