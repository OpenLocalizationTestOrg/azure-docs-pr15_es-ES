<properties
    pageTitle="Introducción a Azure Mobile compromiso para iOS en objetivo C | Microsoft Azure"
    description="Aprenda a usar Azure Mobile compromiso con las notificaciones de inserción y analíticas iOS aplicaciones para."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/05/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Introducción a Azure Mobile compromiso para las aplicaciones de iOS de objetivo C

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tema muestra cómo usar el compromiso de Azure Mobile comprender el uso de la aplicación y enviar notificaciones de inserción a usuarios segmentados para una aplicación de iOS.
En este tutorial, se crea una aplicación de iOS en blanco que recopila datos básico y recibe las notificaciones de inserción con el sistema de notificación de inserción de Apple (APN).

Este tutorial requiere lo siguiente:

+ 8 XCode, que se puede instalar desde el MAC App Store
+ el [compromiso de móvil iOS SDK]

Realizar este tutorial es un requisito previo para todos los otros tutoriales de Mobile compromiso para las aplicaciones de iOS.

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).

##<a id="setup-azme"></a>Compromiso de Mobile de configuración de la aplicación de iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar la aplicación con el compromiso de móvil de back-end

Este tutorial presenta una "integración básica," que es el conjunto mínimo necesario para recopilar datos y enviar una notificación de inserción. La documentación de integración completa puede encontrarse en la [integración de contratación móvil iOS SDK](mobile-engagement-ios-sdk-overview.md)

Se crea una aplicación básica con XCode para demostrar la integración.

###<a name="create-a-new-ios-project"></a>Crear un nuevo proyecto de iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Conectar la aplicación con el compromiso de móvil de back-end

1. Descargue el [compromiso de móvil iOS SDK].
2. Extraer la. tar.gz archivo a una carpeta en el equipo.
3. Haga clic en el proyecto y, a continuación, seleccione **Agregar archivos a**.

    ![][1]

4. Vaya a la carpeta que haya extraído el SDK, seleccione la `EngagementSDK` carpeta y luego pulse **Aceptar**.

    ![][2]

5. Abra la ficha **Crear fases** y, en el menú **Binario con las bibliotecas de vínculos** , agregue los marcos tal como se muestra a continuación:

    ![][3]

6. Volver al portal de Azure en la página de **Información de conexión** de la aplicación y copie la cadena de conexión.

    ![][4]

7. Agregue la siguiente línea de código en el archivo **AppDelegate.m** .

        #import "EngagementAgent.h"

8. Pegar la cadena de conexión en la `didFinishLaunchingWithOptions` delegado.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
        }

9. `setTestLogEnabled`es una instrucción opcional que permite registros SDK para identificar los problemas. 

##<a id="monitor"></a>Habilitar la supervisión en tiempo real

Para iniciar el envío de datos y garantizar que los usuarios están activos, debe enviar al menos una pantalla (actividad) del back-end de compromiso de móvil.

1. Abra el archivo **ViewController.h** e importar **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Ahora reemplace la clase superior de la interfaz de **ViewController** `EngagementViewController`:

    `@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar las notificaciones de inserción y en la aplicación de mensajería

Compromiso de móvil le permite interactuar con los usuarios y póngase en contacto con las notificaciones de inserción y de la aplicación en el contexto de las campañas de mensajería. Este módulo se denomina alcance en el portal de compromiso de móvil.
Las siguientes secciones configuradas su aplicación para recibirlas.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitar la aplicación recibir notificaciones de inserción silencioso

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]  

### <a name="add-the-reach-library-to-your-project"></a>Agregar la biblioteca de acceso a su proyecto

1. Haga clic en el proyecto.
2. Seleccione **Agregar archivo**.
3. Vaya a la carpeta que haya extraído el SDK.
4. Seleccione el `EngagementReach` carpeta.
5. Haga clic en **Agregar**.

### <a name="modify-your-application-delegate"></a>Modificar al delegado de la aplicación

1. En el archivo **AppDeletegate.m** , importar el módulo de contratación alcance.

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>

2. Dentro de la `application:didFinishLaunchingWithOptions` método, cree un módulo de alcance y pasar a la línea de inicialización compromiso existente:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Habilitar la aplicación recibir notificaciones de inserción APN

1. Agregue la línea siguiente a la `application:didFinishLaunchingWithOptions` método:

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

2. Agregar la `application:didRegisterForRemoteNotificationsWithDeviceToken` método como sigue:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }

3. Agregar la `didFailToRegisterForRemoteNotificationsWithError` método como sigue:

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           
           NSLog(@"Failed to get token, error: %@", error);
        }

4. Agregar la `didReceiveRemoteNotification:fetchCompletionHandler` método como sigue:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Compromiso de móvil iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

