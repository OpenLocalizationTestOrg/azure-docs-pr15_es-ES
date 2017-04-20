<properties
    pageTitle="Introducción a la autenticación para las aplicaciones móviles en aplicación Xamarin.Forms | Microsoft Azure"
    description="Aprenda a usar aplicaciones móviles para autenticar a los usuarios de la aplicación de formularios de Xamarin a través de una gran variedad de proveedores de identidades, incluidos AAD, Google, Facebook, Twitter y Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinforms-app"></a>Agregar autenticación para su aplicación Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##<a name="overview"></a>Información general

Este tema muestra cómo se puede autenticar a los usuarios de una aplicación de móvil de aplicación de servicio desde la aplicación cliente. En este tutorial, agregue autenticación al proyecto Tutorial Xamarin.Forms con un proveedor de identidades es compatible con la aplicación de servicio. Después de que se está correctamente autenticado y autorizado por la aplicación móvil, se muestra el valor del identificador de usuario y, a continuación, podrá obtener acceso a datos de tabla restringido.

##<a name="prerequisites"></a>Requisitos previos

Para obtener los mejores resultados con este tutorial, se recomienda que complete primero el tutorial de [crear una aplicación de Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md) . Después de completar este tutorial, tendrá un proyecto de Xamarin.Forms es una aplicación de TodoList varias plataformas.

Si no utiliza el proyecto de servidor de inicio rápido descargadas, debe agregar el paquete de extensión de autenticación a su proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrar la aplicación para la autenticación y configurar servicios de aplicación

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Restringir permisos a los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##<a name="add-authentication-to-the-portable-class-library"></a>Agregar autenticación a la biblioteca de clases portátil

Aplicaciones de Mobile usa el método de extensión [LoginAsync] en la [MobileServiceClient] a un usuario con la autenticación de servicio de aplicaciones de inicio de sesión. En este ejemplo se usa un flujo de autenticación administradas por el servidor que se muestra la interfaz del proveedor de inicio de sesión en la aplicación. Para obtener más información, vea [autenticación administradas por el servidor](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow). Para proporcionar una mejor experiencia de usuario en la aplicación de producción, puede considerar en lugar de utilizar [autenticación administradas por el cliente](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow). 

Para autenticar con un proyecto Xamarin.Forms, defina una interfaz **IAuthenticate** en la biblioteca de clases portátil para la aplicación. Actualizar la interfaz de usuario definida en la biblioteca de clases portátil para agregar un botón de **Inicio de sesión** , que el usuario hace clic para iniciar la autenticación. Después de una autenticación correcta, los datos se cargan desde la aplicación móvil de back-end.

Debe implementar la interfaz **IAuthenticate** para cada plataforma compatible con la aplicación.


1. En Visual Studio o Xamarin Studio, abrir App.cs del proyecto con **portátil** en el nombre, que es el proyecto de biblioteca de clases portátil, a continuación, agregue las siguientes `using` instrucción:

        using System.Threading.Tasks;

2. En App.cs, agregue la siguiente `IAuthenticate` inmediatamente antes de la definición de interfaz la `App` definición de clase.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }

3. Agregue a los siguientes miembros estáticos a la clase de **aplicación** para iniciar la interfaz con una implementación específica de la plataforma.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. Abra TodoList.xaml del proyecto de la biblioteca de clases portátil, agregue el siguiente elemento de **botón** en el elemento de diseño *buttonsPanel* , detrás del botón existente: 

        <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
            Clicked="loginButton_Clicked"/>

    Este botón desencadenadores administrado por servidor autenticación con la aplicación móvil de back-end.

5. Abra TodoList.xaml.cs del proyecto de la biblioteca de clases portátil, a continuación, agregue el siguiente campo a la `TodoList` clase:

        // Track whether the user has authenticated. 
        bool authenticated = false;


