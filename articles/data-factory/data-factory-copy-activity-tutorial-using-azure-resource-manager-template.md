<properties
    pageTitle="Tutorial: Crear una canalización con el Administrador de recursos plantilla | Microsoft Azure"
    description="En este tutorial, cree una canalización de generador de datos de Azure con una actividad de copia con el Administrador de recursos de Azure plantilla."
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
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-resource-manager-template"></a>Tutorial: Crear una canalización con la actividad de copia con el Administrador de recursos de Azure plantilla
> [AZURE.SELECTOR]
- [Información general y los requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Asistente para copiar](data-factory-copy-data-wizard-tutorial.md)
- [Portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure plantilla de administrador de recursos](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API DE REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API DE .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


En este tutorial se muestra cómo crear y supervisar un generador de datos de Azure mediante una plantilla de administrador de recursos de Azure. La canalización en el generador de datos copia datos desde el almacenamiento de blobs de Windows Azure en base de datos de SQL Azure.

## <a name="prerequisites"></a>Requisitos previos
- Vaya a través de [información general de Tutorial y los requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) y complete los pasos de **requisito previo** .
- Siga las instrucciones que aparecen en el artículo [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para instalar la última versión de PowerShell de Azure en el equipo. En este tutorial, usar PowerShell para implementar entidades del generador de datos. 
- (opcional) Consulte [Crear plantillas de Azure Administrador de recursos](../resource-group-authoring-templates.md) para obtener información acerca de las plantillas de administrador de recursos de Azure.


## <a name="in-this-tutorial"></a>En este tutorial

En este tutorial, se crea un generador de datos con las siguientes entidades de fábrica de datos:

Entidad | Descripción  
------ | ----------- 
Servicio de almacenamiento vinculado de Azure | Vínculos a su cuenta de almacenamiento de Azure en el generador de datos. Almacenamiento de Azure es el almacén de datos de origen y base de datos de SQL Azure es el almacén de datos de receptor de la actividad de copia en el tutorial. Especifica la cuenta de almacenamiento que contiene los datos de entrada para la actividad de copia. 
Servicio de vinculado de base de datos de SQL Azure| Vínculos a la base de datos de SQL Azure en el generador de datos. Especifica la base de datos de SQL Azure que contiene los datos de salida de la actividad de copia. 
Dataset de entrada de blobs de Windows Azure | Hace referencia al servicio de almacenamiento de Azure vinculado. El servicio vinculado hace referencia a una cuenta de almacenamiento de Azure y el conjunto de datos de blobs de Windows Azure especifica el contenedor, la carpeta y el nombre de archivo en el almacenamiento de información que contiene los datos de entrada. 
Conjunto de datos de los resultados SQL Azure | Se refiere al servicio SQL Azure vinculado. El servicio SQL Azure vinculado hace referencia a un servidor de SQL Azure y el conjunto de datos de SQL Azure especifica el nombre de la tabla que contiene los datos de salida. 
Canalización de datos | La canalización tiene una actividad de tipo copiar que toma el conjunto de datos de blobs de Windows Azure como entrada y el conjunto de datos de SQL Azure como salida. La actividad de copia copia los datos de un Azure blob a una tabla en la base de datos de SQL Azure.  

Un generador de datos puede tener una o varias tuberías. Una canalización puede tener una o varias actividades. Existen dos tipos de actividades: [las actividades de movimiento de datos](data-factory-data-movement-activities.md) y [las actividades de transformación de datos](data-factory-data-transformation-activities.md). En este tutorial, se crea una canalización con una actividad (actividad copiar).

![Copiar blobs de Windows Azure en la base de datos SQL Azure](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

La siguiente sección proporciona la plantilla de administrador de recursos completa para definir entidades del generador de datos para que pueda ejecutar el tutorial rápidamente y probar la plantilla. Para comprender cómo cada entidad de fábrica de datos está definido, vea la sección [entidades de fábrica de datos en la plantilla](#data-factory-entities-in-the-template) .

## <a name="data-factory-json-template"></a>Plantilla de datos JSON de fábrica
La plantilla de administrador de recursos de nivel superior para definir un generador de datos es: 

    {
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
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Crear un archivo JSON denominado **ADFCopyTutorialARM.json** en la carpeta **C:\ADFGetStarted** con el siguiente contenido:


    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
          "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
          "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
          "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
          "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
          "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
          "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
          } 
        },
        "variables": {
          "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
          "azureSqlLinkedServiceName": "AzureSqlLinkedService",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "blobInputDatasetName": "BlobInputDataset",
          "sqlOutputDatasetName": "SQLOutputDataset",
          "pipelineName": "Blob2SQLPipeline"
        },
        "resources": [
          {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "2015-10-01",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "West US",
            "resources": [
              {
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
                }
              },
              {
                "type": "linkedservices",
                "name": "[variables('azureSqlLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlDatabase",
                  "description": "Azure SQL linked service",
                  "typeProperties": {
                    "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
                  }
                }
              },
              {
                "type": "datasets",
                "name": "[variables('blobInputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "Column0",
                      "type": "String"
                    },
                    {
                      "name": "Column1",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                    "fileName": "[parameters('sourceBlobName')]",
                    "format": {
                      "type": "TextFormat",
                      "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  },
                  "external": true
                }
              },
              {
                "type": "datasets",
                "name": "[variables('sqlOutputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureSqlLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlTable",
                  "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "FirstName",
                      "type": "String"
                    },
                    {
                      "name": "LastName",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "tableName": "[parameters('targetSQLTable')]"
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  }
                }
              },
              {
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
                  "start": "2016-10-02T00:00:00Z",
                  "end": "2016-10-03T00:00:00Z"
                }
              }
            ]
          }
        ]
      }

