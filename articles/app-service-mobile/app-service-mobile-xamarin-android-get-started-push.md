<properties
    pageTitle="Agregar las notificaciones de inserción a la aplicación Xamarin.Android | Servicio de aplicaciones de Azure"
    description="Aprenda a usar el servicio de aplicación de Azure y Azure notificación Hubs enviar notificaciones de inserción a la aplicación de Xamarin.Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Agregar las notificaciones de inserción a la aplicación de Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Información general


En este tutorial, agregue las notificaciones de inserción para el proyecto de [Inicio rápido de Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) para que se envía una notificación de inserción al dispositivo cada vez que se inserta un registro.

Si no utiliza el proyecto de servidor de inicio rápido descargado, tendrá el paquete de extensión de notificación de inserción. Para obtener más información, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .


##<a name="prerequisites"></a>Requisitos previos

Este tutorial requiere lo siguiente:

+ Una cuenta de Google activa. Puede registrarse para una cuenta de Google en [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
+ [Componente de cliente de mensajería de nube de Google](http://components.xamarin.com/view/GCMClient/).

##<a name="configure-hub"></a>Configurar un concentrador de notificación

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a id="register"></a>Habilitar Firebase mensajería en la nube

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

##<a name="configure-azure-to-send-push-requests"></a>Configurar Azure para enviar convocatorias de inserción

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

##<a id="update-server"></a>Actualizar el proyecto de servidor para enviar notificaciones de inserción

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Configurar el proyecto de cliente para las notificaciones de inserción

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Agregar código de notificaciones de inserción a la aplicación

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Notificaciones de inserción de prueba en la aplicación

Puede probar la aplicación con un dispositivo virtual en el emulador. Hay pasos de configuración adicionales necesarios cuando se ejecuta en un emulador.

1. Asegúrese de que está implementando a o depuración en un dispositivo virtual que tiene Google APIs establecer como destino, tal como se muestra a continuación, en el administrador del dispositivo Virtual Android (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Agregar una cuenta de Google en el dispositivo Android haciendo clic en **aplicaciones** > **configuración** > **Agregar una cuenta**, a continuación, siga las indicaciones.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Ejecute la aplicación todolist como antes e insertar un nuevo elemento de todo. En este momento, se muestra un icono de notificación en el área de notificación. Puede abrir el cajón de notificación para ver el texto completo de la notificación.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
