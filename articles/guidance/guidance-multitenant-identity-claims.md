<properties
   pageTitle="Trabajar con identidades basada en notificaciones en aplicaciones multiempresa | Microsoft Azure"
   description="Cómo utilizar notificaciones para validación del emisor y la autorización"
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

# <a name="working-with-claims-based-identities-in-multitenant-applications"></a>Trabajar con identidades basada en notificaciones en aplicaciones multiempresa

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo forma [parte de una serie]. También es una [aplicación de ejemplo] completa que acompaña a esta serie.

## <a name="claims-in-azure-ad"></a>Notificaciones de Azure AD

Cuando un usuario inicia sesión, Azure AD envía un token de identificador que contiene un conjunto de notificaciones sobre el usuario. Una notificación es simplemente un fragmento de información, expresado como un par de clave y valor. Por ejemplo, `email` = `bob@contoso.com`.  Reclamaciones tienen un emisor &mdash; en este caso, Azure AD &mdash; que es la entidad que el usuario se autentica y crea las notificaciones. Las notificaciones de confianza porque confía en el emisor. (Por el contrario, si no confía en el emisor, no confiar en las notificaciones de!)

En un nivel alto:

1.  El usuario se autentica.
2.  El IDP envía un conjunto de notificaciones.
3.  La aplicación normaliza o aumenta las notificaciones (opcionales).
4.  La aplicación usa las notificaciones para tomar decisiones de autorización.

En conectar OpenID, el conjunto de notificaciones que recibe está controlado por el [parámetro de ámbito] de la solicitud de autenticación. Sin embargo, Azure AD emite un conjunto limitado de reclamaciones mediante OpenID conectar; consulte [Token compatibles y los tipos de notificación]. Si desea obtener más información sobre el usuario, debe usar la API de Azure AD Graph.

Estas son algunas de las notificaciones de AAD que una aplicación podría interesa normalmente:

Tipo de notificación en el símbolo del Id. |    Descripción
-----------------------|--------------
y | ¿Quién emitió el token para. Se trata de identificador de cliente. de la aplicación En general, no es necesario que preocuparse por esta notificación, porque el software intermedio valida automáticamente. Ejemplo:`"91464657-d17a-4327-91f3-2ed99386406f"`
grupos   | Una lista de grupos AAD de que el usuario es miembro. Ejemplo:`["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
ISS  | El [emisor] del token OIDC. Ejemplo:`https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
nombre    | Nombre para mostrar del usuario. Ejemplo:`"Alice A."`
OID | El identificador de objeto para el usuario en AAD. Este valor es el identificador inmutable y no reutilizable del usuario. Usar este valor, el correo electrónico, como un identificador único para los usuarios; pueden cambiar las direcciones de correo electrónico. Si utiliza la API de Azure AD gráfico en la aplicación, el identificador de objeto es ese valor se usa para consultar la información de perfil. Ejemplo:`"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
roles   | Una lista de funciones de aplicación para el usuario. Ejemplo:`["SurveyCreator"]`
TID | Id. de inquilinos Este valor es un identificador único para el inquilino de Azure AD. Ejemplo:`"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique_name | Un nombre para mostrar legible humanos del usuario. Ejemplo:`"alice@contoso.com"`
UPN | Nombre principal de usuario. Ejemplo:`"alice@contoso.com"`

Esta tabla enumeran los tipos de Reclamación tal y como aparecen en el token de ID. En ASP.NET 1.0 del núcleo, el software intermedio OpenID conectar convierte algunos de los tipos de notificación cuando rellena la colección de notificaciones para el usuario principal:

-   OID >`http://schemas.microsoft.com/identity/claims/objectidentifier`
-   TID >`http://schemas.microsoft.com/identity/claims/tenantid`
-   unique_name >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-   UPN >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## <a name="claims-transformations"></a>Transformaciones de reclamaciones

Durante el flujo de autenticación que desea modificar las notificaciones que recibe desde el IDP. En ASP.NET Core 1.0, puede realizar reclamaciones transformación dentro del evento **AuthenticationValidated** desde el software intermedio OpenID conectarse. (Consulte [eventos de autenticación]).

Las reclamaciones que agregue durante **AuthenticationValidated** se almacenan en la cookie de autenticación de la sesión. No desplace volver a Azure AD.

Aquí se muestran algunos ejemplos de transformación de notificaciones:

