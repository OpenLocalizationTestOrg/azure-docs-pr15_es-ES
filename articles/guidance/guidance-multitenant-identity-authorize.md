<properties
   pageTitle="Autorización en las aplicaciones multiempresa | Microsoft Azure"
   description="Cómo llevar a cabo la autorización en una aplicación multiempresa"
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
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="role-based-and-resource-based-authorization-in-multitenant-applications"></a>Autorización basada en roles y recursos en las aplicaciones de multiempresa

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie]. También es una [aplicación de ejemplo] completa que acompaña a esta serie.

Nuestra [implementación de referencia] es una aplicación de ASP.NET Core 1.0. En este artículo veremos dos enfoques generales autorización, con la API de ASP.NET Core 1.0 autorización.

-   **Autorización basado en roles**. Autorizar una acción basándose en los roles asignados a un usuario. Por ejemplo, algunas acciones requieren un rol de administrador.
-   **Autorización de recursos**. Autorizar una acción basada en un recurso determinado. Por ejemplo, todos los recursos tienen un propietario. El propietario puede eliminar el recurso; no de otros usuarios.

Una aplicación típica emplea una combinación de ambos. Por ejemplo, para eliminar un recurso, el usuario debe ser el recurso propietario _o_ administrador.


## <a name="role-based-authorization"></a>Autorización basado en roles

Las [Encuestas Tailspin] [ Tailspin] aplicación define los siguientes roles:

- Administrador. Puede realizar todas las operaciones de CRUD en cualquier encuesta que pertenece a ese inquilino.
- Creador. Puede crear nuevas encuestas
- Lector. Puede leer las encuestas que pertenecen a ese inquilino

Funciones se aplican a _los usuarios_ de la aplicación. En la aplicación de encuestas, un usuario es un administrador, autor o lector.

Para obtener información sobre cómo definir y administrar las funciones, consulte [funciones de aplicación].

Independientemente de cómo administrar los roles, el código de autorización tendrá un aspecto similar. Núcleo de ASP.NET 1.0 presenta una abstracción denominada [directivas de autorización de][policies]. Con esta característica, defina las directivas de autorización de código y, a continuación, aplicar estas directivas a acciones del controlador. La directiva está desconectada del controlador.

### <a name="create-policies"></a>Crear directivas

Para definir una directiva, cree primero una clase que implementa `IAuthorizationRequirement`. Es más fácil derivan `AuthorizationHandler`. En la `Handle` método, examine la claim(s) relevante.

Aquí tiene un ejemplo de la aplicación Tailspin encuestas:

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] Consulte [SurveyCreatorRequirement.cs]

Esta clase define el requisito de un usuario crear una encuesta nueva. El usuario debe estar en la función SurveyAdmin o SurveyCreator.

En la clase de inicio, definir una directiva con nombre que incluye uno o más requisitos. Si hay varios requisitos, el usuario debe cumplir _cada_ requisito autorización. El código siguiente define dos directivas:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] Consulte [Startup.cs]

Este código también establece el esquema de autenticación, que indica a qué software intermedio de autenticación debe ejecutar si se produce un error de autorización de ASP.NET. En este caso, se especifica el software intermedio autenticación de cookies, porque el software de autenticación de cookie intermedio puede redirigir al usuario a una página de "Prohibido". La ubicación de la página de prohibido se establece en la opción de AccessDeniedPath para el software intermedio cookie; vea [Configurar el software intermedio de autenticación].

### <a name="authorize-controller-actions"></a>Autorizar acciones del controlador

Por último, para autorizar una acción en un controlador MVC, establezca la directiva la `Authorize` atributo:

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

En versiones anteriores de ASP.NET, ¿establezca la propiedad de **Roles** en el atributo:

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Esto aún se admite en ASP.NET Core 1.0, pero tiene algunas desventajas en comparación con las directivas de autorización:

