<properties
   pageTitle="Suscripción e inquilinos integrado en aplicaciones multiempresa | Microsoft Azure"
   description="Cómo inquilinos incorporados en una aplicación multiempresa"
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

# <a name="sign-up-and-tenant-onboarding-in-a-multitenant-application"></a>Suscripción e integrado en una aplicación de multiempresa de inquilinos

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie]. También es una [aplicación de ejemplo] completa que acompaña a esta serie.

En este artículo se describe cómo implementar una _suscripción a un_ proceso en una aplicación de múltiples arrendatario, que permite a un cliente de inicio de sesión de su organización para la aplicación.
Hay varias razones para implementar un proceso de registro:

-   Permitir que un administrador de AD a consentimiento para que la organización del cliente completo usar la aplicación.
-   Recopilar pago con tarjeta de crédito u otra información del cliente.
-   Realizar cualquier instalación de una sola vez por inquilino necesario para la aplicación.

## <a name="admin-consent-and-azure-ad-permissions"></a>Permisos de Azure AD y consentimiento del administrador

Para autenticar con Azure AD, una aplicación necesita obtener acceso al directorio del usuario. Como mínimo, la aplicación necesita permiso de lectura del perfil del usuario. La primera vez que un usuario inicia sesión, Azure AD muestra una página de consentimiento que se enumera los permisos que se solicita. Haciendo clic en **Aceptar**, el usuario concede permiso a la aplicación.

De forma predeterminada, se concede consentimiento en cada usuario. Cada usuario que inicia sesión ve la página consentimiento. Sin embargo, Azure AD también admite _consentimiento de administración_, que permite a un administrador de AD a consentimiento para toda la organización.

Cuando se usa el flujo de consentimiento de administración, el consentimiento página indica que el Administrador de AD concede permiso en nombre de todo el inquilino:

![Petición de consentimiento del administrador](media/guidance-multitenant-identity/admin-consent.png)

Después de que el administrador hace clic en **Aceptar**, otros usuarios en el mismo inquilino pueden iniciar sesión y Azure AD omitirá la pantalla de consentimiento.

Sólo un administrador de AD puede dar consentimiento de administración, como se concede permiso en nombre de toda la organización. Si un usuario no administrador intenta autenticar con el flujo de consentimiento del administrador, Azure AD muestra un error:

![Error de consentimiento](media/guidance-multitenant-identity/consent-error.png)

Si la aplicación requiere permisos adicionales en un momento posterior, el cliente deberá suscribirse de nuevo y consentimiento a los permisos actualizados.  

## <a name="implementing-tenant-sign-up"></a>Implementación de suscripción a un inquilino

Para las [Encuestas Tailspin] [ Tailspin] aplicación, se ha definido varios requisitos para el proceso de registro:

-   Un inquilino debe registrarse antes de que los usuarios puedan iniciar sesión.
-   Suscripción usa el flujo de consentimiento del administrador.
-   Suscripción agrega a inquilino de usuario a la base de datos de aplicación.
-   Después de un inquilino registra el tiempo de espera, la aplicación muestra una página de integrado.

En esta sección, le guiaremos a través de la implementación del proceso de suscripción.
Es importante comprender que "suscribirse" frente a "iniciar sesión" es un concepto de aplicación. Durante el flujo de autenticación Azure AD no inherente saber si el usuario está en el proceso de suscripción. Depende de la aplicación para realizar un seguimiento del contexto.

Cuando un usuario anónimo visita la aplicación de encuestas, el usuario es se muestra dos botones, uno para iniciar sesión y otro para "inscribir su compañía" (registrarse).

![Página de inicio de sesión de la aplicación](media/guidance-multitenant-identity/sign-up-page.png)

Estos botones invocar acciones en la clase [AccountController] .

La `SignIn` acción devuelve un **ChallegeResult**, que hace que el software OpenID conectar intermedio redirigir a los extremos de autenticación. Esta es la manera predeterminada desencadenador autenticación en ASP.NET Core 1.0.  

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

Comparar ahora la `SignUp` acción:

```csharp
[AllowAnonymous]
public IActionResult SignUp()
{
    // Workaround for https://github.com/aspnet/Security/issues/546
    HttpContext.Items.Add("signup", "true");

    var state = new Dictionary<string, string> { { "signup", "true" }};
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties(state)
        {
            RedirectUri = Url.Action(nameof(SignUpCallback), "Account")
        });
}
```

