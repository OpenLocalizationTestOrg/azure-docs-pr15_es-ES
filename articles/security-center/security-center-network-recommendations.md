<properties
   pageTitle="Protección de su red en Centro de seguridad de Azure | Microsoft Azure"
   description="Las direcciones de este documento recomendaciones en Centro de seguridad de Azure que ayudan a protección su red de Azure y mantenerse cumple con las directivas de seguridad."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-network-in-azure-security-center"></a>Protección de su red en Centro de seguridad de Azure

Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Cuando el centro de seguridad se identifica posibles vulnerabilidad de seguridad, crea recomendaciones que le guiarán a lo largo del proceso de configuración de los controles necesarios.  Recomendaciones se aplican a los tipos de recursos de Azure: máquinas virtuales (VM), redes, aplicaciones y SQL.

Este artículo tratan las recomendaciones que se aplican a su red.  Centro de recomendaciones de red alrededor de los firewalls de generación siguientes, grupos de seguridad de red, configurar reglas de tráfico entrante y más.  Utilice la siguiente tabla como referencia para ayudarle a entender las recomendaciones de red disponibles y lo que cada uno de ellos hará si se aplica.

## <a name="available-network-recommendations"></a>Recomendaciones de red disponibles

|Recomendación|Descripción|
|-----|-----|
|[Agregue un Firewall de generación siguiente](security-center-add-next-generation-firewall.md)|Se recomienda agregar un Firewall de siguiente generación (NGFW) de un asociado de Microsoft para aumentar la protección de la seguridad.|
|[Enrutar el tráfico a través de NGFW solo](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Se recomienda configurar reglas de grupo (GSN) de seguridad de red que forzar el tráfico a su máquina virtual a través de su NGFW.|
|[Habilitar a grupos de seguridad de red en subredes o máquinas virtuales de Windows](security-center-enable-network-security-groups.md)|Se recomienda que habilite NSGs en subredes o máquinas virtuales.|
|[Restringir el acceso a través de Internet opuestas extremo](security-center-restrict-access-through-internet-facing-endpoints.md)|Se recomienda que configurar reglas de tráfico entrante para NSGs.|

## <a name="see-also"></a>Vea también

Para obtener más información acerca de las recomendaciones que se aplican a otros tipos de recursos de Azure, consulte lo siguiente:

- [Proteger los equipos virtuales en el centro de seguridad de Azure](security-center-virtual-machine-recommendations.md)
- [Proteger las aplicaciones en el centro de seguridad de Azure](security-center-application-recommendations.md)
- [Proteger el servicio SQL Azure en el centro de seguridad de Azure](security-center-sql-service-recommendations.md)

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
