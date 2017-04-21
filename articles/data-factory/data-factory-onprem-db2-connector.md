<properties 
    pageTitle="Mover los datos de DB2 | Generador de datos de Azure" 
    description="Obtenga más información sobre cómo mover los datos de la base de datos de DB2 con el generador de datos de Azure" 
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
    ms.date="09/06/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-db2-using-azure-data-factory"></a>Mover los datos de DB2 con el generador de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos de DB2 a otros datos almacenar. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y combinaciones de almacén de datos compatibles.

Generador de datos admite la conexión a orígenes de DB2 locales con Data Management Gateway. Consulte el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway e instrucciones paso a paso sobre la configuración de la puerta de enlace. 

> [AZURE.NOTE]
Usar la puerta de enlace para conectarse a DB2, incluso si está hospedado en máquinas virtuales de IaaS de Azure. Si está intentando conectarse a una instancia de DB2 alojado en la nube, también puede instalar la instancia de puerta de enlace en la VM IaaS.

Generador de datos admite actualmente solo mover datos desde DB2 a otro almacena datos, no desde otra almacena datos a DB2. 

## <a name="installation"></a>Instalación 

Data Management Gateway proporciona un controlador de DB2 integrado que admite lo siguiente: 

- SQLAM 9 / 10 / 11
- DB2 para LUW (Linux, Unix y Windows)
- DB2 para z/OS y DB2 para (también conocido como como / 400)

Por lo tanto, ya no necesita instalar manualmente los controladores al copiar datos de DB2.

> [AZURE.NOTE] Consulte [problemas de puerta de enlace de solución de problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace problemas relacionados con. 

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie datos de una base de datos DB2 es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

Los ejemplos siguientes proporcionan definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos de la base de datos DB2 y el almacenamiento de blobs de Windows Azure. Sin embargo, los datos se pueden copiar a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>Ejemplo: Copiar datos de DB2 a blobs de Windows Azure

En este ejemplo se muestra cómo copiar datos de una base de datos de DB2 local a un almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  
 
El ejemplo tiene las siguientes entidades de fábrica de datos:

1.  Servicio de tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties)vinculado.
2.  Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado. 
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
5.  [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

El ejemplo copia datos de un resultado de consulta en una base de datos DB2 a un blobs de Windows Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

Como primer paso, instale y configure una data management gateway. Las instrucciones incluidas en el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio de DB2 vinculada:**

    {
        "name": "OnPremDb2LinkedService",
        "properties": {
            "type": "OnPremisesDb2",
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

**El conjunto de datos de entrada DB2:**

En el ejemplo se supone que haya creado una tabla "MyTable" en DB2 y contiene una columna denominada "fecha y hora" para los datos de la serie de tiempo.

Establecer "externo": verdadero informa servicio del generador de datos que este conjunto de datos externo para el generador de datos y no se produce en una actividad en el generador de datos. Observe que el **tipo** se establece en **RelationalTable**. 

    {
        "name": "Db2DataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremDb2LinkedService",
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
        "name": "AzureBlobDb2DataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **RelationalSource** y tipo de **receptor** se establece en **BlobSink**. La consulta SQL especificada para la propiedad de **consulta** selecciona los datos de la tabla Pedidos.

    {
        "name": "CopyDb2ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from \"Orders\""
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Db2DataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobDb2DataSet"
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
                    "name": "Db2ToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="db2-linked-service-properties"></a>Propiedades del servicio de DB2 vinculada

La tabla siguiente proporciona la descripción para elementos JSON específicos al servicio de DB2 vinculado. 

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| tipo | La propiedad tipo debe estar establecida en: **OnPremisesDB2** | Sí |
| servidor | Nombre del servidor de DB2. | Sí |
| base de datos | Nombre de la base de datos DB2. | Sí |
| esquema | Nombre del esquema de la base de datos. El nombre del esquema distingue mayúsculas de minúsculas. | No |
| authenticationType | Tipo de autenticación que se usa para conectarse a la base de datos DB2. Los valores posibles son: anónimo, Basic y Windows. | Sí |
| nombre de usuario | Especifique el nombre de usuario si está utilizando autenticación básica o Windows. | No |
| contraseña | Especifique la contraseña para la cuenta de usuario que haya especificado para el nombre de usuario. | No |
| gatewayName | Nombre de la puerta de enlace que debe usar el servicio de generador de datos para conectarse a la base de datos de DB2 local. | Sí |

Para obtener más información sobre cómo establecer credenciales para un origen de datos local DB2, vea [establecer credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) . 


## <a name="db2-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos de DB2

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección typeProperties es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección de typeProperties para el conjunto de datos de tipo RelationalTable (que incluye el conjunto de datos de DB2) tiene las siguientes propiedades.

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| nombre de tabla | Nombre de la tabla de la instancia de base de datos DB2 vinculada servicio hace referencia a. El nombre de tabla distingue mayúsculas de minúsculas. | No (si se especifica la **consulta** de **RelationalSource** ) |

## <a name="db2-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de DB2

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directivas están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Para copiar actividad, cuando el origen es de tipo **RelationalSource** (que incluye DB2) las propiedades siguientes están disponibles en typeProperties sección:


| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------- | -------------- |
| consulta | Uso de la consulta personalizada para leer datos. | Cadena de consulta SQL. Por ejemplo: `"query": "select * from "MySchema"."MyTable""`. | No (si se especifica el **nombre de tabla** de **conjunto de datos** )|

> [AZURE.NOTE] Nombres de tabla y esquema distinguen mayúsculas de minúsculas. Incluya los nombres de "" (comillas dobles) en la consulta.  

**Ejemplo:**

    "query": "select * from "DB2ADMIN"."Customers""


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>Asignación de tipo para DB2
Como se mencionó en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza las conversiones de tipo automática de tipos de origen para recibir tipos con el siguiente enfoque paso 2:

1. Convertir de tipos de origen nativo al tipo de .NET
2. Conversión de tipo de .NET a tipo de lavabo nativo

Al mover datos a DB2, se usan las siguientes asignaciones de tipo de DB2 al tipo de .NET.

Tipo de base de datos DB2 | Tipo de .NET framework 
----------------- | ------------------- 
SmallInt | Int16
Entero | Int32
BigInt | Int64
Real | Solo
Doble | Doble
Hacer flotante | Doble
Decimal | Decimal
DecimalFloat | Decimal
Numérico | Decimal
Fecha | Fecha y hora
Hora | Intervalo de tiempo
Marca de tiempo | Fecha y hora
XML | Byte]
Char | Cadena
VarChar | Cadena
LongVarChar | Cadena
DB2DynArray | Cadena
Binario | Byte]
VarBinary | Byte]
LongVarBinary | Byte]
Gráfico | Cadena
VarGraphic | Cadena
LongVarGraphic | Cadena
CLOB | Cadena
BLOB | Byte]
DbClob | Cadena
SmallInt | Int16
Entero | Int32
BigInt | Int64
Real | Solo
Doble | Doble
Hacer flotante | Doble
Decimal | Decimal
DecimalFloat | Decimal
Numérico | Decimal
Fecha | Fecha y hora
Hora | Intervalo de tiempo
Marca de tiempo | Fecha y hora
XML | Byte]
Char | Cadena


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.


