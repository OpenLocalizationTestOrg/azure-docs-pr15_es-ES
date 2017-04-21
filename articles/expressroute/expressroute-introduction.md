<properties 
   pageTitle="Introducción a ExpressRoute | Microsoft Azure"
   description="Esta página proporciona una descripción general del servicio ExpressRoute, incluido cómo funciona una conexión ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-technical-overview"></a>Información general técnica de ExpressRoute

Microsoft Azure ExpressRoute le permite ampliar sus redes local en la nube de Microsoft sobre una conexión privada dedicada facilitada mediante un proveedor de servicios de conectividad. Con ExpressRoute, puede establecer conexiones a servicios de nube de Microsoft, como Microsoft Azure, Office 365 y CRM Online. Conectividad puede ser de una red (IP VPN) y a cualquier, una red de punto a punto Ethernet o una conexión entre virtual a través de un proveedor de conectividad en una instalación de la ubicación. Conexiones de ExpressRoute no vaya a través de Internet pública. Esto permite conexiones ExpressRoute ofrecer más confiabilidad, velocidades, latencia menor y mayor seguridad que típico conexiones a través de Internet.

![](./media/expressroute-introduction/expressroute-basic.png)

**Beneficios clave incluyen:**

- Capa 3 conectividad entre la Cloud Microsoft a través de un proveedor de servicios de conectividad y de su red local. Conectividad puede ser desde una red (IPVPN) y a cualquier, una conexión Ethernet de punto a punto, o a través de una conexión entre virtual a través de un intercambio de Ethernet.
- Conectividad con servicios de nube de Microsoft a través de todas las áreas de la región geopolíticas.
- Conectividad global con servicios de Microsoft a través de todas las regiones con el complemento de ExpressRoute premium.
- Enrutamiento entre la red y Microsoft sobre protocolos estándar (BGP) dinámico.
- Redundancia integrada en todas las ubicaciones interconexión para una mayor confiabilidad.
- Conexión [SLA](https://azure.microsoft.com/support/legal/sla/)de tiempo activo.
- QoS y compatibilidad de clases múltiples de servicio para aplicaciones especiales, como Skype para la empresa.

Consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md) para obtener más detalles.

## <a name="howtoconnect"></a>¿Cómo puedo conectar mi red a Microsoft mediante ExpressRoute?

Puede crear una conexión entre su red local y la nube de Microsoft de tres maneras distintas:

### <a name="co-located-at-a-cloud-exchange"></a>Ubicado en un cambio de la nube

Si se encuentra conjunta en una instalación con un cambio de la nube, puede solicitar virtuales conexiones cruzadas en la nube de Microsoft a través de exchange de Ethernet del proveedor de la ubicación. Proveedores de ubicación compañeros pueden ofrecer conexiones cruzadas de nivel 2 o administrado Layer 3 conexiones cruzadas entre su infraestructura de la función de la ubicación y la nube de Microsoft.

### <a name="point-to-point-ethernet-connections"></a>Conexiones de punto a punto Ethernet 

Puede conectar los centros de datos locales/oficinas en la nube de Microsoft a través de vínculos de Ethernet de punto a punto. Proveedores de Ethernet de punto a punto pueden ofrecer conexiones de nivel 2 o administrado Layer 3 conexiones entre el sitio y la nube de Microsoft.

### <a name="any-to-any-ipvpn-networks"></a>Cualquier redes (IPVPN)

Puede integrar su WAN con la nube de Microsoft. Proveedores de IPVPN (normalmente MPLS VPN) ofrecen-cualquier conectividad entre sus sucursales y centros de datos. La nube de Microsoft puede estar interconectada a su WAN para darle el aspecto de cualquier sucursal. Proveedores de WAN normalmente ofrecen conectividad de nivel 3 administrado. Características y funcionalidades de ExpressRoute son todo idénticas en todos los modelos de conectividad anteriores. 

Proveedores de conectividad pueden ofrecer uno o varios de los modelos de conectividad. Puede trabajar con su proveedor de conectividad para elegir el modelo que mejor le convenga.

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)



## <a name="expressroute-features"></a>Características de ExpressRoute

ExpressRoute es compatible con las características y funcionalidades siguientes: 

### <a name="layer-3-connectivity"></a>Conectividad de nivel 3

Microsoft usa dinámico enrutamiento protocolo estándar (BGP) para intercambiar la ruta entre su red local, las instancias de Azure y Microsoft direcciones públicas.  Nos establecer varias sesiones BGP con su red para perfiles de tráfico diferentes. Encontrará más detalles en el artículo [ExpressRoute circuito y el enrutamiento de dominios](expressroute-circuit-peerings.md) .

### <a name="redundancy"></a>Redundancia

