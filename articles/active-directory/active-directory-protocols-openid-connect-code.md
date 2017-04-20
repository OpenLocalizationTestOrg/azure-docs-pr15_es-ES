<properties
    pageTitle="Información general sobre el protocolo de .NET Azure AD | Microsoft Azure"
    description="Este artículo describe cómo usar mensajes HTTP para autorizar el acceso a web API en su inquilino con Azure Active Directory y conectar OpenID y aplicaciones web."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizar el acceso a las aplicaciones web mediante OpenID conectarse y Azure Active Directory

[Conectar OpenID](http://openid.net/specs/openid-connect-core-1_0.html) es una capa de identidad simple que se basa en el protocolo OAuth 2.0. OAuth 2.0 define mecanismos para obtener y usar **tokens de access** para tener acceso a los recursos protegidos, pero no definen métodos estándares para proporcionar información de identidad. Conectar OpenID implementa autenticación como una extensión del proceso de autorización de OAuth 2.0, que proporciona información sobre el usuario final en el formulario de un `id_token` que comprueba la identidad del usuario, así como proporciona información de perfil básica sobre el usuario.

Conectar OpenID es nuestra recomendación si está creando una aplicación web que está alojada en un servidor y tener acceso a través de un explorador.

## <a name="authentication-flow-using-openid-connect"></a>Flujo de autenticación mediante OpenID conectar

El flujo de inicio de sesión más básico contiene los siguientes pasos: cada uno de ellos se describe en detalle a continuación.

![OpenId conectar flujo de autenticación](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## <a name="send-the-sign-in-request"></a>Envíe la solicitud de inicio de sesión

Cuando la aplicación web necesita autenticar al usuario, debe dirigir al usuario a la `/authorize` punto final. Esta solicitud es similar al primer segmento del [Flujo de código de autorización de OAuth 2.0](active-directory-protocols-oauth-code.md), con algunas diferencias importantes:

- La solicitud debe incluir el ámbito `openid` en la `scope` parámetro.
- La `response_type` parámetro debe incluir `id_token`.
- La solicitud debe incluir la `nonce` parámetro.

Para que una solicitud de ejemplo tendría un aspecto similar a este:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | Obligatorio | La `{tenant}` valor de la ruta de acceso de la solicitud de puede usarse para controlar quién puede iniciar sesión en la aplicación.  Los valores permitidos son los identificadores de inquilinos, por ejemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` o `common` para independiente del inquilino tokens |
| client_id | Obligatorio | El identificador de aplicación asignado a su aplicación cuando se registra con Azure AD. Puede encontrar esto en el Portal de clásico de Azure. Haga clic en **Active Directory**, haga clic en el directorio, haga clic en la aplicación y, a continuación, haga clic en **Configurar** |
| response_type | Obligatorio | Debe incluir `id_token` conectar OpenID inicio de sesión de.  También puede incluir otros response_types, como por ejemplo `code`. |
| ámbito | Obligatorio | Lista de ámbitos separados por espacios.  Para conectar OpenID, debe incluir el ámbito `openid`, que se traduce en el permiso "Iniciar sesión" en la IU de consentimiento.  También puede incluir otros ámbitos en esta solicitud de solicitud de consentimiento. |
| valor de seguridad | Obligatorio | Un valor incluido en la solicitud, generada por la aplicación, que se incluirán en el resultado: `id_token` como una notificación.  La aplicación puede comprobar este valor para mitigar ataques de reproducción de token.  El valor suele ser una cadena aleatorio y único o GUID que se pueden usar para identificar el origen de la solicitud.  |
| redirect_uri | se recomienda | Redirect_uri de la aplicación, donde se pueden enviar y recibir por la aplicación de respuestas de autenticación.  Debe coincidir exactamente con uno de los redirect_uris registrado en el portal, excepto que debe ser la dirección url codificada. |
| response_mode | se recomienda | Especifica el método que se debe utilizar para devolver la authorization_code resultante a la aplicación.  Los valores admitidos son `form_post` para *Publicar formulario HTTP* o `fragment` para el *fragmento de dirección URL*.  Para las aplicaciones web se recomienda utilizar `response_mode=form_post` para garantizar la transferencia más segura de símbolos a la aplicación.  
| estado | se recomienda | Un valor incluido en la solicitud que se devolverá en la respuesta de token.  Puede ser una cadena de cualquier contenido que desee.  Normalmente se usa un valor único generado aleatoriamente para [prevenir ataques de falsificación de solicitud entre sitios](http://tools.ietf.org/html/rfc6749#section-10.12).  El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, por ejemplo, la página o estaban en la vista. |
| símbolo del sistema | opcional | Indica el tipo de interacción con el usuario que es necesario.  Los valores sólo es válidos en este momento son 'login', 'Ninguno' y 'consentimiento'.  `prompt=login`solicitará al usuario que introduzca sus credenciales en esa solicitud, negación inicio de sesión único en.  `prompt=none`es lo opuesto - se asegurará de que el usuario no se presenta con cualquier mensaje interactivo alguna.  Si no puede completar la solicitud mediante el inicio de sesión único en silencio, el extremo devolverán un error.  `prompt=consent`se activará el cuadro de diálogo de consentimiento OAuth después de que el usuario inicia sesión, que pide al usuario para conceder permisos a la aplicación. |
| login_hint | opcional | Puede utilizarse para rellenar el campo de dirección de correo/nombre de usuario de inicio de sesión en la página para el usuario si conoce su nombre de usuario antes de tiempo.  Aplicaciones a menudo usará este parámetro durante la nueva autenticación, ya tiene extraído el nombre de usuario desde una anterior sesión utilizando la `preferred_username` reclamar. |


En este momento, el usuario tendrá que introducir sus credenciales y completar la autenticación.

### <a name="sample-response"></a>Respuesta de ejemplo

Una respuesta de ejemplo, después de que el usuario se autentica, puede tener un aspecto similar a este:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| ID_token | La `id_token` que solicita la aplicación. Puede usar el `id_token` para comprobar la identidad del usuario y comenzar una sesión con el usuario.  |
| estado | Un valor incluido en la solicitud que se devolverá en la respuesta de token. Normalmente se usa un valor único generado aleatoriamente para [prevenir ataques de falsificación de solicitud entre sitios](http://tools.ietf.org/html/rfc6749#section-10.12).  El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, por ejemplo, la página o estaban en la vista. |

### <a name="error-response"></a>Respuesta de error
También se pueden enviar respuestas de error a la `redirect_uri` por lo que puede controlar la aplicación correctamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error de autenticación.  |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de error para errores de extremo de autorización

La siguiente tabla describe los distintos códigos de error que pueden devolver en el `error` parámetro de la respuesta de error.

| Código de error | Descripción | Acción del cliente |
|------------|-------------|---------------|
| invalid_request | Error de protocolo, como un parámetro requerido que falta. | Corrija y vuelva a enviar la solicitud. Este es un desarrollo normalmente se captura el error durante la prueba inicial.|
| unauthorized_client | No se permite la aplicación cliente para solicitar un código de autorización. | Normalmente, esto ocurre cuando la aplicación cliente no está registrada en Azure AD o no se agrega al inquilino de Azure AD del usuario. La aplicación puede solicitar al usuario con instrucciones para instalar la aplicación y agregarlo a Azure AD. |
| ACCESS_DENIED | Propietario de recurso denegado consentimiento | La aplicación cliente puede notificar al usuario que se no puede continuar a menos que el usuario. |
| unsupported_response_type | El servidor de autorización no admite el tipo de respuesta en la convocatoria. | Corrija y vuelva a enviar la solicitud. Este es un desarrollo normalmente se captura el error durante la prueba inicial.|
|server_error | El servidor ha encontrado un error inesperado. | Vuelva a intentar la solicitud. Estos errores pueden derivarse condiciones temporales. La aplicación cliente puede explicar al usuario que se ha retrasado su respuesta debido a un error temporal. |
| temporarily_unavailable | El servidor temporalmente está demasiado ocupado para controlar la solicitud. | Vuelva a intentar la solicitud. La aplicación cliente puede explicar al usuario que se ha retrasado su respuesta debido a un problema temporal. |
| invalid_resource |El recurso de destino no es válido, porque no existe, Azure AD no encuentra o no está configurado correctamente.| Esto indica que el recurso, si existe, no está configurado en el inquilino. La aplicación puede solicitar al usuario con instrucciones para instalar la aplicación y agregarlo a Azure AD. |

## <a name="validate-the-idtoken"></a>Validar la id_token

Acaba de recibir un `id_token` no es suficiente para autenticar al usuario; debe validar la firma y comprobar las notificaciones de la `id_token` por los requisitos de la aplicación. El extremo de Azure AD usa JSON Web Tokens (JWTs) y cifrado de clave pública para firmar tokens y compruebe que son válidos.

Puede elegir validar el `id_token` en cliente de código, pero frecuente es enviar la `id_token` en un servidor back-end y realizar la validación allí. Una vez haya validar la firma de la `id_token`, hay algunos reclamaciones deberá comprobar.

Puede que también desee validar reclamaciones adicionales dependiendo de su situación. Incluyen algunas validaciones comunes:

- Asegurarse de que la organización de usuario se dispone de la aplicación.
- Asegurarse de que el usuario tiene autorización y privilegios adecuados
- Se ha producido la garantizar una determinada intensidad de autenticación, como la autenticación multifactor.

Una vez que haya validada por completo el `id_token`, puede iniciar una sesión con el usuario y usar las notificaciones de la `id_token` para obtener información sobre el usuario en la aplicación. Esta información puede utilizarse para mostrar, registros, autorizaciones, etcetera. Para obtener más información acerca de los tipos de token y notificaciones, lea [Token compatibles y los tipos de notificación](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Enviar una solicitud de inicio de sesión

Cuando se desea firmar el usuario de la aplicación, no es suficiente para borrar las cookies o al final de lo contrario, la aplicación de la sesión con el usuario.  También debe redirigir al usuario a la `end_session_endpoint` para cerrar sesión.  Si no puede hacerlo, el usuario podrá volver a autenticarse a su aplicación sin introducir sus credenciales de nuevo, porque tienen una única sesión sesión válida con el extremo de Azure AD.

Simplemente puede redirigir al usuario a la `end_session_endpoint` que aparece en el documento de metadatos OpenID conectarse:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | se recomienda | La dirección URL que debe redirige al usuario después de cierre de sesión correcto.  Si no se incluye, el usuario se mostrará un mensaje genérico.  |

## <a name="token-acquisition"></a>Adquisición de token

Muchas aplicaciones web necesitan no sólo iniciar sesión el usuario en, pero también tener acceso a un servicio web en nombre del usuario mediante OAuth. Este escenario combina OpenID conectarse para la autenticación de usuario al adquirir simultáneamente un `authorization_code` que puede usarse para obtener `access_tokens` con el flujo de código de autorización de OAuth.

## <a name="get-access-tokens"></a>Obtener acceso Tokens

Para obtener acceso a tokens, debe modificar la solicitud de inicio de sesión desde arriba:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e      // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                   
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

Incluye los ámbitos de permiso de la solicitud y usando `response_type=code+id_token`, el `authorize` extremo se asegurará de que el usuario ha aceptado los permisos que se indican en el `scope` parámetro de la consulta y devolver un código de autorización de exchange para un token de acceso de la aplicación.

### <a name="successful-response"></a>Respuesta correcta

Una respuesta correcta usando `response_mode=form_post` el siguiente aspecto:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| ID_token | La `id_token` que solicita la aplicación. Puede usar el `id_token` para comprobar la identidad del usuario y comenzar una sesión con el usuario. |
| código | La authorization_code que solicita la aplicación. La aplicación puede utilizar el código de autorización para solicitar un token de acceso para el recurso de destino. Authorization_codes están muy corta duración, normalmente que expiren después de unos 10 minutos. |
| estado | Si se incluye un parámetro de estado de la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores de estado de la solicitud y respuesta son idénticos. |

### <a name="error-response"></a>Respuesta de error

También se pueden enviar respuestas de error a la `redirect_uri` por lo que puede controlar la aplicación correctamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error de autenticación.  |

Para obtener una descripción de los códigos de error posibles y su acción de cliente recomendada, consulte [códigos de Error para errores de extremo de autorización](#error-codes-for-authorization-endpoint-errors).

Una vez que haya recibido autorización `code` y un `id_token`, puede el usuario inicio de sesión y obtener acceso tokens en su nombre.  Para cerrar la sesión de usuario en, debe validar la `id_token` tal y como se describió anteriormente. Para obtener el token de acceso, puede seguir los pasos descritos en la [documentación de protocolo de OAuth](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token).
