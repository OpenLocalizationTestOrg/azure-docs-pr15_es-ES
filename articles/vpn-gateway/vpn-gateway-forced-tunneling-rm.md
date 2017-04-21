<properties 
   pageTitle="Configurar el túnel forzado para conexiones de sitio a otro mediante el modelo de implementación de administrador de recursos | Microsoft Azure"
   description="Cómo redirigir o forzar todo el tráfico de Internet enlazado a su ubicación local."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurar el túnel forzado utiliza el modelo de implementación de administrador de recursos de Azure

> [AZURE.SELECTOR]
- [PowerShell - clásica](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Administrador de recursos](vpn-gateway-forced-tunneling-rm.md)

Túnel forzado le permite redirigir o "forzar" todo el tráfico de Internet enlazado volver a su ubicación local a través de un túnel VPN de sitio a sitio de inspección y auditoría. Se trata de un requisito de seguridad crítica de TI la mayoría de las empresas directivas.

Sin túnel forzado, tráfico de Internet enlazado de sus máquinas virtuales en Azure pasarán por siempre de infraestructura de red Azure directamente fuera a Internet, sin la opción para que pueda inspeccionar o el tráfico de auditoría. Acceso no autorizado de Internet puede dar como resultado revelación de información u otros tipos de problemas de seguridad

En este artículo se explica cómo configurar forzado túnel para redes virtuales creadas con el modelo de implementación de administrador de recursos.

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modelos de implementación y herramientas para túnel forzado**

Se pueden configurar una conexión túnel forzada para el modelo de implementación clásico y el modelo de implementación de administrador de recursos. Consulte la tabla siguiente para obtener más información. Actualizamos esta tabla como nuevos artículos, nuevos modelos de implementación y otras herramientas no estarán disponibles para esta configuración. Cuando un artículo está disponible, nos vincularlo directamente desde la tabla.

[AZURE.INCLUDE [vpn-gateway-table-forced-tunneling](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="about-forced-tunneling"></a>Información sobre forzado túnel


El siguiente diagrama muestra cómo forzado works túnel. 

![Se obliga túnel](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

En el ejemplo anterior, el cliente no se fuerza subred túnel. Pueden seguir las cargas de trabajo en la subred front-end Aceptar y responder a solicitudes de clientes de Internet directamente. Las subredes back-end y el nivel intermedio tienen túnel. Se obliga o redirige a un sitio local a través de uno de los túneles VPN S2S las conexiones salientes de estas dos subredes a Internet.

Esto le permite restringir y acceso a Internet desde sus máquinas virtuales de inspeccionar o cloud services en Azure, mientras sigue habilitar la arquitectura de servicio de varios niveles necesaria. También puede aplicar túnel forzado a las redes virtuales completas si no hay ningún cargas de trabajo a través de Internet en sus redes virtuales.

## <a name="requirements-and-considerations"></a>Consideraciones y requisitos

Túnel forzado en Azure está configurado a través de rutas de red virtual definidas por el usuario. Redirigir el tráfico a un sitio local se expresa como una ruta predeterminada a la puerta de enlace VPN de Azure. Para obtener más información acerca de enrutamiento definidas por el usuario y redes virtuales, vea [rutas y reenvío IP definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md).

- Cada subred de red virtual tiene una tabla de enrutamiento de sistema integradas. La tabla de enrutamiento de sistema tiene los siguientes tres grupos de rutas:

    - **VNet local redirige:** Directamente en las máquinas virtuales de destino en la misma red virtual
    
    - **Rutas locales:** La puerta de enlace VPN de Azure
    
    - **Ruta predeterminada:** Directamente a Internet. Se quitarán los paquetes destinados a las direcciones IP privadas no cubiertas por las rutas de dos anterior.

-  Este procedimiento utiliza rutas definidas por el usuario (UDR) para crear una tabla de enrutamiento para agregar una ruta predeterminada y, a continuación, asociar la tabla de enrutamiento para su subredes VNet habilitar túnel forzado en esas subredes.

- Túnel forzado debe estar asociado con un VNet que tenga una puerta de enlace VPN basado en la ruta. Debe establecer un "sitio de predeterminado" entre los sitios local de cruz local conectado a la red virtual.

- ExpressRoute forzado túnel no está configurado a través de este mecanismo, pero está habilitada en su lugar, anuncia una ruta predeterminada a través de las sesiones de interconexión ExpressRoute BGP. Consulte la [Documentación de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para obtener más información.

## <a name="configuration-overview"></a>Introducción a la configuración

El procedimiento siguiente le ayuda a crear un grupo de recursos y un VNet. A continuación, deberá crear una puerta de enlace VPN y configurar el túnel forzado. En este procedimiento, la red virtual "VNet de varios niveles" tiene 3 subredes: *front-end*, *nivel intermedio*y *back-end*, con 4 entre conexiones locales: *DefaultSiteHQ*y 3 *ramas*.

Los pasos del procedimiento el *DefaultSiteHQ* como la conexión de sitio predeterminado para forzado túnel y configurar nivel intermedio y back-end subredes usar forzado túnel.

    
## <a name="before-you-begin"></a>Antes de empezar

Compruebe que tiene los siguientes elementos antes de comenzar la configuración.

- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Debe instalar la última versión de los cmdlets de PowerShell de administrador de recursos de Azure (1.0 o posteriores). Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .


## <a name="configure-forced-tunneling"></a>Configurar el túnel forzado

1. En la consola de PowerShell, inicie sesión en su cuenta de Azure. Este cmdlet le solicitará las credenciales de inicio de sesión para su cuenta de Azure. Después de iniciar sesión, descarga la configuración de cuenta para que estén disponibles para Azure PowerShell.

        Login-AzureRmAccount 

2. Obtener una lista de las suscripciones de Azure.

        Get-AzureRmSubscription

2. Especifique la suscripción que desea usar. 

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
        
3. Crear un grupo de recursos.

        New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Crear una red virtual y especificar subredes. 

        $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
        $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
        $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
        $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
        $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Crear las puertas de enlace de red local.

        $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
        $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
        $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
        $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
        
6. Crear la tabla de rutas y una regla de ruta.

        New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
        $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
        Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
        Set-AzureRmRouteTable -RouteTable $rt


7. Asociar la tabla de rutas a las subredes de nivel intermedio y back-end.

        $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
        Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

8. Crear la puerta de enlace con un sitio predeterminado. Este paso lleva algún tiempo en completarse, a veces 45 minutos o más, porque es crear y configurar la puerta de enlace.<br> La `-GatewayDefaultSite` es el parámetro de cmdlet que permite la configuración de enrutamiento forzada a trabajar, así que tenga cuidado al configurar este valor correctamente. Este parámetro está disponible en PowerShell 1.0 o posterior.

        $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
        $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
        New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

9. Establecer las conexiones VPN de sitio a otro.

        $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
        $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
        $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
        $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
        $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

        Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
        



