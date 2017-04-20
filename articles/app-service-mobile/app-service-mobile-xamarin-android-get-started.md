<properties
    pageTitle="Introducción a Azure aplicaciones móviles para Xamarin.Android"
    description="Siga este tutorial para empezar a usar aplicaciones de Azure Mobile para el desarrollo de Xamarin Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha" />

#<a name="create-a-xamarinandroid-app"></a>Crear una aplicación de Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Información general

En este tutorial se muestra cómo agregar un servicio basado en la nube de back-end a una aplicación de Xamarin.Android. Para obtener más información, consulte [¿qué aplicaciones móviles](app-service-mobile-value-prop.md).

Una captura de pantalla de la aplicación completada está por debajo de:

![][0]

Realizar este tutorial es un requisito previo para todos los otros tutoriales de aplicaciones móviles para las aplicaciones de Xamarin.Android.

##<a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita los siguientes requisitos previos:

* Una cuenta de Azure active. Si no tiene una cuenta, registrarse para una prueba de Azure y obtener hasta 10 aplicaciones móviles gratuita. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio con Xamarin. Consulte [configurar e instalar para Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obtener instrucciones.

>[AZURE.NOTE]Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicación](https://tryappservice.azure.com/?appServiceName=mobile).  Puede crear una aplicación móvil de corta duración starter inmediatamente en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="create-an-azure-mobile-app-backend"></a>Crear una aplicación móvil de Azure de back-end

Siga estos pasos para crear un aplicación móvil de back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Ahora haya proporcionado un back-end de aplicación de Azure Mobile que se puede utilizar las aplicaciones cliente móvil. A continuación, descargar un proyecto de servidor para una sencilla "lista de tareas pendientes" back-end y publíquela en Azure.

## <a name="configure-the-server-project"></a>Configurar el proyecto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Descargar y ejecutar la aplicación Xamarin.Android

1. En **descargar y ejecuta el proyecto Xamarin.Android**, haga clic en el botón **Descargar** .

    Guarde el archivo de proyecto comprimida en su equipo local y tome nota de dónde lo guarda.

2. Presione la tecla **F5** para generar el proyecto e iniciar la aplicación.

3. En la aplicación, escriba el texto descriptivo, como _completado el tutorial_ y, a continuación, haga clic en el botón **Agregar** .

    ![][10]

    Datos de la solicitud se insertan en la tabla TodoItem. Elementos que se almacenan en la tabla se devuelven por la aplicación móvil de back-end y los datos aparecen en la lista.

    > [AZURE.NOTE] Puede revisar el código de acceso a la aplicación móvil de back-end para consultar e insertar datos, que se encuentran en el archivo ToDoActivity.cs C#.

##<a name="next-steps"></a>Pasos siguientes

* [Agregar la sincronización sin conexión a su aplicación](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [Agregar autenticación para su aplicación](app-service-mobile-xamarin-android-get-started-users.md)
* [Agregar las notificaciones de inserción a la aplicación de Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md)
* [Cómo usar al cliente administrado para aplicaciones móviles de Azure](app-service-mobile-dotnet-how-to-use-client-library.md)


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
