<properties
    pageTitle="Aplicación Web de Azure AD versión 2.0 .NET | Microsoft Azure"
    description="Cómo crear una aplicación Web de MVC de .NET web llamadas servicios mediante personal cuentas de Microsoft y trabajo o cuentas de escuela de inicio de sesión."
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
    ms.date="10/27/2016"
    ms.author="dastrock"/>

# <a name="calling-a-web-api-from-a-net-web-app"></a>Llamar a un sitio web API desde una aplicación web de .NET

Rápidamente con el extremo de la versión 2.0, puede agregar autenticación a sus aplicaciones web y web API con el soporte técnico para ambas cuentas personales de Microsoft y cuentas de trabajo o escuela.  Aquí, vamos a crear una aplicación web de MVC que firma los usuarios con OpenID conectar, con algunas ayuda de software intermedio OWIN de Microsoft.  La aplicación web obtener tokens de acceso OAuth 2.0 para un sitio web api protegidos por OAuth 2.0, que permite crear, leer y eliminar un usuario dado "lista de tareas pendientes".

En este tutorial se centrará principalmente en usar MSAL para adquirir y usar tokens de access en una aplicación web, que se describe en completa [aquí](active-directory-v2-flows.md#web-apps).  Como los requisitos previos, es aconsejable primero Obtenga información sobre cómo [agregar inicio de sesión básica de una aplicación web](active-directory-v2-devquickstarts-dotnet-web.md) o cómo [proteger correctamente una API de web](active-directory-v2-devquickstarts-dotnet-api.md).

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

## <a name="download-sample-code"></a>Descargar el código de ejemplo

El código de este tutorial se mantiene [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Para seguir a lo largo, puede [descargar el esquema de la aplicación como un .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) o clonar la estructura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Como alternativa, puede [Descargar la aplicación completada como un .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) o clonar la aplicación completada:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Registrar una aplicación
Crear una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o siga estos [pasos detallados](active-directory-v2-app-registration.md).  Asegúrese de que:

- Copia hacia abajo el **Identificador de la aplicación** asignado a la aplicación, tendrá pronto.
- Crear un **Secreto de aplicación** de la **contraseña** , escriba y a continuación, copiar su valor para más tarde
- Agregue la plataforma de **Web** para la aplicación.
- Escriba el correcto **URI redirigir**. Indica el uri de redirección a Azure AD dónde deben dirigirse respuestas de autenticación: es el valor predeterminado para este tutorial `https://localhost:44326/`.


## <a name="install-owin"></a>Instalar OWIN
Agregue los paquetes de NuGet OWIN software intermedio para la `TodoList-WebApp` proyecto usando la consola del Administrador de paquetes.  El software de OWIN intermedio se utilizará para emitir solicitudes de inicio de sesión y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario, entre otras cosas.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Inicio de sesión del usuario
Configurar el software de OWIN intermedio para utilizar el [Protocolo de autenticación OpenID conectarse](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  

-   Abrir la `web.config` archivo en la raíz de la `TodoList-WebApp` del proyecto y a continuación, especifique los valores de configuración de la aplicación en la `<appSettings>` sección.
    -   La `ida:ClientId` es el **Identificador de la aplicación** asignada a su aplicación en el portal de registro.
    - La `ida:ClientSecret` es el **Secreto de la aplicación** que creó en el portal de registro.
    -   La `ida:RedirectUri` es la **Redirección Uri** especificado en el portal.
- Abrir la `web.config` archivo en la raíz de la `TodoList-Service` project y reemplace la `ida:Audience` con el mismo **Id de aplicación** anteriores.


- Abra el archivo `App_Start\Startup.Auth.cs` y agregar `using` instrucciones para las bibliotecas de arriba.
- En el mismo archivo, implementar la `ConfigureAuth(...)` método.  Los parámetros que se proporcionan en `OpenIDConnectAuthenticationOptions` servirá como las coordenadas de la aplicación para comunicarse con Azure AD.

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
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Usar MSAL para obtener acceso a tokens
En la `AuthorizationCodeReceived` notificación, queremos usar [OAuth 2.0 conjuntamente con OpenID Connect](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) para canjear la authorization_code para un token de acceso para el servicio de la lista de tareas pendientes.  MSAL puede facilitar este proceso para usted:

- En primer lugar, instale la versión preliminar de MSAL:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```
- Y agregar otra `using` instrucción a la `App_Start\Startup.Auth.cs` archivo para MSAL.
- Ahora, agregue un nuevo método, la `OnAuthorizationCodeReceived` controlador de eventos.  Este controlador usará MSAL para adquirir un token de acceso a la API de la lista de tareas pendientes y guardará el token en la caché del símbolo de MSAL para más tarde:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

- En aplicaciones web, MSAL tiene una caché de token extensible que se pueden usar para almacenar símbolos.  En este ejemplo se implementa el `NaiveSessionCache` que utiliza el almacenamiento de la sesión de http tokens de caché.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Llamar a la API de Web
Ahora es el momento de usar la access_token adquirido en el paso 3.  Abra la aplicación web `Controllers\TodoListController.cs` archivo, que hace que todas las solicitudes CRUD a la API de la lista de tareas pendientes.

- Puede usar MSAL nuevo aquí para capturar access_tokens de la caché MSAL.  En primer lugar, agregue un `using` declaración de MSAL a este archivo.

    `using Microsoft.Identity.Client;`

- En la `Index` la acción, use MSAL `AcquireTokenSilentAsync` método para obtener un access_token que se pueden usar para leer los datos del servicio de la lista de tareas pendientes:

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

- El ejemplo agrega el token resultante a la solicitud HTTP GET como el `Authorization` encabezado, que usa el servicio de la lista de tareas pendientes para autenticar la solicitud.
- Si el servicio de la lista de tareas pendientes devuelve un `401 Unauthorized` respuesta, la access_tokens en MSAL ha de ser válidas por algún motivo.  En este caso, debe eliminar cualquier access_tokens de la caché MSAL y mostrar al usuario un mensaje que deberá iniciar sesión de nuevo, que reinicie el flujo de adquisición de token.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

- Del mismo modo, si MSAL no puede devolver un access_token por cualquier motivo, debe indicar al usuario que vuelva a iniciar sesión.  Esto es tan sencillo como capturar cualquier `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

- El mismo exacto `AcquireTokenSilentAsync` llamada es implementd en la `Create` y `Delete` acciones.  En aplicaciones web, puede usar este método MSAL para obtener access_tokens siempre que lo necesite en la aplicación.  MSAL se ocupará de adquirir, almacenamiento en caché y actualización de tokens para usted.

Por último, crear y ejecutar la aplicación!  Inicie sesión con una Account de Microsoft o una cuenta de Azure AD y observe cómo la identidad del usuario se refleja en la barra de navegación superior.  Agregar y eliminar algunos elementos de lista de tareas pendientes del usuario para ver que la versión 2.0 de OAuth protegidos llamadas a la API en acción.  Ahora tiene una aplicación web & web API, ambas protegidas mediante protocolos estándar, que pueden autenticar a los usuarios con sus cuentas personales y de trabajo o la escuela.

Para referencia, en el ejemplo completo (sin los valores de configuración), [se proporciona aquí](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Pasos siguientes

Para obtener recursos adicionales, consulte:
- [La Guía del programador de versión 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Etiqueta de StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtener actualizaciones de seguridad de nuestros productos

Le recomendamos que obtener notificaciones de cuándo se producen incidencias de seguridad, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscribirse a las alertas de aviso de seguridad.
