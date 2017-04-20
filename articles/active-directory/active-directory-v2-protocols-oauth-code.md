

<properties
    pageTitle="Flujo de código de autorización de Azure AD versión 2.0 OAuth | Microsoft Azure"
    description="Creación de aplicaciones web mediante la implementación de Azure AD del protocolo de autenticación OAuth 2.0."
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
    ms.date="08/08/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>versión 2.0 protocolos: flujo de código de autorización de OAuth 2.0

Concesión de código de autorización de OAuth 2.0 puede utilizarse en las aplicaciones que se instalan en un dispositivo para obtener acceso a los recursos protegidos, como las API de web.  Implementación de versión 2.0 modelo aplicación de OAuth 2.0 puede agregar inicio de sesión y API de acceso a sus aplicaciones móviles y de escritorio.  Esta guía es independiente del idioma y describe cómo enviar y recibir mensajes HTTP sin usar una de nuestras bibliotecas de código abierto.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

El flujo de código de autorización de OAuth 2.0 se describe en el [punto 4.1 de la especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749).  Se usa para realizar la autenticación y la autorización en la mayoría de los tipos de aplicación, incluidas [aplicaciones web](active-directory-v2-flows.md#web-apps) e [instalado de forma nativa aplicaciones](active-directory-v2-flows.md#mobile-and-native-apps).  Le permite aplicaciones adquirir access_tokens que se puede utilizar para tener acceso a los recursos que están protegidos con el extremo de la versión 2.0 de forma segura.  

## <a name="protocol-diagram"></a>Diagrama de protocolo
En un nivel alto, el flujo de autenticación completo para una aplicación nativa/mobile es un poco similar a esta:

![Flujo de código de autenticación de OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Solicitar un código de autorización
El flujo de código de autorización comienza con el cliente de dirigir el usuario a la `/authorize` punto final.  En esta solicitud, el cliente indica los permisos que necesita para adquirir del usuario:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] ¡Haga clic en el vínculo siguiente para ejecutar esta solicitud! Después de iniciar sesión, el explorador debe redirigirá a `https://localhost/myapp/` con un `code` en la barra de direcciones.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://Login.microsoftonline.com/Common/oauth2/v2.0/Authorize...</a>

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | Obligatorio | La `{tenant}` valor de la ruta de acceso de la solicitud de puede usarse para controlar quién puede iniciar sesión en la aplicación.  Los valores permitidos son `common`, `organizations`, `consumers`y los identificadores de inquilinos.  Para obtener más información, vea [conceptos básicos de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | Obligatorio | El identificador de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) asignado a la aplicación. |
| response_type | Obligatorio | Debe incluir `code` para el flujo de código de autorización. |
| redirect_uri | se recomienda | Redirect_uri de la aplicación, donde se pueden enviar y recibir por la aplicación de respuestas de autenticación.  Debe coincidir exactamente con uno de los redirect_uris registrado en el portal, excepto que debe ser la dirección url codificada.  Para las aplicaciones nativas y móviles, debe usar el valor predeterminado de `https://login.microsoftonline.com/common/oauth2/nativeclient`. |
| ámbito | Obligatorio | Lista de [ámbitos](active-directory-v2-scopes.md) que desea que el usuario Permitir separadas por espacios.  |
| response_mode | se recomienda | Especifica el método que debe usarse para enviar el reverso de token resultante a la aplicación.  Puede ser `query` o `form_post`.  |
| estado | se recomienda | Un valor incluido en la solicitud que se devolverá en la respuesta de token.  Puede ser una cadena de cualquier contenido que desee.  Normalmente se usa un valor único generado aleatoriamente para [prevenir ataques de falsificación de solicitud entre sitios](http://tools.ietf.org/html/rfc6749#section-10.12).  El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, por ejemplo, la página o estaban en la vista. |
| símbolo del sistema | opcional | Indica el tipo de interacción con el usuario que es necesario.  Los valores sólo es válidos en este momento son 'login', 'Ninguno' y 'consentimiento'.  `prompt=login`solicitará al usuario que introduzca sus credenciales en esa solicitud, negación inicio de sesión único en.  `prompt=none`es lo opuesto - se asegurará de que el usuario no se presenta con cualquier mensaje interactivo alguna.  Si no puede completar la solicitud mediante el inicio de sesión único en silencio, el extremo de la versión 2.0 devolverán un error.  `prompt=consent`se activará el cuadro de diálogo de consentimiento OAuth después de que el usuario inicia sesión, que pide al usuario para conceder permisos a la aplicación. |
| login_hint | opcional | Puede utilizarse para rellenar el campo de dirección de correo/nombre de usuario de inicio de sesión en la página para el usuario si conoce su nombre de usuario antes de tiempo.  Aplicaciones a menudo usará este parámetro durante la nueva autenticación, ya tiene extraído el nombre de usuario desde una anterior sesión utilizando la `preferred_username` reclamar. |
| domain_hint | opcional | Puede ser una de `consumers` o `organizations`.  Si se incluye, se omitirá el proceso de detección basada en correo electrónico que el usuario pasa por en el inicio de sesión de la versión 2.0 en la página, lo que lleva a una experiencia de usuario simplificada un poco más.  Aplicaciones a menudo usará este parámetro durante la nueva autenticación por extraer la `tid` de una sesión anterior.  Si el `tid` reclamar valor es `9188040d-6c67-4c5b-b112-36a304b66dad`, debe usar `domain_hint=consumers`.  En caso contrario, use `domain_hint=organizations`. |

En este momento, el usuario tendrá que introducir sus credenciales y completar la autenticación.  El punto final de la versión 2.0 también se asegurará de que el usuario ha aceptado los permisos que se indican en el `scope` parámetro de consulta.  Si el usuario no ha aceptado a cualquiera de estos permisos, le pedirá al usuario acepta los permisos necesarios.  Detalles de [permisos, consentimiento y aplicaciones de múltiples arrendatarios se proporcionan a continuación](active-directory-v2-scopes.md).

Una vez que el usuario se autentica y concede consentimiento, el extremo de la versión 2.0 devolverá una respuesta a su aplicación en el indicado `redirect_uri`, utilizando el método especificado en el `response_mode` parámetro.

#### <a name="successful-response"></a>Respuesta correcta
Una respuesta correcta usando `response_mode=query` el siguiente aspecto:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| código | La authorization_code que solicita la aplicación. La aplicación puede utilizar el código de autorización para solicitar un token de acceso para el recurso de destino.  Authorization_codes están muy corta duración, normalmente que expiren después de unos 10 minutos. |
| estado | Si se incluye un parámetro de estado de la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores de estado de la solicitud y respuesta son idénticos. |

#### <a name="error-response"></a>Respuesta de error
También se pueden enviar respuestas de error a la `redirect_uri` por lo que puede controlar la aplicación correctamente:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
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

## <a name="request-an-access-token"></a>Solicitar un token de acceso
Ahora que ha adquirido una authorization_code y tener permiso por el usuario, puede canjear la `code` para un `access_token` al recurso deseado, mediante el envío de un `POST` solicitud para la `/token` punto final:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] ¡Intente ejecutar esta solicitud en Postman! (No se olvide de reemplazar la `code`)     [ ![Ejecutar en Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------------- |
| inquilino | Obligatorio | La `{tenant}` valor de la ruta de acceso de la solicitud de puede usarse para controlar quién puede iniciar sesión en la aplicación.  Los valores permitidos son `common`, `organizations`, `consumers`y los identificadores de inquilinos.  Para obtener más información, vea [conceptos básicos de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | Obligatorio | El identificador de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) asignado a la aplicación. |
| grant_type | Obligatorio | Debe ser `authorization_code` para el flujo de código de autorización. |
| ámbito | Obligatorio | Lista de ámbitos separados por espacios.  Los ámbitos solicitados en este segmento deben ser equivalente a o un subconjunto de los ámbitos solicitado en el primer segmento.  Si los ámbitos especificados en esta solicitud abarcan varios servidores de recursos, el extremo de la versión 2.0 devolverá un símbolo para el recurso especificado en el primer ámbito.  Para obtener información más detallada de ámbitos, consulte [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md).  |
| código | Obligatorio | La authorization_code que adquiere de forma en la primera fase del flujo de.   |
| redirect_uri | Obligatorio | El mismo valor de redirect_uri que se usó para adquirir la authorization_code. |
| client_secret | necesaria para aplicaciones web | El secreto de aplicación que creó en el portal de registro de la aplicación de la aplicación.  No se debe utilizar en una aplicación nativa, porque client_secrets no se pueden almacenar confiable en dispositivos.  Se requiere para aplicaciones web y web API, que tienen la capacidad de almacenar los client_secret de forma segura en el servidor. |

#### <a name="successful-response"></a>Respuesta correcta
Una respuesta de token correcta tendrá un aspecto similar:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar al recurso protegido, como un sitio web API. |
| token_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es portador  |
| expires_in | ¿Cuánto tiempo el token de acceso es válido (en segundos). |
| ámbito | Los ámbitos que sea válido para el access_token. |
| refresh_token |  Un símbolo de actualización OAuth 2.0. La aplicación puede utilizar este token adquirir tokens de acceso adicionales después de que expire el token de acceso actual.  Refresh_tokens son larga y puede utilizarse para conservar el acceso a los recursos para largos períodos de tiempo.  Para obtener más detalles, consulte la [referencia de token de versión 2.0](active-directory-v2-tokens.md).  |
| ID_token | Un unsigned JSON Web Token (JWT). La base64Url de aplicación pueden descodificar los segmentos de este token solicitar información sobre el usuario que ha iniciado sesión en. La aplicación puede almacenar en caché los valores y mostrarlas, pero no debe confiar en ellas autorización o límites de seguridad de.  Para obtener más información sobre id_tokens, vea la [referencia de token de extremo de versión 2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Respuesta de error
Respuestas de error tendrá un aspecto similar:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error de autenticación.  |
| error_codes | Una lista de códigos de error específicos de STS diagnósticos de ayuda.  |
| marca de tiempo | La hora del error. |
| traza | Un identificador único para la solicitud de ayuda para diagnósticos.  |
| correlation_id | Un identificador único para la solicitud que puede ayudar a diagnósticos en componentes. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de error para errores de extremo de token

| Código de error | Descripción | Acción del cliente |
|------------|-------------|---------------|
| invalid_request | Error de protocolo, como un parámetro requerido que falta. | Corrija y vuelva a enviar la solicitud |
| invalid_grant | El código de autorización no es válido o ha expirado. | Pruebe una nueva solicitud para la `/authorize` punto final |
| unauthorized_client | El cliente autenticado no está autorizado para usar este tipo de concesión de autorización. | Normalmente, esto ocurre cuando la aplicación cliente no está registrada en Azure AD o no se agrega al inquilino de Azure AD del usuario. La aplicación puede solicitar al usuario con instrucciones para instalar la aplicación y agregarlo a Azure AD. |
| invalid_client | Error de autenticación de cliente. | Las credenciales del cliente no son válidas. Para corregir el error, el Administrador de la aplicación actualiza las credenciales. |
| unsupported_grant_type | El servidor de autorización no admite el tipo de concesión de autorización. | Cambiar el tipo de concesión de la solicitud. Este tipo de error debe aparecer solo durante el desarrollo y detectarse durante la prueba inicial. |
| invalid_resource | El recurso de destino no es válido, porque no existe, Azure AD no encuentra o no está configurado correctamente. | Esto indica que el recurso, si existe, no está configurado en el inquilino. La aplicación puede solicitar al usuario con instrucciones para instalar la aplicación y agregarlo a Azure AD. |
| interaction_required | La solicitud requiere la interacción del usuario. Por ejemplo, un paso de autenticación adicionales es necesario. | Vuelva a intentar la solicitud con el mismo recurso. |
| temporarily_unavailable | El servidor temporalmente está demasiado ocupado para controlar la solicitud. | Vuelva a intentar la solicitud. La aplicación cliente puede explicar al usuario que se ha retrasado su respuesta debido a un problema temporal.|

## <a name="use-the-access-token"></a>Usar el token de acceso
Ahora que ha adquirido correctamente un `access_token`, puede usar el token de solicitudes de Web API incluyendo en el `Authorization` encabezado:

> [AZURE.TIP] ¡Ejecutar esta solicitud en Postman! (Reemplazar el `Authorization` encabezado primera)     [ ![Ejecutar en Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Actualizar el token de acceso
Access_tokens tienen una duración breve y debe actualizar después de que expiren para seguir teniendo acceso a los recursos.  Puede hacerlo enviando otro `POST` solicitud para la `/token` extremo, esta vez que proporciona la `refresh_token` en lugar de la `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] ¡Intente ejecutar esta solicitud en Postman! (No se olvide de reemplazar la `refresh_token`)     [ ![Ejecutar en Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | -------- |
| inquilino | Obligatorio | La `{tenant}` valor de la ruta de acceso de la solicitud de puede usarse para controlar quién puede iniciar sesión en la aplicación.  Los valores permitidos son `common`, `organizations`, `consumers`y los identificadores de inquilinos.  Para obtener más información, vea [conceptos básicos de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | Obligatorio | El identificador de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) asignado a la aplicación. |
| grant_type | Obligatorio | Debe ser `refresh_token` para esta fase de flujo de código de autorización. |
| ámbito | Obligatorio | Lista de ámbitos separados por espacios.  Los ámbitos solicitados en este segmento deben ser equivalente a o un subconjunto de los ámbitos solicitado en el segmento de solicitud de authorization_code original.  Si los ámbitos especificados en esta solicitud abarcan varios servidores de recursos, el extremo de la versión 2.0 devolverá un símbolo para el recurso especificado en el primer ámbito.  Para obtener información más detallada de ámbitos, consulte [permisos, consentimiento y ámbitos](active-directory-v2-scopes.md).  |
| refresh_token | Obligatorio | La refresh_token que ha adquirido en la segunda fase del flujo de.   |
| redirect_uri | Obligatorio | El mismo valor de redirect_uri que se usó para adquirir la authorization_code. |
| client_secret | necesaria para aplicaciones web | El secreto de aplicación que creó en el portal de registro de la aplicación de la aplicación.  No se debe utilizar en una aplicación nativa, porque client_secrets no se pueden almacenar confiable en dispositivos.  Se requiere para aplicaciones web y web API, que tienen la capacidad de almacenar los client_secret de forma segura en el servidor. |

#### <a name="successful-response"></a>Respuesta correcta
Una respuesta de token correcta tendrá un aspecto similar:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar al recurso protegido, como un sitio web API. |
| token_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es portador  |
| expires_in | ¿Cuánto tiempo el token de acceso es válido (en segundos). |
| ámbito | Los ámbitos que sea válido para el access_token. |
| refresh_token |  Un nuevo símbolo de actualización de OAuth 2.0. Debe reemplazar el antiguo token de actualización con este token de actualización recién adquirida para garantizar su tokens actualización válidas tanto como sea posible.  |
| ID_token | Un unsigned JSON Web Token (JWT). La base64Url de aplicación pueden descodificar los segmentos de este token solicitar información sobre el usuario que ha iniciado sesión en. La aplicación puede almacenar en caché los valores y mostrarlas, pero no debe confiar en ellas autorización o límites de seguridad de.  Para obtener más información sobre id_tokens, vea la [referencia de token de extremo de versión 2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Respuesta de error
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error de autenticación.  |
| error_codes | Una lista de códigos de error específicos de STS diagnósticos de ayuda.  |
| marca de tiempo | La hora del error. |
| traza | Un identificador único para la solicitud de ayuda para diagnósticos.  |
| correlation_id | Un identificador único para la solicitud que puede ayudar a diagnósticos en componentes. |

Para obtener una descripción de los códigos de error y la acción de cliente recomendada, consulte [códigos de Error para errores de token de extremo](#error-codes-for-token-endpoint-errors).
