<properties
   pageTitle="Introducción a la integración de registro de Microsoft Azure | Microsoft Azure"
   description="Obtenga información sobre la integración de registro de Azure, sus capacidades claves y cómo funciona."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Introducción a la integración de registro de Microsoft Azure (vista preliminar)

Obtenga información sobre la integración de registro de Azure, sus capacidades claves y cómo funciona.

## <a name="overview"></a>Información general

Plataforma como servicio (PaaS) y la infraestructura como servicio (IaaS) hospedado en Azure generar una gran cantidad de datos en los registros de seguridad. Estos registros contienen información esencial que puede proporcionar inteligencia y eficaz recomendaciones sobre violaciones de políticas, amenazas internas y externas, problemas de cumplimiento de reglamentaciones y anomalías en la red, host y actividad de usuario.

Integración de registro Azure le permite integrar registros sin formato de los recursos de Azure en los sistemas de gestión de eventos (SIEM) e información de seguridad local. Integración de Azure registro recopila Azure diagnósticos de Windows *(TORUNDA)* máquinas virtuales de Windows, así como los diagnósticos de soluciones de socios como un Firewall de aplicación Web (WAFS). Esta integración proporciona un panel unificado de todos los activos, local o en la nube para que puedan agregar, relacionar, analizar y alertas para los eventos de seguridad.

![Integración de registro de Azure][1]

## <a name="what-logs-can-i-integrate"></a>¿Qué registros se debe integrar?

Azure genera extensiva de registro por cada servicio de Azure. Estos registros están clasificados por dos tipos principales:

- **Registros de control y administración**, que da visibilidad a las operaciones de administrador de recursos de Azure crear, actualizar y eliminar. Registros de auditoría de Azure es un ejemplo de este tipo de registro.
- **Registros de plano de datos**, que da visibilidad en los eventos generados como parte del uso de un recurso de Azure. Ejemplos de este tipo de registro son los eventos de Windows, en sistema, seguridad, y registros de la aplicación en una máquina virtual.

Integración de Azure registro actualmente admite la integración de los registros de auditoría de Azure, registros de máquina virtual y las alertas del centro de seguridad de Azure.

Si tiene preguntas sobre la integración de registro de Azure, envíe un correo electrónico a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Pasos siguientes

En este documento, se han introducido a la integración de registro de Azure. Para obtener más información sobre la integración de registro de Azure y los tipos de registros compatibles, consulte lo siguiente:

- [Integración con Microsoft Azure registro para los registros de Azure (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) : Centro de descarga para información detallada, requisitos del sistema y las instrucciones de instalación en la integración de registro de Azure.
- [Introducción a la integración de Azure registro](security-azure-log-integration-get-started.md) : este tutorial le guiará a través de la instalación de integración de registro de Azure y la integración de registros de almacenamiento de Azure, registros de auditoría de Azure y alertas del centro de seguridad.
- [Pasos de configuración de partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : esta entrada de blog muestra cómo configurar la integración de Azure registro para trabajar con las soluciones de asociados Splunk, ArcSight HP y IBM QRadar.
- [Registro de azure integración con frecuencia preguntas más frecuentes (P+F)](security-azure-log-integration-faq.md) - este preguntas más frecuentes sobre responde a preguntas sobre la integración de registro de Azure.
- [Alertas del centro de seguridad de la integración con Azure iniciar integración](../security-center/security-center-integrating-alerts-with-log-integration.md) : este documento se muestra cómo sincronizar alertas del centro de seguridad, junto con los eventos de seguridad de máquina virtual recopilados diagnósticos de Azure y Azure registros de auditoría, con la solución SIEM o el análisis de registro.
- [Nuevas características para los registros de auditoría de Azure y diagnóstico de Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : esta entrada de blog presenta a los registros de auditoría de Azure y otras características que ayudan a obtienen recomendaciones sobre las operaciones de los recursos de Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
