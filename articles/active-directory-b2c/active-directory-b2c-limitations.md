<properties
    pageTitle="Azure Active B2C de directorio: Restricciones y limitaciones | Microsoft Azure"
    description="Una lista de restricciones de Azure Active Directory B2C y limitaciones"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure Active B2C de directorio: Restricciones y limitaciones

Existen diversas características y funcionalidades de Azure Active Directory (AD Azure) B2C que no son compatibles con. Muchos de estos problemas conocidos y limitaciones resolverá en el futuro, pero debe tener en cuenta de ellos si se crean aplicaciones para consumidores con Azure AD B2C.

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Problemas durante la creación de Azure AD B2C inquilinos

Si tiene problemas durante la [creación de un inquilino de Azure AD B2C](active-directory-b2c-get-started.md), vea [crear un inquilino de Azure AD o un inquilino de Azure AD B2C: problemas y soluciones](active-directory-b2c-support-create-directory.md) para obtener instrucciones.

Tenga en cuenta que existen conocidos problemas al eliminar a un inquilino B2C existente y volver a crearlo con el mismo nombre de dominio. Debe crear a un inquilino B2C con otro nombre de dominio.

## <a name="note-about-b2c-tenant-quotas"></a>Tenga en cuenta las cuotas de B2C inquilino

De forma predeterminada, el número de usuarios en un inquilino B2C se limita a 50.000 usuarios. Si necesita aumentar la cuota de su inquilino B2C, póngase en contacto con soporte técnico.

## <a name="branding-issues-on-verification-email"></a>Problemas de personalización de marca en correo electrónico de verificación

El correo electrónico de verificación de forma predeterminada contiene la marca de Microsoft. Se eliminará en el futuro. Por ahora, puede quitarlo mediante la [característica de personalización de marca de empresa](../active-directory/active-directory-add-company-branding.md).

## <a name="restrictions-on-applications"></a>Restricciones de aplicaciones

Los siguientes tipos de aplicaciones no son compatibles actualmente con Azure AD B2C. Para obtener una descripción de los tipos de aplicaciones compatibles, consulte [Azure AD B2C: tipos de aplicaciones](active-directory-b2c-apps.md).

### <a name="single-page-applications-javascript"></a>Aplicaciones de la página (JavaScript)

Muchas aplicaciones moderna tiene una sola página aplicación (SPA) front-end escrito principalmente en JavaScript y con qué frecuencia se usa un marco SPA como AngularJS, Ember.js, Durandal, etcetera. Este flujo aún no está disponible en Azure AD B2C.

### <a name="daemons--server-side-applications"></a>Daemons / servidor aplicaciones