## <a name="parameters-json"></a>Parámetros JSON 
Crear un archivo JSON denominado **ADFCopyTutorialARM Parameters.json** que contiene los parámetros para la plantilla de administrador de recursos de Azure. 

> [AZURE.IMPORTANT] Especifique el nombre y la clave de su cuenta de almacenamiento de Azure para los parámetros **storageAccountName** y **storageAccountKey** .  

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": { 
            "storageAccountName": { "value": "<Name of the Azure storage account>"  },
            "storageAccountKey": {
                "value": "<Key for the Azure storage account>"
            },
            "sourceBlobContainer": { "value": "adftutorial" },
            "sourceBlobName": { "value": "emp.txt" },
            "sqlServerName": { "value": "<Name of the Azure SQL server>" },
            "databaseName": { "value": "<Name of the Azure SQL database>" },
            "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
            "sqlServerPassword": { "value": "<password for the user>" },
            "targetSQLTable": { "value": "emp" }
        }
    }

> [AZURE.IMPORTANT] Puede que tenga archivos JSON parámetro independiente de desarrollo, pruebas y entornos de producción que puede usar con la misma plantilla de datos JSON de fábrica. Mediante una secuencia de comandos de PowerShell, puede automatizar implementar entidades de datos fábrica en estos entornos.  

## <a name="create-data-factory"></a>Crear el generador de datos
1. Iniciar **PowerShell de Azure** y ejecute el siguiente comando:
    - Ejecutar `Login-AzureRmAccount` y escriba el nombre de usuario y contraseña que utiliza para iniciar sesión en el portal de Azure.  
    - Ejecutar `Get-AzureRmSubscription` para ver todas las suscripciones para esta cuenta.
    - Ejecutar `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` para seleccionar la suscripción que desea trabajar con. 
