<properties
    pageTitle="Empezar a trabajar con aplicaciones de Azure Mobile compromiso para Windows Phone Silverlight"
    description="Aprenda a usar Azure Mobile compromiso con las notificaciones de inserción y analíticas para las aplicaciones de Windows Phone Silverlight."
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Empezar a trabajar con aplicaciones de Azure Mobile compromiso para Windows Phone Silverlight

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tema muestra cómo usar el compromiso de Azure Mobile comprender el uso de la aplicación y enviar notificaciones de inserción a los usuarios segmentados de una aplicación de Windows Phone Silverlight.
Este tutorial muestra el escenario de difusión simple con compromiso de móvil. En ella, crear una aplicación de Windows Phone Silverlight en blanco que recopila datos básico y recibe las notificaciones de inserción con el servicio de notificación de inserción de Microsoft (MPNS).

> [AZURE.NOTE] Si se dirige a Windows Phone 8.1 (no de Silverlight), consulte el [tutorial Universal de Windows](mobile-engagement-windows-store-dotnet-get-started.md).

Este tutorial requiere lo siguiente:

+ 2013 de Visual Studio
+ Paquete de Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Configurar compromiso móvil para su Windows Phone aplicación

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar la aplicación con el compromiso de móvil de back-end

Este tutorial presenta una "integración básica," que es el conjunto mínimo necesario para recopilar datos y enviar una notificación de inserción. La documentación de integración completa puede encontrarse en la [integración de Windows Phone SDK de Mobile compromiso](mobile-engagement-windows-phone-sdk-overview.md)

Se crea una aplicación básica con Visual Studio para demostrar la integración.

###<a name="create-a-new-windows-phone-silverlight-project"></a>Crear un nuevo proyecto de Silverlight de Windows Phone

Los siguientes pasos suponen el uso de Visual Studio de 2015, aunque los pasos son similares en versiones anteriores de Visual Studio. 

1. Inicie Visual Studio y, en la pantalla **principal** , seleccione **Nuevo proyecto**.

2. En el menú emergente, seleccione **Windows 8** -> **Windows Phone** -> **Aplicación en blanco (Windows Phone Silverlight)**. Rellene el **nombre** de la aplicación y el **nombre de la solución**y, a continuación, haga clic en **Aceptar**.

    ![][1]

3. Puede elegir como destino **Windows Phone 8.0** o **Windows Phone 8.1**.

Acaba de crear una nueva aplicación de Windows Phone Silverlight en el que se integrará el SDK de compromiso de Azure Mobile.

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Conectar la aplicación con el compromiso de móvil de back-end

1. Instalar el paquete de nuget [MicrosoftAzure.MobileEngagement] en su proyecto.

2. Abrir `WMAppManifest.xml` (en la carpeta Propiedades) y asegúrese de que los siguientes declarados (agregarlas si no se encuentra) en el `<Capabilities />` etiqueta:

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Ahora, pegue la cadena de conexión que ha copiado de una versión anterior de la aplicación móvil compromiso y pegarlo en la `Resources\EngagementConfiguration.xml` de archivos, entre los `<connectionString>` y `</connectionString>` etiquetas:

    ![][3]

4. En la `App.xaml.cs` archivo:

    una. Agregar la `using` instrucción:

            using Microsoft.Azure.Engagement;

    b. Inicializar el SDK en el `Application_Launching` método:

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c. Inserte el siguiente en la `Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

##<a id="monitor"></a>Habilitar la supervisión en tiempo real

Para iniciar el envío de datos y garantizar que los usuarios están activos, debe enviar al menos una pantalla (actividad) del back-end de compromiso de móvil.

1. En la MainPage.xaml.cs, agregue el `using` instrucción:

        using Microsoft.Azure.Engagement;

2. Reemplace la clase base de **MainPage**, que es antes de **PhoneApplicationPage**, con **EngagementPage**.

        class MainPage : EngagementPage 
    
3. En el `MainPage.xml` archivo:

    una. Agregar a las declaraciones de espacios de nombres:

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b. Reemplazar `phone:PhoneApplicationPage` en el nombre de etiqueta XML con `engagement:EngagementPage`.

##<a id="monitor"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar las notificaciones de inserción y en la aplicación de mensajería

Compromiso de Mobile permite interactuar y llegar a los usuarios con las notificaciones de inserción y la mensajería de la aplicación en el contexto de las campañas. Este módulo se denomina alcance en el portal de compromiso de móvil.
Las siguientes secciones configuradas su aplicación para recibirlas.

###<a name="enable-your-app-to-receive-mpns-push-notifications"></a>Habilitar la aplicación recibir notificaciones de inserción de MPNS

Agregar nuevas capacidades a su `WMAppManifest.xml` archivo:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###<a name="initialize-the-reach-sdk"></a>Inicializar el SDK de alcance

1. En `App.xaml.cs`, llame a `EngagementReach.Instance.Init();` de la función **Application_Launching** , justo después de la inicialización del agente:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. En `App.xaml.cs`, llame a `EngagementReach.Instance.OnActivated(e);` de la función **Application_Activated** , justo después de la inicialización del agente:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Todo está listo. Ahora se verificará que han correctamente Bajaron por la chimenea fuera esta integración básica.

##<a id="send"></a>Enviar una notificación a la aplicación

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Ahora debe ver una notificación en el dispositivo que se mostrará como una notificación de la aplicación si la aplicación se abre en caso contrario, como una notificación como la siguiente: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
