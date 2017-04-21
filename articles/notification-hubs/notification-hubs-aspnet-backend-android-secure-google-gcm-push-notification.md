<properties
    pageTitle="Enviar notificaciones de inserción segura con Hubs de notificación de Azure"
    description="Obtenga información sobre cómo enviar notificaciones de inserción seguro a una aplicación Android de Azure. Ejemplos de código escritos en Java y C#."
    documentationCenter="android"
    keywords="inserción de notificación, las notificaciones de inserción, mensajes, las notificaciones de inserción android de inserción"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

#<a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Enviar notificaciones de inserción segura con Hubs de notificación de Azure

> [AZURE.SELECTOR]
- [Universal de Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

##<a name="overview"></a>Información general

> [AZURE.IMPORTANT] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Soporte de notificaciones de inserción en Microsoft Azure le permite acceder a una infraestructura de mensaje inserción fácil de usar, varias plataformas, escala horizontal, que simplifica la implementación de notificaciones de inserción para las aplicaciones de consumidor y empresa para las plataformas móviles.

Debido a normativos o restricciones de seguridad, a veces una aplicación que desee incluir algo en la notificación de que no se pueden transmitir a través de la infraestructura de notificación de inserción estándar. Este tutorial describe cómo lograr la misma experiencia mediante el envío de información confidencial a través de una conexión segura y autenticada entre el dispositivo Android de cliente y el servidor de la aplicación.

En un nivel alto, el flujo es como sigue:

1. La aplicación de fondo:
    - Carga de seguridad de almacena en la base de datos back-end.
    - Envía el identificador de esta notificación en el dispositivo Android (no se envía ninguna información segura).
2. La aplicación en el dispositivo, al recibir la notificación:
    - El dispositivo Android el back-end que solicita la carga de seguridad de contactos.
    - La aplicación puede mostrar la carga como una notificación en el dispositivo.

Es importante que tenga en cuenta que en el flujo anterior (y en este tutorial), se supone que el dispositivo almacena un símbolo de autenticación en almacenamiento local, después de que el usuario inicia sesión. Así garantiza una experiencia perfecta por completo, como el dispositivo puede recuperar la carga de seguridad de la notificación con este token. Si la aplicación no almacenar tokens de autenticación en el dispositivo, o si se pueden caducar estos tokens, la aplicación de dispositivo, al recibir la notificación de inserción debe mostrar una notificación genérica preguntar al usuario para iniciar la aplicación. A continuación, la aplicación autentica al usuario y muestra la carga de notificación.

Este tutorial muestra cómo enviar notificaciones de inserción segura. Se basa en el tutorial de [Informar a los usuarios](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , por lo que debe completar los pasos en ese tutorial primero si todavía no lo ha hecho.

> [AZURE.NOTE] En este tutorial se asume que ha creado y configurado su centro de notificación, como se describe en [Introducción a la notificación Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modificar el proyecto de Android

Ahora que ha modificado su aplicación back-end para enviar el *identificador* de una notificación de inserción, debe cambiar su aplicación Android para controlar la notificación y el back-end para recuperar el mensaje seguro para que se muestre de devolución de llamada.
Para ello, debe asegurarse de que su aplicación Android sabe cómo autenticarse con su back-end cuando recibe las notificaciones de inserción.

Vamos a modificar ahora el flujo de *Inicio de sesión* para guardar el valor de encabezado de autenticación en las preferencias de la aplicación compartidas. Mecanismos análogos se pueden usar para almacenar cualquier token de autenticación (por ejemplo, OAuth tokens) que la aplicación tendrá que utilizar sin necesidad de credenciales de usuario.

1. En el proyecto de aplicación Android, agregue las siguientes constantes en la parte superior de la clase de **actividad principal** :

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Aún en la clase de **actividad principal** , actualizar la `getAuthorizationHeader()` método que contenga el siguiente código:

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3. Agregue las siguientes `import` instrucciones en la parte superior del archivo de **actividad principal** :

        import android.content.SharedPreferences;

Ahora cambiaremos el controlador a la que se llama cuando se recibe la notificación.

4. En la **MyHandler** clase cambiar la `OnReceive()` método que contengan:

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

5. A continuación, agregue el `retrieveNotification()` método, reemplazar el marcador de posición `{back-end endpoint}` con el extremo de back-end que se obtienen al implementar el fondo:

        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }


Este método llama a su aplicación back-end para recuperar el contenido de la notificación con las credenciales almacenadas en las preferencias de compartido y muestra como una notificación normal. Busca la notificación al usuario de aplicación exactamente igual que cualquier otra notificación de inserción.

Tenga en cuenta que es preferible para controlar los casos de la propiedad de encabezado de autenticación que falta o rechace por el back-end. La administración de estos casos específica dependen principalmente de la experiencia del usuario de destino. Es una opción Mostrar una notificación con un mensaje genérico para autenticar al usuario para recuperar la notificación.

## <a name="run-the-application"></a>Ejecute la aplicación

Para ejecutar la aplicación, haga lo siguiente:

1. Asegúrese de que **AppBackend** se implementa en Azure. Si utiliza Visual Studio, ejecute la aplicación de Web API de **AppBackend** . Se muestra una página web ASP.NET.

2. En Eclipse, ejecute la aplicación en un dispositivo Android físico o el emulador.

3. En la interfaz de usuario de la aplicación Android, escriba un nombre de usuario y contraseña. Pueden ser cualquier cadena, pero deben ser el mismo valor.

4. En el interfaz de usuario de la aplicación Android, haga clic en **iniciar sesión**. A continuación, haga clic en **Enviar push**.
