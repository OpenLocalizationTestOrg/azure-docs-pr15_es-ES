<properties
    pageTitle="Agregar las notificaciones de inserción a la aplicación Android con aplicaciones móviles de Azure"
    description="Aprenda a usar aplicaciones de Azure Mobile enviar notificaciones de inserción a la aplicación de Android."
    services="app-service\mobile"
    documentationCenter="android"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-android-app"></a>Agregar las notificaciones de inserción a la aplicación de Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Información general
En este tutorial, agregue las notificaciones de inserción al proyecto [Android inicio rápido] para que se envía una notificación de inserción al dispositivo cada vez que se inserta un registro.

Si no utiliza el proyecto de servidor de inicio rápido descargado, necesita el paquete de extensión de notificación de inserción. Para obtener más información, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Requisitos previos

Se necesita lo siguiente:

* Un IDE dependiendo de back-end de su proyecto:

    * [Android Studio](https://developer.android.com/sdk/index.html) si esta aplicación tiene Node.js back-end.

    * [Comunidad de Visual Studio 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) o posterior si esta aplicación tiene .net back-end.

* Android 2.3 o superior, revisión del repositorio de Google 27 o superior y servicios de Google Play 9.0.2 o superior para la mensajería de nube de Firebase.

* Completar el [Inicio rápido Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Crear un proyecto que admita Firebase nube mensajería

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurar un concentrador de notificación

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar Azure para enviar notificaciones de inserción

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Habilitar las notificaciones de inserción para el proyecto de servidor

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Agregar las notificaciones de inserción a la aplicación

En esta sección, actualice la aplicación de cliente Android para controlar las notificaciones de inserción.

### <a name="verify-android-sdk-version"></a>Comprobar la versión de SDK Android

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

El siguiente paso es instalar los servicios de Google Play. Mensajería de nube de Google tiene algunas API requisitos mínimos para desarrollo y pruebas, que debe cumplir la propiedad **minSdkVersion** en el manifiesto.

Si está probando con un dispositivo más antiguo, consulte [Establecer el Google Play servicios SDK] para determinar cómo bajo puede establecer este valor y configurarlo adecuadamente.

### <a name="add-google-play-services-to-the-project"></a>Agregar servicios de Google Play al proyecto

[AZURE.INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>Agregar código

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Probar la aplicación con el servicio móvil publicado

Puede probar la aplicación directamente asociar un teléfono Android con un cable USB, o mediante un dispositivo virtual en el emulador.

## <a name="more"></a>Más

<!-- URLs -->
[Inicio rápido Android]: app-service-mobile-android-get-started.md

[Configurar servicios de Google Play SDK]:https://developers.google.com/android/guides/setup
