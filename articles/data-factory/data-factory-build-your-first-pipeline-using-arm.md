<properties
    pageTitle="Crear su primer generador de datos (plantilla de administrador de recursos) | Microsoft Azure"
    description="En este tutorial, se crea una canalización de generador de datos de Azure de ejemplo con una plantilla de administrador de recursos de Azure."
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
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Tutorial: Crear su primer generador de datos de Azure con el Administrador de recursos de Azure plantilla
> [AZURE.SELECTOR]
- [Información general y los requisitos previos](data-factory-build-your-first-pipeline.md)
- [Portal de Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Plantilla de administrador de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [API DE REST](data-factory-build-your-first-pipeline-using-rest-api.md)

En este artículo, usa una plantilla de administrador de recursos de Azure para crear su primer generador de datos de Azure.

## <a name="prerequisites"></a>Requisitos previos
- Lea el artículo de [Visión general de Tutorial](data-factory-build-your-first-pipeline.md) y complete los pasos de **requisito previo** .
- Siga las instrucciones que aparecen en el artículo [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para instalar la última versión de PowerShell de Azure en el equipo.
- Consulte [Crear plantillas de Azure Administrador de recursos](../resource-group-authoring-templates.md) para obtener información acerca de las plantillas de administrador de recursos de Azure. 

## <a name="in-this-tutorial"></a>En este tutorial
Entidad | Descripción  
------ | ----------- 
Servicio de almacenamiento vinculado de Azure | Vínculos a su cuenta de almacenamiento de Azure en el generador de datos. La cuenta de almacenamiento de Azure contiene los datos de entrada y salidos para la canalización de este ejemplo. 
Servicios de HDInsight a petición vinculados| Vínculos un HDInsight a petición de clúster para el generador de datos. El clúster se creará automáticamente a los datos de proceso y se elimina cuando haya terminado el procesamiento.
Dataset de entrada de blobs de Windows Azure | Hace referencia al servicio de almacenamiento de Azure vinculado. El servicio vinculado hace referencia a una cuenta de almacenamiento de Azure y el conjunto de datos de blobs de Windows Azure especifica el contenedor, la carpeta y el nombre de archivo en el almacenamiento de información que contiene los datos de entrada. 
Conjunto de datos de salida de blobs de Windows Azure | Hace referencia al servicio de almacenamiento de Azure vinculado. El servicio vinculado hace referencia a una cuenta de almacenamiento de Azure y el conjunto de datos de blobs de Windows Azure especifica el contenedor, la carpeta y el nombre de archivo en el almacenamiento de información que contiene los datos de salida. 
Canalización de datos | La canalización tiene una actividad de tipo HDInsightHive consume el conjunto de datos de entrada y genera el conjunto de datos de salida.   

Un generador de datos puede tener una o varias tuberías. Una canalización puede tener una o varias actividades. Existen dos tipos de actividades: [las actividades de movimiento de datos](data-factory-data-movement-activities.md) y [las actividades de transformación de datos](data-factory-data-transformation-activities.md). En este tutorial, se crea una canalización con una actividad (actividad copiar).

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

Crear un archivo JSON denominado **ADFTutorialARM.json** en la carpeta **C:\ADFGetStarted** con el siguiente contenido:

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
            "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
            "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
            "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
            "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
            "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
            "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
            "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
            "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
        },
        "variables": {
            "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
            "azureStorageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDatasetName": "AzureBlobInput",
            "blobOutputDatasetName": "AzureBlobOutput",
            "pipelineName": "HiveTransformPipeline"
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
                "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "HDInsightOnDemand",
                    "typeProperties": {
                        "clusterSize": 1,
                        "version": "3.2",
                        "timeToLive": "00:05:00",
                        "osType": "windows",
                        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
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
                    "typeProperties": {
                        "fileName": "[parameters('inputBlobName')]",
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "external": true
                }
            },
            {
                "type": "datasets",
                "name": "[variables('blobOutputDatasetName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Month",
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
                    "[variables('hdInsightOnDemandLinkedServiceName')]",
                    "[variables('blobInputDatasetName')]",
                    "[variables('blobOutputDatasetName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "description": "Pipeline that transforms data using Hive script.",
                    "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                            "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                            "defines": {
                                "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                                "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                            }
                        },
                        "inputs": [
                            {
                                "name": "[variables('blobInputDatasetName')]"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "[variables('blobOutputDatasetName')]"
                            }
                        ],
                        "policy": {
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Month",
                            "interval": 1
                        },
                        "name": "RunSampleHiveActivity",
                        "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                    }
                    ],
                    "start": "2016-10-01T00:00:00Z",
                    "end": "2016-10-02T00:00:00Z",
                    "isPaused": false
                }
            }
            ]
        }
        ]
    }

