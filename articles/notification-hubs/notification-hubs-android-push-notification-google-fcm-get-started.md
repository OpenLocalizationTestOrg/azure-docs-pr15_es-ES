<properties
    pageTitle="Enviar notificaciones de inserción para Android con Hubs de notificación de Azure y la mensajería de nube de Firebase | Microsoft Azure"
    description="En este tutorial, aprenderá a utilizar Hubs de notificación de Azure y mensajería de nube de Firebase para las notificaciones de inserción a dispositivos Android."
    services="notification-hubs"
    documentationCenter="android"
    keywords="notificaciones, notificación de inserción, notificación de inserción android, fcm, firebase de inserción en la nube mensajería"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-android-with-azure-notification-hubs"></a>Enviar notificaciones de inserción para Android con Hubs de notificación de Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Información general

> [AZURE.IMPORTANT] Este tema muestra las notificaciones de inserción con Google Firebase nube mensajería (FCM). Si sigue usando mensajería de nube de Google (GCM), consulte [Enviar notificaciones de inserción para Android con Hubs de notificación de Azure y GCM](notification-hubs-android-push-notification-google-gcm-get-started.md).

En este tutorial se muestra cómo usar Hubs de notificación de Azure y la mensajería de nube de Firebase para enviar notificaciones de inserción a la aplicación Android.
Debe crear una aplicación de Android en blanco que recibe las notificaciones de inserción mediante mensajería de nube de Firebase (FCM).



