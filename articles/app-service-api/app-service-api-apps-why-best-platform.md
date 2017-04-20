<properties 
    pageTitle="Introducción de aplicaciones de la API | Microsoft Azure" 
    description="Obtenga información sobre cómo servicio de aplicación de Azure le ayuda a desarrollar, host y las API de REST de consumir." 
    services="app-service\api" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/23/2016" 
    ms.author="rachelap"/>

# <a name="api-apps-overview"></a>Información general de aplicaciones de la API

API de aplicaciones de servicio de la aplicación de Azure ofrecen características que hacen que resulte más fácil desarrollar, host y consumir API en la nube y locales. Con la API aplicaciones obtener seguridad de nivel empresarial, control de acceso simple, conectividad híbrido, generación automática de SDK y perfecta integración con [Aplicaciones de lógica](../app-service-logic/app-service-logic-what-are-logic-apps.md).

[Servicio de aplicación de Azure](../app-service/app-service-value-prop-what-is.md) es una plataforma completamente administrada para web, móvil y escenarios de integración. Aplicaciones de la API es uno de los cuatro tipos de aplicación que ofrece [Servicios de aplicación de Azure](../app-service/app-service-value-prop-what-is.md).

![Tipos de aplicación de servicio de la aplicación de Azure](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## <a name="why-use-api-apps"></a>¿Por qué usar API aplicaciones?

Estas son algunas características clave de aplicaciones de la API:

- **Traer la API existente como-es** -no es necesario que cambie el código en la API existentes a aprovechar las ventajas de la API de aplicaciones: implemente el código para una aplicación de API. Puede usar la API cualquier lenguaje o framework compatible con la aplicación de servicio, incluido ASP.NET y C#, Java, PHP, Node.js y Python.

- **Consumo fácil** - compatibilidad integrada para [metadatos Swagger API](http://swagger.io/) hace que la API de fácil consumo por una gran variedad de clientes.  Generar automáticamente el código de cliente de la API en una gran variedad de idiomas, incluyendo C#, Java y Javascript. Configurar fácilmente [CORS](app-service-api-cors-consume-javascript.md) sin cambiar el código. Para obtener más información, consulte [aplicaciones API de aplicación de servicio de metadatos para la generación de detección y el código de la API](app-service-api-metadata.md) y [Consume una aplicación de la API de JavaScript mediante CORS](app-service-api-cors-consume-javascript.md). 

- **Control de acceso simple** - proteger una aplicación de API de acceso no autenticado sin cambios en el código. Servicios de autenticación integrados proteger API para acceso por otros servicios o clientes que representa a los usuarios. Proveedores de identidades compatibles incluyen Azure Active Directory, Facebook, Twitter, Google y Account de Microsoft. Los clientes pueden utilizar la biblioteca de autenticación de Active Directory (ADAL) o el SDK de aplicaciones móviles. Para obtener más información, vea [autenticación y la autorización para las aplicaciones de la API de servicio de aplicaciones de Azure](app-service-api-authentication.md).

- **Integración de visual Studio** - herramientas dedicadas en Visual Studio simplificar el trabajo de creación, implementación, consumo, depuración y administración de aplicaciones de la API. Para obtener más información, consulte [el SDK de Azure 2.8.1 para .NET de anuncio](/blog/announcing-azure-sdk-2-8-1-for-net/).

- **Integración con aplicaciones de lógica** - aplicaciones API que cree pueden usar las [Aplicaciones de lógica de aplicación de servicio](../app-service-logic/app-service-logic-what-are-logic-apps.md).  Para obtener más información, consulte [uso de la API personalizada hospedada en servicio de la aplicación con aplicaciones de lógica](../app-service-logic/app-service-logic-custom-hosted-api.md) y [nuevo esquema versión 2015-08-01-preview](../app-service-logic/app-service-logic-schema-2015-08-01.md).

Además, una aplicación de API puede sacar partido de las características ofrecidas por [Aplicaciones Web](../app-service-web/app-service-web-overview.md) y [Aplicaciones móviles](../app-service-mobile/app-service-mobile-value-prop.md). Lo contrario también es cierto: si usa una aplicación web o una aplicación móvil para hospedar una API, puede aprovechar características de aplicaciones de la API como metadatos Swagger cliente CORS y generación de código de acceso a Explorador de dominios. La única diferencia entre los tipos de tres aplicación (API, web, móvil) es el nombre y el icono que se utiliza para ellos en el portal de Azure.

## <a name="whats-the-difference-between-api-apps-and-azure-api-management"></a>¿Cuál es la diferencia entre las aplicaciones de API y administración de la API de Azure?

API de aplicaciones y [Administración de la API de Azure](../api-management/api-management-key-concepts.md) son servicios complementarios:

* Administración de la API es acerca de la API de administración. Poner un front-end de administración de la API en una API para uso monitor y acelerador, manipular la entrada y salida, consolidar varias API en un extremo y así sucesivamente. Las API administradas pueden estar hospedadas en cualquier lugar.
* Aplicaciones de la API es acerca de la API de hospedaje. El servicio incluye características que facilitan la API de desarrollo y consumir, pero no los tipos de supervisión, limitación, manipular o hace que la API de administración de la consolidación. Si no necesita las características de administración de la API, puede hospedar las API en las aplicaciones de la API sin usar la API de administración.

Este es un diagrama que muestra la administración de la API para API alojadas en las aplicaciones de la API y cualquier otro sitio.

![Administración de la API de Azure y aplicaciones de la API](./media/app-service-api-apps-why-best-platform/apia-apim.png)

Algunas características de administración de la API y aplicaciones de la API tienen funciones similares.  Por ejemplo, ambos pueden automatizar CORS soporte. Cuando utiliza conjuntamente los dos servicios, debería usar API de administración para CORS ya que funciona como el front-end a sus aplicaciones de la API. 

## <a name="getting-started"></a>Introducción

Para empezar con la API de aplicaciones mediante la implementación de código de ejemplo en uno, consulte el tutorial para el marco que prefiera:

* [ASP.NET](app-service-api-dotnet-get-started.md) 
* [Node.js](app-service-api-nodejs-api-app.md) 
* [Java](app-service-api-java-api-app.md) 

Para hacer preguntas sobre las aplicaciones de la API, iniciar un subproceso en el [foro de la API de aplicaciones](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps). 
