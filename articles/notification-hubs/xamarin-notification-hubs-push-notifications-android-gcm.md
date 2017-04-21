<properties
    pageTitle="Introducción a Hubs de notificación para aplicaciones Xamarin.Android | Microsoft Azure"
    description="En este tutorial, aprenderá a usar Hubs de notificación de Azure para enviar notificaciones de inserción a una aplicación Xamarin Android."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter="xamarin"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Introducción a la notificación Hubs con Xamarin para Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Información general

En este tutorial se muestra cómo usar Hubs de notificación de Azure para enviar notificaciones de inserción a una aplicación de Xamarin.Android.
Debe crear una aplicación de Xamarin.Android en blanco que recibe las notificaciones de inserción mediante mensajería de nube de Google (GCM). Cuando haya terminado, podrá usar su centro de notificación para difundir las notificaciones de inserción a todos los dispositivos que ejecutan la aplicación. El código finalizado está disponible en la [aplicación de NotificationHubs] [ GitHub] muestra.

Este tutorial muestra el escenario de difusión simple en el uso de Hubs de notificación.


## <a name="before-you-begin"></a>Antes de empezar

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

El código finalizado para este tutorial se pueden encontrar en GitHub [aquí](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid).



##<a name="prerequisites"></a>Requisitos previos

Este tutorial requiere lo siguiente:

+ Visual Studio con Xamarin en Windows o Xamarin Studio en Mac OS X. completa instrucciones de instalación están en el [programa de instalación e instalar para Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ Cuenta de Google activa
+ [Componente de mensajería de Azure]
+ [Componente de cliente de mensajería de nube de Google]

Realizar este tutorial es un requisito previo para todos los otros tutoriales Hubs de notificación para las aplicaciones de Xamarin.Android.

> [AZURE.IMPORTANT] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="enable-google-cloud-messaging"></a>Habilitar mensajería de nube de Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-your-notification-hub"></a>Configurar su centro de notificación

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>Haga clic en la ficha <b>Configurar</b> en la parte superior, escriba el valor de <b>Clave API</b> obtenida en la sección anterior y, a continuación, haga clic en <b>Guardar</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


El concentrador de notificación ahora está configurado para trabajar con GCM y tiene las cadenas de conexión a ambos registrar su aplicación para recibir notificaciones y enviar notificaciones de inserción.

##<a name="connect-your-app-to-the-notification-hub"></a>Conectar la aplicación al concentrador de notificación

###<a name="create-a-new-project"></a>Crear un nuevo proyecto

1. En Xamarin Studio, haga clic en **Solución de nuevo**, haga clic en **Aplicación Android**y haga clic en **siguiente**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. Escriba el **Nombre de la aplicación** y el **identificador**. Haga clic en las **Plataformas de destino** que desea admitir y, a continuación, haga clic en **siguiente** y **crear**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


    Esto crea un nuevo proyecto de Android.

2. Abra las propiedades del proyecto haciendo clic en el nuevo proyecto en la vista de solución y elija **Opciones**. Seleccione el elemento de **Aplicación Android** en la sección **Generar** .

    Asegúrese de que la primera letra del **nombre del paquete** es en minúsculas.

    > [AZURE.IMPORTANT] La primera letra del nombre del paquete debe ser minúscula. En caso contrario, recibirá errores manifiestos de aplicación cuando se registran el **BroadcastReceiver** y **IntentFilter** para las notificaciones de inserción a continuación.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. Opcionalmente, puede establecer la **versión mínima Android** a otro nivel de API.

4. Opcionalmente, establezca la **versión Android de destino** a la otra versión de API que desee dirigirse (debe ser el nivel de API 8 o posterior).

Haga clic en **Aceptar** y cierre el cuadro de diálogo Opciones de proyecto.


###<a name="add-the-required-components-to-your-project"></a>Agregar los componentes necesarios a su proyecto

El Google Cloud cliente de mensajería disponible en el almacén de componentes Xamarin simplifica el proceso de soporte de notificaciones de inserción de Xamarin.Android.

1. Haga clic en la carpeta de componentes de aplicación Xamarin.Android y elija **Obtener más componentes**.

2. Busque el componente de **Mensajería de Azure** y agregarlo al proyecto.

3. Busque el componente de **Cliente de mensajería de nube de Google** y agregarlo al proyecto.


###<a name="set-up-notification-hubs-in-your-project"></a>Configurar hubs de notificación en el proyecto

1. Recopilar la información siguiente para su centro de aplicación y notificación Android:

    - **GoogleProjectNumber**: obtener este valor de número de proyecto de la información general de la aplicación en el Portal de programadores de Google. Cometió una nota de este valor en una versión anterior cuando creó la aplicación en el portal.
    - **Escuchar la cadena de conexión**: en el panel en el [Portal de Azure clásica], haga clic en **las cadenas de conexión de la vista**. Copie la cadena de conexión de *DefaultListenSharedAccessSignature* para este valor.
    - **Nombre del concentrador**: este es el nombre de su centro desde el [Portal de Azure clásico]. Por ejemplo, *mynotificationhub2*.

    Crear una clase **Constants.cs** para el proyecto Xamarin y defina los valores de la constantes siguientes en la clase. Reemplace los marcadores de posición por sus valores.

        public static class Constants
        {
            public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
            public const string ListenConnectionString = "<Listen connection string>";
            public const string NotificationHubName = "<hub name>";
        }

2. Agregue las siguientes instrucciones using al **MainActivity.cs**:

        using Android.Util;
        using Gcm.Client;

3. Agregar una variable de instancia a la `MainActivity` clase que se utilizará para mostrar un cuadro de diálogo Alerta cuando la aplicación se está ejecutando:

        public static MainActivity instance;


3. Cree el método siguiente en la clase de **actividad principal** :

        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. En la `OnCreate` método **MainActivity.cs**, inicializar el `instance` variable y agregue una llamada a `RegisterWithGCM`:

        protected override void OnCreate (Bundle bundle)
        {
            instance = this;

            base.OnCreate (bundle);

            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            RegisterWithGCM();
        }


4. Crear una nueva clase, **MyBroadcastReceiver**.

    > [AZURE.NOTE] Se le guiará a través de la creación de una clase **BroadcastReceiver** desde cero a continuación. Sin embargo, es una alternativa rápida al crear manualmente **MyBroadcastReceiver.cs** hacer referencia al archivo **GcmService.cs** que se encuentra en el proyecto Xamarin.Android de ejemplo incluido con los [ejemplos de NotificationHubs][GitHub]. Duplicar **GcmService.cs** y cambiar los nombres de clase pueden ser un lugar ideal para iniciar también.

5. Agregue las siguientes instrucciones using al **MyBroadcastReceiver.cs** (que hace referencia al componente y ensamblado que agregó anteriormente):

        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;

5. En **MyBroadcastReceiver.cs**, agregue las siguientes solicitudes de permiso entre las instrucciones de **uso** y la declaración de **espacio de nombres** :

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. En **MyBroadcastReceiver.cs**, cambie la clase **MyBroadcastReceiver** para que coincida con la siguiente:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Agregar otra clase en **MyBroadcastReceiver.cs** denominado **PushHandlerService**, que se deriva de **GcmServiceBase**. Asegúrese de aplicar el atributo de **servicio** a la clase:

        [Service] // Must use the service tag
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID)
            {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
            }
        }


