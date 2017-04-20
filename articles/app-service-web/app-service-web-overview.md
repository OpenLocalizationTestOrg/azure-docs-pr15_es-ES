<properties
    pageTitle="Descripción general de aplicaciones Web | Microsoft Azure"
    description="Obtenga información sobre cómo el servicio de aplicación de Azure le ayuda a desarrollar y aplicaciones web del host"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="cephalin"/>

# <a name="web-apps-overview"></a>Introducción a aplicaciones Web

*Aplicación de servicio Web Apps* es una plataforma de cálculo totalmente administrado que está optimizada para el hospedaje de aplicaciones web y sitios Web. Esta oferta de [plataforma como servicio](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) de Microsoft Azure permite centrarse en la lógica empresarial mientras Azure se encarga de la infraestructura para ejecutar y ajustar el tamaño de las aplicaciones.

El siguiente vídeo 5 minutos presenta Azure aplicación de servicio Web Apps.

[AZURE.VIDEO azure-app-service-web-apps-with-yochay-kiriaty]

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="what-is-a-web-app-in-app-service"></a>¿Qué es una aplicación web en la aplicación de servicio?

En el servicio de aplicación, una *aplicación web* es los recursos de cálculo Azure proporciona para alojar una sitio Web o una aplicación web.  

Recursos de cálculo pueden estar en compartido o dedicados máquinas virtuales (VM), según el nivel de precios que elija. El código de la aplicación se ejecuta en una máquina virtual administrada aislado de los demás clientes.

El código puede estar en cualquier idioma o un marco de trabajo que es compatible con el [Servicio de aplicación de Azure](../app-service/app-service-value-prop-what-is.md), como ASP.NET, Node.js, Java, PHP o Python. También puede ejecutar secuencias de comandos que use [PowerShell y otros lenguajes de scripting](web-sites-create-web-jobs.md#acceptablefiles) en una aplicación web.

Para obtener ejemplos de escenarios de aplicación típicos que puede usar las aplicaciones Web para, vea [escenarios de aplicación Web](https://azure.microsoft.com/documentation/scenarios/web-app/) y la sección de **escenarios y recomendaciones** de [servicio de la aplicación de Azure, comparación de máquinas virtuales, tela de servicio y servicios en la nube](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>¿Por qué usar aplicaciones Web?

Estas son algunas características clave del servicio de aplicación que se aplican a aplicaciones Web:

- **Varios idiomas y marcos** - aplicación de servicio admite primera clase ASP.NET, Node.js, Java, PHP y Python. También puede ejecutar [PowerShell y otras secuencias de comandos o ejecutables](../app-service-web/web-sites-create-web-jobs.md) en máquinas virtuales de servicio de aplicación.

- **Optimización de DevOps** - configurar [implementación y la integración continua](../app-service-web/app-service-continuous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket. Promover actualizaciones a través de [prueba y entornos de ensayo](../app-service-web/web-sites-staged-publishing.md). Realizar [A / B pruebas](../app-service-web/app-service-web-test-in-production-get-start.md). Administrar las aplicaciones de servicio de la aplicación con [Azure PowerShell](../powershell-install-configure.md) o la [entre plataformas de línea de comandos interfaz](../xplat-cli-install.md).

- **Escala Global con alta disponibilidad** - escala [hacia arriba](../app-service-web/web-sites-scale.md) o [fuera](../monitoring-and-diagnostics/insights-how-to-scale.md) manual o automáticamente. Hospedar las aplicaciones en cualquier lugar de la infraestructura de centro de datos global de Microsoft y, a continuación, el servicio de aplicación [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) va alta disponibilidad.

- **Conexiones a datos locales y plataformas de SaaS** - elija de más de 50 [conectores](../connectors/apis-list.md) para sistemas empresariales (como SAP, Siebel y Oracle), servicios de SaaS (por ejemplo, Salesforce y Office 365) y servicios de internet (por ejemplo, Facebook y Twitter). Acceso a datos con [Conexiones híbrido](../biztalk-services/integration-hybrid-connection-overview.md) y [Redes virtuales de Azure](../app-service-web/web-sites-integrate-with-vnet.md)en local.

- **Seguridad y cumplimiento** - aplicación de servicio es [ISO, seguridad social y PCI compatible](https://www.microsoft.com/TrustCenter/).

- **Plantillas de aplicación** - elegir entre una amplia lista de plantillas de aplicación en [Azure Marketplace](https://azure.microsoft.com/marketplace/) que le permiten utilizar un Asistente para instalar el software de código abierto conocido como WordPress, Joomla y Drupal.

- **Integración de visual Studio** - herramientas dedicadas en Visual Studio simplificar el trabajo de creación, implementación y depuración.

Además, una aplicación web puede aprovechar las ventajas de las características ofrecidas por [Aplicaciones de la API](../app-service-api/app-service-api-apps-why-best-platform.md) (como la compatibilidad con CORS) y [Aplicaciones móviles](../app-service-mobile/app-service-mobile-value-prop.md) (por ejemplo, las notificaciones de inserción). Para obtener más información acerca de los tipos de aplicación de servicio de aplicaciones, vea [Introducción al servicio de la aplicación de Azure](../app-service/app-service-value-prop-what-is.md).

Además de aplicaciones Web en la aplicación de servicio, Azure ofrece otros servicios que se pueden usar para alojar aplicaciones web y sitios Web. Para la mayoría de los escenarios, aplicaciones Web es la mejor opción.  Arquitectura de microservice, considere la posibilidad de [Tela de servicio](https://azure.microsoft.com/documentation/services/service-fabric)y si necesita tener más control sobre las VM que el código se ejecuta en, considere la posibilidad de [máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Para obtener más información sobre cómo elegir entre estos servicios Azure, vea [servicio de aplicación de Azure, máquinas virtuales, tela de servicio y comparación de servicios en la nube](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Introducción

Para empezar por implementar código de ejemplo para una nueva aplicación web en la aplicación de servicio, siga el tutorial de [implementar la aplicación web de primera a Azure en 5 minutos](app-service-web-get-started.md) . Tendrá una cuenta gratuita de Azure.

Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.
