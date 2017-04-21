<properties 
    pageTitle="Mover los datos de local HDFS | Generador de datos de Azure" 
    description="Obtenga información sobre cómo mover los datos de HDFS locales con Azure datos fábrica." 
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

# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Mover los datos de HDFS local con el generador de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos de un HDFS local a otro almacén de datos. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una visión general de movimiento de datos con la actividad de copia y combinaciones de almacén de datos compatibles.

Generador de datos admite actualmente solo mover datos desde un HDFS local a otro almacena datos, pero no para mover datos desde otro almacena datos a un HDFS local.


## <a name="enabling-connectivity"></a>Habilitar la conectividad
Servicio de generador de datos admite la conexión a HDFS local con Data Management Gateway. Consulte el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway e instrucciones paso a paso sobre la configuración de la puerta de enlace. Usar la puerta de enlace para conectarse a HDFS, incluso si está hospedado en una máquina virtual IaaS de Azure. 

Aunque puede instalar la puerta de enlace en el mismo equipo local o en la máquina virtual de Azure como la HDFS, recomendamos que instale la puerta de enlace en un equipo independiente o Azure IaaS VM. Necesidad de puerta de enlace en otro equipo reduce el conflicto de recursos y mejora el rendimiento. Cuando se instala la puerta de enlace en otro equipo, el equipo podrá tener acceso al equipo con la HDFS. 


## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie datos de HDFS local es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

Los ejemplos siguientes proporcionan definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos desde un HDFS local a un almacenamiento de blobs de Windows Azure. Sin embargo, los datos se pueden copiar a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Ejemplo: Copiar datos de HDFS local a blobs de Windows Azure

Este ejemplo muestra cómo copiar datos desde un HDFS local para el almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  
 
El ejemplo tiene las siguientes entidades de fábrica de datos:

