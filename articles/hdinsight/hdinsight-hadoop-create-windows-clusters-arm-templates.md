<properties
   pageTitle="Crear clústeres basados en Windows Hadoop en HDInsight con plantillas de administrador de recursos de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear clústeres para HDInsight de Azure con plantillas de administrador de recursos de Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Crear clústeres basados en Windows Hadoop en HDInsight con plantillas de administrador de recursos de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear clústeres HDInsight con plantillas de administrador de recursos de Azure. Para obtener más información, vea [implementar una aplicación con el Administrador de recursos de Azure plantilla](../resource-group-template-deploy.md). Para otra creación de clúster características y herramientas haga clic en la ficha, seleccione en la parte superior de esta página o vea [métodos de creación de clúster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Requisitos previos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Antes de comenzar las instrucciones de este artículo, debe tener el siguiente:

- [Suscripción de azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- CLI PowerShell Azure o Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Plantillas de administrador de recursos

Plantilla de administrador de recursos facilita la crear clústeres HDInsight, sus recursos dependientes (por ejemplo, la cuenta de almacenamiento predeterminada) y otros recursos (como base de datos de SQL Azure usar Apache Sqoop) para la aplicación en una única operación coordinada. En la plantilla, defina los recursos que son necesarios para la aplicación y especificar los parámetros de implementación para valores de entrada para distintos entornos. La plantilla se compone de JSON y expresiones que puede utilizar para construir valores para su implementación.

Una plantilla de administrador de recursos para crear un clúster de HDInsight y la cuenta de almacenamiento de Azure dependiente puede encontrarse en [Apéndice A](#appx-a-arm-template). Use un editor de texto para guardar la plantilla en un archivo en su estación de trabajo. Aprenderá cómo llamar a la plantilla con varias herramientas.

Para obtener más información acerca de la plantilla de administrador de recursos, vea

- [Plantillas de administrador de recursos de Azure autor](../resource-group-authoring-templates.md)
- [Implementar una aplicación con la plantilla de administrador de recursos de Azure](../resource-group-template-deploy.md)


## <a name="deploy-with-powershell"></a>Implementar con PowerShell

El procedimiento siguiente crea un clúster de HDInsight.

**Para implementar un clúster mediante la plantilla de administrador de recursos**

1. Guarde el archivo de json en [Apéndice A](#appx-a-arm-template) su estación de trabajo.
2. Configure los parámetros si es necesario.
3. Ejecutar la plantilla mediante el siguiente script de PowerShell:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

    La secuencia de comandos de PowerShell sólo configura el nombre del clúster y el nombre de la cuenta de almacenamiento.  Puede establecer otros valores de la plantilla de administrador de recursos. 
    
Para obtener más información, vea [implementar con PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## <a name="deploy-with-azure-cli"></a>Implementar con CLI Azure

En el ejemplo siguiente se crea un clúster y su cuenta de almacenamiento dependientes y contenedor al llamar a una plantilla de administrador de recursos:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{\"clusterName\":{\"value\":\"hdi1229win\"},\"clusterStorageAccountName\":{\"value\":\"hdi1229store\"},\"location\":{\"value\":\"East US 2\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"}}"





## <a name="deploy-with-rest-api"></a>Implementar con la API de REST

Vea [implementar con la API de REST](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## <a name="deploy-with-visual-studio"></a>Implementar con Visual Studio

Con Visual Studio, puede crear un proyecto del grupo de recursos e implementarlo en Azure a través de la interfaz de usuario. Seleccione el tipo de recursos que se van a incluir en el proyecto y los recursos se agregan automáticamente a la plantilla de administrador de recursos. El proyecto también proporciona una secuencia de comandos de PowerShell para implementar la plantilla.

Para obtener una introducción al uso de Visual Studio con los grupos de recursos, vea [crear e implementar grupos de recursos de Azure a través de Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

##<a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido diversas maneras para crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:


- Para obtener un ejemplo de implementación de recursos a través de la biblioteca de cliente .NET, vea [recursos de implementar mediante una plantilla y bibliotecas de .NET](../virtual-machines/virtual-machines-windows-csharp-template.md).
- Para obtener un ejemplo detallado de implementar una aplicación, vea [aprovisionar e implementar microservices predecible en Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Para obtener instrucciones sobre cómo implementar la solución en distintos entornos, consulte [desarrollo y entornos de prueba en Microsoft Azure](../solution-dev-test-environments.md).
- Para obtener información sobre las secciones de la plantilla de administrador de recursos de Azure, consulte [plantillas de edición](../resource-group-authoring-templates.md).
- Para obtener una lista de las funciones que puede usar en una plantilla de administrador de recursos de Azure, vea [funciones de plantilla](../resource-group-template-functions.md).



##<a name="appx-a-resource-manager-template"></a>Plantilla de administrador de recursos AppX A:

La plantilla de administrador de recursos de Azure siguiente crea un clúster Hadoop basado en Windows con la cuenta de almacenamiento de Azure dependientes.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

