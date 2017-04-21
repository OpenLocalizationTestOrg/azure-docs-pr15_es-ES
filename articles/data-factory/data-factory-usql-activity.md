<properties 
    pageTitle="Ejecutar secuencia de comandos de U SQL Azure datos lago Analytics de la fábrica de datos de Azure" 
    description="Obtenga información sobre cómo procesar datos ejecutando secuencias de comandos U SQL en el servicio de cálculo de análisis de lago de datos de Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="spelluru"/>

# <a name="run-u-sql-script-on-azure-data-lake-analytics-from-azure-data-factory"></a>Ejecutar secuencia de comandos de U SQL Azure datos lago Analytics de la fábrica de datos de Azure
> [AZURE.SELECTOR]
[Sección](data-factory-hive-activity.md)  
[Cerdo](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
[El aprendizaje](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimiento almacenado](data-factory-stored-proc-activity.md)
[Datos lago análisis U-SQL](data-factory-usql-activity.md)
[.NET personalizada](data-factory-use-custom-activities.md)
 
Una canalización en un generador de datos de Azure procesa datos en servicios de almacenamiento vinculado mediante servicios de cálculo vinculada. Contiene una secuencia de actividades, donde cada actividad realiza una operación de procesamiento específico. Este artículo describe la **Actividad de datos lago análisis U SQL** que se ejecuta una secuencia de comandos **SQL U** en un servicio de cálculo vinculada de **Análisis de lago de datos de Azure** . 

> [AZURE.NOTE] 
> Cree una cuenta de Azure datos lago análisis antes de crear una canalización con una actividad de SQL U de análisis de datos lago. Para obtener información sobre el análisis de lago de datos de Azure, consulte [Introducción a análisis de lago de datos de Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Revise la [crear su primer tutorial de canalización](data-factory-build-your-first-pipeline.md) para conocer los pasos detallados para crear un generador de datos, servicios vinculados, los conjuntos de datos y una canalización. Use fragmentos JSON con Editor de fábrica de datos o Visual Studio o Azure PowerShell para crear entidades de generador de datos.

## <a name="azure-data-lake-analytics-linked-service"></a>Análisis de datos de Azure lago vinculado servicio
Crear un servicio de **Análisis de lago de datos de Azure** vinculado para establecer un vínculo a un generador de datos de Azure un servicio de cálculo de análisis de lago de datos de Azure. La actividad de análisis de lago U-SQL de datos en la canalización se refiere a este servicio vinculado. 

En el ejemplo siguiente se proporciona la definición de JSON para un servicio de análisis de lago de datos de Azure vinculado. 

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>", 
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }


En la tabla siguiente se describe las propiedades que se usan en la definición de JSON. 

(Propiedad) | Descripción | Obligatorio
-------- | ----------- | --------
Tipo | Se debe establecer la propiedad type: **AzureDataLakeAnalytics**. | Sí
nombre de la cuenta | Nombre de cuenta de análisis de datos de Azure lago. | Sí
dataLakeAnalyticsUri | URI de lago análisis de datos de Azure. |  No 
autorización | Código de autorización se recupera automáticamente después de hacer clic en el botón **autorizar** en el Editor del generador de datos y completar el inicio de sesión de OAuth.  | Sí 
subscriptionId | Id. de suscripción de Azure | No (si no se especifica, se utiliza la suscripción de la fábrica de datos). 
resourceGroupName | Nombre del grupo de recursos de Azure |  No (si no se especifica, se utiliza el grupo de recursos de la fábrica de datos).
Id. de sesión | identificador de la sesión de la sesión de autorización de OAuth. Cada identificador de sesión único y sólo se puede utilizar una vez. Sesión que ID se genera automáticamente en el Editor del generador de datos. | Sí

El código de autorización generado mediante el botón **autorizar** caduca después de unos minutos. Consulte la tabla siguiente para las horas de expiración para distintos tipos de cuentas de usuario. Es posible que vea el siguiente error mensaje cuando la autenticación **token expira**: error de la operación de credenciales: invalid_grant - AADSTS70002: Error de validación de credenciales. AADSTS70008: La concesión de acceso proporcionado está caducada o ha caducado. Id. de seguimiento: Id. de correlación d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 marca de tiempo: 2015-12-15 21:09:31Z

 
| Tipo de usuario | Caduca después de |
| :-------- | :----------- | 
| Cuentas de usuario no administradas por Azure Active Directory (@hotmail.com, @live.com, etcetera.) | 12 horas |
| Cuentas de usuarios administradas por Azure Active Directory (AAD) | ejecutar 14 días después de la última segmentación de datos. <br/><br/>90 días, si una división basada en servicios vinculados basado en OAuth se ejecuta al menos una vez cada 14 días. |

Para evitar o resolver este error, autorizar mediante la **autorizar** botón cuando el **token expira** y bola el servicio vinculado. También puede generar valores de propiedades de **ID** y la **autorización** mediante programación utilizando código en la sección siguiente. 

  
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

Consulte los temas de [Clases AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Clase AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)y [AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obtener más información sobre las clases de fábrica datos utilizadas en el código. Agregar una referencia a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para la clase WindowsFormsWebAuthenticationDialog. 
 
 
## <a name="data-lake-analytics-u-sql-activity"></a>Actividad de SQL U lago análisis de datos 

El siguiente fragmento JSON define una canalización con una actividad de SQL U de análisis de datos lago. La definición de actividad tiene una referencia al servicio de análisis de lago de datos de Azure vinculada que creó anteriormente.   
  

    {
        "name": "ComputeEventsByRegionPipeline",
        "properties": {
            "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
            "activities": 
            [
                {
                    "type": "DataLakeAnalyticsU-SQL",
                    "typeProperties": {
                        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                        "scriptLinkedService": "StorageLinkedService",
                        "degreeOfParallelism": 3,
                        "priority": 100,
                        "parameters": {
                            "in": "/datalake/input/SearchLog.tsv",
                            "out": "/datalake/output/Result.tsv"
                        }
                    },
                    "inputs": [
                        {
                            "name": "DataLakeTable"
                        }
                    ],
                    "outputs": 
                    [
                        {
                            "name": "EventsByRegionTable"
                        }
                    ],
                    "policy": {
                        "timeout": "06:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "EventsByRegion",
                    "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
                }
            ],
            "start": "2015-08-08T00:00:00Z",
            "end": "2015-08-08T01:00:00Z",
            "isPaused": false
        }
    }


La siguiente tabla describe los nombres y las descripciones de las propiedades están de la actividad. 

(Propiedad) | Descripción | Obligatorio
:-------- | :----------- | :--------
tipo | La propiedad tipo se debe establecer **DataLakeAnalyticsU -**SQL. | Sí
scriptPath | Ruta de acceso a la carpeta que contiene la secuencia de comandos SQL U. Nombre del archivo distingue mayúsculas de minúsculas. | N (si usa script)
scriptLinkedService | Servicio vinculada que lleve de almacenamiento que contiene la secuencia de comandos para el generador de datos | N (si usa script)
secuencia de comandos | Especificar secuencias de comandos de línea en lugar de especificar scriptPath y scriptLinkedService. Por ejemplo: "script": "Prueba crear base de datos". | No (si usa scriptPath y scriptLinkedService)
degreeOfParallelism | El número máximo de nodos que simultáneamente se usa para ejecutar el trabajo. | No
prioridad | Determina qué trabajos de todo lo que están en la cola deben estar seleccionados para ejecutar la primera. Menor es el número, mayor es la prioridad. | No 
parámetros | Parámetros de la secuencia de comandos SQL U | No 

Vea la [Definición de secuencia de comandos de SearchLogProcessing.txt](#script-definition) para la definición de secuencia de comandos. 

## <a name="sample-input-and-output-datasets"></a>Ejemplo de entrada y salida de conjuntos de datos

### <a name="input-dataset"></a>Conjunto de datos de entrada
En este ejemplo, los datos de entrada se encuentran en una tienda de lago de datos de Azure (archivo SearchLog.tsv en la carpeta de entrada o datalake). 

    {
        "name": "DataLakeTable",
        "properties": {
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
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }   

### <a name="output-dataset"></a>Conjunto de datos de salida
En este ejemplo, los datos de salida producidos por el script SQL U se almacenan en una tienda de lago de datos de Azure (datalake y salida carpeta). 

    {
        "name": "EventsByRegionTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="sample-data-lake-store-linked-service"></a>Servicio de vinculado de lago almacén de datos de ejemplo
Aquí es la definición de la muestra que lago almacén de datos de Azure vinculado servicio que utilizan los conjuntos de datos de entrada y salida. 

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

Consulte el artículo de [mover los datos hacia y desde el almacén de lago de datos de Azure](data-factory-azure-datalake-connector.md) para obtener una descripción de las propiedades JSON. 

## <a name="sample-u-sql-script"></a>Secuencia de comandos de SQL U 

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv(nullEscape:"#NULL#");
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start <= DateTime.Parse("2012/02/19");
    
    OUTPUT @rs1   
        TO @out
          USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

Los valores de **@in** y **@out** parámetros en la secuencia de comandos SQL U se pasan dinámicamente por ADF con la sección 'parameters'. Consulte la sección 'parameters' en la definición de canalización.

También puede especificar otras propiedades como degreeOfParallelism y prioridad en la definición de una canalización para las tareas que se ejecutan en el servicio de análisis de lago de datos de Azure.

## <a name="dynamic-parameters"></a>Parámetros dinámicos
En la definición de canalización de ejemplo y alejar parámetros se asignan con valores no modificables. 

    "parameters": {
        "in": "/datalake/input/SearchLog.tsv",
        "out": "/datalake/output/Result.tsv"
    }

Es posible usar parámetros dinámicos en su lugar. Por ejemplo: 

    "parameters": {
        "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
        "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
    }

En este caso, archivos de entrada aún se toman de la carpeta /datalake/input y archivos de salida se generan en la carpeta /datalake/output. Los nombres de archivo son dinámicos en función de la hora de inicio del sector.  