<properties
    pageTitle="Introducción a Azure Mobile compromiso de implementación de unidad iOS"
    description="Aprenda a usar Azure Mobile compromiso con análisis y notificaciones Push para las aplicaciones de unidad implementar para dispositivos iOS."
    services="mobile-engagement"
    documentationCenter="unity"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-unity-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Introducción a Azure Mobile compromiso de implementación de unidad iOS

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tema muestra cómo usar el compromiso de Azure Mobile para comprender el uso de la aplicación y cómo enviar notificaciones de inserción para segmenta los usuarios de una aplicación de unidad cuando se implementa en un dispositivo iOS.
Este tutorial utiliza la clásica unidad dar un tutorial bola como punto de partida. Debe seguir los pasos de este [tutorial](mobile-engagement-unity-roll-a-ball.md) antes de continuar con la integración de contratación Mobile que se muestran en el tutorial siguiente. 

Este tutorial requiere lo siguiente:

+ [Editor de unidad](http://unity3d.com/get-unity)
+ [Unidad de contratación móvil SDK](https://aka.ms/azmeunitysdk)
+ Editor de XCode

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).

##<a id="setup-azme"></a>Compromiso de Mobile de configuración de la aplicación de iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar la aplicación con el compromiso de móvil de back-end

###<a name="import-the-unity-package"></a>Importar el paquete de unidad

1. Descargar el [paquete de unidad de contratación Mobile](https://aka.ms/azmeunitysdk) y guardarlo en el equipo local. 

2. Vaya a **activos -> Importar paquete -> paquete personalizado** y seleccione el paquete que ha descargado en el paso anterior. 

    ![][70] 

3. Asegúrese de que todos los archivos están seleccionados y haga clic en el botón **Importar** . 

    ![][71] 

4. Una vez que la importación es correcta, verá los archivos SDK importados en el proyecto.  

    ![][72] 

###<a name="update-the-engagementconfiguration"></a>Actualizar la EngagementConfiguration

1. Abra el archivo de comandos de **EngagementConfiguration** desde la carpeta SDK y actualizar la **IOS\_conexión\_cadena** con la cadena de conexión obtenida previamente desde el portal de Azure.  

    ![][73]

2. Guarde el archivo. 

###<a name="configure-the-app-for-basic-tracking"></a>Configurar la aplicación para seguimiento básico

1. Abrir la secuencia de comandos de **PlayerController** adjuntado al objeto Reproductor para su edición. 

2. Agregue la siguiente instrucción using:

        using Microsoft.Azure.Engagement.Unity;

3. Agregue lo siguiente a la `Start()` método
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###<a name="deploy-and-run-the-app"></a>Implementar y ejecute la aplicación

1. Conectar un dispositivo iOS con su equipo. 

2. Abrir el **archivo -> configuración de generación** 

    ![][40]

3. Seleccione **iOS** y, a continuación, haga clic en **Cambiar plataforma**

    ![][41]

    ![][42]

4. Haga clic en **Configuración del Reproductor** y proporcionar un identificador de paquete válido. 

    ![][53]

5. Por último, haga clic en **Crear y ejecutar**

    ![][54]

6. Se le pedirá que proporcione un nombre de carpeta para almacenar el paquete de iOS. 

    ![][43]

7. Si todo va bien, el proyecto que se va a compilar y debe abrir hacia arriba en la aplicación XCode. 

8. Asegúrese de que el **identificador de empaquetar** es correcta en el proyecto.  

    ![][75]

10. Ejecutar ahora la aplicación en XCode para que el paquete se implementa en su dispositivo conectado y verá su juego de unidad en su teléfono! 

##<a id="monitor"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar las notificaciones de inserción y en la aplicación de mensajería

Compromiso de móvil le permite interactuar con los usuarios y póngase en contacto con las notificaciones de inserción y de la aplicación en el contexto de las campañas de mensajería. Este módulo se denomina alcance en el portal de compromiso de móvil.
No tiene que realizar ninguna configuración adicional en su aplicación para recibir notificaciones y ya está configurado para el mismo.

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
