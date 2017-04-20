<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Los tipos de símbolos emitidos en el B2C de Azure Active Directory."
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


# <a name="azure-ad-b2c-token-reference"></a>B2C de Azure AD: Referencia de Token

B2C de Azure Active Directory (AD Azure) emite varios tipos de tokens de seguridad como procesa cada [flujo de autenticación](active-directory-b2c-apps.md). Este documento describe el formato, las características de seguridad y contenido de cada tipo de token.

## <a name="types-of-tokens"></a>Tipos de símbolos

Azure AD B2C es compatible con el [Protocolo de autorización de OAuth 2.0](active-directory-b2c-reference-protocols.md), que utiliza tokens de acceso y tokens de actualización. También admite la autenticación e inicio de sesión a través de [OpenID conectarse](active-directory-b2c-reference-protocols.md), que presenta un tercer tipo de token: el token de ID. Cada uno de estos tokens se representa como un token portador.

Un token portador es un token de seguridad ligero que permite el acceso de "portador" a un recurso protegido. El portador es cualquiera de las partes que puede presentar el token. Azure AD debe autenticarse primero una fiesta para que pueda recibir un token portador. Pero, si no se realizan los pasos necesarios para proteger el token de transmisión y el almacenamiento, se puede interceptar y utilizar una de las partes no deseadas. Algunos tokens de seguridad tienen un mecanismo integrado para evitar que personas no autorizadas usarlas, pero tokens portador no tiene este mecanismo. Debe ser transporte en un canal seguro, como la seguridad de capa de transporte (HTTPS).

Si un token portador se transmite fuera de un canal seguro, una fiesta malintencionada puede utilizar un ataque de intermediario para adquirir el token y usarlo para obtener acceso no autorizado a un recurso protegido. Se aplican los mismos principios de seguridad cuando tokens portador se almacenan o en caché para su uso posterior. Siempre asegúrese de que la aplicación se transmite y almacena los símbolos de portador de forma segura.

