<properties
    pageTitle="Azure notificación Hubs informar a los usuarios para Android con .NET back-end"
    description="Aprenda a enviar notificaciones de inserción a los usuarios en Azure. Ejemplos de código escritos en Java para Android"
    documentationCenter="android"
    services="notification-hubs"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-notify-users-for-android-with-net-backend"></a>Azure notificación Hubs informar a los usuarios para Android con .NET back-end


[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

##<a name="overview"></a>Información general

Soporte de notificaciones de inserción en Azure le permite acceder a una fácil de usar, el varias plataformas y la infraestructura de inserción de escala horizontal, lo que simplifica la implementación de notificaciones de inserción para las aplicaciones de consumidor y empresa para las plataformas móviles. En este tutorial se muestra cómo usar Hubs de notificación de Azure para enviar notificaciones de inserción a un usuario de la aplicación específica en un dispositivo específico. Un back-end de ASP.NET WebAPI se usa para autenticar a clientes y generar notificaciones, como se muestra en el tema de guía [registro de la aplicación de back-end](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend). En este tutorial se basa en el hub de notificación que creó en el tutorial de [Introducción con notificación Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md) .

> [AZURE.NOTE] En este tutorial se asume que ha creado y configurado su centro de notificación, como se describe en [Introducción a la notificación Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="create-the-android-project"></a>Crear el proyecto de Android

El siguiente paso es crear la aplicación Android.

1. Siga el tutorial de [Introducción con notificación Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md) para crear y configurar la aplicación para recibir notificaciones de inserción de GCM.

2. Abra el archivo **res/layout/activity_main.xml** , reemplace el con las siguientes definiciones de contenido.

    Esto agrega nuevos controles de EditText para iniciar sesión como un usuario. También se agrega un campo de una etiqueta de nombre de usuario que forme parte de notificaciones que envíe:

        <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
            android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

        <EditText
            android:id="@+id/usernameText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/usernameHint"
            android:layout_above="@+id/passwordText"
            android:layout_alignParentEnd="true" />
        <EditText
            android:id="@+id/passwordText"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:ems="10"
            android:hint="@string/passwordHint"
            android:inputType="textPassword"
            android:layout_above="@+id/buttonLogin"
            android:layout_alignParentEnd="true" />
        <Button
            android:id="@+id/buttonLogin"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/loginButton"
            android:onClick="login"
            android:layout_above="@+id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:layout_marginBottom="24dp" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="WNS on"
            android:textOff="WNS off"
            android:id="@+id/toggleButtonWNS"
            android:layout_toLeftOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="GCM on"
            android:textOff="GCM off"
            android:id="@+id/toggleButtonGCM"
            android:checked="true"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true" />
        <ToggleButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textOn="APNS on"
            android:textOff="APNS off"
            android:id="@+id/toggleButtonAPNS"
            android:layout_toRightOf="@id/toggleButtonGCM"
            android:layout_centerVertical="true" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessageTag"
            android:layout_below="@id/toggleButtonGCM"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_tag_hint" />
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/editTextNotificationMessage"
            android:layout_below="@+id/editTextNotificationMessageTag"
            android:layout_centerHorizontal="true"
            android:hint="@string/notification_message_hint" />
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/send_button"
            android:id="@+id/sendbutton"
            android:onClick="sendNotificationButtonOnClick"
            android:layout_below="@+id/editTextNotificationMessage"
            android:layout_centerHorizontal="true" />
        </RelativeLayout>



3. Abra el archivo **res/values/strings.xml** y reemplace la `send_button` definición con las siguientes líneas que redefinir la cadena para la `send_button` y agregue las cadenas de los otros controles:

        <string name="usernameHint">Username</string>
        <string name="passwordHint">Password</string>
        <string name="loginButton">1. Log in</string>
        <string name="send_button">2. Send Notification</string>
        <string name="notification_message_tag_hint">
            Recipient username tag
        </string>

    El diseño gráfico main_activity.xml debe tener el siguiente aspecto:

    ![][A1]

4. Crear una nueva clase denominada **RegisterClient** en el mismo paquete como su `MainActivity` clase. Use el código siguiente para el nuevo archivo de clase.

        import java.io.IOException;
        import java.io.UnsupportedEncodingException;
        import java.util.Set;

        import org.apache.http.HttpResponse;
        import org.apache.http.HttpStatus;
        import org.apache.http.client.ClientProtocolException;
        import org.apache.http.client.HttpClient;
        import org.apache.http.client.methods.HttpPost;
        import org.apache.http.client.methods.HttpPut;
        import org.apache.http.client.methods.HttpUriRequest;
        import org.apache.http.entity.StringEntity;
        import org.apache.http.impl.client.DefaultHttpClient;
        import org.apache.http.util.EntityUtils;
        import org.json.JSONArray;
        import org.json.JSONException;
        import org.json.JSONObject;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.util.Log;

        public class RegisterClient {
            private static final String PREFS_NAME = "ANHSettings";
            private static final String REGID_SETTING_NAME = "ANHRegistrationId";
            private String Backend_Endpoint;
            SharedPreferences settings;
            protected HttpClient httpClient;
            private String authorizationHeader;

            public RegisterClient(Context context, String backendEnpoint) {
                super();
                this.settings = context.getSharedPreferences(PREFS_NAME, 0);
                httpClient =  new DefaultHttpClient();
                Backend_Endpoint = backendEnpoint + "/api/register";
            }

            public String getAuthorizationHeader() {
                return authorizationHeader;
            }

            public void setAuthorizationHeader(String authorizationHeader) {
                this.authorizationHeader = authorizationHeader;
            }

            public void register(String handle, Set<String> tags) throws ClientProtocolException, IOException, JSONException {
                String registrationId = retrieveRegistrationIdOrRequestNewOne(handle);

                JSONObject deviceInfo = new JSONObject();
                deviceInfo.put("Platform", "gcm");
                deviceInfo.put("Handle", handle);
                deviceInfo.put("Tags", new JSONArray(tags));

                int statusCode = upsertRegistration(registrationId, deviceInfo);

                if (statusCode == HttpStatus.SC_OK) {
                    return;
                } else if (statusCode == HttpStatus.SC_GONE){
                    settings.edit().remove(REGID_SETTING_NAME).commit();
                    registrationId = retrieveRegistrationIdOrRequestNewOne(handle);
                    statusCode = upsertRegistration(registrationId, deviceInfo);
                    if (statusCode != HttpStatus.SC_OK) {
                        Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                        throw new RuntimeException("Error upserting registration");
                    }
                } else {
                    Log.e("RegisterClient", "Error upserting registration: " + statusCode);
                    throw new RuntimeException("Error upserting registration");
                }
            }

            private int upsertRegistration(String registrationId, JSONObject deviceInfo)
                    throws UnsupportedEncodingException, IOException,
                    ClientProtocolException {
                HttpPut request = new HttpPut(Backend_Endpoint+"/"+registrationId);
                request.setEntity(new StringEntity(deviceInfo.toString()));
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                request.addHeader("Content-Type", "application/json");
                HttpResponse response = httpClient.execute(request);
                int statusCode = response.getStatusLine().getStatusCode();
                return statusCode;
            }

            private String retrieveRegistrationIdOrRequestNewOne(String handle) throws ClientProtocolException, IOException {
                if (settings.contains(REGID_SETTING_NAME))
                    return settings.getString(REGID_SETTING_NAME, null);

                HttpUriRequest request = new HttpPost(Backend_Endpoint+"?handle="+handle);
                request.addHeader("Authorization", "Basic "+authorizationHeader);
                HttpResponse response = httpClient.execute(request);
                if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                    Log.e("RegisterClient", "Error creating registrationId: " + response.getStatusLine().getStatusCode());
                    throw new RuntimeException("Error creating Notification Hubs registrationId");
                }
                String registrationId = EntityUtils.toString(response.getEntity());
                registrationId = registrationId.substring(1, registrationId.length()-1);

                settings.edit().putString(REGID_SETTING_NAME, registrationId).commit();

                return registrationId;
            }
        }

    Este componente implementa el resto de llamadas necesarias para póngase en contacto con el servidor de la aplicación, con el fin de registrar las notificaciones de inserción. Localmente también almacena la *registrationIds* creado por el concentrador de notificación como se describe en el [registro de la aplicación de back-end](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend). Tenga en cuenta que usa un token de autorización almacenado en almacenamiento local al hacer clic en el botón de **Inicio de sesión** .

5. En el `MainActivity` clase quitar o comentar el campo private para `NotificationHub`, y agregar un campo para el `RegisterClient` clase y una cadena de extremo de su servidor ASP.NET. Asegúrese de reemplazar `<Enter Your Backend Endpoint>` con la su extremo real back-end obtenido previamente. Por ejemplo, `http://mybackend.azurewebsites.net`.


        //private NotificationHub hub;
        private RegisterClient registerClient;
        private static final String BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";


6. En la `MainActivity` de clase, en la `onCreate` método, elimine o comente la inicialización de la `hub` campo y la llamada a la `registerWithNotificationHubs` método. A continuación, agregar código para inicializar una instancia de la `RegisterClient` clase. El método debe contener las siguientes líneas:

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            MyHandler.mainActivity = this;
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
            gcm = GoogleCloudMessaging.getInstance(this);

            //hub = new NotificationHub(HubName, HubListenConnectionString, this);
            //registerWithNotificationHubs();

            registerClient = new RegisterClient(this, BACKEND_ENDPOINT);

            setContentView(R.layout.activity_main);
        }

