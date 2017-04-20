<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="Los tipos de aplicaciones que puede crear en el B2C de Azure Active Directory."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active B2C de directorio: Tipos de aplicaciones

B2C de Azure Active Directory (AD Azure) es compatible con la autenticación para una gran variedad de arquitecturas de aplicación moderna. Todos ellos se basan en los protocolos estándar [OAuth 2.0](active-directory-b2c-reference-protocols.md) o [OpenID conectarse](active-directory-b2c-reference-protocols.md). Este documento describe brevemente los tipos de aplicaciones que se pueden generar, independientemente de la plataforma o el idioma que prefiera. También le ayuda entender los escenarios de alto nivel antes de [empezar a crear aplicaciones](active-directory-b2c-overview.md#getting-started).

## <a name="the-basics"></a>Conceptos básicos
Todas las aplicaciones que usa Azure AD B2C deben estar registrada en el [directorio de B2C](active-directory-b2c-get-started.md) a través del [Portal de Azure](https://portal.azure.com/). El proceso de registro de la aplicación recopila y asigna algunos valores de la aplicación:

- **Identificador de la aplicación** que identifica la aplicación.
- **Redirigir URI** que puede usarse para dirigir respuestas a la aplicación.
- Otros valores específicos del escenario. Para obtener más detalles, obtenga información sobre cómo [registrar una aplicación](active-directory-b2c-app-registration.md).

Después de que se registra la aplicación, se comunica con Azure AD enviando solicitudes al extremo de la versión 2.0 de Azure AD:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Cada solicitud que se envía a Azure AD B2C especifica una **Directiva**. Una directiva controla el comportamiento de Azure AD. También puede usar estos extremos para crear un conjunto muy personalizable de experiencias de usuario. Directivas comunes incluyen suscripción, inicio de sesión y directivas de edición de perfil. Si no está familiarizado con las directivas, lea acerca de Azure AD B2C [marco de directivas extensible](active-directory-b2c-reference-policies.md) antes de continuar.

La interacción de cada aplicación con un extremo de la versión 2.0 sigue un modelo de alto nivel similar:

1. La aplicación dirige al usuario al extremo versión 2.0 para ejecutar una [Directiva](active-directory-b2c-reference-policies.md).
2. El usuario completa la directiva según la definición de la directiva.
4. La aplicación recibe algún tipo de token de seguridad desde el punto final de la versión 2.0.
5. La aplicación usa el token de seguridad para tener acceso a información protegida o un recurso protegido.
6. El servidor de recursos valida el token de seguridad para comprobar que se puede conceder acceso.
7. La aplicación actualiza periódicamente el token de seguridad.

<!-- TODO: Need a page for libraries to link to -->
Estos pasos pueden varía ligeramente en función del tipo de aplicación que está creando. Bibliotecas de código abierto pueden tratar los detalles por usted.

## <a name="web-apps"></a>Aplicaciones Web
Para las aplicaciones web (incluidos .NET, PHP, Java, Ruby, Python y Node.js) que están alojadas en un servidor y tiene acceso a través de un explorador, Azure AD B2C admite [OpenID conectar](active-directory-b2c-reference-protocols.md) para todas las experiencias de usuario. Esto incluye el inicio de sesión, suscripción y administración de perfiles. En la implementación de Azure AD B2C de OpenID Connect, la aplicación web inicia estas experiencias de usuario a través de las peticiones de autenticación Azure AD. El resultado de la solicitud es una `id_token`. Este token de seguridad representa la identidad del usuario. También proporciona información sobre el usuario en el formulario de Reclamación:

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

Más información sobre los tipos de tokens y notificaciones disponibles para una aplicación en la [referencia de token B2C](active-directory-b2c-reference-tokens.md).

En una aplicación web, cada ejecución de una [Directiva](active-directory-b2c-reference-policies.md) realiza estos pasos:

![Imagen de calles de aplicación Web](./media/active-directory-b2c-apps/webapp.png)

Validación de la `id_token` mediante una clave de firma pública recibidos de Azure AD es suficiente para verificar la identidad del usuario. También se establece una cookie de sesión que se pueden usar para identificar el usuario en las siguientes solicitudes de página.

Para ver este escenario en acción, pruebe uno de los ejemplos de código de inicio de sesión de aplicación web en nuestra [Introducción a la sección](active-directory-b2c-overview.md#getting-started).

Además de facilitar el inicio de sesión simple, una aplicación de servidor web también necesite obtener acceso a un servicio web de back-end. En este caso, la aplicación web puede realizar un ligeramente distintos [flujo OpenID conectarse](active-directory-b2c-reference-oidc.md) y adquirir tokens con códigos de autorización y actualizar tokens. Este escenario se muestra en la siguiente [sección API de Web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>API de Web
Puede usar Azure AD B2C para proteger los servicios web como web de REST API de la aplicación. Web API puede utilizar OAuth 2.0 para proteger sus datos mediante la autenticación de las solicitudes entrantes de HTTP con tokens. El llamador de un sitio web API anexa un símbolo en el encabezado de autorización de una solicitud HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

La API de web, a continuación, puede usar el símbolo para verificar la identidad del llamador API y extraer información acerca de la persona que llama de reclamaciones codificados en el token. Más información sobre los tipos de tokens y notificaciones disponibles para una aplicación en la [referencia de token de Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
    Azure AD B2C admite actualmente solo web API que se tiene acceso a sus propios clientes conocidos. Por ejemplo, la aplicación completa puede incluir una aplicación de iOS, una aplicación Android y back-end web API. Esta arquitectura es totalmente compatible. Permitir que un cliente de partner, como otra aplicación de iOS tener acceso a la misma web que API no es compatible actualmente. Todos los componentes de la aplicación completa deben compartir un identificador de aplicación única.

Un sitio web API puede recibir tokens entre varios tipos de clientes, incluidas aplicaciones web, escritorio y aplicaciones móviles, única página aplicaciones, demonios de servidor y otra API de web. Aquí tiene un ejemplo del flujo completado para una aplicación web que llama a un sitio web API:

![Imagen de calles de Web API de aplicación Web](./media/active-directory-b2c-apps/webapi.png)

Para obtener más información sobre códigos de autorización, actualización tokens y los pasos para obtener tokens, obtenga información sobre el [protocolo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Para obtener información sobre cómo proteger un web API usando Azure AD B2C, consulte la web tutoriales API en nuestra [Introducción a la sección](active-directory-b2c-overview.md#getting-started).

## <a name="mobile-and-native-apps"></a>Mobile y aplicaciones nativas
Aplicaciones instaladas en dispositivos, como las aplicaciones móviles y de escritorio, a menudo necesitan tener acceso a servicios back-end o web API en nombre de los usuarios. Puede agregar experiencias de administración de identidades personalizadas a su nativas aplicaciones y servicios de back-end de llamada segura con Azure AD B2C y el [flujo de código de autorización de OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

En este flujo de la aplicación ejecuta [directivas](active-directory-b2c-reference-policies.md) y recibe un `authorization_code` de Azure AD después de que el usuario finalice la directiva. La `authorization_code` representa el permiso de la aplicación llamar a los servicios de back-end en nombre del usuario que actualmente ha iniciado sesión en. La aplicación, a continuación, puede intercambiar los `authorization_code` en el fondo de un `id_token` y un `refresh_token`.  Puede usar la aplicación de la `id_token` para autenticar a un sitio web de back-end API en las solicitudes HTTP. También puede usar el `refresh_token` para obtener un nuevo `id_token` cuando expira una más antigua.

> [AZURE.NOTE]
    Azure AD B2C admite actualmente solo los tokens que se usan para tener acceso a un servicio de web back-end de la aplicación. Por ejemplo, la aplicación completa puede incluir una aplicación de iOS, una aplicación Android y back-end web API. Esta arquitectura es totalmente compatible. Permitir que la aplicación obtener acceso a un sitio web del partner API mediante símbolos de acceso OAuth 2.0 no es compatible actualmente. Todos los componentes de la aplicación completa deben compartir un identificador de aplicación única.

![Imagen de calles aplicación nativa](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Limitaciones actuales
Azure AD B2C actualmente no admite los siguientes tipos de aplicaciones, pero se encuentran en la roadmapy. Restricciones de Azure AD B2C y limitaciones adicionales se describen en [las restricciones y limitaciones](active-directory-b2c-limitations.md).

### <a name="single-page-apps-javascript"></a>Aplicaciones de la página (JavaScript)
Muchas aplicaciones moderna tienen un aplicación de una sola página front-end escrito principalmente en JavaScript. Suelen usar un marco de trabajo como AngularJS, Ember.js o Durandal. Generalmente disponibles servicio de Azure AD admite estas aplicaciones mediante el flujo implícito OAuth 2.0. Sin embargo, este flujo no está disponible en Azure AD B2C.

### <a name="daemonsserver-side-apps"></a>Aplicaciones de daemons/servidor
Aplicaciones que contienen los procesos de ejecución larga o que funcionan sin la presencia de un usuario también necesitan una manera de acceder a recursos protegidos como web API. Estas aplicaciones pueden autenticar y obtener tokens mediante la identidad de la aplicación (en lugar de una identidad de usuario delegada) y mediante el cliente de OAuth 2.0 flujo de credenciales.

Azure AD B2C no admite actualmente este flujo. Estas aplicaciones pueden obtener tokens solo después de que se ha producido un flujo de usuario interactivo.

### <a name="web-api-chains-on-behalf-of-flow"></a>Cadenas de Web API (en nombre de flujo)
Muchos arquitecturas incluyen un web API que necesita llamar a otro nivel inferior web API, donde ambos están protegidos por Azure AD B2C. Este escenario es común en clientes nativos que tienen un API Web back-end. Esto, a continuación, llama a un servicio en línea de Microsoft como la API de Azure AD Graph.

Este escenario encadenados web API puede admitirán mediante la concesión de credencial portador de OAuth 2.0 JWT, también conocido como el flujo en nombre de.  Sin embargo, el flujo en nombre de otra persona se implementa actualmente en el B2C de Azure AD.
