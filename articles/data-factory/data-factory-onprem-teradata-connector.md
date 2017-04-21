<properties 
    pageTitle="Mover los datos de Teradata | Generador de datos de Azure" 
    description="Obtenga más información sobre el conector de Teradata al servicio de generador de datos que le permite mover los datos de la base de datos de Teradata" 
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

# <a name="move-data-from-teradata-using-azure-data-factory"></a>Mover los datos de Teradata con el generador de datos de Azure

Este artículo describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos de Teradata a otros datos almacenar. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y combinaciones de almacén de datos compatibles.

Generador de datos admite la conexión a orígenes de Teradata local a través de la puerta de enlace de administración de datos. Consulte el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway e instrucciones paso a paso sobre la configuración de la puerta de enlace. 

> [AZURE.NOTE]
Puerta de enlace es necesario incluso si la Teradata se hospeda en una máquina virtual IaaS de Azure. Puede instalar la puerta de enlace en la misma VM IaaS como el almacén de datos o en una máquina virtual diferente como la puerta de enlace puede conectarse a la base de datos. 

Generador de datos admite sólo mover los datos de Teradata a otro almacena datos, no de otro almacena datos para Teradata.

## <a name="installation"></a>Instalación 

Data Management Gateway para conectarse a la base de datos de Teradata, debe instalar el [Proveedor de datos .NET para Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) en el mismo sistema que Data Management Gateway.

> [AZURE.NOTE] Consulte [problemas de puerta de enlace de solución de problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace problemas relacionados con. 

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie datos de Teradata a ninguna el receptor compatibles de almacenamiento de datos es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

En el ejemplo siguiente se proporciona definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos de Teradata al almacenamiento de blobs de Windows Azure. Sin embargo, los datos se pueden copiar a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.   

### <a name="sample-copy-data-from-teradata-to-azure-blob"></a>Ejemplo: Copiar datos de Teradata a blobs de Windows Azure

En este ejemplo se muestra cómo copiar datos de una base de datos de Teradata en un almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  
 
El ejemplo tiene las siguientes entidades de fábrica de datos:

1.  Servicio de tipo [OnPremisesTeradata](data-factory-onprem-teradata-connector.md#teradata-linked-service-properties)vinculado.
2.  Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [RelationalTable](data-factory-onprem-teradata-connector.md#teradata-dataset-type-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
4.  [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [RelationalSource](data-factory-onprem-teradata-connector.md#teradata-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia datos de un resultado de consulta de base de datos de Teradata a un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

Como primer paso, configure la puerta de enlace de administración de datos. Las instrucciones incluidas en el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio de Teradata vinculada:**

    {
        "name": "OnPremTeradataLinkedService",
        "properties": {
            "type": "OnPremisesTeradata",
            "typeProperties": {
                "server": "<server>",
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


**El conjunto de datos de entrada Teradata:**

En el ejemplo se supone que haya creado una tabla "MyTable" en Teradata y contiene una columna denominada "fecha y hora" para los datos de la serie de tiempo.

Establecer "externo": verdadero informa servicio del generador de datos que la tabla es el generador de datos externa y no se produce en una actividad en el generador de datos.

    {
        "name": "TeradataDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremTeradataLinkedService",
            "typeProperties": {
            },
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
        "name": "AzureBlobTeradataDataSet",
        "properties": {
            "published": false,
            "location": {
                "type": "AzureBlobLocation",
                "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                ],
                "linkedServiceName": "AzureStorageLinkedService"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Canalización con actividad de copia:**

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **RelationalSource** y tipo de **receptor** se establece en **BlobSink**. La consulta SQL especificada para la propiedad de **consulta** selecciona los datos en la última hora para copiar.

    {
        "name": "CopyTeradataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "TeradataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobTeradataDataSet"
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
                    "name": "TeradataToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z",
            "isPaused": false
        }
    }


## <a name="teradata-linked-service-properties"></a>Propiedades del servicio de Teradata vinculado

La tabla siguiente proporciona la descripción de los elementos JSON específicos al servicio de Teradata vinculado. 

(Propiedad) | Descripción | Obligatorio
-------- | ----------- | --------
tipo | La propiedad tipo debe estar establecida en: **OnPremisesTeradata** | Sí
servidor | Nombre del servidor de Teradata. | Sí
authenticationType | Tipo de autenticación que se usa para conectarse a la base de datos de Teradata. Los valores posibles son: anónimo, Basic y Windows. | Sí
nombre de usuario | Especifique el nombre de usuario si está utilizando autenticación básica o Windows. | No 
contraseña | Especifique la contraseña para la cuenta de usuario que haya especificado para el nombre de usuario. | No 
gatewayName | Nombre de la puerta de enlace que debe usar el servicio de generador de datos para conectarse a la base de datos de Teradata local. | Sí

Para obtener más información sobre cómo establecer credenciales para un origen de datos de Teradata local, consulte [configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="teradata-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos de Teradata

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. En este momento no hay ninguna propiedad de tipo compatible para el conjunto de datos de Teradata. 


## <a name="teradata-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de Teradata

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directivas están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Cuando el origen es de tipo **RelationalSource** (que incluye Teradata) las siguientes propiedades están disponibles en la sección **typeProperties** :

(Propiedad) | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
consulta | Uso de la consulta personalizada para leer datos. | Cadena de consulta SQL. Por ejemplo: seleccione * from MyTable. | Sí

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-teradata"></a>Asignación de tipo para Teradata

Como se mencionó en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , la actividad de copia realiza las conversiones de tipo automática de tipos de origen para recibir tipos con el siguiente enfoque paso 2:

1. Convertir de tipos de origen nativo al tipo de .NET
2. Conversión de tipo de .NET a tipo de lavabo nativo

Al mover datos a Teradata, se usan las siguientes asignaciones de tipo de Teradata al tipo de .NET.

Tipo de base de datos de Teradata | Tipo de .NET framework
----------------- | ---------------------------
Char | Cadena
CLOB | Cadena
Gráfico | Cadena
VarChar | Cadena
VarGraphic | Cadena
BLOB | Byte]
Byte | Byte]
VarByte | Byte]
BigInt | Int64
ByteInt | Int16
Decimal | Decimal
Doble | Doble
Entero | Int32
Número | Doble
SmallInt | Int16
Fecha | Fecha y hora
Hora | Intervalo de tiempo
Tiempo de zona horaria | Cadena
Marca de tiempo | Fecha y hora
Marca de tiempo con la zona horaria | DateTimeOffset
Día de intervalo | Intervalo de tiempo
Día de intervalo en hora | Intervalo de tiempo
Día de intervalo minuto | Intervalo de tiempo
Día del intervalo de segundo. | Intervalo de tiempo
Hora de intervalo | Intervalo de tiempo
Intervalo hora, minuto | Intervalo de tiempo
Hora de intervalo de segundo. | Intervalo de tiempo
Minuto de intervalo | Intervalo de tiempo
Minuto de intervalo de segundo. | Intervalo de tiempo
Intervalo de segundo | Intervalo de tiempo
Año de intervalo | Cadena
Año de intervalo al mes. | Cadena
Mes de intervalo | Cadena
Period(Date) | Cadena
Period(Time) | Cadena
Período (hora con la zona horaria) | Cadena
Period(timestamp) | Cadena
Período (marca de tiempo con la zona horaria) | Cadena
XML | Cadena

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.