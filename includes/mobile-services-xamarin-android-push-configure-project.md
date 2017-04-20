
1. En la vista de solución (o el **Explorador de soluciones** de Visual Studio), haga clic en la carpeta de **componentes** , haga clic en **Obtener más componentes...**, busque el componente de **Cliente de mensajería de nube de Google** y agregarlo al proyecto.

2. Abra el archivo de proyecto ToDoActivity.cs y agregue lo siguiente mediante declaración a la clase:

        using Gcm.Client;

3. En la clase **ToDoActivity** , agregue el código siguiente: 

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

    Esto le permite acceder a la instancia de cliente móvil desde el proceso de servicio del controlador de inserción.

4.  Agregue el código siguiente al método **OnCreate** , una vez creado el **MobileServiceClient** :

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Su **ToDoActivity** ahora está preparado para agregar las notificaciones de inserción.