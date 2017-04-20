<properties
pageTitle="Aplicación nativa de Azure Active Directory versión 2.0 .NET | Microsoft Azure"
description="Cómo crear una aplicación nativa de .NET que inicia los usuarios con ambos Account personal de Microsoft y cuentas de trabajo o escuela."
services="active-directory"
documentationCenter=""
authors="dstrockis"
manager="mbaldwin"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="dotnet"
ms.topic="article"
ms.date="07/30/2016"
ms.author="dastrock; vittorib"/>

# <a name="add-sign-in-to-a-windows-desktop-app"></a>Agregar inicio de sesión a una aplicación de escritorio de Windows

Con el extremo de la versión 2.0, puede agregar rápidamente cuentas de trabajo o escuela y autenticación a sus aplicaciones de escritorio con soporte para ambas cuentas personales de Microsoft.  También permite la aplicación de forma segura comunicarse con un sitio web de back-end api, así como [Microsoft Graph](https://graph.microsoft.io) y algunas de las [API de unificada de Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE] No todas las características y escenarios de Azure Active Directory (AD) son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

Para las [aplicaciones nativas de .NET que se ejecutan en un dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD ofrece la biblioteca de autenticación de identidad de Microsoft o MSAL.  Propósitos de MSAL en la vida es más fácil para su aplicación obtener tokens para llamar a los servicios web.  Para mostrar solo lo fácil que es, a continuación, crearemos una aplicación de la lista de tareas pendientes de WPF .NET:

- Inicie la sesión de usuario y obtiene acceso a tokens mediante el [Protocolo de autenticación de OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
- Llamadas de forma segura un servicio web de lista de tareas pendientes, que también está protegido por OAuth 2.0 de back-end.
- Cierra una sesión del usuario.

## <a name="download-sample-code"></a>Descargar el código de ejemplo

El código de este tutorial se mantiene [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Para seguir a lo largo, puede [descargar el esquema de la aplicación como un .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) o clonar la estructura:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

La aplicación completada se proporciona al final de este tutorial también.

## <a name="register-an-app"></a>Registrar una aplicación
Crear una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o siga estos [pasos detallados](active-directory-v2-app-registration.md).  Asegúrese de que:

- Copia hacia abajo el **Identificador de la aplicación** asignado a la aplicación, tendrá pronto.
- Agregue la plataforma **Mobile** para su aplicación.

## <a name="install--configure-msal"></a>Instalar y configurar MSAL
Ahora que ya tiene una aplicación registrada con Microsoft, puede instalar MSAL y escriba el código de identidad.  Para MSAL puedan comunicarse el extremo de la versión 2.0, debe proporcionar con información sobre el registro de aplicación.

-   Empiece agregando MSAL al proyecto TodoListClient mediante la consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

-   En el proyecto TodoListClient, abra `app.config`.  Reemplace los valores de los elementos de la `<appSettings>` sección para reflejar los valores que introduce en el portal de registro de la aplicación.  El código hará referencia a estos valores siempre que use MSAL.
    -   La `ida:ClientId` es el **Identificador de la aplicación** de la aplicación que ha copiado desde el portal.

- En el proyecto de servicio TodoList, abra `web.config` en la raíz del proyecto.  
    - Reemplazar el `ida:Audience` valor con el mismo **Identificador de la aplicación** desde el portal.

## <a name="use-msal-to-get-tokens"></a>Utilice MSAL para obtener tokens
El principio básico detrás MSAL es que siempre que su aplicación necesita un token de acceso, simplemente llame a `app.AcquireToken(...)`, y MSAL hace el resto.  

-   En la `TodoListClient` proyecto, abrir `MainWindow.xaml.cs` y busque el `OnInitialized(...)` método.  El primer paso es iniciar la aplicación `PublicClientApplication` -clase principal del MSAL que representa las aplicaciones nativas.  Esto es donde se pasan MSAL las coordenadas que necesita para comunicarse con Azure AD y saber cómo almacenar en caché tokens.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

- Cuando se inicia la aplicación, va a comprobar y vea si el usuario ya ha iniciado sesión en la aplicación.  Sin embargo, no queremos invocar una interfaz de usuario de inicio de sesión todavía: haremos que el usuario haga clic en "Iniciar sesión" para hacerlo.  En la `OnInitialized(...)` método:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.
    
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

- Si el usuario no ha iniciado sesión y hagan clic en el botón "Iniciar sesión", queremos invocar una interfaz de usuario de inicio de sesión y que el usuario escriba sus credenciales.  Implementar el controlador del botón Inicio de sesión:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

- Si el usuario correctamente signos de MSAL recibirá y un token de caché para usted y puede continuar para llamar a la `GetTodoList()` método con confianza.  Todo lo que está a la izquierda para obtener las tareas de un usuario es implementar la `GetTodoList()` método.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Ejecutar

¡Felicidades! Ahora tiene una aplicación WPF .NET de trabajo que tiene la capacidad de autenticar a los usuarios y llamar de forma segura API Web mediante OAuth 2.0.  Ejecutar ambos proyectos e inicie sesión con una cuenta personal de Microsoft o una cuenta profesional o educativa.  Agregar tareas a la lista de tareas pendientes de ese usuario.  Cerrar la sesión y vuelva a iniciarla como otro usuario para ver su lista de tareas pendientes.  La aplicación se cierra y vuelve a ejecutarla.  Observe cómo la sesión del usuario permanece intacta, que es porque la aplicación almacena tokens en un archivo local.

MSAL facilita incorporar características de identidad comunes a la aplicación con cuentas personales y de trabajo.  Se encarga de todo el trabajo dirty para usted, gestión de caché, compatibilidad con el protocolo OAuth, presentando el usuario con un inicio de sesión de interfaz de usuario, actualizar tokens caducados y mucho más.  Todo lo que necesita saber es una sola llamada API, `app.AcquireTokenAsync(...)`.

Para referencia, el ejemplo completo (sin los valores de configuración) [se proporciona como un .zip aquí](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)o puede clonar ella desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Pasos siguientes

Ahora puede desplazarse a temas más avanzados.  Desea probar:

- [Proteger la API de Web TodoListService con el extremo de la versión 2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Para obtener recursos adicionales, consulte:  

- [La Guía del programador de versión 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Etiqueta de "msal" StackOverflow >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Obtener actualizaciones de seguridad de nuestros productos

Le recomendamos que obtener notificaciones de cuándo se producen incidencias de seguridad, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscribirse a las alertas de aviso de seguridad.