6. Reemplace el método **OnAppearing** con el siguiente código:

        protected override async void OnAppearing()
        {
            base.OnAppearing();
    
            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data 
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Esto se asegura de que los datos solo se actualizan desde el servicio después de que el usuario se ha autenticado.

7. Agregue el siguiente controlador para el evento **Clicked** a la clase **TodoList** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. Guardar los cambios y volver a crear el proyecto de biblioteca de clases portátil no comprobar errores.


##<a name="add-authentication-to-the-android-app"></a>Agregar autenticación a la aplicación de Android

Esta sección muestra cómo implementar la interfaz **IAuthenticate** en el proyecto de aplicación Android. Omitir esta sección si no son compatibles con dispositivos Android.

1. En Visual Studio o Xamarin Studio, haga clic en el proyecto **droid** , a continuación, en **establecer como proyecto de inicio**.

2. Presione F5 para iniciar el proyecto en el depurador, a continuación, compruebe que se produce una excepción no controlada con un código de estado de 401 (no autorizado) cuando se inicie la aplicación. Esto ocurre porque access en el servidor está restringido a solo los usuarios autorizados.

3. Abra MainActivity.cs en el proyecto Android y agregue las siguientes `using` instrucciones:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Actualizar la clase de **actividad principal** para implementar la interfaz **IAuthenticate** , como sigue:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Actualizar la clase de **actividad principal** mediante la adición de un campo **MobileServiceUser** y un método de **autenticación** , que es necesario por la interfaz **IAuthenticate** , como sigue:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


    Si está utilizando un proveedor de identidades que no sea de Facebook, elija un valor diferente para [MobileServiceAuthenticationProvider].

6. Agregue el código siguiente al método de la clase de **actividad principal** antes de la llamada a **OnCreate** `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Esto se asegura de que se ha inicializado el autenticador antes de la carga de la aplicación.

7. Reconstruir la aplicación, ejecutar y luego iniciar sesión con el proveedor de autenticación que eligió y compruebe que está teniendo acceso a datos como un usuario autenticado.

##<a name="add-authentication-to-the-ios-app"></a>Agregar autenticación a la aplicación

Esta sección muestra cómo implementar la interfaz **IAuthenticate** en el proyecto de aplicación de iOS. Omitir esta sección si no son compatibles con dispositivos iOS.

1. En Visual Studio o Xamarin Studio, haga clic en el proyecto de **iOS** , a continuación, en **establecer como proyecto de inicio**.

2. Presione F5 para iniciar el proyecto en el depurador, a continuación, compruebe que se produce una excepción no controlada con un código de estado de 401 (no autorizado) cuando se inicie la aplicación. Esto ocurre porque access en el servidor está restringido a solo los usuarios autorizados.

4. Abra AppDelegate.cs en el proyecto de iOS y agregue las siguientes `using` instrucciones:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Actualizar la clase **AppDelegate** para implementar la interfaz **IAuthenticate** , como sigue:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. Actualizar la clase **AppDelegate** mediante la adición de un campo **MobileServiceUser** y un método de **autenticación** , que es necesario por la interfaz **IAuthenticate** , como sigue:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

    Si está utilizando un proveedor de identidades que no sea de Facebook, elija un valor diferente para [MobileServiceAuthenticationProvider].

6. Agregue la siguiente línea de código para el método **FinishedLaunching** antes de llamar a `LoadApplication()`: 

        App.Init(this);

    Esto se asegura de que el autenticador está inicializado antes de carga la aplicación.

7. Reconstruir la aplicación, ejecutar y luego iniciar sesión con el proveedor de autenticación que eligió y compruebe que está teniendo acceso a datos como un usuario autenticado.


##<a name="add-authentication-to-windows-app-projects"></a>Agregar autenticación a proyectos de aplicación de Windows

Esta sección muestra cómo implementar la interfaz **IAuthenticate** en los proyectos de aplicación de Windows Phone 8.1 y Windows 8.1. Aplican los mismos pasos para proyectos de plataforma de Windows Universal (UWP). Omitir esta sección si no son compatibles con los dispositivos de Windows.

1. En Visual Studio, haga clic en la **WinApp** o el proyecto **WinPhone81** , a continuación, en **establecer como proyecto de inicio**.

2. Presione F5 para iniciar el proyecto en el depurador, a continuación, compruebe que se produce una excepción no controlada con un código de estado de 401 (no autorizado) cuando se inicie la aplicación. Esto ocurre porque access en el servidor está restringido a solo los usuarios autorizados.

3. Abra MainPage.xaml.cs para el proyecto de aplicación de Windows y agregue las siguientes `using` instrucciones:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Reemplazar `<your_Portable_Class_Library_namespace>` con el espacio de nombres de la biblioteca de clases portátil.

4. Actualizar la clase **MainPage** para implementar la interfaz **IAuthenticate** , como sigue:

        public sealed partial class MainPage : IAuthenticate


5. Actualizar la clase **MainPage** agregando un campo **MobileServiceUser** y un método de **autenticación** , que es necesario por la interfaz **IAuthenticate** , como sigue:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


    Si está utilizando un proveedor de identidades que no sea de Facebook, elija un valor diferente para [MobileServiceAuthenticationProvider].

6. Agregue la siguiente línea de código en el constructor para la clase **MainPage** antes de la llamada a `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    Reemplazar `<your_Portable_Class_Library_namespace>` con el espacio de nombres de la biblioteca de clases portátil.  
    Si es el proyecto WinApp, vaya hasta el paso 8. El siguiente paso solo se aplica a del proyecto WinPhone81, donde se necesita para completar la devolución de llamada de inicio de sesión.

7. (Opcional) En el proyecto de aplicación **WinPhone81** , abra App.xaml.cs y agregue las siguientes `using` instrucciones:

        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;

    Reemplazar `<your_Portable_Class_Library_namespace>` con el espacio de nombres de la biblioteca de clases portátil.

8.  Agregue la invalidación del método **OnActivated** siguiente a la clase de **aplicación** :

        protected override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            // We just need to handle activation that occurs after web authentication. 
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Get the client and call the LoginComplete method to complete authentication.
                var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
                client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
        }

    Si ya existe el método de reemplazo, basta con agregar el código condicional desde el fragmento de código anterior.

7. Reconstruir la aplicación, ejecutar y luego iniciar sesión con el proveedor de autenticación que eligió y compruebe que está teniendo acceso a datos como un usuario autenticado.

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha realizado este tutorial autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

+ [Agregar las notificaciones de inserción a la aplicación](app-service-mobile-xamarin-forms-get-started-push.md)  
  Obtenga información sobre cómo agregar inserción notificaciones de soporte técnico a su aplicación y configurar la aplicación móvil de back-end para utilizar Hubs de notificación de Azure para enviar notificaciones de inserción.

+ [Habilitar la sincronización sin conexión para la aplicación](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Obtenga información sobre cómo agregar la aplicación con una aplicación móvil de back-end de soporte técnico sin conexión. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no hay ninguna conexión de red.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