7. En la `MainActivity` de clase, eliminar o comentar todo el `registerWithNotificationHubs` método. No se utilizará en este tutorial.

8. Agregue las siguientes `import` instrucciones al archivo **MainActivity.java** .

        import android.widget.Button;
        import java.io.UnsupportedEncodingException;
        import android.content.Context;
        import java.util.HashSet;
        import android.widget.Toast;
        import org.apache.http.client.ClientProtocolException;
        import java.io.IOException;
        import org.apache.http.HttpStatus;


9. A continuación, agregue los siguientes métodos para controlar el botón **iniciar sesión** , haga clic en evento y enviar notificaciones de inserción.

        @Override
        protected void onStart() {
            super.onStart();
            Button sendPush = (Button) findViewById(R.id.sendbutton);
            sendPush.setEnabled(false);
        }

        public void login(View view) throws UnsupportedEncodingException {
            this.registerClient.setAuthorizationHeader(getAuthorizationHeader());

            final Context context = this;
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(SENDER_ID);
                        registerClient.register(regid, new HashSet<String>());
                    } catch (Exception e) {
                        DialogNotify("MainActivity - Failed to register", e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    Button sendPush = (Button) findViewById(R.id.sendbutton);
                    sendPush.setEnabled(true);
                    Toast.makeText(context, "Logged in and registered.",
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
            return basicAuthHeader;
        }

        /**
         * This method calls the ASP.NET WebAPI backend to send the notification message
         * to the platform notification service based on the pns parameter.
         *
         * @param pns     The platform notification service to send the notification message to. Must
         *                be one of the following ("wns", "gcm", "apns").
         * @param userTag The tag for the user who will receive the notification message. This string
         *                must not contain spaces or special characters.
         * @param message The notification message string. This string must include the double quotes
         *                to be used as JSON content.
         */
        public void sendPush(final String pns, final String userTag, final String message)
                throws ClientProtocolException, IOException {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {

                        String uri = BACKEND_ENDPOINT + "/api/notifications";
                        uri += "?pns=" + pns;
                        uri += "&to_tag=" + userTag;

                        HttpPost request = new HttpPost(uri);
                        request.addHeader("Authorization", "Basic "+ getAuthorizationHeader());
                        request.setEntity(new StringEntity(message));
                        request.addHeader("Content-Type", "application/json");

                        HttpResponse response = new DefaultHttpClient().execute(request);

                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            DialogNotify("MainActivity - Error sending " + pns + " notification",
                                response.getStatusLine().toString());
                            throw new RuntimeException("Error sending notification");
                        }
                    } catch (Exception e) {
                        DialogNotify("MainActivity - Failed to send " + pns + " notification ", e.getMessage());
                        return e;
                    }

                    return null;
                }
            }.execute(null, null, null);
        }


    La `login` controlador para el botón **iniciar sesión** genera una autenticación básica token con en la entrada de nombre de usuario y la contraseña (tenga en cuenta que esto representa cualquier token de su esquema de autenticación usa), a continuación, se utiliza `RegisterClient` llamar el back-end de registro.

    La `sendPush` método llama el back-end para desencadenar una notificación al usuario en función de la etiqueta de usuario segura. Servicio de notificación de plataforma que `sendPush` destinos depende de la `pns` cadena pasa en.

