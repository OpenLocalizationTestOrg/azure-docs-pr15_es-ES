<properties
    pageTitle="Agregar las notificaciones de inserción a la aplicación Xamarin.Forms | Microsoft Azure"
    description="Aprenda a usar los servicios de Azure para enviar notificaciones de inserción de múltiples plataformas a sus aplicaciones de Xamarin.Forms."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Agregar las notificaciones de inserción a la aplicación de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Información general

En este tutorial, agregue inserción notificaciones para todos los proyectos ha generado desde el [Inicio rápido de Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md) para que se envía una notificación de inserción a todos los clientes de varias plataformas cada vez que se inserta un registro.

Si no utiliza el proyecto de servidor de inicio rápido descargado, tendrá el paquete de extensión de notificación de inserción. Para obtener más información, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Requisitos previos

* Para iOS, necesitará un [programa para desarrolladores de Apple pertenencia](https://developer.apple.com/programs/ios/) y un dispositivo iOS físico porque el [iOS simulator no admite las notificaciones de inserción](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="configure-hub"></a>Configurar un concentrador de notificación

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Actualizar el proyecto de servidor para enviar notificaciones de inserción

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##<a name="optional-configure-and-run-the-android-project"></a>(Opcional) Configurar y ejecutar el proyecto Android

Completar esta sección para habilitar las notificaciones de inserción para el proyecto Xamarin.Forms Droid para Android.


###<a name="enable-firebase-cloud-messaging-fcm"></a>Habilitar nube Firebase mensajería (FCM)

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

###<a name="configure-the-mobile-app-backend-to-send-push-requests-using-fcm"></a>Configurar el servidor de la aplicación móvil para enviar peticiones de inserción mediante FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###<a name="add-push-notifications-to-the-android-project"></a>Agregar las notificaciones de inserción al proyecto Android

Con el servidor configurado con FCM, podemos agregar componentes códigos al cliente para registrar con FCM, registrar las notificaciones de inserción con Hubs de notificación de Azure a través de la aplicación móvil de back-end y recibir notificaciones.

1. En el proyecto **Droid** , haga clic en la carpeta de **componentes** , haga clic en **Obtener más componentes...**, busque el componente de **Cliente de mensajería de nube de Google** y agregarlo al proyecto. Este componente es compatible con las notificaciones de inserción de un proyecto Xamarin Android.


2. Abra el archivo de proyecto MainActivity.cs y agregue la siguiente instrucción using en la parte superior del archivo:

        using Gcm.Client;

3. Agregue el código siguiente al método **OnCreate** después de la llamada a **LoadApplication**:

        try
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }


4. Agregar un nuevo método de auxiliar **CreateAndShowDialog** , como sigue:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }


5. Agregue el código siguiente a la clase de **actividad principal** :

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Esto expone la instancia actual de la **actividad principal** , por lo que podemos ejecutar en el subproceso de interfaz de usuario principal.

6. Inicializar el `instance`, variable al principio del método **OnCreate** , como se indica a continuación.

        // Set the current instance of MainActivity.
        instance = this;

2. Agregar un nuevo archivo de clase al proyecto **Droid** denominado `GcmService.cs`y asegúrese de que las siguientes instrucciones **using** están presentes en la parte superior del archivo:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;


9. Agregue las siguientes solicitudes de permisos en la parte superior del archivo, después de las instrucciones de **uso** y antes de la declaración de **espacio de nombres** .

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Agregue la siguiente definición de clase en el espacio de nombres. 

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
        }

    >[AZURE.NOTE]Reemplace **< númeroProyecto >** con el número de proyecto que se indicó anteriormente.   

11. Reemplazar la clase **GcmService** vacía con el código siguiente, que usa el receptor de difusión de nuevo:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


12. Agregue el código siguiente a la clase **GcmService** que invalida el controlador de eventos de **OnRegistered** e implementa un método de **registrar** .

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

        Note that this code uses the `messageParam` parameter in the template registration. 

13. Agregue el código siguiente que implementa **OnMessage**: 

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Esto controla las notificaciones entrantes y enviarlos al jefe de notificación que se muestren.

14. **GcmServiceBase** requiere implementar los métodos de controlador **OnUnRegistered** y **AlOcurrirError** , que puede llevar a cabo como sigue:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Ahora está listo probar las notificaciones de inserción en la aplicación que se ejecuta en un dispositivo Android o el emulador.

###<a name="test-push-notifications-in-your-android-app"></a>Notificaciones de inserción de prueba en su aplicación Android

Los dos primeros pasos son necesarios únicamente para comprobar en un emulador.

1. Asegúrese de que está implementando a o depuración en un dispositivo virtual que tiene Google APIs establecer como destino, tal como se muestra a continuación, en el administrador del dispositivo Virtual Android (AVD).

2. Agregar una cuenta de Google en el dispositivo Android haciendo clic en **aplicaciones** > **configuración** > **Agregar una cuenta**y, a continuación, siga las indicaciones para usar Agregar una cuenta de Google existente en el dispositivo para crear una nueva.

1. En Visual Studio o Xamarin Studio, haga clic con el botón secundario del mouse en el proyecto **Droid** y haga clic en **establecer como proyecto de inicio**.

2. Presione el botón **Ejecutar** para generar el proyecto e iniciar la aplicación en su dispositivo Android o emulador.

3. En la aplicación, escriba una tarea y, a continuación, haga clic en el signo más (**+**) icono.

4. Compruebe que se recibe una notificación cuando se agrega un elemento.


##<a name="optional-configure-and-run-the-ios-project"></a>(Opcional) Configurar y ejecutar el proyecto de iOS

