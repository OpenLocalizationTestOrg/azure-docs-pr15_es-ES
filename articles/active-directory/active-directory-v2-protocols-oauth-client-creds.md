
<properties
    pageTitle="Flujo de credenciales de cliente de OAuth de Azure AD versión 2.0 | Microsoft Azure"
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
    ms.date="09/26/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-client-credentials-flow"></a>fluyen de protocolos versión 2.0 - OAuth 2.0 cliente credenciales

La [concesión de credenciales de cliente de OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4), que también se conoce como "OAuth dos segmentos" puede utilizarse para tener acceso a recursos de hospedaje web mediante la identidad de una aplicación.  Se usa generalmente para interacciones de servidor a servidor que se deben ejecutar en segundo plano sin la precense inmediato de un usuario final.  Estos tipos de aplicaciones a menudo se denominan **daemons** o **cuentas de servicio**.

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

En el más habitual tres "tres secciones OAuth," una aplicación cliente se concede permiso de acceso a un recurso en nombre de un usuario concreto.  El permiso es **delegados** del usuario de la aplicación, normalmente durante el proceso de [consentimiento](active-directory-v2-scopes.md) .  Sin embargo, en el flujo de credenciales de cliente, tienen los permisos **directamente** a la propia aplicación.  Cuando la aplicación presenta un símbolo de un recurso, el recurso exige que la aplicación tiene autorización para realizar alguna acción - no que algún usuario tiene autorización.

## <a name="protocol-diagram"></a>Diagrama de protocolo
El flujo de credenciales de cliente completo tiene un aspecto algo parecido a esto: cada uno de los pasos que se describen en detalle a continuación.

