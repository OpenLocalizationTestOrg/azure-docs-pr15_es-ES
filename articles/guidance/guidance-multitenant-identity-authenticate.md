<properties
   pageTitle="Autenticación en aplicaciones multiempresa | Microsoft Azure"
   description="¿Cómo una aplicación multiempresa puede autenticar los usuarios de Azure AD"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/23/2016"
   ms.author="mwasson"/>

# <a name="authentication-in-multitenant-apps-using-azure-ad-and-openid-connect"></a>Autenticación en aplicaciones multiempresa, con Azure AD y conectar OpenID

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie](guidance-multitenant-identity.md). También es una [aplicación de ejemplo] completa que acompaña a esta serie.

Este artículo describe cómo una aplicación multiempresa puede autenticar usuarios de Azure Active Directory (AD Azure), con OpenID conectar (OIDC) para autenticar.

## <a name="overview"></a>Información general

Nuestra [implementación de referencia](guidance-multitenant-identity-tailspin.md) es una aplicación de ASP.NET Core 1.0. La aplicación utiliza el software de conectar OpenID intermedio integrada para realizar el flujo de autenticación de OIDC. El siguiente diagrama muestra lo que sucede cuando el usuario inicia sesión, en un nivel alto.

![Flujo de autenticación](media/guidance-multitenant-identity/auth-flow.png)

1.  El usuario hace clic en el botón "iniciar sesión" en la aplicación. Esta acción se controla por un controlador de MVC.
2.  El controlador MVC devuelve una acción **ChallengeResult** .
3.  El software intermedio intercepta la **ChallengeResult** y crea una respuesta 302, que se redirige al usuario a la página de inicio de sesión de Azure AD.
4.  El usuario se autentica con Azure AD.
5.  Azure AD envía un token de identificador a la aplicación.
6.  El software intermedio valida el token de ID. En este momento, el usuario se autentica ahora dentro de la aplicación.
7.  El software intermedio redirige al usuario a la aplicación.

## <a name="register-the-app-with-azure-ad"></a>Registrar la aplicación con Azure AD

Para habilitar OpenID conectar, el proveedor de SaaS registra la aplicación dentro de su propios inquilino de Azure AD.

Para registrar la aplicación, siga los pasos de [Integración de aplicaciones con Azure Active Directory](../active-directory/active-directory-integrating-applications.md), en la sección [Agregar una aplicación](../active-directory/active-directory-integrating-applications.md#adding-an-application).

En la página **Configurar** :

-   Nota: el identificador de cliente.
-   En la **aplicación es varios inquilinos**, seleccione **Sí**.
-   Establecer la **Dirección URL de respuesta** a una dirección URL que Azure AD enviará la respuesta de autenticación. Puede usar la dirección URL base de la aplicación.
  - Nota: La dirección URL puede ser cualquier elemento, como el nombre de host coincide con la aplicación implementada.
  - Puede establecer varias direcciones URL de respuesta. Durante el desarrollo, puede usar un `localhost` dirección para ejecutar la aplicación localmente.
-   Generar un secreto de cliente: en **las teclas**, haga clic en la lista desplegable que dice **Seleccione duración** y elija 1 o 2 años. La clave será visible al hacer clic en **Guardar**. Asegúrese de copiar el valor, porque no se muestra de nuevo cuando se vuelva a cargar la página de configuración.

## <a name="configure-the-auth-middleware"></a>Configurar el software auth intermedio

Esta sección describe cómo configurar el software intermedio de autenticación en ASP.NET Core 1.0 para la autenticación multiempresa con OpenID conectarse.

En la clase de inicio, agregue el software intermedio OpenID conectarse:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] Vea [Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs).

