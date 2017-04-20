<properties
    pageTitle="Azure AD versión 2.0 protocolos | Microsoft Azure"
    description="Guía para los protocolos admitidos por el extremo de la versión 2.0 de Azure AD."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20--openid-connect"></a>versión 2.0 protocolos - OAuth 2.0 & OpenID conectar

El punto final de la versión 2.0 puede usar Azure AD para identidad como-servicio con protocolos estándar, OpenID conectarse y OAuth 2.0.  Mientras el servicio es compatible con los estándares, puede haber ciertas diferencias entre las dos implementaciones de estos protocolos.  Esta información será útil si decide escribir su código enviando directamente y administración de solicitudes HTTP o usar un 3 º fiesta Abrir biblioteca de orígenes, en lugar de utilizar una de nuestras bibliotecas de código abierto.
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Conceptos básicos
En casi todos los flujos de OAuth & OpenID conectar, hay cuatro partes en el exchange:

![Funciones de OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- El **Servidor de autorización** es el extremo de la versión 2.0.  Es responsable de garantizar la identidad del usuario, conceder y revocar el acceso a los recursos y emitir tokens.  También conocido como es el proveedor de identidades: controla consecuencias sobre la información del usuario, su acceso y las relaciones de confianza entre las partes de un flujo de forma segura.
- Normalmente, el **Propietario del recurso** es el usuario final.  Es la parte que posee los datos y a continuación, tiene la capacidad de permitir el acceso a ese recurso o datos de terceros.
- El **Cliente de OAuth** es la aplicación, identificada por su identificador de aplicación.  Normalmente, es la parte que el usuario final interactúa con y solicita tokens desde el servidor de autorización.  El cliente debe tener permiso de acceso al recurso por el propietario del recurso.
- El **Servidor de recursos** es donde residen el recurso o los datos.  Confía en el servidor de autorización para autenticar y autorizar al cliente de OAuth de forma segura y usa portador access_tokens para asegurarse de que puede tener acceso a un recurso.


## <a name="app-registration"></a>Registro de aplicación
Todas las aplicaciones que usa el punto final de la versión 2.0 tendrán que estar registrado en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) antes de que puede interactuar con OAuth o OpenID conectarse.  El proceso de registro de la aplicación se recopilar y asignar algunos valores de la aplicación:

- Un **Identificador de la aplicación** que identifica la aplicación
- Una **Redirección URI** o el **Identificador de paquete** que se puede usar para dirigir respuestas a la aplicación
- Algunos valores de escenarios específicos.

Para obtener más detalles, obtenga información sobre cómo [registrar una aplicación](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Extremos
Una vez registrado, la aplicación se comunica con Azure AD enviando solicitudes al extremo versión 2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Donde la `{tenant}` puede realizar uno de los cuatro valores diferentes:

| Valor | Descripción |
| ----------------------- | ------------------------------- |
| `common` | Permite a los usuarios con cuentas de trabajo o la escuela y de cuentas personales de Microsoft de Azure Active Directory para iniciar sesión en la aplicación. |
| `organizations` | Permite a solo los usuarios con cuentas de trabajo o la escuela de Azure Active Directory para iniciar sesión en la aplicación. |
| `consumers` | Permite a solo los usuarios con cuentas personales de Microsoft (MSA) para iniciar sesión en la aplicación. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`o`contoso.onmicrosoft.com` | Permite a solo los usuarios con cuentas de trabajo o la escuela de un determinado inquilino de Azure Active Directory para iniciar sesión en la aplicación.  Puede utilizarse el nombre de dominio descriptivo del inquilino de Azure AD o identificador de guid del inquilino.  |

Para obtener más información sobre cómo interactuar con estos extremos, elija un tipo de aplicación en particular a continuación.

## <a name="tokens"></a>Tokens
La implementación de la versión 2.0 de OAuth 2.0 y conectar OpenID emplean extensiva tokens portador, incluidos tokens portador representados como JWTs. Un token portador es un token de seguridad ligero que permite el acceso de "portador" a un recurso protegido. En este sentido, "portador" es cualquier parte que puede presentar el token. Aunque una fiesta primero debe autenticar con Azure AD para recibir el token portador, si no se realizan los pasos necesarios para proteger el token de transmisión y el almacenamiento, puede interceptar y utilizar una de las partes no deseadas. Mientras algunos tokens de seguridad tienen un mecanismo integrado para evitar que personas no autorizadas usarlos, tokens portador no tiene este mecanismo y deben transporte en un canal seguro como la seguridad de capa de transporte (HTTPS). Si se transmite una token portador, un hombre en el medio ataque puede ser usada por un usuario malintencionado para adquirir el token y usarlo para un acceso no autorizado a un recurso protegido. Se aplican los mismos principios de seguridad al almacenar o almacenamiento en caché tokens portador para su uso posterior. Siempre asegúrese de que la aplicación se transmite y almacena los símbolos de portador de forma segura. Para más consideraciones de seguridad en tokens portador, vea [RFC 6750 sección 5](http://tools.ietf.org/html/rfc6750).

Más detalles de los diferentes tipos de símbolos que se usan en el extremo de la versión 2.0 está disponible en [la referencia de token de extremo de versión 2.0](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protocolos

Si está listo para ver algunas solicitudes de ejemplo, empezar a usar uno de los debajo tutoriales.  Cada uno de ellos corresponde a un escenario de autenticación determinado.  Si necesita ayuda para determinar cuál es el flujo adecuado para usted, consulte [los tipos de aplicaciones que se pueden generar con la versión 2.0](active-directory-v2-flows.md).

- [Crear móvil y aplicación nativa con OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Crear Web conectan aplicaciones con identificador abierto](active-directory-v2-protocols-oidc.md)
- [Crear aplicaciones única página con el flujo implícitos OAuth 2.0](active-directory-v2-protocols-implicit.md)
- [Generar Daemons o procesos de lado del servidor con las credenciales de cliente de OAuth 2.0 flujo](active-directory-v2-protocols-oauth-client-creds.md)
- Obtener tokens en una API de Web con la OAuth 2.0 en nombre de flujo de (próximamente)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 
