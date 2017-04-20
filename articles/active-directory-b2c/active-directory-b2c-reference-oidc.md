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

# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active B2C de directorio: Web iniciar sesión con OpenID conectar

Conectar OpenID es un protocolo de autenticación que se basa en OAuth 2.0, que se pueden usar para firmar de forma segura los usuarios en las aplicaciones web.  Mediante el uso de la implementación de Azure Active Directory (AD Azure) B2C de OpenID conectarse, puede delegar la suscripción, inicio de sesión y experiencias de otra la administración de identidades en las aplicaciones web a Azure AD. Esta guía le mostrará cómo hacerlo en forma independiente del idioma. Describe cómo enviar y recibir mensajes HTTP sin usar una de nuestras bibliotecas de código abierto.

[Conectar OpenID](http://openid.net/specs/openid-connect-core-1_0.html) amplía el protocolo de *autorización* de OAuth 2.0 para usar como protocolo de *autenticación* . Esto le permite realizar el inicio de sesión único mediante OAuth. Presenta el concepto de un `id_token`, que es un token de seguridad que permite al cliente comprobar la identidad del usuario y obtener información de perfil básica sobre el usuario.

Puesto que amplía OAuth 2.0, también permite aplicaciones adquirir **access_tokens**de forma segura. Puede usar access_tokens acceso a los recursos que están protegidos mediante un [servidor de autorización](active-directory-b2c-reference-protocols.md#the-basics). Se recomienda conectar OpenID si desea crear una aplicación web que está alojada en un servidor y tener acceso a través de un explorador. Si desea agregar la administración de identidades para las aplicaciones móviles o de escritorio con Azure AD B2C, debe usar [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) en lugar de OpenID conectarse.

B2C de Azure AD extiende el protocolo OpenID conectar estándar para hacer más sencillo de autenticación y autorización. Presenta el [**parámetro de la directiva**](active-directory-b2c-reference-policies.md), que le permite usar OpenID conectar agregar experiencias de usuario a la aplicación, como suscripción, inicio de sesión y administración de perfiles. Aquí le mostraremos cómo usar OpenID conectarse y directivas de implementar cada una de estas experiencias en las aplicaciones web. También le mostraremos cómo obtener access_tokens para tener acceso a las API de web.

Las solicitudes HTTP de ejemplo siguiente utiliza nuestro directorio de ejemplo B2C, **fabrikamb2c.onmicrosoft.com**, así como nuestro ejemplo aplicación **https://aadb2cplayground.azurewebsites.net** y directivas. Está libre para probar las solicitudes usted mismo con estos valores, o puede reemplazar con su propio.
Obtenga información sobre cómo [obtener su propio B2C inquilino, la aplicación y directivas](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Enviar solicitudes de autenticación
Cuando la aplicación web necesita autenticar al usuario y ejecutar una directiva, puede hacer que el usuario a la `/authorize` punto final. Esta es la parte interactiva de flujo, donde el usuario tendrá realmente acción, dependiendo de la directiva.

En esta solicitud, el cliente indica los permisos que necesita para adquirir del usuario en el `scope` parámetro y la directiva de ejecución en el `p` parámetro. Tres ejemplos se proporcionan a continuación (con saltos de línea para mejorar la legibilidad), cada una con una directiva diferente. Para hacerse una idea de cómo funciona cada solicitud, intente pegar la solicitud en un explorador y ejecutarlo.

#### <a name="use-a-sign-in-policy"></a>Utilizar una directiva de inicio de sesión

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utilizar una directiva de suscripción

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utilizar una directiva de Editar perfil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parámetro | ¿Obligatorio? | Descripción |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Obligatorio | El identificador de aplicación del [portal de Azure](https://portal.azure.com/) asignados a la aplicación. |
| response_type | Obligatorio | El tipo de respuesta, debe incluir `id_token` para conectar OpenID. Si la aplicación web también necesitará tokens para llamar a una API de web, puede usar `code+id_token`, tal y como hemos hecho aquí. |
| redirect_uri | Se recomienda | Redirect_uri de la aplicación, donde se pueden enviar y recibir por la aplicación de respuestas de autenticación. Debe coincidir exactamente con uno de los redirect_uris que registrados en el portal de la excepción de que ésta debe ser la dirección URL codificada. |
| ámbito | Obligatorio | Lista de ámbitos separados por espacios. Un valor único ámbito indica a Azure AD ambos de los permisos que se solicita. La `openid` ámbito indica un permiso para el usuario de inicio de sesión y obtener datos sobre el usuario en el formulario de **id_tokens** (más por llegar sobre esto más adelante en el artículo). La `offline_access` ámbito es opcional para las aplicaciones web. Indica que la aplicación necesitará un **refresh_token** larga acceso a los recursos. |
| response_mode | Se recomienda | El método que se debe utilizar para devolver la authorization_code resultante a la aplicación. Puede ser 'query', 'form_post' o 'fragmento'.  se recomienda 'form_post' para mejorar la seguridad. |
| estado | Se recomienda | Un valor incluido en la solicitud que se devolverá en la respuesta de token. Puede ser una cadena de cualquier contenido que desee. Normalmente se usa un valor único generado aleatoriamente para prevenir ataques de falsificación de solicitud entre sitios. El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, como la estuvieran en la página. |
| valor de seguridad | Obligatorio | Un valor incluido en la solicitud (generada por la aplicación) que se incluirán en la id_token resultante como una notificación. La aplicación puede comprobar este valor para mitigar ataques de reproducción de token. El valor suele ser una cadena aleatoria única que se pueden usar para identificar el origen de la solicitud. |
| p | Obligatorio | La directiva que se ejecutará. Es el nombre de una directiva que se crea en su inquilino B2C. El valor de nombre de la directiva debe comenzar con "b2c\_1\_". Más información acerca de las directivas de [marco de directivas Extensible](active-directory-b2c-reference-policies.md). |
| símbolo del sistema | Opcional | El tipo de interacción con el usuario que es necesario. El valor válido solo en este momento es 'login', que obliga al usuario que introduzca sus credenciales de dicha solicitud. Inicio de sesión único no tendrán efecto. |

En este momento, el usuario tendrá que completar el flujo de trabajo de la directiva. Esto puede implicar al usuario escribir su nombre de usuario y contraseña, iniciar sesión con una identidad social, suscribirse en el directorio, o cualquier otro número de pasos, dependiendo de cómo se define la directiva.

Después de que el usuario finalice la directiva, Azure AD devolverá una respuesta a su aplicación en el indicado `redirect_uri`, utilizando el método que se especifica en la `response_mode` parámetro. La respuesta será exactamente la misma para cada uno de los casos anteriores, independientemente de la directiva que se ha ejecutado.

Una respuesta correcta usando `response_mode=fragment` tendría el siguiente aspecto:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| ID_token | La id_token que solicita la aplicación. Puede usar el id_token para comprobar la identidad del usuario y comenzar una sesión con el usuario. Obtener más detalles sobre id_tokens y su contenido se incluyen en la [referencia de token de Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| código | La authorization_code que solicita la aplicación, si ha usado `response_type=code+id_token`. La aplicación puede utilizar el código de autorización para solicitar un access_token para un recurso de destino. Authorization_codes son muy corta duración. Normalmente, que expiren después de unos 10 minutos. |
| estado | Si se incluye un parámetro de estado de la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores de estado de la solicitud y respuesta son idénticos. |

También puede enviar respuestas de error a la `redirect_uri` para que puede controlar la aplicación correctamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error de autenticación. |
| estado | Vea la descripción completa de la tabla anterior. Si se incluye un parámetro de estado de la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores de estado de la solicitud y respuesta son idénticos. |


## <a name="validate-the-idtoken"></a>Validar la id_token
Solo recibe una id_token no es suficiente para autenticar al usuario, debe validar firma de id_token y comprobar las notificaciones en el token de acuerdo con los requisitos de la aplicación. Azure AD B2C utiliza [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) y cifrado de clave pública para firmar tokens y compruebe que son válidos.

Hay muchas bibliotecas de código abierto que están disponibles para validar JWTs, dependiendo del lenguaje de preferencia. Se recomienda explorar las opciones de implementación de lógica de validación. Esta información será útil saber cómo utilizar correctamente esas bibliotecas.

Azure AD B2C tiene un extremo de metadatos OpenID conectarse, lo que permite obtener información acerca de Azure AD B2C en tiempo de ejecución de una aplicación. Esta información incluye extremos, contenido token y token claves de firma. Hay un documento de metadatos JSON para cada directiva de su inquilino B2C. Por ejemplo, el documento de metadatos de la `b2c_1_sign_in` directiva en la `fabrikamb2c.onmicrosoft.com` se encuentra en:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Una de las propiedades de este documento configuración es el `jwks_uri`, cuyo valor de la misma directiva sería:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

En orden para determinar qué directiva se ha usado en una id_token de inicio de sesión (y dónde capturar los metadatos de), tiene dos opciones. En primer lugar, el nombre de la directiva se incluye en el `acr` reclamar en la id_token. Para obtener información sobre cómo analizar las notificaciones de un id_token, vea la [referencia de token de Azure AD B2C](active-directory-b2c-reference-tokens.md). La otra opción consiste en codificar la directiva en el valor de la `state` parámetro cuando se emitió la petición y, a continuación, descodificar para determinar qué directiva se ha usado. Cualquiera de los métodos es perfectamente válido.

Una vez que haya adquirido el documento de metadatos desde el extremo de metadatos OpenID conectarse, puede usar las claves públicas de RSA 256 (que se encuentran en este extremo) para validar la firma de la id_token. Puede haber varias teclas que se indican a este extremo en cualquier momento, cada una identificada por un `kid`. El encabezado de la id_token también contiene un `kid` reclamar, lo que indica que de estas teclas se usó para firmar el id_token. Vea la [referencia de token de Azure AD B2C](active-directory-b2c-reference-tokens.md) para obtener más información, las secciones en la [validación de tokens](active-directory-b2c-reference-tokens.md#validating-tokens) e [información importante sobre la sustitución de la clave de firma](active-directory-b2c-reference-tokens.md#validating-tokens).
<!--TODO: Improve the information on this-->

Una vez haya valida la firma de la id_token, hay varias reclamaciones que necesita comprobar, por ejemplo:

- Debe validar la `nonce` reclamar evitar que los ataques de reproducción de token. Su valor debe ser especificada en la solicitud de inicio de sesión.
- Debe validar la `aud` reclamar para asegurarse de que el id_token fue emitido para la aplicación. Su valor debe ser el identificador de aplicación de la aplicación.
- Debe validar la `iat` y `exp` notificaciones para asegurarse de que el id_token no ha expirado.

También hay varias validaciones más que debe realizar, se describe en detalle en la [Especificación de Core conectar OpenID](http://openid.net/specs/openid-connect-core-1_0.html).  También puede validar reclamaciones adicionales, dependiendo de su situación. Incluyen algunas validaciones comunes:

- Asegurarse de que la organización de usuario está suscrito a la aplicación.
- Asegurarse de que el usuario tiene privilegios y autorización adecuados.
- Asegurarse de que ha ocurrido una determinada intensidad de autenticación como la autenticación multifactor de Azure.

Para obtener más información sobre las notificaciones en un id_token, vea la [referencia de token de Azure AD B2C](active-directory-b2c-reference-tokens.md).

Después de completamente se valida el id_token, puede iniciar una sesión con el usuario y usar las notificaciones de la id_token para obtener información sobre el usuario en la aplicación. Esta información puede utilizarse para mostrar, registros, autorización y así sucesivamente.

## <a name="get-a-token"></a>Obtener un símbolo
Si todo lo que sus necesidades de la aplicación web hacer es ejecutar directivas, puede omitir las secciones siguientes. Estas secciones solo son aplicables a web aplicaciones que necesitan realizar autentican llamadas a un sitio web API y también están protegidas por Azure AD B2C.

Puede canjear la authorization_code que ha adquirido (usando `response_type=code+id_token`) un token al recurso deseado mediante el envío de un `POST` solicitar a los `/token` extremo. Actualmente, el único recurso que puede solicitar el token es su web de back-end de aplicación API. La convención para solicitar un token a sí mismo es usar el identificador de cliente de la aplicación como el ámbito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parámetro | ¿Obligatorio? | Descripción |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obligatorio | La directiva que se usó para adquirir el código de autorización. No puede utilizar una directiva diferente en esta solicitud. Tenga en cuenta que agregar este parámetro a la **cadena de consulta**, no en el cuerpo de la publicación. |
| client_id | Obligatorio | El identificador de aplicación del [portal de Azure](https://portal.azure.com/) asignados a la aplicación. |
| grant_type | Obligatorio | El tipo de concesión, que debe ser `authorization_code` para el flujo de código de autorización. |
| ámbito | Se recomienda | Lista de ámbitos separados por espacios. Un valor único ámbito indica a Azure AD ambos de los permisos que se solicita. La `openid` ámbito indica un permiso para el usuario de inicio de sesión y obtener datos sobre el usuario en el formulario de **id_tokens**. Puede usar para obtener tokens a su web de back-end de aplicación API, que se representa con el mismo ID de aplicación que el cliente. La `offline_access` ámbito indica que la aplicación necesitará un **refresh_token** larga acceso a los recursos. |
| código | Obligatorio | La authorization_code que adquiere de forma en la primera fase del flujo de. |
| redirect_uri | Obligatorio | Redirect_uri de la aplicación en la que recibió el authorization_code. |
| client_secret | Obligatorio | El secreto de aplicación que ha generado en el [portal de Azure](https://portal.azure.com/). Este secreto de aplicación es un efecto de seguridad importantes. Debe almacenar segura en su servidor. También debe procurar girar este secreto de cliente de forma periódica. |

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
| access_token | El token JWT firmado solicitado. |
| ámbito | Los ámbitos de que el token es válido, que se pueden utilizar para el almacenamiento en caché de tokens para su uso posterior. |
| expires_in | La longitud de tiempo que la access_token es válida (en segundos). |
| refresh_token | Un refresh_token OAuth 2.0. La aplicación puede utilizar este token para adquirir tokens adicionales después de que expire el actual.  Refresh_tokens cuánto tiempo de vida y puede utilizarse para conservar el acceso a los recursos para largos períodos de tiempo. Para obtener más detalles, consulte la [referencia de token B2C](active-directory-b2c-reference-tokens.md). Nota que debe haber utilizado el ámbito `offline_access` en la autorización y el tokens solicitudes para que reciban un refresh_token. |

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

## <a name="use-the-token"></a>Usar el símbolo
Ahora que ha adquirido correctamente un `access_token`, puede usar el token de solicitudes para el servidor web API incluyendo en el `Authorization` encabezado:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Actualizar el token
La id_tokens son vida corta. Debe actualizarlos después de que expiren para poder acceder a los recursos. Puede hacerlo enviando otro `POST` solicitar a los `/token` extremo. En este momento, proporcionar la `refresh_token` en lugar de la `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parámetro | Obligatorio | Descripción |
| ----------------------- | ------------------------------- | -------- |
| p | Obligatorio | La directiva que se usó para adquirir la refresh_token original. No puede utilizar una directiva diferente en esta solicitud. Tenga en cuenta que agregar este parámetro a la **cadena de consulta**, no en el cuerpo de la publicación. |
| client_id | Obligatorio | El identificador de aplicación del [portal de Azure](https://portal.azure.com/) asignados a la aplicación. |
| grant_type | Obligatorio | El tipo de concesión, que debe ser `refresh_token` para esta fase de flujo de código de autorización. |
| ámbito | Se recomienda | Lista de ámbitos separados por espacios. Un valor único ámbito indica a Azure AD ambos de los permisos que se solicita. La `openid` ámbito indica un permiso para el usuario de inicio de sesión y obtener datos sobre el usuario en el formulario de **id_tokens**. Puede usar para obtener tokens a su web de back-end de aplicación API, que se representa con el mismo ID de aplicación que el cliente. La `offline_access` ámbito indica que la aplicación necesitará un **refresh_token** larga acceso a los recursos. |
| redirect_uri | Se recomienda | Redirect_uri de la aplicación en la que recibió el authorization_code. |
| refresh_token | Obligatorio | El refresh_token original que ha adquirido en la segunda fase del flujo de. Nota que debe haber utilizado el ámbito `offline_access` en la autorización y el tokens solicitudes para que reciban un refresh_token. |
| client_secret | Obligatorio | El secreto de aplicación que ha generado en el [portal de Azure](https://portal.azure.com/). Este secreto de aplicación es un efecto de seguridad importantes. Debe almacenar segura en su servidor. También debe procurar girar este secreto de cliente de forma periódica. |

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
| access_token | El token JWT firmado solicitado. |
| ámbito | Los ámbitos de que el token es válido, que se pueden utilizar para el almacenamiento en caché de tokens para su uso posterior. |
| expires_in | La longitud de tiempo que la access_token es válida (en segundos). |
| refresh_token | Un refresh_token OAuth 2.0. La aplicación puede utilizar este token para adquirir tokens adicionales después de que expire el actual.  Refresh_tokens cuánto tiempo de vida y puede utilizarse para conservar el acceso a los recursos para largos períodos de tiempo. Para obtener más detalles, consulte la [referencia de token B2C](active-directory-b2c-reference-tokens.md). |

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


## <a name="send-a-sign-out-request"></a>Enviar una solicitud de cierre de sesión

Si desea firmar el usuario de la aplicación, no es suficiente para borrar las cookies o al final de lo contrario, la aplicación de la sesión con el usuario. También debe redirigir al usuario a Azure AD para cerrar la sesión. Si no puede hacerlo, es posible que el usuario puedo volver a autenticar a su aplicación sin introducir sus credenciales de nuevo. Esto es porque tienen una única sesión sesión válida con Azure AD.

Simplemente puede redirigir al usuario a la `end_session_endpoint` que aparece en el mismo documento metadatos OpenID conectar descrito en la sección anterior "Validar la id_token":

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parámetro | ¿Obligatorio? | Descripción |
| ----------------------- | ------------------------------- | ------------ |
| p | Obligatorio | La directiva que desea utilizar para iniciar sesión del usuario en la aplicación. |
| post_logout_redirect_uri | Se recomienda | La dirección URL que debe redirige al usuario después de éxito cerrar sesión. Si no está incluido, el usuario se mostrarán un mensaje genérico de Azure AD B2C.  |

> [AZURE.NOTE]
    Mientras que el usuario se dirige la `end_session_endpoint` se desactive algunos del estado del usuario único inicio de sesión con Azure AD B2C, no iniciar sesión del usuario en la sesión del usuario identidad social proveedor (IDP). Si el usuario selecciona el mismo IDP durante un inicio de sesión de las siguientes, le autenticarse, sin introducir sus credenciales. Si un usuario desea cerrar la sesión de la aplicación B2C, no necesariamente significa que desean cerrar la sesión de su cuenta de Facebook por completo. Sin embargo, en el caso de las cuentas locales, la sesión del usuario finalizará correctamente.

## <a name="use-your-own-b2c-tenant"></a>Usar a su propio inquilino B2C

Si desea probar estas solicitudes para usted mismo, debe realizar primero estos tres pasos y, a continuación, reemplace los valores de ejemplo anteriormente por su propio:

- [Crear un inquilino B2C](active-directory-b2c-get-started.md)y use el nombre de su inquilino en las solicitudes.
- [Crear una aplicación](active-directory-b2c-app-registration.md) para obtener un identificador de la aplicación y un redirect_uri. Es posible que desee incluir un **web app/web api** en su aplicación y, opcionalmente, crear un **secreto de aplicación**.
- [Crear las directivas](active-directory-b2c-reference-policies.md) para obtener los nombres de la directiva.
