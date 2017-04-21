<properties 
   pageTitle="Acerca de puertas de enlace de red virtual ExpressRoute | Microsoft Azure"
   description="Obtenga información acerca de las puertas de enlace de red virtual para ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="about-virtual-network-gateways-for-expressroute"></a>Acerca de las puertas de enlace de red virtual para ExpressRoute


Una puerta de enlace de red virtual se utiliza para enviar tráfico de red entre redes virtuales Azure y ubicaciones local. Cuando configura una conexión ExpressRoute, debe crear y configurar una puerta de enlace de red virtual y una conexión de red virtual puerta de enlace.

Cuando se crea una puerta de enlace de red virtual, especificar varias configuraciones. Uno de los valores requeridos especifica si se utilizará la puerta de enlace para el tráfico VPN de sitio a otro o ExpressRoute. En el modelo de implementación de administrador de recursos, el valor es '-GatewayType'.

Cuando se envía el tráfico de red en una conexión privada dedicada, use el tipo de puerta de enlace 'ExpressRoute'. También se conoce como una puerta de enlace ExpressRoute. Cuando el tráfico de red se envía cifrado a través de Internet pública, use el tipo de puerta de enlace 'Vpn'. Esto se conoce como una puerta de enlace VPN. Sitio a sitio punto al sitio y las conexiones de VNet a VNet utilizan una puerta de enlace VPN. 

Cada red virtual puede tener solo una puerta de enlace de red virtual por tipo de puerta de enlace. Por ejemplo, puede tener la puerta de enlace de una red virtual que usa - GatewayType Vpn y que usa - GatewayType ExpressRoute. En este artículo se centra en la puerta de enlace de red virtual ExpressRoute.

## <a name="gwsku"></a>SKU de puerta de enlace

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Si desea actualizar la puerta de enlace a un SKU de puerta de enlace más eficaces, en la mayoría de los casos puede usar el cmdlet de PowerShell 'Tamaño AzureRmVirtualNetworkGateway'. Esto funciona para actualizaciones estándar y SKU de alto rendimiento. Sin embargo, para actualizar a la SKU UltraPerformance, debe volver a crear la puerta de enlace.

###  <a name="aggthroughput"></a>Rendimiento agregado estimado SKU de la puerta de enlace


La siguiente tabla muestra los tipos de puerta de enlace y el rendimiento agregado estimado. Esta tabla se aplica al administrador de recursos y modelos de implementación clásico.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)] 


## <a name="resources"></a>Cmdlets de PowerShell y API REST

Para requisitos de sintaxis específica al usar las API de REST y cmdlets de PowerShell para las configuraciones de puerta de enlace de red virtual y recursos técnicos adicionales, vea las páginas siguientes:

|**Clásico** | **Administrador de recursos**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API DE REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API DE REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Pasos siguientes

Vea [Información general de ExpressRoute](expressroute-introduction.md) para obtener más información acerca de las configuraciones de conexión disponible. 







 
