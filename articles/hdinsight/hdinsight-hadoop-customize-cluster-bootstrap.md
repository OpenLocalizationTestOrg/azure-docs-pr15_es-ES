<properties
    pageTitle="Personalizar clústeres HDInsight con inicio | Microsoft Azure"
    description="Obtenga información sobre cómo personalizar clústeres HDInsight con inicio."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizar clústeres HDInsight con inicio

A veces, desea configurar los archivos de configuración que se incluyen:

- clusterIdentity.xml
- site.xml principales
- Gateway.Xml
- HBASE env.xml
- site.xml HBASE
- HDFS site.xml
- sección env.xml
- sección site.xml
- sitio de mapred
- site.xml oozie
- oozie env.xml
- site.xml tormenta
- site.xml tez
- site.xml webhcat
- site.xml hilo

Los clústeres no pueden conservar los cambios debido a crear una nueva imagen. Para obtener más información sobre crear una nueva imagen, vea [Función instancia reinicia debido a actualizaciones de sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para mantener los cambios a través de la duración de los clústeres, puede usar HDInsight personalización de clúster durante el proceso de creación. Esta es la manera recomendada para cambiar las configuraciones de un clúster y se mantiene a través de estos eventos de reinicio de reinicio de Azure crear una nueva imagen. Estos cambios de configuración se aplican antes del inicio de servicio, por lo que no se reinicia servicios. 

Existen 3 métodos de inicio:

- Usar PowerShell Azure

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
    
- Usar el SDK de .NET
- Usar la plantilla de administrador de recursos de Azure

Para obtener información sobre cómo instalar componentes adicionales en el clúster de HDInsight durante la hora de creación, vea:

- [Personalizar clústeres HDInsight con la acción de secuencia de comandos (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
- [Personalizar clústeres HDInsight con la acción de secuencia de comandos (Windows)](hdinsight-hadoop-customize-cluster.md)

## <a name="use-azure-powershell"></a>Usar PowerShell Azure

El siguiente código de PowerShell personaliza la configuración de una sección:

    # hive-site.xml configuration
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
    
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 
    
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $existingResourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -Config $config 

Puede encontrar un script de PowerShell de trabajo completado en [Apéndice A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample).

**Para comprobar el cambio:**

1. Inicie sesión el [portal de Azure](https://portal.azure.com).
2. En el panel izquierdo, haga clic en **Examinar**y, a continuación, haga clic en **Clústeres HDInsight**.
3. Haga clic en el clúster que acaba de crear mediante el script de PowerShell.
4. Haga clic en **panel** de la parte superior de la hoja para abrir la UI Ambari.
5. Menú de la izquierda, haga clic en **sección** .
6. Haga clic en **HiveServer2** de **Resumen**.
7. Haga clic en la ficha de **configuraciones** .
8. Menú de la izquierda, haga clic en **sección** .
9. Haga clic en la ficha **Opciones avanzadas** .
10. Desplácese hacia abajo y, a continuación, expanda **avanzada del sitio de sección**.
11. Busque **hive.metastore.client.socket.timeout** en la sección.

Algunos ejemplos más sobre la personalización de otros archivos de configuración:

    # hdfs-site.xml configuration
    $HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

    # core-site.xml configuration
    $CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

    # mapred-site.xml configuration
    $MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

    # oozie-site.xml configuration
    $OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Para obtener más información, consulte blog de Azim Uddin titulado [creación de un HDInsight clúster personalizar](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## <a name="use-net-sdk"></a>Usar el SDK de .NET

Consulte [basados en Linux crear clústeres en HDInsight mediante .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Plantilla de usar el Administrador de recursos

Puede usar el inicio en la plantilla de administrador de recursos:

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight hadoop personalizar la plantilla de administrador de recursos de azure inicio de clúster](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)



## <a name="see-also"></a>Vea también

- [Crear clústeres de Hadoop en HDInsight] [ hdinsight-provision-cluster] proporciona instrucciones sobre cómo crear un clúster de HDInsight con otras opciones personalizadas.
- [Desarrollar secuencias de comandos de acción de secuencia de comandos para HDInsight][hdinsight-write-script]
- [Instalar y usar el motor en clústeres HDInsight][hdinsight-install-spark]
- [Instalar y usar R en clústeres HDInsight][hdinsight-install-r]
- [Instalar y usar clústeres Solr en HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalar y usar clústeres Giraph en HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fases durante la creación de clúster"

## <a name="appx-a-powershell-sample"></a>Ejemplo de PowerShell AppX A:

Esta secuencia de comandos de PowerShell crea un clúster de HDInsight y personaliza la configuración de una sección:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
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
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
        
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion
