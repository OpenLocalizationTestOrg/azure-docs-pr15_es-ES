<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Crear aplicaciones web mediante el uso de la implementación de Azure Active Directory del protocolo de autenticación OpenID conectarse."
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

# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active B2C de directorio: Flujo de código de autorización de OAuth 2.0

Concesión de código de autorización de OAuth 2.0 puede utilizarse en las aplicaciones que se instalan en un dispositivo para obtener acceso a los recursos protegidos, como las API de web. Mediante el uso de la implementación de Azure Active Directory (AD Azure) B2C de OAuth 2.0, puede agregar tareas de administración de identidad de inicio de sesión, inicio de sesión y otros a sus aplicaciones móviles y de escritorio. Esta guía es independiente del lenguaje. Describe cómo enviar y recibir mensajes HTTP sin usar una de nuestras bibliotecas de código abierto.

<!-- TODO: Need link to libraries -->

El flujo de código de autorización de OAuth 2.0 se describe en el [punto 4.1 de la especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749). Puede usarlo para realizar la autenticación y la autorización en la mayoría de los tipos de aplicación, incluidas [aplicaciones web](active-directory-b2c-apps.md#web-apps) e [instalado de forma nativa aplicaciones](active-directory-b2c-apps.md#mobile-and-native-apps). Le permite aplicaciones adquirir **access_tokens**, que se puede utilizar para tener acceso a los recursos están protegidos mediante un [servidor de autorización](active-directory-b2c-reference-protocols.md#the-basics)de forma segura.

Esta guía se centra en un determinado tipo de autorización código flujo--**clientes pública**de OAuth 2.0. Un cliente público es cualquier aplicación cliente que no es de confianza para mantener la integridad de una contraseña secreta de forma segura. Esto incluye aplicaciones móviles, aplicaciones de escritorio y prácticamente cualquier aplicación que se ejecuta en un dispositivo y necesita obtener access_tokens. Si desea agregar la administración de identidades para una aplicación web mediante Azure AD B2C, debe usar [OpenID conectar](active-directory-b2c-reference-oidc.md) en lugar de OAuth 2.0.

Azure AD B2C amplía el estándar que OAuth 2.0 fluye para hacer más sencillo de autenticación y autorización. Presenta el [**parámetro de la directiva**](active-directory-b2c-reference-policies.md), que le permite usar OAuth 2.0 para agregar experiencias de usuario a la aplicación, como suscripción, inicio de sesión y administración de perfiles. Aquí le mostraremos cómo utilizar directivas y OAuth 2.0 para implementar cada una de estas experiencias en sus aplicaciones nativas. También le mostraremos cómo obtener access_tokens para tener acceso a las API de web.

Las solicitudes HTTP de ejemplo siguiente utiliza nuestro directorio de ejemplo B2C, **fabrikamb2c.onmicrosoft.com**, así como nuestra aplicación de ejemplo y directivas. Está libre para probar las solicitudes a usted mismo con estos valores, o puede reemplazar con su propio.
Obtenga información sobre cómo [obtener su propio directorio, aplicación y directivas de B2C](#use-your-own-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. obtener un código de autorización
El flujo de código de autorización comienza con el cliente de dirigir el usuario a la `/authorize` punto final. Esta es la parte interactiva del flujo, donde el usuario tendrá realmente acción. En esta solicitud, el cliente indica los permisos que necesita para adquirir del usuario en el `scope` parámetro y la directiva de ejecución en el `p` parámetro. Tres ejemplos se proporcionan a continuación (con saltos de línea para mejorar la legibilidad), cada una con una directiva diferente.

#### <a name="use-a-sign-in-policy"></a>Utilizar una directiva de inicio de sesión

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utilizar una directiva de suscripción

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utilizar una directiva de Editar perfil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parámetro | ¿Obligatorio? | Descripción |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Obligatorio | El identificador de aplicación del [portal de Azure](https://portal.azure.com) asignados a la aplicación. |
| response_type | Obligatorio | El tipo de respuesta, debe incluir `code` para el flujo de código de autorización. |
| redirect_uri | Obligatorio | Redirect_uri de la aplicación, donde se pueden enviar y recibir por la aplicación de respuestas de autenticación. Debe coincidir exactamente con uno de los redirect_uris que registrados en el portal de la excepción de que ésta debe ser la dirección URL codificada. |
| ámbito | Obligatorio | Lista de ámbitos separados por espacios. Un valor único ámbito indica a Azure AD ambos de los permisos que se solicita. Con el identificador de cliente como el ámbito indica que su aplicación necesita un **token de acceso** que se pueden usar con su propio servicio o web API, representada por el mismo identificador de cliente.  La `offline_access` ámbito indica que la aplicación necesitará un **refresh_token** larga acceso a los recursos.  También puede usar el `openid` ámbito solicitar un **id_token** de Azure AD B2C. |
| response_mode | Se recomienda | El método que se debe utilizar para devolver la authorization_code resultante a la aplicación. Puede ser 'query', 'form_post' o 'fragmento'.
| estado | Se recomienda | Un valor incluido en la solicitud que se devolverá en la respuesta de token. Puede ser una cadena de cualquier contenido que desee. Normalmente se usa un valor único generado aleatoriamente para prevenir ataques de falsificación de solicitud entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, como la página estuvieran en o la directiva que se está ejecutando. |
| p | Obligatorio | La directiva que se ejecutará. Es el nombre de una directiva que se crea en su directorio B2C. El valor de nombre de la directiva debe comenzar con "b2c\_1\_". Más información acerca de las directivas de [marco de directivas Extensible](active-directory-b2c-reference-policies.md). |
| símbolo del sistema | Opcional | El tipo de interacción con el usuario que es necesario. El valor válido solo en este momento es 'login', que obliga al usuario que introduzca sus credenciales de dicha solicitud. Inicio de sesión único no tendrán efecto. |

En este momento, el usuario tendrá que completar el flujo de trabajo de la directiva. Esto puede implicar al usuario escribir su nombre de usuario y contraseña, iniciar sesión con una identidad social, suscribirse en el directorio, o cualquier otro número de pasos, dependiendo de cómo se define la directiva.

Después de que el usuario finalice la directiva, Azure AD devolverá una respuesta a su aplicación en el indicado `redirect_uri`, utilizando el método especificado en el `response_mode` parámetro. La respuesta será exactamente la misma para cada uno de los casos anteriores, independientemente de la directiva que se ha ejecutado.

Una respuesta correcta que usa `response_mode=query` el siguiente aspecto:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| código | La authorization_code que solicita la aplicación. La aplicación puede utilizar el código de autorización para solicitar un access_token para un recurso de destino. Authorization_codes son muy corta duración. Normalmente, que expiren después de unos 10 minutos. |
| estado | Vea la descripción completa de la tabla anterior. Si se incluye un parámetro de estado de la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |

También se pueden enviar respuestas de error a la `redirect_uri` para que puede controlar la aplicación correctamente:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Mensaje de error específico que ayudan a un desarrollador para identificar la causa de un error de autenticación. |
| estado | Vea la descripción completa de la primera tabla de esta sección. Si se incluye un parámetro de estado de la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores de estado de la solicitud y la respuesta son idénticos. |


## <a name="2-get-a-token"></a>2. obtener un símbolo
Ahora que ha adquirido una authorization_code, puede canjear la `code` un token al recurso deseado mediante el envío de un `POST` solicitar a los `/token` extremo. En Azure AD B2C, el único recurso que puede solicitar el token es su web de back-end de aplicación API. La convención que se utiliza para solicitar un token a sí mismo es usar el identificador de cliente de la aplicación como el ámbito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parámetro | ¿Obligatorio? | Descripción |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obligatorio | La directiva que se usó para adquirir el código de autorización. No puede utilizar una directiva diferente en esta solicitud. Tenga en cuenta que agregar este parámetro a la *cadena de consulta*, no en el cuerpo de la publicación. |
| client_id | Obligatorio | El identificador de aplicación del [portal de Azure](https://portal.azure.com) asignados a la aplicación. |
| grant_type | Obligatorio | El tipo de concesión, que debe ser `authorization_code` para el flujo de código de autorización. |
| ámbito | Más recomendadas | Lista de ámbitos separados por espacios. Un valor único ámbito indica a Azure AD ambos de los permisos que se solicita. Con el identificador de cliente como el ámbito indica que su aplicación necesita un **token de acceso** que se pueden usar con su propio servicio o web API, representada por el mismo identificador de cliente.  La `offline_access` ámbito indica que la aplicación necesitará un **refresh_token** larga acceso a los recursos.  También puede usar el `openid` ámbito solicitar un **id_token** de Azure AD B2C. |
| código | Obligatorio | La authorization_code que adquiere de forma en la primera fase del flujo de. |
| redirect_uri | Obligatorio | Redirect_uri de la aplicación en la que recibió el authorization_code. |

Una respuesta de token correcta tendrá un aspecto similar:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| not_before | La hora en que el token se considera válido, en vez de tiempo. |
| token_type | El valor de tipo de token. El único tipo que admite Azure AD es portador. |
| access_token | El token de Token JSON de Web (JWT) firmado solicitado. |
| ámbito | Los ámbitos de que el token es válido, que se pueden utilizar para el almacenamiento en caché de tokens para su uso posterior. |
| expires_in | La longitud de tiempo que el token no es válido (en segundos). |
| refresh_token | Un refresh_token OAuth 2.0. La aplicación puede utilizar este token para adquirir tokens adicionales después de que expire el actual. Refresh_tokens cuánto tiempo de vida y puede utilizarse para conservar el acceso a los recursos para largos períodos de tiempo. Para obtener más detalles, consulte la [referencia de token B2C](active-directory-b2c-reference-tokens.md). |

Respuestas de error tendrá un aspecto similar:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede utilizarse para clasificar los tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Mensaje de error específico que ayudan a un desarrollador para identificar la causa de un error de autenticación. |

## <a name="3-use-the-token"></a>3. use el token
Ahora que ha adquirido correctamente un `access_token`, puede usar el token de solicitudes para el servidor web API incluyendo en el `Authorization` encabezado:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. actualizar el token
Access & Id tokens son vida corta. Debe actualizarlos después de que expiren para poder acceder a los recursos. Puede hacerlo enviando otro `POST` solicitar a los `/token` extremo. Proporcione esta vez el `refresh_token` en lugar de la `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parámetro | ¿Obligatorio? | Descripción |
| ----------------------- | ------------------------------- | -------- |
| p | Obligatorio | La directiva que se usó para adquirir la refresh_token original. No puede utilizar una directiva diferente en esta solicitud. Tenga en cuenta que agregar este parámetro a la *cadena de consulta*, no en el cuerpo de la publicación. |
| client_id | Se recomienda | El identificador de aplicación del [portal de Azure](https://portal.azure.com) asignados a la aplicación. |
| grant_type | Obligatorio | El tipo de concesión, que debe ser `refresh_token` para esta fase de flujo de código de autorización. |
| ámbito | Se recomienda | Lista de ámbitos separados por espacios. Un valor único ámbito indica a Azure AD ambos de los permisos que se solicita. Con el identificador de cliente como el ámbito indica que su aplicación necesita un **token de acceso** que se pueden usar con su propio servicio o web API, representada por el mismo identificador de cliente.  La `offline_access` ámbito indica que la aplicación necesitará un **refresh_token** larga acceso a los recursos.  También puede usar el `openid` ámbito solicitar un **id_token** de Azure AD B2C. |
| redirect_uri | Opcional | Redirect_uri de la aplicación en la que recibió el authorization_code. |
| refresh_token | Obligatorio | El refresh_token original que ha adquirido en la segunda fase del flujo de. |

Una respuesta de token correcta tendrá un aspecto similar:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| not_before | La hora en que el token se considera válido, en vez de tiempo. |
| token_type | El valor de tipo de token. El único tipo que admite Azure AD es portador. |
| access_token | El token de Token JSON de Web (JWT) firmado solicitado. |
| ámbito | Los ámbitos de que el token es válido, que se pueden utilizar para el almacenamiento en caché de tokens para su uso posterior. |
| expires_in | La longitud de tiempo que el token no es válido (en segundos). |
| refresh_token | Un refresh_token OAuth 2.0. La aplicación puede utilizar este token para adquirir tokens adicionales después de que expire el actual. Refresh_tokens cuánto tiempo de vida y puede utilizarse para conservar el acceso a los recursos para largos períodos de tiempo. Para obtener más detalles, consulte la [referencia de token B2C](active-directory-b2c-reference-tokens.md). |

Respuestas de error tendrá un aspecto similar:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error de autenticación. |

## <a name="use-your-own-b2c-directory"></a>Utilizar su propio directorio B2C

Si desea probar estas solicitudes para usted mismo, debe realizar primero estos tres pasos y, a continuación, reemplace los valores de ejemplo anteriormente por su propio:

- [Crear un directorio B2C](active-directory-b2c-get-started.md) y use el nombre del directorio en las solicitudes.
- [Crear una aplicación](active-directory-b2c-app-registration.md) para obtener un identificador de la aplicación y un redirect_uri. Desea incluir un **cliente nativo** en su aplicación.
- [Crear las directivas](active-directory-b2c-reference-policies.md) para obtener los nombres de la directiva.
