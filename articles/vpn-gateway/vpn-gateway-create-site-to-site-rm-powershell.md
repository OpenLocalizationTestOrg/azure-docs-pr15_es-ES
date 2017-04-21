<properties
   pageTitle="Crear una red virtual con una conexión VPN de sitio a sitio mediante el Administrador de recursos de Azure y PowerShell | Microsoft Azure"
   description="En este artículo le guiará a través de la creación de un VNet utiliza el modelo de implementación de administrador de recursos y conectarse a su red local local mediante una conexión VPN de S2S de la puerta de enlace."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>Crear una VNet con una conexión de sitio a sitio con PowerShell

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal de Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Administrador de recursos - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Clásico: Portal clásica](vpn-gateway-site-to-site-create.md)

En este artículo le guiará a través de creación de una red virtual y una conexión de puerta de enlace VPN de sitio a sitio a su red local utiliza el modelo de implementación de administrador de recursos de Azure. Conexiones de sitio a sitio pueden usarse para entre local y híbrido configuraciones.

![Diagrama de sitio a otro] (./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "sitio de sitio") 


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implementación y métodos para las conexiones de sitio a otro

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

La siguiente tabla muestra los métodos para las configuraciones de sitio a sitio y modelos de implementación disponible actualmente. Cuando esté disponible un artículo con pasos de configuración, nos vincularlo directamente desde esta tabla. 

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configuraciones adicionales

