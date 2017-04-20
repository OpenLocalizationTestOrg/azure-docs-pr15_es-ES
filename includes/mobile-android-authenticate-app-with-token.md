
El ejemplo anterior mostraba un estándar de inicio de sesión, que requiere que el cliente ponerse en contacto con el proveedor de identidades y el servicio de Azure del servidor cada vez que se inicia la aplicación. No solo es este método ineficaz, que pueden surgir problemas relacionados con el uso deben muchos clientes intenta iniciar aplicación al mismo tiempo. Un enfoque mejor es el token de autorización devuelto por el servicio de Azure en caché e intenta utilizar esta primera antes de usar una sesión basada en el proveedor. 

>[AZURE.NOTE]Puede almacenar en caché el token emitido por el servicio de Azure independientemente de si está utilizando la autenticación de cliente- o gestionado por servicio de back-end. Este tutorial usa administrados por el servicio de autenticación.


1. Abra el archivo ToDoActivity.java y agregue las siguientes instrucciones de importación:

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Agregar la los miembros siguientes a la `ToDoActivity` clase.

        public static final String SHAREDPREFFILE = "temp"; 
        public static final String USERIDPREF = "uid";  
        public static final String TOKENPREF = "tkn";   


3. En el archivo ToDoActivity.java, agregue la la definición siguiente para el `cacheUserToken` método.
 
        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }   
  
    Este método almacena el identificador de usuario y token en un archivo de preferencia que se marca como privado. Esto debe proteger el acceso a la memoria caché para que otras aplicaciones en el dispositivo no tienen acceso al token porque la preferencia está en espacio aislada para la aplicación. Sin embargo, si alguien tiene acceso al dispositivo, es posible que puede tener acceso a la caché del símbolo por otros medios. 

    >[AZURE.NOTE]Además, puede proteger el token con cifrado si token acceder a los datos se considera confidencial y alguien puede tener acceso al dispositivo. Sin embargo, una solución completamente segura está fuera del ámbito de este tutorial y dependen de sus requisitos de seguridad.


4. En el archivo ToDoActivity.java, agregue la la definición siguiente para el `loadUserTokenCache` método.

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null); 
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null); 
            if (token == null)
                return false;
                
            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);
                
            return true;
        }



5. En el archivo *ToDoActivity.java* , reemplace el `authenticate` método con el siguiente método que usa una caché del símbolo. Cambiar el proveedor de inicio de sesión si desea usar una cuenta distinta de Google.

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
        
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();  
                    }
                });
            }
        }

6. Crear la autenticación de prueba y la aplicación con una cuenta válida. Ejecutar al menos dos veces. Durante la primera ejecución, debe recibir un mensaje para iniciar sesión y crear la caché del símbolo. Después de eso, cada ejecución intentará cargar la caché del símbolo para la autenticación y no se requieren para el inicio de sesión.



