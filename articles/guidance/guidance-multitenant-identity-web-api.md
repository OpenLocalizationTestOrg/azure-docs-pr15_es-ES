<properties
   pageTitle="Proteger un back-end web API en una aplicación multiempresa | Microsoft Azure"
   description="Cómo proteger un web API de back-end"
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

# <a name="securing-a-backend-web-api-in-a-multitenant-application"></a>Proteger un back-end web API en una aplicación multiempresa

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie]. También es una [aplicación de ejemplo] completa que acompaña a esta serie.

La aplicación de [Encuestas Tailspin] utiliza un sitio web de back-end API para administrar operaciones de CRUD en encuestas. Por ejemplo, cuando un usuario hace clic en "Mis encuestas", la aplicación web envía una solicitud HTTP a la API de web:

```
GET /users/{userId}/surveys
```

La API de web devuelve un objeto JSON:

```
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

La API de web no admite solicitudes anónimas, por lo que debe autenticar la aplicación web mediante el token portador de OAuth 2.

> [AZURE.NOTE] Se trata de un escenario de servidor a servidor. La aplicación no realizar llamadas AJAX a la API del cliente del explorador.

Hay dos enfoques principales que puede usar:

- Delegar la identidad de usuario. La aplicación web autentica con la identidad del usuario.
- Identidad de la aplicación. La aplicación web autentica con su ID de cliente, usando OAuth2 flujo de credenciales de cliente.

La aplicación Tailspin implementa la identidad de usuario delegado. Estas son las diferencias principales:

**Identidad de usuario delegado**

- El token portador enviado a la API de web contiene la identidad del usuario.
- La web API toma decisiones de autorización basadas en la identidad de usuario.
- La aplicación web debe controlar 403 (prohibido) errores desde la web API, si el usuario no está autorizado para realizar una acción.
- Normalmente, la aplicación web aún tiene algunas decisiones de autorización que afectan a la interfaz de usuario, como mostrar u ocultar elementos de interfaz de usuario).
- La API de web potencialmente puede ser usada por los clientes sin confianza, como una aplicación de JavaScript o una aplicación nativa de cliente.

**Identidad de la aplicación**

- La API de web no obtener información sobre el usuario.
- La API de web no puede realizar ninguna autorización basada en la identidad del usuario. Todas las decisiones de autorización realizadas por la aplicación web.  
- No se puede utilizar la API de web por un cliente de confianza (JavaScript o aplicación nativa cliente).
- Este enfoque puede ser un poco más fácil implementar, porque no hay ninguna lógica de autorización de la API de Web.

En uno de los métodos, la aplicación web debe obtener un token de acceso, que es la credencial necesaria para llamar a la API de web.

- Para identidad de usuario delegado, el token debe proceder de la IDP, que puede emitir un token en nombre del usuario.

- Las credenciales de cliente, una aplicación puede obtener el token de la IDP o su propio servidor token de host. (Pero no escribir un servidor token desde cero, usar un marco bien probado como [IdentityServer3].) Si autenticar con Azure AD, se recomienda para obtener el token de acceso de Azure AD, incluso con el flujo de credenciales de cliente.

El resto de este artículo se supone que la aplicación se autentica con Azure AD.

![Obtener el token de acceso](media/guidance-multitenant-identity/access-token.png)

## <a name="register-the-web-api-in-azure-ad"></a>Registrar la web API en Azure AD

Fin de Azure AD a emitir un token portador de la API de web, debe configurar algunas de las cosas en Azure AD.

1. [Registrar la web API en Azure AD].

2. Agregar el identificador de cliente de la aplicación web a la manifiestos de las aplicaciones web API de la `knownClientApplications` propiedad. Consulte [actualizar los manifiestos de aplicación].

3. [Conceder permiso para llamar a la API de web la aplicación web].

  En el Portal de administración de Azure, puede establecer dos tipos de permisos: "Permisos de aplicación" para la identidad de la aplicación (flujo de credenciales de cliente), o "Delegar permisos" de identidad de usuario delegado.

  ![Permisos de delegado](media/guidance-multitenant-identity/delegated-permissions.png)

## <a name="getting-an-access-token"></a>Obtener un token de acceso

Antes de llamar a la API de web, la aplicación web obtiene que un acceso token de Azure AD. En una aplicación. NET, use la [Biblioteca de autenticación de AD Azure (ADAL) para .NET][ADAL].

En el flujo de código de autorización de OAuth 2, la aplicación cambia un código de autorización para un token de acceso. El código siguiente utiliza ADAL para obtener el token de acceso. Llamar a este código durante la `AuthorizationCodeReceived` evento.

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.   
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

Estos son los distintos parámetros que son necesarios:

- `authority`. Derivado del identificador del inquilino del usuario sesión. (No el identificador de inquilinos del proveedor SaaS)  
- `authorizationCode`. el código de autenticación que obtuvo el IDP.
- `clientId`. Identificador de cliente. de la aplicación web
- `clientSecret`. Secreto de cliente de la aplicación web.
- `redirectUri`. La redirección URI que establezca para OpenID conectarse. Esto es donde el IDP devuelve la llamada con el token.
- `resourceID`. URI de identificador de aplicación de la web API, que creó cuando registra la web API en Azure AD
- `tokenCache`. Un objeto que almacena los identificadores de acceso. Vea [Token de almacenamiento en caché].

Si `AcquireTokenByAuthorizationCodeAsync` se realiza correctamente, ADAL almacena el token. Más adelante, puede obtener el token de la caché llamando AcquireTokenSilentAsync:

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

donde `userId` es el identificador de objeto de usuario, que se encuentra en la `http://schemas.microsoft.com/identity/claims/objectidentifier` reclamar.

