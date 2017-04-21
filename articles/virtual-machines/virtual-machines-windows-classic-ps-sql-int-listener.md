<properties
    pageTitle="Configurar un detector de ILB para siempre en grupos de disponibilidad | Microsoft Azure"
    description="Este tutorial utiliza recursos creados con el modelo de implementación clásica y crea un siempre en disponibilidad grupo escucha en Azure con un equilibrador de carga interno (ILB)."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurar un detector de ILB para siempre en grupos de disponibilidad de Azure

> [AZURE.SELECTOR]
- [Escucha interno](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Escucha externa](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="overview"></a>Información general

Este tema muestra cómo configurar un agente de escucha un siempre en grupo de disponibilidad mediante un **Equilibrador de carga interno (ILB)**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para configurar un detector de ILB para un grupo de disponibilidad siempre en el modelo de administrador de recursos, consulte [configurar un equilibrador de carga interno para un grupo de disponibilidad siempre en Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).


El grupo de disponibilidad pueden contener réplicas que sean local solo, Azure, o span local y Azure configuraciones híbridas. Azure réplicas pueden encontrarse en la misma región o en diferentes regiones con varias redes virtuales (VNets). Los pasos siguientes se suponen que ya ha [configurado un grupo de disponibilidad](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) , pero no ha configurado una escucha.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Directrices y limitaciones para escucha interno
Tenga en cuenta las siguientes directrices en la escucha de grupo de disponibilidad en Azure con ILB:

- La escucha de grupo de disponibilidad es compatible con Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2.

- Escucha de grupo interno disponibilidad solo es compatible por el servicio de nube, porque la escucha está configurada para el ILB y hay ILB solo una por cada servicio de nube. Sin embargo, es posible crear varios agentes de escucha externos. Para obtener más información, vea [Configurar una escucha externa para siempre en disponibilidad de los grupos en Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

- No se admite para crear una escucha interna en el mismo servicio de nube que también tenga una escucha externa usando a pública VIP del servicio nube.

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar la accesibilidad de la escucha

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

En este artículo se centra en la creación de un agente de escucha que usa un **Equilibrador de carga interno (ILB)**. Si necesita una escucha público y externas, vea la versión de este artículo proporciona los pasos para configurar una [escucha externa](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Crear extremos de máquina virtual de equilibrio de carga con el servidor directo devuelto

Para ILB, primero debe crear el equilibrador de carga interno. Para ello, en la siguiente secuencia de comandos.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Para **ILB**, debe asignar una dirección IP estática. En primer lugar, examine la configuración actual de VNet, ejecute el siguiente comando:

        (Get-AzureVNetConfig).XMLConfiguration

1. Anote el nombre de **subred** para la subred que contiene las máquinas virtuales que hospeda las réplicas. Se usará en el parámetro **$SubnetName** en la secuencia de comandos.

1. A continuación, anote el nombre de **VirtualNetworkSite** y la **AddressPrefix** de partida para la subred que contiene las máquinas virtuales que hospeda las réplicas. Busque una dirección IP disponible pasando ambos valores con el comando **AzureStaticVNetIP de prueba** y examinar la **AvailableAddresses**. Por ejemplo, si la VNet se denomina *MyVNet* y ' d rango de una dirección de subred que inició a *172.16.0.128*, el siguiente comando muestra direcciones disponibles:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Elija una de las direcciones disponibles y usarla en el parámetro **$ILBStaticIP** de la siguiente secuencia de comandos.

3. Copie la secuencia de comandos de PowerShell debajo en un editor de texto y establecer los valores de variables para su entorno (tenga en cuenta que ha proporcionado valores predeterminados para algunos parámetros). Tenga en cuenta que las implementaciones existentes que utilizan grupos de afinidad no pueden agregar ILB. Para obtener más información sobre los requisitos de ILB, consulte [Equilibrador de carga interno](../load-balancer/load-balancer-internal-overview.md). Además, si su grupo de disponibilidad abarca áreas de Azure, debe ejecutar la secuencia de comandos de una vez en cada centro de datos para el servicio en la nube y los nodos que residen en ese centro de datos.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

1. Una vez que ha establecido las variables, copie la secuencia de comandos desde el editor de texto en la sesión de Azure PowerShell para ejecutarlo. Si el mensaje se siguen viendo >>, escriba ENTRAR otra vez para asegurarse de que la secuencia de comandos comienza a ejecutarse. Nota

>[AZURE.NOTE] El portal clásico Azure no admite el equilibrador de carga interno en este momento, por lo que no verá la ILB o los extremos en el portal de clásico de Azure. Sin embargo, **Get-AzureEndpoint** devuelve una dirección IP interna si el equilibrador de carga se está ejecutando en él. En caso contrario, devuelve null.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Compruebe que está instalado KB2854082 si es necesario

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra los puertos del firewall de nodos de grupo de disponibilidad

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Crear la escucha de grupo de disponibilidad

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Para ILB, debe usar la dirección IP del equilibrador de carga interno (ILB) creada anteriormente. Utilice la siguiente secuencia de comandos para obtener la dirección IP de PowerShell.

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. En una de las máquinas virtuales, copie la secuencia de comandos de PowerShell para su sistema operativo en un editor de texto y establecer las variables de los valores que se indicó anteriormente.

    Para Windows Server 2012 o posterior, utilice la siguiente secuencia de comandos:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
        
    Para Windows Server 2008 R2, utilice la siguiente secuencia de comandos:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
    

1. Una vez han establecido las variables, abra una ventana de Windows PowerShell elevado, a continuación, copie la secuencia de comandos en el editor de texto y pegar en su sesión de Azure PowerShell para ejecutarlo. Si el mensaje se siguen viendo >>, escriba ENTRAR otra vez para asegurarse de que la secuencia de comandos comienza a ejecutarse.

2. Repita este procedimiento en cada máquina virtual. Esta secuencia de comandos configura el recurso Dirección IP con la dirección IP del servicio de nube y establece otros parámetros como el puerto de sondeo. Cuando se conecte el recurso de dirección IP, a continuación, puede responder a la exploración en el puerto de sondeo desde el extremo de equilibrio de carga que creó anteriormente en este tutorial.

## <a name="bring-the-listener-online"></a>Traer el oyente en línea

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementos de seguimiento

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Probar la escucha de grupo de disponibilidad (dentro de la misma VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
