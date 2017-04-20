<properties
    pageTitle="Tipos de extremo de la versión 2.0 | Microsoft Azure"
    description="Los tipos de aplicaciones y escenarios admitidos por el extremo de la versión 2.0 de Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="types-of-apps-for-the-v20-endpoint"></a>Tipos de aplicaciones del extremo de la versión 2.0
El extremo de la versión 2.0 es compatible con la autenticación para una gran variedad de arquitecturas de aplicación moderna, que se basan en los protocolos estándar [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) o [OpenID conectarse](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  En este documento se describe brevemente los tipos de aplicaciones que se pueden generar, independientemente de la plataforma o el idioma que prefiera.  Le ayudará a comprender los escenarios de alto niveles antes de [directamente en el código](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Conceptos básicos
Todas las aplicaciones que usa el punto final de la versión 2.0 tendrán que estar registrado en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  El proceso de registro de la aplicación se recopilar y asignar algunos valores de la aplicación:

- Un **Identificador de la aplicación** que identifica la aplicación
- **Redirigir URI** que puede usarse para dirigir respuestas a la aplicación
- Algunos valores de escenarios específicos.  Para obtener más detalles, obtenga información sobre cómo [registrar una aplicación](active-directory-v2-app-registration.md).

Una vez registrado, la aplicación se comunica con Azure AD enviando solicitudes al extremo de la versión 2.0 de Azure Active Directory.  Le proporcionamos los marcos de código abierto y bibliotecas que ocupan de los detalles de estas solicitudes, o puede implementar la lógica de autenticación diseñando solicitudes de estos extremos:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Aplicaciones Web
Para aplicaciones web (. NET, PHP, Java, Ruby, Python, nodo, etcetera) que se tiene acceso a través de un explorador, puede realizar el usuario sesión usando [OpenID conectarse](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  En OpenID conectar la aplicación web recibe una `id_token`, un token de seguridad que comprueba la identidad del usuario y proporciona información sobre el usuario en el formulario de Reclamación:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Encontrará información sobre todos los tipos de símbolos y notificaciones disponibles para una aplicación en la [referencia de token de versión 2.0](active-directory-v2-tokens.md).

En aplicaciones web de servidor, el flujo de autenticación de inicio de sesión realiza estos pasos de alto niveles:

![Imagen de calles de aplicación Web](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

La validación de la id_token con una clave de firma pública recibida desde el extremo de la versión 2.0 es suficiente para garantizar la identidad del usuario y establecer una cookie de sesión que se pueden usar para identificar el usuario en las siguientes solicitudes de página.

Para ver este escenario en acción, pruebe uno de los ejemplos de código de inicio de sesión de aplicación web en la sección de [Introducción](active-directory-appmodel-v2-overview.md#getting-started) .

Además de sesión simple, una aplicación de servidor web también necesite obtener acceso a algún otro servicio web, como una API de REST.  En este caso, la aplicación de servidor web puede participar en un flujo combinado de OpenID conectar & OAuth 2.0, con el [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Este escenario está cubierto por debajo en el [tema de introducción WebAPI Web App](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>API de Web
Puede usar el punto final de la versión 2.0 para proteger los servicios de web, como API Web de REST de su aplicación.  En lugar de id_tokens y sesión cookies, las API de Web use access_tokens OAuth 2.0 para proteger sus datos y autenticar las solicitudes entrantes.  El llamador de una API de Web, anexa un access_token en el encabezado de autorización de una solicitud HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

La API de Web, a continuación, puede usar el access_token para comprobar la identidad del llamador API y extraer información acerca de la persona que llama de reclamaciones codificados en la access_token.  Encontrará información sobre todos los tipos de símbolos y notificaciones disponibles para una aplicación en la [referencia de token de versión 2.0](active-directory-v2-tokens.md).

API de Web puede conceder a los usuarios la capacidad de participar en o participar en salir de determinadas funcionalidades o datos al exponer permisos, también conocidos como [ámbitos](active-directory-v2-scopes.md).  Para que una aplicación llamada adquirir permiso para un ámbito, el usuario debe consentimiento para el ámbito durante un flujo.  El punto final de la versión 2.0 se ocupará de pida al usuario permisos y registrar los permisos en todos los access_tokens que recibe la API de Web.  Todo lo que necesita de la API de Web que preocuparse es validar el access_tokens que recibe en cada llamada y realizar las comprobaciones de autorización adecuada.

API de Web pueden recibir access_tokens de todos los tipos de aplicaciones, incluso única página aplicaciones, incluso otras API de Web, aplicaciones web de servidor, daemons del lado servidor, escritorio y aplicaciones móviles.  El flujo de alto nivel de autenticación de la api de web es la siguiente:

![Imagen de Web API calles](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Para obtener más información sobre authorization_codes, refresh_tokens y los pasos detallados de obtener access_tokens, obtenga información sobre el [protocolo OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Para obtener información sobre cómo proteger un web api con OAuth2 access_tokens, consulte los ejemplos de código de web api en la [sección de introducción](active-directory-appmodel-v2-overview.md#getting-started).


## <a name="mobile-and-native-apps"></a>Mobile y aplicaciones nativas
Aplicaciones instaladas en un dispositivo, como las aplicaciones móviles y de escritorio, a menudo necesitan tener acceso a servicios de back-end o las API de Web que se almacenan los datos y realizar varias funciones en nombre de un usuario.  Estas aplicaciones pueden agregar inicio de sesión y la autorización a los servicios de back-end mediante el [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols-oauth-code.md).  

En este flujo un la aplicación recibe una authorization_code desde el extremo de la versión 2.0 al inicio de sesión de usuario, que representa el permiso de la aplicación llamar a los servicios de back-end en nombre del usuario que ha iniciado sesión en este momento.  La aplicación, a continuación, puede intercambiar los authoriztion_code en segundo plano para un access_token OAuth 2.0 y un refresh_token.  La aplicación puede utilizar el access_token para autenticar a Web API en las solicitudes HTTP y puede usar el refresh_token para obtener access_tokens nuevo al expiren los más antiguos.

![Imagen de calles aplicación nativa](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Aplicaciones de la página (javascript)
Muchas aplicaciones moderna tienen una sola página aplicación (SPA) front-end principalmente escrito en javascript y con qué frecuencia utilizando marcos como AngularJS, Ember.js, Durandal, etcetera.  El extremo de la versión 2.0 de Azure AD es compatible con estas aplicaciones con el [Flujo de OAuth 2.0 implícitos](active-directory-v2-protocols-implicit.md).

En este flujo de la aplicación recibe tokens desde la versión 2.0 autorizar extremo directamente, sin tener que realizar cualquier intercambios de servidor a servidor back-end.  Esto permite colocan todos los lógica de autenticación y control de sesión para realizar por completo en el cliente de javascript, sin realizar redireccionamientos página adicional.

![Imagen de calles flujo implícita](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Para ver este escenario en acción, pruebe uno de los ejemplos de código de aplicación única página en la sección de [Introducción](active-directory-appmodel-v2-overview.md#getting-started) .

### <a name="daemonsserver-side-apps"></a>Aplicaciones de lado daemons cliente-servidor
Aplicaciones que contienen los procesos de ejecución larga o que funcionan sin la presencia de un usuario también necesitan una manera de acceder a los recursos protegidos, como las API de Web.  Estas aplicaciones pueden autenticar y obtener tokens con la identidad de la aplicación (en lugar de una identidad de usuario delegada) con el cliente de OAuth 2.0 flujo de credenciales.

En este flujo de la aplicación obtiene tokens interacción directa con el `/token` punto final:

![Imagen de daemon aplicación calles](../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Para crear una aplicación de daemon, consulte el documeenation de credenciales de cliente en la sección [Introducción](active-directory-appmodel-v2-overview.md#getting-started) o para [esta aplicación de ejemplo de .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="current-limitations"></a>Limitaciones actuales
Estos tipos de aplicaciones no son compatibles actualmente con el extremo de la versión 2.0, pero se encuentran en el plan.  Limitaciones adicionales y restricciones para el extremo de la versión 2.0 se describen en el [artículo de las limitaciones de versión 2.0](active-directory-v2-limitations.md).

### <a name="chained-web-apis-on-behalf-of"></a>Encadenado web API (en nombre de)
Muchos arquitecturas incluyen una API de Web que necesita llamar otra API Web descendentes, ambos protegidos por el extremo de la versión 2.0.  Este escenario es común en clientes nativos que tienen API Web back-end, que a su vez llama a un servicio de Microsoft Online como Office 365 o la API de gráfico.

Sea compatible con este escenario de Web API encadenado mediante la concesión de credenciales de portador de OAuth 2.0 Jwt, también conocida como el [Flujo de On-Behalf-Of](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow).  Sin embargo, el flujo en nombre de otra persona se implementa actualmente en el extremo de la versión 2.0.  Para ver cómo funciona este flujo de Azure AD disponible de forma general del servicio, consulte el [ejemplo en nombre de código en GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).
