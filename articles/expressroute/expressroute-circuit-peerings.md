<properties 
   pageTitle="ExpressRoute circuitos y dominios de enrutamiento | Microsoft Azure"
   description="Esta página proporciona una descripción general de ExpressRoute circuitos y los dominios de enrutamiento."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-circuits-and-routing-domains"></a>ExpressRoute circuitos y dominios de enrutamiento

 Debe pedir un *circuito ExpressRoute* para conectar su infraestructura local a Microsoft a través de un proveedor de servicios de conectividad. La ilustración siguiente proporciona una representación lógica de conectividad entre su WAN y Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>Circuitos ExpressRoute

Un *circuito ExpressRoute* representa una conexión lógica entre su infraestructura local y servicios de nube de Microsoft a través de un proveedor de servicios de conectividad. Puede solicitar varios circuitos ExpressRoute. Cada circuito puede estar en el mismos o en diferentes regiones y se pueden conectar a su local a través de proveedores de conectividad distinto. 

Circuitos ExpressRoute no se asignan a entidades físicas. Un circuito se identifica mediante un estándar denominado GUID como una clave de servicio (s-clave). La clave del servicio es la única de intercambiar entre Microsoft, el proveedor de servicios de conectividad y de información. La tecla s no es un secreto por motivos de seguridad. Hay una asignación de 1:1 entre un circuito ExpressRoute y la tecla s.

Un circuito ExpressRoute puede tener hasta tres peerings independientes: Azure privado público, Azure y Microsoft. Cada interconexión es un par de BGP independiente sesiones de ellos configurados de forma redundante para alta disponibilidad. Hay un 1: n (1 < = N < = 3) asignación entre un circuito ExpressRoute y el enrutamiento de dominios. Un circuito ExpressRoute puede tener una, dos o todas las tres peerings habilitados por circuito ExpressRoute.
 
Cada circuito tiene un ancho de banda fijo (50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 GB/s, 10 GB/s) y se asigna a un proveedor de servicios de conectividad y una ubicación de interconexión. Selecciona el ancho de banda pueden compartirse entre todos los peerings para el circuito. 

### <a name="quotas-limits-and-limitations"></a>Las cuotas, los límites y limitaciones

Cuotas predeterminadas y los límites se aplican para cada circuito ExpressRoute. Consulte la página de [suscripción de Azure y los límites del servicio, cuotas y restricciones](../azure-subscription-service-limits.md) para información actualizada sobre las cuotas.

## <a name="expressroute-routing-domains"></a>Dominios de enrutamiento ExpressRoute

Un circuito ExpressRoute tiene varios dominios de enrutamiento asociadas: Azure privado público, Azure y Microsoft. Cada uno de los dominios de enrutamiento configuración es idéntica en un par de enrutadores (en activo activo o uso compartido de carga configuración) para alta disponibilidad. Servicios de Azure se clasifican como *pública de Azure* y *Azure privado* para representar lo esquemas de direcciones IP.


![](./media/expressroute-circuit-peerings/expressroute-peerings.png)


### <a name="private-peering"></a>Interconexión privada

Servicios, es decir, máquinas virtuales de Windows (IaaS) de proceso de Azure y servicios de nube (PaaS), que se implementan dentro de una red virtual se pueden conectar a través del dominio interconexión privado. El dominio interconexión privado se considera una extensión de confianza de la red principal en Microsoft Azure. Puede configurar una conectividad bidireccional entre su red principal y redes virtuales Azure (VNets). Esta interconexión le permite conectarse a máquinas virtuales y servicios directamente en sus direcciones IP privados en la nube.  

Puede conectar más de una red virtual al dominio interconexión privado. Revise la [página de preguntas más frecuentes](expressroute-faqs.md) para obtener información sobre los límites y limitaciones. Puede visitar la página de [suscripción de Azure y los límites del servicio, cuotas y restricciones](../azure-subscription-service-limits.md) para obtener información actualizada en límites.  Consulte la página de [enrutamiento](expressroute-routing.md) para obtener información detallada sobre la configuración de enrutamiento.

### <a name="public-peering"></a>Interconexión pública

Servicios de almacenamiento de Azure, bases de datos SQL y sitios Web se ofrecen en las direcciones IP públicas. En privado, puede conectarse a servicios de direcciones IP públicas, incluidos a VIP de los servicios de nube a través del dominio de enrutamiento interconexión público. Puede conectar el dominio público interconexión a su DMZ y conectarse a todos los servicios de Azure en sus direcciones IP públicas desde su WAN sin tener que conectarse a través de internet. 

