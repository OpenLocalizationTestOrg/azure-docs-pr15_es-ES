<properties
    pageTitle="iOS notificaciones Push con Hubs de notificación para las aplicaciones de Xamarin | Microsoft Azure"
    description="En este tutorial, aprenderá a usar Hubs de notificación de Azure para enviar notificaciones de inserción a una aplicación de iOS Xamarin."
    services="notification-hubs"
    keywords="IOS push notificaciones de inserción mensajes, notificaciones de inserción, insertar un mensaje"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>iOS notificaciones Push con Hubs de notificación para las aplicaciones de Xamarin

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Información general
> [AZURE.IMPORTANT] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

En este tutorial se muestra cómo usar Hubs de notificación de Azure para enviar notificaciones de inserción a la aplicación iOS.
Debe crear una aplicación de Xamarin.iOS en blanco que recibe las notificaciones de inserción mediante el [Servicio de notificación de inserción de Apple (APN)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). Cuando haya terminado, podrá usar su centro de notificación para difundir las notificaciones de inserción a todos los dispositivos que ejecutan la aplicación. El código finalizado está disponible en la [aplicación de NotificationHubs] [ GitHub] muestra.

Este tutorial muestra el escenario de difusión del mensaje de inserción simple con Hubs de notificación.

##<a name="prerequisites"></a>Requisitos previos

Este tutorial requiere lo siguiente:

+ [Xcode 6.0][Install Xcode]
+ Un iOS 7.0 (o una versión posterior) dispositivo compatible
+ iOS miembros del programa de desarrollador
+ [Xamarin Studio]

   > [AZURE.NOTE] Debido a los requisitos de configuración de notificaciones de inserción de iOS debe implementar y probar la aplicación de ejemplo en un dispositivo físico iOS (iPhone o iPad) en lugar de en el simulador.

Realizar este tutorial es un requisito previo para todos los otros tutoriales Hubs de notificación para las aplicaciones de iOS Xamarin.


[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##<a name="configure-your-notification-hub"></a>Configurar su centro de notificación

Esta sección le guiará a través de la creación de un nuevo concentrador de notificación y configurar la autenticación con APN utilizando el certificado push **. p12** que ha creado. Si desea usar un concentrador de notificación que ya ha creado, vaya al paso 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>Ya que va a configurar la conexión APNS, en el Portal de Azure, abra la configuración de concentrador de notificación, ande haga clic en <b>Servicios de notificación</b>y, a continuación, haga clic en el elemento de <b>Apple (APN)</b> en la lista. Una vez finalizado, haga clic en <b>Cargar certificado</b> y seleccione el certificado <b>. p12</b> que exportó anteriormente, así como la contraseña para el certificado.</p>
<p>Asegúrese de seleccionar el modo de <b>recinto de seguridad</b> , ya que va a enviar mensajes de inserción en un entorno de desarrollo. Solo puede usar la configuración de <b>producción</b> si desea enviar notificaciones de inserción a los usuarios que ya ha adquirieron la aplicación desde la tienda.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)


El concentrador de notificación ahora está configurado para trabajar con APN y tiene las cadenas de conexión para la aplicación de registrar y enviar notificaciones de inserción.


##<a name="connect-your-app-to-the-notification-hub"></a>Conectar la aplicación al concentrador de notificación

#### <a name="create-a-new-project"></a>Crear un nuevo proyecto

1. En Xamarin Studio, cree un nuevo proyecto de iOS y seleccione la **API unificada** > plantilla de**Aplicación de la vista única** .

    ![Xamarin Studio - tipo de aplicación de selección][31]

2. Agregue una referencia al componente Mensajería de Azure. En la vista de soluciones, haga clic en la carpeta de **componentes** para el proyecto y elija **Obtener más componentes**. Busque el componente de **Mensajería de Azure** y agregue el componente a su proyecto.

3. En **AppDelegate.cs**, agregue la siguiente instrucción using:

        using WindowsAzure.Messaging;

4. Declarar una instancia de **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

5. Crear una clase **Constants.cs** con las variables siguientes:

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. En **AppDelegate.cs**, actualice **FinishedLaunching()** para que coincida con la siguiente:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }

            return true;
        }

7. Invalidar el método **RegisteredForRemoteNotifications()** en **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Invalidar el método **ReceivedRemoteNotification()** en **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Crear el siguiente método **ProcessNotification()** en **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] Puede reemplazar **FailedToRegisterForRemoteNotifications()** para controlar situaciones como sin conexión de red. Esto es especialmente importante donde el usuario puede iniciar la aplicación en modo sin conexión (por ejemplo, avión) y desea controlar inserción mensajería escenarios específicos de la aplicación.


10. Ejecute la aplicación en el dispositivo.


## <a name="sending-push-notifications"></a>Enviar notificaciones de inserción


Puede probar recibir notificaciones de inserción en la aplicación mediante el envío de notificaciones en el [Portal de Azure] a través de la capacidad de **Enviar probar** en el conjunto de herramientas de **solución de problemas** , hacia la derecha en la página concentrador de notificación, como se muestra en la pantalla siguiente.

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

Notificaciones de inserción normalmente se envían a través de un servicio de back-end como servicios móviles o ASP.NET mediante una biblioteca compatible. También puede usar la API de REST directamente al enviar mensajes de inserción si una biblioteca no está disponible en su situación. 

