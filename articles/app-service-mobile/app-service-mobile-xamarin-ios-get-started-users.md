<properties
    pageTitle="Introducción a la autenticación para las aplicaciones móviles en Xamarin iOS"
    description="Aprenda a usar aplicaciones móviles para autenticar a los usuarios de la aplicación de iOS Xamarin a través de una gran variedad de proveedores de identidades, incluidos AAD, Google, Facebook, Twitter y Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinios-app"></a>Agregar autenticación para su aplicación Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tema muestra cómo se puede autenticar a los usuarios de una aplicación de móvil de aplicación de servicio desde la aplicación cliente. En este tutorial, agregue autenticación al proyecto Tutorial Xamarin.iOS con un proveedor de identidades es compatible con la aplicación de servicio. Después de que se está correctamente autenticado y autorizado por la aplicación móvil, se muestra el valor del identificador de usuario y podrá tener acceso a datos de tabla restringido.

En primer lugar, debe completar el tutorial [crear una aplicación de Xamarin.iOS]. Si no utiliza el proyecto de servidor de inicio rápido descargadas, debe agregar el paquete de extensión de autenticación a su proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrar la aplicación para la autenticación y configurar servicios de aplicación

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Restringir permisos a los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. en Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o emulador. Compruebe que se produce una excepción no controlada con un código de estado de 401 (no autorizado) cuando se inicie la aplicación. El error se registra en la consola del depurador. Por lo tanto en Visual Studio, verá el error en la ventana de resultados.

&nbsp;&nbsp;Este error no autorizado ocurre porque la aplicación intenta obtener acceso a la aplicación móvil de back-end como un usuario no autenticado. La tabla *TodoItem* ahora requiere autenticación.

A continuación, se actualizará la aplicación cliente a recursos de solicitud de la aplicación móvil de back-end con un usuario autenticado.

##<a name="add-authentication-to-the-app"></a>Agregar autenticación a la aplicación

En esta sección, modificará la aplicación para mostrar una pantalla de inicio de sesión antes de mostrar datos. Cuando se inicie la aplicación, no se conectará no a su aplicación de servicio y no se muestren los datos. Después de la primera vez que el usuario realiza el movimiento de la actualización, aparecerá la pantalla de inicio de sesión; Después de iniciar sesión correctamente, se mostrará la lista de elementos todo.

1. En el proyecto de cliente, abra el archivo **QSTodoService.cs** y agregue la siguiente instrucción using y `MobileServiceUser` con descriptor de acceso a la clase QSTodoService:

    ```
        using UIKit;
    ```

        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

2. Agregar nuevo método denominado **autenticar** a **QSTodoService** con la siguiente definición:


        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Si está utilizando un proveedor de identidades que no sea un Facebook, cambie el valor pasado a **LoginAsync** encima a uno de los siguientes: _MicrosoftAccount_, _Twitter_, _Google_o _WindowsAzureActiveDirectory_.

3. Abra **QSTodoListViewController.cs**. Modificar la definición del método de **ViewDidLoad** quitando la llamada a **RefreshAsync()** cerca del final:

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }


4. Modifique el método **RefreshAsync** para autenticar si la propiedad de **usuario** es nula. Agregue el código siguiente en la parte superior de la definición del método:

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5. En Visual Studio o Xamarin Studio conectados a su Host de Xamarin crear en su Mac, ejecute el proyecto de cliente dirige a un dispositivo o emulador. Compruebe que la aplicación no muestra ningún dato.

    Movimiento de la actualización se realiza mediante la extracción de la lista de elementos, lo que hace que aparezca la pantalla de inicio de sesión. Una vez que ha escrito correctamente las credenciales válidas, la aplicación mostrará la lista de elementos todo y puede realizar actualizaciones a los datos.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Crear una aplicación de Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
