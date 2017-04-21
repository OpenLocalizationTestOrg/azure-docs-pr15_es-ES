<properties
   pageTitle="Información general sobre BGP con puertas de enlace VPN Azure | Microsoft Azure"
   description="Este artículo proporciona una descripción general de BGP con puertas de enlace de Azure VPN."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/16/2016"
   ms.author="yushwang"/>

# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Información general sobre BGP con puertas de enlace VPN de Azure

Este artículo proporciona una descripción general de soporte técnico BGP (Protocolo de puerta de enlace de borde) en las puertas de enlace VPN de Azure.

## <a name="about-bgp"></a>Acerca de BGP

BGP es el protocolo de enrutamiento estándar que se usan normalmente en Internet para intercambiar información de enrutamiento y alcance entre dos o más redes. Cuando se utiliza en el contexto de las redes virtuales de Azure, BGP permite las puertas de enlace de VPN de Azure y los dispositivos VPN local llamados BGP colegas o vecinos intercambiar "enruta" que le informará de dos puertas de enlace en la disponibilidad y la accesibilidad de dichos prefijos ir a través de las puertas de enlace y enrutadores implicados. BGP también puede habilitar el enrutamiento de tránsito entre varias redes propagarse rutas que una puerta de enlace BGP aprende desde un punto BGP a todos los demás BGP.
 
### <a name="why-use-bgp"></a>¿Por qué usar BGP?

BGP es una característica opcional que puede usar con puertas de enlace VPN basado en la ruta de Azure. También debe asegurarse de que los dispositivos VPN local BGP antes de habilitar la característica de soporte técnico. Puede continuar usar las puertas de enlace VPN de Azure y los dispositivos VPN local sin BGP. Es equivalente a usar rutas estáticas (sin BGP) *frente a* enrutamiento dinámico con BGP entre sus redes y Azure.

Hay varias ventajas y nuevas capacidades con BGP:

#### <a name="support-automatic-and-flexible-prefix-updates"></a>Compatible con las actualizaciones automáticas y flexible prefijo

Con BGP, solo debe declarar un prefijo mínimo a un punto específico de BGP sobre el túnel IPsec S2S VPN. Puede ser tan pequeño como un prefijo de host (/ 32) de la dirección IP de BGP punto del dispositivo VPN local. Puede controlar que local desea anunciar a Azure para permitir que su red Virtual de Azure para obtener acceso a los prefijos de red.
    
También puede anunciar prefijos de más grande que pueden incluir algunos de los prefijos de direcciones VNet, por ejemplo, un espacio de direcciones IP privado (por ejemplo, 10.0.0.0/8) grande. Nota Aunque los prefijos no pueden ser idénticos con cualquiera de los prefijos VNet. Dichas rutas idénticos a su prefijos VNet serán rechazados.

>[AZURE.IMPORTANT] Actualmente, la ruta predeterminada (0.0.0.0/0) a puertas de enlace VPN de Azure de publicidad se bloqueará. Se proporcionará más actualización una vez que esta capacidad está habilitada.

#### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>Admite varios túneles entre un VNet y un sitio local con failover automático en función de BGP

Puede establecer varias conexiones entre su VNet de Azure y los dispositivos VPN local en la misma ubicación. Esta capacidad proporciona varios túneles (rutas de acceso) entre las dos redes en una configuración de activo / activo. Si uno de los túneles se desconecta, las rutas correspondientes se retirará a través de BGP y el tráfico se desplazará automáticamente a los túneles restantes.
    
El siguiente diagrama muestra un ejemplo sencillo de esta configuración altamente disponible:
    
![Varias rutas activos](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>Soporte técnico tránsito enrutamiento entre sus redes local y varios VNets de Azure

BGP permite varias puertas de enlace obtener y propagar prefijos de distintas redes, si están directa o indirectamente conectados. Esto puede habilitar el enrutamiento de tránsito con puertas de enlace de Azure VPN entre los sitios local o a través de varias redes de Virtual de Azure.
    
El siguiente diagrama muestra un ejemplo de una topología de múltiples saltos con varias rutas que puede tránsito tráfico entre las dos redes local a través de puertas de enlace VPN de Azure en Microsoft Networks:

![Tránsito de múltiples saltos](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faqs"></a>Preguntas más frecuentes BGP


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 




## <a name="next-steps"></a>Pasos siguientes

Consulte [Introducción a BGP en puertas de enlace VPN de Azure](./vpn-gateway-bgp-resource-manager-ps.md) para conocer los pasos para configurar BGP para sus conexiones de VNet a VNet y entre local.

