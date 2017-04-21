<properties
    pageTitle="Introducción a Azure notificación Hubs para aplicaciones Kindle | Microsoft Azure"
    description="En este tutorial, aprenderá a usar Hubs de notificación de Azure para enviar notificaciones de inserción a una aplicación de Kindle."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-kindle"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Introducción a Hubs de notificación para aplicaciones Kindle

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Información general

En este tutorial se muestra cómo usar Hubs de notificación de Azure para enviar notificaciones de inserción a una aplicación de Kindle.
Debe crear una aplicación de Kindle en blanco que recibe las notificaciones de inserción mediante mensajería de dispositivo Amazon (ADM).

##<a name="prerequisites"></a>Requisitos previos

Este tutorial requiere lo siguiente:

+ Obtener el SDK Android (suponemos que va a utilizar Eclipse) desde el <a href="http://go.microsoft.com/fwlink/?LinkId=389797">sitio Android</a>.
+ Siga los pasos de <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Configuración del entorno de desarrollo</a> para configurar su entorno de desarrollo para Kindle.

##<a name="add-a-new-app-to-the-developer-portal"></a>Agregar una nueva aplicación en el portal del programador

1. Primero, cree una aplicación en el [portal de programadores de Amazon].

    ![][0]

2. Copie la **tecla de aplicación**.

    ![][1]

3. En el portal, haga clic en el nombre de la aplicación y, a continuación, haga clic en la pestaña **Mensajería de dispositivo** .

    ![][2]

4. Haga clic en **crear un nuevo perfil de seguridad**y, a continuación, cree un nuevo perfil de seguridad (por ejemplo, el **perfil de seguridad de TestAdm**). A continuación, haga clic en **Guardar**.

    ![][3]

5. Haga clic en **Perfiles de seguridad** para ver el perfil de seguridad que acaba de crear. Copie los valores de **Identificador de cliente** y el **Secreto de cliente** para su uso posterior.

    ![][4]

## <a name="create-an-api-key"></a>Crear una clave API

1. Abra un símbolo del sistema con privilegios de administrador.
2. Vaya a la carpeta SDK Android.
3. Escriba el siguiente comando:

        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

    ![][5]

4.  Para el **almacén de claves de** la contraseña, escriba **android**.

5.  Copie la huella digital **MD5** .
6.  En el portal del programador, en la pestaña **mensajería** , haga clic en **Android/Kindle** y escriba el nombre del paquete de la aplicación (por ejemplo, **com.sample.notificationhubtest**) y el valor de **MD5** y, a continuación, haga clic en **Generar clave de API**.

## <a name="add-credentials-to-the-hub"></a>Agregar credenciales al concentrador

En el portal, agregar el secreto de cliente y el identificador de cliente en la ficha **Configurar** de su centro de notificación.

## <a name="set-up-your-application"></a>Configurar la aplicación

> [AZURE.NOTE] Cuando está creando una aplicación, use al menos 17 de nivel de API.

Agregue las bibliotecas ADM a su proyecto Eclipse:

1. Para obtener la biblioteca ADM, [Descargue el SDK]. Extraer el archivo zip SDK.
2. En Eclipse, haga clic en su proyecto y, a continuación, haga clic en **Propiedades**. Seleccione la **Ruta de acceso de compilación de Java** a la izquierda y, a continuación, seleccione la pestaña de **bibliotecas **en la parte superior. Haga clic en **Agregar Jar externos**y a continuación, seleccione el archivo `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` desde el directorio en que se extrae el SDK de Amazon.
3. Descargar el SDK de Android NotificationHubs (vínculo).
4. Descomprima el paquete y, a continuación, arrastre el archivo `notification-hubs-sdk.jar` en la `libs` carpeta en Eclipse.

Editar el manifiesto de aplicación para admitir ADM:

1. Agregar el espacio de nombres de Amazon en el elemento de manifiestos raíz:


        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Agregar permisos como el primer elemento en el elemento manifiesto. Sustituir **[Nombre de su paquete]** con el paquete que se usó para crear la aplicación.

        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />

        <uses-permission android:name="android.permission.INTERNET"/>

        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Inserte el siguiente elemento como el primer elemento secundario del elemento de aplicación. No olvide sustituir **[Su nombre de servicio]** con el nombre de su controlador de mensajes ADM que cree en la siguiente sección (incluido el paquete) y reemplace **[Su nombre de paquete]** con el nombre del paquete con la que ha creado la aplicación.

        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />

        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />

            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## <a name="create-your-adm-message-handler"></a>Crear el controlador de mensajes ADM

1. Crear una nueva clase que herede `com.amazon.device.messaging.ADMMessageHandlerBase` y asígnele el nombre `MyADMMessageHandler`, como se muestra en la siguiente ilustración:

    ![][6]

2. Agregue las siguientes `import` instrucciones:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub

3. Agregue el código siguiente en la clase que ha creado. No olvide sustituir el concentrador nombre y cadena de conexión (escuchar):

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }

        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }

            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }

            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();

             mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Notification Hub Demo")
                .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

4. Agregue el código siguiente a la `OnMessage()` método:

        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);

5. Agregue el código siguiente a la `OnRegistered` método:

            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }

6.  Agregue el código siguiente a la `OnUnregistered` método:

            try {
                getNotificationHub(getApplicationContext()).unregister();
            } catch (Exception e) {
                Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
            }

7. En la `MainActivity` método, agregue la siguiente instrucción de importación:

        import com.amazon.device.messaging.ADM;

8. Agregue el código siguiente al final de la `OnCreate` método:

        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                       MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>Agregar la clave API a la aplicación

1. En Eclipse, cree un nuevo archivo denominado **api_key.txt** en los activos de directorio de su proyecto.
2. Abra el archivo y copie la clave de API que ha generado en el portal de programadores de Amazon.

## <a name="run-the-app"></a>Ejecute la aplicación

1. Iniciar el emulador.
2. En el emulador, deslice el dedo desde la parte superior y haga clic en **configuración**y, a continuación, haga clic en **Mi cuenta** y registrar con una cuenta de Amazon válida.
3. En Eclipse, ejecute la aplicación.

> [AZURE.NOTE] Si se produce un problema, compruebe la hora del emulador (o dispositivo). El valor de hora debe ser preciso. Para cambiar la hora del emulador Kindle, puede ejecutar el comando siguiente desde el directorio de herramientas de la plataforma SDK Android:

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>Enviar un mensaje

Para enviar un mensaje mediante .NET:

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Portal de programadores de Amazon]: https://developer.amazon.com/home.html
[descargar el SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