1.  Servicio de tipo [OnPremisesHdfs](#hdfs-linked-service-properties)vinculado.
2.  Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada del tipo de [recurso compartido de archivos](#hdfs-dataset-type-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [FileSystemSource](#hdfs-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia datos de un HDFS local a un blobs de Windows Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

Como primer paso, configure la puerta de enlace de administración de datos. Las instrucciones en el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) . 

**HDFS vinculado servicio** Este ejemplo usa la autenticación de Windows. Vea la sección [HDFS vinculado servicio](#hdfs-linked-service-properties) para distintos tipos de autenticación que se puede usar. 

    {
        "name": "HDFSLinkedService",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
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

**Conjunto de datos de entrada HDFS** Este conjunto de datos se refiere a la carpeta HDFS DataTransfer/UnitTest /. La canalización de copia todos los archivos en esta carpeta en el destino. 

Establecer "externo": "true" informa de servicio del generador de datos que el conjunto de datos, el generador de datos externo y no se produce en una actividad en el generador de datos.
    
    {
        "name": "InputDataset",
        "properties": {
            "type": "FileShare",
            "linkedServiceName": "HDFSLinkedService",
            "typeProperties": {
                "folderPath": "DataTransfer/UnitTest/"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }




**Conjunto de datos de salida de blobs de Windows Azure**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). La ruta de acceso para el blob se evalúa dinámicamente en función de la hora de inicio de la segmentación de datos que se está procesando. La ruta de acceso de la carpeta usa el año, mes, día y partes de horas de la hora de inicio.

    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La canalización contiene una actividad de la copia que está configurado para usar estos conjuntos de datos de entrada y salidas y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **FileSystemSource** y tipo de **receptor** se establece en **BlobSink**. La consulta SQL especificada para la propiedad de **consulta** selecciona los datos en la última hora para copiar.
    
    {
        "name": "pipeline",
        "properties":
        {
            "activities":
            [
                {
                    "name": "HdfsToBlobCopy",
                    "inputs": [ {"name": "InputDataset"} ],
                    "outputs": [ {"name": "OutputDataset"} ],
                    "type": "Copy",
                    "typeProperties":
                    {
                        "source":
                        {
                            "type": "FileSystemSource"
                        },
                        "sink":
                        {
                            "type": "BlobSink"
                        }
                    },
                    "policy":
                    {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "00:05:00"
                    }
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="hdfs-linked-service-properties"></a>Propiedades del servicio de HDFS vinculado

La tabla siguiente proporciona la descripción de los elementos de JSON específicos HDFS vinculado servicio.

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| tipo | La propiedad tipo debe estar establecida en: **Hdfs** | Sí | 
| Dirección URL | Dirección URL para la HDFS | Sí |
| encryptedCredential | [AzureRMDataFactoryEncryptValue de nuevo](https://msdn.microsoft.com/library/mt603802.aspx) el resultado de la credencial de acceso. | No |
| nombre de usuario | Autenticación de nombre de usuario de Windows. | Sí (para la autenticación de Windows)
| contraseña | Contraseña para la autenticación de Windows. | Sí (para la autenticación de Windows)
| authenticationType | Windows, o anónimos. | Sí |
| gatewayName | Nombre de la puerta de enlace que debe usar el servicio de generador de datos para conectarse a la HDFS. | Sí |   

Para obtener más información sobre cómo establecer credenciales para HDFS local, consulte [configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

### <a name="using-anonymous-authentication"></a>Uso de autenticación anónima

    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "userName": "hadoop",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-windows-authentication"></a>Usar autenticación de Windows
    
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }
 


## <a name="hdfs-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos HDFS

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección typeProperties para el conjunto de datos del tipo de **recurso compartido de archivos** (que incluye el conjunto de datos HDFS) tiene las siguientes propiedades

(Propiedad) | Descripción | Obligatorio
-------- | ----------- | --------
folderPath | Ruta de acceso a la carpeta. Ejemplo:`myfolder`<br/><br/>Usar el carácter de escape ' \ ' caracteres especiales en la cadena. Por ejemplo: para folder\subfolder, especifique la carpeta\\\\subcarpeta y para d:\samplefolder, especifique d:\\\\carpetaEjemplo.<br/><br/>Puede combinar esta propiedad con **partitionBy** tener trayectorias de la carpeta en el sector fechas y horas de inicio o finalización. | Sí
nombre de archivo | Especifique el nombre del archivo en la **folderPath** si desea que la tabla para hacer referencia a un archivo en la carpeta específico. Si no especifica ningún valor para esta propiedad, todos los archivos en la carpeta de destino en la tabla.<br/><br/>Cuando no se especifica el nombre de archivo para un conjunto de datos de salida, el nombre del archivo generado será en el siguiente este formato: <br/><br/>Datos. <Guid>.txt (por ejemplo:: Data.0a405f8a 93ff 4c6f b3be f69616f1df7a.txt | No
partitionedBy | partitionedBy puede utilizarse para especificar un folderPath dinámico, nombre de archivo de datos de la serie de tiempo. Ejemplo: folderPath parametrizada, para cada hora de datos. | No
fileFilter | Especificar un filtro que se utilizará para seleccionar un subconjunto de archivos en la folderPath en lugar de todos los archivos. <br/><br/>Valores permitidos son: `*` (varios caracteres) y `?` (carácter).<br/><br/>Ejemplos 1:`"fileFilter": "*.log"`<br/>Ejemplo 2:`"fileFilter": 2014-1-?.txt"`<br/><br/>**Nota**: fileFilter es aplicable para un conjunto de datos entrada de recurso compartido de archivos | No
| formato | Se admiten los siguientes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**y **ParquetFormat**. Establezca la propiedad **tipo** en formato en uno de estos valores. Consulte [Especificar TextFormat](#specifying-textformat), [AvroFormat que especifica](#specifying-avroformat), [JsonFormat que especifica](#specifying-jsonformat), [OrcFormat que especifica](#specifying-orcformat), [ParquetFormat especificar](#specifying-parquetformat) secciones y para obtener más información. Si desea copiar archivos como-está entre stores basados en archivos (copia binario), puede omitir la sección formato de ambas definiciones de conjunto de datos de entrada y salida. | No 
| compresión | Especifique el tipo y el nivel de compresión de los datos. Tipos compatibles son: **GZip**, **Deflate**y **BZip2** y niveles compatibles son: **óptima** y **más rápida**. Actualmente, no se admiten los valores de compresión de datos en **AvroFormat** o **OrcFormat**. Para obtener más información, vea la sección de [compatibilidad con la compresión](#compression-support) .  | No |



> [AZURE.NOTE] nombre de archivo y fileFilter no pueden usarse simultáneamente.


### <a name="using-partionedby-property"></a>Uso de partionedBy (propiedad)

Como se mencionó en la sección anterior, puede especificar un folderPath dinámico, nombre de archivo de datos de la serie de tiempo con partitionedBy. Puede hacerlo con las macros de datos fábrica y la variable del sistema SliceStart, SliceEnd que indican el período de tiempo lógico para un sector de datos determinado. 

Para obtener más información acerca de los conjuntos de datos de serie de tiempo, programación y sectores, vea los artículos [Crear conjuntos de datos](data-factory-create-datasets.md), [programación y ejecución](data-factory-scheduling-and-execution.md)y [Creación de canalizaciones](data-factory-create-pipelines.md) . 

#### <a name="sample-1"></a>Ejemplo 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

En este ejemplo {sector} se reemplaza con el valor de la variable de sistema de datos fábrica SliceStart en el formato (YYYYMMDDHH) especificado. Hace referencia a la SliceStart hora de inicio de la segmentación de datos. La folderPath es diferente para cada sector. Por ejemplo: wikisampledataout/wikidatagateway/2014100103 o wikisampledataout/wikidatagateway/2014100104.

#### <a name="sample-2"></a>Ejemplo 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

En este ejemplo, el año, mes, día y hora de SliceStart se extraen en variables diferentes que se usan las propiedades folderPath y nombre de archivo.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de HDFS

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directivas están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

Para copiar actividad, cuando el origen es de tipo **FileSystemSource** las propiedades siguientes están disponibles en typeProperties sección:

**FileSystemSource** admite las siguientes propiedades:

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| recursivas | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. | Verdadero, Falso (predeterminado)| No |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.

