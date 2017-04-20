<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Cómo crear una aplicación web que tiene la suscripción, inicio de sesión y con Azure Active Directory B2C de restablecimiento de contraseña."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-sign-up--sign-in-in-a-aspnet-web-app"></a>B2C de Azure AD: Suscripción & iniciar sesión en una aplicación Web de ASP.NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Usando B2C de Azure Active Directory (AD Azure), puede agregar características de administración de identidad de autoservicio potentes a la aplicación web en pocos pasos. En este artículo se describe cómo crear una aplicación web de ASP.NET que incluye la suscripción, inicio de sesión de usuario y restablece la contraseña. La aplicación incluirá compatibilidad con suscripción e inicio de sesión con un nombre de usuario o el correo electrónico y mediante cuentas sociales como Facebook y Google.

Este tutorial difiere de [nuestro tutorial de web de .NET](active-directory-b2c-devquickstarts-web-dotnet.md) en que utiliza un [Inicio de sesión o directiva de inicio de sesión](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy) para proporcionar el registro de usuario y sesión usando un único botón, en lugar de dos (uno por suscripción y otro para el inicio de sesión).  En breve descripción, un suscribirme o directiva de inicio de sesión permite a los usuarios iniciar sesión con una cuenta existente si tiene uno o cree uno nuevo si es su primera vez que usa la aplicación.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C

Antes de poder usar Azure AD B2C, debe crear un directorio o de inquilinos. Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos y más.  Si no tiene uno ya, [Cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar en esta guía.

## <a name="create-an-application"></a>Crear una aplicación

A continuación, deberá crear una aplicación en el directorio B2C. Esto le da información de Azure AD que necesita comunicarse de forma segura con la aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md).  Asegúrese de:

- Incluir una **aplicación/web API de web** en la aplicación.
- Escriba `https://localhost:44316/` como una **redirección URI**. Es la dirección URL predeterminada para este código de ejemplo.
- Copiar el **Identificador de la aplicación** que se asigna a la aplicación.  Lo necesitará más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define por una [Directiva](active-directory-b2c-reference-policies.md). Este ejemplo de código contiene dos experiencias de identidad: **suscripción & inicio de sesión**y **restablece la contraseña**.  Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de la directiva](active-directory-b2c-reference-policies.md). Cuando cree las dos directivas, asegúrese de:

- Elija la **Suscripción a un identificador de usuario** o de **suscripción de correo electrónico** en el módulo de proveedores de identidades.
- Elija el **nombre para mostrar** y otros atributos de la suscripción en la directiva de inicio de sesión y de inicio de sesión.
- Elija la notificación de **nombre de la presentación** como una aplicación reclamación en cada directiva. Puede elegir otras reclamaciones también.
- Copiar el **nombre** de cada directiva después de crearlo. Necesitará los nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de crear las dos directivas, está listo para crear la aplicación.

## <a name="download-the-code-and-configure-authentication"></a>Descargar el código y configurar la autenticación

El código de este ejemplo [se mantiene según GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI). Para generar el ejemplo mientras avanza, puede [descargar el proyecto de esquema como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/skeleton.zip). También puede clonar la estructura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

El ejemplo completo también está [disponible como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip) o en la `complete` rama del mismo repositorio.

Después de descargar el código de ejemplo, abra el archivo de sln de Visual Studio para empezar.

La aplicación se comunica con Azure AD B2C mediante el envío de solicitudes de autenticación de HTTP que especifican la directiva que desea ejecutar como parte de la solicitud. Para las aplicaciones web .NET, puede usar biblioteca OWIN de Microsoft para enviar solicitudes de autenticación OpenID conectarse, ejecutar directivas, administrar las sesiones de usuario y mucho más.

Para empezar, agregue los paquetes de NuGet OWIN software intermedio al proyecto mediante la consola del Administrador de paquetes de Visual Studio.

```
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package System.IdentityModel.Tokens.Jwt
```

Seguidamente, abra la `web.config` en la raíz del proyecto y especifique los valores de configuración de la aplicación en la `<appSettings>` sección, reemplazando los valores por debajo por la suya propia.  Puede dejar la `ida:RedirectUri` y la `ida:AadInstance` valores como, sin cambios.

```
<configuration>
  <appSettings>

    ...

    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SusiPolicyId" value="b2c_1_susi" />
    <add key="ida:PasswordResetPolicyId" value="b2c_1_reset" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

A continuación, agregue una clase de inicio OWIN al proyecto denominado `Startup.cs`. Haga clic en el proyecto, seleccione **Agregar** y **Nuevo elemento**y, a continuación, busque "OWIN". Cambie la declaración de clase a `public partial class Startup`. Parte de esta clase se implementa para usted en otro archivo. El software de OWIN intermedio llamará la `Configuration(...)` método cuando se inicia la aplicación. En este método, realizar una llamada a `ConfigureAuth(...)`, donde de configurar la autenticación de la aplicación.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Abra el archivo `App_Start\Startup.Auth.cs` e implementar la `ConfigureAuth(...)` método.  Los parámetros que se proporcionan en `OpenIdConnectAuthenticationOptions` servir de las coordenadas de la aplicación para comunicarse con Azure AD. También debe configurar la autenticación de cookies. El software intermedio OpenID conectar utiliza cookies para mantener las sesiones de usuario, entre otras cosas.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SusiPolicyId = ConfigurationManager.AppSettings["ida:SusiPolicyId"];
    public static string PasswordResetPolicyId = ConfigurationManager.AppSettings["ida:PasswordResetPolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(PasswordResetPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SusiPolicyId));

    }

    private Task OnSecurityTokenValidated(SecurityTokenValidatedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        // If you wanted to keep some local state in the app (like a db of signed up users),
        // you could use this notification to create the user record if it does not already
        // exist.

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
                SecurityTokenValidated = OnSecurityTokenValidated,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
...
```

