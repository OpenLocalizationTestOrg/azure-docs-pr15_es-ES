<properties
   pageTitle="Crear clústeres basados en Linux Hadoop en HDInsight con plantillas de administrador de recursos de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear clústeres para HDInsight de Azure con plantillas de administrador de recursos de Azure de Azure."
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
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-linux-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Crear clústeres basados en Linux Hadoop en HDInsight con plantillas de administrador de recursos de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear clústeres HDInsight plantillas de Manager(ARM) de recursos de Azure. Para obtener más información, vea [implementar una aplicación con el Administrador de recursos de Azure plantilla](../resource-group-template-deploy.md). Para otra creación de clúster características y herramientas haga clic en la ficha, seleccione en la parte superior de esta página o vea [métodos de creación de clúster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Requisitos previos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de comenzar las instrucciones de este artículo, debe tener el siguiente:

- [Suscripción de azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- CLI PowerShell Azure y Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Plantillas de administrador de recursos

Plantilla de administrador de recursos facilita la crear clústeres HDInsight, sus recursos dependientes (por ejemplo, la cuenta de almacenamiento predeterminada) y otros recursos (como base de datos de SQL Azure usar Apache Sqoop) para la aplicación en una única operación coordinada. En la plantilla, defina los recursos que son necesarios para la aplicación y especificar los parámetros de implementación para valores de entrada para distintos entornos. La plantilla se compone de JSON y expresiones que puede utilizar para construir valores para su implementación.

Una plantilla de administrador de recursos para crear un clúster de HDInsight y la cuenta de almacenamiento de Azure dependiente puede encontrarse en [Apéndice A](#appx-a-arm-template). Usar varias plataformas [VSCode](https://code.visualstudio.com/#alt-downloads) con la [extensión del Administrador de recursos](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) o un editor de texto para guardar la plantilla en un archivo en su estación de trabajo. Aprenderá cómo llamar a la plantilla con diferentes métodos.

Para obtener más información acerca de la plantilla de administrador de recursos, vea

- [Plantillas de administrador de recursos de Azure autor](../resource-group-authoring-templates.md)
- [Implementar una aplicación con la plantilla de administrador de recursos de Azure](../resource-group-template-deploy.md)

Para averiguar el esquema JSON para determinados elementos, puede seguir el procedimiento siguiente:

1. Abra el [portal de Azure](https://porta.azure.com) para crear un clúster de HDInsight.  Consulte [basados en Linux crear clústeres en HDInsight con el portal de Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
2. Configurar los elementos necesarios y los elementos que necesita el esquema JSON.
3. Antes de hacer clic en **crear**, haga clic en **Opciones de automatización** , como se muestra en la siguiente captura de pantalla:

    ![HDInsight Hadoop crear clúster opciones de automatización de esquema de plantilla de administrador de recursos](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-automation-option.png)

    El portal crea una plantilla de administrador de recursos basándose en las configuraciones.
## <a name="deploy-with-powershell"></a>Implementar con PowerShell

El procedimiento siguiente crea clúster HDInsight basados en Linux.

**Para implementar un clúster mediante la plantilla de administrador de recursos**

1. Guarde el archivo de json en [Apéndice A](#appx-a-arm-template) su estación de trabajo. En la secuencia de comandos de PowerShell, el nombre de archivo es *C:\HDITutorials-ARM\hdinsight-arm-template.json*.
2. Si es necesario, establezca los parámetros y variables.
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
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

    La secuencia de comandos de PowerShell sólo configura el nombre del clúster. El nombre de la cuenta de almacenamiento está codificado en la plantilla. Se le pedirá que escriba la contraseña de usuario de clúster (el nombre de usuario predeterminado es *admin*); y la contraseña de usuario SSH (el nombre de usuario SSH predeterminado es *sshuser*).  
    
Para obtener más información, vea [implementar con PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## <a name="deploy-with-azure-cli"></a>Implementar con CLI Azure

En el ejemplo siguiente se crea un clúster y su cuenta de almacenamiento dependientes y contenedor al llamar a una plantilla de administrador de recursos:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"
    
Se le pedirá que escriba el nombre del clúster, la contraseña de usuario de clúster (nombre de usuario es *Administrador*) y la contraseña de usuario SSH (el nombre de usuario SSH predeterminado es *sshuser*). Para proporcionar parámetros en línea:

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{\"clusterName\":{\"value\":\"hdi1229\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"},\"sshPassword\":{\"value\":\"Pass@word1\"}}'

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

La plantilla de administrador de recursos de Azure siguiente crea un clúster Hadoop basados en Linux con la cuenta de almacenamiento de Azure dependientes. 

> [AZURE.NOTE] El ejemplo incluye información de configuración de la sección metastore y Oozie metastore.  Quitar la sección o configurar la sección antes de utilizar la plantilla.

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
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
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
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
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.4",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "2",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
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