-   Se supone un tipo de notificación determinado. Pueden comprobar las directivas para cualquier tipo de notificación. Las funciones son solo un tipo de notificación.
-   El nombre de la función está codificada en el atributo. Con las directivas de la lógica de autorización es desde un único lugar, facilitará actualizar o carga par de valores de configuración.
-   Las directivas permiten decisiones de autorización más complejas (por ejemplo, edad > = 21) que no se puede expresar por pertenencia a una función simple.

## <a name="resource-based-authorization"></a>Autorización de recurso

_Autorización basada en el recurso_ se produce siempre que depende de la autorización de un recurso específico que se verán afectado por una operación. En la aplicación Tailspin encuestas, cada encuesta tiene un propietario y colaboradores de cero a varios.

-   El propietario puede leer, actualizar, eliminar, publicar y anular la publicación de la encuesta.
-   El propietario puede asignar a colaboradores a la encuesta.
-   Los colaboradores pueden leer y actualizar la encuesta.

Tenga en cuenta que "propietario" y "Colaborador" no son funciones de aplicación. se almacenan por la encuesta, en la base de datos de aplicación. Para comprobar si un usuario puede eliminar una encuesta, por ejemplo, la aplicación comprueba si el usuario es el propietario de dicha encuesta.

En ASP.NET Core 1.0, implementar autorización de recurso deriva de **AuthorizationHandler** y reemplaza el método de **controlar** .

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Tenga en cuenta que esta clase inflexible para objetos de la encuesta.  Registrar la clase para DI en el inicio:

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Para realizar comprobaciones de autorización, utilice la interfaz de **IAuthorizationService** , que puede insertar en los controladores. El código siguiente comprueba si un usuario puede leer una encuesta:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Como se pasa en un `Survey` objeto, esta llamada llamará la `SurveyAuthorizationHandler`.

En el código de autorización, es un buen enfoque agregado de todos los permisos del usuario basado en roles y recursos y, a continuación, active establece el agregado contra la operación deseada.
Aquí tenemos un ejemplo de la aplicación de encuestas. La aplicación define varios tipos de permisos:

- Administrador
- Colaborador
- Creador
- Propietario
- Lector

La aplicación también define un conjunto de operaciones posibles en encuestas:

- Crear
- Lectura
- Actualización
- Eliminar
- Publicar
- Unpublsh

El código siguiente crea una lista de permisos para un usuario concreto y la encuesta. Observe que este código busca en funciones de aplicación del usuario y de los campos de colaborador o propietario de la encuesta.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] Vea [SurveyAuthorizationHandler.cs].

En una aplicación de múltiples arrendatario, debe asegurarse de que los permisos no se "desvían" a los datos del otro inquilino. En la aplicación de encuestas, los permisos de colaborador se permiten a través de los inquilinos &mdash; puede asignar a alguien desde otro inquilino como un contriubutor. Otros tipos de permisos están limitados a recursos de inquilino de ese usuario. Para aplicar este requisito, el código comprueba el identificador del inquilino antes de conceder el permiso. (El `TenantId` campo como asignado cuando se crea la encuesta.)

El siguiente paso es buscar la operación (leer, actualizar, eliminar, etcetera) con los permisos. La aplicación de encuestas implementa este paso con una tabla de búsqueda de funciones:

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```


## <a name="next-steps"></a>Pasos siguientes

- Lea el siguiente artículo de esta serie: [proteger un servidor web API en una aplicación multiempresa][web-api]
- Para obtener más información acerca de la autorización de recursos basándose en ASP.NET 1.0 principales, vea [Recursos de autorización][rbac].

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte de una serie]: guidance-multitenant-identity.md
[Funciones de aplicación]: guidance-multitenant-identity-app-roles.md
[policies]: https://docs.asp.net/en/latest/security/authorization/policies.html
[rbac]: https://docs.asp.net/en/latest/security/authorization/resourcebased.html
[implementación de referencia]: guidance-multitenant-identity-tailspin.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Configurar el software intermedio de autenticación]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[web-api]: guidance-multitenant-identity-web-api.md
