<properties
    pageTitle="Flujo de Azure AD versión 2.0 implícito | Microsoft Azure"
    description="Creación de aplicaciones web con implementación de versión 2.0 de Azure AD implícito del flujo de la página aplicaciones de."
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

# <a name="v20-protocols---spas-using-the-implicit-flow"></a>versión 2.0 protocolos - SPAs usando el flujo implícito
Con el extremo de la versión 2.0, puede firmar los usuarios en sus aplicaciones única página con cuentas personales y de trabajo o la escuela de Microsoft.  Página individual y otras aplicaciones de JavaScript que se ejecutan principalmente en una cara de explorador algunas interesantes desafíos cuando se trata de autenticación:

- Las características de seguridad de estas aplicaciones son significativamente diferentes de las aplicaciones web basadas en el servidor tradicional.
- Muchos de los servidores de autorización y proveedores de identidades no admiten solicitudes CORS.
- Explorador de página completa redirige fuera de la aplicación se convierten en particular invasiva a la experiencia del usuario.

Para estas aplicaciones (pensar: AngularJS, Ember.js, React.js, etcetera) Azure AD es compatible con el flujo de OAuth 2.0 implícitos conceder.  El flujo implícito se describe en la [Especificación de OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2).  Su principal ventaja es que permite la aplicación obtener tokens de Azure AD sin realizar un servidor back-end intercambio de credenciales.  Esto permite la aplicación para el usuario de inicio de sesión, mantener la sesión y obtener tokens a otro web API del cliente de código JavaScript.  Existen algunas consideraciones de seguridad importantes a tener en cuenta cuando se usa el flujo implícito - específicamente alrededor de [cliente](http://tools.ietf.org/html/rfc6749#section-10.3) y la [suplantación de usuario](http://tools.ietf.org/html/rfc6749#section-10.3).

Si desea usar el flujo implícito y Azure AD para agregar autenticación para su aplicación de JavaScript, se recomienda que usar nuestra biblioteca de JavaScript de código abierto, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  Hay algunos tutoriales AngularJS [aquí](active-directory-appmodel-v2-overview.md#getting-started) para ayudarle a empezar.  

Sin embargo, si prefiere no utilizar una biblioteca en la aplicación de la página y envíe mensajes de protocolo, siga estos pasos generales.

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).
    
## <a name="protocol-diagram"></a>Diagrama de protocolo
El inicio de sesión implícito todo en flujo tiene un aspecto algo parecido a esto: cada uno de los pasos que se describen en detalle a continuación.

![OpenId conectar calles](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Envíe la solicitud de inicio de sesión

Para iniciar sesión inicialmente el usuario en la aplicación, puede enviar una solicitud de autorización [OpenID conectarse](active-directory-v2-protocols-oidc.md) y obtener un `id_token` desde el extremo de la versión 2.0:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] ¡Haga clic en el vínculo siguiente para ejecutar esta solicitud! Después de iniciar sesión, el explorador debe redirigirá a `https://localhost/myapp/` con un `id_token` en la barra de direcciones.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://Login.microsoftonline.com/Common/oauth2/v2.0/Authorize...</a>

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | Obligatorio | La `{tenant}` valor de la ruta de acceso de la solicitud de puede usarse para controlar quién puede iniciar sesión en la aplicación.  Los valores permitidos son `common`, `organizations`, `consumers`y los identificadores de inquilinos.  Para obtener más información, vea [conceptos básicos de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | Obligatorio | El identificador de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) asignado a la aplicación. |
| response_type | Obligatorio | Debe incluir `id_token` conectar OpenID inicio de sesión de.  También puede incluir la response_type `token`. Usando `token` aquí le permitirá su aplicación recibir un token de acceso inmediatamente desde el extremo de autorizar sin tener que realizar una segunda solicitud al extremo autorizar.  Si utiliza la `token` response_type, la `scope` parámetro debe contener un ámbito que indica qué recurso para emitir el token. |
| redirect_uri | se recomienda | Redirect_uri de la aplicación, donde se pueden enviar y recibir por la aplicación de respuestas de autenticación.  Debe coincidir exactamente con uno de los redirect_uris registrado en el portal, excepto que debe ser la dirección url codificada. |
| ámbito | Obligatorio | Lista de ámbitos separados por espacios.  Para conectar OpenID, debe incluir el ámbito `openid`, que se traduce en el permiso "Iniciar sesión" en la IU de consentimiento.  Opcionalmente, también puede incluir el `email` o `profile` [ámbitos](active-directory-v2-scopes.md) para obtener acceso a datos de usuario adicionales.  También puede incluir otros ámbitos en esta solicitud de solicitud de consentimiento a varios recursos.  |
| response_mode | se recomienda | Especifica el método que debe usarse para enviar el reverso de token resultante a la aplicación.  Debe ser `fragment` para el flujo de implícito.  |
| estado | se recomienda | Un valor incluido en la solicitud que se devolverá en la respuesta de token.  Puede ser una cadena de cualquier contenido que desee.  Normalmente se usa un valor único generado aleatoriamente para [prevenir ataques de falsificación de solicitud entre sitios](http://tools.ietf.org/html/rfc6749#section-10.12).  El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, por ejemplo, la página o estaban en la vista. |
| valor de seguridad | Obligatorio | Un valor incluido en la solicitud, generada por la aplicación, que se incluirán en la id_token resultante como una notificación.  La aplicación puede comprobar este valor para mitigar ataques de reproducción de token.  El valor suele ser una cadena aleatoria única que se pueden usar para identificar el origen de la solicitud.  |
| símbolo del sistema | opcional | Indica el tipo de interacción con el usuario que es necesario.  Los valores sólo es válidos en este momento son 'login', 'Ninguno' y 'consentimiento'.  `prompt=login`solicitará al usuario que introduzca sus credenciales en esa solicitud, negación inicio de sesión único en.  `prompt=none`es lo opuesto - se asegurará de que el usuario no se presenta con cualquier mensaje interactivo alguna.  Si no puede completar la solicitud mediante el inicio de sesión único en silencio, el extremo de la versión 2.0 devolverán un error.  `prompt=consent`se activará el cuadro de diálogo de consentimiento OAuth después de que el usuario inicia sesión, que pide al usuario para conceder permisos a la aplicación. |
| login_hint | opcional | Puede utilizarse para rellenar el campo de dirección de correo/nombre de usuario de inicio de sesión en la página para el usuario si conoce su nombre de usuario antes de tiempo.  Aplicaciones a menudo usará este parámetro durante la nueva autenticación, ya tiene extraído el nombre de usuario desde una anterior sesión utilizando la `preferred_username` reclamar. |
| domain_hint | opcional | Puede ser una de `consumers` o `organizations`.  Si se incluye, se omitirá el proceso de detección basada en correo electrónico que el usuario pasa por en el inicio de sesión de la versión 2.0 en la página, lo que lleva a una experiencia de usuario simplificada un poco más.  Aplicaciones a menudo usará este parámetro durante la nueva autenticación por extraer la `tid` reclamar desde el id_token.  Si el `tid` reclamar valor es `9188040d-6c67-4c5b-b112-36a304b66dad`, debe usar `domain_hint=consumers`.  En caso contrario, use `domain_hint=organizations`. |

En este momento, el usuario tendrá que introducir sus credenciales y completar la autenticación.  El punto final de la versión 2.0 también se asegurará de que el usuario ha aceptado los permisos que se indican en el `scope` parámetro de consulta.  Si el usuario no ha aceptado a cualquiera de estos permisos, le pedirá al usuario acepta los permisos necesarios.  Detalles de [permisos, consentimiento y aplicaciones de múltiples arrendatarios se proporcionan a continuación](active-directory-v2-scopes.md).

Una vez que el usuario se autentica y concede consentimiento, el extremo de la versión 2.0 devolverá una respuesta a su aplicación en el indicado `redirect_uri`, utilizando el método especificado en el `response_mode` parámetro.

#### <a name="successful-response"></a>Respuesta correcta

Una respuesta correcta usando `response_mode=fragment` y `response_type=id_token+token` el aspecto de las siguientes acciones, con saltos de línea para la legibilidad:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access_token | Incluir if `response_type` incluye `token`. El token de acceso que solicita la aplicación, en este caso de Microsoft Graph.  El token de acceso no se pueden descodificar o inspeccionen en caso contrario, se puede tratar como una cadena opaca. |
| token_type | Incluir if `response_type` incluye `token`.  Siempre será `Bearer`. |
| expires_in | Incluir if `response_type` incluye `token`.  Indica el número de segundos que el token es válido para el almacenamiento en caché. |
| ámbito | Incluir if `response_type` incluye `token`.  Indica los ámbitos para que sea válido el access_token. |
| ID_token | La id_token que solicita la aplicación. Puede usar el id_token para comprobar la identidad del usuario y comenzar una sesión con el usuario.  Obtener más detalles sobre id_tokens y su contenido se incluye en la [referencia de token de extremo de versión 2.0](active-directory-v2-tokens.md).  |
| estado | Si se incluye un parámetro de estado de la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores de estado de la solicitud y respuesta son idénticos. |


#### <a name="error-response"></a>Respuesta de error
También se pueden enviar respuestas de error a la `redirect_uri` por lo que puede controlar la aplicación correctamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error de autenticación.  |

## <a name="validate-the-idtoken"></a>Validar la id_token
Solo recibe una id_token no es suficiente para autenticar al usuario; debe validar firma de id_token y comprobar las notificaciones del token por los requisitos de la aplicación.  El extremo de la versión 2.0 usa [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) y cifrado de clave pública para firmar tokens y compruebe que son válidos.

Puede elegir validar el `id_token` en cliente de código, pero frecuente es enviar la `id_token` en un servidor back-end y realizar la validación allí.  Una vez haya valida la firma de la id_token, hay algunos reclamaciones que deberá comprobar.  Vea la [referencia de token de versión 2.0](active-directory-v2-tokens.md) para obtener más información, incluidos [Tokens validar](active-directory-v2-tokens.md#validating-tokens) e [Importante información sobre la firma clave dinámica](active-directory-v2-tokens.md#validating-tokens).  Se recomienda que hagan uso de una biblioteca para analizar y validar tokens - hay al menos una disponible para la mayoría de los idiomas y plataformas.
<!--TODO: Improve the information on this-->

Puede que también desee validar reclamaciones adicionales dependiendo de su situación.  Incluyen algunas validaciones comunes:

- Asegurarse de que la organización de usuario se dispone de la aplicación.
- Asegurarse de que el usuario tiene autorización y privilegios adecuados
- Se ha producido la garantizar una determinada intensidad de autenticación, como la autenticación multifactor.

Para obtener más información sobre las notificaciones en un id_token, vea la [referencia de token de extremo de versión 2.0](active-directory-v2-tokens.md).

Una vez por completo se valida el id_token, puede iniciar una sesión con el usuario y usar las notificaciones de la id_token para obtener información sobre el usuario en la aplicación.  Esta información puede utilizarse para mostrar, registros, autorizaciones, etcetera.

## <a name="get-access-tokens"></a>Obtener acceso tokens

Ahora que ha iniciado sesión el usuario en la aplicación de la página, puede obtener acceso tokens para web llamada API protegidas por Azure AD, como [Microsoft Graph](https://graph.microsoft.io).  Aunque ya ha recibido un token utilizando la `token` response_type, puede usar este método para adquirir tokens a recursos adicionales sin tener que redirigir al usuario que vuelva a iniciar sesión.

En el flujo de OpenID conectar/OAuth normal haría esto realizando una solicitud a la versión 2.0 `/token` punto final.  Sin embargo, el extremo de la versión 2.0 no admite las solicitudes CORS, para realizar llamadas de AJAX para obtener y actualizar tokens está fuera de la pregunta.  En su lugar, puede usar el flujo implícito en un iframe oculto obtener tokens nuevas para otra API de web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Intente copiar y pegar la siguiente solicitud en una pestaña del navegador! (No se olvide de reemplazar la `domain_hint` y la `login_hint` valores con los valores correctos para el usuario)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | Obligatorio | La `{tenant}` valor de la ruta de acceso de la solicitud de puede usarse para controlar quién puede iniciar sesión en la aplicación.  Los valores permitidos son `common`, `organizations`, `consumers`y los identificadores de inquilinos.  Para obtener más información, vea [conceptos básicos de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id | Obligatorio | El identificador de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) asignado a la aplicación. |
| response_type | Obligatorio | Debe incluir `id_token` conectar OpenID inicio de sesión de.  También puede incluir otros response_types, como por ejemplo `code`. |
| redirect_uri | se recomienda | Redirect_uri de la aplicación, donde se pueden enviar y recibir por la aplicación de respuestas de autenticación.  Debe coincidir exactamente con uno de los redirect_uris registrado en el portal, excepto que debe ser la dirección url codificada. |
| ámbito | Obligatorio | Lista de ámbitos separados por espacios.  Para obtener tokens, incluya todos los [ámbitos](active-directory-v2-scopes.md) requerir para el recurso de interés.  |
| response_mode | se recomienda | Especifica el método que debe usarse para enviar el reverso de token resultante a la aplicación.  Puede ser una de `query`, `form_post`, o `fragment`.  |
| estado | se recomienda | Un valor incluido en la solicitud que se devolverá en la respuesta de token.  Puede ser una cadena de cualquier contenido que desee.  Normalmente se usa un valor único generado aleatoriamente para prevenir ataques de falsificación de solicitud entre sitios.  El estado también se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, por ejemplo, la página o estaban en la vista. |
| valor de seguridad | Obligatorio | Un valor incluido en la solicitud, generada por la aplicación, que se incluirán en la id_token resultante como una notificación.  La aplicación puede comprobar este valor para mitigar ataques de reproducción de token.  El valor suele ser una cadena aleatoria única que se pueden usar para identificar el origen de la solicitud.  |
| símbolo del sistema | Obligatorio | Para actualizar y obtener tokens en un iframe oculto, debe usar `prompt=none` para asegurarse de que el iframe no se bloquea en la página de inicio de sesión de versión 2.0 y devuelve inmediatamente. |
| login_hint | Obligatorio | Para actualizar y obtener tokens en un iframe oculto, debe incluir el nombre del usuario en esta sugerencia para distinguir entre varias sesiones, que el usuario puede tener en un momento determinado en el tiempo. Puede extraer el nombre de usuario de un anterior sesión utilizando la `preferred_username` reclamar. |
| domain_hint | Obligatorio | Puede ser una de `consumers` o `organizations`.  Para actualizar y obtener tokens en un iframe oculto, debe incluir la domain_hint en la solicitud.  Debe extraer la `tid` reclamar desde el id_token de una sesión anterior para determinar qué valor desea usar.  Si el `tid` reclamar valor es `9188040d-6c67-4c5b-b112-36a304b66dad`, debe usar `domain_hint=consumers`.  En caso contrario, use `domain_hint=organizations`. |

Gracias a la `prompt=none` parámetro, esta solicitud, ya sea correcta o un error inmediatamente y volver a la aplicación.  Una respuesta correcta se enviarán a la aplicación en el indicado `redirect_uri`, utilizando el método especificado en el `response_mode` parámetro.

#### <a name="successful-response"></a>Respuesta correcta
Una respuesta correcta usando `response_mode=fragment` el siguiente aspecto:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access_token | El símbolo que solicita la aplicación. |
| token_type | Siempre será `Bearer`. |
| estado | Si se incluye un parámetro de estado de la solicitud, debería aparecer el mismo valor en la respuesta. La aplicación debe comprobar que los valores de estado de la solicitud y respuesta son idénticos. |
| expires_in | ¿Cuánto tiempo el token de acceso es válido (en segundos). |
| ámbito | Los ámbitos que sea válido para el token de acceso. |

#### <a name="error-response"></a>Respuesta de error
También se pueden enviar respuestas de error a la `redirect_uri` por lo que puede controlar la aplicación correctamente.  En el caso de `prompt=none`, un error esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error de autenticación.  |

Si recibe este error en la solicitud de iframe, el usuario debe interactiva iniciarla recuperar un nuevo símbolo.  Puede controlar este caso en la forma que tenga sentido para la aplicación.

## <a name="refreshing-tokens"></a>Actualización de tokens

Ambos `id_token`s y `access_token`s caduca después de un breve período de tiempo, por lo que su aplicación debe estar preparada para actualizar estos tokens periódicamente.  Para actualizar ambos tipos de token, puede realizar la misma solicitud de iframe ocultos desde utilizando la `prompt=none` parámetro para controlar el comportamiento de Azure AD.  Si desea recibir una nueva `id_token`, asegúrese de usar `response_type=id_token` y `scope=openid`, así como un `nonce` parámetro.


## <a name="send-a-sign-out-request"></a>Enviar una solicitud de inicio de sesión

La OpenIdConnect `end_session_endpoint` no se admite actualmente por el extremo de la versión 2.0. Esto significa que su aplicación no puede enviar una solicitud al extremo versión 2.0 para finalizar una sesión de usuario y borrar las cookies establecidas por el extremo de la versión 2.0.
Para cerrar la sesión de un usuario a la aplicación simplemente puede finalizar su propia sesión con el usuario y dejar la sesión del usuario con el extremo versión 2.0 en permanecen.  La próxima vez que el usuario intenta iniciar sesión, verán una página "Elija la cuenta", con sus cuentas conocidas ha iniciado sesión en que se muestran.
En la página, el usuario puede cerrar la sesión de cualquier cuenta, finalizar la sesión con el extremo de la versión 2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->