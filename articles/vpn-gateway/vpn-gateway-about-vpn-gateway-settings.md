<properties 
   pageTitle="Acerca de la configuración de puerta de enlace VPN para puertas de enlace de red virtual | Microsoft Azure"
   description="Obtenga información sobre la configuración de puerta de enlace VPN de red Virtual de Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway-settings"></a>Acerca de la configuración de puerta de enlace VPN

Una solución de conexión VPN de puerta de enlace se basa en la configuración de recursos para poder enviar tráfico de red entre redes virtuales y ubicaciones local. Cada recurso contiene valores de configuración. La combinación de los recursos y configuración determina el resultado de la conexión.

Las secciones de este artículo describen los recursos y la configuración que se relacionan con una puerta de enlace VPN en el modelo de implementación de **Administrador de recursos** . Le resultará útil para ver las configuraciones disponibles mediante diagramas de topología de conexión. Puede encontrar las descripciones y diagramas de topología para cada solución de conexión en el artículo [Acerca de la puerta de enlace VPN](vpn-gateway-about-vpngateways.md) . 

## <a name="gwtype"></a>Tipos de puerta de enlace

Cada red virtual sólo puede tener una puerta de enlace de red virtual de cada tipo. Cuando se crea una puerta de enlace de red virtual, asegúrese de que el tipo de puerta de enlace es correcto para su configuración.

Los valores disponibles para - GatewayType son: 

- VPN
- ExpressRoute

Una puerta de enlace VPN requiere la `-GatewayType` *Vpn*.  

Ejemplo:

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased
 

## <a name="gwsku"></a>SKU de puerta de enlace


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>Configuración de la puerta de enlace SKU

**Especificar la SKU de la puerta de enlace en el portal de Azure**

Si utiliza el portal de Azure para crear una puerta de enlace de red virtual del Administrador de recursos, puede seleccionar la puerta de enlace SKU mediante la lista desplegable. Las opciones que se presenta con se corresponden con el tipo de puerta de enlace y VPN que seleccione.

Por ejemplo, si selecciona el tipo de puerta de enlace 'VPN' y el tipo 'directiva basado en VPN', vea SKU 'Básica' porque es la única SKU disponible para VPN PolicyBased. Si selecciona 'Basado en la ruta', puede seleccionar de Basic, estándar y SKU de alto rendimiento. 


**Especificar la puerta de enlace SKU con PowerShell**


En el siguiente ejemplo de PowerShell especifica la `-GatewaySku` como *estándar*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

**Cambiar una puerta de enlace SKU**

Si desea actualizar su SKU de puerta de enlace a un SKU más eficaces (desde básico/estándar para alto rendimiento) puede usar el `Resize-AzureRmVirtualNetworkGateway` cmdlet de PowerShell. También puede reducir el tamaño SKU con este cmdlet de puerta de enlace.

En el siguiente ejemplo de PowerShell muestra una SKU tamaño al alto rendimiento de puerta de enlace.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>Estimado tipo y rendimiento agregado SKU de la puerta de enlace

La siguiente tabla muestra los tipos de puerta de enlace y el rendimiento agregado estimado. Esta tabla se aplica al administrador de recursos y modelos de implementación clásico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 


## <a name="connectiontype"></a>Tipos de conexión

En el modelo de implementación de administrador de recursos, cada configuración requiere un tipo de conexión de la puerta de enlace de red virtual específica. Los valores de la PowerShell Administrador de recursos disponibles para `-ConnectionType` son:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

En el siguiente ejemplo de PowerShell, creamos una conexión de S2S que requiere el tipo de conexión *IPsec*.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipos VPN

Cuando se crea la puerta de enlace de red virtual para una configuración de puerta de enlace VPN, debe especificar un tipo de conexión VPN. El tipo de conexión VPN que elija depende de la topología de conexión que desea crear. Por ejemplo, una conexión de P2S requiere un tipo de VPN RouteBased. Un tipo de VPN también puede confiar en el hardware que va a utilizar. Configuraciones de S2S requieren un dispositivo VPN. Algunos dispositivos VPN solo admiten un determinado tipo de conexión VPN.