[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

El código finalizado para este tutorial puede descargarse desde GitHub [aquí](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).


##<a name="prerequisites"></a>Requisitos previos

> [AZURE.IMPORTANT] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

- Además de una cuenta de Azure active mencionada arriba, este tutorial requiere la versión más reciente de [Studio Android](http://go.microsoft.com/fwlink/?LinkId=389797).
- Android 2.3 o superior para la mensajería de nube de Firebase.
- Revisión del repositorio de Google 27 o superior es necesario para mensajería de nube de Firebase.
- Servicios de Google Play 9.0.2 o superior para la mensajería de nube de Firebase.
- Realizar este tutorial es un requisito previo para todos los otros tutoriales Hubs de notificación para las aplicaciones de Android.


##<a name="create-a-new-android-studio-project"></a>Crear un nuevo proyecto Studio Android

1. En Android Studio, inicie un nuevo proyecto Studio Android.

    ![Android Studio - nuevo proyecto](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)

2. Elija el factor de forma de **teléfonos y tabletas** y el **SDK de mínima** que desea admitir. A continuación, haga clic en **siguiente**.

    ![Android Studio - flujo de trabajo de creación de proyecto](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)

3. Elija **Actividad vacía** de la actividad principal, haga clic en **siguiente**y, a continuación, haga clic en **Finalizar**.


##<a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Crear un proyecto que admita Firebase nube mensajería

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]


##<a name="configure-a-new-notification-hub"></a>Configurar un nuevo concentrador de notificación

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. en el módulo de **configuración** de su centro de notificación, seleccione **Los servicios de notificación** y **Google (GCM)**. Escriba la clave del servidor FCM que copió anteriormente desde la [consola de Firebase](https://firebase.google.com/console/) y haga clic en **Guardar**.

&emsp;&emsp;![Notificación de Azure Hubs - Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

El concentrador de notificación ahora está configurado para trabajar con Firebase Messagin de nube y tiene las cadenas de conexión a ambos registrar su aplicación para recibir y enviar notificaciones de inserción.

##<a id="connecting-app"></a>Conectar la aplicación al concentrador de notificación

###<a name="add-google-play-services-to-the-project"></a>Agregar servicios de Google Play al proyecto

[AZURE.INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###<a name="adding-azure-notification-hubs-libraries"></a>Agregar bibliotecas Hubs de notificación de Azure


1. En el `Build.Gradle` para la **aplicación**, agregue las siguientes líneas en la sección **dependencias** .

        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. Agregar el siguiente repositorio después de la sección **dependencias** .

        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### <a name="updating-the-androidmanifestxml"></a>Actualizar la AndroidManifest.xml.


1. Para admitir FCM, debemos implementar un servicio de escucha de Id. de instancia en nuestro código que se usa para [obtener tokens de registro](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) con la [API de FirebaseInstanceId de Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). En este tutorial se le asigne un nombre a la clase `MyInstanceIDService`. 
 
    Agregue la siguiente definición de servicio en el archivo AndroidManifest.xml, dentro del `<application>` etiqueta. 

        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>



2. Una vez que hemos recibido nuestro token de registro FCM de la API FirebaseInstanceId, se usará para [registrar con el Hub de notificación de Azure](notification-hubs-push-notification-registration-management.md). Es compatible con este registro en segundo plano utilizando un `IntentService` denominado `RegistrationIntentService`. Este servicio también será responsable de la actualización de nuestro token de registro FCM.
 
    Agregue la siguiente definición de servicio en el archivo AndroidManifest.xml, dentro del `<application>` etiqueta. 

        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>



3. También definirá un receptor para recibir notificaciones. Agregue la siguiente definición de receptor al archivo AndroidManifest.xml, dentro del `<application>` etiqueta. Reemplazar el `<your package>` marcador de posición con la su nombre de paquete real se muestra en la parte superior de la `AndroidManifest.xml` archivo.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>



4. Agregue las siguientes FCM necesario relacionados con los permisos siguientes la `</application>` etiqueta. Asegúrese de reemplazar `<your package>` con el nombre del paquete que se muestra en la parte superior de la `AndroidManifest.xml` archivo.

    Para obtener más información sobre estos permisos, vea [Configurar una aplicación de cliente de GCM para Android](https://developers.google.com/cloud-messaging/android/client#manifest) y [migrar una aplicación de cliente GCM para Android mensajería de nube de Firebase](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm).

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />


### <a name="adding-code"></a>Agregar código


1. En la vista de proyecto, expanda la **aplicación** > **src** > **principal** > **java**. Haga clic en la carpeta del paquete en **java**, haga clic en **nuevo**y, a continuación, haga clic en **Clase de Java**. Agregar una nueva clase denominada `NotificationSettings`. 

    ![Android Studio - nueva clase de Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)

    Asegúrese de actualizar estos tres marcadores de posición en el siguiente código para el `NotificationSettings` clase:
    * **SenderId**: el identificador de remitente obtenido anteriormente en la pestaña **Mensajería de nube** de la configuración del proyecto en la [consola de Firebase](https://firebase.google.com/console/).
    * **HubListenConnectionString**: la cadena de conexión de **DefaultListenAccessSignature** para su centro. Puede copiar esa cadena de conexión haciendo clic en **Directivas de acceso** en el módulo de **configuración** de su centro en el [Portal de Azure].
    * **HubName**: utilice el nombre de su centro de notificación que aparece en el módulo de concentrador en el [Portal de Azure].

    `NotificationSettings`código:

        public class NotificationSettings {
            public static String SenderId = "<Your project number>";
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }

2. Con los pasos anteriores, agregue otra nueva clase denominada `MyInstanceIDService`. Se trata de nuestra implementación de servicio de escucha de instancia ID.

    El código para esta clase llamará nuestra `IntentService` [actualizar el token FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) en segundo plano.

        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;
        
        
        public class MyInstanceIDService extends FirebaseInstanceIdService {
        
            private static final String TAG = "MyInstanceIDService";
        
            @Override
            public void onTokenRefresh() {
        
                Log.d(TAG, "Refreshing GCM Registration Token");
        
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };


3. Agregue otra nueva clase a su proyecto con el nombre, `RegistrationIntentService`. Se trata de la implementación de nuestros `IntentService` controlará [actualizar el token FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) y [registrar con el concentrador de notificación](notification-hubs-push-notification-registration-management.md).

    Use el siguiente código para esta clase.

        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;        
        import com.google.firebase.iid.FirebaseInstanceId;
        import com.microsoft.windowsazure.messaging.NotificationHub;
        
        public class RegistrationIntentService extends IntentService {
        
            private static final String TAG = "RegIntentService";
        
            private NotificationHub hub;
        
            public RegistrationIntentService() {
                super(TAG);
            }
        
            @Override
            protected void onHandleIntent(Intent intent) {
        
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
                String storedToken = null;
        
                try {
                    String FCM_token = FirebaseInstanceId.getInstance().getToken();
                    Log.d(TAG, "FCM Registration Token: " + FCM_token);
        
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
        
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
        
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
        
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
        
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
        
                    // Check if the token may have been compromised and needs refreshing.
                    else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
        
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
        
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
        
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
        
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
        
                    else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete registration", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
        
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }


        
4. En la `MainActivity` de clase, agregue lo siguiente `import` instrucciones encima de la declaración de clase.

        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;

5. Agregue los siguientes miembros privado en la parte superior de la clase. Vamos a usar estos [comprueba la disponibilidad de los servicios de reproducción de Google recomendada por Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. En la `MainActivity` de clase, agregue el método siguiente a la disponibilidad de los servicios de Google Play. 

        /**
         * Check the device to make sure it has the Google Play Services APK. If
         * it doesn't, display a dialog that allows users to download the APK from
         * the Google Play Store or enable it in the device's system settings.
         */
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }


7. En la `MainActivity` de clase, agregue el código siguiente que buscará servicios de Google Play antes de llamar a su `IntentService` para obtener el registro FCM token y registrar con su centro de notificación.

        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }


8. En la `OnCreate` método de la `MainActivity` de clase, agregue el código siguiente para iniciar el proceso de registro cuando se crea la actividad.

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }


9. Agregar estos métodos adicionales a la `MainActivity` para comprobar el estado de informe y el estado de aplicación en la aplicación.

        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
    
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }


10. La `ToastNotify` método utiliza el *"Hola a todos"* `TextView` control para informar del estado y notificaciones de manera permanente en la aplicación. En el diseño activity_main.xml, agregue el siguiente identificador de dicho control.

        android:id="@+id/text_hello"

11. A continuación, agregaremos una subclase para nuestra receptor definido en la AndroidManifest.xml. Agregue otra nueva clase a su proyecto denominado `MyHandler`.

12. Agregue las siguientes instrucciones de importación en la parte superior de `MyHandler.java`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Agregue el código siguiente para el `MyHandler` clase realizar una subclase de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Este código reemplaza el `OnReceive` método, por lo que el controlador de informe que se reciben las notificaciones. El controlador también envía la notificación de inserción al administrador Android notificación utilizando la `sendNotification()` método. La `sendNotification()` método se debe ejecutar cuando no se ejecuta la aplicación y se recibe una notificación.

        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
        
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
        
            private void sendNotification(String msg) {
        
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
        
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
        
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
        
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }


14. Android Studio en la barra de menús, haga clic en **Generar** > **Proyecto reconstruir** para asegurarse de que no hay errores están presentes en el código.
15. Ejecute la aplicación en el dispositivo y comprobar registra correctamente con el concentrador de notificación. 

    > [AZURE.NOTE] Registro puede dar error en el inicio inicial hasta la `onTokenRefresh()` se denomina método de instancia de servicio de Id. La actualización debe empezar un registro correctamente con el concentrador de notificación.

##<a name="sending-push-notifications"></a>Enviar notificaciones de inserción

Puede probar recibe las notificaciones de inserción en la aplicación enviándolos a través del [Portal de Azure] -, busque la sección de **solución de problemas** en el módulo de concentrador, tal como se muestra a continuación.

![Enviar notificación de Azure Hubs - prueba](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Opcional) Enviar notificaciones de inserción directamente desde la aplicación

>[AZURE.IMPORTANT] En este ejemplo de enviar notificaciones de la aplicación cliente se proporciona con fines de aprendizaje. Dado que esto requerirá la `DefaultFullSharedAccessSignature` para presentar en la aplicación de cliente, expone su centro de notificación para el riesgo de que un usuario puede tener acceso para enviar notificaciones no autorizadas a sus clientes.

Normalmente, debe enviar notificaciones utilizando un servidor back-end. En algunos casos, podría desea poder enviar notificaciones de inserción directamente desde la aplicación cliente. En esta sección se explica cómo enviar notificaciones desde el cliente con la [API de REST de concentrador de notificación de Azure](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. En la vista de proyecto de Android Studio, expanda **aplicación** > **src** > **principal** > **resolución** > **Diseño**. Abrir la `activity_main.xml` archivo de diseño y haga clic en el **texto** de la ficha para actualizar el contenido de texto del archivo. Lo actualiza con el código siguiente, que agrega nuevas `Button` y `EditText` controles para enviar mensajes de notificación de inserción al concentrador de notificación. Agregue este código en la parte inferior, justo antes `</RelativeLayout>`.

        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. En la vista de proyecto de Android Studio, expanda **aplicación** > **src** > **principal** > **resolución** > **valores**. Abrir la `strings.xml` de archivos y agregue los valores de cadena que hace referencia la nueva `Button` y `EditText` controles. Agregar estos elementos en la parte inferior del archivo, justo antes `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. En el `NotificationSetting.java` , agregue la siguiente configuración para la `NotificationSettings` clase.

    Actualización `HubFullAccess` con la cadena de conexión de **DefaultFullSharedAccessSignature** para su centro. Esta cadena de conexión se puede copiar desde el [Portal de Azure] haciendo clic en **Directivas de acceso** en el módulo de **configuración** para su centro de notificación.

        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";

4. En el `MainActivity.java` , agregue lo siguiente `import` instrucciones anteriores la `MainActivity` clase.

        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;

6. En la `MainActivity.java` de archivos, agregue los siguientes miembros en la parte superior de la `MainActivity` clase.  

        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;

6. Debe crear un token de acceso de Software firma (SA) para autenticar una solicitud de entrada para enviar mensajes a su centro de notificación. Para ello, analizar los datos de clave de la cadena de conexión y, a continuación, crear el token de SA, como se mencionó en la referencia de la API de REST de [Conceptos comunes](http://msdn.microsoft.com/library/azure/dn495627.aspx) . El código siguiente es un ejemplo de implementación.

    En `MainActivity.java`, agregue el método siguiente a la `MainActivity` clase para analizar la cadena de conexión.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
    
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }


7. En `MainActivity.java`, agregue el método siguiente a la `MainActivity` clase para crear un símbolo de autenticación SA.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
    
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
    
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
    
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
    
            return token;
        }



8. En `MainActivity.java`, agregue el método siguiente a la `MainActivity` clase para controlar el clic de botón **Enviar notificación** y enviar el mensaje de notificación de inserción al concentrador con la API de REST integrados.

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
    
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
    
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
    
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
    
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
    
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
    
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //      "tag1 || tag2 || tag3");
    
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
    
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }



##<a name="testing-your-app"></a>Probar la aplicación

####<a name="push-notifications-in-the-emulator"></a>Notificaciones de inserción en el emulador

Si desea probar las notificaciones de inserción dentro de un emulador, asegúrese de que la imagen de emulador admite el nivel de API de Google que eligió en la aplicación. Si la imagen no admite nativa APIs Google, se terminará con la **servicio\_no\_disponibles** excepción.

Además, asegúrese de que ha agregado su cuenta de Google a su emulador en ejecución en **configuración** > **cuentas**. En caso contrario, intenta registrar con GCM puede traducirse en la **autenticación\_error** excepción.

####<a name="running-the-application"></a>Ejecutar la aplicación

1. Ejecute la aplicación y observe que el identificador de registro se notifica para un registro correcto.

    ![Pruebas en Android - registro de canal](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)

2. Escriba un mensaje de notificación que se envíen a todos los dispositivos Android que se han registrado con el concentrador.

    ![Pruebas en Android - enviar un mensaje](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)

3. Presione **Enviar notificación**. Todos los dispositivos que tienen la aplicación ejecutando mostrará un `AlertDialog` instancia con el mensaje de notificación de inserción. Dispositivos que no tienen la aplicación ejecutando pero registraron anteriormente para las notificaciones de inserción recibirán una notificación en el Administrador de notificación de Android. Los que se pueden visualizar deslizando hacia abajo de la esquina superior izquierda.

    ![Pruebas en Android - notificaciones](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

##<a name="next-steps"></a>Pasos siguientes

Se recomienda el tutorial de [Uso Hubs de notificación para las notificaciones de inserción a los usuarios] como el siguiente paso. Le mostrará cómo enviar notificaciones de un back-end ASP.NET usar etiquetas para dirigirse a usuarios específicos.

Si desea segmentar los usuarios por grupos de interés, consulte el tutorial de [Hubs de notificación de uso para enviar noticias] .

Para obtener más información general sobre Hubs de notificación, consulte nuestra [Guía de Hubs de notificación].

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Guía de Hubs de notificación]: notification-hubs-push-notification-overview.md
[Usar Hubs de notificación para las notificaciones de inserción a los usuarios]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Usar Hubs de notificación para enviar noticias]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portal de Azure]: https://portal.azure.com