> [AZURE.NOTE] Puede encontrar otro ejemplo de plantilla de administrador de recursos para crear un generador de datos de Azure en [Tutorial: crear una canalización con actividad copia mediante una plantilla de administrador de recursos de Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  

## <a name="parameters-json"></a>Parámetros JSON 
Crear un archivo JSON denominado **ADFTutorialARM Parameters.json** que contiene los parámetros para la plantilla de administrador de recursos de Azure.  

> [AZURE.IMPORTANT] Especificar el nombre y la clave de su cuenta de almacenamiento de Azure para los parámetros **storageAccountName** y **storageAccountKey** en este archivo de parámetros. 

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "storageAccountName": {
                "value": "<Name of your Azure Storage account>"
            },
            "storageAccountKey": {
                "value": "<Key of your Azure Storage account>"
            },
            "blobContainer": {
                "value": "adfgetstarted"
            },
            "inputBlobFolder": {
                "value": "inputdata"
            },
            "inputBlobName": {
                "value": "input.log"
            },
            "outputBlobFolder": {
                "value": "partitioneddata"
            },
            "hiveScriptFolder": {
                "value": "script"
            },
            "hiveScriptFile": {
                "value": "partitionweblogs.hql"
            }
        }
    }

> [AZURE.IMPORTANT] Puede que tenga archivos JSON parámetro independiente de desarrollo, pruebas y entornos de producción que puede usar con la misma plantilla de datos JSON de fábrica. Mediante una secuencia de comandos de PowerShell, puede automatizar implementar entidades de datos fábrica en estos entornos. 

## <a name="create-data-factory"></a>Crear el generador de datos

1. Iniciar **PowerShell de Azure** y ejecute el siguiente comando: 
    - Ejecutar `Login-AzureRmAccount` y escriba el nombre de usuario y contraseña que utiliza para iniciar sesión en el portal de Azure.  
    - Ejecutar `Get-AzureRmSubscription` para ver todas las suscripciones para esta cuenta.
    - Ejecutar `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` para seleccionar la suscripción que desea trabajar con. Esta suscripción debe ser el mismo que el utilizado en el portal de Azure.
1. Ejecute el comando siguiente para implementar entidades de generador de datos con la plantilla de administrador de recursos que creó en el paso 1. 

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>Canalización de Monitor
 
