<properties
    pageTitle="Azure AD versión 2.0 ámbitos, permisos y consentimiento | Microsoft Azure"
    description="Descripción de autorización en el extremo de la versión 2.0 de Azure AD, incluido ámbitos, permisos y consentimiento."
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
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="scopes-permissions--consent-in-the-v20-endpoint"></a>Ámbitos, permisos y consentimiento del extremo de la versión 2.0

Aplicaciones que se integran en Azure AD siguen un modelo de autorización especial que permite a los usuarios controlar cómo puede tener acceso a sus datos de una aplicación.  La implementación de la versión 2.0 de este modelo de autorización se ha actualizado, cambiar la forma en que una aplicación debe interactuar con Azure AD.  Este tema trata sobre los conceptos básicos de este modelo de autorización, incluidos los ámbitos, permisos y consentimiento.

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

## <a name="scopes--permissions"></a>Ámbitos y permisos

Azure AD implementa el protocolo de autorización de [OAuth 2.0](active-directory-v2-protocols.md) , que es un método para permitir una aplicación de terceros 3ª acceso a los recursos web hospedado en nombre de un usuario.  Cualquier recurso hospedado en web que se integra con Azure AD tendrá un identificador de recursos o **URI de ID de aplicación**.  Por ejemplo, algunos de los recursos de Microsoft hospedado en web son:

- Office 365 había unificada API de correo:`https://outlook.office.com`
- La API de gráfico de Azure AD:`https://graph.windows.net`
- Microsoft Graph:`https://graph.microsoft.com`

El mismo es true para todos los recursos parte 3ª ha integrado con Azure AD.  Cualquiera de estos recursos también puede definir un conjunto de permisos que se pueden usar para dividir la funcionalidad de ese recurso en partes más pequeñas.  Por ejemplo, Microsoft Graph ha definido unos permisos:

