<properties
    pageTitle="Crear HBase clústeres en una red Virtual | Microsoft Azure"
    description="Introducción a HBase en HDInsight de Azure. Aprenda a crear HDInsight HBase clústeres en una red Virtual Azure."
    keywords=""
    services="hdinsight,virtual-network"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/18/2016"
   ms.author="jgao"/>

# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Crear clústeres de HBase de red Virtual de Azure 

Obtenga información sobre cómo crear clústeres HDInsight HBase de Azure en una [Red Virtual de Azure][1].

Con la integración de red virtual HBase clústeres se pueden implementar a la misma red virtual que sus aplicaciones para que las aplicaciones puedan comunicarse con HBase directamente. Las ventajas incluyen:

- Conectividad directa de la aplicación web a los nodos del clúster HBase, que permite la comunicación a través del procedimiento de HBase Java remoto de llamadas API (RPC).
- Rendimiento mejorado de no tener el tráfico vaya en varias puertas de enlace y equilibradores de carga.
- La capacidad para procesar información confidencial de forma más segura sin exponer un extremo del público.

###<a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con PowerShell de Azure**. Consulte [instalar y usar PowerShell de Azure](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). 

## <a name="create-hbase-cluster-into-virtual-network"></a>Crear el clúster de HBase en una red virtual

