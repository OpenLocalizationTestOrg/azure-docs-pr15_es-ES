<properties
    pageTitle="Administrar grupos de Hadoop de HDInsight con PowerShell | Microsoft Azure"
    description="Obtenga información sobre cómo realizar tareas administrativas para los clústeres Hadoop HDInsight con PowerShell de Azure."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Administrar clústeres de Hadoop en HDInsight con PowerShell de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

PowerShell Azure es un entorno de secuencias de comandos eficaces que puede usar para controlar y automatizar la implementación y administración de las cargas de trabajo en Azure. En este artículo, aprenderá a administrar clústeres Hadoop en HDInsight de Azure mediante una consola de Azure PowerShell local mediante el uso de Windows PowerShell. Para la lista de los cmdlets de HDInsight PowerShell, consulte [referencia de los cmdlets HDInsight][hdinsight-powershell-reference].



**Requisitos previos**

Antes de comenzar este artículo, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="install-azure-powershell"></a>Instale PowerShell Azure

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Si ha instalado Azure PowerShell versión 0.9 x, debe desinstalar antes de instalar una versión más reciente.

Para comprobar la versión de la PowerShell instalado:

    Get-Module *azure*
    
Para desinstalar la versión anterior, ejecutar programas y características en el panel de control. 


##<a name="create-clusters"></a>Crear clústeres

Consulte [basados en Linux crear clústeres en HDInsight con PowerShell de Azure](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

##<a name="list-clusters"></a>Lista clústeres
Use el comando siguiente para mostrar todos los clústeres en la suscripción actual:

    Get-AzureRmHDInsightCluster

##<a name="show-cluster"></a>Mostrar clúster

Use el comando siguiente para mostrar los detalles de un clúster específico en la suscripción actual:

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##<a name="delete-clusters"></a>Eliminar clústeres

Use el comando siguiente para eliminar un clúster:

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

También puede eliminar un clúster eliminando el grupo de recursos que contiene el clúster. Tenga en cuenta, se eliminarán todos los recursos en el grupo incluida la cuenta de almacenamiento predeterminada.

    Remove-AzureRmResourceGroup -Name <Resource Group Name>
            
##<a name="scale-clusters"></a>Clústeres de escala
La característica de ajuste de escala de clúster le permite cambiar el número de nodos de trabajo utilizados por un clúster que se ejecuta en Azure HDInsight sin tener que volver a crear el clúster.

>[AZURE.NOTE] Solo clústeres con HDInsight versión 3.1.3 o superior son compatibles. Si no está seguro de la versión de su clúster, puede comprobar la página de propiedades.  Consulte [clústeres de lista y mostrar](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

El impacto de cambiar el número de nodos de datos para cada tipo de clúster compatible con HDInsight:

- Hadoop

    Sin problemas, puede aumentar el número de nodos de trabajo en un clúster de Hadoop que se está ejecutando sin afectar a los trabajos pendientes o se está ejecutando. También se pueden enviar trabajos de nuevos mientras la operación está en curso. Errores en una operación de escalado se administran correctamente para que el clúster siempre se quede en un estado funcional.

    Cuando se cambia el tamaño de un clúster de Hadoop hacia abajo al reducir el número de nodos de datos, algunos de los servicios en el clúster se reinician. Esto hace todas ellas y trabajos pendientes errores al final de la operación de rotación. Sin embargo, puede volver a enviar las tareas una vez completada la operación.

- HBase

    Sin problemas, puede agregar o quitar nodos en el clúster HBase mientras se está ejecutando. Servidores regionales están equilibrados automáticamente en unos minutos de completar la operación de rotación. Sin embargo, puede ajustar manualmente los servidores regionales inicio de sesión en la headnode de clúster y ejecutando los siguientes comandos desde una ventana de símbolo del sistema:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Tormenta

    Sin problemas, puede agregar o quitar nodos de datos en el clúster de tormenta mientras se está ejecutando. Pero después de la finalización correcta de la operación de escalado, debe equilibrar la topología.

    Volver a equilibrar puede realizarse de dos maneras:

    * Interfaz de usuario de web de tormenta
    * Herramienta de línea de comandos de interfaz

    Consulte la [documentación de tormenta Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obtener más detalles.

    La interfaz de usuario de web tormenta está disponible en el clúster HDInsight:

    ![Equilibrar de escala de tormenta HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Mostramos un ejemplo de cómo usar el comando CLI a equilibrar la topología de tormenta:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Para cambiar el tamaño de clúster Hadoop con Azure PowerShell, ejecute el siguiente comando desde un equipo cliente:

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    

##<a name="grantrevoke-access"></a>Conceder o denegar el acceso a

HDInsight clústeres tienen los siguientes servicios de web HTTP (todos estos servicios tienen extremos REST):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


De forma predeterminada, estos servicios se le ha concedido acceso. Puede revocar o conceder el acceso. Para revocar:

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Para conceder:

    $clusterName = "<HDInsight Cluster Name>"

    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "<Enter the Password>"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
    
    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] Conceder y revocar el acceso, se restablece el clúster nombre de usuario y contraseña.

También puede hacerse a través del Portal. Consulte [Administrar HDInsight mediante el portal de Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Actualizar credenciales de usuario HTTP

Es el mismo procedimiento que [conceder o revocar HTTP access](#grant/revoke-access). Si el clúster se le ha concedido el acceso HTTP, debe revocar primero.  Y, a continuación, conceda acceso con credenciales de usuario HTTP nuevas.


##<a name="find-the-default-storage-account"></a>Buscar la cuenta de almacenamiento predeterminada

El siguiente script de Powershell muestra cómo obtener el nombre de la cuenta de almacenamiento predeterminada y la clave de cuenta de almacenamiento predeterminada para un clúster.

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##<a name="find-the-resource-group"></a>Busque el grupo de recursos

En el modo de administrador de recursos, cada clúster HDInsight pertenece a un grupo de recursos de Azure.  Para buscar el grupo de recursos:

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup


##<a name="submit-jobs"></a>Enviar trabajos

**Enviar trabajos MapReduce**

Vea [ejemplos de ejecutar Hadoop MapReduce en HDInsight de Windows](hdinsight-run-samples.md).

**Enviar trabajos de sección** 

Consulte [Ejecutar subárbol consultas con PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**Enviar trabajos de cerdo**

Vea [trabajos de ejecutar cerdo con PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Enviar trabajos Sqoop**

Consulte [usar Sqoop con HDInsight](hdinsight-use-sqoop.md).

**Enviar trabajos Oozie**

Consulte [Usar Oozie con Hadoop para definir y ejecutar un flujo de trabajo en HDInsight](hdinsight-use-oozie.md).

##<a name="upload-data-to-azure-blob-storage"></a>Cargar datos con el almacenamiento de blobs de Windows Azure
Vea [cargar datos a HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Vea también
* [Documentación de referencia de cmdlet HDInsight][hdinsight-powershell-reference]
* [Administrar HDInsight mediante el portal de Azure][hdinsight-admin-portal]
* [Administrar HDInsight mediante una interfaz de línea de comandos][hdinsight-admin-cli]
* [Crear clústeres HDInsight][hdinsight-provision]
* [Cargar datos a HDInsight][hdinsight-upload-data]
* [Enviar trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Introducción a HDInsight de Azure][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
