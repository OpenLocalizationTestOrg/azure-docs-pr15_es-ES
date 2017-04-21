<properties
   pageTitle="Crear clústeres basados en Windows Hadoop en HDInsight con PowerShell de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear clústeres para Azure HDInsight con PowerShell de Azure."
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
   ms.date="08/10/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>Crear clústeres basados en Windows Hadoop en HDInsight con PowerShell de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear clústeres HDInsight con PowerShell de Azure. PowerShell Azure es un módulo que proporciona cmdlets para administrar Azure con Windows PowerShell. Para otra creación de clúster características y herramientas haga clic en la ficha, seleccione en la parte superior de esta página o vea [métodos de creación de clúster](hdinsight-provision-clusters.md#cluster-creation-methods).


##<a name="prerequisites"></a>Requisitos previos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de comenzar las instrucciones de este artículo, debe tener el siguiente:

- Suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- PowerShell Azure.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Crear clústeres
PowerShell Azure es un entorno de secuencias de comandos eficaces que puede usar para controlar y automatizar la implementación y administración de las cargas de trabajo en Azure. Esta sección proporciona instrucciones sobre cómo crear un clúster de HDInsight con PowerShell de Azure. Para obtener información sobre cómo configurar una estación de trabajo para ejecutar los cmdlets de HDInsight de Windows PowerShell, vea [instalar y configurar Azure PowerShell](../powershell-install-configure.md). Para obtener más información sobre cómo usar PowerShell Azure con HDInsight, consulte [Administrar HDInsight con PowerShell](hdinsight-administer-use-powershell.md). Para la lista de los cmdlets de HDInsight de Windows PowerShell, vea [referencia de los cmdlets HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Los procedimientos siguientes son necesarios para crear un clúster de HDInsight con PowerShell Azure:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext 

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName 

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName 

## <a name="create-clusters-using-arm-template"></a>Crear clústeres con BRAZO plantilla

Puede usar PowerShell Azure implementar una plantilla ARM que crea un clúster de HDInsight.  Consulte [plantillas con PowerShell de Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md#call-templates-using-powershell).

##<a name="customize-clusters"></a>Personalizar clústeres

- Consulte [clústeres personalizar HDInsight con inicio](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Consulte [clústeres basados en Windows personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).


##<a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido diversas maneras para crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md) : Obtenga información sobre cómo empezar a trabajar con el clúster HDInsight
* [Enviar Hadoop mediante programación de trabajos](hdinsight-submit-hadoop-jobs-programmatically.md) - Aprenda mediante programación enviar trabajos a HDInsight
* [Administrar Hadoop clústeres en HDInsight con PowerShell](hdinsight-administer-use-powershell.md) : Obtenga información sobre cómo trabajar con HDInsight con PowerShell de Azure
* [Documentación de Azure HDInsight SDK]  [ hdinsight-sdk-documentation] -descubrir el SDK HDInsight




[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx
