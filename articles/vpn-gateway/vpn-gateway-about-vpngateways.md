<properties 
   pageTitle="Acerca de la puerta de enlace VPN | Microsoft Azure"
   description="Obtenga información sobre las conexiones de puerta de enlace VPN para redes virtuales de Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway"></a>Acerca de la puerta de enlace VPN


Una puerta de enlace de red virtual se usa para enviar tráfico de red entre redes virtuales Azure y ubicaciones locales y también entre redes virtuales dentro de Azure (VNet a VNet). Al configurar una puerta de enlace VPN, debe crear y configurar una puerta de enlace de red virtual y una conexión de red virtual puerta de enlace.

En el modelo de implementación de administrador de recursos, cuando se crea un recurso de puerta de enlace de red virtual, especificar varias configuraciones. Uno de los valores requeridos es '-GatewayType'. Existen dos tipos de puerta de enlace de red virtual: Vpn y ExpressRoute. 

Cuando se envía el tráfico de red en una conexión privada dedicada, use el tipo de puerta de enlace 'ExpressRoute'. También se conoce como una puerta de enlace ExpressRoute. Cuando el tráfico de red se envía cifrado a través de una conexión pública, use el tipo de puerta de enlace 'Vpn'. Esto se conoce como una puerta de enlace VPN. Sitio a sitio punto al sitio y las conexiones de VNet a VNet utilizan una puerta de enlace VPN.

Cada red virtual puede tener solo una puerta de enlace de red virtual por tipo de puerta de enlace. Por ejemplo, puede tener la puerta de enlace de una red virtual que usa - GatewayType ExpressRoute y a continuación, una que usa - GatewayType Vpn. En este artículo se centra principalmente en la puerta de enlace VPN. Para obtener más información sobre ExpressRoute, vea la [Información general técnica de ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Precios

[AZURE.INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)] 


## <a name="gateway-skus"></a>SKU de puerta de enlace

