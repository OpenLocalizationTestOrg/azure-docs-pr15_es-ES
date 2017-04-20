<properties
    pageTitle="Agregar las notificaciones de inserción a la aplicación de Xamarin.iOS con el servicio de aplicación de Azure"
    description="Aprenda a usar el servicio de aplicación de Azure para enviar notificaciones de inserción a la aplicación de Xamarin.iOS"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinios-app"></a>Agregar las notificaciones de inserción a la App Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Información general

En este tutorial, agregue las notificaciones de inserción para el proyecto de [Inicio rápido de Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) para que se envía una notificación de inserción al dispositivo cada vez que se inserta un registro.

Si no utiliza el proyecto de servidor de inicio rápido descargado, tendrá el paquete de extensión de notificación de inserción. Para obtener más información, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Requisitos previos

* Complete el tutorial [rápido Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) .

* Un dispositivo iOS física. Notificaciones de inserción no son compatibles con el simulador iOS.

##<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrar la aplicación para las notificaciones de inserción en el portal de programadores de Apple

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

##<a name="configure-your-mobile-app-to-send-push-notifications"></a>Configurar la aplicación móvil para enviar notificaciones de inserción

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Actualizar el proyecto de servidor para enviar notificaciones de inserción

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="configure-your-xamarinios-project"></a>Configurar el proyecto Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##<a name="add-push-notifications-to-your-app"></a>Agregar las notificaciones de inserción a la aplicación

1. En **QSTodoService**, agregue la siguiente propiedad para que **AppDelegate** puede adquirir al cliente móvil:

            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Agregue las siguientes `using` instrucción a la parte superior del archivo **AppDelegate.cs** .

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. En **AppDelegate**, reemplace el evento de **FinishedLaunching** :

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. En el mismo archivo, reemplace el evento de **RegisteredForRemoteNotifications** . En este código se va a registrar una notificación de plantilla simple que se enviará a todas las plataformas compatibles con el servidor.

    Para obtener más información sobre plantillas con Hubs de notificación, consulte [plantillas](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. A continuación, reemplace el evento de **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

La aplicación se ha actualizado para admitir notificaciones de inserción.

## <a name="test"></a>Notificaciones de inserción de prueba en la aplicación

1. Presione el botón **Ejecutar** para generar el proyecto e inicie la aplicación en un dispositivo capaz de iOS y luego haga clic en **Aceptar** para aceptar las notificaciones de inserción.

    > [AZURE.NOTE] Explícitamente debe aceptar las notificaciones de inserción de la aplicación. Esta solicitud sólo se produce la primera vez que se ejecute la aplicación.

2. En la aplicación, escriba una tarea y, a continuación, haga clic en el signo más (**+**) icono.

3. Compruebe que se recibe una notificación y haga clic en **Aceptar** para cerrar la notificación.

4. Repita el paso 2 e inmediatamente la aplicación se cierra, compruebe que se muestra una notificación.

En este tutorial se completó correctamente.

<!-- Images. -->

<!-- URLs. -->



