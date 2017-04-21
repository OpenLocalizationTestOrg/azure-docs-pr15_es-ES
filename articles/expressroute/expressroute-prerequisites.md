<properties
   pageTitle="Requisitos previos para la adopción de ExpressRoute | Microsoft Azure"
   description="Esta página proporciona una lista de condiciones que deben cumplir antes de que puede solicitar un circuito de Azure ExpressRoute."
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


# <a name="expressroute-prerequisites--checklist"></a>Lista de comprobación & ExpressRoute los requisitos previos  

Para conectarse a servicios de nube de Microsoft con ExpressRoute, deberá comprobar que se cumplen los requisitos siguientes enumerados en las secciones siguientes.

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Cuenta de Azure

- Una cuenta de Microsoft Azure válida y activa. Esto es necesario configurar el circuito ExpressRoute. ExpressRoute circuitos son recursos dentro de Azure suscripciones. Una suscripción de Azure es necesario incluso si conectividad se limita a los servicios de nube de Microsoft que no sean de Azure, como servicios de Office 365 y CRM online.
- Una suscripción de Office 365 activa (si está usando Servicios de Office 365). Vea la sección [requisitos específicos de Office 365](#office-365-specific-requirements) de este artículo para obtener más información.

## <a name="connectivity-provider"></a>Proveedor de servicios de conectividad
- Puede trabajar con un [partner de conectividad ExpressRoute](expressroute-locations.md#partners) para conectarse a la nube de Microsoft. Puede configurar una conexión entre su red local y Microsoft de [tres maneras](expressroute-introduction.md#howtoconnect). 
- Si su proveedor no es un asociado de conectividad ExpressRoute, aún puede conectarse a la nube de Microsoft a través de un [proveedor de servicios de nube exchange](expressroute-locations.md#nonpartners).

## <a name="network-requirements"></a>Requisitos de red
- **Conectividad redundante**: no es necesario redundancia de conectividad física entre usted y su proveedor. Microsoft requieren redundantes BGP sesiones que configurar entre enrutadores de Microsoft y los enrutadores interconexión, incluso cuando tiene solo [una conexión física a un cambio de la nube](expressroute-faqs.md#onep2plink). 
- **Enrutamiento**: dependiendo de cómo se conecta a la Cloud Microsoft, usted o su proveedor necesita configurar y administrar las sesiones BGP para [dominios de enrutamiento](expressroute-circuit-peerings.md). Algunos proveedor de servicios de conectividad de Ethernet o el proveedor de servicios de nube exchange puede ofrecer administración BGP como un servicio de valor agregado.
- **NAT**: Microsoft sólo acepta direcciones IP públicas a través de Microsoft interconexión. Si está utilizando direcciones IP de su red local, usted o sus necesidades de proveedor para traducir las direcciones IP privadas a la dirección IP pública direcciones [usando el NAT](expressroute-nat.md).
- **QoS**: Skype para Business tiene varios servicios (por ejemplo, voz y vídeo, texto) que requieren diferenciados tratamiento de QoS. Usted y su proveedor deben seguir los [requisitos de QoS](expressroute-qos.md).
- **Seguridad de red**: debe considerar la [seguridad de red](../best-practices-network-security.md) al conectarse a la Cloud Microsoft a través de ExpressRoute.
 
## <a name="office-365"></a>Office 365

Si va a habilitar Office 365 en ExpressRoute, revise los siguientes documentos para obtener más información sobre los requisitos de Office 365.


- [Información general sobre ExpressRoute para Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Enrutamiento de ExpressRoute para Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [Intervalos de direcciones IP y URL de Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Planear la red y ajustar el rendimiento de Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Herramientas y calculadoras de ancho de banda de red](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Integración de Office 365 con entornos locales](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## <a name="crm-online"></a>CRM Online 
Si va a habilitar CRM Online en ExpressRoute, revise los siguientes documentos para obtener más información acerca de CRM Online

- [Direcciones URL de CRM Online](https://support.microsoft.com/kb/2655102) y los [intervalos de direcciones IP](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre ExpressRoute, consulte las [Preguntas más frecuentes de ExpressRoute](expressroute-faqs.md).
- Buscar un proveedor de servicios de conectividad de ExpressRoute. Consulte [ExpressRoute asociados e interconexión ubicaciones](expressroute-locations.md).
- Consulte requisitos de [enrutamiento](expressroute-routing.md), [NAT](expressroute-nat.md) y [QoS](expressroute-qos.md).
- Configurar la conexión de ExpressRoute.
    - [Crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurar el enrutamiento](expressroute-howto-routing-classic.md)
    - [Vincular un VNet a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

