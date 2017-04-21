<properties 
    pageTitle="Mover los datos a o de tabla de Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo mover los datos de almacenamiento de tablas de Azure con el generador de datos de Azure." 
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
    ms.date="09/13/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Mover los datos de tabla de Azure con el generador de datos de Azure

En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos de tabla de Azure desde o a otro almacén de datos. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos y combinaciones de almacén de datos compatibles con la actividad de copia.

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie los datos de almacenamiento de tablas de Azure es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

Los ejemplos siguientes proporcionan definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos a y desde el almacenamiento de tablas de Azure y base de datos de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** desde cualquiera de los orígenes a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.

## <a name="sample-copy-data-from-azure-table-to-azure-blob"></a>Ejemplo: Copiar datos de tabla de Azure en blobs de Windows Azure

Se muestra en el ejemplo siguiente:

1.  Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (que se usa para tabla & blob).
2.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada del tipo de [Azure](#azure-table-dataset-type-properties).
3.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
3.  [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [AzureTableSource](#azure-table-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

El ejemplo copia los datos que pertenecen a la partición predeterminada en una tabla de Azure a un blob cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos.

**Servicio de almacenamiento de Azure vinculada:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Generador de datos de Azure es compatible con dos tipos de servicios de almacenamiento de Azure vinculado: **AzureStorage** y **AzureStorageSas**. Para el primero, especifique la cadena de conexión que incluye la clave de cuenta y por la posterior, especifique el Uri de firma de acceso compartido (SA). Para obtener información detallada, consulte la sección [Servicios vinculados](#linked-services) .  

**Azure dataset de entrada de tabla:**

En el ejemplo se supone que haya creado una tabla "MyTable" en la tabla de Azure.
 
Establecer "externo": "true" informa de servicio del generador de datos que el conjunto de datos, el generador de datos externo y no se produce en una actividad en el generador de datos.

    {
      "name": "AzureTableInput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
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

**Conjunto de datos de salida de blobs de Windows Azure:**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). La ruta de acceso para el blob se evalúa dinámicamente en función de la hora de inicio de la segmentación de datos que se está procesando. La ruta de acceso de la carpeta usa el año, mes, día y partes de horas de la hora de inicio. 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Canalización con la actividad de copia:**

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **AzureTableSource** y tipo de **receptor** se establece en **BlobSink**. La consulta SQL especificada en la propiedad **AzureTableSourceQuery** selecciona los datos de la partición predeterminada cada hora para copiar.

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureTabletoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                        {
                            "name": "AzureTableInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureTableSource",
                            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },              
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
             ]  
        }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-table"></a>Ejemplo: Copiar datos de blobs de Windows Azure a tabla de Azure

Se muestra en el ejemplo siguiente:

1.  Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (que se usa para tabla & blob)
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) del tipo de [Azure](#azure-table-dataset-type-properties). 
4.  [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) y [AzureTableSink](#azure-table-copy-activity-type-properties). 


Las copias de ejemplo serie de tiempo de datos de un Azure blob a un Azure tabla cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos.

**Servicio de almacenamiento de Azure (de tabla de Azure y Blob) vinculado:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Generador de datos de Azure es compatible con dos tipos de servicios de almacenamiento de Azure vinculado: **AzureStorage** y **AzureStorageSas**. Para el primero, especifique la cadena de conexión que incluye la clave de cuenta y por la posterior, especifique el Uri de firma de acceso compartido (SA). Para obtener información detallada, consulte la sección [Servicios vinculados](#linked-services) . 

**Dataset de entrada de blobs de Windows Azure:**

Datos se ha seleccionado en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). El nombre de ruta de acceso y la carpeta para el blob se evalúan dinámicamente en función de la hora de inicio de la segmentación de datos que se está procesando. Año, mes y parte del día de la hora de inicio utiliza la ruta de acceso y nombre de archivo usa el parte de horas de la hora de inicio. "externo": "true" configuración informa servicio del generador de datos que el conjunto de datos externo para el generador de datos y no se produce en una actividad en el generador de datos.
    
    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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

**Tablas de Azure de salida del conjunto de datos:**

El ejemplo copia datos en una tabla denominada "MyTable" en la tabla de Azure. Crear una tabla de Azure con el mismo número de columnas como se esperaba el archivo CSV de Blob que contengan. Se agregan nuevas filas a la tabla cada hora. 

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Canalización con la actividad de copia:**

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **BlobSource** y tipo de **receptor** se establece en **AzureTableSink**. 


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureTableOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "AzureTableSink",
                "writeBatchSize": 100,
                "writeBatchTimeout": "01:00:00"
              }
            },
            "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },                      
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

## <a name="linked-services"></a>Servicios vinculados
Existen dos tipos de servicios vinculados que puede usar para vincular un almacenamiento de blobs de Windows Azure a un generador de datos de Azure. Son: **AzureStorage** vinculado servicios y **AzureStorageSas** vinculado. El servicio de almacenamiento de Azure vinculado proporciona el generador de datos con acceso global para el almacenamiento de Azure. Mientras que la SA de almacenamiento de Azure (firma de acceso compartido) vinculado servicio proporciona el generador de datos con acceso restringido o de límite de tiempo para el almacenamiento de Azure. No existen otras diferencias entre estos dos servicios vinculados. Elija el servicio vinculado que se adapte a sus necesidades. Las siguientes secciones proporcionan más detalles sobre estos dos servicios vinculados.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-table-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos de tabla de Azure

Para obtener una lista completa de secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección typeProperties es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección **typeProperties** para el conjunto de datos del tipo de **Azure** tiene las siguientes propiedades.

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| nombre de tabla | Nombre de la tabla de la instancia de base de datos de Azure tabla vinculada servicio hace referencia a. | Sí. Cuando se especifica un nombre de tabla sin un azureTableSourceQuery, todos los registros de la tabla se copian en el destino. Si también se especifica una azureTableSourceQuery, registros de la tabla que satisface la consulta se copian en el destino. |

### <a name="schema-by-data-factory"></a>Esquema de generador de datos
Para almacena datos sin esquema como tabla de Azure, el servicio de datos fábrica deduce el esquema de una de las siguientes maneras:

1.  Si especifica la estructura de datos utilizando la propiedad de la **estructura** de la definición de conjunto de datos, el servicio de generador de datos respeta la estructura como el esquema. En este caso, si una fila contiene un valor de una columna, se proporciona un valor nulo para el mismo.
2. Si no especifica la estructura de datos utilizando la propiedad de la **estructura** de la definición de conjunto de datos, el generador de datos deduce el esquema utilizando la primera fila de los datos. En este caso, si la primera fila contiene el esquema completo, se pierden algunas columnas en el resultado de la operación de copia.

Por lo tanto, para los orígenes de datos sin esquema, lo mejor es especificar la estructura de datos utilizando la propiedad de la **estructura** .

## <a name="azure-table-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de la tabla de Azure

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida de conjuntos de datos y directivas están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores.

**AzureTableSource** admite las siguientes propiedades en la sección typeProperties:

(Propiedad) | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | Uso de la consulta personalizada para leer datos. | Cadena de consulta de tabla de Azure. Vea ejemplos de la siguiente sección. | No. Cuando se especifica un nombre de tabla sin un azureTableSourceQuery, todos los registros de la tabla se copian en el destino. Si también se especifica una azureTableSourceQuery, registros de la tabla que satisface la consulta se copian en el destino.
azureTableSourceIgnoreTableNotFound | Indica si debe la excepción de tabla no existen. | TRUE<br/>FALSO | No |

### <a name="azuretablesourcequery-examples"></a>ejemplos de azureTableSourceQuery

Si la columna de tabla de Azure es de tipo cadena: 

    azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"

Si la columna de tabla de Azure es de tipo de fecha y hora: 

    "azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"


**AzureTableSink** admite las siguientes propiedades en la sección typeProperties:


(Propiedad) | Descripción | Valores permitidos | Obligatorio  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | Partición clave valor predeterminado que puede ser usada por el receptor. | Un valor de cadena. | No 
azureTablePartitionKeyName | Especifique el nombre de la columna cuyos valores se usan como claves de partición. Si no se especifica, se utiliza AzureTableDefaultPartitionKeyValue como la clave de partición. | Un nombre de columna. | No |
azureTableRowKeyName | Especifique el nombre de la columna cuyos valores de columna se utilizan como clave de fila. Si no se especifica, use un GUID para cada fila. | Un nombre de columna. | No  
azureTableInsertType | El modo de insertar datos en tablas de Azure.<br/><br/>Esta propiedad controla si las filas existentes en la tabla de resultados con claves de partición y fila coincidentes tienen sus valores reemplazados o combinados. <br/><br/>Para obtener información sobre cómo funcionan estas configuraciones (combinar y reemplazar), consulte los temas de [Insertar o entidad de combinación](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Insertar o reemplazar la entidad](https://msdn.microsoft.com/library/azure/hh452242.aspx) . <br/><br> Esta configuración se aplica en el nivel de fila, no el nivel de la tabla y ninguna opción elimina filas de la tabla de salida que no existen en la entrada. | Combinar (predeterminado)<br/>reemplazar | No 
writeBatchSize | Inserta los datos en la tabla de Azure cuando se visita el writeBatchSize o writeBatchTimeout. | Entero (número de filas)| No (predeterminado: 10000) 
writeBatchTimeout | Inserta los datos en la tabla de Azure cuando se visita el writeBatchSize o writeBatchTimeout | intervalo de tiempo<br/><br/>Ejemplo: "00: 20:00" (20 minutos) | No (valor predeterminado para el tiempo de espera de almacenamiento cliente predeterminado valor 90 seg)

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Asigne una columna de origen a una columna de destino con el traductor de propiedad JSON para poder usar la columna de destino como el azureTablePartitionKeyName.

En el ejemplo siguiente, la columna de origen DivisionID se asigna a la columna de destino: DivisionID.  

    "translator": {
        "type": "TabularTranslator",
        "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
    } 

La DivisionID se especifica como la clave de partición. 

    "sink": {
        "type": "AzureTableSink",
        "azureTablePartitionKeyName": "DivisionID",
        "writeBatchSize": 100,
        "writeBatchTimeout": "01:00:00"
    }


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-table"></a>Asignación de tipo para tablas de Azure

Como se mencionó en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , actividad copia realiza las conversiones de tipo automática de tipos de origen para recibir tipos con el enfoque de dos pasos siguientes.

1. Convertir de tipos de origen nativo al tipo de .NET
2. Conversión de tipo de .NET a tipo de lavabo nativo

Al mover los datos a y desde la tabla de Azure, el siguiente [asignaciones definen por el servicio de tabla de Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) se usan entre tipos de Azure tabla OData en el tipo de .NET y viceversa. 

| Tipo de datos de OData | Tipo .NET | Detalles |
| --------------- | --------- | ------- |
| Edm.Binary | byte] | Una matriz de bytes hasta 64 KB. |
| Edm.Boolean | BOOL | Un valor Boolean. |
| Edm.DateTime | Fecha y hora | Un valor de 64 bits expresado como hora Universal coordinada (UTC). El intervalo de fecha y hora compatible comienza desde medianoche, del 1 de enero de 1601 D.C. (D.C.) UTC. El intervalo finaliza en 31 de diciembre de 9999. |
| Edm.Double | doble | Valor de punto flotante de 64 bits. |
| Edm.Guid | GUID | Un identificador único global de 128 bits. |
| Edm.Int32 | Int32 o int | Un entero de 32 bits. |
| Edm.Int64 | Int64 o long | Un entero de 64 bits. |
| Edm.String | Cadena | Un valor de codificación UTF-16. Valores de cadena pueden ser hasta 64 KB. |

### <a name="type-conversion-sample"></a>Ejemplo de conversión de tipo

En el ejemplo siguiente es para copiar datos de un blobs de Windows Azure a tabla de Azure con las conversiones de tipo. 

Supongamos que el conjunto de datos Blob en formato CSV y contiene tres columnas. Una de ellas es una columna de fecha y hora con un formato de fecha y hora personalizado mediante francés abreviaturas para el día de la semana. 

Definir el conjunto de datos de origen de blobs manera junto con las definiciones de tipo para las columnas.
    
    {
        "name": " AzureBlobInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1,
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

Dada la asignación de tipo de tipo de tabla de Azure OData de tipo .NET, se define la tabla en la tabla de Azure con el siguiente esquema. 

**Esquema de la tabla Azure:**

Nombre de columna | Tipo
----------- | --------
identificador de usuario | Edm.Int64
nombre | Edm.String 
LastLoginDate | Edm.DateTime

A continuación, defina el conjunto de datos de tabla de Azure como sigue. No debe especificar sección "estructura" con la información de tipo, ya que la información de tipo ya está especificada en el almacén de datos subyacente.

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

En este caso, el generador de datos automáticamente de conversión de tipos incluido el campo de fecha y hora con el formato de fecha y hora personalizado utilizando la referencia cultural "NG NG" al mover datos de blobs de Windows Azure tabla.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Para obtener información sobre los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar, consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md).







