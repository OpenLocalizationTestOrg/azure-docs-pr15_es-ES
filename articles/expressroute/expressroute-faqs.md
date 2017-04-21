<properties
   pageTitle="ExpressRoute preguntas más frecuentes"
   description="Las preguntas más frecuentes de ExpressRoute contiene información sobre compatibles con servicios de Azure, costo, datos y conexiones, SLA, proveedores y ubicaciones, ancho de banda y detalles técnicos adicionales."
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

# <a name="expressroute-faq"></a>ExpressRoute preguntas más frecuentes


## <a name="what-is-expressroute"></a>¿Qué es ExpressRoute?
ExpressRoute es un servicio de Azure que le permite crear conexiones privadas entre los centros de datos de Microsoft y la infraestructura en sus instalaciones o en una instalación de ubicación cooperativa. Conexiones de ExpressRoute no vaya a través de Internet pública y ofrecer una mayor seguridad, confiabilidad y velocidades con latencias menores que típico conexiones a través de Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>¿Cuáles son las ventajas de usar ExpressRoute y conexiones de red privada?
Conexiones de ExpressRoute no vaya a través de Internet pública y ofrecer una mayor seguridad, confiabilidad y velocidades con latencia inferior y coherente que típico conexiones a través de Internet. En algunos casos, el uso de conexiones de ExpressRoute para transferir datos entre dispositivos y local Azure puede generar beneficios de costo significativo.

### <a name="what-microsoft-cloud-services-are-supported-over-expressroute"></a>¿A qué servicios de nube de Microsoft son compatibles con sobre ExpressRoute?
ExpressRoute es compatible con la mayoría de servicios de Microsoft Azure hoy incluidos Office 365.  Busque actualizaciones de disponibilidad general pronto.

