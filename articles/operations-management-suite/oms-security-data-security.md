<properties
   pageTitle="Seguridad del conjunto de aplicaciones de administración de operaciones y seguridad de datos de auditoría solución | Microsoft Azure"
   description="Este documento explica cómo administra y protegido de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="yurid"/>

# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Seguridad de datos de solución de seguridad del conjunto de aplicaciones de administración de operaciones y auditoría

Para ayudar a los clientes evitar, detectar y responder a las amenazas, [solución de auditoría y seguridad (OMS) del conjunto de aplicaciones de administración de operaciones](operations-management-suite-overview.md) recopila y procesa datos sobre los recursos, que incluye:

- Registro de eventos de seguridad
- Eventos de seguimiento para Windows (ETW)
- Eventos de auditoría de AppLocker
- Registro de Firewall de Windows
- Eventos de análisis de amenazas avanzados
- Resultados de la evaluación de línea base
- Resultados de la evaluación de antimalware
- Resultados de evaluación y revisión de actualización
- Secuencias de Syslogs explícitamente están habilitadas en el agente

Asegúrese de compromisos seguros para proteger la privacidad y seguridad de datos. Microsoft se adhiere a las directrices de seguridad y cumplimiento estrictas: desde la codificación a un servicio de funcionamiento.
En este artículo se explica cómo administra y a salvo de OMS solución de seguridad y auditoría.

## <a name="data-sources"></a>Orígenes de datos

Solución de auditoría y seguridad OMS analizan los datos de máquinas virtuales y equipos físicos donde está instalado el agente de OMS. Solución de auditoría y seguridad de OMS pueden recopilar información de configuración de eventos de seguridad, como mensajes de registro del sistema, registros de IIS, registros de auditoría y eventos de Windows. Ejemplos de este tipo de datos son: tipo de sistema operativo y la versión, ejecuta procesos, nombre del equipo, direcciones IP, se registran en usuario y el identificador de inquilinos.  

## <a name="data-protection"></a>Protección de datos

**Separación de datos**: los datos se mantienen separadas de forma lógica en cada componente en todo el servicio. Etiquetado todos los datos por la organización. Este etiquetado continúa durante el ciclo de vida de datos y, a continuación, se aplica en cada nivel del servicio. 

**Acceso a datos**: para proporcionar recomendaciones de seguridad e investigar posibles amenazas de seguridad, personal de Microsoft puede tener acceso a los datos recopilados o analizar por servicios. Se cumplen las [Condiciones de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) y la [Declaración de privacidad](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), qué estado que Microsoft no puede usar los datos del cliente o derivan información para ningún propósito comercial publicidad o similar. Para proporcionar recomendaciones de seguridad e investigar posibles amenazas de seguridad, personal de Microsoft puede tener acceso a los datos recopilados o analizar por servicios. Sólo usamos datos del cliente según sea necesario para proporcionar servicios de Azure, incluidos fines compatibles con proporcionar estos servicios. Conservar todos los derechos de sus propios datos.

**Uso de datos**: Microsoft usa patrones y sobre amenazas aparecer en varios inquilinos para mejorar nuestras capacidades de detección y prevención; Para hacerlo conforme a los compromisos de privacidad que se describe en la [Declaración de privacidad](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [AZURE.NOTE] Ubicación de datos está configurado en el nivel de área de trabajo OMS, durante la creación del área de trabajo, que forma parte del proceso de configuración de seguridad de OMS y auditoría inicial.

## <a name="see-also"></a>Vea también

En este documento, ha aprendido cómo administra y a salvo de OMS. Para obtener más información sobre la solución de seguridad de OMS y auditoría, consulte:

- [Información general de las operaciones Management Suite (OMS)](operations-management-suite-overview.md)
- [Supervisar y responder a las alertas de seguridad de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría](oms-security-responding-alerts.md)
- [Supervisar los recursos de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría](oms-security-monitoring-resources.md)

