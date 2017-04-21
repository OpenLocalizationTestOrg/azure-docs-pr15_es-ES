<properties
    pageTitle="Mover los datos de Salesforce usando datos Factory | Microsoft Azure"
    description="Obtenga información sobre cómo mover datos de Salesforce mediante el generador de datos de Azure."
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
    ms.date="10/25/2016"
    ms.author="jingwang"/>

# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Mover datos de Salesforce mediante el generador de datos de Azure
En este artículo se describe cómo puede usar copiar actividad en un generador de datos de Azure para copiar datos de Salesforce a cualquier almacén de datos que aparece en la columna receptor de la tabla de [orígenes y receptores compatibles](data-factory-data-movement-activities.md#supported-data-stores) . En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que ofrece una descripción general de movimiento de datos con la actividad de copia y combinaciones de almacén de datos compatibles.

Generador de datos de Azure admite actualmente solo los datos móviles de Salesforce a [stores]((data-factory-data-movement-activities.md#supported-data-stores) de datos de lavabo compatibles, pero no almacena no admite el movimiento de datos de otros datos a Salesforce.

## <a name="prerequisites"></a>Requisitos previos
- Debe utilizar una de las siguientes ediciones de Salesforce: Developer Edition, Professional Edition, Enterprise Edition o ilimitado Edition.
- Permisos de la API deben estar habilitado. Vea [cómo habilitar el acceso de API de Salesforce por conjunto de permisos?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
- Para copiar datos de Salesforce a stores de datos local, debe tener al menos datos Management Gateway 2.0 instalado en su entorno local.

## <a name="salesforce-request-limits"></a>Límites de solicitudes de Salesforce
Salesforce tiene límites de solicitudes de API total y solicitudes simultáneas de API. Consulte la sección "API solicitar límites" en el artículo de [Salesforce programador límites](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) para obtener más detalles. Nota Si el número de solicitudes simultáneas supera el límite, el límite se produce y verá errores aleatorios; Si el número total de solicitudes excede el límite, se bloqueará la cuenta de Salesforce 24 horas; También puede recibir el error "REQUEST_LIMIT_EXCEEDED" en ambos casos.

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie datos de Salesforce a ninguna el receptor compatibles de almacenamiento de datos es usar al Asistente para copiar los datos. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar los datos.

En el ejemplo siguiente se proporciona definiciones de JSON de ejemplo que puede usar para crear una canalización con el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos de Salesforce para el almacenamiento de blobs de Windows Azure. Sin embargo, los datos se pueden copiar a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.   

## <a name="sample-copy-data-from-salesforce-to-an-azure-blob"></a>Ejemplo: Copiar datos de Salesforce a un blobs de Windows Azure
Este ejemplo copia datos de Salesforce a un blobs de Windows Azure cada hora. Las propiedades JSON que se usan en estos ejemplos se describen en secciones después de los ejemplos. Puede copiar los datos directamente a cualquiera de los receptores que se enumeran en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md#supported-data-stores) mediante copia actividad en el generador de datos de Azure.

Estos son los artefactos generador de datos que necesita crear para implementar el escenario. Las secciones que siguen a la lista ofrecen detalles sobre estos pasos.

- Un servicio del tipo de [Salesforce](#salesforce-linked-service-properties) vinculado
- Un servicio vinculado del tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
- Un [conjunto de datos](data-factory-create-datasets.md) de entrada del tipo [RelationalTable](#salesforce-dataset-properties)
- Un [conjunto de datos](data-factory-create-datasets.md) de salida del tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
- Una [canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [RelationalSource](#relationalsource-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)

**Servicio de Salesforce vinculado**

Este ejemplo usa el servicio de **Salesforce** vinculado. Consulte la sección de [Salesforce vinculado servicio](#salesforce-linked-service-properties) para las propiedades que son compatibles con este servicio vinculado.  Para obtener instrucciones sobre cómo restablecer u obtener el token de seguridad, consulte [obtener el token de seguridad](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) .

    {
        "name": "SalesforceLinkedService",
        "properties":
        {
            "type": "Salesforce",
            "typeProperties":
            {
                "username": "<user name>",
                "password": "<password>",
                "securityToken": "<security token>"
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

**Conjunto de datos de entrada de Salesforce**

    {
        "name": "SalesforceInput",
        "properties": {
            "linkedServiceName": "SalesforceLinkedService",
            "type": "RelationalTable",
            "typeProperties": {
                "tableName": "AllDataType__c"  
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

Establecer **externos** en **true** , servicio del generador de datos le informa de que el conjunto de datos, el generador de datos externo y no se produce en una actividad en el generador de datos.

> [AZURE.IMPORTANT] El elemento "__c" del nombre de la API es necesario para cualquier objeto personalizado.

![Nombre de datos API de fábrica - conexión de Salesforce-](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Conjunto de datos de salida de blobs de Windows Azure**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1).

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/alltypes_c"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Canalización de la actividad de copia**

La canalización contiene actividad de copia, que está configurado para usar la entrada anterior y conjuntos de datos de salida, y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **RelationalSource**y el tipo de **receptor** se establece en **BlobSink**.

Ver [Propiedades de tipo de RelationalSource](#relationalsource-type-properties) para la lista de propiedades que son compatibles con la RelationalSource.

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2016-06-01T18:00:00",
            "end":"2016-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":[  
            {
                "name": "SalesforceToAzureBlob",
                "description": "Copy from Salesforce to an Azure blob",
                "type": "Copy",
                "inputs": [
                {
                    "name": "SalesforceInput"
                }
                ],
                "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"             
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

> [AZURE.IMPORTANT] El elemento "__c" del nombre de la API es necesario para cualquier objeto personalizado.

![Nombre de datos API de fábrica - conexión de Salesforce-](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="salesforce-linked-service-properties"></a>Propiedades del servicio de Salesforce vinculado

La siguiente tabla proporciona descripciones de elementos JSON específicos del servicio de Salesforce vinculado.

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| tipo | La propiedad tipo debe estar establecida en: **Salesforce**. | Sí |
| nombre de usuario |Especifique un nombre de usuario para la cuenta de usuario. | Sí |
| contraseña | Especificar una contraseña para la cuenta de usuario.  | Sí |
| securityToken | Especifique un token de seguridad de la cuenta de usuario. Para obtener instrucciones sobre cómo restablecer u obtener un token de seguridad, consulte [obtener el token de seguridad](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) . Para obtener información sobre la seguridad de tokens en general, vea [la API y seguridad](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).  | Sí |

## <a name="salesforce-dataset-properties"></a>Propiedades del conjunto de datos de Salesforce

Para obtener una lista completa de secciones y las propiedades que están disponibles para definir conjuntos de datos, vea el artículo [crear conjuntos de datos](data-factory-create-datasets.md) . Secciones como estructura, la disponibilidad y la directiva de un conjunto de datos JSON son similares para todos los tipos de conjunto de datos (SQL Azure, blobs de Windows Azure, tablas de Azure y así sucesivamente).

La sección **typeProperties** es diferente para cada tipo de conjunto de datos y proporciona información sobre la ubicación de los datos en el almacén de datos. La sección typeProperties para un conjunto de datos del tipo de **RelationalTable** tiene las siguientes propiedades:

| (Propiedad) | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| nombre de tabla | Nombre de la tabla de Salesforce. | No (si se especifica una **consulta** de **RelationalSource** ) |

> [AZURE.IMPORTANT]  El elemento "__c" del nombre de la API es necesario para cualquier objeto personalizado.

![Nombre de datos API de fábrica - conexión de Salesforce-](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="relationalsource-type-properties"></a>Propiedades de tipo de RelationalSource

Para obtener una lista completa de propiedades que están disponibles para definir las actividades y secciones, vea el artículo de [canalizaciones de creación](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y diversas directivas están disponibles para todos los tipos de actividades.

Las propiedades que están disponibles en la sección typeProperties de la actividad, por otro lado, varían en función de cada tipo de actividad. Para copiar actividad, que varían en función de los tipos de orígenes y receptores.

En la actividad de copia, cuando el origen es del tipo **RelationalSource** (que incluye Salesforce), las siguientes propiedades están disponibles en la sección typeProperties:

| (Propiedad) | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| consulta | Uso de la consulta personalizada para leer datos. | Una consulta de SQL-92 o [Lenguaje de consulta de objeto de Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Por ejemplo: `select * from MyTable__c`. | No (si se especifica el **nombre de tabla** del **conjunto de datos** ) |

> [AZURE.IMPORTANT] El elemento "__c" del nombre de la API es necesario para cualquier objeto personalizado.

![Nombre de datos API de fábrica - conexión de Salesforce-](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Sugerencias de consulta

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Recuperar datos mediante dónde cláusula en la columna de fecha y hora
Cuando se especifica la consulta SQL o SOQL, preste atención a la diferencia de formato de fecha y hora. Por ejemplo:

- **Ejemplo SOQL**: $$Text.Format (' seleccione Id, nombre, BillingCity de cuenta donde LastModifiedDate > = {0:yyyy-MM-ddTHH} LastModifiedDate y < {1:yyyy-MM-ddTHH}', WindowStart, WindowEnd)
- **Ejemplo SQL**: $$Text.Format ('SELECT * de cuenta donde LastModifiedDate > = {{ts\'{0:yyyy-MM-dd hh}\'}} y LastModifiedDate < {{ts\'{1:yyyy-MM-dd hh}\'}}', WindowStart, WindowEnd)'.

### <a name="retrieving-data-from-salesforce-report"></a>Recuperar datos de informes de Salesforce
Puede recuperar datos de informes de Salesforce especificando la consulta como `{call "<report name>"}`, por ejemplo, `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Recuperar registros eliminados de la Papelera de reciclaje de Salesforce
Para consultar los registros suaves eliminados de la Papelera de reciclaje de Salesforce, puede especificar **"IsDeleted = 1"** en la consulta. Por ejemplo, 

- Para consultar sólo los registros eliminados, especifique "seleccione *de MyTable__c * *donde IsDeleted = 1**"
- Para consultar todos los registros incluidos la existente y eliminado, especifique "seleccione *de MyTable__c * *donde IsDeleted = 0 o IsDeleted = 1**"

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-salesforce"></a>Asignación de tipos de Salesforce
Tipo de Salesforce | . Tipo de red
--------------- | ---------------
Numerar automáticamente | Cadena
Casilla de verificación | Valor booleano
Moneda | Doble
Fecha | Fecha y hora
Fecha y hora | Fecha y hora
Correo electrónico | Cadena
Id. | Cadena
Relación de búsqueda | Cadena
Lista desplegable de selección múltiple | Cadena
Número | Doble
Porcentaje | Doble
Teléfono | Cadena
Lista desplegable | Cadena
Texto | Cadena
Área de texto | Cadena
Área de texto (largo) | Cadena
Área de texto (enriquecido) | Cadena
Texto (cifrada) | Cadena
DIRECCIÓN URL | Cadena

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Vea la [actividad de copia Guía de rendimiento y optimización](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.
