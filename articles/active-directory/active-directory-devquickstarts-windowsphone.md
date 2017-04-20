<properties
    pageTitle="Windows Phone de Azure AD Introducción | Microsoft Azure"
    description="Cómo crear una aplicación de Windows Phone que se integra con Azure AD para iniciar sesión y llamadas de Azure AD protegido API con OAuth."
    services="active-directory"
    documentationCenter="windows"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>



# <a name="integrate-azure-ad-with-a-windows-phone-app"></a>Integrar Azure AD con una aplicación de Windows Phone

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si desarrolla una aplicación de Windows Phone 8.1, Azure AD facilita la forma muy sencilla para autenticar los usuarios con sus cuentas de Active Directory.  También permite la aplicación de forma segura consumir cualquier web API protegido por Azure AD, como las API de Office 365 o la API de Azure.

> [AZURE.NOTE] Este ejemplo de código utiliza la versión 2.0 ADAL.  La tecnología más reciente, desea probar en su lugar nuestro [Tutorial Universal de Windows mediante v3.0 ADAL](active-directory-devquickstarts-windowsstore.md).  Si realmente va a crear una aplicación para Windows Phone 8.1, éste es el lugar adecuado.  Versión 2.0 ADAL es aún totalmente compatible y usa la manera recomendada de desarrollo de aplicaciones agianst Windows Phone 8.1 Azure AD.

.NET nativa los clientes que necesitan tener acceso a los recursos protegidos, Azure AD proporciona la Active Directory Authentication Library o ADAL.  Propósitos de ADAL en la vida es más fácil para su aplicación obtener el token de acceso.  Para mostrar solo lo fácil que es, a continuación, crearemos una aplicación de Windows Phone 8.1 "directorio buscador":

