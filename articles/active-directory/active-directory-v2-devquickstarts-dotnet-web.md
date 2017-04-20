<properties
    pageTitle="Aplicación Web de Azure AD versión 2.0 .NET | Microsoft Azure"
    description="Cómo crear una aplicación Web de MVC de .NET que firma cuentas de trabajo o escuela y los usuarios con ambos Account personal de Microsoft."
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

# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Agregar inicio de sesión para una aplicación web de MVC .NET

Con el extremo de la versión 2.0, puede agregar rápidamente cuentas de trabajo o escuela y autenticación a sus aplicaciones web con soporte para ambas cuentas personales de Microsoft.  En las aplicaciones web ASP.NET, puede realizar esto con software intermedio OWIN de Microsoft incluido en .NET Framework 4.5.

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

 Aquí vamos a crear una aplicación web que usa OWIN para inicio de sesión del usuario, mostrar información sobre el usuario e inicie el usuario de la aplicación.
 
 ## <a name="download"></a>Descargar
El código de este tutorial se mantiene [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Para seguir a lo largo, puede [descargar el esquema de la aplicación como un .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) o clonar la estructura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

La aplicación completada se proporciona al final de este tutorial también.

## <a name="register-an-app"></a>Registrar una aplicación
Crear una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o siga estos [pasos detallados](active-directory-v2-app-registration.md).  Asegúrese de que:

- Copia hacia abajo el **Identificador de la aplicación** asignado a la aplicación, tendrá pronto.
- Agregue la plataforma de **Web** para la aplicación.
- Escriba el correcto **URI redirigir**. Indica el uri de redirección a Azure AD dónde deben dirigirse respuestas de autenticación: es el valor predeterminado para este tutorial `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Instalar y configurar la autenticación de OWIN
A continuación, se configurará el software OWIN intermedio para utilizar el protocolo de autenticación OpenID conectarse.  OWIN se usará para emitir solicitudes de inicio de sesión y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario, entre otras cosas.

-   Para comenzar, abra la `web.config` de archivos en la raíz del proyecto y a continuación, especifique los valores de configuración de la aplicación en la `<appSettings>` sección.
    -   La `ida:ClientId` es el **Identificador de la aplicación** asignada a su aplicación en el portal de registro.
    -   La `ida:RedirectUri` es la **Redirección Uri** especificado en el portal.

-   A continuación, agregue los paquetes de NuGet OWIN software intermedio para el proyecto mediante la consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Agregar una "OWIN inicio clase" al proyecto denominado `Startup.cs` derecha, haga clic en el proyecto--> **Agregar** --> **Nuevo elemento** --> Buscar "OWIN".  El software de OWIN intermedio llamará la `Configuration(...)` método cuando se inicia la aplicación.
-   Cambie la declaración de clase a `public partial class Startup` -ya hemos implementado una parte de esta clase para usted en otro archivo.  En la `Configuration(...)` método, realizar una llamada a ConfigureAuth(...) para configurar la autenticación para la aplicación web  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

-   Abra el archivo `App_Start\Startup.Auth.cs` e implementar la `ConfigureAuth(...)` método.  Los parámetros que se proporcionan en `OpenIdConnectAuthenticationOptions` servirá como las coordenadas de la aplicación para comunicarse con Azure AD.  También deberá configurar la autenticación de Cookie: el software intermedio OpenID conectar utiliza cookies debajo de la cubierta.

```C#
public void ConfigureAuth(IAppBuilder app)
             {
                     app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

                     app.UseCookieAuthentication(new CookieAuthenticationOptions());

                     app.UseOpenIdConnectAuthentication(
                             new OpenIdConnectAuthenticationOptions
                             {
                                     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
                                     // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                     // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                                     ClientId = clientId,
                                     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                                     RedirectUri = redirectUri,
                                     Scope = "openid email profile",
                                     ResponseType = "id_token",
                                     PostLogoutRedirectUri = redirectUri,
                                     TokenValidationParameters = new TokenValidationParameters
                                     {
                                             ValidateIssuer = false,
                                     },
                                     Notifications = new OpenIdConnectAuthenticationNotifications
                                     {
                                             AuthenticationFailed = OnAuthenticationFailed,
                                     }
                             });
             }
```

## <a name="send-authentication-requests"></a>Enviar solicitudes de autenticación
La aplicación ahora está configurada correctamente para comunicarse con el punto final de la versión 2.0 mediante el protocolo de autenticación OpenID conectarse.  OWIN se encargan de todos los detalles horrible de elaborar los mensajes de autenticación, validación tokens de Azure AD y mantener la sesión de usuario.  Todo lo que queda ofrecen a los usuarios un modo de inicio de sesión y cerrar sesión.

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

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-   Ahora, abra `Views\Shared\_LoginPartial.cshtml`.  Esto es donde deberá mostrar al usuario vínculos de inicio de sesión y cierre de sesión de la aplicación y el nombre del usuario en una vista de impresión.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
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

## <a name="display-user-information"></a>Mostrar información de usuario
Autenticación de usuarios con OpenID conectarse, el extremo de la versión 2.0 devuelve una id_token a la aplicación que contiene [reclamaciones](active-directory-v2-tokens.md#id_tokens)o aserciones sobre el usuario.  Puede usar estas notificaciones para personalizar la aplicación:

- Abrir la `Controllers\HomeController.cs` archivo.  Puede acceder a reclamaciones del usuario en los controladores a través de la `ClaimsPrincipal.Current` objeto principal de seguridad.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

## <a name="run"></a>Ejecutar

Por último, crear y ejecutar la aplicación!   Inicie sesión con una Account personal de Microsoft o una cuenta profesional o educativa y observe cómo la identidad del usuario se refleja en la barra de navegación superior.  Ahora tiene una aplicación web segura mediante protocolos estándar que pueden autenticar a los usuarios con sus cuentas personales y de trabajo o la escuela.

Para referencia, el ejemplo completo (sin los valores de configuración) [se proporciona como un .zip aquí](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip)o puede clonar ella desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Pasos siguientes

Ahora puede desplazarse a temas más avanzados.  Desea probar:

[Proteger una API Web con el extremo de la versión 2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Para obtener recursos adicionales, consulte:
- [La Guía del programador de versión 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Etiqueta de StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtener actualizaciones de seguridad de nuestros productos

Le recomendamos que obtener notificaciones de cuándo se producen incidencias de seguridad, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscribirse a las alertas de aviso de seguridad.
