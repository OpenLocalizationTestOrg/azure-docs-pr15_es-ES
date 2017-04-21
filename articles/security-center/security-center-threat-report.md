<properties
   pageTitle="Informe de inteligencia de amenaza de centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a usar informes de amenaza de centro de seguridad de Azure inteligente durante una investigación para obtener más información acerca de una alerta de seguridad."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-threat-intelligence-report"></a>Informe de inteligencia de amenaza de centro de seguridad de Azure
Este documento explica cómo informes de amenaza de centro de seguridad de Azure inteligente puede ayudarle a obtener más información sobre una amenaza que ha generado una alerta de seguridad.

## <a name="what-is-a-threat-intelligence-report"></a>¿Qué es un informe de inteligencia de amenaza?
Detección de amenazas del centro de seguridad funciona mediante la supervisión de información de seguridad de los recursos de Azure, la red y soluciones de socios conectados. Analiza esta información, a menudo correlación información de varios orígenes, identificar amenazas. Este proceso es parte de las [capacidades de detección](security-center-detection-capabilities.md)de centro de seguridad. 

Cuando el centro de seguridad se identifica una amenaza, activará una [alerta de seguridad](security-center-managing-and-responding-alerts.md), que contiene información detallada acerca de un evento concreto, incluidas sugerencias para solucionar el problema. Para facilitar la respuesta a incidencias a los equipos a investigar y solucionen amenazas, centro de seguridad incluye un informe de inteligencia de amenaza que contiene información sobre la amenaza de que se ha detectado, incluida la información como la: 

- Intruso identidad o asociaciones (si esta información está disponible)
- Objetivos de atacantes
- Campañas de ataque histórica y actual (si esta información está disponible)
- Atacantes tácticas, procedimientos y herramientas
- Indicadores de asociados de compromiso (IoC) como direcciones URL y hash de archivo
- Victimology, que es el sector y prevalencia geográfica para ayudarle a determinar si los recursos de Azure están en riesgo
- Información de mitigación y corrección

>[AZURE.NOTE] La cantidad de información de cualquier informe determinado variará; el nivel de detalle se basa en el malware actividad y prevalencia.

Centro de seguridad tiene tres tipos de informes de amenaza, que pueden variar según el ataque. Los informes disponibles son:

- **Informe de grupo de actividad**: proporciona profundización en atacantes, sus objetivos y tácticas.
- **Informe de la campaña**: se centra en los detalles de las campañas de ataque específicos. 
- **Informe de resumen de amenaza**: cubre todos los elementos en los informes de dos anterior.

Este tipo de información es muy útil durante el proceso de [respuesta a incidencias](security-center-incident-response.md) , donde hay una investigación en curso para conocer el origen del ataque, razones de intruso y qué puede hacer para mitigar este problema en el futuro. 

## <a name="how-to-access-the-threat-intelligence-report"></a>¿Cómo puedo obtener acceso al informe de inteligencia de amenaza?

Puede revisar las alertas actuales consultando el mosaico de **las alertas de seguridad** . Abra el Portal de Azure y siga los pasos siguientes para ver más detalles sobre cada alerta:

1. En el panel Centro de seguridad, verá el mosaico de **las alertas de seguridad** .

2. Haga clic en el icono para abrir el módulo de **las alertas de seguridad** que contiene más detalles acerca de las alertas y haga clic en la alerta de seguridad que desea obtener más información acerca de.

    ![Alertas de seguridad](./media/security-center-threat-report/security-center-threat-report-fig1.png)

3. En este caso el módulo **sospechoso proceso ejecutado** muestra los detalles acerca de la alerta tal como se muestra en la figura siguiente:

    ![Detais de alerta de seguridad](./media/security-center-threat-report/security-center-threat-report-fig2.png)

4.  La cantidad de información disponible para cada alerta de seguridad varían según el tipo de alerta. En el campo de **informes** tiene un vínculo al informe de inteligencia de amenazas. Haga clic en ella y aparecerá otra ventana del explorador con un archivo PDF.

    ![Selección de almacenamiento](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Desde aquí puede descargar el archivo PDF para este informe y obtener más información sobre el problema de seguridad que se ha detectado y realizar acciones en función de la información provista.

## <a name="see-also"></a>Vea también

En este documento, ha aprendido cómo se puede ayudar informes de amenaza de centro de seguridad de Azure inteligente durante una investigación sobre las alertas de seguridad. Para obtener más información sobre el centro de seguridad de Azure, consulte lo siguiente:

- [Preguntas más frecuentes del centro de seguridad de azure](security-center-faq.md). Preguntas más frecuentes sobre el uso del servicio de búsqueda.
- [Sacar provecho de centro de seguridad de Azure de respuesta a incidencias](security-center-incident-response.md)
- [Capacidades de detección de centro de seguridad de Azure](security-center-detection-capabilities.md)
- [Guían de planificación de centro de seguridad de azure y operaciones](security-center-planning-and-operations-guide.md). Obtenga información sobre cómo planificar y comprender las consideraciones de diseño para la adopción de centro de seguridad de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md). Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Control el incidente de seguridad en el centro de seguridad de Azure](security-center-incident.md)
- [Blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/). Busque entradas de blog sobre cumplimiento y la seguridad de Azure.