1.  Después de iniciar sesión el [portal de Azure](https://portal.azure.com/), haga clic en **Examinar** y seleccione **generadores de datos**.
        ![Examinar -> generadores de datos](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.  En el módulo de **Generadores de datos** , haga clic en el generador de datos (**TutorialFactoryARM**) que creó.   
2.  En el módulo de **Generador de datos** para el generador de datos, haga clic en **diagrama**.
        ![Mosaico de diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.  En la **Vista de diagrama**, consulte información general sobre los conjuntos de datos usados en este tutorial y canalizaciones.
    
    ![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. En la vista de diagrama, haga doble clic en el conjunto de datos **AzureBlobOutput**. Verá que la segmentación de datos que se está procesando.

    ![Conjunto de datos](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Cuando haya finalizado el proceso, vea la segmentación de datos en estado **Listo** . Creación de un clúster de HDInsight a petición suele algún tiempo (aproximadamente 20 minutos). Por lo tanto, espere la canalización de pasar **unos 30 minutos** para procesar la segmentación de datos.

    ![Conjunto de datos](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png) 
10. Cuando la segmentación de datos está **preparada** , compruebe la carpeta de **partitioneddata** en el contenedor de **adfgetstarted** en el almacenamiento de blobs para los datos de salida.  

Para obtener instrucciones sobre cómo usar los módulos portal Azure para supervisar la canalización y conjuntos de datos que haya creado en este tutorial, vea [canalización y conjuntos de datos de Monitor](data-factory-monitor-manage-pipelines.md) .

También puede usar el Monitor y administrar aplicaciones para supervisar su canalizaciones de datos. Consulte [Monitor y administrar mediante la aplicación de supervisión de canalizaciones de generador de datos de Azure](data-factory-monitor-manage-app.md) para obtener información detallada sobre el uso de la aplicación. 

> [AZURE.IMPORTANT] Cuando la segmentación de datos se procesa correctamente, se elimina el archivo de entrada. Por lo tanto, si desea volver a ejecutar la segmentación de datos o realizar de nuevo el tutorial, cargue el archivo de entrada (input.log) a la carpeta inputdata del contenedor adfgetstarted.

## <a name="data-factory-entities-in-the-template"></a>Entidades de fábrica de datos en la plantilla
### <a name="define-data-factory"></a>Definir el generador de datos
Definir un generador de datos en la plantilla de administrador de recursos como se muestra en el ejemplo siguiente:  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

La dataFactoryName se define como: 
      
      "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",

Es una cadena única basada en el ID de grupo de recursos.  

### <a name="defining-data-factory-entities"></a>Definir entidades del generador de datos
Las siguientes entidades de fábrica de datos se definen en la plantilla JSON: 

- [Servicio de almacenamiento vinculado de Azure](#azure-storage-linked-service)
- [Servicios de HDInsight a petición vinculados](#hdinsight-on-demand-linked-service)
- [Conjunto de datos de entrada de blobs de Windows Azure](#azure-blob-input-dataset)
- [Conjunto de datos de salida de blobs de Windows Azure](#azure-blob-output-dataset)
- [Canalización de datos con una actividad de copia](#data-pipeline)

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

La **cadena de conexión** utiliza los parámetros storageAccountName y storageAccountKey. Los valores de estos parámetros pasados mediante un archivo de configuración. La definición también usa variables: azureStroageLinkedService y dataFactoryName se definen en la plantilla. 
    
#### <a name="hdinsight-on-demand-linked-service"></a>Servicios de HDInsight a petición vinculados
Consulte el artículo [calcular servicios vinculados](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obtener más información sobre las propiedades JSON utilizados para definir un servicio vinculado a petición de HDInsight.  

      {
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "HDInsightOnDemand",
          "typeProperties": {
            "clusterSize": 1,
            "version": "3.2",
            "timeToLive": "00:05:00",
            "osType": "windows",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
          }
        }
      }

Tenga en cuenta los siguientes puntos: 

- El generador de datos crea un clúster de HDInsight **basado en Windows** con la anterior JSON. También puede hacer que cree un clúster de HDInsight **basado en Linux** . Para obtener más información, vea [Servicios de vinculados a petición HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
- Puede usar **su propio clúster de HDInsight** en lugar de usar un clúster de HDInsight a petición. Para obtener más información, vea [Servicios vinculados de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
- El clúster HDInsight crea un **contenedor predeterminado** en el almacenamiento de blobs especificada en el JSON (**linkedServiceName**). HDInsight no eliminar este contenedor cuando se elimina el clúster. Este comportamiento es por diseño. Con el servicio de HDInsight vinculado a petición, un HDInsight cada vez que un sector debe procesarse a menos que haya una existente, se crea clúster live clúster (**período de Vida**) y se elimina cuando se realiza el procesamiento.

    Mientras se procesan más sectores, verá muchos contenedores en el almacenamiento de blobs de Windows Azure. Si no es necesario para solucionar problemas de los trabajos, desea eliminarlos para reducir el costo de almacenamiento. Los nombres de los contenedores siguen un patrón: "adf**yourdatafactoryname**-**linkedservicename**- datetimestamp". Use herramientas como el [Explorador de almacenamiento de Microsoft](http://storageexplorer.com/) para eliminar contenedores en el almacenamiento de blobs de Windows Azure.

Para obtener más información, vea [Servicios de vinculados a petición HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .



#### <a name="azure-blob-input-dataset"></a>Conjunto de datos de entrada de blobs de Windows Azure
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
          "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
              "type": "TextFormat",
              "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          },
          "external": true
        }
      }

Esta definición usa los siguientes parámetros definidos en la plantilla de parámetro: blobContainer, inputBlobFolder y inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Conjunto de datos de salida de blobs de Windows Azure
Especifique los nombres de contenedor de blobs de Windows y la carpeta que contiene los datos de salida. Ver [Propiedades del conjunto de datos de blobs de Windows Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obtener más información sobre las propiedades JSON utilizados para definir un conjunto de datos de blobs de Windows Azure.  

      {
        "type": "datasets",
        "name": "[variables('blobOutputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
          "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
              "type": "TextFormat",
              "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          }
        }
      }

Esta definición usa los siguientes parámetros definidos en la plantilla de parámetro: blobContainer y outputBlobFolder. 

#### <a name="data-pipeline"></a>Canalización de datos
Definir la canalización de transformación de datos ejecutando el script de sección en un clúster de Azure HDInsight a petición. Para las descripciones de elementos JSON utilizados para definir un proceso en este ejemplo, consulte [Canalización JSON](data-factory-create-pipelines.md#pipeline-json) . 

    {
        "type": "datapipelines",
        "name": "[variables('pipelineName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('hdInsightOnDemandLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('blobOutputDatasetName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "description": "Pipeline that transforms data using Hive script.",
          "activities": [
            {
              "type": "HDInsightHive",
              "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                  "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                  "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
              },
              "inputs": [
                {
                  "name": "[variables('blobInputDatasetName')]"
                }
              ],
              "outputs": [
                {
                  "name": "[variables('blobOutputDatasetName')]"
                }
              ],
              "policy": {
                "concurrency": 1,
                "retry": 3
              },
              "scheduler": {
                "frequency": "Month",
                "interval": 1
              },
              "name": "RunSampleHiveActivity",
              "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
            }
          ],
          "start": "2016-10-01T00:00:00Z",
          "end": "2016-10-02T00:00:00Z",
          "isPaused": false
        }
      }

## <a name="reuse-the-template"></a>Volver a usar la plantilla 
En el tutorial, ha creado una plantilla para definir entidades de fábrica de datos y una plantilla para pasar valores de parámetros. Para usar la misma plantilla para implementar entidades de fábrica de datos en distintos entornos, cree un archivo de parámetro para cada entorno y usarla cuando se implementa en ese entorno.     

Ejemplo:  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json

Observe que el primer comando utiliza el archivo de parámetros para el entorno de desarrollo, una segunda para el entorno de prueba y la tercera uno para el entorno de producción.  

También puede volver a usar la plantilla para llevar a cabo tareas repetidas. Por ejemplo, deberá crear muchos generadores de datos con una o varias tuberías que implementan la misma lógica pero cada generador de datos utiliza almacenamiento de Azure diferente y cuentas de base de datos de SQL Azure. En este escenario, use la misma plantilla en el mismo entorno (desarrollo, prueba o producción) con archivos de otro parámetro para crear generadores de datos. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Plantilla de administrador de recursos para la creación de una puerta de enlace
Aquí es una plantilla de administrador de recursos de ejemplo para crear una puerta de enlace lógico en la parte posterior. Instalar una puerta de enlace en el equipo local o Azure IaaS VM y registrar la puerta de enlace con el servicio de generador de datos mediante una clave. Para obtener más información, vea [mover datos entre local y la nube](data-factory-move-data-between-onprem-and-cloud.md) .

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "GatewayUsingArmDF",
            "apiVersion": "2015-10-01",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "eastus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "gateways",
                        "apiVersion": "[variables('apiVersion')]",
                        "name": "GatewayUsingARM",
                        "properties": {
                            "description": "my gateway"
                        }
                    }            
                ]
            }
        ]
    }

Esta plantilla crea un generador de datos denominado GatewayUsingArmDF con una puerta de enlace con el nombre: GatewayUsingARM. 

## <a name="see-also"></a>Vea también
| Tema | Descripción |
| :---- | :---- |
| [Actividades de transformación de datos](data-factory-data-transformation-activities.md) | Este artículo proporciona una lista de las actividades de transformación de datos (como HDInsight sección transformación utilizados en este tutorial) compatibles con el generador de datos de Azure. |
| [Programación y ejecución](data-factory-scheduling-and-execution.md) | En este artículo se explica los aspectos de programación y ejecución del generador de datos de Azure del modelo de aplicación. |
| [Canalizaciones](data-factory-create-pipelines.md) | Este artículo le ayudará a comprender canalizaciones y actividades en el generador de datos de Azure y cómo usarlas para llevar a cabo controlados por datos flujos de trabajo para su situación o empresa. |
| [Conjuntos de datos](data-factory-create-datasets.md) | Este artículo le ayudará a comprender los conjuntos de datos en el generador de datos de Azure.
| [Supervisar y administrar tuberías mediante la aplicación de supervisión](data-factory-monitor-manage-app.md) | En este artículo se describe cómo supervisar, administrar y depurar tuberías mediante la supervisión y la aplicación de administración. 

  

