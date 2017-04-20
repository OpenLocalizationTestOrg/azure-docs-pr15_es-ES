<properties
    pageTitle="Agregar autenticación para su aplicación de la plataforma de Windows Universal (UWP) | Aplicaciones móviles de Azure"
    description="Aprenda a usar aplicaciones de Azure aplicación servicio Mobile autenticar a los usuarios de la aplicación de la plataforma de Windows Universal (UWP) con una amplia variedad de proveedores de identidades, incluidos: AAD, Google, Facebook, Twitter y Microsoft."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-windows-app"></a>Agregar autenticación para su aplicación de Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tema muestra cómo agregar autenticación basada en nube a la aplicación móvil. En este tutorial, se agrega la autenticación del proyecto de tutorial rápido de la plataforma de Windows Universal (UWP) para las aplicaciones móviles con un proveedor de identidades es compatible con el servicio de aplicación de Azure. Después de que se está correctamente autenticado y autorizado por la aplicación móvil de back-end, se muestra el valor del identificador de usuario.

En este tutorial se basa en el tutorial de aplicaciones móviles. En primer lugar, debe completar el tutorial de [Introducción a las aplicaciones móviles](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Registrar la aplicación para la autenticación y configurar el servicio de aplicación

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permisos a los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Ahora, puede comprobar que se ha deshabilitado el acceso anónimo a su back-end. Con el proyecto de aplicación UWP establece como el proyecto de inicio, implementa y ejecuta la aplicación; Compruebe que se produce una excepción no controlada con un código de estado de 401 (no autorizado) cuando se inicie la aplicación. Esto ocurre porque la aplicación intenta obtener acceso a su código de la aplicación móvil como un usuario no autenticado, pero la tabla *TodoItem* ahora requiere autenticación.

A continuación, se actualizará la aplicación para autenticar usuarios antes de solicitar recursos de la aplicación de servicio.

##<a name="add-authentication"></a>Agregar autenticación a la aplicación

1. En el UWP aplicación MainPage.cs del archivo de proyecto y agregar fragmento de código siguiente a la clase MainPage:
    
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Este código autentica al usuario con un inicio de sesión de Facebook. Si está utilizando un proveedor de identidades que no sea de Facebook, cambie el valor de **MobileServiceAuthenticationProvider** anteriormente al valor para el proveedor.

3. Salida de comentario o eliminar la llamada al método **ButtonRefresh_Click** (o el método **InitLocalStoreAsync** ) en **el existente OnNavigatedTo método** . Impide que los datos que se está cargando antes de que el usuario se autentica. A continuación, agregará un botón de **Inicio de sesión** a la aplicación que activa la autenticación.

4. Agregar fragmento de código siguiente a la clase MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Abra el archivo de proyecto MainPage.xaml, busque el elemento que define el botón **Guardar** y reemplazar con el siguiente código:

        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>

9. Presione la tecla F5 para ejecutar la aplicación, haga clic en el botón **Inicio de sesión** e iniciar sesión en la aplicación con su proveedor de identidades elegido. Después de que el inicio de sesión es correcta, la aplicación se ejecuta sin errores y puede consultar el back-end y realizar actualizaciones a los datos.


##<a name="tokens"></a>Almacenar el token de autenticación en el cliente

El ejemplo anterior mostraba un estándar de inicio de sesión, que requiere que el cliente ponerse en contacto con el proveedor de identidades y el servicio de aplicación cada vez que se inicia la aplicación. No sólo es ineficaz, puede ejecutar este método en uso-relaciona problemas deben muchos clientes intenta iniciar aplicación al mismo tiempo. Un enfoque mejor es el token de autorización devuelto por la aplicación de servicio de caché e intenta utilizar esta primera antes de usar una sesión basada en el proveedor.

>[AZURE.NOTE]Puede almacenar en caché el token emitido por los servicios de aplicación, independientemente de si está utilizando la autenticación de cliente- o gestionado por servicio. Este tutorial usa administrados por el servicio de autenticación.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha realizado este tutorial autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

+ [Agregar las notificaciones de inserción a la aplicación](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Obtenga información sobre cómo agregar inserción notificaciones de soporte técnico a su aplicación y configurar la aplicación móvil de back-end para utilizar Hubs de notificación de Azure para enviar notificaciones de inserción.

+ [Habilitar la sincronización sin conexión para la aplicación](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Obtenga información sobre cómo agregar la aplicación con una aplicación móvil de back-end de soporte técnico sin conexión. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no hay ninguna conexión de red.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

