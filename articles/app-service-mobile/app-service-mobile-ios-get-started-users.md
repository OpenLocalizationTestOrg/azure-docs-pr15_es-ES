<properties
    pageTitle="Agregar autenticación en iOS con aplicaciones móviles de Azure"
    description="Aprenda a usar aplicaciones de Azure Mobile para autenticar a los usuarios de la aplicación iOS a través de una gran variedad de proveedores de identidades, incluidos AAD, Google, Facebook, Twitter y Microsoft."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-ios-app"></a>Agregar autenticación para su aplicación iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

En este tutorial, agregue autenticación al proyecto [iOS rápidos empezar] con un proveedor de identidades compatible. En este tutorial se basa en el tutorial de [Inicio rápido de iOS] , primero.

##<a name="register"></a>Registrar la aplicación para la autenticación y configurar el servicio de aplicación

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permisos a los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

En Xcode, presione **Ejecutar** para iniciar la aplicación. Se produce una excepción porque la aplicación intenta obtener acceso a la copia back-end como un usuario no autenticado, pero _TodoItem_ tabla ahora requiere autenticación.

##<a name="add-authentication"></a>Agregar autenticación de aplicación

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[Inicio rápido de iOS]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