Esta sección es para ejecutar el proyecto de iOS Xamarin para dispositivos iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]


####<a name="configure-the-notification-hub-for-apns"></a>Configurar el hub de notificación para APN

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

A continuación va a configurar la configuración de proyecto iOS en Xamarin Studio o Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####<a name="add-push-notifications-to-your-ios-app"></a>Agregar las notificaciones de inserción a la aplicación de iOS

1. En el proyecto de **iOS** , abra AppDelegate.cs agregar la siguiente instrucción **using** a la parte superior del archivo de código.

        using Newtonsoft.Json.Linq;

4. En la clase **AppDelegate** , agregue un reemplazo para el evento **RegisteredForRemoteNotifications** registrar las notificaciones de:

        public override void RegisteredForRemoteNotifications(UIApplication application, 
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. En **AppDelegate**, agregue también el reemplazo siguiente para el controlador de eventos de **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification(UIApplication application, 
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    Este método controla notificaciones entrantes mientras se ejecuta la aplicación.

2. En la clase **AppDelegate** , agregue el código siguiente al método **FinishedLaunching** : 

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Esto habilita la compatibilidad con notificaciones remotas y las solicitudes de registro de inserción.

La aplicación se ha actualizado para admitir notificaciones de inserción.

####<a name="test-push-notifications-in-your-ios-app"></a>Notificaciones de inserción de prueba en su aplicación iOS

1. Haga clic con el botón secundario del mouse en el proyecto de iOS y haga clic en **establecer como proyecto de StartPp**.

2. Presione el botón **Ejecutar** o **F5** en Visual Studio para generar el proyecto e inicie la aplicación en un dispositivo iOS, luego haga clic en **Aceptar** para aceptar las notificaciones de inserción.

    > [AZURE.NOTE] Explícitamente debe aceptar las notificaciones de inserción de la aplicación. Esta solicitud sólo se produce la primera vez que se ejecute la aplicación.

3. En la aplicación, escriba una tarea y, a continuación, haga clic en el signo más (**+**) icono.

4. Compruebe que se recibe una notificación y haga clic en **Aceptar** para cerrar la notificación.


##<a name="optional-configure-and-run-the-windows-projects"></a>(Opcional) Configurar y ejecutar los proyectos de Windows

Esta sección es para ejecutar el Xamarin.Forms WinApp y WinPhone81 proyectos para dispositivos de Windows. Estos pasos también admiten proyectos de plataforma de Windows Universal (UWP). Puede omitir esta sección si no está trabajando con los dispositivos de Windows.


####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrar su aplicación de Windows para las notificaciones de inserción con WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####<a name="configure-the-notification-hub-for-wns"></a>Configurar el hub de notificación para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####<a name="add-push-notifications-to-your-windows-app"></a>Agregar las notificaciones de inserción a la aplicación de Windows

1. En Visual Studio, abra **App.xaml.cs** en un proyecto de Windows y agregue las siguientes instrucciones **using** .

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Reemplazar `<your_TodoItemManager_portable_class_namespace>` con espacio de nombres de su proyecto portátil que contiene el `TodoItemManager` clase.
 

2. En App.xaml.cs agregar el siguiente método **InitNotificationsAsync** : 

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    Este método obtiene el canal de notificación de inserción y registra una plantilla para recibir notificaciones de plantilla de su centro de notificación. Una notificación de plantilla que admita *messageParam* se entregarán a este cliente.

3. En App.xaml.cs, actualice la definición de método de controlador de eventos de **OnLaunched** agregando el `async` modificadora, a continuación, agregue la siguiente línea de código al final del método: 

        await InitNotificationsAsync();

    Esto se asegura de que el registro de la notificación de inserción se crea o actualiza cada vez que se inicia la aplicación. Es importante hacer esto para garantizar que el canal de inserción WNS siempre está activo.  

4. En el Explorador de soluciones para Visual Studio, abra el archivo **Package.appxmanifest** y establezca **Capaz de brindar** en **Sí** en **notificaciones**.

5. Crear la aplicación y comprobar que no contienen errores.  Aplicación de cliente ahora debe registrarse para las notificaciones de plantilla desde la aplicación móvil de back-end. Repita esta sección para cada proyecto de Windows en su solución.


####<a name="test-push-notifications-in-your-windows-app"></a>Notificaciones de inserción de prueba en la aplicación de Windows

1. En Visual Studio, haga clic con el botón secundario del mouse en un proyecto de Windows y haga clic en **establecer como proyecto de inicio**.

2. Presione el botón **Ejecutar** para generar el proyecto e iniciar la aplicación.

3. En la aplicación, escriba un nombre para una nueva todoitem y, a continuación, haga clic en el signo más (**+**) icono para agregarlo.

4. Compruebe que se recibe una notificación cuando se agrega el elemento.

##<a name="next-steps"></a>Pasos siguientes

Más información sobre las notificaciones de inserción:

* [Diagnosticar problemas de notificación de inserción](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Existen varias razones por las notificaciones pueden quitar o no terminan en dispositivos. Este tema muestra cómo analizar y averiguar la causa de errores de notificación de inserción. 

Considere la posibilidad de continuar con uno de los siguientes tutoriales:

* [Agregar autenticación para su aplicación](app-service-mobile-xamarin-forms-get-started-users.md)  
Aprenda a autenticar a los usuarios de la aplicación con un proveedor de identidades.

* [Habilitar la sincronización sin conexión para la aplicación](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Obtenga información sobre cómo agregar la aplicación con una aplicación móvil de back-end de soporte técnico sin conexión. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no hay ninguna conexión de red.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

