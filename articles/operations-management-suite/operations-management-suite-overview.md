<properties
   pageTitle="Información general de administración Suite (OMS) operaciones | Microsoft Azure"
   description="Suite de administración de operaciones de Microsoft (OMS) es basado en la nube administración solución de Microsoft que le ayuda a administrar y proteger su local y la infraestructura de nube.  En este artículo se identifica los distintos servicios que se incluyen en OMS y se proporciona vínculos a su contenido detallada."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="bwren" />

# <a name="what-is-operations-management-suite-oms"></a>¿Qué es el conjunto de aplicaciones de administración de operaciones (OMS)?

Suite de administración de operaciones de Microsoft (OMS) es basado en la nube administración solución de Microsoft que le ayuda a administrar y proteger su local y la infraestructura de nube.  Dado que OMS se implementa como un servicio basado en la nube, puede hacer que marcha rápidamente con una inversión mínima en servicios de infraestructura.  Nuevas características se entregarán automáticamente, lo que le ahorra mantenimiento permanente y costos de actualización.

Además de proporcionar valiosos servicios en su propia, puede integrar OMS con componentes de System Center como System Center Operations Manager para ampliar las inversiones existentes de administración en la nube.  Centro de sistema y OMS pueden trabajar conjuntamente para proporcionar una experiencia de administración híbrida completa.

Las siguientes secciones proporcionan una descripción de alto nivel de las áreas de valor distinto de OMS y los servicios que se implementan.  Puede hacer referencia a la arquitectura OMS para obtener información general de los distintos componentes OMS antes de revisar la documentación detallada para cada uno.


## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Información y análisis](media/operations-management-suite-overview/icon-insight-analytics.png) Información y análisis

[Análisis de registro](http://azure.microsoft.com/documentation/services/log-analytics) le ayuda a recopilar, correlación, buscar y actuar en los datos de registro y rendimiento generados por sistemas operativos y aplicaciones. Proporciona perspectivas operativas en tiempo real con búsqueda integrada y paneles personalizados para analizar fácilmente millones de registros en todas las cargas de trabajo y los servidores independientemente de su ubicación física.

Puede agregar fácilmente soluciones para el análisis de registro que defina datos para recopilar y la lógica de su análisis.  Las soluciones pueden incluir funcionalidad adicional que se entrega automáticamente a agentes con mínima o ninguna configuración.  Además de usar herramientas de análisis de soluciones individuales, puede realizar búsquedas personalizadas a través de todo el conjunto de datos para relacionar datos entre sistemas y aplicaciones.  


## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![Control y automatización](media/operations-management-suite-overview/icon-automation-control.png) Control y automatización

Automatización de Azure automatiza procesos administrativos con [runbooks](../automation/automation-runbook-types.md) que se basa en PowerShell y ejecutar en la nube de Azure.  Runbooks puede tener acceso a cualquier producto o servicio que se puede administrar con PowerShell, incluidos los recursos de otros servicios de Web como Amazon (AWS) de nubes.  Runbooks también se puede ejecutar en un servidor en el centro de datos local para administrar recursos locales.

Automatización de Azure proporciona la administración de la configuración con [PowerShell DSC](../automation/automation-dsc-overview.md).  Puede crear y administrar recursos de DSC alojados en Azure y aplicarla a sistemas de nube y locales para definir y aplicar automáticamente su configuración.


## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Protección y recuperación](media/operations-management-suite-overview/icon-protection-recovery.png) Protección y recuperación

[Copia de seguridad de Azure](http://azure.microsoft.com/documentation/services/backup) protege los datos de la aplicación y conserva años sin cualquier inversión de capital y costos operativos mínimos.  Copia de seguridad datos de servidores físicos y virtuales de Windows además de cargas de trabajo como SQL Server y SharePoint.  También se pueden utilizar por el Administrador de protección de sistema Centro de datos (DPM) para replicar datos protegidos en Azure redundancia y almacenamiento a largo plazo.

[Recuperación de sitio de Azure](http://azure.microsoft.com/documentation/services/site-recovery) contribuye a la continuidad empresarial y una estrategia de recuperación (BCDR) por coordinar replicación, migración tras error y recuperación de máquinas virtuales de local Hyper-V, máquinas virtuales de VMware y servidores físicos de Windows o Linux. Puede reproducir máquinas a un centro de datos secundario o ampliar su centro de datos mediante la replicación de en Azure. Recuperación de sitio también proporciona migración simple y recuperación para las cargas de trabajo. Se integra con mecanismos de recuperación de desastres como SQL Server AlwaysOn y proporcionan los planes de recuperación de fácil migración tras error de cargas de trabajo que están en niveles en varios equipos.


## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![Cumplimiento y la seguridad OMS](media/operations-management-suite-overview/icon-security-compliance.png) Seguridad y cumplimiento
Seguridad y cumplimiento le ayuda a identificar, evaluar y mitigar riesgos de seguridad en la infraestructura.  Estas características de OMS se implementan a través de varias soluciones de análisis de registro que analizar los datos del registro y la configuración de los sistemas de agente para ayudarle a garantizar la seguridad de su entorno en curso.

- La [solución de seguridad y auditoría](oms-security-getting-started.md ) recopila y analiza los eventos de seguridad en los sistemas administrados para identificar la actividad sospechosa.
- La [solución Antimalware](log-analytics-malware.md ) informes sobre el estado de protección antimalware en sistemas administrados.  
- La solución de actualizaciones de sistema realiza un análisis de las actualizaciones de seguridad y otras actualizaciones en los sistemas administrados para que identificar fácilmente los sistemas que requiere revisión.


## <a name="next-steps"></a>Pasos siguientes
- Obtenga información sobre [el análisis de registro](http://azure.microsoft.com/documentation/services/log-analytics).
- Obtenga información sobre la [automatización de Azure](../automation/automation-intro.md).
- Obtenga información sobre la [copia de seguridad de Azure](http://azure.microsoft.com/documentation/services/backup).
- Obtenga información sobre la [recuperación de sitio de Azure](http://azure.microsoft.com/documentation/services/site-recovery).