-   Obtiene acceso a tokens para llamar a la API de Azure AD gráfico mediante el [Protocolo de autenticación de OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Busca en un directorio para los usuarios con un determinado UPN.
-   Usuarios de signos fuera.

Para generar la aplicación de trabajo completado, tendrá que:

2. Registrar la aplicación con Azure AD.
3. Instalar y configurar ADAL.
5. Use ADAL para obtener tokens de Azure AD.

Para comenzar, [descargar un proyecto de esquema](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) o [descargar el ejemplo completo](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Cada uno de ellos es una solución de Visual Studio de 2013.  También necesitará a un inquilino de Azure AD en el que puede crear usuarios y registrar una aplicación.  Si aún no dispone de un inquilino, [Obtenga información sobre cómo obtener una](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>*1. registrar la aplicación de buscador de directorio*
Para habilitar la aplicación obtener tokens, primero deberá registrar en su inquilino de Azure AD y conceder permiso de acceso a la API de Azure AD gráfico:

-   Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com)
-   En la barra de navegación izquierda, haga clic en **Active Directory**
-   Seleccione a un inquilino en el que se va a registrar la aplicación.
-   Haga clic en la pestaña de **aplicaciones** y haga clic en **Agregar** en la Bandeja de la parte inferior.
-   Siga las indicaciones y cree una nueva **Aplicación nativa de cliente**.
    -   El **nombre** de la aplicación describe la aplicación a los usuarios finales
    -   La **Redirección Uri** es una combinación de combinación y la cadena que usará Azure AD para devolver respuestas tokens.  Escriba un valor de marcador de posición por ahora, por ejemplo, `http://DirectorySearcher`.  Deberá reemplace este valor más adelante.
-   Una vez haya completado el registro, AAD asignar su aplicación un identificador único del cliente.  Necesitará este valor en las siguientes secciones, así que cópielo desde la ficha **Configurar** .
- También en la ficha **Configurar** , busque la sección "Permisos a otras aplicaciones".  Para la aplicación "Azure Active Directory", agregue el permiso de **acceso a su directorio de la organización** en **Permisos de delegado**.  Esto le permitirá la aplicación de la API de gráfico para los usuarios de la consulta.

## <a name="2-install--configure-adal"></a>*2. instalar y configurar ADAL*
Ahora que ya tiene una aplicación en Azure AD, puede instalar ADAL y escriba el código de identidad.  Para ADAL puedan comunicarse con Azure AD, debe proporcionar con información sobre el registro de aplicación.
-   Empiece agregando ADAL al proyecto DirectorySearcher mediante la consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   En el proyecto DirectorySearcher, abra `MainPage.xaml.cs`.  Reemplazar los valores de la `Config Values` región para reflejar los valores que introduce en el Portal de Azure.  El código hará referencia a estos valores siempre que use ADAL.
    -   La `tenant` es el dominio de su inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com
    -   La `clientId` está dentro de la aplicación que ha copiado desde el portal.
-   Debe detectar el uri de devolución de llamada para su aplicación de Windows Phone.  Establecer un punto de interrupción en esta línea en el `MainPage` método:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Ejecute la aplicación y tomarse un copie el valor de `redirectUri` cuando la interrupción.  Debe ser similar

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- En la ficha **Configurar** de la aplicación en el Portal de administración de Azure, reemplace el valor de la **RedirectUri** con este valor.  

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. use ADAL para obtener Tokens de AAD*
El principio básico detrás de ADAL es que siempre que su aplicación necesita un token de acceso, simplemente llama `authContext.AcquireToken(…)`, y ADAL hace el resto.  

-   El primer paso es iniciar la aplicación `AuthenticationContext` -ADAL de clase principal.  Esto es donde se pasa ADAL las coordenadas que necesita para comunicarse con Azure AD y saber cómo almacenar en caché tokens.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- Ahora, busque la `Search(...)` método, que se invoca cuando la cliks de usuario "Búsqueda" del botón de la interfaz de usuario de la aplicación.  Este método hace una solicitud GET a la API de Azure AD Graph para consulta de los usuarios cuyo UPN comienza con el término de búsqueda especificado.  Pero con el fin de la API de gráfico de la consulta, tendrá que incluir una access_token en el `Authorization` encabezado de la solicitud - esto es donde se ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- Si la autenticación interactiva es necesaria, ADAL usará el agente de Windows Phone de autenticación Web (WAB) y [modelo de continuación](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) para mostrar Azure AD iniciar sesión en la página.  Cuando el usuario inicia sesión, la aplicación debe pasar ADAL los resultados de la interacción WAB.  Esto es tan sencillo como implementar la `ContinueWebAuthentication` interfaz:

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- Ahora es el momento de usar la `AuthenticationResult` que ADAL se devuelve a la aplicación.  En la `QueryGraph(...)` devolución de llamada, adjuntar la access_token ha adquirido a la solicitud GET en el encabezado de autorización:

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- También puede usar el `AuthenticationResult` objeto para mostrar información sobre el usuario en la aplicación. En la `QueryGraph(...)` método, utilice el resultado para mostrar el identificador del usuario en la página:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Por último, puede usar ADAL para iniciar sesión del usuario en la aplicación.  Cuando el usuario hace clic en el botón "cerrar sesión", queremos para asegurarse de que la siguiente llamada a `AcquireTokenSilentAsync(...)` se producirá un error.  Con ADAL, esto es tan fácil como borrar la caché de token:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

¡Felicidades! Ahora tiene una aplicación de Windows Phone que tiene la capacidad para autenticar a los usuarios, llamar de forma segura API Web mediante OAuth 2.0 y obtenga información básica sobre el usuario.  Si todavía no lo ha hecho, ahora es el momento para rellenar al inquilino de algunos usuarios.  Ejecute la aplicación DirectorySearcher e inicie sesión con uno de esos usuarios.  Buscar otros usuarios en función de su UPN.  La aplicación se cierra y vuelve a ejecutarla.  Observe cómo la sesión del usuario permanece intacta.  Cerrar la sesión y vuelva a iniciarla como otro usuario.

ADAL facilita la incorporar todas estas características de identidad comunes a la aplicación.  Se encarga de todo el trabajo dirty para usted, gestión de caché, compatibilidad con el protocolo OAuth, presentando el usuario con un inicio de sesión de interfaz de usuario, actualizar tokens caducados y mucho más.  Todo lo que necesita saber es una sola llamada API, `authContext.AcquireToken*(…)`.

Para referencia, se proporciona el ejemplo completo (sin los valores de configuración) [aquí](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Ahora puede mover a escenarios de identidad adicional.  Desea probar:

[Proteger un Web .NET API con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]