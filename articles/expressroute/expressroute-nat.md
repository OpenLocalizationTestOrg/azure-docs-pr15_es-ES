<properties
   pageTitle="Requisitos de NAT para circuitos ExpressRoute | Microsoft Azure"
   description="Esta página proporciona requisitos detallados para configurar y administrar NAT para circuitos ExpressRoute."
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

# <a name="expressroute-nat-requirements"></a>Requisitos de ExpressRoute NAT

Para conectarse a servicios de nube de Microsoft con ExpressRoute, debe configurar y administrar NAT. Algunos proveedores de conectividad ofrecen configurar y administrar NAT como un servicio administrado. Póngase en contacto con su proveedor de conectividad para ver si ofrecen dicho servicio. Si no es así, debe cumplir los requisitos que se describen a continuación. 

Revise la página [dominios enrutamiento y circuitos ExpressRoute](expressroute-circuit-peerings.md) para obtener una visión general de los distintos dominios de enrutamiento. Para satisfacer los requisitos de dirección IP públicos de Azure público e interconexión de Microsoft, se recomienda configurar NAT entre la red y Microsoft. Esta sección proporciona una descripción detallada de la infraestructura NAT que necesita configurar.

## <a name="nat-requirements-for-azure-public-peering"></a>Requisitos de NAT para interconexión pública de Azure

La ruta de interconexión pública Azure le permite conectarse a todos los servicios alojados en Azure sobre sus direcciones IP públicas. Estos incluyen servicios enumerados en las [Preguntas más frecuentes de ExpessRoute](expressroute-faqs.md) y cualquier hospedado por proveedores de software independientes de Microsoft Azure. Conectividad con servicios de Microsoft Azure en interconexión pública siempre se inicia desde su red en la red de Microsoft. El tráfico destinado a Microsoft Azure en interconexión público debe ser SNATed a las direcciones IPv4 públicas válidas antes de unirse a la red de Microsoft. La ilustración siguiente proporciona una imagen de alto nivel de cómo NAT podría estar configurado para cumplir los requisitos anteriores.

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>Anuncios de grupo y la ruta de IP de NAT

Debe asegurarse de que el tráfico está introduciendo la ruta de interconexión pública Azure con dirección IPv4 pública válida. Microsoft debe poder validar la propiedad del grupo de direcciones IPv4 NAT frente a un registro de Internet enrutamiento regional (RIR) o un registro de enrutamiento de Internet (TIR). Se realiza una comprobación basándose en el número de lo que se peered con y las direcciones IP que utiliza para NAT. Consulte la página de [requisitos de enrutamiento ExpressRoute](expressroute-routing.md) para obtener información sobre el enrutamiento de registros.
 
No hay ninguna restricción en la longitud del prefijo NAT IP anunciada a través de esta interconexión. Debe supervisar el grupo NAT y asegúrese de que no necesiten de sesiones NAT.

>[AZURE.IMPORTANT] No se debe anuncia el conjunto de IP de NAT anunciado a Microsoft a Internet. Esto interrumpir la conectividad con otros servicios de Microsoft.

## <a name="nat-requirements-for-microsoft-peering"></a>Requisitos de NAT para Microsoft interconexión

La ruta de acceso de interconexión de Microsoft le permite conectarse a servicios de nube de Microsoft que no se admiten a través de la ruta de interconexión pública de Azure. La lista de servicios incluye los servicios de Office 365, como Exchange Online, SharePoint Online, Skype para la empresa y CRM Online. Microsoft espera admitir una conectividad bidireccional en la interconexión de Microsoft. El tráfico destinado a los servicios de nube de Microsoft debe ser SNATed a las direcciones IPv4 públicas válidas antes de unirse a la red de Microsoft. El tráfico destinado a la red de servicios de nube de Microsoft debe SNATed antes de que entren en su red. La ilustración siguiente proporciona una imagen de alto nivel de cómo NAT debe ser el programa de instalación de Microsoft interconexión.
 
![](./media/expressroute-nat/expressroute-nat-microsoft.png) 


#### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Tráfico que provienen de su red destinada a Microsoft

- Debe asegurarse de que el tráfico es escribir la ruta de acceso de interconexión de Microsoft con una dirección IPv4 pública válida. Microsoft puedan validar el propietario del grupo de direcciones IPv4 NAT contra registro enrutamiento regional de internet (RIR) o un registro de enrutamiento de internet (TIR). Se realiza una comprobación basándose en el número de lo que se peered con y las direcciones IP que utiliza para NAT. Consulte la página de [requisitos de enrutamiento ExpressRoute](expressroute-routing.md) para obtener información sobre el enrutamiento de registros.

- Direcciones IP que usa para la configuración de interconexión pública de Azure y otros circuitos ExpressRoute no deben ser anuncia a Microsoft a través de la sesión BGP. No hay ninguna restricción en la longitud del prefijo NAT IP anunciada a través de esta interconexión.

    >[AZURE.IMPORTANT] No se debe anuncia el conjunto de IP de NAT anunciado a Microsoft a Internet. Esto interrumpir la conectividad con otros servicios de Microsoft.

#### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Tráfico que provienen de Microsoft destinado a su red

- Algunos escenarios requieren Microsoft iniciar la conectividad a los extremos de servicio hospedado dentro de su red. Un ejemplo típico del escenario sería conectividad a los servidores ADFS alojado en su red de Office 365. En estos casos, debe perderse prefijos adecuados de su red en la interconexión de Microsoft. 

- Debe tráfico SNAT destinado a las direcciones IP de su red de Microsoft. 

## <a name="next-steps"></a>Pasos siguientes

- Consulte los requisitos de [enrutamiento](expressroute-routing.md) y [QoS](expressroute-qos.md).
- Para obtener información de flujo de trabajo, consulte [ExpressRoute circuito aprovisionamiento circuito Estados y flujos de trabajo](expressroute-workflows.md).
- Configurar la conexión de ExpressRoute.

    - [Crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurar el enrutamiento](expressroute-howto-routing-classic.md)
    - [Vincular un VNet a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