Como `SignIn`, el `SignUp` acción también devuelve un `ChallengeResult`. Pero esta vez, agregaremos un fragmento de información de estado a la `AuthenticationProperties` en el `ChallengeResult`:

-   el registro: un indicador booleano, que indica que el usuario ha iniciado el proceso de suscripción.

La información de estado en `AuthenticationProperties` se agrega al parámetro de [estado] OpenID conectarse, que redondear viajes durante el flujo de autenticación.

![Parámetro de estado](media/guidance-multitenant-identity/state-parameter.png)

Después de que el usuario se autentica en Azure AD y se redirige a la aplicación, el vale de autenticación contiene el estado. Estamos usando este hecho para asegurarse de que el valor de "suscripción" se conserva en el flujo de autenticación completo.

## <a name="adding-the-admin-consent-prompt"></a>Agregar el símbolo del sistema de administración consentimiento

En Azure AD, se activa el flujo de consentimiento administración agregando un parámetro "instrucción" a la cadena de consulta en la solicitud de autenticación:

```
/authorize?prompt=admin_consent&...
```

La aplicación de encuestas agrega el símbolo del sistema durante la `RedirectToAuthenticationEndpoint` evento. Este evento se denomina justo antes de que el software intermedio redirige al extremo de autenticación.

```csharp
public override Task RedirectToAuthenticationEndpoint(RedirectContext context)
{
    if (context.IsSigningUp())
    {
        context.ProtocolMessage.Prompt = "admin_consent";
    }

    _logger.RedirectToIdentityProvider();
    return Task.FromResult(0);
}
```

> [AZURE.NOTE] Vea [SurveyAuthenticationEvents.cs].

Establecer` ProtocolMessage.Prompt` indica el software intermedio para agregar el parámetro "instrucción" a la solicitud de autenticación.

Tenga en cuenta que sólo se necesita el símbolo del sistema durante la suscripción. El inicio de sesión normal no debe incluir él. Para distinguir entre ellas, se comprueba si la `signup` valor en el estado de autenticación. El método de extensión siguiente comprueba esta condición:

```csharp
internal static bool IsSigningUp(this BaseControlContext context)
{
    Guard.ArgumentNotNull(context, nameof(context));

    string signupValue;
    object obj;
    // Check the HTTP context and convert to string
    if (context.HttpContext.Items.TryGetValue("signup", out obj))
    {
        signupValue = (string)obj;
    }
    else
    {
        // It's not in the HTTP context, so check the authentication ticket.  If it's not there, we aren't signing up.
        if ((context.AuthenticationTicket == null) ||
            (!context.AuthenticationTicket.Properties.Items.TryGetValue("signup", out signupValue)))
        {
            return false;
        }
    }

    // We have found the value, so see if it's valid
    bool isSigningUp;
    if (!bool.TryParse(signupValue, out isSigningUp))
    {
        // The value for signup is not a valid boolean, throw                
        throw new InvalidOperationException($"'{signupValue}' is an invalid boolean value");
    }

    return isSigningUp;
}
```

> [AZURE.NOTE] Vea [BaseControlContextExtensions.cs].

> [AZURE.NOTE] Nota: Este código incluye una solución para un error conocido en ASP.NET Core 1.0 RC1. En la `RedirectToAuthenticationEndpoint` evento, hay ninguna manera para obtener las propiedades de autenticación que contiene el estado de "suscripción". Como solución alternativa, el `AccountController.SignUp` método también coloca el estado "suscripción" en la `HttpContext`. Esto funciona porque `RedirectToAuthenticationEndpoint` ocurre antes de la redirección, todavía tenemos el mismo `HttpContext`.

## <a name="registering-a-tenant"></a>Registrar un inquilino

La aplicación de encuestas almacena información sobre cada inquilino y usuario en la base de datos de aplicación.

![Tabla de inquilinos](media/guidance-multitenant-identity/tenant-table.png)

En la tabla inquilino, IssuerValue es el valor de la notificación de emisor del inquilino. Azure AD, esto es `https://sts.windows.net/<tentantID>` y da como resultado un valor único por cada inquilino.

