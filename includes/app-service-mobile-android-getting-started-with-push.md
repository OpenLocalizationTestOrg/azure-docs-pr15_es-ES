1. En el proyecto de la **aplicación** , abra el archivo `AndroidManifest.xml`. En el código en los dos pasos siguientes, reemplace _`**my_app_package**`_ con el nombre del paquete de aplicación para el proyecto, que es el valor de la `package` atributo de la `manifest` etiqueta.

2. Agregue los siguientes permisos nuevos después de la existente `uses-permission` elemento:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Agregue el código siguiente después de la `application` etiqueta de apertura:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Abra el archivo *ToDoActivity.java*y agregue la siguiente instrucción de importación:

        import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Agregue la siguiente variable privada a la clase: reemplazar _`<PROJECT_NUMBER>`_ con el número de proyecto asignado por Google a su aplicación en el procedimiento anterior:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

6. Cambiar la definición de la *MobileServiceClient* de **privado** a **estático público**, por lo que ahora el siguiente aspecto:

        public static MobileServiceClient mClient;

7. A continuación, necesitamos agregar una nueva clase para controlar las notificaciones. En el Explorador de proyectos, abra el **src** => **principal** => nodos de**java** y contextual el nodo de nombre de paquete: haga clic en **nuevo**y luego haga clic en **Clase de Java**.

8. En **nombre** , escriba `MyHandler`, a continuación, haga clic en **Aceptar**.


    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)


9. En el archivo MyHandler, reemplace la declaración de clase con

        public class MyHandler extends NotificationsHandler {


10. Agregue las siguientes instrucciones de importación para la `MyHandler` clase:

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;


11. A continuación, agregue este miembro a la `MyHandler` clase:

        public static final int NOTIFICATION_ID = 1;


12. En la `MyHandler` de clase, agregue el código siguiente para reemplazar el método **onRegistered** , que registra el dispositivo con el concentrador de notificación del servicio móvil.

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            new AsyncTask<Void, Void, Void>() {

                protected Void doInBackground(Void... params) {
                    try {
                        ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                        return null;
                    }
                    catch(Exception e) {
                        // handle error             
                    }
                    return null;            
                }
            }.execute();
        }


13. En la `MyHandler` de clase, agregue el código siguiente para reemplazar el método **onReceive** , que hace que la notificación que se muestra cuando se reciba.

        @Override
        public void onReceive(Context context, Bundle bundle) {
                String msg = bundle.getString("message");

                PendingIntent contentIntent = PendingIntent.getActivity(context,
                        0, // requestCode
                        new Intent(context, ToDoActivity.class),
                        0); // flags

                Notification notification = new NotificationCompat.Builder(context)
                        .setSmallIcon(R.drawable.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                        .setContentText(msg)
                        .setContentIntent(contentIntent)
                        .build();

                NotificationManager notificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);
                notificationManager.notify(NOTIFICATION_ID, notification);
        }


14. En el archivo TodoActivity.java, actualice el método de **onCreate** de la clase *ToDoActivity* para registrar la clase de controlador de notificación. Asegúrese de agregar este código cuando se crea una instancia de la *MobileServiceClient* .


        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    La aplicación se ha actualizado para admitir notificaciones de inserción.
