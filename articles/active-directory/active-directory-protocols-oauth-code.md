<properties
    pageTitle="Información general sobre el protocolo de .NET Azure AD | Microsoft Azure"
    description="Este artículo describe cómo usar mensajes HTTP para autorizar el acceso a web API en su inquilino con Azure Active Directory y OAuth 2.0 y aplicaciones web."
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


# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>Autorizar el acceso a las aplicaciones web mediante OAuth 2.0 y Azure Active Directory

Usos de Active Directory (AD Azure) Azure OAuth 2.0 para que pueda autorizar el acceso a web API de su inquilino de Azure AD y aplicaciones web. Esta guía es independiente del lenguaje y describe cómo enviar y recibir mensajes HTTP sin usar una de nuestras bibliotecas de código abierto.

El flujo de código de autorización de OAuth 2.0 se describe en el [punto 4.1 de la especificación de OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1) . Se usa para realizar la autenticación y la autorización en la mayoría de los tipos de aplicación, incluidas aplicaciones web e instalado de forma nativa aplicaciones.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## <a name="oauth-20-authorization-flow"></a>Flujo de autorización de OAuth 2.0

A un alto nivel, el flujo de toda autorización para una aplicación es algo similar a esta:

![Flujo de código de autenticación de OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## <a name="request-an-authorization-code"></a>Solicitar un código de autorización

El flujo de código de autorización comienza con el cliente de dirigir el usuario a la `/authorize` punto final. En esta solicitud, el cliente indica los permisos que necesita para adquirir del usuario. Puede obtener los extremos de OAuth 2.0 desde la página de la aplicación en Azure Portal clásica, en el botón **Extremos de vista** del cajón inferior.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | Obligatorio | La `{tenant}` valor de la ruta de acceso de la solicitud de puede usarse para controlar quién puede iniciar sesión en la aplicación.  Los valores permitidos son los identificadores de inquilinos, por ejemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` o `common` para independiente del inquilino tokens |
| client_id | Obligatorio | El identificador de aplicación asignado a su aplicación cuando se registra con Azure AD. Lo puede encontrar en el Portal de administración de Azure. Haga clic en **Active Directory**, haga clic en el directorio, haga clic en la aplicación y, a continuación, haga clic en **Configurar** |
| response_type | Obligatorio | Debe incluir `code` para el flujo de código de autorización. |
| redirect_uri | se recomienda | Redirect_uri de la aplicación, donde se pueden enviar y recibir por la aplicación de respuestas de autenticación.  Debe coincidir exactamente con uno de los redirect_uris registrado en el portal, excepto que debe ser la dirección url codificada.  Para las aplicaciones nativas y móviles, debe usar el valor predeterminado de `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode | se recomienda | Especifica el método que debe usarse para enviar el reverso de token resultante a la aplicación.  Puede ser `query` o `form_post`.  |
| estado | se recomienda | Un valor incluido en la solicitud que se devolverá en la respuesta de token. Normalmente se usa un valor único generado aleatoriamente para [prevenir ataques de falsificación de solicitud entre sitios](http://tools.ietf.org/html/rfc6749#section-10.12).  El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, por ejemplo, la página o estaban en la vista. |
| recursos | opcional | URI de identificador de aplicación de la web API (recurso seguro). Para buscar el URI de ID de aplicación de la web API, en el Portal de administración de Azure, haga clic en **Active Directory**, haga clic en el directorio, haga clic en la aplicación y, a continuación, haga clic en **Configurar**. |
| símbolo del sistema | opcional |  Indicar el tipo de interacción con el usuario que es necesario.<p> Los valores válidos son: <p> *Inicio de sesión*: el usuario se le solicitará que volver a autenticarse. <p> *consentimiento*: consentimiento del usuario se ha concedido, pero debe actualizarse. El usuario se solicitará al consentimiento. <p> *admin_consent*: un administrador le solicitará consentimiento en nombre de todos los usuarios de su organización |
| login_hint | opcional | Puede utilizarse para rellenar el campo de dirección de correo/nombre de usuario de inicio de sesión en la página para el usuario si conoce su nombre de usuario antes de tiempo.  Aplicaciones a menudo usará este parámetro durante la nueva autenticación, ya tiene extraído el nombre de usuario desde una anterior sesión utilizando la `preferred_username` reclamar. |
| domain_hint | opcional | Proporciona una sugerencia acerca del inquilino o dominio que el usuario debe utilizar para iniciar sesión. El valor de la domain_hint es un dominio registrado del inquilino. Si el inquilino se federa con un directorio local, AAD se redirige al servidor de federación inquilino especificado. |

> [AZURE.NOTE] Si el usuario es parte de una organización, un administrador de la organización puede consentimiento rechazar en nombre del usuario o permitir al usuario consentimiento. El usuario tiene la opción de consentimiento solo cuando lo permite el administrador.

En este momento, el usuario tendrá que escribir sus credenciales y consentimiento a los permisos que se indican en el `scope` parámetro de consulta. Una vez que el usuario se autentica y concede consentimiento, Azure AD envía una respuesta a su aplicación en la `redirect_uri` dirección en la convocatoria.

### <a name="successful-response"></a>Respuesta correcta

Una respuesta correcta podría tener este aspecto:

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parámetro | Descripción |
| -----------------------| --------------- |
| admin_consent | El valor es True si un administrador aceptado una solicitud de confirmación de solicitud de consentimiento.|
| código | El código de autorización que solicita la aplicación. La aplicación puede utilizar el código de autorización para solicitar un token de acceso para el recurso de destino. |
| session_state | Un valor único que identifica la sesión de usuario actual. Este valor es un GUID, pero se debe tratar como un valor opaco que se pasa sin examen. |
| estado | Si se incluye un parámetro de estado de la solicitud, debería aparecer el mismo valor en la respuesta. Es una buena práctica para la aplicación para comprobar que los valores de estado de la solicitud y respuesta son idénticos antes de utilizar la respuesta. Esto ayuda a detectar [ataques de falsificación de solicitud entre sitios (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) con el cliente.  

### <a name="error-response"></a>Respuesta de error

También se pueden enviar respuestas de error a la `redirect_uri` para que puede controlar la aplicación correctamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
|-----------|-------------|
| Error | Un valor de código de error definido en la sección 5.2 de [Marco de autorización de OAuth 2.0](http://tools.ietf.org/html/rfc6749). La tabla siguiente describe los códigos de error que devuelve Azure AD. |
| error_description | Una descripción más detallada del error. Este mensaje no está pensado para ser fáciles de usuario final. |
| estado | El valor de estado es un valor de no volver a utilizar generado aleatoriamente que se envían en la solicitud y devuelve en la respuesta a evitar ataques de falsificación (CSRF) solicitud entre sitios. |

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

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Usar el código de autorización para solicitar un token de acceso

Ahora que ha adquirido un código de autorización y tener permiso por el usuario, pueden introducir el código de un token de acceso al recurso deseado, mediante el envío de una solicitud de entrada para la `/token` punto final:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------------- |
| inquilino | Obligatorio |  La `{tenant}` valor de la ruta de acceso de la solicitud de puede usarse para controlar quién puede iniciar sesión en la aplicación.  Los valores permitidos son los identificadores de inquilinos, por ejemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` o `common` para independiente del inquilino tokens |
| client_id | Obligatorio | El identificador de aplicación asignado a su aplicación cuando se registra con Azure AD. Puede encontrar esto en el Portal de clásico de Azure. Haga clic en **Active Directory**, haga clic en el directorio, haga clic en la aplicación y, a continuación, haga clic en **Configurar** |
| grant_type | Obligatorio | Debe ser `authorization_code` para el flujo de código de autorización. |
| código | Obligatorio | La `authorization_code` que ha adquirido en la sección anterior   |
| redirect_uri | Obligatorio | El mismo `redirect_uri` que se usó para adquirir la `authorization_code`. |
| client_secret | necesaria para aplicaciones web | El secreto de aplicación que creó en el portal de registro de la aplicación de la aplicación.  No se debe utilizar en una aplicación nativa, porque client_secrets no se pueden almacenar confiable en dispositivos.  Es necesario para aplicaciones web y web API, que tienen la capacidad de almacenar los `client_secret` segura en el servidor. |
| recursos | requerido si se especifica en la solicitud de código de autorización, else opcional | URI de identificador de aplicación de la web API (recurso seguro).
Para buscar el URI de identificador de aplicación, en el Portal de administración de Azure, haga clic en **Active Directory**, haga clic en el directorio y, a continuación, haga clic en la aplicación y, a continuación, haga clic en **Configurar**.

### <a name="successful-response"></a>Respuesta correcta

Azure AD devuelve un token de acceso en una respuesta correcta. Para minimizar las llamadas de red de la aplicación cliente y su latencia asociada, la aplicación cliente debe caché tokens de acceso durante la duración del token que se especifica en la respuesta OAuth 2.0. Para determinar la duración del token, utilice la `expires_in` o `expires_on` valores de parámetro.

Si un recurso de la API de web devuelve un `invalid_token` código de error, es posible que el recurso ha determinado que haya caducado el token. Si los tiempos de reloj de cliente y recursos son diferentes (conocidos como un "desfase temporal"), el recurso podría considerar el token que ha caducado antes de que se desactiva el token de la caché del cliente. Si es así, desactive el token de la caché, incluso si está todavía dentro de su duración calculada.

Una respuesta correcta podría tener este aspecto:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar al recurso protegido, como un sitio web API. |
| token_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es portador. Para obtener más información sobre los tokens portador, consulte [OAuth2.0 autorización Framework: uso de Token portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)  |
| expires_in | ¿Cuánto tiempo el token de acceso es válido (en segundos). |
| expires_on | El tiempo cuando expira el token de acceso. El número de segundos que representa la fecha de 1970-01-01T0:0:0Z UTC hasta la fecha de expiración. Este valor se utiliza para determinar la duración de tokens en caché. |
| recursos | URI de identificador de aplicación de la web API (recurso seguro).|
| ámbito | Permisos de suplantación concedidos a la aplicación cliente. El permiso predeterminado es `user_impersonation`. El propietario del recurso protegido puede registrar valores adicionales de Azure AD.|
| refresh_token |  Un símbolo de actualización OAuth 2.0. La aplicación puede utilizar este token para adquirir tokens de acceso adicionales después de que expire el token de acceso actual.  Actualizar tokens son larga y puede utilizarse para conservar el acceso a los recursos para largos períodos de tiempo. |
| ID_token | Un unsigned JSON Web Token (JWT). La base64Url de aplicación pueden descodificar los segmentos de este token solicitar información sobre el usuario que ha iniciado sesión en. La aplicación puede almacenar en caché los valores y mostrarlas, pero no debe confiar en ellas autorización o límites de seguridad de. |

### <a name="jwt-token-claims"></a>Notificaciones de Token JWT
El token JWT en el valor de la `id_token` parámetro puede descodificar en las notificaciones siguientes:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

La `id_token` parámetro incluye los siguientes tipos de notificación. Para obtener más información sobre los tokens de web JSON, vea la [especificación de borrador IETF JWT](http://go.microsoft.com/fwlink/?LinkId=392344). Para obtener más información acerca de los tipos de token y notificaciones, lea [Token compatibles y los tipos de notificación](active-directory-token-and-claims.md)

| Tipo de notificación | Descripción |
|------------|-------------|
| y | Audiencia del token. Cuando se emite el token a una aplicación de cliente, la audiencia es la `client_id` del cliente.
| EXP | Hora de vencimiento. El tiempo cuando expira el token. Para que el token de ser válidos, la fecha y hora actuales debe ser menor o igual que el `exp` valor. La hora se representa como el número de segundos desde el 1 de enero de 1970 (1970-01-01T0:0:0Z) UTC hasta el momento fue emitido el token. |
| family_name | Del usuario último nombre o apellido. La aplicación puede mostrar este valor. |
| given_name | Nombre del usuario. La aplicación puede mostrar este valor. |
| tabla | Emitido al tiempo. El tiempo cuando fue emitido el JWT. La hora se representa como el número de segundos desde el 1 de enero de 1970 (1970-01-01T0:0:0Z) UTC hasta el momento fue emitido el token. |
| ISS | Identifica al emisor de tokens |
| NBF | No antes de tiempo. El tiempo que el token se hace efectivo. Para que el token de ser válidos, la fecha y hora actuales debe ser mayor o igual que el valor de Nbf. La hora se representa como el número de segundos desde el 1 de enero de 1970 (1970-01-01T0:0:0Z) UTC hasta el momento fue emitido el token. |
| OID | Identificador de objeto (ID) del objeto de usuario en Azure AD. |
| Sub | Identificador de token de asunto. Esto es un identificador persistente e inmutable para el usuario que describe el token. Usar este valor en la memoria caché o lógica. |
| TID | Identificador (ID) del inquilino Azure AD que emitir el token de inquilinos. |
| unique_name | Un identificador único para el se pueden mostrar al usuario. Suele ser un nombre principal de usuario (UPN). |
| UPN | Nombre principal de usuario del usuario. |
| ver | Versión. La versión del token JWT, normalmente 1.0. |

### <a name="error-response"></a>Respuesta de error

Los errores de extremo de emisión de token son códigos de error HTTP, porque el cliente llama el extremo de emisión de token directamente. Además del código de estado HTTP, el extremo de emisión de token de Azure AD también devuelve un documento JSON con objetos que describen el error.

Una respuesta de error de ejemplo podría tener este aspecto:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error de autenticación.  |
| error_codes | Una lista de códigos de error específicos de STS diagnósticos de ayuda. |
| marca de tiempo | La hora del error. |
| traza | Un identificador único para la solicitud de ayuda para diagnósticos.  |
| correlation_id | Un identificador único para la solicitud que puede ayudar a diagnósticos en componentes.|

#### <a name="http-status-codes"></a>Códigos de estado HTTP

La siguiente tabla enumeran los códigos de estado HTTP que devuelve el extremo de emisión de token. En algunos casos, el código de error es suficiente para describir la respuesta, pero si hay errores, deberá analizar el documento JSON y examinar el código de error.

| Código de HTTP | Descripción |
|-----------|-------------|
| 400       | Código HTTP predeterminado. Usados en la mayoría de los casos y suele ser debido a una solicitud incorrecta. Corrija y vuelva a enviar la solicitud. |
| 401       | Error de autenticación. Por ejemplo, la solicitud falta el parámetro client_secret.|
| 403       | Error de autorización. Por ejemplo, el usuario no tiene permiso para obtener acceso al recurso. |
| 500       | Ha ocurrido un error interno en el servicio. Vuelva a intentar la solicitud. |

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

## <a name="use-the-access-token-to-access-the-resource"></a>Usar el token de acceso para tener acceso al recurso

Ahora que ha adquirido correctamente un `access_token`, puede usar el token de solicitudes de Web API, incluyendo en el `Authorization` encabezado. La especificación de [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) explica cómo usar tokens portador en solicitudes HTTP para tener acceso a los recursos protegidos.

### <a name="sample-request"></a>Ejemplo de solicitud

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Respuesta de error

Recursos protegidos que implementan los códigos de estado HTTP RFC 6750 problema. Si la solicitud no incluye las credenciales de autenticación o falta el token, la respuesta incluye una `WWW-Authenticate` encabezado. Cuando se produce un error en una solicitud, el servidor de recursos responde con el código de estado HTTP y un código de error.

El siguiente es un ejemplo de una respuesta de éxito cuando la solicitud del cliente no incluye el token portador:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parámetros de error

| Parámetro | Descripción |
|-----------|-------------|
| authorization_uri | URI (extremo físico) del servidor de autorización. Este valor también se usa como una clave de búsqueda para obtener más información acerca del servidor de un extremo de detección. <p><p> El cliente debe validar que el servidor de autorización es de confianza. Cuando los recursos están protegidos por Azure AD, es suficiente comprobar que la dirección URL comienza con https://login.windows.net u otro nombre de host que admita Azure AD. Un recurso específico de inquilinos siempre debe devolver una autorización específica del inquilino URI. |
| Error | Un valor de código de error definido en la sección 5.2 de [Marco de autorización de OAuth 2.0](http://tools.ietf.org/html/rfc6749).|
| error_description | Una descripción más detallada del error. Este mensaje no está pensado para ser fáciles de usuario final.|
| resource_id | Devuelve el identificador único del recurso. La aplicación cliente puede utilizar este identificador como el valor de la `resource` parámetro cuando se solicita un token para el recurso. <p><p> Es muy importante para la aplicación cliente comprobar este valor, en caso contrario, un servicio malintencionado podrían provocar un ataque de **elevación de privilegios** <p><p> La estrategia recomendada para prevenir un ataque es comprobar que la `resource_id` coincide con la base de la dirección URL de la API de web que tiene acceso. Por ejemplo, si se tiene acceso a https://service.contoso.com/data, la `resource_id` puede ser htttps://service.contoso.com/. La aplicación cliente debe rechazar una `resource_id` que no empiezan con la dirección URL base a menos que hay una forma alternativa confiable para verificar el id. |

#### <a name="bearer-scheme-error-codes"></a>Códigos de error de combinación de portador

La especificación de RFC 6750 define los siguientes errores para los recursos que están usando el encabezado de autenticación WWW y el esquema de portador en la respuesta.

| Código de estado HTTP | Código de error | Descripción | Acción del cliente |
|------------------|------------|-------------|---------------|
| 400 | invalid_request | La solicitud no es correcto. Por ejemplo, podría ser falta un parámetro, o utilizar el mismo parámetro dos veces. | Corregir el error y vuelva a intentar la solicitud. Este tipo de error debe aparecer solo durante el desarrollo y detectarse en pruebas iniciales. |
| 401 | invalid_token   | El token de acceso es válida, falta o se revoca. El valor del parámetro error_description proporciona detalles adicionales. |  Solicitar un token nuevo desde el servidor de autorización. Si se produce un error en el nuevo token, ha ocurrido un error inesperado. Enviar un mensaje de error al usuario y vuelva a intentarlo después retrasos aleatorios. |
| 403 | insufficient_scope | El token de acceso no contiene los permisos de suplantación necesarios para tener acceso al recurso. | Enviar una nueva solicitud de autorización al extremo autorización. Si la respuesta contiene el parámetro scope, use el valor de ámbito de la solicitud al recurso. |
| 403 | insufficient_access | El asunto del token no tiene los permisos necesarios para tener acceso al recurso. | Preguntar al usuario para usar una cuenta diferente o para solicitar permisos para el recurso especificado. |

## <a name="refreshing-the-access-tokens"></a>Actualizar los identificadores de acceso

Símbolos de acceso son de corta duración y deben actualizarse después de que expiren para seguir teniendo acceso a los recursos. Puede actualizar el `access_token` enviando otro `POST` solicitar a la `/token` extremo, pero esta vez proporcionar la `refresh_token` en lugar de la `code`.

Actualizar tokens no tiene duración especificada. Normalmente, la duración de tokens de actualización es relativamente larga. Sin embargo, en algunos casos, actualizar tokens expiren, se ha caducado o carecen de privilegios suficientes para la acción que desee. La aplicación debe esperar y controlar los errores devueltos por el extremo de emisión de token correctamente.

Cuando reciba una respuesta a un error de token de actualización, descartar el token de actualización actual y solicitar un nuevo código de autorización o token de acceso. En particular, cuando usa una actualización token en el flujo de concesión de código de autorización, si recibe una respuesta con la `interaction_required` o `invalid_grant` códigos de error, descartar el token de actualización y solicitar un código de autorización de nuevo.

Un ejemplo de solicitud al extremo **específicas del inquilino** (también puede usar el extremo **comunes** ) para obtener un nuevo acceso token mediante un símbolo de actualización es similar a esta:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Parámetro | Descripción |
|-----------|-------------|
| access_token | El nuevo token de acceso que se ha solicitado.|
| expires_in   | Duración restante del token en segundos. Un valor típico es 3600 (una hora). |
| expires_on   | La fecha y hora en que expire el token. El número de segundos que representa la fecha de 1970-01-01T0:0:0Z UTC hasta la fecha de expiración. |
| refresh_token | Refresh_token de OAuth 2.0 nuevo que puede usarse para solicitar nuevos tokens de acceso cuando expira en esta respuesta. |
| recursos     | Identifica el recurso protegido que puede ser el token de acceso utilizado para el acceso. |
| ámbito        | Permisos de suplantación concedidos a la aplicación cliente nativa. El permiso predeterminado es **user_impersonation**. El propietario del recurso de destino puede registrar valores alternativos en Azure AD. |
| token_type   | El tipo de token. El único valor admitido es **portador**. |

### <a name="successful-response"></a>Respuesta correcta

Una respuesta de token correcta tendrá un aspecto similar:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### <a name="error-response"></a>Respuesta de error

Una respuesta de error de ejemplo podría tener este aspecto:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error de autenticación.  |
| error_codes | Una lista de códigos de error específicos de STS diagnósticos de ayuda. |
| marca de tiempo | La hora del error. |
| traza | Un identificador único para la solicitud de ayuda para diagnósticos.  |
| correlation_id | Un identificador único para la solicitud que puede ayudar a diagnósticos en componentes.|

Para obtener una descripción de los códigos de error y la acción de cliente recomendada, consulte [códigos de Error para errores de token de extremo](#error-codes-for-token-endpoint-errors).
