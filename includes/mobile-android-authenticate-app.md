
1. En el **Explorador de proyectos** en Studio Android, abra el archivo ToDoActivity.java y agregue las siguientes instrucciones de importación.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Agregue el método siguiente a la clase **ToDoActivity** : 
    
        private void authenticate() {
            // Login using the Google provider.
            
            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
    
            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();  
                }
            });     
        }


    Esto crea un nuevo método para controlar el proceso de autenticación. El usuario se autentica mediante un inicio de sesión de Google. Se muestra un cuadro de diálogo que muestra el identificador del usuario autenticado. No puede continuar sin una autenticación positiva.

    > [AZURE.NOTE] Si está utilizando un proveedor de identidades que no sea de Google, cambie el valor pasado para el método de **Inicio de sesión** anterior a uno de los siguientes: _MicrosoftAccount_, _Facebook_, _Twitter_o _windowsazureactivedirectory_.

3. En el método **onCreate** , agregue la siguiente línea de código después del código que crea una instancia de la `MobileServiceClient` objeto.

        authenticate();

    Esta llamada inicia el proceso de autenticación.

4. Mover el código restante después de `authenticate();` en el método de **onCreate** a un nuevo método **createTable** , que es similar a esta:

        private void createTable() {
    
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
    
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
    
            // Load the items from Azure.
            refreshItemsFromTable();
        }

9. En el menú **Ejecutar** , haga clic en **Ejecutar aplicación** para iniciar la aplicación e inicie sesión con su proveedor de identidades elegido. 

    Cuando haya iniciado sesión correctamente, debe ejecutar la aplicación sin errores y podrá consultar el servicio de back-end y realizar actualizaciones a los datos.