8. **GcmServiceBase** implementa métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()**y **OnError()**. Nuestra clase de implementación de **PushHandlerService** debe invalidar estos métodos y, a continuación, estos métodos se activan en respuesta a la interacción con el hub de notificación.


9. Invalidar el método **OnRegistered()** en **PushHandlerService** mediante el siguiente código:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...",
                                "The device has been Registered!");

            Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                        context);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }

            //var tags = new List<string>() { "falcons" }; // create tags if you want
            var tags = new List<string>() {};

            try
            {
                var hubRegistration = Hub.Register(registrationId, tags.ToArray());
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }
        }

    > [AZURE.NOTE] En el código de **OnRegistered()** anterior, debe tener en cuenta la posibilidad de especificar etiquetas para registrar de canales de mensajería específicos.

10. Invalidar el método **OnMessage** en **PushHandlerService** mediante el siguiente código:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }

11. Agregar los siguientes métodos **createNotification** y **dialogNotify** a **PushHandlerService** para notificar a los usuarios cuando se recibe una notificación.

    >[AZURE.NOTE] Diseño de la notificación en Android 5.0 y versiones posteriores representa una desviación significativa del de versiones anteriores. Si esta prueba en Android 5.0 o posterior, la aplicación tendrán que estar ejecutándose para recibir la notificación. Para obtener más información, vea [Notificaciones Android](http://go.microsoft.com/fwlink/?LinkId=615880).

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }

        protected void dialogNotify(String title, String message)
        {

            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }


12. Reemplazar a miembros abstractos **OnUnRegistered()**, **OnRecoverableError()**y **OnError()** para que el código se compila:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }

        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

            return base.OnRecoverableError (context, errorId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }


##<a name="run-your-app-in-the-emulator"></a>Ejecute la aplicación en el emulador

Si ejecutar esta aplicación en el emulador, asegúrese de que usa un Android Virtual dispositivo (AVD) que es compatible con APIs Google.

> [AZURE.IMPORTANT] Para recibir notificaciones de inserción, debe configurar una cuenta de Google en su dispositivo Android Virtual. (En el emulador, vaya a **configuración** y haga clic en **Agregar cuenta**). Asegúrese de que el emulador está conectado a Internet.

