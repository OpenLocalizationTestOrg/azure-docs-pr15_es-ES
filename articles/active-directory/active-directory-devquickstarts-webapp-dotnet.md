<properties
    pageTitle="Introducción de .NET de Azure AD | Microsoft Azure"
    description="Cómo crear una aplicación Web de MVC de .NET que se integra con Azure AD para iniciar sesión."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="aspnet-web-app-sign-in--sign-out-with-azure-ad"></a>Signo de aplicación Web de ASP.NET en & Cerrar sesión con Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD facilita la forma muy sencilla delegar la administración de identidades de la aplicación web, proporcionar solo inicio de sesión y cierre de sesión con solo unas pocas líneas de código.  En las aplicaciones web de Asp.NET, puede realizar esto con la implementación de Microsoft del software intermedio dirigido a la Comunidad OWIN incluido en .NET Framework 4.5.  Aquí, usaremos OWIN para:
-   Inicie la sesión de usuario en la aplicación con Azure AD como el proveedor de identidades.
-   Mostrar información sobre el usuario.
-   Inicie sesión en el usuario de la aplicación.

Para ello, necesitará:

1. Registrar una aplicación con Azure AD
2. Configurar la aplicación para usar la canalización de autenticación OWIN.
3. Use OWIN para emitir solicitudes de inicio de sesión y cierre de sesión a Azure AD.
4. Imprimir datos sobre el usuario.

Para comenzar, [Descargue la estructura de la aplicación](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) o [descargar el ejemplo completo](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  También necesitará a un inquilino de Azure AD en el que se va a registrar la aplicación.  Si aún no tiene una, [Obtenga información sobre cómo obtener una](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>*1. registrar una aplicación con Azure AD*
Para habilitar la aplicación autenticar usuarios, primero deberá registrar una nueva aplicación en su inquilino.

- Inicie sesión en el Portal de administración de Azure.
- En la barra de navegación izquierda, haga clic en **Active Directory**.
- Seleccione al inquilino donde desea registrar la aplicación.
- Haga clic en la pestaña de **aplicaciones** y haga clic en Agregar en la Bandeja de la parte inferior.
- Siga las indicaciones y cree una nueva **aplicación Web o WebAPI**.
    - El **nombre** de la aplicación describe la aplicación a los usuarios finales
    -   La **Dirección URL de inicio de sesión** es la dirección URL base de la aplicación.  El valor predeterminado de la estructura es `https://localhost:44320/`.
    - La **Aplicación identificador URI** es un identificador único para la aplicación.  La convención consiste en usar `https://<tenant-domain>/<app-name>`, p. ej.`https://contoso.onmicrosoft.com/my-first-aad-app`
- Una vez haya completado el registro, AAD asignar su aplicación un identificador único del cliente.  Necesitará este valor en las siguientes secciones, así que cópielo desde la ficha configurar.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurar la aplicación para usar la canalización de autenticación OWIN*
A continuación, se configurará el software OWIN intermedio para utilizar el protocolo de autenticación OpenID conectarse.  OWIN se usará para emitir solicitudes de inicio de sesión y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario, entre otras cosas.

-   Para empezar, agregue los paquetes de NuGet OWIN software intermedio para el proyecto mediante la consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Agregar una clase de inicio de OWIN al proyecto denominado `Startup.cs` derecha, haga clic en el proyecto--> **Agregar** --> **Nuevo elemento** --> Buscar "OWIN".  El software de OWIN intermedio llamará la `Configuration(...)` método cuando se inicia la aplicación.
-   Cambie la declaración de clase a `public partial class Startup` -ya hemos implementado una parte de esta clase para usted en otro archivo.  En la `Configuration(...)` método, realizar una llamada a ConfgureAuth(...) para configurar la autenticación para la aplicación web  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Abra el archivo `App_Start\Startup.Auth.cs` e implementar la `ConfigureAuth(...)` método.  Los parámetros que se proporcionan en `OpenIDConnectAuthenticationOptions` servirá como las coordenadas de la aplicación para comunicarse con Azure AD.  También deberá configurar la autenticación de Cookie: el software intermedio OpenID conectar utiliza cookies debajo de la cubierta.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-   Por último, abra la `web.config` de archivos en la raíz del proyecto y a continuación, escriba los valores de configuración de la `<appSettings>` sección.
    -   La aplicación `ida:ClientId` es el Guid que ha copiado desde el Portal de Azure en el paso 1.
    -   La `ida:Tenant` es el nombre de su inquilino de Azure AD, por ejemplo, "contoso.onmicrosoft.com".
    -   Su `ida:PostLogoutRedirectUri` indica a Azure AD donde se debe redirigir un usuario cuando finaliza correctamente una solicitud de cierre de sesión.

## <a name="3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>*3. use OWIN para emitir solicitudes de inicio de sesión y cierre de sesión a Azure AD*
La aplicación ahora está configurada correctamente para comunicarse con Azure AD mediante el protocolo de autenticación OpenID conectarse.  OWIN se encargan de todos los detalles horrible de elaborar los mensajes de autenticación, validación tokens de Azure AD y mantener la sesión de usuario.  Todo lo que queda ofrecen a los usuarios un modo de inicio de sesión y cerrar sesión.

- Puede usar autorizar etiquetas en los controladores de requieren que el usuario inicia sesión antes de acceder a una página determinada.  Abrir `Controllers\HomeController.cs`y agregue la `[Authorize]` etiqueta en el controlador de sobre.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-   También puede usar OWIN para ejecutar directamente las solicitudes de autenticación desde dentro del código.  Abrir `Controllers\AccountController.cs`.  En las acciones SignIn() y SignOut(), emita desafío OpenID conectarse y solicitudes de cierre de sesión, respectivamente.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-   Ahora, abra `Views\Shared\_LoginPartial.cshtml`.  Esto es donde deberá mostrar al usuario vínculos de inicio de sesión y cierre de sesión de la aplicación y el nombre del usuario en una vista de impresión.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## <a name="4--display-user-information"></a>*4. información de usuario de visualización de*
Autenticación de usuarios con OpenID Connect, Azure AD devuelve una id_token a la aplicación que contiene "reclamaciones" o aserciones sobre el usuario.  Puede usar estas notificaciones para personalizar la aplicación:

- Abrir la `Controllers\HomeController.cs` archivo.  Puede acceder a reclamaciones del usuario en los controladores a través de la `ClaimsPrincipal.Current` objeto principal de seguridad.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Por último, crear y ejecutar la aplicación!  Si todavía no lo ha hecho, ahora es el momento de crear un nuevo usuario en el inquilino con un *. dominio onmicrosoft.com.  Inicie sesión con dicho usuario y observe cómo la identidad del usuario se refleja en la barra de navegación superior.  Cerrar la sesión y vuelva a iniciarla como otro usuario en su inquilino.  Si se siente especialmente ambicioso, registrar y ejecutar otra instancia de esta aplicación (con su propio clientId) e inspección vea Inicio de sesión único en acción.

Para referencia, en el ejemplo completo (sin los valores de configuración), [se proporciona aquí](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  

Ahora puede desplazarse a temas más avanzados.  Desea probar:

[Proteger un Web API con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