2. Ejecute el comando siguiente para implementar entidades de generador de datos con la plantilla de administrador de recursos que creó en el paso 1.

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>Canalización de Monitor
1. Inicie sesión en el [portal de Azure](https://portal.azure.com) con su cuenta de Azure.
2. Haga clic en **fábricas de datos** en el menú de la izquierda (o) haga clic en **más servicios** y haga clic en **fábricas de datos** en **inteligencia + análisis de** categoría.

    ![Menú de generadores de datos](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. En la página de **generadores de datos** , buscar y encontrar el generador de datos. 

    ![Busque el generador de datos](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. Haga clic en el generador de datos de Azure. Consulte la página de inicio para el generador de datos.

    ![Página principal de la fábrica de datos](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
5. Haga clic en **diagrama de** mosaico para ver la vista de diagrama de la fábrica de datos.

    ![Vista de diagrama de generador de datos](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-diagram-view.png)
6. En la vista de diagrama, haga doble clic en el conjunto de datos **SQLOutputDataset**. Ver el estado de la segmentación de datos. Cuando haya terminado la operación de copia, el estado de establecer en **Listo.**

    ![Sector de salida en estado listo](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/output-slice-ready.png)
7. Cuando la segmentación de datos está **preparada** , compruebe que los datos se copian a la tabla de **emp** en la base de datos de SQL Azure.

Para obtener instrucciones sobre cómo usar los módulos portal Azure para supervisar la canalización y conjuntos de datos que haya creado en este tutorial, vea [canalización y conjuntos de datos de Monitor](data-factory-monitor-manage-pipelines.md) .

También puede usar el Monitor y administrar aplicaciones para supervisar su canalizaciones de datos. Consulte [Monitor y administrar mediante la aplicación de supervisión de canalizaciones de generador de datos de Azure](data-factory-monitor-manage-app.md) para obtener información detallada sobre el uso de la aplicación.


## <a name="data-factory-entities-in-the-template"></a>Entidades de fábrica de datos en la plantilla

### <a name="define-data-factory"></a>Definir el generador de datos
Definir un generador de datos en la plantilla de administrador de recursos, como se muestra en el ejemplo siguiente:  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

La dataFactoryName se define como: 
      
    "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"

Es una única cadena basada en el ID de grupo de recursos.  

### <a name="defining-data-factory-entities"></a>Definir entidades del generador de datos
Las siguientes entidades de fábrica de datos se definen en la plantilla JSON: 

1. [Servicio de almacenamiento vinculado de Azure](#azure-storage-linked-service)
2. [Servicio de vinculado de SQL Azure](#azure-sql-database-linked-service)
3. [Conjunto de datos de blobs de Windows Azure](#azure-blob-dataset)
4. [Conjunto de datos SQL Azure](#azure-sql-dataset)
5. [Canalización de datos con una actividad de copia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Servicio de almacenamiento vinculado de Azure
Especifique el nombre y la clave de su cuenta de almacenamiento de Azure en esta sección. Ver [el almacenamiento de Azure vinculado servicio](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obtener más información sobre las propiedades JSON utilizados para definir un servicio de almacenamiento de Azure vinculado. 

    {
        "type": "linkedservices",
        "name": "[variables('azureStorageLinkedServiceName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureStorage",
            "description": "Azure Storage linked service",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
            }
        }
    }

La cadena de conexión utiliza los parámetros storageAccountName y storageAccountKey. Los valores de estos parámetros pasados mediante un archivo de configuración. La definición también usa variables: azureStroageLinkedService y dataFactoryName se definen en la plantilla. 
    
#### <a name="azure-sql-database-linked-service"></a>Servicio de vinculado de base de datos de SQL Azure
Especifique el nombre del servidor de SQL Azure, el nombre de la base de datos, el nombre de usuario y la contraseña de usuario en esta sección. Para obtener más información sobre las propiedades JSON utilizados para definir un servicio de SQL Azure vinculado, vea [servicio de vinculado de SQL Azure](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) .  

    {
        "type": "linkedservices",
        "name": "[variables('azureSqlLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "Azure SQL linked service",
            "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
            }
        }
    }

La cadena de conexión usa nombreDeServidorSQL, databaseName, sqlServerUserName y parámetros de sqlServerPassword cuyos valores se pasan mediante un archivo de configuración. La definición también usa las siguientes variables de la plantilla: azureSqlLinkedServiceName, dataFactoryName.

#### <a name="azure-blob-dataset"></a>Conjunto de datos de blobs de Windows Azure
Especifique los nombres de contenedor de blobs de Windows, la carpeta y el archivo que contiene los datos de entrada. Ver [Propiedades del conjunto de datos de blobs de Windows Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obtener más información sobre las propiedades JSON utilizados para definir un conjunto de datos de blobs de Windows Azure. 


    {
        "type": "datasets",
        "name": "[variables('blobInputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
            "structure": [
            {
                "name": "Column0",
                "type": "String"
            },
            {
                "name": "Column1",
                "type": "String"
            }
            ],
            "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            },
            "external": true
        }
    }

#### <a name="azure-sql-dataset"></a>Conjunto de datos SQL Azure
Especifique el nombre de la tabla en la base de datos de SQL Azure que contiene los datos copiados al almacenamiento de blobs de Windows Azure. Ver [Propiedades del conjunto de datos de SQL Azure](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) para obtener más información sobre las propiedades JSON utilizados para definir un conjunto de datos de SQL Azure. 

    {
        "type": "datasets",
        "name": "[variables('sqlOutputDatasetName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]",
            "[variables('azureSqlLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
            "structure": [
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
            ],
            "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

#### <a name="data-pipeline"></a>Canalización de datos
Definir una canalización que copie los datos del conjunto de datos de blobs de Windows Azure al conjunto de datos de SQL Azure. Para las descripciones de elementos JSON utilizados para definir un proceso en este ejemplo, consulte [Canalización JSON](data-factory-create-pipelines.md#pipeline-json) . 

    {
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
            "start": "2016-10-02T00:00:00Z",
            "end": "2016-10-03T00:00:00Z"
        }
    }

## <a name="reuse-the-template"></a>Volver a usar la plantilla 
En el tutorial, ha creado una plantilla para definir entidades de fábrica de datos y una plantilla para pasar valores de parámetros. La canalización de copia datos desde una cuenta de Azure almacenamiento en una base de datos de SQL Azure especificado a través de parámetros. Para usar la misma plantilla para implementar entidades de fábrica de datos en distintos entornos, cree un archivo de parámetro para cada entorno y usarla cuando se implementa en ese entorno.     

Ejemplo:  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json

Observe que el primer comando utiliza el archivo de parámetros para el entorno de desarrollo, una segunda para el entorno de prueba y la tercera uno para el entorno de producción.  

También puede volver a usar la plantilla para llevar a cabo tareas repetidas. Por ejemplo, deberá crear muchos generadores de datos con una o varias tuberías que implementan la misma lógica pero cada generador de datos utiliza almacenamiento de Azure diferente y cuentas de base de datos de SQL Azure. En este escenario, use la misma plantilla en el mismo entorno (desarrollo, prueba o producción) con archivos de otro parámetro para crear generadores de datos.   

