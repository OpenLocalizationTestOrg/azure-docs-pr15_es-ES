<properties
    pageTitle="Introducción a Android aplicaciones Azure Mobile compromiso"
    description="Aprenda a usar Azure Mobile compromiso con las notificaciones de inserción y analíticas Android aplicaciones para."
    services="mobile-engagement"
    documentationCenter="android"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Introducción a Azure Mobile compromiso para aplicaciones de Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo usar Azure Mobile compromiso para comprender el uso de la aplicación y cómo enviar notificaciones de inserción a usuarios segmentados de una aplicación de Android.
Este tutorial muestra el escenario de difusión simple con compromiso de móvil. En ella, crear una aplicación de Android en blanco que recopila datos básico y recibe las notificaciones de inserción con mensajería de nube de Google (GCM).

## <a name="prerequisites"></a>Requisitos previos

Completar este tutorial, requiere [Android herramientas de desarrollador](https://developer.android.com/sdk/index.html), que incluye el entorno de desarrollo integrado Studio Android y la plataforma de Android más reciente.

También se requiere el [SDK de Mobile compromiso Android](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] Para completar este tutorial, necesita una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Configurar el compromiso de móvil para su aplicación Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Conectar la aplicación con el compromiso de móvil de back-end

Este tutorial presenta una "integración básica," que es el conjunto mínimo necesario para recopilar datos y enviar una notificación de inserción. Crear una aplicación básica con Android Studio para demostrar la integración.

La documentación de integración completa puede encontrarse en la [integración de Mobile compromiso Android SDK](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Crear un proyecto de Android

1. Inicie **Studio Android**y, en el menú emergente, seleccione **iniciar un nuevo proyecto Studio Android**.

    ![][1]

2. Proporcionar un dominio de aplicación de nombre y empresa. Tome nota de lo que rellena, ya que necesitará más adelante. Haga clic en **siguiente**.

    ![][2]

3. Seleccione el nivel de API y el formato de destino y haga clic en **siguiente**.

    >[AZURE.NOTE] Compromiso de Mobile requiere nivel de API 10 mínimo (Android 2.3.3).

    ![][3]

4. Seleccione la **Actividad en blanco** , que es la pantalla sola para esta aplicación y haga clic en **siguiente**.

    ![][4]

5. Por último, deje los valores predeterminados como está y haga clic en **Finalizar**.

    ![][5]

Android Studio crea ahora la aplicación de demostración en la que se integre compromiso de móvil.

### <a name="include-the-sdk-library-in-your-project"></a>Incluir la biblioteca SDK en el proyecto

1. Descargar el [SDK de contratación móviles Android](https://aka.ms/vq9mfn).
2. Extraer el archivo a una carpeta en el equipo.
3. Identificar la biblioteca .jar para la versión actual de este SDK y copiar al Portapapeles.

      ![][6]

4. Vaya a la sección de **proyecto** (1) y pegar la .jar en la carpeta de bibliotecas (2).

      ![][7]

5. Para cargar la biblioteca, sincronice el proyecto.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Conectar la aplicación a back-end de contratación Mobile con la cadena de conexión

1. Copie las siguientes líneas de código en la creación de la actividad (hecho sólo en un lugar de la aplicación, normalmente la actividad principal). Para esta aplicación de ejemplo, abra la actividad principal en src -> main -> carpeta de java y agregue lo siguiente:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Resolver las referencias al presionar Alt + ENTRAR o agregando las siguientes instrucciones de importación:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. Volver al Portal clásico de Azure en la página de **Información de conexión** de la aplicación y copie la **Cadena de conexión**.

      ![][9]

4. Pegar en el `setConnectionString` parámetro, reemplazar toda la cadena se muestra en el siguiente código:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Agregar permisos y una declaración de servicio

1. Agregar estos permisos para el Manifest.xml del proyecto inmediatamente antes o después de la `<application>` etiqueta:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Para declarar el servicio de agente, agregue este código entre el `<application>` y `</application>` etiquetas:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. En el código que pegó, reemplace `"<Your application name>"` en la etiqueta que se muestra en el menú **configuración** , donde puede ver los servicios que se ejecutan en el dispositivo. Puede agregar la palabra "Servicio" de la etiqueta por ejemplo.

### <a name="send-a-screen-to-mobile-engagement"></a>Enviar una pantalla a compromiso de móvil

Para iniciar el envío de datos y asegúrese de que los usuarios están activos, debe enviar al menos una pantalla (actividad) del back-end de compromiso de móvil.

Vaya a **MainActivity.java** y agregue lo siguiente para reemplazar la clase base de **actividad principal** a **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] Si la clase base no es una *actividad*, consulte [Avanzada Reporting Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes) cómo heredar de clases diferentes.


Comentario la línea siguiente para este escenario de ejemplo sencillo:

    // setSupportActionBar(toolbar);

Si desea conservar la `ActionBar` en la aplicación, vea [Avanzada Reporting Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes).

## <a name="connect-app-with-real-time-monitoring"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Habilitar las notificaciones de inserción y en la aplicación de mensajería

Durante una campaña compromiso móvil le permite interactuar con y llegar a los usuarios con las notificaciones de inserción y la mensajería de la aplicación. Este módulo se denomina alcance en el portal de compromiso de móvil.
La siguiente sección configura su aplicación para recibirlas.

### <a name="copy-sdk-resources-in-your-project"></a>Copiar SDK recursos del proyecto

1. Vaya a su contenido de la descarga SDK y copie la carpeta de **recursos** .

    ![][10]

2. Vaya a Android Studio, seleccione el directorio **principal** de los archivos del proyecto y péguelo para agregar los recursos al proyecto.

    ![][11]

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Pasos siguientes

Vaya a [Android SDK](mobile-engagement-android-sdk-overview.md) para obtener información detallada acerca de la integración de SDK.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