Cada circuito ExpressRoute consta de dos conexiones a dos enrutador de borde de Microsoft Enterprise (MSEEs) desde el proveedor de servicios de conectividad o el borde de la red. Microsoft necesitarán dual conexión BGP desde el proveedor de servicios de conectividad y la cara: uno para cada MSEE. Puede elegir no implementar dispositivos redundantes / Ethernet circuitos y el final. Sin embargo, los proveedores de conectividad usar dispositivos redundantes para asegurarse de que las conexiones se entregan a Microsoft de forma redundante. Una configuración de conectividad redundante Layer 3 es necesario para nuestros [SLA](https://azure.microsoft.com/support/legal/sla/) sea válido. 

### <a name="connectivity-to-microsoft-cloud-services"></a>Conectividad con servicios de nube de Microsoft

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Conexiones de ExpressRoute habilitar el acceso a los siguientes servicios:

- Servicios de Microsoft Azure
- Servicios de Microsoft Office 365
- Servicios de Microsoft CRM Online 
 
Puede visitar la página de [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md) para obtener una lista de servicios admitidos por ExpressRoute.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Conectividad con todas las regiones dentro de una región geopolíticas

Puede conectarse a Microsoft en una de nuestras [ubicaciones interconexión](expressroute-locations.md) y tener acceso a todas las áreas dentro de la región geopolíticas. 

Por ejemplo, si está conectado a Microsoft en Amsterdam mediante ExpressRoute, tendrá acceso a todos los servicios de nube de Microsoft alojado en Europa del Norte y Europa occidental. Consulte el artículo [ExpressRoute asociados e interconexión ubicaciones](expressroute-locations.md) para obtener información general del regiones geopolíticas, las áreas de la nube de Microsoft asociadas y ubicaciones de interconexión ExpressRoute correspondientes.

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Conectividad global con el complemento de ExpressRoute premium

Puede habilitar la característica de complementos de prima ExpressRoute extender la conectividad a través de límites geopolíticos. Por ejemplo, si está conectado a Microsoft en Amsterdam mediante ExpressRoute, tendrá acceso a todos los servicios de nube de Microsoft alojados en todas las regiones en todo el mundo (se excluyen nubes nacionales). Puede obtener acceso a servicios de implementada en América del sur o Australia del mismo modo que se tiene acceso a las regiones de Europa occidental y Norte.

### <a name="rich-connectivity-partner-ecosystem"></a>Ecosistema de socios de conectividad enriquecido

ExpressRoute tiene un ecosistema creciente de proveedores de conectividad y socios de Hueco. Puede consultar el artículo de [ubicaciones y proveedores de ExpressRoute](expressroute-locations.md) para la información más reciente.

### <a name="connectivity-to-national-clouds"></a>Conectividad con nubes nacionales

Microsoft administra entornos de nube aislado para regiones geopolíticas especiales y segmentos de clientes. Consulte la página [ubicaciones y proveedores de ExpressRoute](expressroute-locations.md) para obtener una lista de nubes nacionales y proveedores.

### <a name="supported-bandwidth-options"></a>Opciones de ancho de banda compatibles

Puede adquirir circuitos ExpressRoute para una amplia gama de anchos de banda. A continuación se muestra la lista de anchos de banda compatibles. No olvide póngase en contacto con su proveedor de conectividad para determinar la lista de anchos de banda compatibles proporcionan.

- 50 Mbps
- 100 Mbps
- 200 Mbps
- 500 Mbps
- 1 GB/s
- 2 GB/s
- 5 GB/s
- 10 GB/s

### <a name="dynamic-scaling-of-bandwidth"></a>Ajuste de escala dinámicos de ancho de banda

Tienen la capacidad para aumentar el ancho de banda de circuito ExpressRoute (según el mejor esfuerzo) sin tener que romper las conexiones. 

### <a name="flexible-billing-models"></a>Modelos de facturación flexibles

Puede seleccionar un modelo de facturación que mejor le convenga. Elija entre los modelos de facturación que se muestran a continuación. Consulte la página de [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md) para obtener más detalles. 

- **Datos ilimitados**. Circuito de ExpressRoute se cargará en función de una cuota mensual y la transferencia de todos los datos entrantes y salientes es gratuito. 
- **Datos de uso medido**. Circuito de ExpressRoute se cargará en función de una cuota mensual. Transferencia de todos los datos entrantes es gratuito. Transferencia de datos de salida se cargará por GB de transferencia de datos. Tasas de transferencia de datos varían según la región.
- **Complemento de ExpressRoute premium**. La prima ExpressRoute es un complemento sobre el circuito ExpressRoute. El complemento de premium ExpressRoute proporciona las siguientes capacidades: 
    - Límites de ruta mayor de Azure públicas y Azure privada interconexión de 4.000 redirige a 10.000 rutas.
    - Conectividad global para los servicios. Un circuito ExpressRoute creado en cualquier región (excepto nubes nacionales) tendrá acceso a los recursos a través de cualquier otra región en el mundo. Por ejemplo, una red virtual creada en Europa occidental se puede acceder a través de un circuito ExpressRoute aprovisionado en Silicon Valley.
    - Aumento del número de vínculos de VNet por ExpressRoute circuito de 10 a un límite mayor, según el ancho de banda del circuito.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las conexiones de ExpressRoute y dominios de enrutamiento. Consulte [ExpressRoute circuitos y dominios de enrutamiento](expressroute-circuit-peerings.md).
- Buscar un proveedor de servicios. Consulte [ExpressRoute asociados e interconexión ubicaciones](expressroute-locations.md).
- Asegúrese de que se cumplen todos los requisitos previos. Vea [requisitos previos de ExpressRoute](expressroute-prerequisites.md).
- Consulte los requisitos de [enrutamiento](expressroute-routing.md), [NAT](expressroute-nat.md) y [QoS](expressroute-qos.md).
- Configurar la conexión de ExpressRoute.
    - [Crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurar el enrutamiento](expressroute-howto-routing-classic.md)
    - [Vincular un VNet a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)
