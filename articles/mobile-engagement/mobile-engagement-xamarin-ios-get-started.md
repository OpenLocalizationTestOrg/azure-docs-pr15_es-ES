<properties
    pageTitle="Introducción a Azure compromiso móvil para Xamarin.iOS"
    description="Aprenda a usar Azure Mobile compromiso con análisis y notificaciones Push para aplicaciones Xamarin.iOS."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Introducción a Azure compromiso móvil para las aplicaciones de Xamarin.iOS

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tema muestra cómo usar el compromiso de Azure Mobile comprender el uso de la aplicación y enviar notificaciones de inserción a los usuarios segmentados en una aplicación de Xamarin.iOS.
En este tutorial, se crea una aplicación de Xamarin.iOS en blanco que recopila datos básico y recibe las notificaciones de inserción con el sistema de notificación de inserción de Apple (APN).

Este tutorial requiere lo siguiente:

+ [Xamarin Studio](http://xamarin.com/studio). También puede usar Visual Studio con Xamarin, pero este tutorial utiliza Xamarin Studio. Para obtener instrucciones de instalación, consulte [configurar e instalar para Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
+ [Compromiso móvil Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).

##<a id="setup-azme"></a>Compromiso de Mobile de configuración de la aplicación de iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar la aplicación con el compromiso de móvil de back-end

Este tutorial presenta una "integración básica" que es el mínimo conjunto necesarios para recopilar datos y enviar una notificación de inserción.

Se creará una aplicación básica con Xamarin para demostrar la integración:

###<a name="create-a-new-xamarinios-project"></a>Crear un nuevo proyecto de Xamarin.iOS

1. Inicie Xamarin Studio. Vaya a **archivo** -> **nueva** -> **solución** 

    ![][1]

2. Seleccione **Único de vista de aplicación**, asegúrese de que el idioma seleccionado es **C#** y, a continuación, haga clic en **siguiente**.

    ![][2]

3. Rellene el **Nombre de la aplicación** y el **Identificador de la organización** y, a continuación, haga clic en **siguiente**. 

    ![][3]

    > [AZURE.IMPORTANT] Asegúrese de que el perfil de publicación que utiliza finalmente para implementar la aplicación iOS usa un identificador de aplicación que coincida con exactamente con el identificador de paquete tiene aquí. 

4. Actualizar el **Nombre del proyecto**, el **Nombre de la solución** y la **ubicación** si es necesario y haga clic en **crear**.

    ![][4]
 
Xamarin Studio creará la aplicación de demostración en el que se integrará compromiso de móvil. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar la aplicación a Mobile compromiso back-end

1. Haga clic con el botón secundario del mouse en la carpeta de **paquetes** de soluciones y seleccione **Agregar paquetes**

    ![][5]

2. Buscar el **SDK de Microsoft Azure Mobile compromiso Xamarin** y agregar a la solución.  

    ![][6]
   
3. Abra **AppDelegate.cs** y agregue la siguiente instrucción using:

        using Microsoft.Azure.Engagement.Xamarin;

4. En el método **FinishedLaunching** , agregue lo siguiente para inicializar la conexión con Mobile compromiso back-end. Asegúrese de agregar la **cadena de conexión**. Este código también usa un ficticias **NotificationIcon** que agrega el SDK de contratación móvil que desea reemplazar. 

        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

##<a id="monitor"></a>Habilitar la supervisión en tiempo real

Para iniciar el envío de datos y asegurarse de que los usuarios están activos, debe enviar al menos una pantalla a la Mobile compromiso de back-end.

1. Abra **ViewController.cs** y agregue la siguiente instrucción using:

        using Microsoft.Azure.Engagement.Xamarin;

2. Reemplazar la clase desde la que `ViewController` hereda de `UIViewController` a `EngagementViewController`. 

##<a id="monitor"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar las notificaciones de inserción y en la aplicación de mensajería

Compromiso de móvil le permite interactuar con los usuarios y póngase en contacto con las notificaciones de inserción y de la aplicación en el contexto de las campañas de mensajería. Este módulo se denomina alcance en el portal de compromiso de móvil.
Las siguientes secciones configuradas su aplicación para recibirlas.

### <a name="modify-your-application-delegate"></a>Modificar al delegado de la aplicación

1. Abra la **AppDelegate.cs** y agregue la siguiente instrucción using:

        using System; 

2. Ahora dentro de la `FinishedLaunching` método, agregue lo siguiente para registrar mensajes de inserción después de`EngagementAgent.init(...)`

        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. Por último - actualizar o agregar los métodos siguientes:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. En el archivo **Info.plist** en la solución, confirme que el **Identificador de paquete** coincide con el **Identificador de aplicación** que tiene en su perfil de aprovisionamiento en el centro de desarrollo de Apple. 

    ![][7]

5. En el mismo archivo **Info.plist** , asegúrese de que ha activado la **Habilitar modos de fondo** y **Notificaciones remoto**. 

    ![][8]

6. Ejecute la aplicación en el dispositivo que se han asociado a este perfil de publicación. 

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