>[AZURE.NOTE] Diseño de la notificación en Android 5.0 y versiones posteriores representa una desviación significativa del de versiones anteriores. Para obtener más información, vea [Notificaciones Android](http://go.microsoft.com/fwlink/?LinkId=615880).


1. En **Herramientas**, haga clic en **Abrir el Administrador de emulador Android**, seleccione el dispositivo y, a continuación, haga clic en **Editar**.

    ![][18]

2. Seleccione **Las API de Google** de **destino**y, a continuación, haga clic en **Aceptar**.

    ![][19]

3. En la barra de herramientas superior, haga clic en **Ejecutar**y, a continuación, seleccione la aplicación. Esto inicia el emulador y ejecuta la aplicación.

  La aplicación recupera la *registrationId* de GCM y registra con el hub de notificación.

##<a name="send-notifications-from-your-backend"></a>Enviar notificaciones desde los back-end


Puede probar recibir notificaciones en su aplicación mediante el envío de notificaciones en el [Portal de Azure clásica] a través de la ficha Depurar en el hub de notificación, como se muestra en la pantalla siguiente.

![][30]


Normalmente se envían las notificaciones de inserción en un servicio de back-end como servicios móviles o ASP.NET a través de una biblioteca compatible. También puede usar la API de REST directamente al enviar mensajes de notificación si una biblioteca no está disponible para su back-end.

Aquí tiene una lista de algunos otros tutoriales que puede que desee revisar para enviar notificaciones:

- ASP.NET: Vea [Usar Hubs de notificación para las notificaciones de inserción a los usuarios].
- Azure Java Hubs de notificación SDK: vea [cómo usar Hubs de notificación de Java](notification-hubs-java-push-notification-tutorial.md) para enviar notificaciones de Java. Esto se ha probado en Eclipse para el desarrollo de Android.
- PHP: Vea [cómo usar Hubs de notificación de PHP](notification-hubs-php-push-notification-tutorial.md).


En las subsecciones siguientes del tutorial, enviar notificaciones mediante una aplicación de consola de .NET y mediante el servicio móvil a través de una secuencia de comandos de nodo.

####<a name="optional-send-notifications-by-using-a-net-app"></a>(Opcional) Enviar notificaciones mediante una aplicación de .NET

En esta sección, le enviaremos notificaciones mediante una aplicación de consola de .NET

1. Crear una nueva aplicación de consola de Visual C#:

    ![][20]

2. En Visual Studio, haga clic en **Herramientas**, haga clic en **Administrador de paquetes de NuGet**y, a continuación, haga clic en la **Consola del Administrador de paquetes**.

    Muestra la consola del Administrador de paquetes de Visual Studio.

3. En la ventana de la consola del Administrador de paquetes, establecer **predeterminado de proyecto** para el nuevo proyecto de aplicación de consola y, a continuación, en la ventana de la consola, ejecute el siguiente comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Esto agrega una referencia en el SDK de Hubs de notificación de Azure utilizando el <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">paquete Microsoft.Azure.Notification Hubs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Abra el archivo Program.cs y agregue las siguientes `using` instrucción:

        using Microsoft.Azure.NotificationHubs;

5. En la `Program` de clase, agregue el método siguiente. Actualizar el texto de marcador de posición con su nombre de concentrador y la cadena de conexión de *DefaultFullSharedAccessSignature* desde el [Portal de Azure clásico].

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }

6. Agregue las siguientes líneas en el método **Main** :

         SendNotificationAsync();
         Console.ReadLine();

7. Presione la tecla F5 para ejecutar la aplicación. Debe recibir una notificación en la aplicación.

    ![][21]

####<a name="optional-send-notifications-by-using-a-mobile-service"></a>(Opcional) Enviar notificaciones mediante un servicio móvil

1. Siga [Introducción a servicios para dispositivos móviles].

1. Inicie sesión en el [Portal de clásico de Azure]y seleccione el servicio de móvil.

2. Seleccione la ficha **Programador** en la parte superior.

    ![][22]

3. Crear un nuevo trabajo programado, inserte un nombre y seleccione **a petición**.

    ![][23]

4. Cuando se crea el trabajo, haga clic en el nombre del trabajo. A continuación, haga clic en la pestaña de la **secuencia de comandos** en la barra superior.

5. Inserte el siguiente script dentro de la función de programador. Asegúrese de reemplazar los marcadores de posición con su nombre de concentrador de notificación y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente. Haga clic en **Guardar**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6. Haga clic en **Ejecutar una vez** en la barra inferior. Recibirá una notificación.

##<a name="next-steps"></a>Pasos siguientes

En este sencillo ejemplo, las notificaciones se difusión a todos los dispositivos Android. Para dirigirse a usuarios específicos, consulte el tutorial [Hubs de notificación de uso para las notificaciones de inserción a los usuarios]. Si desea agrupar los usuarios por grupos de interés, puede leer [Hubs de notificación de uso para enviar noticias]. Más información sobre cómo usar notificación Hubs en [Notificación Hubs guía] y en la [Notificación Hubs procedimientos para Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a servicios para dispositivos móviles]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Portal de clásico de Azure]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Guía de Hubs de notificación]: http://msdn.microsoft.com/library/jj927170.aspx
[Notificación Hubs procedimientos para Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Usar Hubs de notificación para las notificaciones de inserción a los usuarios]: /manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de notificación para enviar noticias]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Componente de cliente de mensajería de nube de Google]: http://components.xamarin.com/view/GCMClient/
[Componente de mensajería de Azure]: http://components.xamarin.com/view/azure-messaging
