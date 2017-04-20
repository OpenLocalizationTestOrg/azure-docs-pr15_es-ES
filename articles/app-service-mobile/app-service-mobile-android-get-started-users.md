<properties
    pageTitle="Agregar autenticación en Android con aplicaciones móviles | Servicio de aplicaciones de Azure"
    description="Aprenda a usar aplicaciones móviles en servicio de la aplicación de Azure para autenticar a los usuarios de la aplicación a través de una gran variedad de proveedores de identidades, incluyendo Google, Facebook, Twitter y Microsoft Android."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-android-app"></a>Agregar autenticación para su aplicación Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumen

En este tutorial, agregue autenticación al proyecto todolist tutorial sobre el uso de un proveedor de identidades compatible Android. En este tutorial se basa en el tutorial de [Introducción a las aplicaciones móviles] , debe completar primero.

##<a name="register"></a>Registrar la aplicación para la autenticación y configurar el servicio de aplicación

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permisos a los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ En Android Studio, abra el proyecto ha finalizado proyectado con el tutorial de [Introducción a las aplicaciones móviles]. En el menú **Ejecutar** haga clic en **ejecutar la aplicación** y compruebe que se produce una excepción no controlada con un código de estado de 401 (no autorizado) cuando se inicie la aplicación.

     Esta excepción ocurre porque la aplicación intenta obtener acceso a la copia back-end como un usuario no autenticado, pero la tabla _TodoItem_ ahora requiere autenticación.

A continuación, actualice la aplicación para autenticar usuarios antes de solicitar recursos de la aplicación móvil de back-end.

## <a name="add-authentication-to-the-app"></a>Agregar autenticación a la aplicación

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Símbolos de autenticación de caché en el cliente

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha realizado este tutorial autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

+ [Notificaciones de inserción de agregar a su aplicación Android](app-service-mobile-android-get-started-push.md) Obtenga información sobre cómo configurar la aplicación móvil de back-end para utilizar Hubs de notificación de Azure para enviar notificaciones de inserción.

+ [Habilitar la sincronización sin conexión para su aplicación Android](app-service-mobile-android-get-started-offline-data.md) Obtenga información sobre cómo agregar la aplicación con un aplicación móvil de back-end de soporte técnico sin conexión. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no hay ninguna conexión de red.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Empezar a trabajar con aplicaciones móviles]: app-service-mobile-android-get-started.md
