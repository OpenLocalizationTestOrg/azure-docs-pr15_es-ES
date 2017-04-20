<properties
    pageTitle="Agregar notificaciones Push para iOS aplicación con aplicaciones móviles de Azure"
    description="Aprenda a usar aplicaciones de Azure móvil para enviar notificaciones de inserción a la aplicación."
    services="app-service\mobile"
    documentationCenter="ios"
    manager="yochayk"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="yuaxu"/>


# <a name="add-push-notifications-to-your-ios-app"></a>Agregar notificaciones Push para su aplicación de iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Información general
En este tutorial, agregue las notificaciones de inserción para el proyecto de [Inicio rápido de iOS] para que se envía una notificación de inserción al dispositivo cada vez que se inserta un registro.

Si no utiliza el proyecto de servidor de inicio rápido descargado, tendrá el paquete de extensión de notificación de inserción. Para obtener más información, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

[IOS simulator no admite las notificaciones de inserción](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Necesita un dispositivo iOS físico y una [participación en el programa de desarrolladores de Apple](https://developer.apple.com/programs/ios/).

##<a name="configure-hub"></a>Configurar concentrador de notificación

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Aplicación de registro para las notificaciones de inserción

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar Azure para enviar notificaciones de inserción

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Actualizar back-end para enviar notificaciones de inserción

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Agregar las notificaciones de inserción a la aplicación

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Notificaciones de inserción de prueba

[AZURE.INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>Más

* Plantillas de proporcionan flexibilidad para enviar Push entre plataformas e inserta localizado. [Cómo usar iOS biblioteca de cliente para las aplicaciones móviles de Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) se muestra cómo registrar plantillas.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Inicio rápido de iOS]: app-service-mobile-ios-get-started.md
