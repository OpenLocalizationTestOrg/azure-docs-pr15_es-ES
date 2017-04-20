<properties
    pageTitle="Autenticación y la autorización para las aplicaciones de la API de servicio de la aplicación de Azure | Microsoft Azure"
    description="Obtenga información sobre los servicios de autenticación y la autorización de servicio de la aplicación de Azure proporciona para las aplicaciones de la API."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="rachelap"/>

# <a name="authentication-and-authorization-for-api-apps-in-azure-app-service"></a>Autenticación y la autorización para las aplicaciones de la API de servicio de la aplicación de Azure

## <a name="overview"></a>Información general 

> [AZURE.NOTE] En este tema se migrarán a una consolidación [aplicación de servicio de autenticación o autorización](../app-service/app-service-authentication-overview.md) tema, que abarca la Web, móvil y aplicaciones de la API.

Servicio de aplicación de Azure ofrece servicios de autenticación y la autorización integrados que implementan [OAuth 2.0](#oauth) y [Conectar OpenID](#oauth). En este artículo se describe los servicios y las opciones que están disponibles para las aplicaciones de la API de servicio de aplicaciones de Azure.

El siguiente diagrama muestra algunas características clave de autenticación de servicio de aplicaciones:

* Preprocesa las solicitudes entrantes de API, lo que significa que funciona con cualquier lenguaje o framework compatible con la aplicación de servicio.
* Le ofrece varias opciones para la autenticación de cuánto trabajo desea hacer en su propio código.
* Funciona para el usuario final y la autenticación de la cuenta de servicio. 
* Es compatible con cinco proveedores de identidades: Azure Active Directory, Facebook, Google, Twitter y Account de Microsoft.
* Funciona de la misma para aplicaciones de la API, aplicaciones Web y aplicaciones móviles.

![](./media/app-service-api-authentication/api-apps-overview.png)

## <a name="language-agnostic"></a>Independiente del idioma

Proceso de autenticación de servicio de aplicación pasa antes de que las solicitudes de llegar a la aplicación de la API, lo que significa que funcionan las características de autenticación para las aplicaciones de la API escritas en cualquier idioma o un marco.  La API se puede basar en ASP.NET, Java, Node.js o cualquier marco que admite la aplicación de servicio.

Servicio de aplicación pasa en el token de web JSON (JWT) en el encabezado de autorización de una solicitud HTTP y código escrito en cualquier idioma o un marco de trabajo puede obtener la información que necesita desde el token. Además, servicio de aplicación permite facilitar el acceso a las notificaciones más utilizadas mediante la configuración de algunos encabezados especiales, como las siguientes:

* X MS CLIENTE PRINCIPAL DE NOMBRE
* X MS CLIENTE CAPITAL ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
En la API de .NET, puede usar el `Authorize` de atributo y para autorización específica, puede escribir fácilmente el código en función de reclamaciones porque la información de las reclamaciones se rellena en clases de .NET.

## <a name="multiple-protection-options"></a>Varias opciones de protección

Aplicación de servicio puede evitar que las solicitudes HTTP anónimas se comunique con la aplicación de la API, puede pasar en todas las solicitudes y validar tokens para las solicitudes de incluyan o puede dejar que a través de todas las solicitudes sin realizar ninguna acción en ellas:

1. Permitir solo las solicitudes autenticadas llegar a la aplicación de la API.

    Si se recibe una solicitud anónima desde un explorador, servicio de la aplicación le redirigirá a una página de inicio de sesión para el proveedor de autenticación (Twitter AD, Google, Azure, etc.) que elija. 

    Con esta opción, no es necesario escribir cualquier código de autenticación en su aplicación y se simplifica el código de autorización porque se proporcionan las solicitudes más importantes en los encabezados HTTP.

2. Permitir que todas las solicitudes llegar a la aplicación de la API, pero validar solicitudes autenticadas y pasar a lo largo de la información de autenticación en los encabezados HTTP.

    Esta opción permite más flexibilidad en la administración de solicitudes de anónimas, pero tiene que escribir código si desea evitar que los usuarios anónimos con la API. Dado que las notificaciones más populares se pasan en los encabezados de las solicitudes HTTP, el código de autorización es relativamente sencillo.
    
3. Permitir que todas las solicitudes llegar a la API, realizar ninguna acción en la información de autenticación de las solicitudes.

    Esta opción conserva las tareas de autenticación y la autorización por completo hasta el código de la aplicación.

En el [portal de Azure](https://portal.azure.com/), seleccione la opción que desee en el **autenticación / autorización** módulo.

![](./media/app-service-api-authentication/authblade.png)

Para las opciones 1 y 2, activar la **Aplicación de servicio de autenticación**y, en la lista desplegable **acción que se ejecuta cuando no se autentica la solicitud** , elija **iniciar sesión** o **Permitir solicitud (ninguna acción)**.  Si elige **iniciar sesión**, debe elegir un proveedor de servicios de autenticación y configurar ese proveedor.

![](./media/app-service-api-authentication/actiontotake.png)

Para obtener información detallada sobre cómo configurar la autenticación, consulte [cómo configurar la aplicación de servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). El artículo se aplica a aplicaciones de la API, así como aplicaciones móviles y vínculos a otros artículos para los demás proveedores de autenticación.
 
## <a id="internal"></a>Autenticación de cuentas de servicio

Autenticación de servicio de aplicaciones funciona para escenarios internos como para llamar desde una aplicación de API a otra aplicación de la API. En este escenario, obtendrá un token mediante credenciales para una cuenta de servicio en lugar de credenciales de usuario final. Una cuenta de servicio también conocido como es un *servicio principal* de Azure Active Directory y autenticación mediante dicha cuenta también conocido como es un escenario de servicio para el servicio. 

Escenarios de servicio para proteger la aplicación de la API llamada mediante Azure Active Directory y proporcionar un token de autorización principal del servicio AAD cuando llame a la aplicación de la API. Obtener un token proporcionando el cliente de identificador y el cliente secreto desde la aplicación de AAD. Código especial de Azure solo no es necesario, como solía ser verdaderos para controlar el token de Zumo de servicios móviles. Un ejemplo de este escenario de uso de las aplicaciones de la API de ASP.NET está cubierto por el tutorial [autenticación principal del servicio para las aplicaciones de la API](app-service-api-dotnet-service-principal-auth.md).

Si desea controlar un escenario de servicio de servicio sin usar autenticación de servicio de la aplicación, puede usar autenticación básica o los certificados de cliente. Para obtener información acerca de los certificados de cliente en Azure, consulte [Cómo configurar TLS mutuo autenticación para las aplicaciones Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Para obtener información acerca de la autenticación básica en ASP.NET, consulte [Filtros de autenticación en ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Autenticación de cuentas de servicio desde una aplicación de la lógica de servicio de aplicación a una aplicación de API es un caso especial que se explica en [usando la API personalizada hospedada en servicio de la aplicación con aplicaciones de lógica](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="mobile-client-authentication"></a>Autenticación de clientes móviles

Para obtener información sobre cómo controlar la autenticación de clientes móviles, consulte la [documentación de autenticación para aplicaciones móviles](../app-service-mobile/app-service-mobile-ios-get-started-users.md). Autenticación de servicio de aplicaciones funciona del mismo modo para aplicaciones móviles y aplicaciones de la API.
  
## <a name="more-information"></a>Más información

Para obtener más información sobre la autenticación y la autorización de servicio de la aplicación de Azure, consulte los siguientes recursos:

* [Servicio de aplicación de autenticación de expansión / autorización](/blog/announcing-app-service-authentication-authorization/)
* [Cómo configurar la aplicación de servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (Incluye vínculos para otros proveedores de autenticación en la parte superior de la página). 

Para obtener más información acerca de OAuth 2.0, conectar OpenID y Tokens de Web JSON (JWT), consulte los siguientes recursos.

* [Introducción a OAuth 2.0] (http://shop.oreilly.com/product/0636920021810.do "Introducción a OAuth 2.0") 
* [Introducción a OAuth2, OpenID conectarse y JSON Web Tokens (JWT) - curso PluralSight](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Crear y proteger API de REST para varios clientes en ASP.NET - curso PluralSight](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Para obtener más información acerca de Azure Active Directory, consulte los siguientes recursos.

* [Escenarios de Azure AD](http://aka.ms/aadscenarios)
* [Guía de Azure programadores de AD](http://aka.ms/aaddev)
* [Ejemplos de Azure AD](http://aka.ms/aadsamples)

## <a name="next-steps"></a>Pasos siguientes

Este artículo explica las características de autenticación y la autorización del servicio de aplicación que puede utilizar para las aplicaciones de la API de. El siguiente tutorial de la serie de introducción al obtener muestra cómo implementar la [autenticación de usuario en las aplicaciones de API del servicio de aplicación](app-service-api-dotnet-user-principal-auth.md).
