<properties
    pageTitle="Azure compromiso móvil iOS Introducción SDK | Microsoft Azure"
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

#<a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK para Azure Mobile contratación

Empiece aquí para obtener todos los detalles sobre cómo integrar Azure Mobile compromiso en una aplicación de iOS. Si le gustaría pruébelo en primer lugar, asegúrese de que seguir nuestro [tutorial de 15 minutos](mobile-engagement-ios-get-started.md).

Haga clic para ver el [Contenido de SDK](mobile-engagement-ios-sdk-content.md)

##<a name="integration-procedures"></a>Procedimientos de integración
1. Empiece aquí: [cómo integrar compromiso Mobile en su aplicación iOS](mobile-engagement-ios-integrate-engagement.md)

2. Para las notificaciones: [cómo integrar alcance (notificaciones) en la aplicación de iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implementación del plan de etiqueta: [cómo usar el compromiso Mobile avanzadas etiquetado API en su aplicación iOS](mobile-engagement-ios-use-engagement-api.md)


##<a name="release-notes"></a>Notas de la versión

### <a name="400-09122016"></a>4.0.0 (09/12/2016)

-   Notificación fija no accionada en dispositivos iOS 10.
-   Descartar XCode 7.

Para una versión anterior, vea las [notas de la versión completa](mobile-engagement-ios-release-notes.md)

##<a name="upgrade-procedures"></a>Procedimientos de actualización

Si ya ha integrado una versión anterior de contratación en la aplicación, debe tener en cuenta los siguientes puntos al actualizar el SDK.

Deberá seguir diversos procedimientos si hay varias versiones de la consulte SDK los [Procedimientos de actualización](mobile-engagement-ios-upgrade-procedure.md).

Para cada nueva versión del SDK debe reemplazarse (quitar y volver a importar en xcode) las carpetas EngagementSDK y EngagementReach.

###<a name="from-300-to-400"></a>Desde 3.0.0 a 4.0.0

### <a name="xcode-8"></a>XCode 8
8 XCode es obligatorio empezando desde la versión 4.0.0 del SDK.

> [AZURE.NOTE] Si realmente depende XCode 7 puede usar [iOS compromiso SDK v3.2.4](https://aka.ms/r6oouh). Hay un error conocido en el módulo de alcance de esta versión anterior mientras se ejecuta en dispositivos iOS 10: las notificaciones del sistema no están activadas. Para corregir esto, deberá implementar la API desusada `application:didReceiveRemoteNotification:` en su aplicación delegar como sigue:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **No se recomienda esta solución** como este comportamiento, puede cambiar cualquier actualización de la versión de próximas iOS (incluso los pequeños) porque este iOS API es obsoleto. Debe cambiar a 8 XCode tan pronto como sea posible.

#### <a name="usernotifications-framework"></a>UserNotifications framework
Debe agregar el `UserNotifications` marco en las fases de compilación.

en el Explorador de proyectos, abra el panel de proyecto y seleccione el destino correcto. A continuación, abra la pestaña **"Crear fases"** y en el menú **"binario con bibliotecas de vínculos"** , agregue framework `UserNotifications.framework` -establecer el vínculo como`Optional`

#### <a name="application-push-capability"></a>Capacidad de inserción de aplicación
XCode 8 puede restablecer su aplicación capacidad de inserción, compruebe el `capability` ficha de su destino seleccionado.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Agregar el nuevo código de registro de notificación de iOS 10
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

#### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Si ya tiene su propia implementación de UNUserNotificationCenterDelegate

El SDK tiene su propia implementación del protocolo UNUserNotificationCenterDelegate. Se usa el SDK para supervisar el ciclo de vida de notificaciones de contratación en dispositivos que ejecutan en iOS 10 o superior. Si el SDK detecta al delegado no usará su propia implementación porque puede haber solo un delegado UNUserNotificationCenter por la aplicación. Esto significa que tendrá que agregar la lógica de contratación a su propio delegado.

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