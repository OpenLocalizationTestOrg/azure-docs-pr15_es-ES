<properties
   pageTitle="Proteger el servicio de SQL Azure en el centro de seguridad de Azure | Microsoft Azure"
   description="Las direcciones de este documento recomendaciones en Centro de seguridad de Azure que ayudan a protección el servicio SQL Azure y mantenerse cumple con las directivas de seguridad."
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

# <a name="protecting-azure-sql-service-in-azure-security-center"></a>Proteger el servicio de SQL Azure en el centro de seguridad de Azure

Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Cuando el centro de seguridad se identifica posibles vulnerabilidad de seguridad, crea recomendaciones que le guiarán a lo largo del proceso de configuración de los controles necesarios.  Recomendaciones se aplican a los tipos de recursos de Azure: máquinas virtuales (VM), redes, aplicaciones y SQL.

Este artículo tratan las recomendaciones que se aplican al servicio de SQL Azure.  Centro de recomendaciones de servicios de SQL Azure alrededor de habilitar la auditoría en los servidores de SQL Azure y bases de datos y habilitar el cifrado de bases de datos SQL.  Utilice la siguiente tabla como referencia para ayudarle a entender las recomendaciones de servicio SQL disponibles y lo que cada uno de ellos hará si se aplica.

## <a name="available-sql-service-recommendations"></a>Recomendaciones de servicio SQL disponibles

|Recomendación|Descripción|
|-----|-----|
|[Habilitar el servidor de auditoría de SQL](security-center-enable-auditing-on-sql-servers.md)|Se recomienda activar la auditoría en los servidores de SQL Azure (servicio SQL Azure; no incluye ejecutando en sus máquinas virtuales SQL).|
|[Habilitar la auditoría de SQL de la base de datos](security-center-enable-auditing-on-sql-databases.md)|Se recomienda activar la auditoría en bases de datos de SQL Azure (servicio SQL Azure; no incluye ejecutando en sus máquinas virtuales SQL).|
|[Habilitar el cifrado de datos transparente en bases de datos SQL](security-center-enable-transparent-data-encryption.md)|Se recomienda habilitar el cifrado de bases de datos SQL (solo en el servicio SQL Azure).|

## <a name="see-also"></a>Vea también

Para obtener más información acerca de las recomendaciones que se aplican a otros tipos de recursos de Azure, consulte lo siguiente:

- [Proteger los equipos virtuales en el centro de seguridad de Azure](security-center-virtual-machine-recommendations.md)
- [Proteger las aplicaciones en el centro de seguridad de Azure](security-center-application-recommendations.md)
- [Protección de su red en Centro de seguridad de Azure](security-center-network-recommendations.md)

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