## <a name="send-authentication-requests-to-azure-ad"></a>Enviar las peticiones de autenticación a Azure AD
La aplicación ahora está configurada correctamente para comunicarse con Azure AD B2C mediante el protocolo de autenticación OpenID conectarse.  OWIN se encargan de todos los detalles de elaborar los mensajes de autenticación, validación tokens de Azure AD y mantener la sesión de usuario.  Todo lo que queda iniciar flujo de cada usuario.

Cuando un usuario selecciona **Inicio de sesión** o **Olvidó su contraseña?** en la aplicación web, se invoca la acción asociada en `Controllers\AccountController.cs`. En cada caso, puede usar métodos OWIN integrados para desencadenar la directiva de la derecha:

```C#
// Controllers\AccountController.cs

public void Login()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SusiPolicyId);
    }
}

public void ResetPassword()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
        new AuthenticationProperties() { RedirectUri = "/" }, Startup.PasswordResetPolicyId);
    }
}
```

Durante la ejecución de la suscripción o directiva de inicio de sesión, el usuario tiene la oportunidad de hacer clic en un **Olvidó su contraseña?** vínculo.  En este caso, Azure AD B2C enviará su aplicación de un mensaje de error específico que indica que debe ejecutar una contraseña restablece la directiva.  Puede capturar este error en `Startup.Auth.cs` utilizando la `AuthenticationFailed` notificación:

```C#
// Used for avoiding yellow-screen-of-death TODO
private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        // If the user canceled the sign in, redirect back to the home page
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```


Además de invocar explícitamente una directiva, puede utilizar un `[Authorize]` etiqueta en los controladores que se ejecutará una directiva si el usuario no ha iniciado sesión. Abrir `Controllers\HomeController.cs` y agregue la `[Authorize]` etiqueta en el controlador de notificaciones.  OWIN seleccionará la última directiva configurada cuando la `[Authorize]` se visita etiqueta.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

También puede usar OWIN para cerrar la sesión del usuario de la aplicación. In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void Logout()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>Mostrar información de usuario
Cuando los usuarios se autentican usando OpenID conectarse, Azure AD devuelve un token de identificador a la aplicación que contiene las **notificaciones**. Estos son aserciones sobre el usuario. Puede usar notificaciones para personalizar la aplicación.  

Abrir la `Controllers\HomeController.cs` archivo. Puede tener acceso a las notificaciones de usuario en los controladores a través de la `ClaimsPrincipal.Current` objeto principal de seguridad.

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Puede obtener acceso a cualquier reclamación que recibe la aplicación de la misma manera.  Una lista de todas las notificaciones que recibe la aplicación está disponible para usted en la página **notificaciones** .

## <a name="run-the-sample-app"></a>Ejecute la aplicación de ejemplo

Por último, puede crear y ejecutar la aplicación. Suscríbase a la aplicación usando un nombre de usuario o la dirección de correo electrónico. Cerrar la sesión y vuelva a iniciarla como el mismo usuario. Editar perfil de usuario. Cerrar sesión y registrarse como un usuario diferente. Observe que la información se muestra en la pestaña de **reclamaciones** corresponde a la información que haya configurado en las directivas.

## <a name="add-social-idps"></a>Agregar IDPs sociales

Actualmente, la aplicación admite solo usuario suscripción e inicio de sesión utilizando **las cuentas locales**. Se trata de cuentas almacenadas en el directorio B2C que usan un nombre de usuario y contraseña. Mediante el uso de Azure AD B2C, puede agregar compatibilidad para otros **proveedores de identidades** (IDPs) sin cambiar el código.

Para agregar IDPs sociales a su aplicación, comience siguiendo las instrucciones detalladas en estos artículos. Para cada IDP desea admitir, necesita registrar una aplicación en ese sistema y obtener un identificador de cliente.

- [Configurar Facebook como un IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar Google como un IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar Amazon como un IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar LinkedIn como un IDP](active-directory-b2c-setup-li-app.md)

Después de agregar los proveedores de identidades al directorio B2C, necesitará editar cada una de las tres directivas para incluir la nuevas IDPs, como se describe en el [artículo de referencia de la directiva](active-directory-b2c-reference-policies.md). Después de guardar las directivas, vuelva a ejecutar la aplicación.  Debe ver el nuevos IDPs agregados como inicio de sesión y las opciones de suscripción en cada una de su identidad experiencias.

Puede experimentar con las directivas y observa el efecto en la aplicación de ejemplo. Agregar o quitar IDPs, manipular notificaciones de la aplicación o cambiar los atributos de suscripción. Experimente hasta que pueda ver cómo directivas, las solicitudes de autenticación y OWIN unir.

Para referencia, en el ejemplo completo (sin los valores de configuración), [se proporciona como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip). También puede clonar desde GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