Aplicaciones que contienen los procesos de ejecución larga o que funcionan sin la presencia de un usuario también necesitan una manera de acceder a los recursos protegidos, como las API de Web. Estas aplicaciones pueden autenticar y obtener tokens mediante la identidad de la aplicación (en lugar de identidad delegada del consumidor) en el [flujo de credenciales de cliente de OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Este flujo no está disponible en Azure AD B2C, para por ahora, las aplicaciones pueden obtener tokens solo después de que se ha producido un flujo de inicio de sesión de consumidor interactivo.

### <a name="standalone-web-apis"></a>API de Web independiente

En la AD B2C de Azure, tiene la capacidad para [crear una API de Web que se protegen con tokens OAuth 2.0](active-directory-b2c-apps.md#web-apis). Sin embargo, dicha API Web solo podrán recibir tokens desde un cliente que comparte la aplicación misma Id. No se admite la creación de una API de Web que se accede desde varios clientes diferentes.

### <a name="web-api-chains-on-behalf-of"></a>Cadenas de la API de Web (en nombre de)

Muchos arquitecturas incluyen una API de Web que necesita llamar otra API Web descendentes, ambos protegidos por Azure AD B2C. Este escenario es común en clientes nativos que tienen un API Web back-end, que a su vez llama a un servicio en línea de Microsoft como la API de Azure AD Graph.

Este escenario de Web API encadenada puede admitirán mediante la concesión de credenciales de portador de OAuth 2.0 Jwt, también conocida como el flujo en nombre de. Sin embargo, el flujo en nombre de otra persona se implementa actualmente en el B2C de Azure AD.

## <a name="restriction-on-libraries-and-sdks"></a>Restricción de bibliotecas y SDK

El conjunto de bibliotecas de Microsoft compatible que funcionan Azure AD B2C es muy limitado en este momento. Disponemos de soporte técnico para aplicaciones web de .NET según y servicios, así como NodeJS web aplicaciones y servicios.  También tenemos una biblioteca de cliente de vista previa .NET denominada MSAL que se pueden usar con Azure AD B2C en Windows y otras aplicaciones .NET.

No actualmente disponemos de biblioteca de soporte técnico de otros idiomas o las plataformas, incluidas iOS y Android.  Si desea crear en una plataforma diferente mencionado anteriormente, se recomienda usar un SDK de código abierto, hacer referencia a nuestro [OAuth 2.0 y la referencia de protocolo conectar OpenID](active-directory-b2c-reference-protocols.md) según sea necesario.  Azure AD B2C implementa OAuth & OpenID conectarse, lo que permite usar una biblioteca de OAuth o conectar OpenID genérica para la integración.

Nuestra iOS y Android inicio rápido utilizar bibliotecas de código abierto que hemos probado para la compatibilidad con Azure AD B2C.  Todos los tutoriales de inicio rápido están disponibles en la sección de [Introducción](active-directory-b2c-overview.md#getting-started) .

## <a name="restriction-on-protocols"></a>Restricción de protocolos

Azure AD B2C admite OpenID conectarse y OAuth 2.0. Sin embargo, no todas las características y las capacidades de cada protocolo se han implementado. Para entender mejor el ámbito de la funcionalidad de protocolo admitido en Azure AD B2C, lea nuestro [OpenID conectarse y la referencia de protocolo de OAuth 2.0](active-directory-b2c-reference-protocols.md). Compatibilidad con el protocolo SAML y WS Federal de tecnología no está disponible.

## <a name="restriction-on-tokens"></a>Restricción de tokens

Muchos de los símbolos de Azure AD B2C se implementan como JSON Web Tokens o JWTs. Sin embargo, no toda la información contenida en JWTs (conocidos como "reclamaciones") es bastante como debería o falta. Algunos ejemplos incluyen "sub" y las notificaciones de "preferred_username".  Como los valores, formato o significado de reclamaciones cambiar con el tiempo, tokens para las directivas existentes permanecen del mismo modo, puede confiar en sus valores en las aplicaciones de producción.  A medida que cambian los valores, se hará que la oportunidad de configurar esos cambios para cada una de las directivas.  Para entender mejor los tokens que emite actualmente por el servicio de Azure AD B2C, lea nuestro [token referencia](active-directory-b2c-reference-tokens.md).

## <a name="restriction-on-nested-groups"></a>Restricción de grupos anidados

Pertenencia a grupos anidados no es compatibles con los inquilinos de Azure AD B2C. No planea agregar esta capacidad.

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Restricción en función de la consulta diferencial en API de Azure AD Graph

La [característica de consulta diferencial en API de Azure AD gráfico](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) no se admiten en Azure AD B2C inquilinos. Esto es en nuestra guía a largo plazo.

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Problemas con la administración de usuarios en el portal de clásico de Azure

Características de B2C están accesibles en el portal de Azure. Sin embargo, puede usar el portal de clásico Azure para tener acceso a otras características del inquilino, incluida la administración de usuario. Actualmente, hay un par de problemas conocidos con la administración de usuarios (la ficha **usuarios** ) en el portal de clásica Azure:

- Para un usuario de la cuenta local (por ejemplo, un cliente que se inscribe con una dirección de correo electrónico y contraseña, o un nombre de usuario y contraseña), el campo **Nombre de usuario** no se corresponde con el inicio de sesión identificador (dirección de correo electrónico o nombre de usuario) que se usó durante la suscripción. Esto es porque el campo que se muestra en el portal de clásico Azure es realmente el usuario nombre Principal (UPN), que no se usa en escenarios B2C. Para ver el identificador de inicio de sesión de la cuenta local, busque el objeto de usuario en el [Explorador de gráfico](https://graphexplorer.cloudapp.net/). Encontrará el mismo problema con un usuario de la cuenta social (por ejemplo, un cliente que se inscribe con Facebook, Google +, etc.), pero en ese caso, no hay ningún identificador de inicio de sesión para hablar de.

    ![Cuenta local - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Para un usuario de la cuenta local, no podrá editar cualquiera de los campos y guardar los cambios en la ficha **perfil** .

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Problemas con contraseña iniciados por el administrador restablecer en el portal de clásico de Azure

Si restablecer la contraseña de un consumidor local basado en la cuenta en el portal de Azure clásico (el comando **Restablecer contraseña** en la ficha **usuarios** ), consumer no pueda cambiar su contraseña en el siguiente inicio de sesión, si utiliza un signo hacia arriba o iniciar sesión en la directiva y se bloqueará fuera de sus aplicaciones. Como solución alternativa, use la [API de Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) para restablecer la contraseña del consumidor (sin caducidad de contraseña) o utilizar una directiva en lugar de un inicio de sesión de inicio de sesión directiva de inicio de sesión.

## <a name="issues-with-creating-a-custom-attribute"></a>Problemas con la creación de un atributo personalizado

Un [atributo personalizado agregado en el portal de Azure](active-directory-b2c-reference-custom-attr.md) inmediatamente se ha creado en su inquilino B2C. Debe utilizar el atributo personalizado en al menos una de las directivas para se crean en su inquilino B2C y no estarán disponibles a través de la API de gráfico.

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Problemas al verificar un dominio en el portal de clásico de Azure

Actualmente no puede comprobar un dominio correctamente en el [portal de clásico de Azure](https://manage.windowsazure.com/).

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Problemas con el inicio de sesión con la directiva AMF en exploradores de Safari

Solicitudes de éxito de las directivas de inicio de sesión (con AMF activado) forma intermitente en los exploradores Safari con errores de HTTP 400 (solicitud incorrecta). Esto es debido a los límites de tamaño de cookie baja de Safari. Existen dos alternativas para solucionar este problema:

- Utilice la "suscripción o inicio de sesión de directiva" en lugar de "inicio de sesión de la directiva".
- Reduzca el número de **notificaciones de la aplicación** que se solicita en la directiva.