Por motivos de seguridad adicional en tokens portador, vea [RFC 6750 sección 5](http://tools.ietf.org/html/rfc6750).

Muchos de los tokens que emite Azure AD B2C se implementan como símbolos de web JSON (JWTs). Una JWT es una forma compacta y seguridad de la dirección URL de la transferencia de información entre dos partes. JWTs contienen información que se conoce como notificaciones. Estos son aserciones de información sobre el portador y el asunto del token. Las notificaciones de JWTs son objetos JSON codificados y serie para la transmisión. Porque la JWTs emitidos por Azure AD B2C se ha iniciado sesión pero no cifrados, puede inspeccionar fácilmente el contenido de una JWT a depurar. Hay varias herramientas que puede hacerlo, incluyendo [calebb.net](http://calebb.net). Para obtener más información sobre JWTs, consulte [especificaciones JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Tokens de Id.

Un símbolo de ID es un formulario de token de seguridad que recibe la aplicación desde la B2C de Azure AD `authorize` y `token` extremos. Tokens ID están representados como [JWTs](#types-of-tokens)y contienen notificaciones que puede usar para identificar a los usuarios en la aplicación. Cuando se adquieren tokens de identificador de la `authorize` extremo, con qué frecuencia se utilizan para los usuarios de aplicaciones web de inicio de sesión. Cuando se adquieren tokens de identificador de la `token` extremo, se pueden enviar en solicitudes HTTP durante la comunicación entre dos componentes de la misma aplicación o servicio. Puede usar las notificaciones en un token de identificador como le convenga. Normalmente se utilizan para mostrar la información de cuenta o para tomar decisiones de control de acceso en una aplicación.  

Tokens ID iniciado sesión, pero no están cifrados en este momento. Cuando su aplicación o API recibe un símbolo de ID, debe [validar la firma](#token-validation) para demostrar que el token es auténtico. Su aplicación o API también debe validar unos notificaciones del token para demostrar que es válida. Dependiendo de los requisitos de escenario, pueden variar las notificaciones validadas por una aplicación, pero su aplicación debe realizar algunos [validaciones de Reclamación comunes](#token-validation) en todas las situaciones.

#### <a name="sample-id-token"></a>Símbolo de ID de ejemplo
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Símbolos de acceso

Un token de acceso también es una forma de token de seguridad que recibe la aplicación desde la B2C de Azure AD `authorize` y `token` extremos. Símbolos de acceso también se representan como [JWTs](#types-of-tokens)y contienen notificaciones que puede usar para identificar a los usuarios en los servicios web y las API.

Tokens de acceso se ha iniciado sesión, pero no son muy similares a los tokens id y cifrado en este momento.  Símbolos de acceso se deben utilizar para proporcionar acceso a servicios web y las API de y a identificar y autenticar al usuario en estos servicios.  Sin embargo, no proporcionan cualquier aserción de autorización de estos servicios.  Es decir que la `scp` reclamación en símbolos de acceso no limitar o en caso contrario, representan el acceso que se ha concedido el asunto del token.

Cuando la API recibe un token de acceso, debe [validar la firma](#token-validation) para demostrar que el token es auténtico. La API también debe validar unos notificaciones del token para demostrar que es válida. Dependiendo de los requisitos de escenario, pueden variar las notificaciones validadas por una aplicación, pero su aplicación debe realizar algunos [validaciones de Reclamación comunes](#token-validation) en todas las situaciones.

### <a name="claims-in-id--access-tokens"></a>Notificaciones de identificador y símbolos de acceso

Cuando utiliza Azure AD B2C, tiene poseen control sobre el contenido de los símbolos. Puede configurar [directivas](active-directory-b2c-reference-policies.md) para enviar ciertos conjuntos de datos de usuario de reclamaciones que requiere la aplicación para sus operaciones. Estas notificaciones pueden incluir propiedades estándar como el usuario `displayName` y `emailAddress`. También pueden incluir [atributos personalizados de usuario](active-directory-b2c-reference-custom-attr.md) que se pueden definir en su directorio B2C. Cada acceso a y el identificador de token que reciba contendrá un conjunto determinado de reclamaciones relacionadas con la seguridad. Las aplicaciones pueden usar estas notificaciones autentique los usuarios y las solicitudes de forma segura.

Tenga en cuenta que las notificaciones de tokens ID no se devuelven en un orden determinado. Además, pueden introducir notificaciones nuevos en tokens ID en cualquier momento. La aplicación no debe romper mientras se introducen notificaciones nuevos. Estas son las notificaciones que se esperan que exista en tokens ID y acceso emitidos por Azure AD B2C. Las reclamaciones adicionales vendrá determinados por las directivas. Como práctica, intente inspeccionar las notificaciones del token de identificador muestra pegándola en [calebb.net](http://calebb.net). Encontrará más detalles en la [especificación OpenID conectarse](http://openid.net/specs/openid-connect-core-1_0.html).

| Nombre | Reclamar | Valor de ejemplo | Descripción |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Audiencia | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Una notificación de audiencia identifica al destinatario del token. Azure AD B2C, la audiencia es el identificador de la aplicación de la aplicación como asignada a su aplicación en el portal de registro de la aplicación. La aplicación debería validar este valor y rechazar el token si no coincide. |
| Emisor | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Esta notificación identifica el servicio de token de seguridad (STS) que construye y devuelve el token. También identifica el directorio de Azure AD en el que el usuario fue autenticado. La aplicación debería validar la reclamación de emisor para asegurarse de que el token suministrado desde el extremo de la versión 2.0. |
| Emitido en | `iat` | `1438535543` | Esta notificación es el momento en el que fue emitido el token representado en tiempo de tiempo. |
| Fecha de caducidad | `exp` | `1438539443` | La hora de expiración de la reclamación es el momento en que el token es válido, se representan en tiempo de tiempo. La aplicación debería utilizar esta notificación para comprobar la validez de la duración del token.  |
| No antes | `nbf` | `1438535543` | Esta notificación es el momento en que el token es válida, representado en tiempo de tiempo. Esto suele ser el mismo que el tiempo que fue emitido el token. La aplicación debería utilizar esta notificación para comprobar la validez de la duración del token.  |
| Versión | `ver` | `1.0` | Esta es la versión del token de identificador definida por Azure AD. |
| Código hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hash de código está incluido en un símbolo de ID solo cuando haya emitido el token junto con un código de autorización de OAuth 2.0. Un hash código puede utilizarse para validar la autenticidad de un código de autorización. Vea la [especificación OpenID conectarse](http://openid.net/specs/openid-connect-core-1_0.html) para obtener más detalles sobre cómo llevar a cabo esta validación. |
| Hash de token de acceso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hash de token de acceso se incluye en un símbolo de ID solo cuando haya emitido el token junto con un símbolo de acceso OAuth 2.0. Puede usarse un hash de token de acceso para validar la autenticidad de un token de acceso. Vea la [especificación OpenID conectarse](http://openid.net/specs/openid-connect-core-1_0.html) para obtener más detalles sobre cómo llevar a cabo esta validación. |
| Valor de seguridad | `nonce` | `12345` | Un valor de seguridad es una estrategia usada para mitigar ataques de reproducción de token. La aplicación puede especificar un valor de seguridad en una solicitud de autorización mediante la `nonce` parámetro de consulta. Se va a emitir el valor que se proporciona en la solicitud sin modificar en la `nonce` reclamar de solo un símbolo de ID. Esto permite la aplicación para comprobar el valor con el valor que especifica en la solicitud, que se asocia la sesión de la aplicación con un símbolo de identificador proporcionado. La aplicación debe realizar esta validación durante el proceso de validación de token de ID. |
| Asunto | `sub` | `Not supported currently. Use oid claim.` | Esta es una entidad de seguridad acerca de que el token declara información, como el usuario de una aplicación. Este valor es inmutable y no se puede reasignar o volver a utilizar. Puede usar para realizar comprobaciones de autorización de forma segura, como cuando se utiliza el token de acceso a un recurso. Sin embargo, todavía no se ha implementado la reclamación de asunto en la B2C de Azure AD. Debe configurar las directivas para incluir el identificador de objeto `oid` reclamar y use el valor para identificar a los usuarios, en lugar de usar la reclamación de asunto para la autorización. |
| Referencia de clase de contexto de autenticación | `acr` | `b2c_1_sign_in` | Este es el nombre de la directiva que se usó para adquirir el token de ID.  |
| Tiempo de autenticación | `auth_time` | `1438535543` | Esta notificación es el momento en que un último especificado credenciales de usuario representados en tiempo de tiempo. |


### <a name="refresh-tokens"></a>Actualizar tokens

Actualizar los tokens son tokens de seguridad que puede usar la aplicación para adquirir nuevos tokens de identificador y acceso tokens en un flujo de OAuth 2.0. Proporcionan la aplicación con acceso a largo plazo a los recursos en nombre de los usuarios sin necesidad de interacción con los usuarios.

Para recibir una actualización token en una respuesta de token, la su aplicación debe solicitar la `offline_acesss` ámbito. Para obtener más información sobre la `offline_access` ámbito, consulte la [referencia de protocolo de Azure AD B2C](active-directory-b2c-reference-protocols.md).

Actualizar tokens y siempre estén, completamente opaco a su aplicación. Se emiten Azure AD y puedan inspeccionar y sólo interpreta Azure AD. Son larga, pero no se debe escribir la aplicación con la esperanza de un símbolo de actualización durará durante un período específico de tiempo. Tokens de actualización pueden ser no válidas en cualquier momento para una variedad de motivos. Es la única manera de la aplicación saber si un símbolo de actualización es válido intentar canjear realizando una solicitud de token a Azure AD.

Cuando se canjea un token de actualización para un nuevo token (y si la aplicación se ha concedido el `offline_access` ámbito), recibirá un nuevo token de actualización en la respuesta del token. Debe guardar el token de actualización recién emitido. Debería reemplazar el token de actualización que se ha usado previamente en la convocatoria. Esto ayuda a garantizar que los tokens actualización siguen siendo válidos tanto como sea posible.

## <a name="token-validation"></a>Validación de token

Para validar un token, la aplicación debe comprobar la firma y notificaciones del token.

Muchas bibliotecas Abrir origen están disponibles para validar JWTs, dependiendo de su idioma preferido. Se recomienda explorar las opciones en lugar de implementar su propia lógica de validación. La información de esta guía puede ayudarle a aprender a usar correctamente esas bibliotecas.

### <a name="validate-the-signature"></a>Validar la firma
Un JWT contiene tres segmentos, separados por el `.` caracteres. El primer segmento es el **encabezado**, el segundo es el **cuerpo**y la tercera es la **firma**. El segmento de la firma puede usarse para validar la autenticidad del token de modo que se puede confiar en la aplicación.

Tokens de Azure AD B2C firmados mediante algoritmos estándar de cifrado asimétricos, como RSA 256. El encabezado del token contiene información sobre el método de cifrado y de clave utilizado para firmar el token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

La `alg` reclamación indica el algoritmo que se usó para firmar el token. La `kid` reclamación indica la clave pública determinada que se usó para firmar el token.

En cualquier momento, Azure AD puede iniciar sesión un token con cualquiera de un conjunto determinado de pares de clave pública y privada. Azure AD gira el conjunto de claves posibles periódicamente, por lo que se debe escribir su aplicación para controlar los cambios claves automáticamente. Una frecuencia razonable para comprobar las actualizaciones a las claves públicas utilizadas Azure AD es cada 24 horas.

Azure AD B2C tiene un extremo de metadatos OpenID conectarse. Esto permite aplicaciones obtener información acerca de Azure AD B2C en tiempo de ejecución. Esta información incluye extremos, contenido token y token claves de firma. El directorio B2C contiene un documento de metadatos JSON para cada directiva. Por ejemplo, el documento de metadatos de la `b2c_1_sign_in` directiva en la `fabrikamb2c.onmicrosoft.com` se encuentra en:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`es el directorio de B2C utilizado para autenticar al usuario y `b2c_1_sign_in` es la directiva que se utiliza para adquirir el token. Para determinar qué directiva se usó para firmar un token (y dónde ir para capturar los metadatos), tiene dos opciones. En primer lugar, el nombre de la directiva se incluye en el `acr` reclamar en el token. Puede analizar reclamaciones fuera del cuerpo de la JWT base 64 descodificar el cuerpo y deserializar la cadena JSON resultante. La `acr` reclamación será el nombre de la directiva que se usó para emitir el token.  La otra opción consiste en codificar la directiva en el valor de la `state` parámetro cuando se emitió la petición y, a continuación, descodificar para determinar qué directiva se ha usado. Cualquiera de los métodos es válida.

El documento de metadatos es un objeto JSON que contiene varios elementos útiles de información. Incluyen la ubicación de los extremos necesarios para realizar la autenticación OpenID conectarse. También incluyen `jwks_uri`, que proporciona la ubicación del conjunto de las claves públicas que se utilizan para firmar tokens. Que aquí se proporciona la ubicación, pero es mejor capturar la ubicación dinámicamente con el documento de metadatos y analizar los `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

El documento JSON que se encuentra en esta dirección URL contiene toda la información de clave pública en uso en un momento determinado. Puede usar la aplicación de la `kid` reclamar en el encabezado JWT para seleccionar la clave pública en el documento JSON que se utiliza para iniciar un símbolo concreto. A continuación, puede realizar validación de firma con la clave pública correcta y el algoritmo de indicado.

Una descripción de cómo llevar a cabo la validación de firma está fuera del ámbito de este documento. Muchas bibliotecas Abrir origen están disponibles para ayudarle con este si lo necesita.

### <a name="validate-the-claims"></a>Validar las notificaciones
Cuando su aplicación o API recibe un símbolo de ID, también debe realizar varias comprobaciones frente a las notificaciones del token de ID. Estos incluyen, pero no están limitados a:

- La notificación de la **audiencia** : comprueba que el token de identificador estaba destinado a la aplicación.
- Las notificaciones **no antes** y **fecha de caducidad** : estos comprobar que no ha expirado el token de ID.
- La notificación de **emisor** : comprueba que Azure AD emitió el token a su aplicación.
- El **valor de seguridad**: se trata de una estrategia de mitigación de ataques de reproducción de token.

Para obtener una lista completa de validaciones que debe realizar la aplicación, consulte la [especificación OpenID conectarse](https://openid.net). Detalles de los valores esperados para estas notificaciones se incluyen en la [sección token](#types-of-tokens)de anterior.  

## <a name="token-lifetimes"></a>Duración de token

La duración de token siguiente se proporciona para obtener más información. Puede ayudarle al desarrollar y depurar aplicaciones. Tenga en cuenta que no se deben escribir las aplicaciones que va a pasar cualquiera de estas duraciones permanezca constante. Pueden y cambiará.  Puede obtener más información sobre la personalización de tokens duraciones en Azure AD B2C [aquí](active-directory-b2c-token-session-sso.md).

| Token | Duración | Descripción |
| ----------------------- | ------------------------------- | ------------ |
| Tokens de Id. | Una hora | Tokens ID son normalmente válidos para una hora. La aplicación web puede utilizar este período de duración para mantener sus propias sesiones con usuarios (recomendados). También puede elegir una duración de sesión diferente. Si su aplicación debe obtener un identificador nuevo token, simplemente debe realizar una nueva solicitud de inicio de sesión en Azure AD. Si un usuario tiene una sesión de explorador válidas con Azure AD, ese usuario no deberá volver a introducir las credenciales. |
| Actualizar tokens | Hasta 14 días | Un símbolo de actualización solo es válido para un máximo de 14 días. Sin embargo, un símbolo de actualización puede no ser válido en cualquier momento por varios motivos. La aplicación debería seguir intenta usar un símbolo de actualización hasta que se produce un error en la solicitud, o su aplicación reemplaza el token de actualización con uno nuevo.  Un símbolo de actualización también puede ser válido si ha pasado 90 días desde que el usuario escribió última credenciales. |
| Códigos de autorización | Cinco minutos | Códigos de autorización son deliberadamente corta duración. Debe canjear inmediatamente por tokens de acceso, tokens ID o tokens de actualización cuando se reciben. |