En este tutorial, revisaremos Simplifique y solo se muestran probar la aplicación cliente mediante el envío de notificaciones mediante .NET SDK para hubs de notificación en una aplicación de consola en lugar de un servicio de back-end. Se recomienda el tutorial de [Uso Hubs de notificación para las notificaciones de inserción a los usuarios](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) como el siguiente paso para enviar notificaciones de un back-end ASP.NET. Sin embargo, se pueden utilizar los siguientes enfoques para enviar notificaciones:

* **Interfaz REST**: puede admite la notificación de inserción en cualquier plataforma de back-end mediante la [interfaz REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure notificación Hubs .NET SDK**: en el Nuget paquete Manager para Visual Studio, ejecutar [Microsoft.Azure.NotificationHubs de paquete de instalación](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : [cómo usar Hubs de notificación de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

**Aplicaciones móviles**: para obtener un ejemplo de cómo enviar notificaciones de un servidor de aplicaciones de Azure aplicación servicio Mobile se integra con Hubs de notificación, vea [Agregar las notificaciones de inserción a la aplicación móvil](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

* **Java o PHP**: para obtener un ejemplo de cómo enviar notificaciones de inserción mediante las API de REST, consulte "Cómo usar Hubs de notificación de Java y PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).


####<a name="optional-send-push-notifications-from-a-net-console-app"></a>(Opcional) Enviar notificaciones de inserción de una aplicación de consola de .NET

En esta sección, le enviaremos las notificaciones de inserción mediante una aplicación de consola .NET simple. Para el propósito de este ejemplo, se cambiará a un entorno de desarrollo de Windows que tiene ya instalado Visual Studio.

1. En Visual Studio, cree una nueva aplicación de consola de Visual C#:

    ![Visual Studio - crear una nueva aplicación de consola][213]

2. En Visual Studio, haga clic en **Herramientas**, haga clic en **Administrador de paquetes de NuGet**y, a continuación, haga clic en la **Consola del Administrador de paquetes**.

    La consola del Administrador de paquete debería aparecer acoplada a la parte inferior de su área de trabajo de Visual Studio.

3. En la ventana de la consola del Administrador de paquetes, establecer **predeterminado de proyecto** para el nuevo proyecto de aplicación de consola y, a continuación, en la ventana de la consola, ejecute el siguiente comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Esto agrega una referencia en el SDK de Hubs de notificación de Azure utilizando el <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">paquete Microsoft.Azure.Notification Hubs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Abrir la `Program.cs` de archivos y agregue las siguientes `using` instrucción que podemos utilizar clases Azure y funciones dentro de la clase principal:

        using Microsoft.Azure.NotificationHubs;

3. En la `Program` de clase, agregue el método siguiente (no olvide reemplazar la **cadena de conexión** y el **nombre de concentrador**):

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Agregue las siguientes líneas en su `Main` método:

         SendNotificationAsync();
         Console.ReadLine();

5. Presione la tecla F5 para ejecutar la aplicación. En pocos segundos, debe aparecer una notificación de inserción en el dispositivo. Si está utilizando una red de datos de telefonía móvil o Wi-Fi, asegúrese de que tiene una conexión a internet activa en el dispositivo.

Puede encontrar todas las cargas posibles en la Apple [Local y la Guía de programación de notificaciones de inserción].

####<a name="optional-send-notifications-from-a-mobile-service"></a>(Opcional) Enviar notificaciones de un servicio móvil

En esta sección, le enviaremos usando un servicio móvil a través de una secuencia de comandos de nodo de notificaciones de inserción.

Para enviar una notificación mediante el servicio móvil, siga [Introducción a servicios para dispositivos móviles]y, a continuación:

1. Inicie sesión en el [Portal de clásico de Azure]y seleccione el servicio de móvil.

2. Seleccione la ficha **Programador** en la parte superior.

    ![Azure Portal clásica - programador][215]

3. Crear un nuevo trabajo programado, inserte un nombre y seleccione **a petición**.

    ![Portal de Azure clásica - crear nuevo trabajo][216]

4. Cuando se crea el trabajo, haga clic en el nombre del trabajo. A continuación, haga clic en la pestaña de la **secuencia de comandos** en la barra superior.

5. Inserte el siguiente script dentro de la función de programador. Asegúrese de reemplazar los marcadores de posición con su nombre de concentrador de notificación y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente. Haga clic en **Guardar**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. Haga clic en **Ejecutar una vez** en la barra inferior. Debe recibir una alerta en el dispositivo.

##<a name="next-steps"></a>Pasos siguientes

En este sencillo ejemplo, las notificaciones de inserción se difusión a todos los dispositivos iOS. Para dirigirse a usuarios específicos, consulte el tutorial [Hubs de notificación de uso para las notificaciones de inserción a los usuarios]. Si desea agrupar los usuarios por grupos de interés, puede leer [Hubs de notificación de uso para enviar noticias]. Más información sobre cómo usar notificación Hubs en [Notificación Hubs guía] y en la [Notificación Hubs procedimientos para iOS].


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introducción a servicios para dispositivos móviles]: /develop/mobile/tutorials/get-started-xamarin-ios
[Portal de clásico de Azure]: https://manage.windowsazure.com/
[Guía de Hubs de notificación]: http://msdn.microsoft.com/library/jj927170.aspx
[Notificación Hubs procedimientos para iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Usar Hubs de notificación para las notificaciones de inserción a los usuarios]: /manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de notificación para enviar noticias]: /manage/services/notification-hubs/breaking-news-dotnet

[Local y la Guía de programación de notificación de inserción]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Portal de Azure]: https://portal.azure.com
