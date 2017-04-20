<properties
    pageTitle="Introducción de .NET de Azure AD | Microsoft Azure"
    description="Cómo crear .NET MVC Web API que se integra con Azure AD para la autenticación y la autorización."
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


# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Proteger una API Web usando el token portador de Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si desea crear una aplicación que proporciona acceso a los recursos protegidos debe saber cómo proteger los recursos frente a accesos no autorizados.
Azure AD facilita sencillas y fáciles de proteger un web API con Tokens de acceso de 2.0 portador de OAuth solo unas pocas líneas de código.

En las aplicaciones web de Asp.NET, puede realizar esto con la implementación de Microsoft del software intermedio dirigido a la Comunidad OWIN incluido en .NET Framework 4.5.  Aquí usaremos OWIN para crear un sitio web de "Lista de tareas pendientes" API que:
-   Designa qué API está protegida.
-   Valide que las llamadas de Web API contienen un Token de acceso válido.

Para ello, necesitará:

1. Registrar una aplicación con Azure AD
2. Configurar la aplicación para usar la canalización de autenticación OWIN.
3. Configurar una aplicación de cliente para llamar a la a hacer lista Web API

Para comenzar, [Descargue la estructura de la aplicación](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) o [descargar el ejemplo completo](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Cada uno de ellos es una solución de Visual Studio de 2013.  También necesitará a un inquilino de Azure AD en el que se va a registrar la aplicación.  Si aún no tiene una, [Obtenga información sobre cómo obtener una](active-directory-howto-tenant.md).


## <a name="1--register-an-application-with-azure-ad"></a>*1. registrar una aplicación con Azure AD*
Para proteger su aplicación, primero deberá crear una aplicación en su inquilino y proporcionar Azure AD con algunas partes clave de la información.

-   Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com)
-   En la barra de navegación izquierda, haga clic en **Active Directory**
-   Seleccione a un inquilino en el que se va a registrar la aplicación.
-   Haga clic en la pestaña de **aplicaciones** y haga clic en **Agregar** en la Bandeja de la parte inferior.
-   Siga las indicaciones y cree una nueva **aplicación Web o WebAPI**.
    -   El **nombre** de la aplicación describe la aplicación a los usuarios finales.  Escriba "Para la lista de servicio".
    -   La **Redirección Uri** es una combinación de combinación y la cadena que Azure AD usaría para devolver los símbolos de la aplicación solicitada. Escriba `https://localhost:44321/` para este valor.
-   Una vez haya completado el registro, vaya a la ficha **Configurar** y busque el campo **URI de ID de aplicación** .  Especifique un identificador de inquilino específicos para este valor, por ejemplo`https://contoso.onmicrosoft.com/TodoListService`
- Guardar la configuración.  Dejar abierto el portal, también deberá registrar breve de la aplicación de cliente.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurar la aplicación para usar la canalización de autenticación OWIN*

Ahora que ha registrado una aplicación con Azure AD, debe configurar la aplicación para comunicarse con Azure AD para validar entrantes solicitudes & tokens.

-   Para comenzar, abra la solución y agregar los paquetes de NuGet OWIN software intermedio al proyecto TodoListService mediante la consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-   Agregar una clase de inicio de OWIN al proyecto TodoListService denominado `Startup.cs`.  Derecha, haga clic en el proyecto--> **Agregar** --> **Nuevo elemento** --> Buscar "OWIN".  El software de OWIN intermedio llamará la `Configuration(…)` método cuando se inicia la aplicación.
-   Cambie la declaración de clase a `public partial class Startup` -ya hemos implementado una parte de esta clase para usted en otro archivo.  En la `Configuration(…)` método, realizar una llamada a ConfgureAuth(...) para configurar la autenticación para la aplicación web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Abra el archivo `App_Start\Startup.Auth.cs` e implementar la `ConfigureAuth(…)` método.  Los parámetros que se proporcionan en `WindowsAzureActiveDirectoryBearerAuthenticationOptions` servirá como las coordenadas de la aplicación para comunicarse con Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-   Ahora puede usar `[Authorize]` atributos para proteger los controladores y acciones con autenticación de portador JWT.  Decorar los `Controllers\TodoListController.cs` clase con una etiqueta de autorizar.  Esto obligar al usuario a iniciar sesión antes de acceder a dicha página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Cuando un llamador autorizado correctamente llama a uno de los `TodoListController` API, la acción que tenga acceso a la información acerca de la persona que llama.  OWIN proporciona acceso a las solicitudes dentro del token portador a través de la `ClaimsPrincpal` objeto.  
- Es un requisito común de web API validar los "ámbitos" presentes en el token - Esto garantiza que el usuario final ha aceptado los permisos necesarios para tener acceso al servicio de lista Todo:

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-   Por último, abra la `web.config` de archivos en la raíz del proyecto TodoListService y a continuación, escriba los valores de configuración de la `<appSettings>` sección.
  - La `ida:Tenant` es el nombre de su inquilino de Azure AD, por ejemplo, "contoso.onmicrosoft.com".
  - Su `ida:Audience` es el URI de identificador de aplicación de la aplicación que ha introducido en el Portal de Azure.

## <a name="3--configure-a-client-application--run-the-service"></a>*3. configurar una aplicación de cliente y ejecutar el servicio*
Para poder ver el servicio de lista Todo en acción, debe configurar al cliente de lista Todo, por lo que puede obtener tokens de AAD y realizar llamadas al servicio.

- Volver al [Portal de administración de Azure](https://manage.windowsazure.com)
- Crear una nueva aplicación en su inquilino de Azure AD y seleccione **Aplicación nativa de cliente** en el símbolo del sistema resultante.
    -   El **nombre** de la aplicación describe la aplicación a los usuarios finales
    -   Escriba `http://TodoListClient/` para el valor de **Redirección Uri** .
- Una vez haya completado el registro, AAD asignar la aplicación de un único **Identificador de cliente**. Necesitará este valor en los siguientes pasos, por lo tanto cópiela desde la ficha configurar.
- También en la ficha **Configurar** , busque la sección "Permisos a otras aplicaciones". Haga clic en "Agregar aplicación". Seleccione "Todas las aplicaciones" en el menú desplegable "Mostrar" y haga clic en la marca de verificación superior. Busque y haga clic en el que hacer lista servicio y haga clic en la marca de verificación de la parte inferior para agregar la aplicación. Seleccione "Acceso a hacer lista servicio" en la lista desplegable "Delegar permisos" y guardar la configuración.


- En Visual Studio, abra `App.config` en la TodoListClient del proyecto y escriba los valores de configuración de la `<appSettings>` sección.
  - La `ida:Tenant` es el nombre de su inquilino de Azure AD, por ejemplo, "contoso.onmicrosoft.com".
  - Su `ida:ClientId` ID de aplicación que copió desde el Portal de Azure.
  - Su `todo:TodoListResourceId` es el URI de identificador de aplicación de la aplicación de servicio de lista para hacer que ha introducido en el Portal de Azure.

Por último, limpiar, crear y ejecutar cada proyecto!  Si todavía no lo ha hecho, ahora es el momento de crear un nuevo usuario en el inquilino con un *. dominio onmicrosoft.com.  Inicie sesión en el cliente de la lista de tareas pendientes con dicho usuario y agregar algunas tareas a la lista de tareas pendientes del usuario.

Para referencia, se proporciona el ejemplo completo (sin los valores de configuración) [aquí](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Ahora puede mover a más escenarios de identidad adicional.

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
