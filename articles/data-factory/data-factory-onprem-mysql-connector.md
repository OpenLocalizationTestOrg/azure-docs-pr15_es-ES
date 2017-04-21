<properties 
    pageTitle="Mover los datos de MySQL | Generador de datos de Azure" 
    description="Obtenga información sobre cómo mover los datos de la base de datos MySQL con el generador de datos de Azure." 
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

# <a name="move-data-from-mysql-using-azure-data-factory"></a>Mover los datos de MySQL con el generador de datos de Azure

Este artículo describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos de MySQL a otros datos almacenar. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y combinaciones de almacén de datos compatibles.

Servicio de generador de datos admite la conexión a orígenes de MySQL locales con Data Management Gateway. Consulte el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway e instrucciones paso a paso sobre la configuración de la puerta de enlace. 

> [AZURE.NOTE] Puerta de enlace es necesario incluso si la base de datos MySQL está alojado en un equipo virtual de Azure IaaS (VM). Puede instalar la puerta de enlace en la misma VM como el almacén de datos o en una máquina virtual diferente como la puerta de enlace puede conectarse a la base de datos.  

Generador de datos admite actualmente solo mover los datos de MySQL a otro almacena datos, pero no para mover datos desde otro almacena datos a MySQL.

## <a name="installation"></a>Instalación 
Data Management Gateway para conectarse a la base de datos MySQL, debe instalar el [Conector o red de MySQL 6.6.5 de Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) en el mismo sistema que Data Management Gateway.

> [AZURE.NOTE] Consulte [problemas de puerta de enlace de solución de problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para obtener sugerencias sobre solución de problemas de conexión o puerta de enlace problemas relacionados con. 

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie datos de una base de datos MySQL a cualquier el receptor compatibles de almacenamiento de datos es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

En el ejemplo siguiente se proporciona definiciones de JSON de ejemplo que puede usar para crear un proceso. Para obtener un tutorial completo con instrucciones paso a paso, vea el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) . Datos se pueden copiar a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.   

## <a name="sample-copy-data-from-mysql-to-azure-blob"></a>Ejemplo: Copiar datos de MySQL a blobs de Windows Azure
En este ejemplo se muestra cómo copiar datos de una base de datos MySQL local a un almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  

> [AZURE.IMPORTANT] Este ejemplo proporciona fragmentos JSON. No incluye instrucciones paso a paso para crear el generador de datos. Consulte el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones paso a paso. 
 
El ejemplo tiene las siguientes entidades de fábrica de datos:

1.  Servicio de tipo [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties)vinculado.
2.  Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia datos de un resultado de consulta de base de datos MySQL a un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

Como primer paso, configure la puerta de enlace de administración de datos. Las instrucciones incluidas en el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) . 

**Servicio MySQL vinculado**

    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
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

**Conjunto de datos MySQL entrada**

En el ejemplo se supone que haya creado una tabla "MyTable" en MySQL y contiene una columna denominada "timestampcolumn" para los datos de la serie de tiempo.

Establecer "externo": "true" informa de servicio del generador de datos que la tabla, el generador de datos externo y no se produce en una actividad en el generador de datos.
    
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
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



**Conjunto de datos de salida de blobs de Windows Azure**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). La ruta de acceso para el blob se evalúa dinámicamente en función de la hora de inicio de la segmentación de datos que se está procesando. La ruta de acceso de la carpeta usa el año, mes, día y partes de horas de la hora de inicio.

    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
        "name": "CopyMySqlToBlob",
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
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="mysql-linked-service-properties"></a>Propiedades del servicio MySQL vinculado

La tabla siguiente proporciona la descripción para elementos de JSON específicos servicio MySQL vinculado.

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| tipo | La propiedad tipo debe estar establecida en: **OnPremisesMySql** | Sí |
| servidor | Nombre del servidor MySQL. | Sí |
| base de datos | Nombre de la base de datos MySQL. | Sí | 
| esquema  | Nombre del esquema de la base de datos. | No | 
| authenticationType | Tipo de autenticación que se usa para conectarse a la base de datos MySQL. Los valores posibles son: anónimo, Basic y Windows. | Sí | 
| nombre de usuario | Especifique el nombre de usuario si está utilizando autenticación básica o Windows. | No | 
| contraseña | Especifique la contraseña para la cuenta de usuario que haya especificado para el nombre de usuario. | No | 
| gatewayName | Nombre de la puerta de enlace que debe usar el servicio de generador de datos para conectarse a la base de datos MySQL local. | Sí |

Para obtener más información sobre cómo establecer credenciales para un origen de datos MySQL local, consulte [configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="mysql-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos MySQL

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección de typeProperties para el conjunto de datos de tipo **RelationalTable** (que incluye el conjunto de datos MySQL) tiene las siguientes propiedades

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| nombre de tabla | Nombre de la tabla de la instancia de base de datos MySQL servicios vinculados hace referencia a. | No (si se especifica la **consulta** de **RelationalSource** ) | 

## <a name="mysql-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de MySQL

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, tablas de entrada y salidas, son directivas están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección **typeProperties** de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Cuando el origen de actividad de copia es de tipo **RelationalSource** (que incluye MySQL) las siguientes propiedades están disponibles en la sección typeProperties:

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| consulta | Uso de la consulta personalizada para leer datos. | Cadena de consulta SQL. Por ejemplo: seleccione * from MyTable. | No (si se especifica el **nombre de tabla** de **conjunto de datos** ) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-mysql"></a>Asignación de tipo para MySQL

Como se mencionó en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , actividad copia realiza las conversiones de tipo automática de tipos de origen para recibir tipos con el enfoque de dos pasos siguientes:

1. Convertir de tipos de origen nativo al tipo de .NET
2. Conversión de tipo de .NET a tipo de lavabo nativo

Al mover datos a MySQL, se usan las siguientes asignaciones de tipos de MySQL a tipos de .NET.

| Tipo de base de datos MySQL | Tipo de .NET framework |
| ------------------- | ------------------- | 
| bigint sin firmar | Decimal |
| bigint | Int64 |
| bit | Decimal |
| BLOB | Byte] |
| BOOL |  Valor booleano | 
| Char | Cadena |
| fecha | Fecha y hora |
| fecha y hora | Fecha y hora |
| decimal | Decimal |
| precisión doble | Doble |
| doble | Doble |
| enumeración | Cadena |
| flotante | Solo |
| int sin signo | Int64 |
| int | Int32 |
| entero sin signo | Int64 |
| entero | Int32 | 
| varbinary larga | Byte] |
| varchar largo | Cadena |
| longblob | Byte] |
| LONGTEXT | Cadena | 
| mediumblob |  Byte] | 
| mediumint sin firmar | Int64 |
| mediumint | Int32 | 
| mediumtext | Cadena |
| numérico | Decimal |
| real |  Doble |
| establecer | Cadena |
| smallint sin firmar | Int32 |
| smallint | Int16 |
| texto | Cadena |
| hora | Intervalo de tiempo |
| marca de tiempo | Fecha y hora |
| tinyblob | Byte] |
| tinyint sin signo |  Int16 | 
| tinyint | Int16 |
| tinytext | Cadena | 
| varchar | Cadena | 
| año | Int | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.

