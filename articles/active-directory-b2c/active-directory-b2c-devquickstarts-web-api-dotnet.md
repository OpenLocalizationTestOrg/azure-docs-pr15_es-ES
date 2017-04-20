<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Cómo crear una aplicación web que llama a un sitio web API utilizando Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>B2C de Azure AD: Llamar a un sitio web API desde una aplicación web de .NET

Usando B2C de Azure Active Directory (AD Azure), puede agregar las características de administración de identidad de autoservicio eficaces a las aplicaciones web y web API en pocos pasos. En este artículo se describe cómo crear una aplicación web de "Lista de tareas pendientes".NET controlador de vista de modelo (MVC) que llama a un sitio web API utilizando tokens portador

Este artículo no trata sobre cómo implementar el inicio de sesión, suscripción y administración de perfiles de Azure AD B2C. Se centra en web llamada API después de que el usuario ya está autenticado. Si todavía no lo ha hecho, debe empezar con la [.NET web app tutorial de introducción](active-directory-b2c-devquickstarts-web-dotnet.md) obtener información sobre los conceptos básicos de Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C

Antes de poder usar Azure AD B2C, debe crear un directorio o de inquilinos.  Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos y mucho más.  Si no tiene uno ya, [Cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar en esta guía.

## <a name="create-an-application"></a>Crear una aplicación

A continuación, deberá crear una aplicación en el directorio B2C. Esto le da información de Azure AD que necesita comunicarse de forma segura con la aplicación. En este caso, la aplicación web y la API de web estará representado por una sola **ID de aplicación**, ya que comprenden una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación/web API de web** en la aplicación.
- Escriba `https://localhost:44316/` como una **dirección URL de respuesta**. Es la dirección URL predeterminada para este código de ejemplo.
- Copie el **Identificador de la aplicación** que se asigna a la aplicación. También necesitará esto más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define por una [Directiva](active-directory-b2c-reference-policies.md). Esta aplicación web contiene tres experiencias de identidad: inicio de sesión, inicio de sesión y Editar perfil. Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de la directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Elija el **nombre para mostrar** y otros atributos de la suscripción en la directiva de suscripción.
- Elija el **nombre para mostrar** y el **Identificador de objeto de** notificaciones de la aplicación en cada directiva. Puede elegir otras reclamaciones también.
- Copiar el **nombre** de cada directiva después de crearlo. Debe tener el prefijo `b2c_1_`. Necesitará los nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de haber creado las tres directivas, está listo para crear la aplicación.

Tenga en cuenta que este artículo trata sobre cómo usar las directivas que acaba de crear. Para obtener información sobre cómo funcionan las directivas de Azure AD B2C, comience con la [.NET web app tutorial de introducción](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Descargar el código

El código de este tutorial [se mantiene según GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Para generar el ejemplo mientras avanza, puede [descargar el proyecto de esquema como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). También puede clonar la estructura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

La aplicación completada también está [disponible como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) o en la `complete` rama del mismo repositorio.

Después de descargar el código de ejemplo, abra el archivo de sln de Visual Studio para empezar.

## <a name="configure-the-task-web-app"></a>Configurar la aplicación web de tarea

Para obtener `TaskWebApp` para comunicarse con Azure AD B2C, deberá proporcionar algunos parámetros comunes. En la `TaskWebApp` proyecto, abrir la `web.config` de archivos en la raíz del proyecto y reemplazar los valores de la `<appSettings>` sección. Puede dejar la `AadInstance`, `RedirectUri`, y `TaskServiceUrl` los valores que aparecen.

```
  <appSettings>
    
    ...
    
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>Obtener acceso tokens y llamar a la API de tarea

En esta sección se describe cómo utilizar el token recibido durante el inicio de sesión con Azure AD B2C para tener acceso a un sitio web API también está protegida con Azure AD B2C.

Este artículo no trata sobre los detalles de cómo proteger la API. Para obtener información sobre cómo web API autentica de forma segura solicitudes mediante Azure AD B2C, consulte la [Introducción de web API a artículo](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="save-the-sign-in-token"></a>Guardar el inicio de sesión de token

En primer lugar, autenticar al usuario (con cualquiera de las directivas) y recibir un token de inicio de sesión de Azure AD B2C.  Si no está seguro de cómo ejecutar directivas, volver atrás y pruebe la [.NET web app tutorial de introducción](active-directory-b2c-devquickstarts-web-dotnet.md) para obtener información sobre los conceptos básicos de Azure AD B2C.

Abra el archivo `App_Start\Startup.Auth.cs`.  Hay un cambio importante que debe realizar para la `OpenIdConnectAuthenticationOptions` -debe establecer `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

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
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",
        
        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>Obtener un token de los controladores

La `TasksController` es responsable de comunicarse con la API, enviar solicitudes HTTP a la API para leer, crear y eliminar tareas web.  Como la API está protegida mediante Azure AD B2C, debe recuperar primero el token que guardó en el paso anterior.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;
        
    ...
}
```

La `BootstrapContext` contiene el token que adquirido mediante la ejecución de una de las directivas de B2C de inicio de sesión.

### <a name="read-tasks-from-the-web-api"></a>Leer las tareas de la API de web

Cuando tenga un símbolo, puede adjuntarlo a HTTP `GET` solicitar en el `Authorization` encabezado para llamar de forma segura `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Crear y eliminar tareas en la API de web

Siga el mismo patrón cuando envíe `POST` y `DELETE` las solicitudes de web API, usando la `BootstrapContext` para recuperar el token de inicio de sesión. Se ha implementado la acción crear para usted. Puede probar la finalización de la acción de eliminación en `TasksController.cs`.

## <a name="run-the-sample-app"></a>Ejecute la aplicación de ejemplo

Por último, crear y ejecutar la aplicación. Inicio de sesión iniciar sesión y crear tareas para el usuario que ha iniciado sesión en. Cerrar la sesión e inicie sesión como un usuario diferente. Crear tareas para ese usuario. Observe cómo las tareas están almacenados por usuario en la API, porque la API extrae la identidad del usuario desde el token que recibe.

Para referencia, en el ejemplo completo [se proporciona como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). También puede clonar desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