Cuando un nuevo inquilino inscribe, la aplicación de encuestas escribe un registro del inquilino a la base de datos. Esto ocurre dentro de la `AuthenticationValidated` evento. (No hacerlo antes de este evento, como el token de identificador no validar todavía, por lo que no puede confiar en los valores de notificación. Consulte [autenticación].

Este es el código correspondiente de la aplicación de encuestas:

```csharp
public override async Task AuthenticationValidated(AuthenticationValidatedContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var userId = principal.GetObjectIdentifierValue();
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    _logger.AuthenticationValidated(userId, issuerValue);

    // Normalize the claims first.
    NormalizeClaims(principal);
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue)
        .ConfigureAwait(false);

    if (context.IsSigningUp())
    {
        // Originally, we were checking to see if the tenant was non-null, however, this would not allow
        // permission changes to the application in AAD since a re-consent may be required.  Now we just don't
        // try to recreate the tenant.
        if (tenant == null)
        {
            tenant = await SignUpTenantAsync(context, tenantManager)
                .ConfigureAwait(false);
        }

        // In this case, we need to go ahead and set up the user signing us up.
        await CreateOrUpdateUserAsync(context.AuthenticationTicket, userManager, tenant)
            .ConfigureAwait(false);
    }
    else
    {
        if (tenant == null)
        {
            _logger.UnregisteredUserSignInAttempted(userId, issuerValue);
            throw new SecurityTokenValidationException($"Tenant {issuerValue} is not registered");
        }

        await CreateOrUpdateUserAsync(context.AuthenticationTicket, userManager, tenant)
            .ConfigureAwait(false);
    }
}
```

> [AZURE.NOTE] Vea [SurveyAuthenticationEvents.cs].

Este código hace lo siguiente:

1.  Compruebe si el valor de emisor del inquilino está ya en la base de datos. Si no ha firmado el inquilino, `FindByIssuerValueAsync` devuelve null.
2.  Si el usuario se suscribe:
  1.    Agregar el inquilino a la base de datos (`SignUpTenantAsync`).
  2.    Agregar el usuario autenticado a la base de datos (`CreateOrUpdateUserAsync`).
3.  En caso contrario, complete el flujo normal de inicio de sesión:
  1.    Si no se encontró el emisor del inquilino en la base de datos, significa que no está registrado el inquilino y el cliente necesita registrarse. En ese caso, se produce una excepción para hacer que la autenticación no se realice correctamente.
  2.    En caso contrario, cree un registro de base de datos para este usuario, si no hay uno ya (`CreateOrUpdateUserAsync`).

Éste es el método de [SignUpTenantAsync] que agrega al inquilino a la base de datos.

```csharp
private async Task<Tenant> SignUpTenantAsync(BaseControlContext context, TenantManager tenantManager)
{
    Guard.ArgumentNotNull(context, nameof(context));
    Guard.ArgumentNotNull(tenantManager, nameof(tenantManager));

    var principal = context.AuthenticationTicket.Principal;
    var issuerValue = principal.GetIssuerValue();
    var tenant = new Tenant
    {
        IssuerValue = issuerValue,
        Created = DateTimeOffset.UtcNow
    };

    try
    {
        await tenantManager.CreateAsync(tenant)
            .ConfigureAwait(false);
    }
    catch(Exception ex)
    {
        _logger.SignUpTenantFailed(principal.GetObjectIdentifierValue(), issuerValue, ex);
        throw;
    }

    return tenant;
}
```

Aquí es un resumen del flujo de suscripción a todo en la aplicación de encuestas:

1.  El usuario hace clic en el botón de **Inicio de sesión** .
2.  La `AccountController.SignUp` acción devuelve un resultado de desafío.  El estado de autenticación incluye el valor de "suscripción".
3.  En la `RedirectToAuthenticationEndpoint` evento, agregue el `admin_consent` símbolo del sistema.
4.  El software intermedio OpenID conectar redirige a Azure AD y el usuario se autentica.
5.  En la `AuthenticationValidated` evento, busque el estado "suscripción".
6.  Agregar al inquilino a la base de datos.

## <a name="next-steps"></a>Pasos siguientes

- Lea el siguiente artículo de esta serie: [funciones de aplicación en aplicaciones multiempresa][app roles]


<!-- Links -->
[app roles]: guidance-multitenant-identity-app-roles.md
[Tailspin]: guidance-multitenant-identity-tailspin.md
[parte de una serie]: guidance-multitenant-identity.md
[AccountController]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Controllers/AccountController.cs
[estado]: http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest
[SurveyAuthenticationEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs
[BaseControlContextExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/BaseControlContextExtensions.cs
[Autenticación]: guidance-multitenant-identity-authenticate.md
[SignUpTenantAsync]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
