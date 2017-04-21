<properties
   pageTitle="Configurar conexiones VPN Expressroute y a sitios que pueden coexistencia para el modelo de implementación de administrador de recursos | Microsoft Azure"
   description="En este artículo se explica cómo configurar una conexión VPN de sitio a sitio que puede coexistencia para el modelo de administrador de recursos y ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="charleywen"/>

# <a name="configure-expressroute-and-site-to-site-coexisting-connections-for-the-resource-manager-deployment-model"></a>Configurar conexiones de coexistentes ExpressRoute y sitio a otro para el modelo de implementación de administrador de recursos

> [AZURE.SELECTOR]
- [PowerShell - Administrador de recursos](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - clásica](expressroute-howto-coexist-classic.md)

Tener la capacidad de configurar VPN de sitio a sitio y ExpressRoute tiene varias ventajas. Puede configurar VPN de sitio a sitio como una ruta de acceso seguro migración tras error para ExressRoute o usar VPN de sitio a sitio para conectarse a los sitios que no están conectados a través de ExpressRoute. Trataremos los pasos para configurar ambos escenarios en este artículo. En este artículo se aplica al modelo de implementación de administrador de recursos. Esta configuración no está disponible en el portal de Azure.


**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] Circuitos ExpressRoute deben estar configurados previamente antes de seguir las instrucciones a continuación. Asegúrese de que ha seguido a las guías para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y [Configurar el enrutamiento](expressroute-howto-routing-arm.md) antes de seguir los pasos siguientes.

## <a name="limits-and-limitations"></a>Límites y limitaciones

