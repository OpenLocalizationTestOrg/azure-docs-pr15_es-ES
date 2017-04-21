<properties
    pageTitle="Extender HDInsight con una red Virtual | Microsoft Azure"  
    description="Aprenda a usar una red Virtual Azure HDInsight de conectarse a otros recursos de la nube o recursos en el centro de datos"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="larryfr"/>


#<a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Extender HDInsight capacidades mediante red Virtual de Azure

Una red Virtual Azure le permite ampliar sus soluciones Hadoop para incorporar recursos locales como SQL Server, combinar varios tipos de clúster HDInsight o crear redes privadas seguras entre los recursos en la nube.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-and-cli.md)]


##<a id="whatis"></a>¿Qué es una red Virtual Azure?

[Red Virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/) le permite crear una red segura persistente que contiene los recursos que necesita para la solución. Una red virtual le permite:

* Conectarse a recursos de la nube juntos en una red privada (solo de nube).

    ![diagrama de configuración solo nube](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

    Usar una red Virtual para vincular los servicios de Azure con HDInsight de Azure permite las siguientes situaciones:

    * **Servicios de HDInsight invocar o trabajos** desde sitios Web Azure o los servicios que se ejecutan en máquinas virtuales de Windows Azure.

    * **Transferir directamente datos** entre HDInsight y base de datos de SQL Azure, SQL Server u otra solución de almacenamiento de datos se ejecuta en una máquina virtual.

    * **Combinar varios servidores HDInsight** en una única solución. HDInsight formar una gran variedad de tipos, que corresponden a la carga de trabajo o la tecnología de clúster está optimizado para clústeres. No hay ningún método para crear un clúster que combina varios tipos, como tormenta y HBase en un clúster. Usar una red virtual permite varios clústeres comunicarse directamente entre sí.

* Conectar los recursos de la nube a su red local del centro de datos (para sitios o sitio de punto) mediante una red privada virtual (VPN).

    Configuración de sitio a sitio le permite conectar varios recursos desde el centro de datos a la red virtual Azure mediante una VPN de hardware o el servicio de enrutamiento y acceso remoto.

    ![diagrama de configuración de sitio a otro](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

    Configuración del sitio de punto le permite conectarse un recurso específico a la red virtual Azure con software VPN.

    ![diagrama de la configuración del sitio de punto](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

    Usar una red Virtual para vincular la nube y su centro de datos permite escenarios similares a la configuración de nube. Pero, en lugar de limitarse a trabajar con recursos en la nube, también puede trabajar con recursos en el centro de datos.

    * **Transferir directamente datos** entre HDInsight y su centro de datos. Un ejemplo usa Sqoop para transferir datos a o desde SQL Server o leer datos generados por una aplicación de línea de negocio (LOB).

    * **Servicios de HDInsight invocar o trabajos** desde una aplicación LOB. Un ejemplo usa las API de Java HBase para almacenar y recuperar datos de un clúster de HDInsight HBase.

Para obtener más información sobre características de una red Virtual, ventajas y funciones, vea la [información general de la red Virtual de Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE] Debe crear la red Virtual de Azure antes de hacer el aprovisionamiento de un clúster de HDInsight. Para obtener más información, vea [tareas de configuración de red Virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="virtual-network-requirements"></a>Requisitos de red virtuales

> [AZURE.IMPORTANT] Crear un clúster de HDInsight en una red Virtual requiere configuraciones específicas de una red Virtual, que se describen en esta sección.

###<a name="location-based-virtual-networks"></a>Redes virtuales de ubicación

HDInsight de Azure admite solo según ubicación redes virtuales y actualmente no funciona con las redes virtuales basadas en el grupo afinidad.

###<a name="classic-or-v2-virtual-network"></a>Red Virtual v2 o clásico

Clústeres basados en Windows requieren una red Virtual clásica, mientras clústeres basados en Linux requieren una red Virtual del Administrador de recursos de Azure. Si no tiene el tipo correcto de red, no podrán utilizarse al crear el clúster.

Si tiene recursos en una red Virtual que no se puede usar el clúster que va a crear, puede crear una nueva red Virtual que se puede utilizar el clúster y conectarse a la red Virtual incompatible. A continuación, puede crear el clúster en la versión de red requiere y podrá tener acceso a recursos de la otra red puesto que se unen los dos. Para obtener más información sobre la conexión de redes virtuales clásica y nueva, vea [Conectar VNets clásico a VNets nuevo](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

###<a name="custom-dns"></a>Personalizar DNS

Al crear una red virtual, Azure proporciona resolución de nombres de forma predeterminada para los servicios de Azure como HDInsight están instalados en la red. Sin embargo, debe usar su propio sistema de nombres de dominio (DNS) de situaciones como cruzada resolución de nombres de dominio de red. Por ejemplo, cuando la comunicación entre los servicios que se encuentran en dos une redes virtuales. HDInsight admite tanto la resolución de nombres de Azure de forma predeterminada, así como DNS personalizado cuando se utiliza con una red Virtual Azure.

Para obtener más información sobre cómo usar su propio servidor DNS con una red Virtual Azure, consulte la sección de __resolución de nombres con su propio servidor DNS__ del documento de [Resolución de nombres para máquinas virtuales y las instancias de función](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) .

###<a name="secured-virtual-networks"></a>Proteger redes virtuales

El servicio de HDInsight es un servicio gestionado y requiere acceso a Internet durante el aprovisionamiento y mientras se está ejecutando. Se trata de modo que Azure puede supervisar el mantenimiento del clúster, iniciar migraciones tras error de recursos de clúster, cambie el número de nodos en el clúster a través de operaciones de escala y otras tareas de administración.

Si necesita instalar HDInsight en una red Virtual protegida, debe permitir el acceso entrante a través del puerto 443 para las siguientes direcciones IP, que permiten Azure administrar el clúster HDInsight.

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

Permitir el acceso de entrada de puerto 443 para estas direcciones le permitirá instalar correctamente HDInsight en una red virtual protegida.

> [AZURE.IMPORTANT] HDInsight no admite restringir el tráfico saliente, el tráfico de entrada. Al definir reglas de grupo de seguridad de la red de la subred que contiene HDInsight, sólo utilizar las reglas de entrada.

Los siguientes ejemplos muestran cómo crear un nuevo grupo de seguridad de red que permite a las direcciones necesarias y a continuación, se aplica el grupo de seguridad a una subred dentro de su red Virtual. El procedimiento siguiente asume que ya ha creado una red Virtual y la subred que desea instalar HDInsight en.

__Uso de PowerShell de Azure__

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroupId $nsg

__Uso de la CLI de Azure__

1. Utilice el comando siguiente para crear un nuevo grupo de seguridad de red denominado `hdisecure`. Reemplace __RESOURCEGROUPNAME__ y la __ubicación__ con el grupo de recursos que contiene la red Virtual de Azure y la ubicación (región), que se creó el grupo.

        azure network nsg create RESOURCEGROUPNAME hdisecure LOCATION
    
    Una vez creado el grupo, recibirá información en el grupo nuevo. Busque una línea similar a la siguiente y guarde el `/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure` información. Se utilizará en un paso posterior.
    
        data:    Id                              : /subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure

2. Use las siguientes acciones para agregar reglas para el nuevo grupo de seguridad de red que permiten la comunicación entrante en el puerto 443 del servicio de administración y el mantenimiento de HDInsight de Azure. Reemplace __RESOURCEGROUPNAME__ con el nombre del grupo de recursos que contiene la red Virtual de Azure.

        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule1 -p "*" -o "*" -u "443" -f "168.61.49.99" -e "VirtualNetwork" -c "Allow" -y 300 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule2 -p "*" -o "*" -u "443" -f "23.99.5.239" -e "VirtualNetwork" -c "Allow" -y 301 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule3 -p "*" -o "*" -u "443" -f "168.61.48.131" -e "VirtualNetwork" -c "Allow" -y 302 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "443" -f "138.91.141.162" -e "VirtualNetwork" -c "Allow" -y 303 -r "Inbound"

3. Una vez que se han creado las reglas, use las siguientes acciones para aplicar el nuevo grupo de seguridad de red a una subred. Reemplace __RESOURCEGROUPNAME__ con el nombre del grupo de recursos que contiene la red Virtual de Azure. Reemplazar __VNETNAME__ y __SUBNETNAME__ con el nombre de la red Virtual de Azure y la subred que usará al instalar HDInsight.

        azure network vnet subnet set RESOURCEGROUPNAME VNETNAME SUBNETNAME -w "/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    
    Una vez completado este comando, puede instalar HDInsight correctamente en la red Virtual segura en la subred utilizada en estos pasos.

> [AZURE.IMPORTANT] Usar el acceso de solo abrir pasos anteriores para el servicio de administración y el mantenimiento de HDInsight en la nube de Azure. Esto le permite instalar correctamente un clúster de HDInsight en la subred, sin embargo, se bloquea el acceso al clúster HDInsight desde fuera de la red Virtual de forma predeterminada. Tendrá que agregar reglas de grupo de seguridad de red adicionales si desea permitir el acceso desde fuera de la red Virtual.
>
> Por ejemplo, para permitir el acceso SSH desde internet, debe agregar una regla similar al siguiente: 
>
> * Azure PowerShell:```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Azure CLI-```azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "22" -f "*" -e "VirtualNetwork" -c "Allow" -y 304 -r "Inbound"```

Para obtener más información sobre los grupos de seguridad de red, vea [Introducción a los grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md). Para obtener información sobre cómo controlar el enrutamiento en una red Virtual de Azure, vea [rutas de definidas por el usuario e IP de reenvío](../virtual-network/virtual-networks-udr-overview.md).

##<a id="tasks"></a>Tareas y la información

Esta sección contiene información sobre tareas comunes y es posible que tenga con HDInsight de una red virtual.

###<a name="determine-the-fqdn"></a>Determinar el nombre completo

El clúster HDInsight se asignará un nombre de dominio completo (FQDN) específico para la interfaz de red Virtual. Esta es la dirección que se debe usar al conectar con el clúster de otros recursos de la red virtual. Para determinar el nombre de dominio completo, utilice la siguiente dirección URL para consultar el servicio de administración de Ambari:

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Para obtener más información sobre el uso de Ambari con HDInsight, consulte [clústeres Monitor Hadoop en HDInsight con la API de Ambari](hdinsight-monitor-use-ambari-api.md).

Debe especificar el nombre de clúster y un servicio y el componente se ejecuta en el clúster, como el Administrador de recursos del hilo.

> [AZURE.NOTE] Los datos devueltos están un documento de notación de objetos JavaScript (JSON) que contiene una gran cantidad de información sobre el componente. Para extraer solo el nombre de dominio completo, debe usar un analizador JSON para recuperar la `host_components[0].HostRoles.host_name` valor.

Por ejemplo, para devolver el FQDN de un clúster de HDInsight Hadoop, puede usar uno de los siguientes métodos para recuperar los datos para el Administrador de recursos del hilo:

* [Azure PowerShell](../powershell-install-configure.md)

        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix

        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/     components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [doblez](http://curl.haxx.se/) y [jq](http://stedolan.github.io/jq/)

        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###<a name="connecting-to-hbase"></a>Conectarse a HBase

Para conectarse a HBase remotamente mediante la API de Java, debe determinar las direcciones de quórum ZooKeeper para el clúster HBase y especificar esto en la aplicación.

Para obtener a la ZooKeeper dirección quórum, use uno de los siguientes métodos para consultar el servicio de administración de Ambari:

* [Azure PowerShell](../powershell-install-configure.md)

        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix

        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [doblez](http://curl.haxx.se/) y [jq](http://stedolan.github.io/jq/)

        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] Para obtener más información sobre el uso de Ambari con HDInsight, consulte [clústeres Monitor Hadoop en HDInsight con la API de Ambari](hdinsight-monitor-use-ambari-api.md).

Una vez que la información de quórum, use en su aplicación de cliente.

Por ejemplo, para una aplicación de Java que usa la API HBase, ¿agregar un archivo de **site.xml hbase** al proyecto y especifique la información de quórum en el archivo como sigue:

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

###<a name="verify-network-connectivity"></a>Comprobar la conectividad de red

Algunos servicios, como SQL Server, pueden limitar las conexiones entrantes de red. Esto impedirá que HDInsight funciona correctamente con estos servicios.

Si tiene problemas de acceso a un servicio desde HDInsight, consulte la documentación del servicio para asegurarse de que ha habilitado el acceso de red. También puede comprobar el acceso de red mediante la creación de una máquina virtual Azure en la misma red virtual y utilice utilidades de cliente para comprobar que la máquina virtual puede conectarse al servicio de la red virtual.

##<a id="nextsteps"></a>Pasos siguientes

Los siguientes ejemplos muestran cómo usar HDInsight con una red Virtual Azure:

* [Analizar datos de sensor con tormenta y HBase en HDInsight](hdinsight-storm-sensor-data-analysis.md) - muestra cómo configurar un clúster de tormenta y HBase en una red virtual, así como cómo escribir datos de forma remota a HBase de tormenta.

* [Aprovisionar Hadoop clústeres en HDInsight](hdinsight-hadoop-provision-linux-clusters.md) : proporciona información sobre el aprovisionamiento de clústeres Hadoop, incluida la información sobre el uso de la red Virtual de Azure.

* [Usar Sqoop con Hadoop en HDInsight](hdinsight-use-sqoop-mac-linux.md) - proporciona información sobre cómo utilizar Sqoop para transferir datos con SQL Server a través de una red virtual.

Para obtener más información acerca de las redes virtuales Azure, vea la [información general de la red Virtual de Azure](../virtual-network/virtual-networks-overview.md).
