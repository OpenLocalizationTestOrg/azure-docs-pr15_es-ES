<properties
    pageTitle="Crear una aplicación de Android en aplicaciones móviles de servicio de aplicaciones de Azure | Microsoft Azure"
    description="Siga este tutorial para empezar con el uso de una aplicación móvil Azure back-ends para el desarrollo de Android"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

#<a name="create-an-android-app"></a>Crear una aplicación de Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Información general

En este tutorial se muestra cómo agregar un servicio basado en la nube de back-end a una aplicación móvil Android mediante una aplicación móvil Azure de back-end.  Va a crear una aplicación móvil back-end y una sencilla _lista de tareas pendientes_ aplicación Android que almacena los datos de aplicación en Azure.

Realizar este tutorial es un requisito previo para todos los otros tutoriales Android sobre el uso de la característica de aplicaciones móviles en servicio de la aplicación de Azure.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita lo siguiente:

* [Herramientas de desarrollador android](https://developer.android.com/sdk/index.html), que incluye el entorno de desarrollo integrado Studio Android y la plataforma de Android más reciente.
* Azure móvil Android SDK, que se hace referencia como parte del proyecto Tutorial rápido que descargar automáticamente.
* Una [cuenta de Azure active](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Crear una aplicación móvil Azure back-end

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurar el proyecto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-android-app"></a>Descargar y ejecutar la aplicación Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