- Leer calendario de un usuario
- Escribir en el calendario de un usuario
- Enviar correo como un usuario
- [+ más](https://graph.microsoft.io)

Definiendo estos permisos, el recurso puede tener un control específico sobre sus datos y cómo se expone al exterior.  Una aplicación de terceros 3ª puede solicitar estos permisos de un usuario final - y el usuario final debe aprobar los permisos para que la aplicación puede actuar en su nombre.  Por fragmentar la funcionalidad del recurso en conjuntos de permisos más pequeños, 3er aplicaciones de terceros pueden crearse para solicitar sólo los permisos específicos que necesitan para realizar sus tareas.  También permite a los usuarios finales sabe exactamente cómo una aplicación va a utilizar sus datos, para que sean más seguros de que la aplicación no se comporta malintencionadas.

En Azure AD y OAuth, estos permisos se denominan **ámbitos**.  Puede verlos también conocida como **oAuth2Permissions**.  Un ámbito se representa en Azure AD como un valor de cadena.  Continuar con el ejemplo de Microsoft Graph, el valor de ámbito para cada permiso es:

- Leer el calendario de un usuario:`Calendar.Read`
- Escribir en el calendario de un usuario:`Mail.ReadWrite`
- Enviar correo como un usuario:`Mail.Send`

Una aplicación puede solicitar estos permisos especificando los ámbitos de solicitudes de extremo de la versión 2.0, como se describe a continuación.

## <a name="openid-connect-scopes"></a>Conectar OpenId ámbitos

La implementación de la versión 2.0 de conectar OpenID tiene unos ámbitos bien definidos que no se aplican a cualquier recurso determinado - `openid`, `email`, `profile`, y `offline_access`.

#### <a name="openid"></a>OpenId

Si realiza una aplicación de inicio de sesión con [OpenID conectarse](active-directory-v2-protocols.md#openid-connect-sign-in-flow), debe solicitar la `openid` ámbito.  La `openid` ámbito aparecerá en la pantalla de consentimiento de cuenta de trabajo como el permiso "Iniciar sesión" y en la pantalla de consentimiento de cuenta Microsoft personal como el permiso "Ver su perfil y conectarse a aplicaciones y servicios con su cuenta de Microsoft".  Permite que una aplicación recibir un identificador único para el usuario en el formulario de la `sub` reclamar.  También permite el acceso de la aplicación en el extremo de la información de usuario.  La `openid` ámbito también puede usarse en el extremo de token de versión 2.0 para adquirir id_tokens, que se puede utilizar para proteger HTTP llamadas entre los distintos componentes de una aplicación.

#### <a name="email"></a>Correo electrónico

La `email` ámbito pueden incluir junto con la `openid` ámbito y otras.  Permite el acceso de la aplicación a la dirección de correo electrónico principal del usuario en el formulario de la `email` reclamar.  La `email` reclamación solo se incluirán en tokens si está asociada con la cuenta de usuario, que no siempre es el caso de una dirección de correo electrónico.  Si usa el `email` ámbito, la aplicación debe estar preparada para controlar el caso de que el `email` reclamación no existe en el token.

#### <a name="profile"></a>Perfil

La `profile` ámbito pueden incluir junto con la `openid` ámbito y otras.  Permite el acceso de la aplicación a un amplio abanico de información sobre el usuario.  Esto incluye, pero no se limita a nombre dado del usuario, apellido, nombre de usuario preferido, identificador de objeto y así sucesivamente.  Para obtener una lista completa de las notificaciones de perfil disponibles en id_tokens para un usuario determinado, consulte la [referencia de token de versión 2.0](active-directory-v2-tokens.md).

#### <a name="offlineaccess"></a>Offline_access

La [ `offline_access` ámbito](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) permite la aplicación de acceso a los recursos en nombre del usuario durante un período de tiempo prolongado.  En la pantalla de consentimiento de cuenta de trabajo, este ámbito aparecerá como el permiso "Tener acceso a los datos en cualquier momento".  En la pantalla de consentimiento de cuenta de Microsoft personal, aparecerá como el permiso "Tener acceso a la información en cualquier momento".  Cuando un usuario aprueba la `offline_access` ámbito, la aplicación se habilitarán para recibir tokens de actualización desde el extremo de token de versión 2.0.  Actualizar tokens son larga y permitir que su aplicación adquirir nuevos tokens de acceso como las antiguas expiren.

Si su aplicación no se solicita la `offline_access` ámbito, no recibirá refresh_tokens.  Esto significa que cuando canjear un authorization_code en el [flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), sólo recibirá vuelva un access_token desde el `/token` punto final.  Esa access_token permanecerá válida durante un breve período de tiempo (normalmente una hora), pero caducará al final.  Volver a partir de ese momento, la aplicación necesitará redirigir al usuario a la `/authorize` extremo para recuperar una nueva authorization_code.  Durante este redireccionamiento, el usuario puede o no posible que tenga que escribir sus credenciales de nuevo o volver a consentimiento a permisos, según el tipo de aplicación.

Para obtener más información sobre cómo obtener y usar actualizar tokens, consulte la [referencia de protocolo de versión 2.0](active-directory-v2-protocols.md).


## <a name="requesting-individual-user-consent"></a>Solicitar consentimiento de usuario individual

En una solicitud de autorización [OpenID conectar o OAuth 2.0](active-directory-v2-protocols.md) , una aplicación puede solicitar los permisos que necesita usar la `scope` parámetro de consulta.  Por ejemplo, cuando un usuario inicia sesión en una aplicación, la aplicación debería enviar una solicitud similar al siguiente (con saltos de línea para mejorar la legibilidad):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

La `scope` parámetro es una lista separada por espacios de ámbitos que solicita la aplicación.  Cada ámbito individual indica si se agrega el valor de ámbito para el identificador de recursos (URI de ID de aplicación).  La solicitud anterior indica que la aplicación necesita permiso para leer el calendario del usuario y enviar correo electrónico como el usuario.

Después de que el usuario escribe sus credenciales, el extremo de la versión 2.0 buscará un registro coincidente de **consentimiento del usuario**.  Si el usuario no ha aceptado a cualquiera de los permisos solicitados en el pasado, el extremo de la versión 2.0 le preguntará al usuario que conceda los permisos solicitados.  

![Captura de pantalla de consentimiento de cuenta de trabajo](../media/active-directory-v2-flows/work_account_consent.png)

Cuando el usuario aprueba el permiso, se registrará el consentimiento para que el usuario no tenga que volver a consentimiento en inicios de sesión subsiguientes.

## <a name="requesting-consent-for-an-entire-tenant"></a>Solicitar consentimiento para todo un inquilino

A menudo, cuando una organización adquiere una suscripción a una aplicación o la licencia, desean totalmente la aprovisionar para los empleados.  Como parte de este proceso, un administrador de empresa puede conceder consentimiento para la aplicación para que actúe en nombre de cualquier empleado.  Al conceder consentimiento para todo un inquilino, empleados de la organización no experimentará la pantalla de su consentimiento para esa aplicación.

Para solicitar consentimiento para todos los usuarios en un inquilino, la aplicación puede usar el **Administrador de consentimiento extremo**, que se describen a continuación.

## <a name="admin-restricted-scopes"></a>Restringido por el Administrador de ámbitos

Ciertos permisos de privilegio alto en el ecosistema de Microsoft pueden marcarse como **restringido por el administrador**.  Ejemplos de dichos ámbitos:

- Leer datos del directorio de la organizaion:`Directory.Read`
- Escribir datos en el directorio de la organización:`Directory.ReadWrite`
- Grupos de seguridad de lectura en el directorio de la organización:`Groups.Read.All`

Mientras un usuario consumidor puede conceder acceso a una aplicación a dichos datos, los usuarios de la organización tienen restricciones conceder acceso al mismo conjunto de información confidencial.  Si su aplicación solicita el acceso a uno de estos permisos de un usuario profesional, el usuario recibirá un mensaje de error que indica que son no autorizados a consentimiento con los permisos de la aplicación.

Si su aplicación requiere acceso a estos ámbitos restringido por el administrador para las organizaciones, se debe solicitar directamente desde el Administrador de una organización que también usa la **administración consentimiento extremo**, que se describen a continuación.

Cuando un administrador conceda estos permisos a través del extremo de consentimiento del administrador, se concederá consentimiento para todos los usuarios del inquilino, como se describió anteriormente.

## <a name="using-the-admin-consent-endpoint"></a>Con el extremo de consentimiento del administrador

Siguiendo estos pasos, la aplicación podrán recopilar los permisos para todos los usuarios de un determinado inquilino, incluidos ámbitos restringido por el administrador.  Para ver un ejemplo de código que implementa lo pasos se han descrito a continuación, consulte el [ejemplo restringido ámbitos de administración](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar los permisos en el portal de registro de la aplicación

- Si todavía no lo ha hecho, vaya a la aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o [crear una aplicación](active-directory-v2-app-registration.md) .
- Busque la sección **Permisos de Microsoft Graph** y agregue los permisos que requiere la aplicación.
- Asegúrese de **Guardar** el registro de aplicación

#### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: iniciar sesión el usuario en su aplicación

Normalmente al crear una aplicación que utiliza el Administrador de consentimiento extremo, la aplicación tendrán que tener una página o la vista que permite el Administrador de aprobar los permisos de la aplicación.  Esta página puede ser parte del flujo de registro de la aplicación, parte de la configuración de la aplicación, o un flujo de "conectar" dedicada.  En muchos casos, tiene sentido para que la aplicación mostrar esta "conectar" vista sólo después de que un usuario ha iniciado sesión con una cuenta profesional o educativa Microsoft.

Inicio de sesión del usuario en la aplicación le permite identificar la organización buscan una solución a los que el administrador pertenece antes pedirle que apruebe los permisos necesarios.  Aunque no es estrictamente necesario, puede ayudarle a crear una experiencia más intuitiva para los usuarios de la organización.  Para cerrar la sesión de usuario en, siga los [tutoriales de protocolo versión 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar los permisos de un administrador de directorio

Cuando esté listo para solicitar permisos de administrador de la empresa, puede redirigir al usuario a la versión 2.0 **Administrador consentimiento extremo**.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | Obligatorio | El inquilino de directorio que desea solicitar permiso de.  Se puede ofrecer en formato de nombre descriptivo o guid. |
| client_id | Obligatorio | El identificador de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) asignado a la aplicación. |
| redirect_uri | Obligatorio | Redirect_uri donde desea que la respuesta que se envíen para que su aplicación controle.  Debe coincidir exactamente con uno de los redirect_uris registrado en el portal. |
| estado | se recomienda | Un valor incluido en la solicitud que se devolverá en la respuesta de token.  Puede ser una cadena de cualquier contenido que desee.  El estado se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, por ejemplo, la página o estaban en la vista. |

En este momento, Azure AD se exigir que un administrador de inquilinos puedan iniciar sesión completar la solicitud.  El administrador le pedirá que aprobar todos los permisos que ha solicitado para su aplicación en el portal de registro. 

##### <a name="successful-response"></a>Respuesta correcta
Si el administrador aprueba los permisos para la aplicación, será la respuesta correcta:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| inquilino | El inquilino de directorio que conceden los permisos solicitado, en formato guid de la aplicación. |
| estado | Un valor incluido en la solicitud que se devolverá en la respuesta de token.  Puede ser una cadena de cualquier contenido que desee.  El estado se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, por ejemplo, la página o estaban en la vista. |
| admin_consent | Se establece en `True`. |



##### <a name="error-response"></a>Respuesta de error
Si el administrador no aprobar los permisos para la aplicación, será la respuesta error:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| Error | Una cadena de código de error que puede usarse para clasificar tipos de errores que se producen y puede utilizarse ante los errores. |
| error_description | Un mensaje de error específico que ayudan a un desarrollador a identificar la causa de un error.  |

Una vez que se ha recibido una respuesta correcta desde el extremo de consentimiento de administración, la aplicación ha obtenido los permisos que solicita.  Ahora puede mover hasta que se solicita un token para el recurso que desee, como se describe a continuación.

## <a name="using-permissions"></a>Uso de permisos

Después de que el usuario acepta los permisos para la aplicación, su aplicación para adquirir tokens de acceso que representan los permisos de la aplicación para tener acceso a un recurso en alguna.  Un token de acceso dado sólo se puede usar para un único resorce, pero codificado dentro de ella será todos los permisos que se le ha concedido a su aplicación para ese recurso.  Para adquirir un token de acceso, la aplicación puede realizar una solicitud en el extremo de token de versión 2.0:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

El token de acceso resultante puede usarse en las solicitudes HTTP al recurso: sujeto indicará al recurso que su aplicación tiene el permiso adecuado para realizar una tarea determinada.  

Para obtener más detalles sobre el protocolo OAuth 2.0 y cómo adquirir tokens de acceso, vea la [referencia de protocolo de extremo de versión 2.0](active-directory-v2-protocols.md).
