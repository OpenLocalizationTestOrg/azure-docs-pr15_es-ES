<properties
    pageTitle="Azure de aplicación de servicio para web, móvil y aplicaciones de la API | Microsoft Azure"
    description="Obtenga información sobre cómo Azure aplicación de servicio le ayuda a desarrollar, implementar y administrar web y aplicaciones móviles."
    keywords="servicio de aplicación, servicio de aplicaciones de azure, servicio de aplicación de costo, escala, scalable, implementación de la aplicación, implementación de la aplicación de azure, paas, plataforma como servicio, sitio Web, sitio web, web, móviles de azure"
    services="app-service"
    documentationCenter=""
    authors="omarkmsft"
    manager="erikre"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="omark"/>

# <a name="what-is-azure-app-service"></a>¿Qué es el servicio de aplicación de Azure?

*Aplicación* es una [plataforma como servicio](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) que se ofrece de Microsoft Azure. Crear web y aplicaciones móviles en cualquier plataforma o dispositivo. Integre sus aplicaciones con soluciones SaaS, conectar con aplicaciones locales y automatizar los procesos empresariales. Azure ejecuta las aplicaciones totalmente administrado máquinas virtuales de Windows (VM), con la elección de los recursos compartidos de máquina virtual o VM dedicadas.

Aplicación de servicio incluye la web y capacidades móviles que nos previamente entregados por separado como sitios Web de Azure y Azure Mobile Services. También incluye nuevas capacidades para automatizar procesos empresariales y nube API de hospedaje. Como un servicio integrado único, servicio de la aplicación le permite redactar varios componentes, sitios Web, fondo de una aplicación móvil, las API de REST y procesos empresariales--en una única solución.

El siguiente vídeo 4 minutos proporciona una breve explicación de cómo el servicio de aplicación se relaciona con las ofertas de Azure anteriores y novedades en él.

+[AZURE.VIDEO app-service-history-lesson]

## <a name="why-use-app-service"></a>¿Por qué usar la aplicación de servicio?

Estas son algunas características clave y capacidades de aplicación de servicio:

- **Varios idiomas y marcos** - aplicación de servicio admite primera clase ASP.NET, Node.js, Java, PHP y Python. También puede ejecutar [Windows PowerShell y otras secuencias de comandos o ejecutables](../app-service-web/web-sites-create-web-jobs.md) en máquinas virtuales de servicio de aplicación.

- **Optimización de DevOps** - configurar [implementación y la integración continua](../app-service-web/app-service-continuous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket. Promover actualizaciones a través de [prueba y entornos de ensayo](../app-service-web/web-sites-staged-publishing.md). Realizar [A / B pruebas](../app-service-web/app-service-web-test-in-production-get-start.md). Administrar las aplicaciones de servicio de la aplicación con [Azure PowerShell](../powershell-install-configure.md) o la [entre plataformas de línea de comandos interfaz](../xplat-cli-install.md).

- **Escala Global con alta disponibilidad** - escala [hacia arriba](../app-service-web/web-sites-scale.md) o [fuera](../monitoring-and-diagnostics/insights-how-to-scale.md) manual o automáticamente. Hospedar las aplicaciones en cualquier lugar de la infraestructura de centro de datos global de Microsoft y, a continuación, el servicio de aplicación [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) va alta disponibilidad.

- **Conexiones a datos locales y plataformas de SaaS** - elija de más de 50 [conectores](../connectors/apis-list.md) para sistemas empresariales (como SAP, Siebel y Oracle), servicios de SaaS (por ejemplo, Salesforce y Office 365) y servicios de internet (por ejemplo, Facebook y Twitter). Acceso a datos con [Conexiones híbrido](../biztalk-services/integration-hybrid-connection-overview.md) y [Redes virtuales de Azure](../app-service-web/web-sites-integrate-with-vnet.md)en local.

- **Seguridad y cumplimiento** - aplicación de servicio es [ISO, seguridad social y PCI compatible](https://www.microsoft.com/TrustCenter/).

- **Plantillas de aplicación** - elegir entre una amplia lista de plantillas de aplicación en [Azure Marketplace](https://azure.microsoft.com/marketplace/) que le permiten utilizar un Asistente para instalar el software de código abierto conocido como WordPress, Joomla y Drupal.

- **Integración de visual Studio** - herramientas dedicadas en Visual Studio simplificar el trabajo de creación, implementación y depuración.

## <a name="app-types-in-app-service"></a>Tipos de aplicación de servicio de aplicaciones

Aplicación de servicio proporciona varios *tipos de aplicación*, cada una de las cuales se va a hospedar un tipo específico de carga de trabajo:

- [**Aplicaciones web**](../app-service-web/app-service-web-overview.md) - para alojar aplicaciones web y sitios Web.

- [**Aplicaciones móviles**](../app-service-mobile/app-service-mobile-value-prop.md) Para hospedar una aplicación móvil atrás extremos.

- [**Aplicaciones de la API**](../app-service-api/app-service-api-apps-why-best-platform.md) - para las API de REST de hospedaje.

- [**Aplicaciones de lógica**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - para automatizar procesos empresariales y la integración de sistemas y datos a través de nubes sin escribir código.

La *aplicación* de word aquí se refiere a los recursos de hospedaje dedicados a una carga de trabajo en ejecución. Toma "Online" como ejemplo, probablemente está acostumbrado a pensar en una aplicación web como el código de aplicación juntos ofrecer funcionalidad a un explorador y los recursos de cálculo. Pero en el servicio de aplicación de una *aplicación web* de los recursos de cálculo Azure proporciona para alojar el código de la aplicación. Si la aplicación se compone de un sitio web front-end y API de REST back-end, puede implementar ambos a una aplicación web o podría implementar el código de front-end para una aplicación web y el código de back-end para una aplicación de API. La aplicación puede estar compuesta de varias aplicaciones de servicio de aplicaciones de diferentes tipos.

## <a name="app-service-plans"></a>Planes de servicio de aplicaciones

[Planes de servicio de aplicación](azure-web-sites-web-hosting-plans-in-depth-overview.md) , especifique el tipo de recursos de cálculo que las aplicaciones que se ejecutan en. Si espera cargas de tráfico light, puede usar máquinas virtuales compartidas (**Free** y **compartido** precios niveles). Para las cargas superior, puede elegir entre varios tamaños de VM dedicados (niveles**básicos**, **estándar**y **Premium** ). Varias aplicaciones de servicio de aplicación pueden compartir el mismo plan y ajustar los niveles de precios de arriba y abajo juntos en el plan.

Si necesita más escalabilidad y aislamiento de red, puede ejecutar las aplicaciones en un [Entorno de aplicación de servicio](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Precios

Para obtener información acerca de cuánto costes de servicio de aplicación, vea [Tarifas de servicio de aplicación](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Pruebe la aplicación de servicio

[Crear una aplicación web de ejemplo, la aplicación móvil o la aplicación lógica](http://go.microsoft.com/fwlink/?LinkId=523751) y la reproducción con él para 1 hora, sin tarjeta de crédito no requieren, compromisos sin complicaciones.

O abra una [cuenta gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/)y pruebe uno de los tutoriales de introducción a:

* [Tutorial: Crear una aplicación web](../app-service-web/app-service-web-get-started.md)
* [Tutorial: Crear una aplicación móvil](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Tutorial: Crear una aplicación de API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Tutorial: Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
