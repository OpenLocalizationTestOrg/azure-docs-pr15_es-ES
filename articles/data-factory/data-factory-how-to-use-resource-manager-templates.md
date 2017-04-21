<properties 
    pageTitle="Usar el Administrador de recursos de plantillas en el generador de datos | Microsoft Azure" 
    description="Aprenda a crear y usar plantillas de administrador de recursos de Azure para crear entidades de generador de datos." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="shlo"/>

# <a name="use-templates-to-create-azure-data-factory-entities"></a>Use las plantillas para crear entidades de generador de datos de Azure

## <a name="overview"></a>Información general
Al usar el generador de datos de Azure para sus necesidades de integración de datos, es posible que volver a utilizar el mismo patrón en distintos entornos o de implementar la misma tarea repetidas veces dentro de la misma solución. Plantillas le ayudan a implementar y administrar estos escenarios de forma fácil. Plantillas de fábrica de datos de Azure son ideales para escenarios que implican reutilización y repetición.
 
Tenga en cuenta la situación donde una organización tiene 10 plantas de fabricación en todo el mundo. Los registros de cada planta se almacenan en una base de datos de SQL Server local independiente. La empresa desea crear un almacén de datos en la nube para análisis ad-hoc. También desea tener la misma lógica pero configuraciones diferentes para los entornos de desarrollo, prueba y producción. 

En este caso, una tarea es necesario repetir dentro del mismo entorno, pero con diferentes valores en todas las fábricas 10 datos para cada fábrica. De hecho, **repetición** está presente. Creación de plantillas permite la extracción de este flujo genérico (es decir, canalizaciones teniendo las mismas actividades en cada generador de datos), pero utiliza un archivo de parámetro independiente para cada fábrica.

Además, como la organización desea implementar estas fábricas 10 datos varias veces en distintos entornos, plantillas pueden utilizar esta **reutilización** mediante el uso de archivos de parámetro independiente para entornos de desarrollo, prueba y producción.

