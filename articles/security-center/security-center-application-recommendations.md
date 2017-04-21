<properties
   pageTitle="Proteger las aplicaciones en el centro de seguridad de Azure | Microsoft Azure"
   description="Las direcciones de este documento recomendaciones en Centro de seguridad de Azure que ayudan a protegen sus aplicaciones de Azure y mantenerse cumple con las directivas de seguridad."
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

# <a name="protecting-your-applications-in-azure-security-center"></a>Proteger las aplicaciones en el centro de seguridad de Azure

Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Cuando el centro de seguridad se identifica posibles vulnerabilidad de seguridad, crea recomendaciones que le guiarán a lo largo del proceso de configuración de los controles necesarios.  Recomendaciones se aplican a los tipos de recursos de Azure: máquinas virtuales (VM), redes, aplicaciones y SQL.

Este artículo tratan las recomendaciones que se aplican a las aplicaciones.  Centro de recomendaciones de aplicación alrededor de implementación de un servidor de aplicaciones web.  Utilice la siguiente tabla como referencia para ayudarle a entender las recomendaciones de aplicaciones disponibles y lo que cada uno de ellos hará si se aplica.

## <a name="available-application-recommendations"></a>Recomendaciones de aplicaciones disponibles

|Recomendación|Descripción|
|-----|-----|
|[Agregar un servidor de aplicaciones web](security-center-add-web-application-firewall.md)|Se recomienda implementar un servidor de aplicaciones web (WAFS) para los extremos de la web. Puede proteger varias aplicaciones web en el centro de seguridad agregando estas aplicaciones para las implementaciones WAFS existentes. WAFS (creados con el modelo de implementación de administrador de recursos) tendrán que implementará en una red virtual independiente. Dispositivos WAFS (creados con el modelo de implementación clásico) están limitados a usar un grupo de seguridad de la red. Esta compatibilidad se extiende a una implementación completamente personalizada de un dispositivo WAFS (clásico) en el futuro.|
|[Finalizar la protección de la aplicación](security-center-add-web-application-firewall.md#finalize-application-protection)|Para completar la configuración de un WAFS, el tráfico debe se transfiere al dispositivo WAFS. Seguir esta recomendación completarán los cambios necesarios de instalación.|

## <a name="see-also"></a>Vea también

Para obtener más información acerca de las recomendaciones que se aplican a otros tipos de recursos de Azure, consulte lo siguiente:

- [Proteger los equipos virtuales en el centro de seguridad de Azure](security-center-virtual-machine-recommendations.md)
- [Protección de su red en Centro de seguridad de Azure](security-center-network-recommendations.md)
- [Proteger el servicio SQL Azure en el centro de seguridad de Azure](security-center-sql-service-recommendations.md)

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
