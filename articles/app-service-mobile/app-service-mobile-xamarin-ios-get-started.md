<properties
    pageTitle="Introducción a Azure aplicación Servicio móvil aplicaciones para aplicaciones Xamarin.iOS | Microsoft Azure"
    description="Siga este tutorial para empezar con el uso de aplicaciones móviles para el desarrollo de Xamarin.iOS."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


#<a name="create-a-xamarinios-app"></a>Crear una aplicación de Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Información general

En este tutorial se muestra cómo agregar un servicio basado en la nube de back-end a una aplicación móvil de Xamarin.iOS mediante una aplicación móvil Azure de back-end.  Crear una aplicación móvil back-end y una sencilla _lista de tareas pendientes_ aplicación Xamarin.iOS que almacena los datos de aplicación en Azure.

Realizar este tutorial es un requisito previo para todos los otros tutoriales Xamarin.iOS sobre cómo usar la característica de aplicaciones móviles en servicio de la aplicación de Azure.

##<a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita los siguientes requisitos previos:

* Una cuenta de Azure active. Si no tiene una cuenta, suscribirse a una prueba de Azure y obtener hasta 10 aplicaciones móviles gratuitas que puede seguir usando incluso cuando su prueba termine. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio con Xamarin. Consulte [configurar e instalar para Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obtener instrucciones.

* Un Mac Xcode v7.0 o posterior y Xamarin Studio Comunidad instalado. Consulte [configurar e instalar para Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) y [configuración, instalar y las comprobaciones para los usuarios de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

>[AZURE.NOTE]Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse a una cuenta de Azure, vaya a [Probar el servicio de aplicación](https://tryappservice.azure.com/?appServiceName=mobile). Puede crear una aplicación móvil de corta duración starter inmediatamente en la aplicación de servicio: sin tarjeta de crédito obligatorio y sin compromisos.

## <a name="create-an-azure-mobile-app-backend"></a>Crear una aplicación móvil de Azure de back-end

Siga estos pasos para crear un aplicación móvil de back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurar el proyecto de servidor

Ahora haya proporcionado un back-end de aplicación de Azure Mobile que se puede utilizar las aplicaciones cliente móvil. A continuación, descargar un proyecto de servidor para una sencilla "lista de tareas pendientes" back-end y publíquela en Azure.

Siga los pasos siguientes para configurar el proyecto de servidor para usar la Node.js o .NET de back-end.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Descargar y ejecutar la aplicación Xamarin.iOS

1. Abra el [portal de Azure] en una ventana del explorador.

2. En el módulo de configuración de la aplicación móvil, haga clic en **Empezar a** > **Xamarin.iOS**. En el paso 3, haga clic en **crear una nueva aplicación** si aún no está seleccionada.  A continuación, haga clic en el botón **Descargar** .

    Se descargará una aplicación de cliente se conecta a su back-end móvil. Guarde el archivo de proyecto comprimida en su equipo local y tome nota de dónde lo guarda.

3. Extraer el proyecto que ha descargado y, a continuación, ábralo en Xamarin Studio (o Visual Studio).

    ![][9]

    ![][8]

4. Presione la tecla F5 para generar el proyecto e inicie la aplicación en el emulador iPhone.

5. En la aplicación, escriba el texto descriptivo, como _Obtener Xamarin_y, a continuación, haga clic en el **+** botón.

    ![][10]

    Datos de la solicitud se insertan en la tabla TodoItem. Elementos que se almacenan en la tabla se devuelven por la aplicación móvil de back-end y los datos se muestran en la lista.

>[AZURE.NOTE]Puede revisar el código de acceso a la aplicación móvil de back-end para consultar e insertar los datos en el archivo QSTodoService.cs C#.

##<a name="next-steps"></a>Pasos siguientes

* [Agregar la sincronización sin conexión a su aplicación](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Agregar autenticación para su aplicación](app-service-mobile-xamarin-ios-get-started-users.md)
* [Agregar las notificaciones de inserción a la aplicación de Xamarin.Android](app-service-mobile-xamarin-ios-get-started-push.md)
* [Cómo usar al cliente administrado para aplicaciones móviles de Azure](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com/