- **No se admite el enrutamiento de tránsito.** No se pueden enrutar (a través de Azure) entre su red local conectado a través de VPN de sitio a sitio y su red local conectado a través de ExpressRoute.
- **No se admite la puerta de enlace SKU básica.** Debe utilizar una puerta de enlace no SKU básica para la [puerta de enlace de ExpressRoute](expressroute-about-virtual-network-gateways.md) y la [puerta de enlace VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Sólo basado en ruta puerta de enlace VPN es compatible.** Debe usar un basado en la ruta de [Puerta de enlace VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Ruta estática debe configurarse de la puerta de enlace VPN.** Si su red local está conectada a ExpressRoute y una VPN de sitio a sitio, debe tener una ruta estática configurada en su red local para enrutar la conexión VPN de sitio a sitio a Internet.
- **ExpressRoute puerta de enlace debe estar configurado en primer lugar.** En primer lugar debe crear la puerta de enlace ExpressRoute antes de agregar la puerta de enlace VPN de sitio a otro.


## <a name="configuration-designs"></a>Diseños de configuración

### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurar una VPN de sitio a sitio como una ruta de migración tras error para ExpressRoute

Puede configurar una conexión VPN de sitio a sitio como una copia de seguridad para ExpressRoute. Esto se aplica solo a redes virtuales vinculadas a la ruta de interconexión privada Azure. No hay ninguna solución basado en VPN de migración tras error para los servicios accesible a través de Azure público y peerings de Microsoft. Circuito de ExpressRoute siempre es el vínculo principal. Datos fluirá a través de la ruta de acceso VPN de sitio a sitio solo si se produce un error en el circuito de ExpressRoute.
>[AZURE.NOTE] Mientras ExpressRoute circuito está preferida sobre VPN de sitio a sitio cuando ambas rutas son los mismos, Azure utilizará a la coincidencia de prefijo longuest para elegir la ruta hacia el destino del paquete.

![Coexistencia](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurar una conexión VPN para conectarse a los sitios que no está conectados a través de ExpressRoute para sitios

Puede configurar su red donde algunos sitios se conectan directamente a Azure sobre VPN de sitio a sitio y algunos sitios a través de ExpressRoute. 

![Coexistencia](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] No puede configurar una red virtual como enrutador tránsito.

## <a name="selecting-the-steps-to-use"></a>Selección de los pasos para usar

Hay dos conjuntos de procedimientos para elegir para configurar las conexiones que pueden coexistencia diferentes. El procedimiento de configuración que seleccione dependen de si tiene una red virtual existente que desea conectarse o que desea crear una nueva red virtual.


- No tiene un VNet y necesita crear uno.
    
    Si todavía no tiene una red virtual, este procedimiento le guiará a través de creación de una red virtual nueva usando el modelo de implementación de administrador de recursos y la creación de nuevas conexiones VPN ExpressRoute y sitio a otro. Para configurar, siga los pasos de la sección de artículo [para crear una nueva red virtual y coexistentes conexiones](#new).

- Ya tengo un modelo de implementación de administrador de recursos VNet.

    Ya puede tener una red virtual en lugar de con una conexión VPN de sitio a sitio existente o una conexión de ExpressRoute. La sección [Configurar conexiones de coexsiting para un VNet existente](#add) le guiará a través de eliminar la puerta de enlace y, a continuación, crear nuevas conexiones VPN ExpressRoute y sitio a otro. Tenga en cuenta que al crear nuevas conexiones, deben completar los pasos en un orden muy específico. No use las instrucciones que aparecen en otros artículos para crear las conexiones y puertas de enlace.

    En este procedimiento, crear conexiones que pueden coexistencia se requieren que se va a eliminar la puerta de enlace y a continuación, configure las puertas de enlace nuevos. Esto significa que tendrá el tiempo de inactividad para las conexiones entre local al eliminar y volver a crear la puerta de enlace y conexiones, pero no necesitará migrar máquinas virtuales o servicios a una red virtual nueva. Las máquinas virtuales y servicios aún podrán comunicarse fuera a través del equilibrador de carga mientras configura la puerta de enlace si están configurados para ello.


## <a name="new"></a>Para crear una nueva red virtual y conexiones coexistentes

Este procedimiento se le guíe durante la creación de un VNet y crear sitio a sitio y las conexiones de ExpressRoute se coexisten.
    
1. Debe instalar la última versión de los cmdlets de PowerShell de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) . Tenga en cuenta que los cmdlets que se usará para esta configuración puede ser un poco distintos de lo que es posible que esté familiarizado con. Asegúrese de usar los cmdlets especificados en estas instrucciones.

2. Inicio de sesión de su cuenta y configurar el entorno.
    
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. Crear una red virtual incluidos subred de puerta de enlace. Para obtener más información sobre la configuración de red virtual, vea [configuración de red Virtual de Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).

    >[AZURE.IMPORTANT] La subred de puerta de enlace debe ser /27 o un prefijo más corto (como /26 o /25).
    
    Crear un nuevo VNet.

        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

    Agregar subredes.

        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Guardar la configuración de VNet.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Crear una puerta de enlace ExpressRoute. Para obtener más información sobre la configuración de puerta de enlace ExpressRoute, consulte [configuración de puerta de enlace de ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Debe ser la GatewaySKU *estándar*, *alto rendimiento*o *UltraPerformance*.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Vínculo de la puerta de enlace ExpressRoute al circuito ExpressRoute. Una vez se haya completado este paso, se establece la conexión entre su red local y Azure a través de ExpressRoute. Para obtener más información acerca de la operación de vinculación, vea [VNets vínculo a ExpressRoute](expressroute-howto-linkvnet-arm.md).

        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. <a name="vpngw"></a>A continuación, cree la puerta de enlace VPN de sitio a otro. Para obtener más información sobre la configuración de puerta de enlace VPN, consulte [configurar un VNet con una conexión de sitio a otro](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). Debe ser la GatewaySKU *estándar*, *alto rendimiento*o *UltraPerformance*. El VpnType debe *RouteBased*.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

    La puerta de enlace VPN Azure es compatible con la BGP. Puede especificar - EnableBgp en el siguiente comando.

        $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -EnableBgp $true

    Puede encontrar el BGP interconexión IP y el número de AS Azure se usa para la puerta de enlace VPN $azureVpn.BgpSettings.BgpPeeringAddress y $azureVpn.BgpSettings.Asn. Para obtener más información, vea [Configurar BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) de puerta de enlace VPN de Azure.

7. Crear un sitio local entidad de puerta de enlace VPN. Este comando no configura la puerta de enlace VPN local. En su lugar, permita que proporcione la configuración de puerta de enlace local, como la dirección IP pública y local en el espacio de direcciones, para que la puerta de enlace de Azure VPN puedan conectarse a él.

    Si el dispositivo VPN local solo admite enrutamiento estático, puede configurar las rutas estáticas de la siguiente manera.

        $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress

    Si el dispositivo VPN local admite el BGP y desea habilitar enrutamiento dinámico, debe saber la BGP interconexión IP y el número de AS que usa el dispositivo VPN local.

        $localVPNPublicIP = "<Public IP>"
        $localBGPPeeringIP = "<Private IP for the BGP session>"
        $localBGPASN = "<ASN>"
        $localAddressPrefix = $localBGPPeeringIP + "/32"
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN

8. Configurar el dispositivo VPN local para conectarse a la nueva puerta de enlace VPN de Azure. Para obtener más información sobre la configuración de dispositivo VPN, consulte [Configuración de dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Vincular la puerta de enlace VPN de sitio a sitio en Azure a la puerta de enlace local.

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>Configurar conexiones de coexsiting para un VNet ya existente

Si tiene una red virtual existente, compruebe el tamaño de la subred de puerta de enlace. Si la subred de puerta de enlace es /28 o /29, primero debe eliminar la puerta de enlace de red virtual y aumentar el tamaño de la subred de puerta de enlace. Los pasos descritos en esta sección le mostrará cómo hacerlo.

Si la subred de puerta de enlace es /27 o mayor y la red virtual está conectada a través de ExpressRoute, puede omitir los pasos siguientes y continúe en ["Paso 6: crear una puerta de enlace VPN de sitio a sitio"](#vpngw) en la sección anterior. 

>[AZURE.NOTE] Al eliminar la puerta de enlace existente, sus instalaciones locales perderán la conexión con su red virtual mientras está trabajando en esta configuración. 

1. Debe instalar la última versión de los cmdlets de PowerShell de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) . Tenga en cuenta que los cmdlets que se usará para esta configuración puede ser un poco distintos de lo que es posible que esté familiarizado con. Asegúrese de usar los cmdlets especificados en estas instrucciones. 

2. Eliminar la puerta de enlace VPN de sitio a otro o ExpressRoute existente. 

        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. Eliminar subred de puerta de enlace.
        
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. Agregar una subred de puerta de enlace es /27 o mayor.
    >[AZURE.NOTE] Si no tiene suficientes direcciones IP a la izquierda de su red virtual para aumentar el tamaño de la subred de puerta de enlace, debe agregar más espacio de direcciones IP.

        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Guardar la configuración de VNet.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. En este momento, tendrá un VNet con ningún puertas de enlace. Para crear nuevas puertas de enlace y completar las conexiones, puede continuar con el [paso 4: crear una puerta de enlace de ExpressRoute](#gw), que se encuentra en el conjunto de pasos anterior.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Para agregar la configuración del sitio de punto a la puerta de enlace VPN
Puede seguir los pasos siguientes para agregar la configuración del sitio de punto a la puerta de enlace VPN en una configuración de coexistencia.

1. Agregar conjunto de direcciones del cliente VPN. 

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. Cargar el certificado de raíz de la VPN en Azure de la puerta de enlace VPN. En este ejemplo, se supone que se almacena el certificado raíz en el equipo local donde se ejecutan los siguientes cmdlets de PowerShell. 

        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Para obtener más información sobre VPN punto al sitio, vea [Configurar una conexión punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre ExpressRoute, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md).
