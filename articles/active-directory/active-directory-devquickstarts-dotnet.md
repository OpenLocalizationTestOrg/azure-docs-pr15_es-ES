<properties
    pageTitle="Introducción de .NET de Azure AD | Microsoft Azure"
    description="Cómo crear una aplicación de escritorio de Windows .NET que se integra con Azure AD para iniciar sesión y llamadas de Azure AD protegido API con OAuth."
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


# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Integrar Azure AD en una aplicación WPF de escritorio de Windows

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si desarrolla una aplicación de escritorio, Azure AD facilita la forma muy sencilla para autenticar los usuarios con sus cuentas de Active Directory.  También permite la aplicación de forma segura consumir cualquier web API protegido por Azure AD, como las API de Office 365 o la API de Azure.

.NET nativa los clientes que necesitan tener acceso a los recursos protegidos, Azure AD proporciona la Active Directory Authentication Library o ADAL.  Propósitos de ADAL en la vida es más fácil para su aplicación obtener el token de acceso.  Para mostrar solo lo fácil que es, a continuación, crearemos una aplicación de la lista de tareas pendientes de WPF .NET:

-   Obtiene acceso a tokens para llamar a la API de Azure AD gráfico mediante el [Protocolo de autenticación de OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Busca en un directorio para los usuarios con un alias determinado.
-   Usuarios de signos fuera.

Para generar la aplicación de trabajo completado, tendrá que:

2. Registrar la aplicación con Azure AD.
3. Instalar y configurar ADAL.
5. Use ADAL para obtener tokens de Azure AD.

Para comenzar, [Descargue la estructura de la aplicación](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) o [descargar el ejemplo completo](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  También necesitará a un inquilino de Azure AD en el que puede crear usuarios y registrar una aplicación.  Si aún no dispone de un inquilino, [Obtenga información sobre cómo obtener una](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. registrar la aplicación DirectorySearcher*
Para habilitar la aplicación obtener tokens, primero deberá registrar en su inquilino de Azure AD y conceder permiso de acceso a la API de Azure AD gráfico:

-   Inicie sesión en el Portal de administración de Azure
-   En la barra de navegación izquierda, haga clic en **Active Directory**
-   Seleccione a un inquilino en el que se va a registrar la aplicación.
-   Haga clic en la pestaña de **aplicaciones** y haga clic en **Agregar** en la Bandeja de la parte inferior.
-   Siga las indicaciones y cree una nueva **Aplicación nativa de cliente**.
    -   El **nombre** de la aplicación describe la aplicación a los usuarios finales
    -   La **Redirección Uri** es una combinación de combinación y la cadena que usará Azure AD para devolver respuestas tokens.  Escriba un valor específico a la aplicación, por ejemplo, `http://DirectorySearcher`.
-   Una vez haya completado el registro, AAD asignar su aplicación un identificador único del cliente.  Necesitará este valor en las siguientes secciones, así que cópielo desde la ficha **Configurar** .
- También en la ficha **Configurar** , busque la sección "Permisos a otras aplicaciones".  Para la aplicación "Azure Active Directory", agregue el permiso de **acceso a su directorio de la organización** en **Permisos de delegado**.  Esto le permitirá la aplicación de la API de gráfico para los usuarios de la consulta.

## <a name="2-install--configure-adal"></a>*2. instalar y configurar ADAL*
Ahora que ya tiene una aplicación en Azure AD, puede instalar ADAL y escriba el código de identidad.  Para ADAL puedan comunicarse con Azure AD, debe proporcionar con información sobre el registro de aplicación.
-   Empiece agregando ADAL al proyecto DirectorySearcher mediante la consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   En el proyecto DirectorySearcher, abra `app.config`.  Reemplace los valores de los elementos de la `<appSettings>` sección para reflejar los valores que introduce en el Portal de Azure.  El código hará referencia a estos valores siempre que use ADAL.
    -   La `ida:Tenant` es el dominio de su inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com
    -   La `ida:ClientId` está dentro de la aplicación que ha copiado desde el portal.
    -   La `ida:RedirectUri` es la redirección de url registrado en el portal.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. use ADAL para obtener Tokens de AAD*
El principio básico detrás de ADAL es que siempre que su aplicación necesita un token de acceso, simplemente llama `authContext.AcquireTokenAsync(...)`, y ADAL hace el resto.  

-   En la `DirectorySearcher` proyecto, abrir `MainWindow.xaml.cs` y busque el `MainWindow()` método.  El primer paso es iniciar la aplicación `AuthenticationContext` -ADAL de clase principal.  Esto es donde se pasa ADAL las coordenadas que necesita para comunicarse con Azure AD y saber cómo almacenar en caché tokens.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

- Ahora, busque la `Search(...)` método, que se invoca cuando la cliks de usuario "Búsqueda" del botón de la interfaz de usuario de la aplicación.  Este método hace una solicitud GET a la API de Azure AD Graph para consulta de los usuarios cuyo UPN comienza con el término de búsqueda especificado.  Pero con el fin de la API de gráfico de la consulta, tendrá que incluir una access_token en el `Authorization` encabezado de la solicitud - esto es donde se ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- Cuando la aplicación solicita un token llamando `AcquireTokenAsync(...)`, ADAL intentará devolver un token sin preguntar al usuario las credenciales.  Si ADAL determina que el usuario debe iniciar sesión obtener un token, mostrar un cuadro de diálogo de inicio de sesión, recopilar las credenciales del usuario y devolver un token tras la autenticación.  Si no puede devolver un token por cualquier motivo ADAL, producirá un `AdalException`.
- Observe que la `AuthenticationResult` objeto contiene un `UserInfo` objeto que puede usarse para recopilar la información que necesite la aplicación.  En el DirectorySearcher `UserInfo` se usa para personalizar la interfaz de usuario de la aplicación con el identificador del usuario.

- Cuando el usuario hace clic en el botón "cerrar sesión", queremos para asegurarse de que la siguiente llamada a `AcquireTokenAsync(...)` pedirá al usuario para iniciar sesión.  Con ADAL, esto es tan fácil como borrar la caché de token:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- Sin embargo, si el usuario no haga clic en el botón "cerrar sesión", que desea mantener la sesión de usuario para la próxima vez que ejecuten DirectorySearcher.  Cuando se inicia la aplicación, puede comprobar la caché del símbolo de ADAL para un símbolo existente y actualice la interfaz de usuario.  En la `CheckForCachedToken()` método, realizar otra llamada a `AcquireTokenAsync(...)`, en esta ocasión pasando el `PromptBehavior.Never` parámetro.  `PromptBehavior.Never`le indicará ADAL que no se le solicitará al usuario para iniciar sesión y ADAL en su lugar debe iniciar una excepción si no puede devolver un token.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

¡Felicidades! Ahora tiene una aplicación de WPF .NET que tiene la capacidad para autenticar a los usuarios, llamar de forma segura API Web mediante OAuth 2.0 y obtenga información básica sobre el usuario.  Si todavía no lo ha hecho, ahora es el momento para rellenar al inquilino de algunos usuarios.  Ejecute la aplicación DirectorySearcher e inicie sesión con uno de esos usuarios.  Buscar otros usuarios en función de su UPN.  La aplicación se cierra y vuelve a ejecutarla.  Observe cómo la sesión del usuario permanece intacta.  Cerrar la sesión y vuelva a iniciarla como otro usuario.

ADAL facilita la incorporar todas estas características de identidad comunes a la aplicación.  Se encarga de todo el trabajo dirty para usted, gestión de caché, compatibilidad con el protocolo OAuth, presentando el usuario con un inicio de sesión de interfaz de usuario, actualizar tokens caducados y mucho más.  Todo lo que necesita saber es una sola llamada API, `authContext.AcquireTokenAsync(...)`.

Para referencia, se proporciona el ejemplo completo (sin los valores de configuración) [aquí](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Ahora puede mover a escenarios adicionales.  Desea probar:

[Proteger un Web .NET API con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