Seleccione el tipo de VPN debe cumplir todos los requisitos de conexión para la solución que desea crear. Por ejemplo, si desea crear una conexión VPN de S2S de la puerta de enlace y una conexión VPN de P2S de la puerta de enlace para la misma red virtual, usaría tipo VPN *RouteBased* porque P2S requiere un tipo de VPN RouteBased. También necesitará, compruebe que el dispositivo VPN admite una conexión VPN RouteBased. 

Una vez que se ha creado una puerta de enlace de red virtual, no puede cambiar el tipo de conexión VPN. Tendrá que eliminar la puerta de enlace de red virtual y cree uno nuevo. Existen dos tipos VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


En el siguiente ejemplo de PowerShell especifica la `-VpnType` como *RouteBased*. Cuando se crea una puerta de enlace, debe asegurarse de que el VpnType - es correcta para su configuración. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisitos de la puerta de enlace

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## <a name="gwsub"></a>Subred de puerta de enlace

Para configurar una puerta de enlace de red virtual, debe crear una subred de puerta de enlace para su VNet. Nombre de la subred de puerta de enlace debe *GatewaySubnet* funcione correctamente. Este nombre permite saber que se debe usar esta subred de la puerta de enlace a Azure.

El tamaño mínimo de la subred de puerta de enlace depende por completo de la configuración que desea crear. Aunque es posible crear una subred de puerta de enlace tan pequeños como /29, le recomendamos que cree una subred de puerta de enlace de /28 o mayor (/ 28, /27, /26, etcetera.). 

Crear un tamaño de la puerta de enlace impide que supere las limitaciones de tamaño de puerta de enlace. Por ejemplo, que ha creado una puerta de enlace de red virtual con un tamaño de subred de puerta de enlace /29 para una conexión de S2S. Ahora desea configurar un S2S/ExpressRoute coexistencia configuración. Dicha configuración requiere un tamaño mínimo /28 subred de puerta de enlace. Para crear su configuración, tendrá que modificar la subred de puerta de enlace para acomodar el requisito mínimo para la conexión, que es /28.

En el siguiente ejemplo de administrador de recursos PowerShell muestra una subred de puerta de enlace con el nombre GatewaySubnet. Puede ver que la notación CIDR especifica un /27, lo que permite suficientes direcciones IP para la mayoría de las configuraciones que existen actualmente.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 


## <a name="lng"></a>Puertas de enlace de red local

Al crear una configuración de puerta de enlace VPN, la puerta de enlace de red local representa a menudo su ubicación local. En el modelo de implementación clásica, la puerta de enlace de red local se conoce como un sitio Local. 

Asigne un nombre, la dirección IP pública del dispositivo VPN local, de la puerta de enlace de red local y especificar los prefijos de dirección que se encuentran en la ubicación local. Azure busca en los prefijos de dirección de destino para el tráfico de red, consulta la configuración que haya especificado para la puerta de enlace de red local y enruta paquetes según corresponda. También especificar puertas de enlace de red local para las configuraciones de VNet a VNet que usan una conexión VPN de puerta de enlace.

En el ejemplo de PowerShell siguiente se crea una nueva puerta de enlace de red local:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

A veces necesita modificar la configuración de puerta de enlace de red local. Por ejemplo, al agregar o modificar el intervalo de direcciones, o si cambia la dirección IP del dispositivo VPN. Para un VNet clásico, puede cambiar esta configuración en el portal de clásico en la página de redes locales. Para el Administrador de recursos, vea [modificar de la configuración de puerta de enlace de red local con PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>Cmdlets de PowerShell y API REST

Para requisitos de sintaxis específica al usar las API de REST y cmdlets de PowerShell para las configuraciones de puerta de enlace VPN y recursos técnicos adicionales, vea las páginas siguientes:

|**Clásico** | **Administrador de recursos**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API DE REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API DE REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Pasos siguientes

Consulte [Acerca de la puerta de enlace VPN](vpn-gateway-about-vpngateways.md) para obtener más información acerca de las configuraciones de conexión disponible. 







 
