<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Cómo crear una aplicación de escritorio de Windows que incluye inicio de sesión, suscripción, y administración de perfiles mediante el uso de Azure Active Directory B2C."
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
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>B2C de Azure AD: Crear una aplicación de escritorio de Windows

Usando B2C de Azure Active Directory (AD Azure), puede agregar características de administración de identidad de autoservicio eficaces a su aplicación de escritorio en pocos pasos. En este artículo le mostrará cómo crear una aplicación de "lista de tareas pendientes" de Windows Presentation Foundation (WPF) de .NET que incluye la suscripción, inicio de sesión de usuario y administración de perfiles. La aplicación incluirá compatibilidad con suscripción e inicio de sesión con un nombre de usuario o el correo electrónico. También incluye compatibilidad con suscripción e inicio de sesión utilizando cuentas sociales como Facebook y Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C

Antes de poder usar Azure AD B2C, debe crear un directorio o de inquilinos.  Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos y más. Si no tiene uno ya, [Cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar en esta guía.

## <a name="create-an-application"></a>Crear una aplicación

A continuación, deberá crear una aplicación en el directorio B2C. Esto le da información de Azure AD que necesita comunicarse de forma segura con la aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md).  Asegúrese de:

- Incluir un **cliente nativo** en la aplicación.
- Copiar la **redirección URI** `urn:ietf:wg:oauth:2.0:oob`. Es la dirección URL predeterminada para este código de ejemplo.
- Copie el **Identificador de la aplicación** que se asigna a la aplicación. Lo necesitará más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define por una [Directiva](active-directory-b2c-reference-policies.md). Este ejemplo de código contiene tres experiencias de identidad: inicio de sesión, inicio de sesión y Editar perfil. Debe crear una directiva para cada tipo, como se describe en el [artículo de referencia de la directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Elija la **Suscripción a un identificador de usuario** o de **suscripción de correo electrónico** en el módulo de proveedores de identidades.
- En la directiva de suscripción, elija **nombre para mostrar** y otros atributos de suscripción.
- Elija las notificaciones de **nombre para mostrar** y el **Identificador de objeto** como notificaciones de la aplicación para cada directiva. Puede elegir otras reclamaciones también.
- Copiar el **nombre** de cada directiva después de crearlo. Debe tener el prefijo `b2c_1_`.  Necesitará estos nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de haber creado correctamente las tres directivas, está listo para crear la aplicación.

## <a name="download-the-code"></a>Descargar el código

El código de este tutorial [se mantiene según GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Para generar el ejemplo, mientras avanza, puede [descargar un proyecto de esquema como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). También puede clonar la estructura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

La aplicación completada también está [disponible como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) o en la `complete` rama del mismo repositorio.

Después de descargar el código de ejemplo, abra el archivo de sln de Visual Studio para empezar. La `TaskClient` proyecto es la aplicación de escritorio de WPF que el usuario interactúa con. Para el propósito de este tutorial, llama a un sitio web de tareas de back-end API, alojado en Azure, que almacena la lista de tareas pendientes de cada usuario.  No es necesario generar la API de web, tenemos ya en ejecución para usted.

Para obtener información sobre cómo web API autentica de forma segura solicitudes mediante Azure AD B2C, consulte la [Introducción de web API a artículo](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Ejecutar políticas
La aplicación se comunica con Azure AD B2C enviando mensajes de autenticación que especifican la directiva que desean ejecutar como parte de la solicitud HTTP. .NET para aplicaciones de escritorio, puede usar la vista previa de biblioteca de autenticación de Microsoft (MSAL) para enviar mensajes de autenticación de OAuth 2.0, ejecutar políticas y obtener tokens que llamen a las API de web.

### <a name="install-msal"></a>Instalar MSAL
Agregar MSAL a la `TaskClient` proyecto mediante la consola del Administrador de paquetes de Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Escriba los detalles de B2C
Abra el archivo `Globals.cs` y reemplazar cada uno de los valores de propiedad con su propio. Esta clase se utiliza en `TaskClient` valores de referencia que se usan normalmente.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### <a name="create-the-publicclientapplication"></a>Crear la PublicClientApplication
La clase principal de MSAL es `PublicClientApplication`. Esta clase representa la aplicación en el sistema de Azure AD B2C. Cuando la aplicación inicializa, crea una instancia de `PublicClientApplication` en `MainWindow.xaml.cs`. Esto puede usarse en toda la ventana.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };
    
    ...
```

### <a name="initiate-a-sign-up-flow"></a>Iniciar un flujo de suscripción
Cuando un usuario decida signos hacia arriba, en el que desea iniciar un flujo de inicio de sesión que utiliza la suscripción que ha creado. Usando MSAL, simplemente llame a `pca.AcquireTokenAsync(...)`. Los parámetros que se pasa a `AcquireTokenAsync(...)` determinar qué token recibe, la directiva que se usa en la solicitud de autenticación y mucho más.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user 
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>Iniciar un flujo de inicio de sesión
Puede iniciar un flujo de inicio de sesión de la misma manera que iniciar un flujo de suscripción. Cuando un usuario inicia sesión, realice la misma llamada a MSAL, esta vez utilizando la directiva de inicio de sesión:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Iniciar un flujo de Editar perfil
De nuevo, puede ejecutar una directiva de modificar el perfil de la misma manera:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

En todos estos casos, MSAL devuelve un token en `AuthenticationResult` o produce una excepción. Cada vez que se obtiene un token de MSAL, puede usar el `AuthenticationResult.User` objeto para actualizar los datos de usuario en la aplicación, como la interfaz de usuario. ADAL también almacena el token para su uso en otros elementos de la aplicación.


### <a name="check-for-tokens-on-app-start"></a>Busque tokens de inicio de la aplicación
También puede usar MSAL para realizar un seguimiento de estado de inicio de sesión del usuario.  En esta aplicación, se desea que el usuario permanezca sesión incluso después de que la aplicación cierra y vuelva a abrirlo.  Vuelva dentro de la `OnInitialized` reemplazar, use del MSAL `AcquireTokenSilent` método para comprobar tokens de caché:

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>Llamar a la API de tarea
Ahora han utilizado MSAL ejecutar directivas y obtener tokens.  Cuando desee usar uno estos tokens para llamar a la API de tarea, puede usar nuevamente de MSAL `AcquireTokenSilent` método para comprobar tokens de caché:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

Cuando la llamada a `AcquireTokenSilentAsync(...)` se realiza correctamente y se encuentra un token en la memoria caché, puede agregar el símbolo de la `Authorization` encabezado de la solicitud HTTP. La API de web tarea usará este encabezado para autenticar la solicitud para leer la lista de tareas pendientes del usuario:

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Cerrar la sesión del usuario
Por último, puede usar MSAL para finalizar una sesión de usuario con la aplicación cuando el usuario selecciona **Cerrar sesión**.  Al utilizar MSAL, para ello, desactive todos los símbolos de la caché de token:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Ejecute la aplicación de ejemplo

Por último, crear y ejecutar el ejemplo.  Suscríbase a la aplicación usando un nombre de usuario o la dirección de correo electrónico. Cerrar la sesión y vuelva a iniciarla como el mismo usuario. Editar perfil de usuario. Cerrar sesión y registrarse utilizando un usuario diferente.

## <a name="add-social-idps"></a>Agregar IDPs sociales

Actualmente, la aplicación admite solo inicio de sesión de usuario e inicio de sesión que utilizan **cuentas locales**. Se trata de cuentas almacenadas en el directorio B2C que usan un nombre de usuario y contraseña. Mediante el uso de Azure AD B2C, puede agregar compatibilidad para otros proveedores de identidades (IDPs) sin cambiar el código.

Para agregar IDPs sociales a su aplicación, comience siguiendo las instrucciones detalladas en estos artículos. Para cada IDP desea admitir, necesita registrar una aplicación en ese sistema y obtener un identificador de cliente.

- [Configurar Facebook como un IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar Google como un IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar Amazon como un IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar LinkedIn como un IDP](active-directory-b2c-setup-li-app.md)

Después de agregar los proveedores de identidades al directorio B2C, necesitará editar cada una de las tres directivas para incluir la nuevas IDPs, como se describe en el [artículo de referencia de la directiva](active-directory-b2c-reference-policies.md). Después de guardar las directivas, vuelva a ejecutar la aplicación. Debe ver el nuevos IDPs agregados como inicio de sesión y las opciones de suscripción en cada una de su identidad experiencias.

Puede experimentar con las directivas y observar los efectos de la aplicación de ejemplo. Agregar o quitar IDPs, manipular notificaciones de la aplicación o cambiar los atributos de suscripción. Experimente hasta que pueda ver cómo directivas, las solicitudes de autenticación y MSAL unir.

Para referencia, en el ejemplo completo [se proporciona como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). También puede clonar desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