## <a name="templating-with-azure-resource-manager"></a>Plantillas con el Administrador de recursos Azure
[Plantillas de administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md#template-deployment) son una excelente forma de lograr plantillas en el generador de datos de Azure. Plantillas de administrador de recursos definen la infraestructura y la configuración de su solución de Azure a través de un archivo JSON. Debido a las plantillas de administrador de recursos de Azure funcionan con Azure más o todos los servicios, puede ampliamente utilizado para administrar fácilmente todos los recursos de los activos de Azure. Consulte [crear un administrador de recursos de Azure plantillas](../resource-group-authoring-templates.md) para más información sobre las plantillas de administrador de recursos en general. 

## <a name="tutorials"></a>Tutoriales
Consulte los siguientes tutoriales para obtener instrucciones paso a paso crear entidades de fábrica de datos usando las plantillas de administrador de recursos:

- [Tutorial: Crear una canalización para copiar los datos mediante el Administrador de recursos de Azure plantilla](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [Tutorial: Crear una canalización para procesar datos mediante el Administrador de recursos de Azure plantilla](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Plantillas de fábrica de datos en Github
Consulte las siguientes plantillas de Azure inicio rápido en Github: 

- [Crear un generador de datos para copiar datos de almacenamiento de blobs de Windows Azure en la base de datos de SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
- [Crear un generador de datos con la actividad de subárbol en clúster HDInsight de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
- [Crear un generador de datos para copiar datos de Salesforce a Blobs de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)

No dude en compartir sus plantillas de fábrica de datos de Azure en el [Inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/). Consulte la [Guía de contribución](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) al desarrollar plantillas que se pueden compartir a través de este repositorio. 

Las secciones siguientes proporcionan detalles sobre cómo definir los recursos del generador de datos en una plantilla de administrador de recursos. 

## <a name="defining-data-factory-resources-in-templates"></a>Definición de recursos de la fábrica de datos en plantillas
La plantilla de nivel superior para definir un generador de datos es:

    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
    {
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "[variables('apiVersion')]",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "westus",
        "resources": [
        { "type": "linkedservices",
            ...
        },
        {"type": "datasets",
            ...
        },
        {"type": "dataPipelines",
            ...
        }
    }

### <a name="define-data-factory"></a>Definir el generador de datos

Definir un generador de datos en la plantilla de administrador de recursos como se muestra en el ejemplo siguiente:

    "resources": [
    {
        "name": "[variables('<mydataFactoryName>')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "East US"
    }

La dataFactoryName se define en "variables" como:

    "dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",

### <a name="define-linked-services"></a>Definir servicios vinculados 
    
    "type": "linkedservices",
    "name": "[variables('<LinkedServiceName>')]",
    "apiVersion": "2015-10-01",
    "dependsOn": [ "[variables('<dataFactoryName>')]" ],
    "properties": {
        ...
    }


Para obtener más información acerca de las propiedades JSON para el servicio vinculado específico que desee implementar, vea [Servicio de almacenamiento en vinculado](data-factory-azure-blob-connector.md#azure-storage-linked-service) o [Calcular servicios vinculados](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . El parámetro de "dependsOn" especifica el nombre de la fábrica de datos correspondiente. Se muestra un ejemplo de la definición de un servicio vinculado para el almacenamiento de Azure en la definición de JSON siguiente:

### <a name="define-datasets"></a>Definir los conjuntos de datos

    "type": "datasets",
    "name": "[variables('<myDatasetName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<myDatasetLinkedServiceName>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        ...
    }

Para obtener más información acerca de las propiedades JSON para el tipo de conjunto de datos específicos que desea implementar, consulte [compatibles almacena datos](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Nota que el parámetro "dependsOn" especifica el nombre del generador de datos correspondientes y almacenamiento vinculada servicio. Se muestra un ejemplo de la definición de tipo de conjunto de datos de almacenamiento de blobs de Windows Azure en la definición de JSON siguiente:

    "type": "datasets",
    "name": "[variables('storageDataset')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('storageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "[variables('storageLinkedServiceName')]",
    "typeProperties": {
        "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
        "fileName": "[parameters('sourceBlobName')]",
        "format": {
            "type": "TextFormat"
        }
    },
    "availability": {
        "frequency": "Hour",
        "interval": 1
    }

### <a name="define-pipelines"></a>Definir canalizaciones

    "type": "dataPipelines",
    "name": "[variables('<mypipelineName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<inputDatasetLinkedServiceName>')]",
        "[variables('<outputDatasetLinkedServiceName>')]",
        "[variables('<inputDataset>')]",
        "[variables('<outputDataset>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        activities: {
            ...
        }
    }

Consulte [definir canalizaciones](data-factory-create-pipelines.md#pipeline-json) para obtener más información acerca de las propiedades JSON para definir la canalización específica y actividades que desea implementar. Nota El parámetro "dependsOn" especifica el nombre de la fábrica de datos y cualquier correspondiente vinculado servicios o conjuntos de datos. Se muestra un ejemplo de una canalización que copie los datos de almacenamiento de blobs de Windows Azure a la base de datos de SQL Azure en el fragmento JSON siguiente:

    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('azureSqlLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "activities": [
        {
            "name": "CopyFromAzureBlobToAzureSQL",
            "description": "Copy data frm Azure blob to Azure SQL",
            "type": "Copy",
            "inputs": [
                {
                    "name": "[variables('blobInputDatasetName')]"
                }
            ],
            "outputs": [
                {
                    "name": "[variables('sqlOutputDatasetName')]"
                }
            ],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink",
                    "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "columnMappings": "Column0:FirstName,Column1:LastName"
                }
            },
            "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }
        ],
        "start": "2016-10-03T00:00:00Z",
        "end": "2016-10-04T00:00:00Z"

## <a name="parameterizing-data-factory-template"></a>Plantilla de datos fábrica parametrizar
Prácticas recomendadas sobre cómo parametrizar, consulte el artículo de [prácticas recomendadas para crear plantillas de administrador de recursos de Azure](../resource-manager-template-best-practices.md#parameters) . En general, se debe minimizar uso del parámetro, sobre todo si se pueden utilizar variables en su lugar. Solo proporcionar parámetros en las situaciones siguientes:

- Configuración varía según el entorno (ejemplo: desarrollo, prueba y producción)
- Información confidencial (como contraseñas)

Si necesita extraer información confidencial de la [Cámara de Azure clave](../key-vault/key-vault-get-started.md) al implementar entidades de generador de datos de Azure con plantillas, especifique la **clave depósito** y el **secreto nombre** tal como se muestra en el ejemplo siguiente:

    "parameters": {
        "storageAccountKey": { 
            "reference": {
                "keyVault": {
                    "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
                },
                "secretName": "<secretName>"
            }, 
        },
        ...
    }

> [AZURE.NOTE] Mientras exportar plantillas para generadores de datos existente actualmente no es compatible todavía, está en la obra. 