Conectividad siempre se inicia desde su WAN en servicios de Microsoft Azure. Servicios de Microsoft Azure no podrá iniciar conexiones en la red a través de este dominio enrutamiento. Una vez habilitada la interconexión pública, podrá conectar a todos los servicios de Azure. No permitir que elegir selectivamente que nos anunciar rutas a los servicios. Puede revisar la lista de prefijos que se anunciar a usted a través de esta interconexión en la página de [Intervalos de IP de centro de datos de Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653) . La página se actualiza semanalmente.

Puede definir los filtros de ruta personalizada dentro de su red para consumir sólo las rutas que necesita. Consulte la página de [enrutamiento](expressroute-routing.md) para obtener información detallada sobre la configuración de enrutamiento. Puede definir los filtros de ruta personalizada dentro de su red para consumir sólo las rutas que necesita. 

Vea la [página de preguntas más frecuentes](expressroute-faqs.md) para obtener más información sobre servicios compatibles a través del dominio de enrutamiento interconexión público. 
 
### <a name="microsoft-peering"></a>Interconexión de Microsoft

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Conectividad con todos los otros servicios en línea de Microsoft (por ejemplo, servicios de Office 365) estarán a través de la interconexión de Microsoft. Habilitar la conectividad bidireccional entre los servicios de nube WAN y Microsoft a través del dominio de enrutamiento interconexión de Microsoft. Debe conectarse a servicios de nube de Microsoft sólo a través de las direcciones IP públicas que pertenecen a usted o su proveedor de conectividad y debe cumplir todas las reglas definidas. Consulte la página de [requisitos previos de ExpressRoute](expressroute-prerequisites.md) para obtener más información.

Consulte la [página de preguntas más frecuentes](expressroute-faqs.md) para obtener más información sobre servicios compatibles, costos y detalles de configuración. Consulte la página [Ubicaciones de ExpressRoute](expressroute-locations.md) para obtener más información en la lista de proveedores de conectividad que ofrece soporte técnico de Microsoft interconexión.

## <a name="routing-domain-comparison"></a>Comparación de dominio de enrutamiento

La siguiente tabla compara los tres dominios de enrutamiento.

||**Interconexión privada**|**Interconexión pública**|**Interconexión de Microsoft**|
|---|---|---|---|
|**Max. prefijos # admitidos por interconexión**|4000 de forma predeterminada, 10.000 con ExpressRoute Premium|200|200|
|**Intervalos de direcciones IP compatibles**|Cualquier dirección IPv4 válida dentro de su WAN.|Direcciones IPv4 públicas su propiedad o su proveedor de servicios de conectividad.|Direcciones IPv4 públicas su propiedad o su proveedor de servicios de conectividad.|
|**COMO los requisitos de número**|Públicas y privadas como números. Debe ser propietario del público como número si decide usar uno. | Públicas y privadas como números. Sin embargo, debe comprobar la propiedad de direcciones IP públicas.| Públicas y privadas como números. Sin embargo, debe comprobar la propiedad de direcciones IP públicas.|
|**Direcciones IP de la interfaz de enrutamiento**|Direcciones IP pública y RFC1918|Direcciones IP públicas registradas para usted en enrutamiento de registros.| Direcciones IP públicas registradas para usted en enrutamiento de registros.|
|**Soporte técnico de Hash MD5**| Sí|Sí|Sí|

Puede elegir habilitar uno o varios de los dominios de enrutamiento como parte de su circuito ExpressRoute. Puede elegir que todos los dominios de enrutamiento colocar en la misma VPN si desea combinarlos en un único dominio enrutamiento. También puede colocarlos en distintos dominios de enrutamiento, similares al diagrama. La configuración recomendada es que interconexión privada está conectado directamente a la red principal y el público y vínculos de interconexión de Microsoft están conectados a su DMZ.
 
Si opta por que todas las sesiones de interconexión tres, debe tener tres pares de sesiones BGP (un par de cada tipo de interconexión). Los pares de sesión BGP proporcionan un vínculo altamente disponible. Si se está conectando a través de proveedores de nivel 2 conectividad, será responsable de configurar y administrar el enrutamiento. Puede obtener más revisando los [flujos de trabajo](expressroute-workflows.md) para configurar ExpressRoute.

## <a name="next-steps"></a>Pasos siguientes

- Buscar un proveedor de servicios. Consulte [proveedores de servicios de ExpressRoute y ubicaciones](expressroute-locations.md).
- Asegúrese de que se cumplen todos los requisitos previos. Vea [requisitos previos de ExpressRoute](expressroute-prerequisites.md).
- Configurar la conexión de ExpressRoute.
    - [Crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurar el enrutamiento (circuito peerings)](expressroute-howto-routing-classic.md)
    - [Vincular un VNet a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)