### <a name="where-is-the-service-available"></a>¿Dónde está disponible el servicio?
Consulte esta página para disponibilidad y ubicación de servicio: [ubicaciones y socios de ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>¿Cómo puedo utilizar ExpressRoute para conectarse a Microsoft si no tengo asociaciones con uno de los socios carrier ExpressRoute?
Puede seleccionar un operador regional y land conexiones Ethernet a uno de exchange compatible ubicaciones de proveedor. A continuación, puede del mismo nivel con Microsoft en la ubicación del proveedor. Compruebe la última sección de [ubicaciones y socios de ExpressRoute](expressroute-locations.md) para ver si existe el proveedor de servicios en cualquiera de las ubicaciones de exchange. A continuación, puede solicitar un circuito ExpressRoute a través del proveedor de servicio para conectarse a Azure.

### <a name="how-much-does-expressroute-cost"></a>¿Cuánto cuesta ExpressRoute costo?
Comprobar los [Detalles de precios](https://azure.microsoft.com/pricing/details/expressroute/) para la información de precios.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>¿Si paga por un circuito ExpressRoute de un determinado ancho de banda, la conexión VPN que comprar de mi proveedor de servicios de red tiene por qué ser la misma velocidad?
No. Puede adquirir una conexión VPN de cualquier velocidad de su proveedor de servicios. Sin embargo, la conexión a Azure se limitará al ancho de banda de circuito ExpressRoute que compra.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-required"></a>Si paga por un circuito ExpressRoute de un determinado ancho de banda, ¿tienen la capacidad de ráfaga hasta velocidades superiores, si es necesario?
Sí. ExpressRoute circuitos están configurados para admitir casos donde puede ráfaga los dos veces el límite de ancho de banda adquirido para sin costo adicional. Póngase en contacto con su proveedor de servicios si son compatibles con esta función.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>¿Puedo usar la misma conexión de red privada Virtual y de red otros servicios de Azure simultáneamente?
Sí. Un circuito ExpressRoute, una vez el programa de instalación le permitirá obtener acceso a servicios dentro de una red virtual y otros servicios de Azure simultáneamente. Se conectará a redes virtuales sobre la ruta de acceso de interconexión privada y otros servicios en la ruta de interconexión pública.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>¿ExpressRoute ofrece un contrato de nivel de servicio (SLA)?
Consulte la [página de SLA ExpressRoute](https://azure.microsoft.com/support/legal/sla/) para obtener más información.

## <a name="supported-services"></a>Servicios compatibles
Compatible con los servicios de Azure más sobre ExpressRoute.

- Conectividad con máquinas virtuales y servicios de nube implementados en redes virtuales son compatibles en la ruta de interconexión privada.
- Sitios Web Azure es compatibles en la ruta de interconexión pública.
- Concentrador IoT es compatible en la ruta de interconexión pública.
- Office 365 es compatible en la ruta de interconexión de Microsoft.
- Todos los demás servicios sean accesibles a través de la ruta de acceso de interconexión público. Las excepciones son las siguientes.

    **No se admiten los siguientes servicios:**

    - CDN
    - Pruebas de carga de servicios de equipo de Visual Studio
    - Autenticación multifactor
    - Administrador de tráfico

## <a name="data-and-connections"></a>Datos y conexiones

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>¿Hay límites en la cantidad de datos que puedo transferir con ExpressRoute?
No se establece un límite en la cantidad de transferencia de datos. Consulte [Detalles de precios](https://azure.microsoft.com/pricing/details/expressroute/) para obtener información sobre tipos de ancho de banda.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>¿Qué velocidades de conexión son compatibles con ExpressRoute?
Admite ofertas de ancho de banda:

| 50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1Gbps, 2 GB/s, 5 GB/s, 10 Gbps |

### <a name="which-service-providers-are-available"></a>¿Los proveedores de servicios están disponibles?
Consulte [ubicaciones y socios de ExpressRoute](expressroute-locations.md) para la lista de proveedores de servicios y ubicaciones.

## <a name="technical-details"></a>Detalles técnicos

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>¿Cuáles son los requisitos técnicos para conectar mi ubicación locales con Azure?
Vea [página de requisitos previos de ExpressRoute](expressroute-prerequisites.md) para ver los requisitos.

### <a name="are-connections-to-expressroute-redundant"></a>¿Son las conexiones a ExpressRoute redundantes?
Sí. Cada circuito Express ruta tiene un par de redundante de cruzada conexiones configuradas para proporcionar alta disponibilidad.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>¿Pierda conectividad si uno de los vínculos de ExpressRoute un error?
No se pierden la conexión si se produce un error en una de las conexiones entre. Una conexión redundante está disponible para admitir la carga de su red. Además, puede crear varios circuitos en otra ubicación interconexión para lograr resistencia de error.

### <a name="onep2plink"></a>¿Si no estoy compartir ubicación en un cambio de la nube y mi proveedor de servicios ofrece una conexión punto a punto, debo pedir dos conexiones físicas entre mi red local y Microsoft? 
No, solo necesita una conexión física si su proveedor de servicios puede establecer dos circuitos virtuales de Ethernet a través de la conexión física. Se terminará la conexión física (por ejemplo, una fibra óptico) en una capa 1 (L1) dispositivo (consulte la imagen siguiente). Los dos circuitos virtuales de Ethernet etiquetados con VLAN ID, uno para el circuito principal y otro para el secundario. Los identificadores de VLAN se encuentran en el exterior 802.1Q encabezado Ethernet. La 802.1Q interna encabezado Ethernet (no se muestra) se asigna a un [dominio de enrutamiento ExpressRoute](expressroute-circuit-peerings.md)de específico. 

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)


### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>¿Ampliar uno de Mis VLAN a Azure usando ExpressRoute?
No. No se admiten extensiones de conectividad 2 de capa en Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>¿Tiene más de un circuito ExpressRoute en mi suscripción?
Sí. Puede tener más de un circuito de ExpressRoute en la suscripción. El límite predeterminado del número de circuitos dedicados se establece en 10. Puede ponerse en contacto con Microsoft Support para aumentar el límite si es necesario.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>¿Puedo tener circuitos ExpressRoute de proveedores de servicios diferente?
Sí. Puede tener ExpressRoute circuitos con muchos proveedores de servicios. Cada circuito ExpressRoute se asociará con el proveedor de uno servicios solo.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>¿Cómo conectar mi redes virtuales a un circuito ExpressRoute
Los pasos básicos se describen a continuación.

- Se debe establecer un circuito ExpressRoute y el proveedor de servicios habilitarlo.
- Usted o el proveedor debe configurar la interconexión BGP (s).
- Debe establecer el vínculo de la red virtual al circuito ExpressRoute.

Para obtener más información, vea [ExpressRoute flujos de trabajo para el aprovisionamiento de circuito y Estados de circuito](expressroute-workflows.md) .

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>¿Hay límites de conectividad de mi circuito ExpressRoute?
Sí. Página [ubicaciones y socios ExpressRoute](expressroute-locations.md) proporciona información general sobre los límites de conectividad para un circuito ExpressRoute. Conectividad para un circuito ExpressRoute se limita a una sola región geopolíticas. Conectividad se puede expandir para entre las regiones geopolíticas al habilitar la característica de premium ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>¿Puedo vincular a más de una red virtual para un circuito ExpressRoute?
Sí. Puede vincular hasta 10 redes virtuales a un circuito de ExpressRoute.

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Tengo varias suscripciones Azure que contienen las redes virtuales. ¿Puedo conectar redes virtuales que se encuentran en suscripciones independientes a un solo circuito ExpressRoute?
Sí. Puede autorizar hasta 10 otras suscripciones Azure usar un único circuito ExpressRoute. Al habilitar la característica de premium ExpressRoute se puede aumentar este límite.

Para obtener más detalles, vea [compartir un circuito ExpressRoute a través de varias suscripciones](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>¿Son redes virtuales conectadas al mismo circuito aislado entre sí?
No. Todas las redes virtuales vinculadas al mismo circuito ExpressRoute forman parte del mismo dominio enrutamiento y no están aisladas entre sí desde una perspectiva de enrutamiento. Si necesita aislamiento de ruta, debe crear un circuito ExpressRoute independiente.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>¿Puedo tener una red virtual conectada a más de un circuito de ExpressRoute?
Sí. Puede vincular una única red virtual con un máximo de 4 circuitos ExpressRoute. Debe pedir a través de 4 diferentes [ubicaciones ExpressRoute](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>¿Puedo obtener acceso a internet de mi virtuales redes conectadas a circuitos ExpressRoute?
Sí. Si no se anuncia rutas predeterminadas (0.0.0.0/0) o prefijos de ruta de internet a través de la sesión BGP, podrá conectarse a internet desde una red virtual vinculada a un circuito de ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>¿Puedo bloquear el conectividad a internet a virtuales redes conectadas a circuitos ExpressRoute?
Sí. Puede anunciar rutas predeterminado (0.0.0.0/0) para bloquear toda la conectividad de internet a máquinas virtuales implementado dentro de una red virtual y enrutar todo el tráfico a por el circuito ExpressRoute. Tenga en cuenta que si anunciar rutas predeterminadas, se forzar el tráfico a los servicios ofrecidos sobre público atrás interconexión (como almacenamiento de Azure y SQL DB) a su local. Tendrá que configurar los enrutadores para devolver el tráfico a Azure a través de la ruta de acceso de interconexión público o a través de internet.

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>¿Pueden redes virtuales vinculadas al mismo circuito ExpressRoute hablar entre sí?
Sí. Máquinas virtuales implementados en redes virtuales conectadas al mismo circuito ExpressRoute puede comunicarse con ellos.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>¿Usar conectividad a los sitios de redes virtuales junto con ExpressRoute?
Sí. ExpressRoute puede coexistencia con VPN de sitio a sitio.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>¿Puedo mover una red virtual de configuración de sitio a sitio / punto al sitio para usar ExpressRoute?
Sí. Tendrá que crear una puerta de enlace ExpressRoute dentro de la red virtual. Habrá un tiempo de inactividad pequeña asociado con el proceso.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>¿Qué es necesario para conectarse al almacenamiento de Azure través ExpressRoute?
Debe establecer un circuito ExpressRoute y configurar rutas de interconexión público.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>¿Hay límites en el número de rutas que puedo anunciar?
Sí. Se aceptan hasta 4000 prefijos de ruta para interconexión privada y 200 para interconexión pública e interconexión de Microsoft. Se puede aumentar a 10.000 rutas de interconexión privada si habilita la característica de premium ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>¿Hay restricciones en los intervalos de IP que puedo anunciar sobre la sesión BGP?
No se aceptan prefijos privados (RFC1918) en la sesión BGP interconexión pública y Microsoft.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>¿Qué ocurre si se excede el BGP limita?
Se quitarán las sesiones BGP. Se restablecerán una vez que el recuento de prefijo pasa por debajo del límite.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>¿Qué es el tiempo de espera de BGP ExpressRoute? ¿Puede se ajustará?
El tiempo de espera es 180. Los mensajes de mantenimiento se envían cada 60 segundos. Estos son fijos configuración en el lado de Microsoft que no se puede cambiar.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>Una vez que anunciar la ruta predeterminada (0.0.0.0/0) a las redes virtuales, no se puede activar Windows que se ejecuta en mi máquinas virtuales de Azure. ¿Cómo para solucionarlo?
Los siguientes pasos le ayudarán a Azure reconoce la solicitud de activación:

1. Establecer la interconexión pública para su circuito ExpressRoute.
2. Realizar una búsqueda DNS y busque la dirección IP del **kms.core.windows.net**
3. A continuación, siga uno de los dos elementos siguientes para que el servicio de administración de claves reconocerá que la solicitud de activación proviene de Azure y respeta la solicitud.
    - En su red local, enrutar el tráfico destinado a la dirección IP (obtenida en el paso 2) volver a Azure a través de la interconexión pública.
    - Tiene su pin de pelo proveedor NSP el tráfico a Azure a través de la interconexión pública.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>¿Puedo cambiar el ancho de banda de un circuito ExpressRoute?
Sí. Puede aumentar el ancho de banda de un circuito ExpressRoute sin necesidad de corte hacia abajo. Debe realizar un seguimiento con su proveedor de servicios de conectividad para asegurarse de que los usuarios actualicen los límites dentro de sus redes para admitir el aumento de ancho de banda. Sin embargo no podrá reducir el ancho de banda de un circuito ExpressRoute. Necesidad de inferior que el ancho de banda significará una anulación hacia abajo y vuelve a crear un circuito ExpressRoute.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>¿Cómo puedo cambiar el ancho de banda de un circuito ExpressRoute?
Puede actualizar el ancho de banda del circuito ExpressRoute mediante el cmdlet de PowerShell de API del circuito dedicado de actualización.

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>¿Qué es ExpressRoute premium?
ExpressRoute premium es un conjunto de características que se muestran a continuación.

 - Aumentar el enrutamiento límite de tabla de 4000 rutas a 10.000 rutas de interconexión privada.
 - Mayor número de VNets que se pueden conectar al circuito ExpressRoute (el valor predeterminado es 10). Consulte la tabla siguiente para obtener más detalles.
 - Conectividad global en la red principal de Microsoft. Ahora podrá vincular un VNet en una región geopolíticas con un circuito ExpressRoute en otra región. **Ejemplo:** Puede vincular un VNet creado en Europa occidental a un circuito ExpressRoute creado en Silicon Valley.
 - Conectividad con servicios de Office 365 y CRM Online.

### <a name="how-many-vnets-can-i-link-to-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a>¿Cuántos VNets ¿puedo vincular un circuito ExpressRoute si habilitado el ExpressRoute premium?
La tabla siguiente muestra los límites de ExpressRoute y el número de VNets por ExpressRoute circuito.


[AZURE.INCLUDE [expressroute-limits](../../includes/expressroute-limits.md)]


### <a name="how-do-i-enable-expressroute-premium"></a>¿Cómo habilitar el ExpressRoute premium?
Pueden habilitar las características premium ExpressRoute cuando la característica está habilitada y se puede cerrar actualizando el estado de circuito. Puede habilitar ExpressRoute premium en tiempo de creación de circuito o puede llamar a la API circuito de actualización dedicado / cmdlet de PowerShell para habilitar ExpressRoute premium.

### <a name="how-do-i-disable-expressroute-premium"></a>¿Cómo desactivo ExpressRoute premium?
Puede deshabilitar ExpressRoute premium llamando la actualización dedicada circuito API / cmdlet de PowerShell, debe asegurarse de que ha escalado la conectividad debe cumplir los límites predeterminados antes de deshabilitar ExpressRoute premium. Se producirá un error al deshabilitar ExpressRoute premium si ajusta la utilización más allá de los límites de forma predeterminada.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>¿Elegir las características que desee desde el conjunto de características premium?
No. No podrá elegir las características que necesita. Nos habilitar todas las características cuando se activan ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>¿Cuánto cuesta ExpressRoute costo de premium?
Consulte [Detalles de los precios](https://azure.microsoft.com/pricing/details/expressroute/) de costo.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>¿Pagar premium ExpressRoute además de los cargos de ExpressRoute estándares?
Sí. Cargos de premium ExpressRoute aplicarán sobre tarifas de circuito ExpressRoute y necesarios para el proveedor de servicios de conectividad.

## <a name="expressroute-and-office-365-services-and-crm-online"></a>ExpressRoute y servicios de Office 365 y CRM Online

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-crm-online"></a>¿Cómo creo un circuito ExpressRoute para conectarse a servicios de Office 365 y CRM Online?

1. Revise la página de la [página de los requisitos previos de ExpressRoute](expressroute-prerequisites.md) para asegurarse de que cumple los requisitos.
2. Revise la lista de proveedores de servicios y ubicaciones en [ubicaciones y socios ExpressRoute](expressroute-locations.md) para asegurarse de que se cumplen sus necesidades de conectividad.
3. Planear los requisitos de capacidad revisando [Planear la red y ajustar el rendimiento de Office 365](http://aka.ms/tune/).
4. Siga los pasos indicados en los flujos de trabajo para conectividad [ExpressRoute flujos de trabajo para el aprovisionamiento de circuito y circuito Estados](expressroute-workflows.md)de instalación.

>[AZURE.IMPORTANT] Asegúrese de que ha habilitado el complemento de premium ExpressRoute al configurar la conectividad con servicios de Office 365 y CRM Online.

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-crm-online"></a>¿Debo habilitar interconexión pública de Azure para conectarse a servicios de Office 365 y CRM Online?
No, solo debe habilitar Microsoft Peering. El tráfico de autenticación a Azure AD se enviarán a través de Microsoft Peering. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-crm-online"></a>¿Pueden mi circuitos ExpressRoute existentes admite la conectividad con servicios de Office 365 y CRM Online?
Sí. Su circuito ExpressRoute existente puede estar configurado para permitir la conectividad con servicios de Office 365. Asegurarse de que tiene suficiente capacidad para conectarse a servicios de Office 365 y asegúrese de que ha activado el complemento de premium. [Planear la red y ajustar el rendimiento de Office 365](http://aka.ms/tune/) le ayudará a planear las necesidades de conectividad. Consulte también [crear y modificar un circuito ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>¿Qué servicios se pueden acceder a través de una conexión de ExpressRoute de Office 365?

Consulte la página [URL de Office 365 y los intervalos de direcciones IP](http://aka.ms/o365endpoints) para una lista actualizada de servicios compatible a través de ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-and-crm-online-cost"></a>¿Cuánto cuesta de ExpressRoute para servicios de Office 365 y CRM Online costo?
Servicios de Office 365 y CRM Online requiere complemento premium estén habilitadas. La [página de detalles de precios](https://azure.microsoft.com/pricing/details/expressroute/) proporciona detalles de costos para ExpressRoute.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>¿Qué regiones ExpressRoute para Office 365 es compatible con?
Consulte [ubicaciones y socios de ExpressRoute](expressroute-locations.md) para más información sobre la lista de socios y ubicaciones donde se admite ExpressRoute.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>¿Puedo acceder a Office 365 a través de internet incluso si se ha configurado ExpressRoute para mi organización?
Sí. Extremos de Office 365 servicio son accesibles a través de internet, aunque ExpressRoute se ha configurado para su red. Si se encuentra en una ubicación que está configurada para conectarse a servicios de Office 365 a través de ExpressRoute, se conectará a través de ExpressRoute.

### <a name="can-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>¿Dynamics AX en línea se puede acceder a través de una conexión de ExpressRoute?
No, no es compatible.
