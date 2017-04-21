<properties
   pageTitle="Habilitar la auditoría en bases de datos SQL en Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar la recomendación del centro de seguridad de Azure **Habilitar la auditoría en bases de datos SQL**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-auditing-on-sql-databases-in-azure-security-center"></a>Habilitar la auditoría en bases de datos SQL en Centro de seguridad de Azure

Centro de seguridad de Azure se recomienda activar la auditoría en todas las bases de datos SQL si la auditoría no está habilitada. Auditoría puede ayudarle a mantener el cumplimiento de reglamentaciones, comprender la actividad de la base de datos y obtenga información sobre discrepancias y anomalías que podrían indicar cuestiones empresariales o sospechosos violaciones de seguridad.

Una vez que se ha activado la auditoría puede configurar opciones de detección de amenaza y mensajes de correo electrónico para recibir alertas de seguridad. Detección de amenazas detecta actividades irregulares de la base de datos que indica los posibles amenazas de seguridad de la base de datos. Esto le permite detectar y responder a las posibles amenazas según se van produciendo.

Esta recomendación se aplica al servicio de SQL Azure. no incluir ejecutando en sus máquinas virtuales SQL.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.  No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el módulo de **recomendaciones** , seleccione **Habilitar auditoría en bases de datos SQL**.  Se abrirá el módulo **Habilitar auditoría en bases de datos SQL** .
![Habilitar la auditoría en bases de datos SQL][1]

2. Seleccione una base de datos SQL para habilitar la auditoría en. Se abrirá el módulo de **detección de auditoría y amenazas** .
![Detección de auditoría y amenazas][2]
3. En el módulo de **detección de auditoría y amenazas** , seleccione **activado** en **auditoría**.
![Activar la detección de auditoría y amenazas][3]


5. Siga los pasos en [Introducción a la detección de amenaza de base de datos de SQL](../sql-database/sql-database-threat-detection-get-started.md) para activar y configurar detección de amenazas y configurar la lista de mensajes de correo electrónico que va a recibir alertas de seguridad tras la detección de actividades irregulares.

## <a name="see-also"></a>Vea también

En este artículo le mostramos cómo implementar la recomendación del centro de seguridad "Habilitar la auditoría en bases de datos SQL". Para obtener más información sobre cómo proteger su base de datos SQL, consulte lo siguiente:

- [Proteger la base de datos SQL](../sql-database/sql-database-security.md)

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Recomendaciones de seguridad de la administración de centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión del estado de seguridad del centro de seguridad de Azure](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Las soluciones de asociados de supervisión con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/) : obtener las últimas noticias de seguridad de Azure y la información.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]:./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection.png
[3]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
