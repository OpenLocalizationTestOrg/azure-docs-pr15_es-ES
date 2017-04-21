<properties
    pageTitle="Introducción a Azure compromiso móvil para Xamarin.Android"
    description="Aprenda a usar Azure Mobile compromiso con análisis y notificaciones Push para aplicaciones Xamarin.Android."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/16/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Introducción a Azure compromiso móvil para las aplicaciones de Xamarin.Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo usar Azure Mobile compromiso para comprender el uso de la aplicación y cómo enviar notificaciones de inserción a los usuarios segmentados de una aplicación de Xamarin.Android.
Este tutorial muestra el escenario de difusión simple con compromiso de móvil. En ella, crear una aplicación de Xamarin.Android en blanco que recopila datos básico y recibe las notificaciones de inserción con mensajería de nube de Google (GCM).

Este tutorial requiere lo siguiente:

+ [Xamarin Studio](http://xamarin.com/studio). También puede usar Visual Studio con Xamarin, pero este tutorial utiliza Xamarin Studio. Para obtener instrucciones de instalación, consulte [configurar e instalar para Visual Studio y Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ [Compromiso móvil Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).

##<a id="setup-azme"></a>Compromiso de móvil de configuración de la aplicación Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar la aplicación con el compromiso de móvil de back-end

Este tutorial presenta una "integración básica," que es el conjunto mínimo necesario para recopilar datos y enviar una notificación de inserción. 

Se crea una aplicación básica con Xamarin Studio para demostrar la integración.

###<a name="create-a-new-xamarinandroid-project"></a>Crear un nuevo proyecto de Xamarin.Android

1. Inicio **Xamarin Studio** vaya a **archivo** -> **nueva** -> **solución** 

    ![][1]

2. Seleccione la **Aplicación Android** , a continuación, asegúrese de que el idioma seleccionado es **C#** y haga clic en **siguiente**.

    ![][2]

3. Escriba el **Nombre de la aplicación** y el **identificador de la organización**. Asegúrese de que la marca de verificación **Reproducir servicios de Google** y, a continuación, haga clic en **siguiente**. 

    ![][3]
    
4. Actualizar el **Nombre del proyecto**, el **Nombre de la solución** y la **ubicación** si es necesario y haga clic en **crear**.

    ![][4]
 
Xamarin Studio creará la aplicación en el que se integrará compromiso de móvil. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar la aplicación a Mobile compromiso back-end

1. Haga clic con el botón secundario del mouse en la carpeta de **paquetes** de soluciones y seleccione **Agregar paquetes**

    ![][5]

2. Buscar el **SDK de Microsoft Azure Mobile compromiso Xamarin** y agregar a la solución.  

    ![][6]
   
3. Abra **MainActivity.cs** y agregue las siguientes instrucciones:

        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;

4. En la `OnCreate` método, agregue lo siguiente para inicializar la conexión con Mobile compromiso back-end. Asegúrese de agregar la **cadena de conexión**. 

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

###<a name="add-permissions-and-a-service-declaration"></a>Agregar permisos y una declaración de servicio

1. Abra el archivo **Manifest.xml** dentro de la carpeta de propiedades. Seleccione la ficha origen para que actualizar directamente el origen XML.
 
2. Agregar estos permisos al Manifest.xml (que se encuentra en la carpeta **Propiedades** ) de su proyecto inmediatamente antes o después de la `<application>` etiqueta:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. Agregue las siguientes entre la `<application>` y `</application>` etiquetas para declarar el servicio de agente:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

4. En el código que acaba de pegar, reemplace `"<Your application name>"` en la etiqueta. Esto se muestra en el menú **configuración** , donde los usuarios pueden ver los servicios que se ejecutan en el dispositivo. Puede agregar la palabra "Servicio" de la etiqueta por ejemplo.

###<a name="send-a-screen-to-mobile-engagement"></a>Enviar una pantalla a compromiso de móvil

Para iniciar el envío de datos y asegurarse de que los usuarios están activos, debe enviar al menos una pantalla a la Mobile compromiso de back-end. Para hacer esto: asegúrese de que el `MainActivity` hereda de `EngagementActivity` en lugar de `Activity`.

    public class MainActivity : EngagementActivity
    
Como alternativa, si no se heredan de `EngagementActivity` , a continuación, debe agregar `.StartActivity` y `.EndActivity` métodos en `OnResume` y `OnPause` respectivamente.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }
    
            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

##<a id="monitor"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar las notificaciones de inserción y en la aplicación de mensajería

Compromiso de móvil le permite interactuar con y llegar a los usuarios con las notificaciones de inserción y de la aplicación en el contexto de las campañas de mensajería. Este módulo se denomina alcance en el portal de compromiso de móvil.
Las secciones siguientes se configura su aplicación para recibirlas.

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