10. En la `MainActivity` de clase, actualizar la `sendNotificationButtonOnClick` método para llamar a la `sendPush` método con el usuario seleccionado servicios de notificación de la plataforma de manera.

        /**
         * Send Notification button click handler. This method sends the push notification
         * message to each platform selected.
         *
         * @param v The view
         */
        public void sendNotificationButtonOnClick(View v)
                throws ClientProtocolException, IOException {

            String nhMessageTag = ((EditText) findViewById(R.id.editTextNotificationMessageTag))
                    .getText().toString();
            String nhMessage = ((EditText) findViewById(R.id.editTextNotificationMessage))
                    .getText().toString();

            // JSON String
            nhMessage = "\"" + nhMessage + "\"";

            if (((ToggleButton)findViewById(R.id.toggleButtonWNS)).isChecked())
            {
                sendPush("wns", nhMessageTag, nhMessage);
            }
            if (((ToggleButton)findViewById(R.id.toggleButtonGCM)).isChecked())
            {
                sendPush("gcm", nhMessageTag, nhMessage);
            }
            if (((ToggleButton)findViewById(R.id.toggleButtonAPNS)).isChecked())
            {
                sendPush("apns", nhMessageTag, nhMessage);
            }
        }



## <a name="run-the-application"></a>Ejecute la aplicación


1. Ejecute la aplicación en un dispositivo o un emulador mediante Studio Android.

2. En la aplicación Android, escriba un nombre de usuario y contraseña. Ambas deben tener el mismo valor de cadena y no debe contener espacios ni caracteres especiales.

3. En la aplicación Android, haga clic en **iniciar sesión**. Espere a que un mensaje de aviso que indica la **sesión iniciada en y registrado**. Esto le permitirá el botón **Enviar notificación** .

    ![][A2]

4. Haga clic en el botón de alternancia botones para habilitar todas las plataformas donde tiene ejecutó la aplicación y registrado un usuario.
5. Escriba el nombre del usuario que recibirá el mensaje de notificación. Ese usuario debe estar registrado para recibir notificaciones en los dispositivos de destino.

6. Escriba un mensaje para el usuario reciba un mensaje de notificación de inserción.
7. Haga clic en **Enviar notificación**.  Cada dispositivo que tiene un registro con la etiqueta de nombre de usuario correspondiente recibirá la notificación de inserción.


[A1]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users.png
[A2]: ./media/notification-hubs-aspnet-backend-android-notify-users/android-notify-users-enter-password.png
