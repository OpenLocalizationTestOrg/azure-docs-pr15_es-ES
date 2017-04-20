<properties
    pageTitle="Crear una aplicación de Cordova en aplicaciones móviles de servicio de aplicaciones de Azure | Microsoft Azure"
    description="Siga este tutorial para empezar con el uso de una aplicación móvil Azure back-ends para el desarrollo de Apache Cordova"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova, javascript, móvil, cliente" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-an-apache-cordova-app"></a>Crear un Apache Cordova aplicación

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Información general

En este tutorial se muestra cómo agregar un servicio basado en la nube de back-end a una aplicación móvil de Apache Cordova mediante una aplicación móvil Azure de back-end.  Va a crear una aplicación móvil back-end y una sencilla _lista de tareas pendientes_ aplicación Apache Cordova que almacena los datos de aplicación en Azure.

Realizar este tutorial es un requisito previo para todos los otros tutoriales Apache Cordova sobre cómo usar la característica de aplicaciones móviles en servicio de la aplicación de Azure.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita lo siguiente:

* Un equipo con la [Comunidad de Visual Studio 2015] o una versión posterior.
* [Visual Studio Tools para Cordova Apache].
* Una [cuenta de Azure active](https://azure.microsoft.com/pricing/free-trial/).

También puede omitir Visual Studio y utilizar la línea de comandos de Apache Cordova directamente.  Esto es útil cuando se complete el tutorial en un equipo Mac.  Compilación de aplicaciones de cliente de Apache Cordova mediante la línea de comandos no está cubierto por este tutorial.

## <a name="create-a-new-azure-mobile-app-backend"></a>Crear una aplicación móvil Azure back-end

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Vea un vídeo que muestra los pasos similares](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Configurar el proyecto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Descargar y ejecutar la aplicación Cordova Apache

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha realizado este tutorial de inicio rápido, mover a uno de los siguientes tutoriales:

* [Agregar autenticación] para su Apache Cordova aplicación.
* [Agregar las notificaciones de inserción] a su Apache Cordova aplicación.

Más información sobre conceptos clave con el servicio de aplicación de Azure.

* [Autenticación]
* [Notificaciones de inserción]

Obtenga información sobre cómo usar el SDK.

* [Apache Cordova SDK]
* [SDK de servidor de ASP.NET]
* [Node.js Server SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Comunidad de Visual Studio de 2015]: http://www.visualstudio.com/
[Visual Studio Tools para Cordova Apache]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Agregar autenticación]: app-service-mobile-cordova-get-started-users.md
[Agregar las notificaciones de inserción]: app-service-mobile-cordova-get-started-push.md
[Autenticación]: app-service-mobile-auth.md
[Notificaciones de inserción]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK de servidor de ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