Para obtener más información acerca de la clase de inicio, vea [Inicio de la aplicación](https://docs.asp.net/en/latest/fundamentals/startup.html) en la documentación de ASP.NET Core 1.0.

Establecer las opciones de software intermedio siguientes:

- **ClientId**. Identificador de cliente de la aplicación, que obtuvo cuando registra la aplicación en Azure AD.
- **Entidad**. Para una aplicación multiempresa, establezca esta propiedad en `https://login.microsoftonline.com/common/`. Esta es la dirección URL del extremo comunes de Azure AD, que permite a los usuarios de cualquier inquilino de Azure AD para iniciar sesión. Para obtener más información acerca del extremo comunes, vea [esta entrada de blog](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/).
- En **TokenValidationParameters**, establezca **ValidateIssuer** en falso. Esto significa que la aplicación será responsable de validar el valor del emisor en el token de ID. (El software intermedio aún valida el token propiamente dicho.) Para obtener más información sobre la validación del emisor, vea [validación del emisor](guidance-multitenant-identity-claims.md#issuer-validation).
- **CallbackPath**. Establecer esta igual que la ruta de acceso en la dirección URL de respuesta que registra en Azure AD. Por ejemplo, si la dirección URL de respuesta es `http://contoso.com/aadsignin`, debería **CallbackPath** `aadsignin`. Si no establece esta opción, el valor predeterminado es `signin-oidc`.
- **PostLogoutRedirectUri**. Especifique una dirección URL para redirigir a los usuarios después de cerrar la sesión. Se trata de una página que permite solicitudes anónimas &mdash; suele ser la página principal.
- **SignInScheme**. Establezca este valor en `CookieAuthenticationDefaults.AuthenticationScheme`. Esta opción significa que después de que el usuario se autentica, las solicitudes de usuario se almacenan localmente en un cookie. Este cookie es cómo el usuario permanezca conectado durante la sesión de explorador.
- **Eventos.** Devoluciones de llamada de evento; ver [eventos de autenticación](#authentication-events).

También puede agregar el software intermedio de autenticación de cookies para la canalización. Este software intermedio es responsable de escribir las notificaciones de usuario a una de las cookies y, a continuación, leer la cookie durante la carga de las siguientes páginas.

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## <a name="initiate-the-authentication-flow"></a>Iniciar el flujo de autenticación

Para iniciar el flujo de autenticación en ASP.NET MVC, devolver un **ChallengeResult** desde el controlador:

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

Hace que el software intermedio devolver una respuesta de 302 (encontrado) que redirige al extremo de autenticación.

## <a name="user-login-sessions"></a>Sesiones de inicio de sesión de usuario

Como se mencionó, cuando el usuario inicia sesión por primera vez, el software de autenticación de cookies intermedio escribe las notificaciones de usuario a una cookie. Después de eso, las solicitudes HTTP se autentican leyendo la cookie.

De forma predeterminada, el software intermedio de cookie escribe una [cookie de sesión][session-cookie], que obtiene una vez eliminados el usuario cierra el explorador. La próxima vez que el usuario siguiente visita el sitio, tienen volver a iniciar sesión. Sin embargo, si establece **IsPersistent** true en el **ChallengeResult**, el software intermedio escribe una cookie persistente, para que el usuario esté conectado después de cerrar el explorador. Puede configurar la caducidad de la cookie; consulte [Opciones de cookies Controlling][cookie-options]. Las cookies persistentes son más cómodas para el usuario, pero pueden ser inapropiadas para algunas aplicaciones (por ejemplo, una aplicación bancaria) donde desea que el usuario para iniciar sesión en cada vez.

## <a name="about-the-openid-connect-middleware"></a>Información sobre el software intermedio OpenID conectar

El software OpenID conectar intermedio en ASP.NET oculta la mayoría de los detalles de protocolo. Esta sección contiene algunas notas sobre la aplicación, que puede ser útil para comprender el flujo de protocolo.

En primer lugar, vamos a examinar el flujo de autenticación en términos de ASP.NET (pasando por alto los detalles del flujo de protocolo OIDC entre la aplicación y Azure AD). El siguiente diagrama muestra el proceso.

![Flujo de inicio de sesión](media/guidance-multitenant-identity/sign-in-flow.png)

En este diagrama, hay dos controladores MVC. El controlador de cuenta controla las solicitudes de inicio de sesión y, a continuación, el controlador de inicio presenta la página principal.

Aquí es el proceso de autenticación:

1. El usuario hace clic en el botón "Inicio de sesión" y, a continuación, el explorador enviará una solicitud GET. Por ejemplo: `GET /Account/SignIn/`.
2. La cuenta no controlador devuelve un `ChallengeResult`.
3. El software de OIDC intermedio devuelve una respuesta HTTP 302, redirigir a Azure AD.
4. El explorador envía la solicitud de autenticación a Azure AD
5. El usuario inicia sesión en Azure AD y Azure AD devuelve una respuesta de autenticación.
6. El software de OIDC intermedio crea una entidad de seguridad de reclamaciones y pasa al software intermedio de autenticación de cookies.
7. El software intermedio de cookie serializa al capital de reclamaciones y establece una cookie.
8. El software de OIDC intermedio se redirige a la dirección URL de devolución de llamada de la aplicación.
10. El explorador sigue el redireccionamiento, enviar la cookie en la solicitud.
11. El software intermedio de cookie deserializa la cookie a notificaciones de principal y establece `HttpContext.User` igual que la entidad de seguridad de reclamaciones. La solicitud se dirige a un controlador MVC.

### <a name="authentication-ticket"></a>Vale de autenticación

Si la autenticación se realiza correctamente, el software de OIDC intermedio crea un vale de autenticación, que contiene una entidad de seguridad de reclamaciones que contiene las notificaciones del usuario. Puede tener acceso a la lista de los eventos **AuthenticationValidated** o **TicketReceived** .

> [AZURE.NOTE] Hasta que se haya completado el flujo de autenticación completo, `HttpContext.User` un anónimo principal, _no_ mantiene el usuario autenticado. El capital anónimo tiene una colección de reclamaciones vacío. Después de autenticación completa y los redireccionamientos de aplicación, el software intermedio de cookie deserializa la cookie de autenticación y conjuntos de `HttpContext.User` a una entidad de seguridad de reclamaciones que representa el usuario autenticado.

### <a name="authentication-events"></a>Eventos de autenticación

Durante el proceso de autenticación, el software intermedio OpenID conectar genera una serie de eventos:

- **RedirectToAuthenticationEndpoint**. Llama derecha antes de que el software intermedio redirige al extremo de autenticación. Puede utilizar este evento para modificar la dirección URL de redirección; Por ejemplo, para agregar los parámetros de la solicitud. Para obtener un ejemplo, consulte [Agregar el símbolo del sistema de administración consentimiento](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt) .

- **AuthorizationResponseReceived**. Llamar después de que el software intermedio recibe la respuesta de autenticación desde el proveedor de identidades (IDP), pero antes de que el software intermedio valida la respuesta.  

- **AuthorizationCodeReceived**. Llamar con el código de autorización.

- **TokenResponseReceived**. Se llama después de que el software intermedio Obtiene un acceso token desde el IDP. Se aplica solo a flujo de código de autorización.

- **AuthenticationValidated**. Llamada después de que el software intermedio valide el token de ID. En este momento, la aplicación tiene un conjunto de reclamaciones validadas sobre el usuario. Puede usar este evento para realizar una validación adicional de las notificaciones, o para transformar notificaciones. Consulte [trabajar con notificaciones](guidance-multitenant-identity-claims.md).

- **UserInformationReceived**. Se llama si el software intermedio Obtiene el perfil de usuario desde el extremo de la información de usuario. Solo se aplica a flujo de código de autorización y sólo cuando `GetClaimsFromUserInfoEndpoint = true` en las opciones de software intermedio.

- **TicketReceived**. Llamada cuando se complete la autenticación. Este es el último evento, suponiendo que la autenticación se realiza correctamente. Después de este evento se controla, el usuario ha iniciado sesión en la aplicación.

- **AuthenticationFailed**. Se llama si se produce un error de autenticación. Utilice este evento para controlar los errores de autenticación &mdash; por ejemplo, al redireccionar a una página de error.

Para proporcionar devoluciones de llamada para estos eventos, establezca la opción de **eventos** en el software intermedio. Para declarar los controladores de eventos de dos formas: en línea con expresiones lambda o en una clase que se deriva de **OpenIdConnectEvents**.

En línea con expresiones lambda:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

Deriva de **OpenIdConnectEvents**:

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

El segundo enfoque se recomienda si sus devoluciones de llamada de evento tienen cualquier lógica importante, para que no ocupen la clase de inicio. Nuestra implementación de referencia usa este enfoque; vea [SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs).

### <a name="openid-connect-endpoints"></a>OpenID conectar extremos

Azure AD admite [OpenID conectar detección](https://openid.net/specs/openid-connect-discovery-1_0.html), donde el proveedor de identidades (IDP) devuelve un documento de metadatos JSON desde un [extremo conocido](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). El documento de metadatos contiene información como:

-   La dirección URL del extremo de autorización. Esto es donde se redirige la aplicación para autenticar al usuario.
-   La dirección URL del extremo "Finalizar sesión", donde la aplicación se pasa a la sesión del usuario.
-   La dirección URL para obtener las claves de firma, que utiliza el cliente para validar los tokens OIDC que obtiene desde el IDP.

De forma predeterminada, el software de OIDC intermedio sabe cómo buscar metadatos. Establezca la opción de **entidad** en el software intermedio y el software intermedio construye la dirección URL de los metadatos. (Puede reemplazar la dirección URL de metadatos estableciendo la opción **MetadataAddress** ).

### <a name="openid-connect-flows"></a>OpenID conectar flujos

De forma predeterminada, el software OIDC intermedio usa flujo híbrido con el modo de respuesta de entrada de formulario.

-   _Flujo de híbrido_ significa que el cliente puede obtener un token de identificador y un código de autorización en el mismo recorrido en el servidor de autorización.
-   _Formulario registrar el modo de respuesta_ indica que el servidor de autorización utiliza una solicitud de HTTP POST para enviar el código de token y la autorización de la aplicación. Los valores son formulario urlencoded (tipo de contenido = "application/x-www-form-urlencoded").

Cuando el software OIDC intermedio se redirige al extremo autorización, la dirección URL de redirección incluye todos los parámetros de cadena de consulta necesarios OIDC. En el flujo de híbrido:

-   client_id. Se establece este valor en la opción **ClientId**
-   ámbito = "openid perfil", lo que significa es una solicitud OIDC y queremos que el perfil de usuario.
-   response_type = "código id_token". Especifica el flujo de híbrido.
-   response_mode = "form_post". Especifica respuesta post de formulario.

Para especificar un flujo diferente, establezca la propiedad de **ResponseType** en las opciones. Por ejemplo:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

## <a name="next-steps"></a>Pasos siguientes

- Lea el siguiente artículo de esta serie: [trabajar con identidades basada en notificaciones en aplicaciones multiempresa][claims]


[claims]: guidance-multitenant-identity-claims.md
[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
