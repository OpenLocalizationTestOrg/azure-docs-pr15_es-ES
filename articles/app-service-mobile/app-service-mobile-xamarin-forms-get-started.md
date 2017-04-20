<properties
    pageTitle="Introducción a aplicaciones Mobile utilizando Xamarin.Forms"
    description="Siga este tutorial para empezar a usar aplicaciones de Azure Mobile para el desarrollo de Xamarin.Forms"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-xamarinforms-app"></a>Crear una aplicación de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Información general

En este tutorial se muestra cómo agregar un servicio basado en la nube de back-end a una aplicación móvil Xamarin.Forms mediante una aplicación móvil de Azure de back-end. Creará aplicación Mobile back-end y una sencilla _lista de tareas pendientes_ aplicación Xamarin.Forms que almacena los datos de aplicación en Azure.

Realizar este tutorial es un requisito previo para todos los otros tutoriales de aplicaciones móviles para Xamarin.Forms.

##<a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita lo siguiente:

* Una cuenta de Azure active. Si no tiene una cuenta, puede registrarse para una prueba de Azure y obtener hasta 10 gratuitos aplicaciones móviles que puede seguir usando incluso cuando su prueba termine. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio con Xamarin. Consulte [configurar e instalar para Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obtener instrucciones. 

* Un Mac Xcode v7.0 o posterior y Xamarin Studio Comunidad instalado. Consulte [configurar e instalar para Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) y [configuración, instalar y las comprobaciones para los usuarios de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).
 
>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](https://tryappservice.azure.com/?appServiceName=mobile), donde puede crear inmediatamente una aplicación móvil de corta duración starter en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="create-a-new-azure-mobile-app-backend"></a>Crear la aplicación móvil de Azure back-end

Siga estos pasos para crear la aplicación móvil back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Ahora haya proporcionado un back-end de aplicación de Azure Mobile que se puede utilizar las aplicaciones cliente móvil. A continuación, se descargará un proyecto de servidor para una sencilla "lista de tareas pendientes" back-end y publíquela en Azure.

## <a name="configure-the-server-project"></a>Configurar el proyecto de servidor

Siga los pasos siguientes para configurar el proyecto de servidor para usar la Node.js o .NET de back-end.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-xamarinforms-solution"></a>Descargar y ejecutar la solución Xamarin.Forms

Aquí tiene un par de opciones. Puede descargar la solución en un equipo Mac y abrirlo en Xamarin Studio, o puede descargar la solución en un equipo Windows y abrirlo en Visual Studio mediante un Mac en red para la creación de la aplicación. Si necesita instrucciones más detalladas sobre los escenarios de configuración de Xamarin, consulte [configurar e instalar para Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .

Comencemos:

 1. En el equipo Mac o en el equipo de Windows, abra el [Portal de Azure] en una ventana del explorador.
 2. En el módulo de configuración de la aplicación móvil, haga clic en **Empezar** (en móvil) > **Xamarin.Forms**. En el paso 3, haga clic en **crear una nueva aplicación** si aún no está seleccionada.  A continuación, haga clic en el botón **Descargar** .

    Esto descarga un proyecto que contiene una aplicación de cliente que está conectada a su aplicación móvil. Guarde el archivo de proyecto comprimida en su equipo local y tome nota de dónde lo guarda.

 3. Extraer el proyecto que ha descargado y, a continuación, ábralo en Xamarin Studio o Visual Studio.

    ![][9]

    ![][8]


##<a name="optional-run-the-ios-project"></a>(Opcional) Ejecute el proyecto de iOS

Esta sección es para ejecutar el proyecto de iOS Xamarin para dispositivos iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

####<a name="in-xamarin-studio"></a>En Xamarin Studio

1. Haga clic en el proyecto de iOS y, a continuación, haga clic en **Establecer como proyecto de inicio**.
2. En el menú **Ejecutar** , haga clic en **Iniciar depuración** para generar el proyecto e inicie la aplicación en el emulador iPhone.

####<a name="in-visual-studio"></a>En Visual Studio
1. Haga clic en el proyecto de iOS y, a continuación, haga clic en **establecer como proyecto de inicio**.
2. En el menú **Generar** , haga clic en **Administrador de configuración**.
3. En el cuadro de diálogo **Administrador de configuración** , seleccione las casillas de verificación **Generar** e **implementar** del proyecto iOS.
4. Presione la tecla **F5** para generar el proyecto e inicie la aplicación en el emulador iPhone.

    >[AZURE.NOTE] Si tiene problemas para crear, ejecutar NuGet Administrador de paquetes y actualizar a la versión más reciente de la Xamarin admiten paquetes. A veces los proyectos tutorial rápido podrán retardo en actualizarse a la más reciente.    

En la aplicación, escriba el texto descriptivo, como _Obtener Xamarin_ y, a continuación, haga clic en el **+** botón.

![][10]

Envía una petición POST al servidor nuevo de la aplicación móvil hospedado en Azure. Datos de la solicitud se insertan en la tabla TodoItem. Elementos que se almacenan en la tabla se devuelven por la aplicación móvil de back-end y los datos se muestran en la lista.

>[AZURE.NOTE]
> Encontrará el código de acceso a la aplicación móvil de back-end en el archivo TodoItemManager.cs C# del proyecto de biblioteca de clase portátil de su solución.

##<a name="optional-run-the-android-project"></a>(Opcional) Ejecute el proyecto Android

Esta sección es para ejecutar el proyecto de droid Xamarin para Android. Puede omitir esta sección si no está trabajando con dispositivos Android.

####<a name="in-xamarin-studio"></a>En Xamarin Studio

1. Haga clic en el proyecto Android y, a continuación, haga clic en **Establecer como proyecto de inicio**.
2. En el menú **Ejecutar** , haga clic en **Iniciar depuración** para generar el proyecto e inicie la aplicación en un emulador Android.

####<a name="in-visual-studio"></a>En Visual Studio
1. Haga clic en el proyecto Android (Droid) y, a continuación, haga clic en **establecer como proyecto de inicio**.
4. En el menú **Generar** , haga clic en **Administrador de configuración**.
5. En el cuadro de diálogo **Administrador de configuración** , seleccione las casillas de verificación **Generar** e **implementar** del proyecto Android.
6. Presione la tecla **F5** para generar el proyecto e inicie la aplicación en un emulador Android.

    >[AZURE.NOTE] Si tiene problemas para crear, ejecutar NuGet Administrador de paquetes y actualizar a la versión más reciente de la Xamarin admiten paquetes. A veces los proyectos tutorial rápido podrán retardo en actualizarse a la más reciente.    


En la aplicación, escriba el texto descriptivo, como _Obtener Xamarin_ y, a continuación, haga clic en el **+** botón.

![][11]

Envía una petición POST al servidor nuevo de la aplicación móvil hospedado en Azure. Datos de la solicitud se insertan en la tabla TodoItem. Elementos que se almacenan en la tabla se devuelven por la aplicación móvil de back-end y los datos se muestran en la lista.

> [AZURE.NOTE]
> Encontrará el código de acceso a la aplicación móvil de back-end en el archivo TodoItemManager.cs C# del proyecto de biblioteca de clase portátil de su solución.


##<a name="optional-run-the-windows-project"></a>(Opcional) Ejecute el proyecto de Windows


Esta sección es para ejecutar el proyecto Xamarin WinApp para dispositivos de Windows. Puede omitir esta sección si no está trabajando con los dispositivos de Windows.


####<a name="in-visual-studio"></a>En Visual Studio
1. Haga clic en cualquiera de los proyectos de Windows y, a continuación, haga clic en **establecer como proyecto de inicio**.
4. En el menú **Generar** , haga clic en **Administrador de configuración**.
5. En el cuadro de diálogo **Administrador de configuración** , seleccione las casillas de verificación **Generar** e **implementar** del proyecto de Windows que haya elegido.
6. Presione la tecla **F5** para generar el proyecto e inicie la aplicación en un emulador de Windows.

    >[AZURE.NOTE] Si tiene problemas para crear, ejecutar NuGet Administrador de paquetes y actualizar a la versión más reciente de la Xamarin admiten paquetes. A veces los proyectos tutorial rápido podrán retardo en actualizarse a la más reciente.    


En la aplicación, escriba el texto descriptivo, como _Obtener Xamarin_ y, a continuación, haga clic en el **+** botón.

Envía una petición POST al servidor nuevo de la aplicación móvil hospedado en Azure. Datos de la solicitud se insertan en la tabla TodoItem. Elementos que se almacenan en la tabla se devuelven por la aplicación móvil de back-end y los datos se muestran en la lista.

![][12]

> [AZURE.NOTE]
> Encontrará el código de acceso a la aplicación móvil de back-end en el archivo TodoItemManager.cs C# del proyecto de biblioteca de clase portátil de su solución.

##<a name="next-steps"></a>Pasos siguientes

* [Agregar autenticación para su aplicación](app-service-mobile-xamarin-forms-get-started-users.md)  
Aprenda a autenticar a los usuarios de la aplicación con un proveedor de identidades.

* [Agregar las notificaciones de inserción a la aplicación](app-service-mobile-xamarin-forms-get-started-push.md)  
Obtenga información sobre cómo agregar inserción notificaciones de soporte técnico a su aplicación y configurar la aplicación móvil de back-end para utilizar Hubs de notificación de Azure para enviar notificaciones de inserción.

* [Habilitar la sincronización sin conexión para la aplicación](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Obtenga información sobre cómo agregar la aplicación con una aplicación móvil de back-end de soporte técnico sin conexión. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no hay ninguna conexión de red.

* [Cómo usar al cliente administrado para aplicaciones móviles de Azure](app-service-mobile-dotnet-how-to-use-client-library.md)  
Obtenga información sobre cómo trabajar con el cliente administrado SDK en su aplicación Xamarin. 


<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal de Azure]: https://portal.azure.com/