[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Para obtener más información acerca de la puerta de enlace SKU, vea [SKU de puerta de enlace](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Rendimiento agregado estimado por SKU

La siguiente tabla muestra los tipos de puerta de enlace y el rendimiento agregado estimado. Esta tabla se aplica al administrador de recursos y modelos de implementación clásico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="configuring-a-vpn-gateway"></a>Configurar una puerta de enlace VPN

Al configurar una puerta de enlace VPN, las instrucciones que utilice dependen del modelo de implementación que usó para crear la red virtual. Por ejemplo, si ha creado su VNet utiliza el modelo de implementación clásica, se utilizan las directrices y las instrucciones para el modelo de implementación clásico para crear y configurar la configuración de puerta de enlace VPN. Para obtener más información acerca de los modelos de implementación, vea [Descripción de administrador de recursos y los modelos de implementación clásico](../resource-manager-deployment-model.md).

Una conexión VPN de puerta de enlace se basa en varios de los recursos que estén configurados con una configuración específica. La mayoría de los recursos se puede configurar por separado, aunque se debe configurar en un orden determinado en algunos casos. Puede empezar a crear y configurar recursos mediante una herramienta de configuración, como el portal de Azure. A continuación, puede decidir más adelante cambiar a otra herramienta, como PowerShell para configurar recursos adicionales, o para modificar los recursos existentes cuando corresponda. Actualmente, no puede configurar cada valor de recursos y recursos en el portal de Azure. Las instrucciones en los artículos para cada topología de conexión especifican cuándo se necesita una herramienta de configuración específica. Para obtener información acerca de los recursos individuales y la configuración de puerta de enlace VPN, consulte [configuración de puerta de enlace de sobre VPN](vpn-gateway-about-vpn-gateway-settings.md).

Las secciones siguientes contienen tablas de la lista:

- modelo de implementación disponibles
- herramientas de configuración disponibles
- vínculos que llevan directamente a un artículo, si está disponible

Use los diagramas y descripciones para seleccionar la topología de conexión para que coincida con los requisitos. Los diagramas muestran las topologías de línea base principal, pero es posible crear configuraciones más complejas con los diagramas como una regla.

## <a name="site-to-site-and-multi-site"></a>Para sitios y múltiples sitios

### <a name="site-to-site"></a>Sitio de sitio

Una conexión VPN de sitio a sitio (S2S) de la puerta de enlace es una conexión a través de túnel VPN IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN encuentra local que tiene una dirección IP pública asignada y no se encuentra detrás de un NAT. Conexiones de S2S pueden usarse para entre local y híbrido configuraciones.   

![Conexión de S2S] (./media/vpn-gateway-about-vpngateways/demos2s.png "sitio de sitio")


### <a name="multi-site"></a>Múltiples sitios

Puede crear y configurar una conexión VPN de puerta de enlace entre su VNet y varias redes de local. Al trabajar con varias conexiones, debe utilizar un tipo de RouteBased VPN (puerta de enlace dinámico para VNets clásico). Porque una VNet sólo puede tener una puerta de enlace VPN, todas las conexiones a través de la puerta de enlace comparten el ancho de banda disponible. A menudo se denomina una conexión "múltiples sitio".
 

![Conexión de múltiples sitios] (./media/vpn-gateway-about-vpngateways/demomulti.png "múltiples sitios")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modelos de implementación y métodos de sitio a sitio y múltiples sitios

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## <a name="vnet-to-vnet"></a>VNet a VNet

Conectarse a una red virtual a otra red virtual (VNet a VNet) es similar a una VNet a conectarse a una ubicación del sitio local. Ambos tipos de conectividad usan una puerta de enlace VPN para proporcionar un túnel seguro mediante IPsec/IKE. Incluso puede combinar VNet a VNet comunicación con las configuraciones de conexión de múltiples sitios. Esto le permite establecer topologías de red que se combinan conectividad entre local con conectividad de red entre virtual.

Puede ser la VNets que conectar:

- en el mismos o diferentes regiones
- en la mismas u otra suscripciones 
- en la misma diferentes modelos de implementación


![VNet a VNet conexión] (./media/vpn-gateway-about-vpngateways/demov2v.png "vnet a vnet")

#### <a name="connections-between-deployment-models"></a>Conexiones entre modelos de implementación

Azure tiene dos modelos de implementación: clásico y el Administrador de recursos. Si ha estado utilizando Azure algún tiempo, probablemente tenga máquinas virtuales de Azure y roles de instancia ejecuta en una VNet clásico. Sus máquinas virtuales más recientes y las instancias de función pueden estar ejecutándose en un VNet creado en el Administrador de recursos. Puede crear una conexión entre la VNets para permitir que los recursos en una VNet para comunicarse directamente con los recursos en otro.

#### <a name="vnet-peering"></a>VNet interconexión

Es posible que pueda usar VNet interconexión para crear la conexión, como la red virtual cumpla ciertos requisitos. Interconexión VNet no utiliza una puerta de enlace de red virtual. Para obtener más información, vea [VNet interconexión](../virtual-network/virtual-network-peering-overview.md).


### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implementación y métodos para VNet a VNet

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## <a name="point-to-site"></a>Sitio de punto

Una conexión VPN de punto de sitio (P2S) de la puerta de enlace le permite crear una conexión segura a su red virtual desde un equipo cliente individuales. P2S es una conexión VPN sobre SSTP (Secure Socket túnel protocolo). Conexiones de P2S no requieren un dispositivo VPN o una dirección IP de público para que funcione. Establecer la conexión VPN iniciando desde el equipo cliente. Esta solución es útil cuando desea conectarse a su VNet desde una ubicación remota, como desde casa o de una conferencia, o solo tiene unos pocos clientes que necesitan conectarse a un VNet. Conexiones de P2S pueden usarse junto con conexiones de S2S a través de la misma puerta de enlace VPN, siempre que todos los requisitos de configuración para ambas conexiones son compatibles.


Conexión punto a sitio de ![] (./media/vpn-gateway-about-vpngateways/demop2s.png "sitio de punto")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Modelos de implementación y métodos para el sitio de punto

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## <a name="expressroute"></a>ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

En una conexión ExpressRoute, una puerta de enlace de red virtual se configura con el tipo de puerta de enlace 'ExpressRoute', en lugar de 'Vpn'. Para obtener más información sobre ExpressRoute, vea la [información general técnica de ExpressRoute](../expressroute/expressroute-introduction.md).


## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Conexiones de coexistentes a sitios y ExpressRoute

ExpressRoute es una conexión directa y dedicada de su WAN (no a través de Internet pública) para Microsoft Services, incluidos Azure. Viajes de tráfico de sitio a sitio VPN cifradas a través de Internet pública. Posibilidad de configurar las conexiones VPN de sitio a sitio y ExpressRoute para la misma red virtual tiene varias ventajas.

Puede configurar una VPN de sitio a sitio como una ruta de acceso seguro migración tras error para ExpressRoute o usar VPN de sitio a sitio para conectarse a los sitios que no forman parte de su red, pero que está conectado a través de ExpressRoute. Observe que esto requiere dos puertas de enlace de red virtual de la misma red virtual, uno con - GatewayType Vpn y la otra con - GatewayType ExpressRoute.


![Mantener conexión] (./media/vpn-gateway-about-vpngateways/demoer.png "expressroute site2site")


### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Modelos de implementación y métodos para S2S y ExpressRoute

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## <a name="next-steps"></a>Pasos siguientes

Planear la configuración de puerta de enlace VPN. Consulte [planificación de puerta de enlace VPN y diseño](vpn-gateway-plan-design.md) y [conectarse a su red local a Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).








 
