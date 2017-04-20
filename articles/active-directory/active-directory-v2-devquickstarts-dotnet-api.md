<properties
    pageTitle="Azure AD versión 2.0 .NET Web API | Microsoft Azure"
    description="Cuentas de trabajo o escuela y cómo crear .NET MVC Web Api que acepte tokens de ambos Account personal de Microsoft."
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
    ms.date="10/10/2016"
    ms.author="dastrock"/>

# <a name="secure-an-mvc-web-api"></a>Proteger un web MVC API

Con Azure Active Directory del extremo de la versión 2.0, si desea proteger un Web API con tokens de acceso [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) , habilitar a los usuarios con la cuenta de Microsoft personal y trabajo o escuela cuentas acceso a la API de Web.

> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

Sitio Web de ASP.NET API, que puede realizar esto con software intermedio OWIN de Microsoft incluido en .NET Framework 4.5.  Aquí usaremos OWIN para crear una "Lista de tareas pendientes" MVC Web API que permite a los clientes crear y leer tareas de la lista de tareas pendientes de un usuario.  La API de web valida que las solicitudes entrantes contienen un token de acceso válidos y rechazar solicitudes que pasa la validación en una ruta protegida.  En este ejemplo se creó usando Visual Studio de 2015.

## <a name="download"></a>Descargar
El código de este tutorial se mantiene [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Para seguir a lo largo, puede [descargar el esquema de la aplicación como un .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) o clonar la estructura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

La aplicación de esquema incluye todo el código de texto reutilizable para una API simple, pero falta todas las partes relacionadas con la identidad. Si no desea seguir a lo largo, en su lugar puede clonar o [descargar el ejemplo completo](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Registrar una aplicación
Crear una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o siga estos [pasos detallados](active-directory-v2-app-registration.md).  Asegúrese de que:

- Copia hacia abajo el **Identificador de la aplicación** asignado a la aplicación, tendrá pronto.

Esta solución de visual studio también contiene "TodoListClient", que es una aplicación WPF sencilla.  La TodoListClient se usa para demostrar cómo un usuario signos de y cómo un cliente puede emitir solicitudes a la API de Web.  En este caso, la TodoListClient y la TodoListService están representados por la aplicación de la misma.  Para configurar la TodoListClient, también debe:

- Agregue la plataforma **Mobile** para su aplicación.


## <a name="install-owin"></a>Instalar OWIN

Ahora que ha registrado una aplicación, debe configurar su aplicación para comunicarse con el punto final de la versión 2.0 para validar entrantes solicitudes & tokens.

- Para comenzar, abra la solución y agregar los paquetes de NuGet OWIN software intermedio al proyecto TodoListService mediante la consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Configurar la autenticación de OAuth

- Agregar una clase de inicio de OWIN al proyecto TodoListService denominado `Startup.cs`.  Derecha, haga clic en el proyecto--> **Agregar** --> **Nuevo elemento** --> Buscar "OWIN".  El software de OWIN intermedio llamará la `Configuration(…)` método cuando se inicia la aplicación.
- Cambie la declaración de clase a `public partial class Startup` -ya hemos implementado una parte de esta clase para usted en otro archivo.  En la `Configuration(…)` método, realizar una llamada a ConfgureAuth(...) para configurar la autenticación para la aplicación web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

- Abra el archivo `App_Start\Startup.Auth.cs` e implementar la `ConfigureAuth(…)` método, que se configura la API de Web para que acepte tokens desde el extremo de la versión 2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

- Ahora puede usar `[Authorize]` atributos para proteger los controladores y acciones con autenticación de portador de OAuth 2.0.  Decorar los `Controllers\TodoListController.cs` clase con una etiqueta de autorizar.  Esto obligar al usuario a iniciar sesión antes de acceder a dicha página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Cuando un llamador autorizado correctamente llama a uno de los `TodoListController` API, la acción que tenga acceso a la información acerca de la persona que llama.  OWIN proporciona acceso a las solicitudes dentro del token portador a través de la `ClaimsPrincpal` objeto.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-   Por último, abra la `web.config` de archivos en la raíz del proyecto TodoListService y a continuación, escriba los valores de configuración de la `<appSettings>` sección.
  - Su `ida:Audience` es el **Identificador de la aplicación** de la aplicación que ha introducido en el portal.

## <a name="configure-the-client-app"></a>Configurar la aplicación de cliente
Para poder ver el servicio de lista Todo en acción, debe configurar al cliente de lista Todo, por lo que puede obtener tokens desde el extremo de la versión 2.0 y realizar llamadas al servicio.

- En el proyecto TodoListClient, abra `App.config` y escriba los valores de configuración de la `<appSettings>` sección.
  - Su `ida:ClientId` identificador de la aplicación copian desde el portal.

Por último, limpiar, crear y ejecutar cada proyecto!  Ahora tiene .NET MVC Web API que acepte tokens desde ambas cuentas personales de Microsoft y cuentas de trabajo o escuela.  Inicie sesión en la TodoListClient y llame a su sitio web api para agregar tareas a la lista de tareas pendientes del usuario.

Para referencia, el ejemplo completo (sin los valores de configuración) [se proporciona como un .zip aquí](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip)o puede clonar ella desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Pasos siguientes
Ahora puede desplazarse a temas adicionales.  Desea probar:

[Llamar a un sitio Web API desde una aplicación Web >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Para obtener recursos adicionales, consulte:
- [La Guía del programador de versión 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Etiqueta de StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtener actualizaciones de seguridad de nuestros productos

Le recomendamos que obtener notificaciones de cuándo se producen incidencias de seguridad, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscribirse a las alertas de aviso de seguridad.
