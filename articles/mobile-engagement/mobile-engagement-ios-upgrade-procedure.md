<properties
    pageTitle="Azure compromiso móvil iOS SDK actualizar procedimiento | Microsoft Azure"
    description="Últimas actualizaciones y procedimientos para iOS SDK para Azure Mobile contratación"
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
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="upgrade-procedures"></a>Procedimientos de actualización

Si ya ha integrado una versión anterior de contratación en la aplicación, debe tener en cuenta los siguientes puntos al actualizar el SDK.

Para cada nueva versión del SDK debe reemplazarse (quitar y volver a importar en xcode) las carpetas EngagementSDK y EngagementReach.

##<a name="from-300-to-400"></a>Desde 3.0.0 a 4.0.0

### <a name="xcode-8"></a>XCode 8
8 XCode es obligatorio empezando desde la versión 4.0.0 del SDK.

> [AZURE.NOTE] Si realmente depende XCode 7 puede usar [iOS compromiso SDK v3.2.4](https://aka.ms/r6oouh). Hay un error conocido en el módulo de alcance de esta versión anterior mientras se ejecuta en dispositivos iOS 10: las notificaciones del sistema no están activadas. Para corregir esto, deberá implementar la API desusada `application:didReceiveRemoteNotification:` en su aplicación delegar como sigue:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **No se recomienda esta solución** como este comportamiento, puede cambiar cualquier actualización de la versión de próximas iOS (incluso los pequeños) porque este iOS API es obsoleto. Debe cambiar a 8 XCode tan pronto como sea posible.

### <a name="usernotifications-framework"></a>UserNotifications framework
Debe agregar el `UserNotifications` marco en las fases de compilación.

en el Explorador de proyectos, abra el panel de proyecto y seleccione el destino correcto. A continuación, abra la pestaña **"Crear fases"** y en el menú **"binario con bibliotecas de vínculos"** , agregue framework `UserNotifications.framework` -establecer el vínculo como`Optional`

### <a name="application-push-capability"></a>Capacidad de inserción de aplicación
XCode 8 puede restablecer su aplicación capacidad de inserción, compruebe el `capability` ficha de su destino seleccionado.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Agregar el nuevo código de registro de notificación de iOS 10
El fragmento de código anterior para registrar la aplicación para notificaciones aún funciona, pero usa desusadas API mientras se ejecuta en iOS 10.

Importar la `User Notification` framework:

        #import <UserNotifications/UserNotifications.h> 

En el delegado de aplicación `application:didFinishLaunchingWithOptions` método reemplazar:

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

por:

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

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Si ya tiene su propia implementación de UNUserNotificationCenterDelegate

El SDK también tiene su propia implementación del protocolo UNUserNotificationCenterDelegate. Se usa el SDK para supervisar el ciclo de vida de notificaciones de contratación en dispositivos que ejecutan en iOS 10 o superior. Si el SDK detecta al delegado no usará su propia implementación porque puede haber solo un delegado UNUserNotificationCenter por la aplicación. Esto significa que tendrá que agregar la lógica de contratación a su propio delegado.

Hay dos maneras de lograrlo.

Enviando el delegado llama el SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

O mediante la herencia de la `AEUserNotificationHandler` clase

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [AZURE.NOTE] Puede determinar si una notificación proviene de contratación o no pasando su `userInfo` diccionario para el agente `isEngagementPushPayload:` método de clase.

##<a name="from-200-to-300"></a>Desde 2.0.0 a 3.0.0
En el texto de soporte técnico para iOS 4.X. Desde esta versión de destino de implementación de la aplicación debe tener al menos iOS 6.

Si está utilizando el alcance de la aplicación, debe agregar `remote-notification` valor para el `UIBackgroundModes` matriz en el archivo Info.plist para recibir notificaciones remotas.

El método `application:didReceiveRemoteNotification:` debe reemplazarse por `application:didReceiveRemoteNotification:fetchCompletionHandler:` en el delegado de la aplicación.

"AEPushDelegate.h" es obsoleto interfaz y tiene que quitar todas las referencias. Esto incluye la eliminación de `[[EngagementAgent shared] setPushDelegate:self]` y los métodos de delegados desde el delegado de la aplicación:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##<a name="from-1160-to-200"></a>Desde 1.16.0 a 2.0.0
La siguiente describe cómo migrar una integración SDK desde el servicio de Capptain ofrecido por Capptain SAS a una aplicación con tecnología de Azure Mobile compromiso.
Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a 1.16 en primer lugar, a continuación, aplique el procedimiento siguiente.

>[AZURE.IMPORTANT] Capptain y Mobile compromiso no son los mismos servicios y el procedimiento siguiente sólo resalta cómo migrar la aplicación de cliente. Migrar el SDK en la aplicación no se migrarán los datos de los servidores de Capptain a los servidores de compromiso de móvil

### <a name="agent"></a>Agente

El método `registerApp:` se ha sustituido por el nuevo método `init:`. El delegado de la aplicación se deben actualizar según corresponda y usar cadena de conexión:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

Seguimiento de SmartAd se ha quitado de SDK debe quitar todas las instancias de `AETrackModule` clase

### <a name="class-name-changes"></a>Cambios de nombres de clase

Como parte de la reconfiguración de marca, hay algunos de los nombres de archivo o de clase que debe cambiar.

Todas las clases con el prefijo "CP" se cambia el nombre con el prefijo "AE".

Ejemplo:

-   `CPModule.h`se cambia el nombre a `AEModule.h`.

Todas las clases con el prefijo "Capptain" se cambia el nombre con el prefijo "Compromiso".

Ejemplos:

-   La clase `CapptainAgent` cambia a `EngagementAgent`.
-   La clase `CapptainTableViewController` cambia a `EngagementTableViewController`.
-   La clase `CapptainUtils` cambia a `EngagementUtils`.
-   La clase `CapptainViewController` cambia a `EngagementViewController`.