-   **Normalización de reclamaciones**o realizar reclamaciones coherente en los usuarios. Esto es especialmente importante si va a obtener notificaciones de varios IDPs, que pueden usar diferentes tipos de solicitud para obtener información similar.
Por ejemplo, Azure AD envía una notificación de "upn" que contiene el correo electrónico del usuario. Otros IDPs le envía una notificación de "correo electrónico". El código siguiente convierte la notificación de "upn" en una notificación de "correo electrónico":

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- Agregar **valores de notificaciones predeterminado** para las notificaciones que no están presentes &mdash; por ejemplo, asignar un usuario a un rol de forma predeterminada. En algunos casos esto puede simplificar la lógica de autorización.
- Agregar **tipos de notificaciones personalizadas** con información específica sobre el usuario. Por ejemplo, podría almacenar información sobre el usuario en una base de datos. Puede agregar una notificación personalizada con esta información al vale de autenticación. La notificación se almacena en una cookie, por lo que necesite obtener de la base de datos una vez por sesión. Por otro lado, también desea evitar la creación de cookies demasiado grandes, por lo que debe tener en cuenta el equilibrio entre el tamaño de cookie frente a las búsquedas de base de datos.   

Una vez completado el flujo de autenticación, las notificaciones están disponibles en `HttpContext.User`. En ese momento, se tratará como una colección de sólo lectura &mdash; por ejemplo, usarlos para tomar decisiones de autorización.

## <a name="issuer-validation"></a>Validación del emisor
En conectar OpenID, la notificación de emisor ("iss") identifica el IDP que emitir el token de ID. Parte del flujo de autenticación OIDC es comprobar que la notificación del emisor coincide con el emisor real. El software de OIDC intermedio encarga de ello.

En Azure AD, el valor de emisor es único por cada inquilino AD (`https://sts.windows.net/<tenantID>`). Por lo tanto, una aplicación debería hacer una comprobación adicional para asegurarse de que el emisor representa a un inquilino que está autorizado para iniciar sesión la aplicación.

Para una aplicación de un solo inquilino, solo puede comprobar que el emisor es su propio inquilino. De hecho, el software de OIDC intermedio hace automáticamente de manera predeterminada. En una aplicación de múltiples arrendatario, debe permitir varios emisores, correspondiente a los inquilinos diferentes. Esto es un enfoque general para usar:

-   En las opciones de software intermedio OIDC, establezca **ValidateIssuer** en falso. Esta opción desactiva la comprobación automática.
-   Cuando se conecte un inquilino, almacenar el inquilino y el emisor de la base de datos del usuario.
-   Siempre que un usuario inicia sesión, busque el emisor de la base de datos. Si no encuentra el emisor, significa que no se ha suscrito dicho inquilino. Puede redirigirlos a una página de inicio de sesión.
-  También puede negras a determinados inquilinos; Por ejemplo, para los clientes que no pagar su suscripción.

Para obtener información más detallada, consulte [suscripción e inquilinos integrado en una aplicación multiempresa][signup].

## <a name="using-claims-for-authorization"></a>Uso de notificaciones de autorización

Con notificaciones, identidad de un usuario ya no es una entidad monolítica. Por ejemplo, un usuario puede tener una dirección de correo electrónico, número de teléfono, cumpleaños, género, etcetera. ¿IDP del usuario almacena toda esta información. Pero, al autenticar al usuario, normalmente recibirá un subconjunto de estos como notificaciones. En este modelo, la identidad del usuario es simplemente un paquete de reclamaciones. Hora de tomar decisiones de autorización de un usuario, se buscará determinados conjuntos de notificaciones. En otras palabras, la pregunta "Puede usuario X realizar acciones Y" finalmente se convierte en "tiene X de notificación de usuario Z".

Estos son algunos patrones básicos de comprobación de reclamaciones.

-  Para comprobar que el usuario tiene una reclamación determinado con un valor determinado:

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    Este código comprueba si el usuario tiene una solicitud de función con el valor "Administrador". Correctamente controla el caso donde el usuario tiene varias notificaciones de función o sin notificación de rol.

    La clase de **sus ClaimTypes** define constantes para los tipos de notificación que se usan normalmente. Sin embargo, puede usar cualquier valor de cadena para el tipo de notificación.

-   Para obtener un único valor para un tipo de notificación cuando se esperaba allí para tener más de un valor:
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-   Para obtener todos los valores de un tipo de notificación:

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

Para obtener más información, consulte [autorización basado en roles y recursos en aplicaciones multiempresa][authorization].

## <a name="next-steps"></a>Pasos siguientes

- Lea el siguiente artículo de esta serie: [suscripción e inquilinos integrado en una aplicación multiempresa][signup]
- Más información acerca de la [autorización basada en notificaciones] en la documentación de ASP.NET Core 1.0

<!-- Links -->
[parte de una serie]: guidance-multitenant-identity.md
[parámetro de ámbito]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[Token compatible y los tipos de notificación]: ../active-directory/active-directory-token-and-claims.md
[emisor]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[Eventos de autenticación]: guidance-multitenant-identity-authenticate.md#authentication-events
[signup]: guidance-multitenant-identity-signup.md
[Autorización basada en notificaciones]: https://docs.asp.net/en/latest/security/authorization/claims.html
[aplicación de ejemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[authorization]: guidance-multitenant-identity-authorize.md
