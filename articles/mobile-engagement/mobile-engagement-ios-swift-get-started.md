<properties
    pageTitle="Introducción a Azure Mobile compromiso para iOS en Swift | Microsoft Azure"
    description="Aprenda a usar Azure Mobile compromiso con análisis y notificaciones Push para iOS aplicaciones."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Introducción a Azure Mobile compromiso para iOS aplicaciones en Swift

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tema muestra cómo usar el compromiso de Azure Mobile comprender el uso de la aplicación y enviar notificaciones de inserción a usuarios segmentados para una aplicación de iOS.
En este tutorial, se crea una aplicación de iOS en blanco que recopila datos básico y recibe las notificaciones de inserción con el sistema de notificación de inserción de Apple (APN).

Este tutorial requiere lo siguiente:

+ 8 XCode, que se puede instalar desde el MAC App Store
+ el [compromiso de móvil iOS SDK]
+ Certificado de notificación de inserción (. p12) que puede obtener en el centro de desarrollo de Apple

> [AZURE.NOTE] Este tutorial usa Swift versión 3.0. 

Realizar este tutorial es un requisito previo para todos los otros tutoriales de Mobile compromiso para las aplicaciones de iOS.

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).

##<a id="setup-azme"></a>Compromiso de Mobile de configuración de la aplicación de iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar la aplicación con el compromiso de móvil de back-end

Este tutorial presenta una "integración básica," que es el conjunto mínimo necesario para recopilar datos y enviar una notificación de inserción. La documentación de integración completa puede encontrarse en la [integración de contratación móvil iOS SDK](mobile-engagement-ios-sdk-overview.md)

Se creará una aplicación básica con XCode para demostrar la integración:

###<a name="create-a-new-ios-project"></a>Crear un nuevo proyecto de iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar la aplicación a Mobile compromiso back-end

1. Descargar el [compromiso de móvil iOS SDK]
2. Extraer la. tar.gz archivo a una carpeta en el equipo
3. Haga clic con el botón secundario del mouse en el proyecto y seleccione "Agregar archivos a..."

    ![][1]

4. Vaya a la carpeta donde extraer el SDK y seleccione la `EngagementSDK` carpeta, a continuación, haga clic en Aceptar.

    ![][2]

5. Abrir la `Build Phases` ficha y, en la `Link Binary With Libraries` menú Agregar los marcos tal como se muestra a continuación:

    ![][3]

8. Crear un encabezado de puente para que pueda usar objetivo C API del SDK, elija Archivo > Nuevo > Archivo > iOS > origen > archivo de encabezado.

    ![][4]

9. Editar el archivo de encabezado puente para exponer código móvil de contratación objetivo C al código rápido, agregue las siguientes importaciones:

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"

10. En configuración de compilación, asegúrese de que la configuración en Swift compilador - generación de código de generación de objetivo C puente encabezado tiene una ruta de acceso a este encabezado. Este es un ejemplo de ruta de acceso: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (dependiendo de la ruta de acceso)**

    ![][6]

11. Volver al portal de Azure en la página de *Información de conexión* de la aplicación y copie la cadena de conexión

    ![][5]

12. Pegar la cadena de conexión en la `didFinishLaunchingWithOptions` delegado

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>Habilitar la supervisión en tiempo real

Para iniciar el envío de datos y garantizar que los usuarios están activos, debe enviar al menos una pantalla (actividad) del back-end de compromiso de móvil.

1. Abra el archivo **ViewController.swift** y reemplace la clase base de **ViewController** ser **EngagementViewController**:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar las notificaciones de inserción y en la aplicación de mensajería

Compromiso de Mobile permite interactuar y póngase en contacto con los usuarios con las notificaciones de inserción y la mensajería de la aplicación en el contexto de las campañas. Este módulo se denomina alcance en el portal de compromiso de móvil.
Las secciones siguientes realizará la instalación de la aplicación para recibirlas.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitar la aplicación recibir notificaciones de inserción silencioso

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Agregar la biblioteca de acceso a su proyecto

1. Haga clic con el botón secundario del mouse en el proyecto
2. Seleccione`Add file to ...`
3. Vaya a la carpeta que haya extraído el SDK
4. Seleccione el `EngagementReach` carpeta
5. Haga clic en Agregar
6. Editar el archivo de encabezado puente para exponer Mobile compromiso objetivo C alcance encabezados y agregue las siguientes importaciones:

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modificar al delegado de la aplicación

1. Dentro de la `didFinishLaunchingWithOptions` : crear un módulo de alcance y pasar a la línea de inicialización compromiso existente:

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Habilitar la aplicación recibir notificaciones de inserción APN
1. Agregue la línea siguiente a la `didFinishLaunchingWithOptions` método:

        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }

2. Agregar la `didRegisterForRemoteNotificationsWithDeviceToken` método como sigue:

        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Agregar la `didReceiveRemoteNotification:fetchCompletionHandler:` método como sigue:

        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Compromiso de móvil iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
