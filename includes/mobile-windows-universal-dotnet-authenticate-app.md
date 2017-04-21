
1. Abra el archivo de proyecto compartido MainPage.cs y agregar fragmento de código siguiente a la clase MainPage:
    
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

3. Salida de comentario o eliminar la llamada al método **RefreshTodoItems** en **el existente OnNavigatedTo método** .

    Impide que los datos que se está cargando antes de que el usuario se autentica. A continuación, agregará un botón de **Inicio de sesión** a la aplicación que activa la autenticación.

4. Agregar fragmento de código siguiente a la clase MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. En el proyecto de aplicación de la tienda Windows, abra el archivo de proyecto MainPage.xaml y agregue el siguiente elemento de **botón** justo antes del elemento que define el botón **Guardar** :

        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. En el proyecto de aplicación de la tienda de Windows Phone, agregue el siguiente elemento de la **ContentPanel**, **botón** después del elemento de **cuadro de texto** :

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. Abra el archivo de proyecto App.xaml.cs compartido y agregue el código siguiente:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

    Si ya existe el método **OnActivated** , basta con agregar el `#if...#endif` bloque de código.

9. Presione la tecla F5 para ejecutar la aplicación de la tienda de Windows, haga clic en el botón **Inicio de sesión** e iniciar sesión en la aplicación con su proveedor de identidades elegido. 

    Cuando haya iniciado sesión correctamente, debe ejecutar la aplicación sin errores y podrá consultar los back-end y realizar actualizaciones a los datos.

10. Haga clic en el proyecto de aplicación de la tienda de Windows Phone, haga clic en **establecer como proyecto de inicio**y luego repita el paso anterior para comprobar que la tienda de Windows Phone aplicación también se ejecute correctamente.  

 