Si desea conectar VNets, pero no va a crear una conexión a una ubicación local, vea [Configurar una conexión de VNet a VNet](vpn-gateway-vnet-vnet-rm-ps.md). Si desea agregar una conexión de sitio a sitio a una VNet que ya tiene una conexión, vea [Agregar una conexión de S2S a una VNet con una conexión de puerta de enlace VPN existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de empezar

Compruebe que tiene los siguientes elementos antes de comenzar la configuración.

- Un dispositivo VPN compatible y alguien configurarlo. Consulte [acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md). Si no está familiarizado con la configuración de su dispositivo VPN o no están familiarizados con la dirección IP rangos que se encuentran en su local en la configuración de red, necesita coordinar con alguien que puede proporcionar los detalles por usted.

- Una dirección IP pública orientada externamente para su dispositivo VPN. Esta dirección IP no se encuentra detrás de un NAT.
    
- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- La última versión de los cmdlets de PowerShell de administrador de recursos de Azure. Para obtener más información sobre la instalación de los cmdlets de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .


## <a name="Login"></a>1. conectar a su suscripción 

Asegúrese de que cambie al modo de PowerShell para usar los cmdlets del Administrador de recursos. Para obtener más información, vea [Usar Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

Abra la consola de PowerShell y conectarse a su cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

    Login-AzureRmAccount

Compruebe las suscripciones para la cuenta.

    Get-AzureRmSubscription 

Especifique la suscripción que desea usar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="VNet"></a>2. crear una red virtual y una subred de puerta de enlace

Los ejemplos utiliza una subred de puerta de enlace de /28. Si es posible crear una subred de puerta de enlace tan pequeños como /29, le recomendamos que cree una subred mayor que incluye más direcciones seleccionando al menos /28 o /27. Esto le permitirá para que las direcciones de suficiente acomodar posibles configuraciones adicionales que desee en el futuro.

Si ya tiene una red virtual con una subred de puerta de enlace es 29 o más grande, puede pasar a [Agregar la puerta de enlace de red local](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Para crear una red virtual y una subred de puerta de enlace

Use el ejemplo siguiente para crear una red virtual y una subred de puerta de enlace. Reemplace los valores para sus propios. 

Primero, cree un grupo de recursos:
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

A continuación, cree su red virtual. Compruebe que los espacios de direcciones que especifique no superponen a cualquiera de los espacios de direcciones que tiene en su red local.

En el ejemplo siguiente se crea una red virtual denominado *testvnet* y dos subredes, una llamada *GatewaySubnet* y otra denominada *subred1*. Es importante crear una subred denominada específicamente *GatewaySubnet*. Si el nombre algo distinto, se producirá un error en la configuración de conexión. 

Establezca las variables.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Crear la VNet.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Para agregar una subred de puerta de enlace a una red virtual que ya ha creado

Este paso es necesario únicamente si necesita agregar una subred de puerta de enlace a un VNet que creó anteriormente.

Puede crear su subred de puerta de enlace mediante el siguiente ejemplo. Asegúrese de asignar un nombre a la subred de puerta de enlace 'GatewaySubnet'. Si el nombre algo distinto, crear una subred, pero Azure no trata como una subred de puerta de enlace.

Establezca las variables.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Crear la subred de puerta de enlace.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Establecer la configuración. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"> </a>agregar la puerta de enlace de red local

En una red virtual, la puerta de enlace de red local normalmente se refiere a su ubicación local. Dar al sitio un nombre por el que puede hacer referencia a él Azure y también especificar el prefijo de espacio de dirección de la puerta de enlace de red local. 

Azure usa el prefijo de dirección IP que especifique para identificar qué tráfico para enviar a su ubicación local. Esto significa que tiene que especificar cada prefijo de dirección que desee que se asociará con la puerta de enlace de red local. Puede actualizar fácilmente estos prefijos si cambia su red local. 

Al usar los ejemplos de PowerShell, tenga en cuenta lo siguiente:
    
- El *GatewayIPAddress* es la dirección IP del dispositivo VPN local. No se encuentra el dispositivo VPN detrás de un NAT. 
- El *AddressPrefix* es el espacio de direcciones local.

Para agregar una puerta de enlace de red local con un prefijo única dirección:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para agregar una puerta de enlace de red local con varios prefijos de direcciones:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Para modificar prefijos de direcciones IP de la puerta de enlace de red local

En ocasiones, cambian su prefijos de puerta de enlace de red local. Los pasos que necesarios para modificar los prefijos de direcciones IP dependen de si ha creado una conexión VPN de puerta de enlace. Vea la sección de [prefijos de direcciones IP modificar una puerta de enlace de red local](#modify) de este artículo.


## <a name="PublicIP"></a>4. solicitar una dirección IP pública de la puerta de enlace VPN

A continuación, solicitar una dirección IP pública que se asignarán a la puerta de enlace VPN VNet de Azure. Esto no es la misma dirección IP que está asignada a su dispositivo VPN; en su lugar se asigna a la puerta de enlace de VPN de Azure. No puede especificar la dirección IP que desee usar. Se asigna dinámicamente a la puerta de enlace. Utilice esta dirección IP al configurar el dispositivo VPN local para conectarse a la puerta de enlace.

La puerta de enlace VPN de Azure para el modelo de implementación de administrador de recursos actualmente solo es compatible con las direcciones IP públicas a través del método de asignación dinámica. Sin embargo, esto significa que cambiará la dirección IP. Sólo los cambios de la dirección de la puerta de enlace IP Azure VPN es cuando se elimina y vuelve a crear la puerta de enlace. La dirección IP pública de puerta de enlace no cambiará en cambiar el tamaño, restablecer o en otras internas o las actualizaciones de mantenimiento de la puerta de enlace VPN de Azure.

Utilice el siguiente ejemplo de PowerShell:

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="GatewayIPConfig"></a>5. crear la configuración de direccionamiento de IP de puerta de enlace

La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo siguiente para crear la configuración de la puerta de enlace.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## <a name="CreateGateway"></a>6. crear la puerta de enlace de red virtual

En este paso, creará la puerta de enlace de red virtual. Creación de una puerta de enlace, puede tardar mucho tiempo en completarse. A menudo 45 minutos o más. 

Utilice los siguientes valores:

- La *-GatewayType* para una configuración de sitio a sitio es *Vpn*. El tipo de puerta de enlace siempre es específico a la configuración que está implementando. Por ejemplo, - GatewayType ExpressRoute pueden obligarle a otras configuraciones de puerta de enlace. 

- La *-VpnType* puede ser *RouteBased* (denominados una puerta de enlace dinámico documentación) o *PolicyBased* (denominados una puerta de enlace estática documentación). Para obtener más información acerca de los tipos de puerta de enlace VPN, consulte [Acerca de puertas de enlace VPN](vpn-gateway-about-vpngateways.md#vpntype).
- Puede ser la *-GatewaySku* *básica*, *estándar*o *alto rendimiento*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="ConfigureVPNDevice"></a>7. configurar el dispositivo VPN

En este momento, necesita la dirección IP pública de la puerta de enlace de red virtual para configurar el dispositivo VPN local. Trabajar con el fabricante del dispositivo para obtener información de configuración específica. Puede hacer referencia a los [Dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obtener más información.

Para buscar la dirección IP pública de la puerta de enlace de red virtual, use el siguiente ejemplo:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="CreateConnection"></a>8. crear la conexión VPN

A continuación, cree la conexión VPN de sitio a sitio entre la puerta de enlace de red virtual y el dispositivo VPN. Asegúrese de reemplazar los valores con su propio. La clave compartida debe coincidir con el valor que se utiliza para la configuración del dispositivo VPN. Observe que la `-ConnectionType` de sitio a sitio es *IPsec*. 

Establezca las variables.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Crear la conexión.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Se establecerá después de una breve durante la conexión. 

## <a name="toverify"></a>Para comprobar una conexión VPN

Hay varias maneras diferentes para verificar la conexión VPN.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Para modificar prefijos de direcciones IP de una puerta de enlace de red local

Si necesita cambiar los prefijos de la puerta de enlace de red local, utilice las instrucciones siguientes. Se proporcionan dos conjuntos de instrucciones. Las instrucciones que elija dependen de si ya ha creado su conexión de puerta de enlace. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Para modificar la dirección IP de puerta de enlace de una puerta de enlace de red local

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Pasos siguientes

- Puede agregar máquinas virtuales con sus redes virtuales. Consulte [crear una máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para los pasos.

- Para obtener información acerca de BGP, vea la [Información general de BGP](vpn-gateway-bgp-overview.md) y [cómo configurar BGP](vpn-gateway-bgp-resource-manager-ps.md).

