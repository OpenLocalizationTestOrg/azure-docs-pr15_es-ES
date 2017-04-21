<properties
    pageTitle="Crear clústeres Hadoop, HBase, tormenta o motor en Linux en HDInsight con PowerShell de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear clústeres Hadoop, HBase, tormenta o motor en Linux para HDInsight con PowerShell de Azure."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="create-linux-based-clusters-in-hdinsight-by-using-azure-powershell"></a>Crear clústeres basados en Linux en HDInsight con PowerShell de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

PowerShell Azure es un entorno de secuencias de comandos eficaces que puede usar para controlar y automatizar la implementación y administración de las cargas de trabajo en Microsoft Azure. Este documento proporciona información sobre cómo crear un clúster basados en Linux HDInsight con PowerShell de Azure. También incluye un script de ejemplo.

> [AZURE.NOTE] Solo está disponible en clientes de Windows Azure PowerShell. Si está utilizando a un cliente Linux, Unix o Mac OS X, consulte [crear un clúster de HDInsight basados en Linux mediante CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) para obtener información sobre el uso de la CLI de Azure para crear un clúster.

## <a name="prerequisites"></a>Requisitos previos
Debe tener el siguiente antes de empezar con este procedimiento:

- Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- PowerShell Azure.
    Para obtener más información sobre cómo usar PowerShell Azure con HDInsight, consulte [Administrar HDInsight con PowerShell](hdinsight-administer-use-powershell.md). Para la lista de cmdlets de HDInsight de Windows PowerShell, vea [referencia de los cmdlets HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Crear clústeres

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para crear un clúster de HDInsight con PowerShell de Azure, debe completar los procedimientos siguientes:

- Crear un grupo de recursos de Azure
- Crear una cuenta de almacenamiento de Azure
- Crear un contenedor de blobs de Windows Azure
- Crear un clúster de HDInsight

Los dos parámetros más importantes que debe establecer para crear clústeres de Linux son los que especifican el tipo de sistema operativo y los detalles del usuario SSH:

- Asegúrese de que se especifique el parámetro **- OSType** como **Linux**.
- Para utilizar SSH para sesiones remotas en los clústeres, puede especificar la contraseña de usuario SSH o la clave pública SSH. Si se especifica la contraseña de usuario SSH y la clave pública SSH, se omitirá la clave. Si desea usar la tecla SSH para sesiones remotas, debe especificar una contraseña SSH en blanco cuando se le pida. Para obtener más información sobre cómo usar SSH con HDInsight, consulte uno de los siguientes artículos:

    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilizar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

La siguiente secuencia de comandos muestra cómo crear un nuevo clúster:

    $token ="<SpecifyAnUniqueString>"

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    #$subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    #Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Key1
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

Los valores que especifique para **$clusterCredentials** se usan para crear la cuenta de usuario de Hadoop para el clúster. Utilice esta cuenta para conectar con el clúster.

Los valores que especifique para **$sshCredentials** se usan para crear el usuario SSH para el clúster. Utilice esta cuenta para iniciar una sesión remota de SSH en el clúster y ejecutar trabajos.

> [AZURE.IMPORTANT] En esta secuencia de comandos, debe especificar el número de nodos de trabajo que se incluirán en el clúster. Si piensa usar más de 32 nodos de trabajo (bien ajustando el clúster después de la creación o la creación de un clúster), también debe especificar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
>
> Para obtener más información sobre los tamaños de nodo y los costos asociados, consulte [HDInsight de precios](https://azure.microsoft.com/pricing/details/hdinsight/).

Pueden tardar hasta 20 minutos para crear un clúster.

En el ejemplo siguiente se muestra cómo agregar una cuenta de almacenamiento adicional:

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## <a name="customize-clusters"></a>Personalizar clústeres

- Consulte [clústeres personalizar HDInsight con inicio](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Consulte [clústeres basados en Windows personalizar HDInsight con la acción de secuencia de comandos](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="delete-the-cluster"></a>Eliminar el clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado correctamente un clúster HDInsight, use los siguientes recursos para obtener información sobre cómo trabajar con el clúster.

### <a name="hadoop-clusters"></a>Clústeres Hadoop

* [Usar subárbol con HDInsight](hdinsight-use-hive.md)
* [Usar cerdo con HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollar aplicaciones Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clústeres de tormenta

* [Desarrollar topologías Java para tormenta en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizar componentes de Python en tormenta en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementar y supervisar topologías con tormenta en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Motor de clústeres

* [Crear una aplicación independiente de Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de motor mediante Livio](hdinsight-apache-spark-livy-rest-interface.md)
* [Motor con BI: realizar análisis de datos interactivas con motor en HDInsight con herramientas de inteligencia empresarial](hdinsight-apache-spark-use-bi-tools.md)
* [Motor con Machine aprendizaje: motor de uso en HDInsight de predecir los resultados de la inspección de comida](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Motor de flujo de datos: Motor de uso en HDInsight para la creación de aplicaciones de transmisión en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
