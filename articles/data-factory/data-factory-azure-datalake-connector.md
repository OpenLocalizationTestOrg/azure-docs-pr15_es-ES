<properties
    pageTitle="Mover los datos del almacén de lago de datos de Azure / | Generador de datos de Azure"
    description="Obtenga información sobre cómo mover los datos de almacén de lago de datos de Azure mediante el generador de datos de Azure"
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
    ms.date="09/27/2016"
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Mover los datos del almacén de lago de datos de Azure con el generador de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia de un generador de datos de Azure para mover datos desde y hacia el almacén de lago de datos de Azure desde otro almacén de datos. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y las combinaciones de almacén de datos compatibles.

> [AZURE.NOTE]
> Cree una cuenta de Azure datos lago tienda antes de crear una canalización con una actividad de copia para mover datos desde una tienda lago de datos de Azure. Para obtener información acerca de la tienda de lago de datos de Azure, vea [Introducción a la tienda de lago de datos de Azure](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> Revise la [crear su primer tutorial de canalización](data-factory-build-your-first-pipeline.md) para conocer los pasos detallados para crear un generador de datos, servicios vinculados, los conjuntos de datos y una canalización. Utilizar los fragmentos de código JSON con Editor de fábrica de datos o Visual Studio o Azure PowerShell para crear las entidades de datos fábrica.

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie los datos de almacén de lago de datos de Azure es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

Los ejemplos siguientes proporcionan definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos a y desde la tienda de lago de datos de Azure y el almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** desde cualquiera de los orígenes a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  


## <a name="sample-copy-data-from-azure-blob-to-azure-data-lake-store"></a>Ejemplo: Copiar datos de blobs de Windows Azure al almacén de lago de datos de Azure
Se muestra en el ejemplo siguiente:

1.  Servicio de tipo [AzureStorage](#azure-storage-linked-service-properties)vinculado.
2.  Servicio de tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties)vinculado.
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.  [Canalización](data-factory-create-pipelines.md) con una actividad de copia que utiliza [BlobSource](#azure-blob-copy-activity-type-properties) y [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

El ejemplo copia datos de la serie de tiempo de un almacenamiento de blobs de Windows Azure al almacén de lago de datos de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos.


**Servicio de almacenamiento vinculado en Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Lago de datos de Azure había vinculado servicio:**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

### <a name="to-create-azure-data-lake-linked-service-using-data-factory-editor"></a>Crear servicio de vinculado Azure datos lago mediante el Editor del generador de datos
El procedimiento siguiente proporciona los pasos para crear un servicio de almacén de lago de datos de Azure vinculado con el Editor del generador de datos.

1. Haga clic en **almacenan nuevos datos** en la barra de comandos y seleccione **Almacén de lago de datos de Azure**.
2. En el editor de JSON para la propiedad **dataLakeStoreUri** , escriba el URI de lago de datos.
3. Haga clic en el botón **autorizar** en la barra de comandos. Debería ver una ventana emergente.

    ![Autorizar botón](./media/data-factory-azure-data-lake-connector/authorize-button.png)

4. Utilice sus credenciales para iniciar sesión y la propiedad de **autorización** en el JSON debe asignarse a un valor ahora.
5. (opcional) Especificar valores para los parámetros opcionales, como **nombre de la cuenta**, **subscriptionID**y **resourceGroupName** en el JSON (o) eliminar estas propiedades desde el JSON.
6. En la barra de comandos para implementar el servicio vinculado, haga clic en **implementar** .

> [AZURE.IMPORTANT] El código de autorización generado mediante el botón **autorizar** caduca después de unos minutos. **Autorizar** mediante la **autorizar** botón cuando el **token expira** y bola el servicio vinculado. Para obtener información detallada, consulte la sección [Servicio de vinculado de almacenamiento de lago de datos de Azure](#azure-data-lake-store-linked-service-properties) . 



**Dataset de entrada de blobs de Windows Azure:**

Datos se ha seleccionado en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). El nombre de ruta de acceso y la carpeta para el blob se evalúan dinámicamente en función de la hora de inicio de la segmentación de datos que se está procesando. Año, mes y parte del día de la hora de inicio utiliza la ruta de acceso y nombre de archivo usa el parte de horas de la hora de inicio. "externo": opción "true" indica el servicio de generador de datos que la tabla, el generador de datos externo y no se produce en una actividad en el generador de datos.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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


**Conjunto de datos de salida de lago de datos de Azure:**

El ejemplo copia datos en una tienda lago de datos de Azure. Nuevos datos están copias a datos lago almacenan cada hora.

    {
        "name": "AzureDataLakeStoreOutput",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Canalización con una actividad de copia:**

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **BlobSource** y tipo de **receptor** se establece en **AzureDataLakeStoreSink**.

    {  
        "name":"SamplePipeline",
        "properties":
        {  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":
            [  
                {
                    "name": "AzureBlobtoDataLake",
                    "description": "Copy Activity",
                    "type": "Copy",
                    "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                    ],
                    "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "treatEmptyAsNull": true,
                            "blobColumnSeparators": ","
                        },
                        "sink": {
                            "type": "AzureDataLakeStoreSink"
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

## <a name="sample-copy-data-from-azure-data-lake-store-to-azure-blob"></a>Ejemplo: Copiar datos de almacén de lago de datos de Azure a blobs de Windows Azure
Se muestra en el ejemplo siguiente:

1.  Servicio de tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties)vinculado.
2.  Servicio de tipo [AzureStorage](#azure-storage-linked-service-properties)vinculado.
3.  Un [conjunto de datos](data-factory-create-datasets.md) a la entrada de tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.  Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](#azure-blob-dataset-type-properties).
5.  Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que utiliza [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) y [BlobSink](#azure-blob-copy-activity-type-properties)

El ejemplo copia datos de la serie temporal desde una tienda lago de datos de Azure en un blobs de Windows Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos.

**Almacén de lago de datos de Azure había vinculado servicio:**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

> [AZURE.NOTE] Consulte los pasos en el ejemplo anterior para obtener la dirección URL de autorización.  

**Servicio de almacenamiento vinculado en Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**El conjunto de datos de entrada lago de datos de Azure:**

Establecer **"externo": true** informa al servicio del generador de datos que la tabla es el generador de datos externa y no se produce en una actividad en el generador de datos.

    {
        "name": "AzureDataLakeStoreInput",
        "properties":
        {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
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

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **AzureDataLakeStoreSource** y tipo de **receptor** se establece en **BlobSink**.


    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureDakeLaketoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                      {
                        "name": "AzureDataLakeStoreInput"
                      }
                    ],
                    "outputs": [
                      {
                        "name": "AzureBlobOutput"
                      }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureDataLakeStoreSource",
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


## <a name="azure-data-lake-store-linked-service-properties"></a>Propiedades de servicio de datos lago almacenamiento vinculada Azure

Puede vincular una cuenta de almacenamiento de Azure a un generador de datos de Azure con un servicio de almacenamiento de Azure vinculado. La tabla siguiente proporciona la descripción de los elementos JSON específicos al servicio de almacenamiento de Azure vinculado.

| (Propiedad) | Descripción | Obligatorio |
| :-------- | :----------- | :-------- |
| tipo | La propiedad tipo debe estar establecida en: **AzureDataLakeStore** | Sí |
| dataLakeStoreUri | Especificar información sobre la cuenta de la tienda de lago de datos de Azure. Se encuentra en el siguiente formato: https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1 | Sí |
| autorización | Haga clic en el botón **autorizar** en el **Editor de datos de fábrica** y escriba sus credenciales que asigna la dirección URL de autorización generado automáticamente a esta propiedad.  | Sí |
| Id. de sesión | Identificador de la sesión de OAuth de la sesión de autorización de oauth. Cada identificador de sesión único y sólo se puede utilizar una vez. Esta configuración se genera automáticamente al usar el Editor del generador de datos. | Sí |  
| nombre de la cuenta | Nombre de cuenta de lago de datos | No |
| subscriptionId | Id. de suscripción de Azure | No (si no se especifica, se utiliza la suscripción de la fábrica de datos). |
| resourceGroupName |  Nombre del grupo de recursos de Azure | No (si no se especifica, se utiliza el grupo de recursos de la fábrica de datos). |

## <a name="token-expiration"></a>Expiración de token 
El código de autorización que generar mediante el botón **autorizar** caduca después de unos minutos. Consulte la tabla siguiente para las horas de expiración para distintos tipos de cuentas de usuario. Es posible que vea el siguiente error mensaje cuando la autenticación **token expira**: "error de la operación de credenciales: invalid_grant - AADSTS70002: Error de validación de credenciales. AADSTS70008: La concesión de acceso proporcionado está caducada o ha caducado. ID de seguimiento: Id. de correlación d18629e8-af88-43c5-88e3-d8419eb1fca1: marca de tiempo de fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 09-21-31Z ".


| Tipo de usuario | Caduca después de |
| :-------- | :----------- | 
| Cuentas de usuario no administradas por Azure Active Directory (@hotmail.com, @live.com, etcetera.). | 12 horas |
| Cuentas de usuarios administradas por Azure Active Directory (AAD) | ejecutar 14 días después de la última segmentación de datos. <br/><br/>90 días, si una división basada en servicios vinculados basado en OAuth se ejecuta al menos una vez cada 14 días. |

Si cambia la contraseña antes de ese momento de expiración de token, el token caduca inmediatamente y verá el error mencionado en esta sección. 

Para evitar o resolver este error, autorizar mediante la **autorizar** botón cuando el **token expira** y bola el servicio vinculado. También puede generar valores de propiedades de **ID** y la **autorización** mediante programación utilizando código en la sección siguiente:

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Para generar los valores de Id. de sesión y la autorización de programación 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Consulte los temas de [Clases AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Clase AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)y [AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obtener más información sobre las clases de fábrica datos utilizadas en el código. Agregue una referencia a la versión de **2.9.10826.1824** de **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** para la clase WindowsFormsWebAuthenticationDialog utilizada en el código. 
 

## <a name="azure-data-lake-dataset-type-properties"></a>Propiedades de tipo de conjunto de datos de lago de datos de Azure

Para obtener una lista completa de secciones JSON y propiedades disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure, etcetera.).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y se proporciona información acerca de la ubicación, etc., formato de los datos en el almacén de datos. La sección typeProperties para el conjunto de datos del tipo de conjunto de datos de **AzureDataLakeStore** tiene las siguientes propiedades:

| (Propiedad) | Descripción | Obligatorio |
| :-------- | :----------- | :-------- |
| folderPath | Almacenar la ruta de acceso a la carpeta en el lago de datos de Azure y contenedor. | Sí |
| nombre de archivo | Nombre del archivo en el almacén de lago de datos de Azure. nombre de archivo es opcional y distingue mayúsculas de minúsculas. <br/><br/>Si especifica un nombre de archivo, la actividad (incluida la copia) funciona en el archivo específico.<br/><br/>Cuando no se especifica el nombre de archivo, copia incluye todos los archivos en la folderPath para el conjunto de datos de entrada.<br/><br/>Cuando no se especifica el nombre de archivo para un conjunto de datos de salida, el nombre del archivo generado será en el siguiente este formato: datos. <Guid>.txt (por ejemplo:: Data.0a405f8a 93ff 4c6f b3be f69616f1df7a.txt | No |
| partitionedBy | partitionedBy es una propiedad opcional. Puede usarlo para especificar un folderPath dinámico y un nombre de archivo de datos de la serie de tiempo. Por ejemplo, folderPath puede tener parámetros para cada hora de datos. Vea la sección [uso partitionedBy propiedad](#using-partitionedby-property) para obtener más detalles. | No |
| formato | Se admiten los siguientes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Establezca la propiedad **tipo** en formato en uno de estos valores. Consulte [Especificar TextFormat](#specifying-textformat), [AvroFormat que especifica](#specifying-avroformat), [JsonFormat que especifica](#specifying-jsonformat), [OrcFormat que especifica](#specifying-orcformat), [ParquetFormat especificar](#specifying-parquetformat) secciones y para obtener más información. Si desea copiar archivos como-está entre stores basados en archivos (copia binario), puede omitir la sección formato de ambas definiciones de conjunto de datos de entrada y salida.| No
| compresión | Especifique el tipo y el nivel de compresión de los datos. Tipos compatibles son: **GZip**, **Deflate**y **BZip2** y niveles compatibles son: **óptima** y **más rápida**. Actualmente, no se admite la configuración de compresión de datos en **AvroFormat** o **OrcFormat**. Para obtener más información, vea la sección de [compatibilidad con la compresión](#compression-support) .  | No |

### <a name="using-partitionedby-property"></a>Uso de partitionedBy (propiedad)
Puede especificar un folderPath dinámico y el nombre de archivo de datos de la serie de tiempo con la sección **partitionedBy** , macros del generador de datos y las variables del sistema: SliceStart y SliceEnd, que indican las fechas inicial y final de un segmento de datos determinado.

Vea los artículos [Crear conjuntos de datos](data-factory-create-datasets.md) y la [programación y ejecución](data-factory-scheduling-and-execution.md) para conocer más detalles en conjuntos de datos de serie de tiempo, la programación y sectores.

#### <a name="sample-1"></a>Ejemplo 1

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

En este ejemplo, {sector} se reemplaza con el valor de la variable de sistema de datos fábrica SliceStart en el formato (YYYYMMDDHH) especificado. Hace referencia a la SliceStart hora de inicio de la segmentación de datos. La folderPath es diferente para cada sector. Por ejemplo: wikisampledataout/wikidatagateway/2014100103 o wikisampledataout/wikidatagateway/2014100104

#### <a name="sample-2"></a>Ejemplo 2

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
 

### <a name="compression-support"></a>Compatibilidad con la compresión  
Procesamiento de grandes conjuntos de datos puede provocar botella de red e i/OS. Por lo tanto, los datos comprimidos en stores pueden no sólo acelerar la transferencia de datos a través de la red y ahorrar espacio en disco, pero también Traer importantes mejoras de rendimiento en el procesamiento de datos grandes. Actualmente, compresión es compatible con datos de archivo almacena como blobs de Windows Azure o sistema de archivos local.  

Para especificar la compresión para un conjunto de datos, utilice la propiedad de **compresión** en el conjunto de datos JSON como en el ejemplo siguiente:   

    {  
        "name": "AzureDatalakeStoreDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureDatalakeStore",  
            "linkedServiceName": "DataLakeStoreLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
La sección **compresión** tiene dos propiedades:  
  
- **Tipo:** el códec de compresión, que puede ser **GZIP**, **Deflate** o **BZIP2**.  
- **Nivel:** la razón de compresión, que puede ser **óptima** o **más rápido**. 
    - **Más rápida:** La operación de compresión debe completar lo antes posible, incluso si el archivo resultante no se comprime de manera óptima. 
    - **Óptima**: la operación de compresión se debe óptima comprimir, incluso si la operación tarda más tiempo en completarse. 
    
    Para obtener más información, vea el tema de [Nivel de compresión](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Supongamos que el conjunto de datos de ejemplo se usa como el resultado de una actividad de copia. La actividad de copia comprime los datos de salida con códec GZIP uso óptimo relación y, a continuación, escribe los datos comprimidos en un archivo denominado pagecounts.csv.gz en la tienda de lago de datos de Azure.   

Cuando se especifica la propiedad de compresión en un conjunto de datos de entrada JSON, la canalización lee datos comprimidos del origen. Cuando se especifica la propiedad de un conjunto de datos de salida JSON, la actividad de copia puede escribir los datos comprimidos al destino. Estos son algunos escenarios de ejemplo: 

- Datos comprimidos de GZIP de lectura desde una tienda lago de datos de Azure, descomprimir el archivo y escribir datos de resultado en una base de datos de SQL Azure. Definir el conjunto de datos de almacén de lago de datos de Azure entrada con la compresión de propiedad JSON en este caso. 
- Leer datos desde un archivo de texto sin formato del sistema de archivos local, comprimir con formato GZip y escribir los datos comprimidos en un almacén de lago de datos de Azure. Definir un conjunto de datos de salida datos Azure lago con la compresión de propiedad JSON en este caso.  
- Leer datos comprimidos GZIP desde una tienda lago de datos de Azure, descomprimir el archivo, comprimirlo mediante BZIP2 y escribir datos de resultado en una tienda de lago de datos de Azure. Establecer el conjunto de tipo de compresión como GZIP y BZIP2 para la entrada y había salida conjuntos de datos, respectivamente.   


## <a name="azure-data-lake-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de datos lago Azure  
Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directiva están disponibles para todos los tipos de actividades.

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copia, que varían en función de los tipos de orígenes y receptores

**AzureDataLakeStoreSource** es compatible con la siguiente sección **typeProperties** de propiedades:

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| recursivas | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. | True (valor predeterminado), False | No |



**AzureDataLakeStoreSink** es compatible con la siguiente sección **typeProperties** de propiedades:

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Especifica el comportamiento de copia. | **PreserveHierarchy:** conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso del archivo de origen a la carpeta de origen es idéntica a la ruta de acceso del archivo de destino a la carpeta de destino.<br/><br/>**FlattenHierarchy:** todos los archivos de la carpeta de origen se crean en el primer nivel de la carpeta de destino. Los archivos de destino se crean con nombre generada automáticamente.<br/><br/>**MergeFiles:** combina todos los archivos de la carpeta de origen a un archivo. Si se especifica el nombre de archivo o blobs, el nombre de archivo combinado sería el nombre especificado; en caso contrario, sería nombre de archivo generado automáticamente. | No |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.