![Flujo de credenciales de cliente](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Obtener autorización directa 
Hay dos maneras de que una aplicación normalmente recibe autorización directa para tener acceso a un recurso: a través de una lista de control de acceso en el recurso o asignación de permisos de aplicación en Azure AD.  Hay otras formas de que un recurso puede optar por autorizar a sus clientes y cada servidor de recursos puede seleccionar el método que más sentido tenga para su aplicación.  Estos dos métodos son las más comunes en Azure AD y más recomendadas para los clientes y los recursos que desean realizar el flujo de credenciales de cliente.

### <a name="access-control-lists"></a>Listas de control de acceso
Un proveedor de recursos determinado puede exigir una comprobación de autorización basada en una lista de identificadores de aplicación que conoce y concede algún nivel de acceso determinado.  Cuando el recurso recibe un símbolo desde el extremo de la versión 2.0, puede descodificar el token y extraer el identificador de la aplicación del cliente desde la `appid` y `iss` notificaciones.  A continuación, puede comparar que la aplicación frente a alguna lista de control de acceso (ACL) mantiene.  El detalle y el método de la lista de control de acceso pueden variar considerablemente de un recurso a recurso.

Un caso de uso común para dicha ACL es probar escaleras para una aplicación web o web api.  La web api solo podrá conceder un subconjunto de sus permisos completos a sus clientes distintos.  Pero para ejecutar pruebas de llevar a cabo en la api, se crea un cliente de prueba que adquiere tokens desde el extremo de la versión 2.0 y envía a la api.  La api puede ACL del identificador de la aplicación para tener acceso completo a la funcionalidad completa de la api del cliente de prueba.  Tenga en cuenta que si tiene dicha lista en el servicio, debe asegurarse no sólo validar el llamador `appid`, pero también valida que la `iss` del token es de confianza también.

Este tipo de autorización es común para demonios y cuentas de servicio que necesitan tener acceso a datos de los usuarios de consumidor con cuentas personales de Microsoft.  Para los datos que pertenecen a las organizaciones, se recomienda que adquirir la autorización necesaria a través de la aplicación perimssions.

### <a name="application-permissions"></a>Permisos de aplicación
En lugar de usar ACL, API pueden exponer un conjunto de **permisos de aplicación** que se pueden conceder a una aplicación.  Una aplicación de permiso se concede a una aplicación por un administrador de una organización y solo puede usarse para obtener acceso a los datos que pertenecen a la organización y sus empleados.  Por ejemplo, Microsoft Graph expone diversos permisos de aplicación:

- Leer el correo en todos los buzones
- Lectura y escritura en todos los buzones de correo
- Envío de correo como cualquier usuario
- Lectura de datos de directorio
- [+ más](https://graph.microsoft.io)

Para adquirir estos permisos en la aplicación, puede realizar los siguientes pasos.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Solicitar los permisos en el portal de registro de la aplicación

- Si todavía no lo ha hecho, vaya a la aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o [crear una aplicación](active-directory-v2-app-registration.md) .  Debe asegurarse de que la aplicación ha creado al menos un secreto de aplicación.
- Busque la sección **Permisos de aplicación directa** y agregue los permisos que requiere la aplicación.
- Asegúrese de **Guardar** el registro de aplicación

#### <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: iniciar sesión el usuario en su aplicación

Normalmente al crear una aplicación que utiliza los permisos de aplicación, la aplicación tendrán que tener una página o la vista que permite el Administrador de aprobar los permisos de la aplicación.  Esta página puede ser parte del flujo de registro de la aplicación, parte de la configuración de la aplicación, o un flujo de "conectar" dedicada.  En muchos casos, tiene sentido para que la aplicación mostrar esta "conectar" vista sólo después de que un usuario ha iniciado sesión con una cuenta profesional o educativa Microsoft.

Inicio de sesión del usuario en la aplicación le permite identificar la organización buscan una solución a los que pertenece el usuario antes de pedirle que apruebe los permisos de aplicación.  Aunque no es estrictamente necesario, puede ayudarle a crear una experiencia más intuitiva para los usuarios de la organización.  Para cerrar la sesión de usuario en, siga los [tutoriales de protocolo versión 2.0](active-directory-v2-protocols.md).

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
| inquilino | Obligatorio | El inquilino de directorio que desea solicitar permiso de.  Se puede ofrecer en formato de nombre descriptivo o guid.  Si no sabe qué inquilino al que pertenece el usuario y quiere permitir que inicie sesión con cualquier inquilino, use `common`. |
| client_id | Obligatorio | El identificador de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) asignado a la aplicación. |
| redirect_uri | Obligatorio | Redirect_uri donde desea que la respuesta que se envíen para que su aplicación controle.  Debe coincidir exactamente con uno de los redirect_uris registrado en el portal, excepto que debe ser la dirección url codificada y puede tener segmentos de ruta adicionales. |
| estado | se recomienda | Un valor incluido en la solicitud que se devolverá en la respuesta de token.  Puede ser una cadena de cualquier contenido que desee.  El estado se usa para codificar información sobre el estado del usuario en la aplicación antes de que se produjo la solicitud de autenticación, por ejemplo, la página o estaban en la vista. |

En este momento, Azure AD se exigir que un administrador de inquilinos puedan iniciar sesión completar la solicitud.  El administrador le pedirá que aprobar todos los permisos de aplicación directa que ha solicitado para su aplicación en el portal de registro. 

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

Una vez que se ha recibido una respuesta correcta desde la aplicación extremo de aprovisionamiento, su aplicación ha obtenido los permisos de aplicación directa que solicite.  Ahora puede mover hasta que se solicita un token para el recurso que desee.

## <a name="get-a-token"></a>Obtener un símbolo
Una vez que haya adquirido la autorización necesaria para la aplicación, puede continuar con la compra API tokens de acceso.  Para obtener un token con el cliente de concesión de credenciales, envíe una solicitud de entrada para la `/token` punto final de la versión 2.0:

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parámetro | | Descripción |
| ----------------------- | ------------------------------- | --------------- |
| client_id | Obligatorio | El identificador de aplicación que el portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) asignado a la aplicación. |
| ámbito | Obligatorio | El valor pasado para la `scope` parámetro en esta solicitud debe ser el identificador de recursos (URI de ID de aplicación) del recurso que desee, estampar con la `.default` sufijo.  Para que el ejemplo Microsoft Graph, el valor debe ser `https://graph.microsoft.com/.default`.  Este valor indica el punto final de la versión 2.0 que de todos los permisos de aplicación directo que ha configurado para la aplicación, debe emitir un token para los que pertenecen al recurso deseado. |
| client_secret | Obligatorio | El secreto de aplicación que ha generado en el portal de registro de la aplicación. |
| grant_type | Obligatorio | Debe ser `client_credentials`. | 

#### <a name="successful-response"></a>Respuesta correcta
Una respuesta correcta tardará el formulario:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parámetro | Descripción |
| ----------------------- | ------------------------------- |
| access_token | El token de acceso solicitado. La aplicación puede utilizar este token para autenticar al recurso protegido, como un sitio web API. |
| token_type | Indica el valor de tipo de token. El único tipo que admite Azure AD es `Bearer`.  |
| expires_in | ¿Cuánto tiempo el token de acceso es válido (en segundos). |

#### <a name="error-response"></a>Respuesta de error
Una respuesta de error tardará el formulario:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

## <a name="use-a-token"></a>Utilice un símbolo
Ahora que ha adquirido un símbolo, puede utilizar ese token para realizar solicitudes al recurso.  Cuando expira el token, simplemente repita la solicitud para la `/token` extremo para adquirir un token de acceso actualizado.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Código de ejemplo
Para ver un ejemplo de una aplicación que implementa de que el client_credentials conceder con el administrador consentimiento extremo, consulte nuestra [versión 2.0 daemon código de ejemplo](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