## <a name="using-the-access-token-to-call-the-web-api"></a>Utilizando el token de acceso para llamar a la API de web

Una vez que tenga el token, enviar en el encabezado de autorización de las solicitudes HTTP a la API de web.

```
Authorization: Bearer xxxxxxxxxx
```

El siguiente método de extensión desde la aplicación de encuestas establece el encabezado de autorización de una solicitud HTTP, mediante la clase **HttpClient** .

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

> [AZURE.NOTE] Vea [HttpClientExtensions.cs].

## <a name="authenticating-in-the-web-api"></a>Autenticación de la API de web

La API de web tiene que autenticar al token portador. En ASP.NET Core 1.0, puede usar el [Microsoft.AspNet.Authentication.JwtBearer] [ JwtBearer] paquete. Este paquete proporciona software intermedio que permite a recibir tokens de portador OpenID conectar la aplicación.

Registrar el software intermedio en la web API `Startup` clase.

```csharp
app.UseJwtBearerAuthentication(options =>
{
    options.Audience = "[app ID URI]";
    options.Authority = "https://login.microsoftonline.com/common/";
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //Instead of validating against a fixed set of known issuers, we perform custom multi-tenant validation logic
        ValidateIssuer = false,
    };
    options.Events = new SurveysJwtBearerEvents();
});
```

> [AZURE.NOTE] Vea [Startup.cs].

- **Audiencia**. Establecer esta opción para la dirección URL de ID de aplicación para la web API, que crean cuando registra la web API con Azure AD.
- **Entidad**. Para una aplicación multiempresa, establezca esta propiedad en `https://login.microsoftonline.com/common/`.
- **TokenValidationParameters**. Para una aplicación multiempresa, establezca **ValidateIssuer** en falso. Esto significa que la aplicación valida al emisor.
- **Eventos** es una clase que se deriva de **JwtBearerEvents**.

### <a name="issuer-validation"></a>Validación del emisor

Validar al emisor de tokens en el evento **JwtBearerEvents.ValidatedToken** . Se envía el emisor de la notificación de "iss".

En la aplicación de encuestas, la API de web no controla [inquilino suscripción]. Por tanto, simplemente comprueba si el emisor está ya en la base de datos de aplicación. Si no es así, se produce una excepción, lo que hace que la autenticación no se realice correctamente.

```csharp
public override async Task ValidatedToken(ValidatedTokenContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // the caller was not from a trusted issuer - throw to block the authentication flow
        throw new SecurityTokenValidationException();
    }
}
```

> [AZURE.NOTE] Vea [SurveysJwtBearerEvents.cs].

También puede usar el evento **ValidatedToken** para realizar [reclamaciones transformación]. Recuerde que las notificaciones provienen directamente de Azure AD, por lo que si la aplicación web rechazó las transformaciones de reclamaciones, los que no se reflejan en el token portador que recibe la API de web.

## <a name="authorization"></a>Autorización

Para obtener una descripción general de autorización, consulte [autorización basado en roles y recursos][Authorization]. 

El software de JwtBearer intermedio controla las respuestas de autorización. Por ejemplo, para restringir una acción de controlador a los usuarios autenticados, use la atrribute **[autorizar]** y especifique **JwtBearerDefaults.AuthenticationScheme** como el esquema de autenticación:

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```

Devuelve un código de 401 estado si el usuario no está autenticado.

Para restringir una acción de controlador authorizaton directiva, especifique el nombre de la directiva en el atributo **[autorizar]** :

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

Devuelve un código de 401 estado si el usuario no está autenticado y 403 si el usuario se autentica pero no autorizado. Registrar la directiva de inicio:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });
}
```

## <a name="next-steps"></a>Pasos siguientes

- Lea el siguiente artículo de esta serie: [tokens de acceso de almacenamiento en caché en una aplicación multiempresa][token cache]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer
[parte de una serie]: guidance-multitenant-identity.md
[Encuestas de Tailspin]: guidance-multitenant-identity-tailspin.md
[IdentityServer3]: https://github.com/IdentityServer/IdentityServer3
[Registrar la web API en Azure AD]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#register-the-surveys-web-api
[Actualizar los manifiestos de aplicación]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#update-the-application-manifests
[Conceder permiso de la aplicación web para llamar a la API de web]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#give-the-web-app-permissions-to-call-the-web-api
[Token de almacenamiento en caché]: guidance-multitenant-identity-token-cache.md
[HttpClientExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Common/HttpClientExtensions.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[suscripción a un inquilino]: guidance-multitenant-identity-signup.md
[SurveysJwtBearerEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/SurveyJwtBearerEvents.cs
[transformación de notificaciones]: guidance-multitenant-identity-claims.md#claims-transformations
[Authorization]: guidance-multitenant-identity-authorize.md
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[token cache]: guidance-multitenant-identity-token-cache.md
