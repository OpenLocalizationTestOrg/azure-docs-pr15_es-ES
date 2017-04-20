<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Cómo generar aplicaciones directamente con los protocolos compatibles con Azure Active Directory B2C."
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
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Protocolos de autenticación

B2C de Azure Active Directory (AD Azure) proporciona identidad como un servicio para las aplicaciones mediante el soporte de dos protocolos estándar: conectar OpenID y OAuth 2.0. El servicio es compatible con los estándares, pero los dos implementaciones de estos protocolos pueden tener ligeramente distinto.  La información de esta guía será útil si escribe el código directamente enviando y administración de solicitudes de HTTP, en lugar de utilizar una biblioteca de código abierto. Se recomienda que lea esta página antes de profundizar en los detalles de cada protocolo específico. Pero si ya está familiarizado con Azure AD B2C, puede ir directamente a [las guías de referencia de protocolo](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Conceptos básicos
Todas las aplicaciones que usa Azure AD B2C deben estar registrado en el directorio B2C en el [portal de Azure](https://portal.azure.com). El proceso de registro de la aplicación recopila y asigna algunos valores de la aplicación:

- **Identificador de la aplicación** que identifica la aplicación.
- Una **Redirección URI** o el **identificador de paquete** que se puede usar para dirigir respuestas a la aplicación.
- Algunos valores de escenarios específicos. Para obtener más información, obtenga información sobre [cómo registrar la aplicación](active-directory-b2c-app-registration.md).

Después de registrar la aplicación, se comunica con Azure AD enviando solicitudes al extremo versión 2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

En casi todos los flujos de OAuth y conectar OpenID cuatro partes están implicadas en el exchange:

![Funciones de OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- El **servidor de autorización** es el extremo de la versión 2.0 de Azure AD. Forma segura controladores cualquier aspecto relacionado con la información de usuario y de acceso. También trata las relaciones de confianza entre las partes de un flujo. Es responsable de comprobar la identidad del usuario, conceder y revocar el acceso a los recursos y emitir tokens. También conocido como es el proveedor de identidades.
- El **propietario del recurso** suele ser el usuario final. Es la parte que posee los datos y tiene la potencia para permitir el acceso a ese recurso o datos de terceros.
- El **cliente de OAuth** es la aplicación. Se identifica mediante su identificador de aplicación. Normalmente, es la parte que interactúan los usuarios finales. También se solicita tokens desde el servidor de autorización. El propietario del recurso debe concederle permiso de cliente para tener acceso al recurso.
- El **servidor de recursos** es donde residen el recurso o los datos. Confíe en el servidor de autorización para autenticar y autorizar al cliente de OAuth de forma segura. También se usa tokens de acceso de portador para asegurarse de que puede tener acceso a un recurso.

## <a name="policies"></a>Directivas de
Posiblemente, las directivas de Azure AD B2C son las características más importantes del servicio. Azure AD B2C extiende los protocolos estándares de OAuth 2.0 y conectar OpenID introduciendo directivas. Permitir a Azure AD B2C realizar mucho más sencillo de autenticación y la autorización. Directivas de describen completamente consumidor identidad la experiencia, incluida la suscripción, inicio de sesión y Editar perfil. Las directivas pueden definirse en una interfaz de usuario administrativo. Se pueden ejecutar mediante un parámetro de consulta especial en las solicitudes de autenticación de HTTP. Las directivas no son características estándar de OAuth 2.0 y conectar OpenID, por lo que debe tomar el tiempo entienda. Para obtener más información, consulte la [Guía de referencia de directiva de Azure AD B2C](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokens
La implementación de Azure AD B2C de OAuth 2.0 y conectar OpenID utiliza intensamente tokens portador, incluidos tokens portador que están representados como símbolos de web JSON (JWTs). Un token portador es un token de seguridad ligero que permite el acceso de "portador" a un recurso protegido. El portador es cualquiera de las partes que puede presentar el token. Azure AD debe autenticarse primero una fiesta para que pueda recibir un token portador. Pero, si no se realizan los pasos necesarios para proteger el token de transmisión y el almacenamiento, se puede interceptar y utilizar una de las partes no deseadas.

Algunos tokens de seguridad tienen un mecanismo integrado que evita que personas no autorizadas usarlas, pero tokens portador no tiene este mecanismo. Debe ser transporte en un canal seguro, como la seguridad de capa de transporte (HTTPS). Si un token portador se transmite fuera de un canal seguro, una fiesta malintencionada puede utilizar un ataque de intermediario para adquirir el token y usarlo para obtener acceso no autorizado a un recurso protegido. Se aplican los mismos principios de seguridad cuando tokens portador se almacenan o en caché para su uso posterior. Siempre asegúrese de que la aplicación se transmite y almacena los símbolos de portador de forma segura.

Por motivos de seguridad token portador adicionales, vea [RFC 6750 sección 5](http://tools.ietf.org/html/rfc6750).

Obtener más información sobre los diferentes tipos de tokens que se usan en Azure AD B2C está disponibles en [la referencia de token de Azure AD](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protocolos

Cuando esté listo para revisar algunas solicitudes de ejemplo, puede empezar con uno de los siguientes tutoriales. Cada uno de ellos corresponde a un escenario de autenticación determinado. Si necesita ayuda para determinar qué flujo es adecuado para usted, consulte [los tipos de aplicaciones que puede generar con Azure AD B2C](active-directory-b2c-apps.md).

- [Crear aplicaciones móviles y nativas mediante OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
- [Crear aplicaciones web mediante OpenID conectar](active-directory-b2c-reference-oidc.md)
