<properties
    pageTitle="Introducción a la autenticación para las aplicaciones móviles en Xamarin Android"
    description="Aprenda a usar aplicaciones móviles para autenticar a los usuarios de la aplicación Xamarin Android a través de una gran variedad de proveedores de identidades, incluidos AAD, Google, Facebook, Twitter y Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinandroid-app"></a>Agregar autenticación para su aplicación Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tema muestra cómo se puede autenticar a los usuarios de una aplicación móvil desde la aplicación cliente. En este tutorial, agregue autenticación al proyecto Tutorial rápido con un proveedor de identidades es compatible con aplicaciones de Azure Mobile. Después de que se está correctamente autenticado y autorizado en la aplicación móvil, se muestra el valor del identificador de usuario.

En este tutorial se basa en el tutorial de la aplicación móvil. Debe finalizar el tutorial [crear una aplicación de Xamarin.Android]también primero. Si no utiliza el proyecto de servidor de inicio rápido descargadas, debe agregar el paquete de extensión de autenticación a su proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register"></a>Registrar la aplicación para la autenticación y configurar servicios de aplicación

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permisos a los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o emulador. Compruebe que se produce una excepción no controlada con un código de estado de 401 (no autorizado) cuando se inicie la aplicación. Esto ocurre porque la aplicación intenta obtener acceso a la aplicación móvil de back-end como un usuario no autenticado. La tabla *TodoItem* ahora requiere autenticación.

A continuación, se actualizará la aplicación cliente a recursos de solicitud de la aplicación móvil de back-end con un usuario autenticado.

##<a name="add-authentication"></a>Agregar autenticación a la aplicación

La aplicación se actualiza para requerir que los usuarios al pulsar el botón de **Inicio de sesión** y autenticar antes de que se muestran los datos.

1. Agregue el código siguiente a la clase **TodoActivity** :

        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");

                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    Esto crea un nuevo método para autenticar a un usuario y un controlador de método para un nuevo botón de **Inicio de sesión** . El usuario en el código de ejemplo anterior se autentica mediante un inicio de sesión de Facebook. Un cuadro de diálogo se usa para mostrar el identificador de usuario una vez autenticado.

    > [AZURE.NOTE] Si está utilizando un proveedor de identidades que no sea de Facebook, cambie el valor pasado a **LoginAsync** encima a uno de los siguientes: _MicrosoftAccount_, _Twitter_, _Google_o _WindowsAzureActiveDirectory_.

3. En el método **OnCreate** , eliminar o comentario la salida de la siguiente línea de código:

        OnRefreshItemsSelected ();

4. En el archivo Activity_To_Do.axml, agregue la siguiente definición de botón de *LoginUser* antes el botón *AddItem* existente:

        <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. Agregar el siguiente elemento en el archivo de recursos de Strings.xml:

        <string name="login_button_text">Sign in</string>

6. En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o emulador e inicie sesión con su proveedor de identidades elegido.

    Cuando haya iniciado sesión correctamente, la aplicación mostrará el identificador de inicio de sesión y la lista de elementos todo y puede realizar actualizaciones a los datos.


<!-- URLs. -->
[Crear una aplicación de Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