En esta sección, creará un clúster basados en Linux HBase con la cuenta de almacenamiento de Azure dependiente en una red virtual Azure mediante una [plantilla de administrador de recursos de Azure](../resource-group-template-deploy.md). Para otros métodos de creación de clúster y la descripción de la configuración, vea [HDInsight crear clústeres](hdinsight-hadoop-provision-linux-clusters.md). Para obtener más información sobre cómo usar una plantilla para crear clústeres Hadoop en HDInsight, consulte [clústeres de Hadoop crear en HDInsight con plantillas de administrador de recursos de Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

> [AZURE.NOTE] Algunas propiedades han sido codificado en la plantilla. Por ejemplo:
>
> * __Ubicación__: US oriental 2
> * Versión __Cluster: 3.4
> * __Número de nodos de trabajo de clúster__: 4
> * __Cuenta de almacenamiento predeterminada__: &lt;nombre de clúster > almacenar
> * __Nombre de red virtual__: &lt;nombre de clúster >-vnet
> * __Espacio de direcciones de red virtual__: 10.0.0.0/16
> * __Nombre de subred__: predeterminado
> * __Intervalo de direcciones de subred__: 10.0.0.0/24
>
> &lt;Nombre de clúster > se reemplazará con el nombre de clúster proporcionan al utilizar la plantilla.

1. Haga clic en la imagen siguiente para abrir la plantilla en el portal de Azure. La plantilla se encuentra en un contenedor de blob público. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Desde el módulo de **implementación personalizada** , escriba lo siguiente:

    - **Suscripción**: seleccione una suscripción de Azure utilizada para crear el clúster de HDInsight, la cuenta de almacenamiento dependiente y la red virtual Azure.
    - **Grupo de recursos**: seleccione **Crear nuevo**y a continuación, especifique un nuevo nombre de grupo de recursos.
    - **Ubicación**: seleccione una ubicación para el grupo de recursos.
    - **NombreDeClúster**: escriba un nombre para el clúster de Hadoop que se creará.
    - **Contraseña y nombre de inicio de sesión de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
    - **SSH nombre de usuario y contraseña**: el nombre de usuario predeterminado es **sshuser**.  Puede cambiarlo. 
    - **Acepto los términos y las condiciones indicadas arriba**: (seleccione)
    

3. Haga clic en **comprar**. Tarda aproximadamente 20 minutos para crear un clúster. Una vez creado el clúster, puede hacer clic en el módulo de clúster en el portal para abrirlo.

Después de completar el tutorial, desea eliminar el clúster. Con HDInsight, los datos se almacenan en el almacenamiento de Azure, por lo que puede eliminar un clúster cuando no está en uso. También se cargan un clúster HDInsight, incluso cuando no está en uso. Dado que muchas veces más que los cargos para el almacenamiento de los cargos para el clúster, tiene sentido económico para eliminar clústeres cuando no están en uso. Para obtener las instrucciones de la eliminación de un clúster, vea [Administrar Hadoop clústeres en HDInsight a través del portal de Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Para empezar a trabajar con su nuevo clúster HBase, puede usar los procedimientos que se encuentra en [empezar a usar HBase con Hadoop en HDInsight](hdinsight-hbase-tutorial-get-started.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Conectar con el clúster de HBase mediante las API de HBase Java RPC

1.  Crear una infraestructura como una máquina virtual de servicio (IaaS) en la misma red virtual Azure y la misma subred. Para obtener instrucciones sobre cómo crear una nueva máquina virtual de IaaS, consulte [crear una máquina Virtual que ejecuta Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Cuando los pasos de este documento, debe utilizar lo siguiente para la configuración de red:

    - __Red virtual__: &lt;nombre de clúster >-vnet
    - __Subred__: predeterminado

    > [AZURE.IMPORTANT] Reemplazar &lt;nombre de clúster > con el nombre que usó para crear el clúster de HDInsight en los pasos anteriores.

    Mediante estos valores se configurará la máquina virtual para usar la misma red virtual y subred que el clúster HDInsight. Esto le permitirá comunicarse directamente entre sí.

2.  Cuando se usa una aplicación Java para conectarse a HBase de forma remota, debe usar el nombre de dominio completo (FQDN). Para ello, debe obtener el sufijo DNS específicos de la conexión del clúster HBase. Para ello, puede usar uno de los métodos siguientes:

    * Use un explorador Web para realizar una llamada de Ambari:
    
        Vaya a https://&lt;nombreDeClúster >.azurehdinsight.net/api/v1/clusters/&lt;nombreDeClúster > / hosts?minimal_response = true. Parece que un archivo JSON con los sufijos DNS.

    * Use el sitio Web de Ambari:

        1. Vaya a https://&lt;nombreDeClúster >. azurehdinsight.net.
        2. En el menú superior, haga clic en **Hosts** .

    * Usar doblez para realizar llamadas de REST:

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        En los datos de notación de objetos JavaScript (JSON) devueltos, busque la entrada de "host_name". Contendrá el nombre completo de los nodos en el clúster. Por ejemplo:

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        La parte del principio de nombre de dominio con el nombre de clúster es el sufijo DNS. Por ejemplo, mycluster.b1.cloudapp.net.

    * Usar PowerShell Azure
    
        Use el siguiente script de PowerShell de Azure para registrar la función **Get-ClusterDetail** , que se puede utilizar para devolver el sufijo DNS:

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS
                 Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
                .Description
                 This command shows the following 4 properties of an HDInsight cluster:
                 1. ZookeeperQuorum (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (supports only HBase type cluster)
                    Shows a list of host FQDNs that run the HBase REST server.
                 4. FQDNSuffix (supports all cluster types)
                    Shows the FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of HBase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs that run the HBase REST server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows the FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        Después de ejecutar la secuencia de comandos de PowerShell de Azure, use el comando siguiente para devolver el sufijo DNS utilizando la función **Get-ClusterDetail** . Cuando se utiliza este comando, especifique el nombre de clúster HDInsight HBase, administrador y contraseña de administrador.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        Devolverá el sufijo DNS. Por ejemplo, **yourclustername.b4.internal.cloudapp.net**.

    * Usar RDP
    
        También puede usar Escritorio remoto para conectarse al clúster HBase (se conectará al nodo principal) y ejecute **ipconfig** desde el símbolo del sistema para obtener el sufijo DNS. Para obtener instrucciones sobre cómo activar el protocolo de escritorio remoto (RDP) y conexión con el clúster mediante RDP, vea [Administrar Hadoop clústeres en HDInsight con el portal de Azure][hdinsight-admin-portal].
        
        ![hdinsight.HBASE.DNS.surffix][img-dns-surffix]


<!--
3.  Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Para comprobar que la máquina virtual puede comunicarse con el clúster HBase, use el comando `ping headnode0.<dns suffix>` desde el equipo virtual. Por ejemplo, ping headnode0.mycluster.b1.cloudapp.net.

Para usar esta información en una aplicación de Java, puede seguir los pasos de [Experto en usar para generar aplicaciones de Java que utilizan HBase con HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) para crear una aplicación. Para que la aplicación se conecte a un servidor remoto de HBase, modifique el archivo de **site.xml hbase** en este ejemplo, utilice el nombre completo Zookeeper. Por ejemplo:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [AZURE.NOTE] Para obtener más información sobre la resolución de nombres en Azure redes virtuales, incluido cómo usar su propio servidor DNS, vea [Resolución de nombres (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##<a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo crear un clúster de HBase. Para obtener más información, consulte:

- [Introducción a HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configurar la replicación de HBase de HDInsight](hdinsight-hbase-geo-replication.md)
- [Crear clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md)
- [Empezar a usar HBase con Hadoop en HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Analizar la opinión de Twitter con HBase en HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Información general de una red virtual][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Detalles de la disposición para el nuevo clúster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Usar la acción de secuencia de comandos para personalizar un clúster HBase"

[azure-preview-portal]: https://portal.azure.com
