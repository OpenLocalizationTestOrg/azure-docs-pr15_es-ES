<properties
    pageTitle="Disponibilidad de Hadoop clústeres en HDInsight | Microsoft Azure"
    description="HDInsight implementa clústeres altamente disponibles y confiables con un nodo principal adicional."
    services="hdinsight"
    tags="azure-portal"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>


#<a name="availability-and-reliability-of-windows-based-hadoop-clusters-in-hdinsight"></a>Disponibilidad y confiabilidad de clústeres basado en Windows Hadoop HDInsight


>[AZURE.NOTE] Los pasos que se utilizan en este documento son específicos para clústeres HDInsight basado en Windows. Si está utilizando un clúster de Linux, vea [disponibilidad y confiabilidad de clústeres basados en Linux Hadoop HDInsight](hdinsight-high-availability-linux.md) para obtener información específica de Linux.

HDInsight permite a los clientes implementar una gran variedad de tipos de clúster, para cargas de trabajo de análisis de datos diferente. Tipos de clúster ofrecidos hoy son Hadoop clústeres para consulta y cargas de trabajo de análisis, clústeres de HBase para cargas de trabajo de NoSQL y clústeres de tormenta de cargas de trabajo de procesamiento de eventos en tiempo real. Dentro de un tipo de clúster determinado, hay funciones diferentes para los distintos nodos. Por ejemplo:



- Se implementan clústeres de Hadoop para HDInsight con dos funciones:
    - Cabeza nodo (2)
    - Nodo de datos (al menos 1 nodo)

- Se implementan clústeres de HBase para HDInsight con tres funciones:
    - Servidores de cabeza (2 nodos)
    - Servidores de región (al menos 1 nodo)
    - Nodos de patrón/Zookeeper (3 nodos)

- Se implementan clústeres de tormenta para HDInsight con tres funciones:
    - Nodos de Nimbus (nodos de 2)
    - Servidores de supervisor (al menos 1 nodo)
    - Nodos de zookeeper (3 nodos)

Implementaciones estándar de Hadoop clústeres normalmente tienen un único nodo principal. HDInsight quita este punto único de error con la adición de un /head nodo principal secundario nodo de servidor/Nimbus para aumentar la disponibilidad y confiabilidad del servicio es necesario para administrar las cargas de trabajo. Estos nodos de servidores/Nimbus cabezal nodos por cabeza están diseñados para administrar el error de nodos de trabajo sin problemas, pero los que las interrupciones de patrón servicios que se ejecutan en el nodo principal haría que el clúster deje de funcionar.


Nodos de [zooKeeper](http://zookeeper.apache.org/ ) (ZKs) se han agregado y se usan para la elección de relleno de nodos de cabeza y para asegurarse de que trabajo nodos y puertas de enlace (GWs) sepan cuándo conmutar secundario al nodo central (cabeza nodo 1) cuando el nodo principal activo (cabeza Node0) se inactiva.

![Diagrama de los nodos de cabeza altamente confiables en la implementación de HDInsight Hadoop.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## <a name="check-active-head-node-service-status"></a>Comprobar el estado del servicio de nodo cabezal activo
Para determinar qué nodo principal está activo y comprobar el estado de los servicios que se ejecuta en ese nodo central, debe conectarse al clúster Hadoop mediante el protocolo de escritorio remoto (RDP). Para las instrucciones de RDP, vea [Administrar Hadoop clústeres en HDInsight a través del portal de Azure](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp). Una vez que ha conectado al clúster, haga doble clic en el icono de **Hadoop servicio disponible** situado en el escritorio para obtener el estado acerca de qué nodo principal la Namenode, servicios de Jobtracker, Templeton, Oozieservice, Metastore y Hiveserver2 están ejecutando o para servicios de HDI 3.0, Namenode, Administrador de recursos, historial de servidor, Templeton, Oozieservice, Metastore y Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

En la captura de pantalla, el nodo principal activo es *headnode0*.

## <a name="access-log-files-on-the-secondary-head-node"></a>Acceso a archivos de registro en el nodo principal secundario

Para obtener acceso a trabajo registros en el nodo secundario principal en caso de que se ha convertido en el nodo principal activo, exploración de la UI JobTracker aún funciona que para el nodo activo principal. Para obtener acceso a JobTracker, debe conectarse al clúster Hadoop mediante RDP como se describe en la sección anterior. Una vez haya remotos en el clúster, haga doble clic en el icono de **Estado de nodo de nombre de Hadoop** situado en el escritorio y, a continuación, haga clic en los **registros de NameNode** para obtener el directorio de registros en el nodo principal secundario.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## <a name="configure-head-node-size"></a>Configurar el tamaño de nodo principal
Los nodos de cabeza se asignan como grandes máquinas virtuales de Windows (VM) de forma predeterminada. Este tamaño es adecuado para la administración de la mayoría de los trabajos Hadoop ejecutar en el clúster. Pero hay situaciones que pueden requerir máquinas virtuales muy grandes para los nodos de cabeza. Un ejemplo es cuando el clúster tiene que administrar un gran número de pequeños trabajos Oozie.

Máquinas virtuales muy grandes pueden configurarse mediante cmdlets de PowerShell de Azure o el SDK HDInsight.

La creación y el suministro de un clúster con PowerShell Azure documentado en [Administrar HDInsight con PowerShell](hdinsight-administer-use-powershell.md). La configuración de un nodo principal muy grande requiere la adición de la `-HeadNodeVMSize ExtraLarge` parámetro la `New-AzureRmHDInsightcluster` cmdlet que se utiliza en el código.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
                -ResourceGroupName $resourceGroupName `
                -ClusterName $clusterName ` 
                -Location $location `
                -HeadNodeVMSize ExtraLarge `
                -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $storageAccountKey `
                -DefaultStorageContainerName $containerName  `
                -ClusterSizeInNodes $clusterNodes

El SDK de la historia es similar. La creación y el suministro de un clúster mediante el SDK documentado en [Usando HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk). La configuración de un nodo principal muy grande requiere la adición de la `HeadNodeSize = NodeVMSize.ExtraLarge` parámetro la `ClusterCreateParameters()` método que se utiliza en el código.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
        Name = clustername,
        Location = location,
        HeadNodeSize = NodeVMSize.ExtraLarge,
        DefaultStorageAccountName = storageaccountname,
        DefaultStorageAccountKey = storageaccountkey,
        DefaultStorageContainer = containername,
        UserName = username,
        Password = password,
        ClusterSizeInNodes = clustersize
    };


## <a name="next-steps"></a>Pasos siguientes

- [Apache ZooKeeper](http://zookeeper.apache.org/ )
- [Conectarse a clústeres HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Usar HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk)
