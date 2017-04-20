<properties
    pageTitle="Azure AD B2C | Microsoft Azure"
    description="Cómo crear una API de Web .NET mediante B2C de Azure Active Directory, protegido mediante símbolos de acceso OAuth 2.0 para la autenticación."
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
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active B2C de directorio: Crear un sitio web de .NET API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con B2C de Azure Active Directory (AD Azure), puede proteger un web API mediante símbolos de acceso OAuth 2.0. Estos tokens permitir que las aplicaciones cliente que utilizan Azure AD B2C autenticar a la API. En este artículo se muestra cómo crear una API de "lista de tareas pendientes".NET controlador de vista de modelo (MVC) que permite a los usuarios a las tareas CRUD. La web API está protegida mediante Azure AD B2C y solo permite a los usuarios administrar su lista de tareas pendientes autenticados.

## <a name="create-an-azure-ad-b2c-directory"></a>Crear un directorio de Azure AD B2C

Antes de poder usar Azure AD B2C, debe crear un directorio o de inquilinos. Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos y más. Si no tiene uno ya, [Cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar en esta guía.

## <a name="create-an-application"></a>Crear una aplicación

A continuación, deberá crear una aplicación en el directorio B2C. Esto le da información de Azure AD que necesita comunicarse de forma segura con la aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación web** o **web API** en la aplicación.
- Usar el **identificador uniforme de recursos de redirigir** `https://localhost:44316/` para la aplicación web. Esta es la ubicación predeterminada del cliente de aplicación web de este ejemplo de código.
- Copie el **identificador de la aplicación** que se asigna a la aplicación. Necesitará más adelante.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define por una [Directiva](active-directory-b2c-reference-policies.md). El cliente de este ejemplo de código contiene tres experiencias de identidad: inicio de sesión, inicio de sesión y Editar perfil. Debe crear una directiva para cada tipo, como se describe en el [artículo de referencia de la directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando crea su tres directivas, asegúrese de:

- Elija la **Suscripción a un identificador de usuario** o de **suscripción de correo electrónico** en el módulo de proveedores de identidades.
- En la directiva de suscripción, elija **nombre para mostrar** y otros atributos de suscripción.
- Elija las notificaciones de **nombre para mostrar** y el **Identificador de objeto** como notificaciones de la aplicación para cada directiva. Puede elegir otras reclamaciones también.
- Copiar el **nombre** de cada directiva después de crearlo. Necesitará estos nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de haber creado correctamente las tres directivas, está listo para crear la aplicación.

## <a name="download-the-code"></a>Descargar el código

El código de este tutorial [se mantiene según GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Para generar el ejemplo mientras avanza, puede [descargar un proyecto de esquema como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). También puede clonar la estructura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

La aplicación completada también está [disponible como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) o en la `complete` rama del mismo repositorio.

Después de descargar el código de ejemplo, abra el archivo de sln de Visual Studio para empezar. El archivo de solución contiene dos proyectos: `TaskWebApp` y `TaskService`. `TaskWebApp`es una aplicación web MVC que el usuario interactúe con. `TaskService`es el sitio web de back-end de la aplicación API que almacena la lista de tareas pendientes de cada usuario.

## <a name="configure-the-task-web-app"></a>Configurar la aplicación web de tarea

Cuando un usuario interactúe con `TaskWebApp`, el cliente envía solicitudes a Azure AD y vuelve tokens que se pueden usar para llamar a la `TaskService` web API. Para el usuario de inicio de sesión y obtener tokens, debe proporcionar `TaskWebApp` con información sobre la aplicación. En la `TaskWebApp` proyecto, abrir la `web.config` de archivos en la raíz del proyecto y reemplazar los valores de la `<appSettings>` sección.  Puede dejar la `AadInstance`, `RedirectUri`, y `TaskServiceUrl` valores como-es.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Este artículo trata sobre la creación del `TaskWebApp` cliente.  Para obtener información sobre cómo crear una aplicación web mediante Azure AD B2C, vea [nuestro tutorial de aplicación web de .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Proteger la API

Cuando tenga un cliente que llama a la API en nombre de los usuarios, puede proteger `TaskService` utilizando tokens portador de OAuth 2.0. La API puede aceptar y validar tokens mediante la interfaz de Web abierta de Microsoft para la biblioteca de .NET (OWIN).

### <a name="install-owin"></a>Instalar OWIN
Empiece por instalar la canalización de autenticación OWIN OAuth:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Escriba los detalles de B2C
Abrir la `web.config` archivo en la raíz de la `TaskService` project y reemplazar los valores de la `<appSettings>` sección. Estos valores se usan en toda la biblioteca API y OWIN.  Puede dejar la `AadInstance` valor sin modificar.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Agregar una clase de inicio OWIN
Agregar una clase de inicio OWIN a la `TaskService` proyecto denominado `Startup.cs`.  Haga clic en el proyecto, seleccione **Agregar** y **Nuevo elemento**y, a continuación, busque OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Configurar la autenticación de OAuth 2.0
Abra el archivo `App_Start\Startup.Auth.cs`e implementar la `ConfigureAuth(...)` método:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Proteger el controlador de tarea
Después de que la aplicación está configurada para usar autenticación de OAuth 2.0, puede proteger su web API agregando una `[Authorize]` etiqueta en el controlador de la tarea. Éste es el controlador donde todos los manipulación de lista de tareas pendientes lleve a cabo, por lo que debe proteger el controlador todo en el nivel de clase. También puede agregar la `[Authorize]` etiqueta acciones individuales para un control más detallado.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Obtener información de usuario de token
`TasksController`las tareas se almacena en una base de datos donde cada tarea tiene un usuario asociado que es "propietario" de la tarea. El propietario se identifica mediante el **identificador de objeto**. (Este es el motivo que sea necesario para agregar el identificador de objeto como una aplicación reclamación en todas las directivas de.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Ejecute la aplicación de ejemplo

Por último, crear y ejecutar ambos `TaskWebApp` y `TaskService`. Suscríbase a la aplicación con una dirección de correo electrónico o nombre de usuario. Crear algunas tareas en la lista de tareas pendientes del usuario y observe cómo se conservan en la API después de detener y reiniciar al cliente.

## <a name="edit-your-policies"></a>Modificar las directivas

Tras asegurarse una API usando Azure AD B2C, puede experimentar con las directivas de la aplicación y ver los efectos (o carecen de sus) en la API. Puede manipular las notificaciones de la aplicación en las directivas y cambiar la información de usuario que está disponible en la web API. Las reclamaciones que agregue estará disponibles para su web .NET MVC API en la `ClaimsPrincipal` objeto, como se describe más adelante en este artículo.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->
