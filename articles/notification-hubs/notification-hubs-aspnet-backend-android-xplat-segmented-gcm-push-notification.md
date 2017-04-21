<properties
    pageTitle="Notificación Hubs de noticias de última hora Tutorial - Android"
    description="Aprenda a usar Hubs de notificación de Bus de servicio de Azure para enviar notificaciones de noticias de más reciente a dispositivos Android."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>


# <a name="use-notification-hubs-to-send-breaking-news"></a>Usar Hubs de notificación para enviar noticias

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##<a name="overview"></a>Información general

Este tema muestra cómo usar Hubs de notificación de Azure difundir las notificaciones de noticias de más reciente a una aplicación de Android. Cuando haya terminado, se podrá registrarse para interrumpir categorías de noticias que le interese y recibir notificaciones de inserción solo para las categorías. Este escenario es un modelo común para muchas aplicaciones donde tienen las notificaciones se envíen a los grupos de usuarios que previamente han declarado interés, por ejemplo, lector RSS, aplicaciones para ventiladores de música, etcetera.

Escenarios de difusión están habilitados, incluyendo una o más _etiquetas_ al crear un registro en el hub de notificación. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos que se han registrado para la etiqueta recibirá la notificación. Dado que las etiquetas son simplemente cadenas, no tiene estén configurados con anticipación. Para obtener más información acerca de las etiquetas, consulte [el enrutamiento de Hubs de notificación y expresiones de etiqueta](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Requisitos previos

En este tema se basa en la aplicación que creó en [Introducción a Hubs de notificación][get-started]. Antes de comenzar este tutorial, debe haber ya completado [empezar con notificación Hubs][get-started].

##<a name="add-category-selection-to-the-app"></a>Agregar selección de categoría a la aplicación

El primer paso es agregar los elementos de interfaz de usuario a su actividad principal existente que permiten al usuario seleccionar categorías para registrar. Las categorías seleccionadas por el usuario se almacenan en el dispositivo. Cuando se inicie la aplicación, se crea un registro de dispositivo en el hub de notificación con las categorías seleccionadas como etiquetas.

1. Abra el archivo res/layout/activity_main.xml y sustituir el contenido con lo siguiente:

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">

                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>

2. Abra el archivo res/values/strings.xml y agregue las siguientes líneas:

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    El diseño gráfico main_activity.xml debe tener el siguiente aspecto:

    ![][A1]

3. Ahora puede crear una clase **notificaciones** en el mismo paquete de la clase de **actividad principal** .

        import java.util.HashSet;
        import java.util.Set;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;

        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;

            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
        
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }

            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }

            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }

            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
        
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
        
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
        
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }

        }

    Esta clase utiliza el almacenamiento local para almacenar las categorías de noticias que este dispositivo debe recibir. También contiene métodos para registrar estas categorías.


4. En la clase de **actividad principal** quitar los campos privados para **NotificationHub** y **GoogleCloudMessaging**y agregar un campo para **las notificaciones**:

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

5. A continuación, en el método **onCreate** , quite la inicialización del campo **concentrador** y el método **registerWithNotificationHubs** . A continuación, agregue las siguientes líneas que inicializar una instancia de la clase de **notificaciones** . 


        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;
    
            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
    
            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);
    
            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`y `HubListenConnectionString` ya debería estar configurado con la `<hub name>` y `<connection string with listen access>` marcadores de posición con su nombre de concentrador de notificación y la cadena de conexión para *DefaultListenSharedAccessSignature* que obtuvo anteriormente.

    > [AZURE.NOTE] Porque las credenciales que se distribuyen con una aplicación de cliente no son seguras por lo general, solo debe distribuir la clave para escuchar acceso con la aplicación de cliente. Escuchar permite el acceso no se puede modificar la aplicación para registrar para las notificaciones, pero los registros existentes y no se pueden enviar notificaciones. La tecla de acceso completo se usa en un servicio de segura back-end para enviar notificaciones y modificar los registros existentes.


6. A continuación, agregue las siguientes importaciones y `subscribe` método para controlar el botón suscribirse, haga clic en evento:
        
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;

        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");

            notifications.storeCategoriesAndSubscribe(categories);
        }

    Este método crea una lista de categorías y utiliza la clase de **notificaciones** para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes con su centro de notificación. Cuando se cambian las categorías, se vuelve a crear el registro con las categorías.

La aplicación está ahora puede almacenar un conjunto de categorías en el almacenamiento local en el dispositivo y registrar con el hub de notificación cuando el usuario cambia la selección de categorías.

##<a name="register-for-notifications"></a>Registro de notificaciones

Estos pasos se registran con el concentrador de notificación en el inicio utilizando las categorías que se han almacenado en el almacenamiento local.

> [AZURE.NOTE] Dado que la registrationId asignado por Google Cloud mensajería (GCM) puede cambiar en cualquier momento, debe registrar notificaciones con frecuencia evitar errores de notificación. En este ejemplo se registra para la notificación cada vez que se inicia la aplicación. Para las aplicaciones que se ejecutan con frecuencia, más de una vez al día, puede omitir probablemente registro para conservar el ancho de banda si ha pasado menos de un día desde el registro anterior.


1. Agregue el código siguiente al final del método **onCreate** en la clase de **actividad principal** :

        notifications.subscribeToCategories(notifications.retrieveCategories());

    Esto se asegura de que cada vez que inicie la aplicación recupera las categorías de almacenamiento local y solicita un registeration para estas categorías. 

2. A continuación, actualice la `onStart()` método de la `MainActivity` de clase como sigue:

    @OverrideonStart() anular protegido {super.onStart();      isVisible = verdadero;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }

    Esto actualiza la actividad principal en función del estado de guardado anteriormente categorías.

La aplicación está ahora completa y puede almacenar un conjunto de categorías en el almacenamiento local de dispositivo utilizado para registrar con el hub de notificación cuando el usuario cambia la selección de categorías. A continuación, se definirá un back-end que puede enviar notificaciones de categoría para esta aplicación.

##<a name="sending-tagged-notifications"></a>Enviar notificaciones etiquetadas

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Ejecute la aplicación y generar notificaciones

1. En Studio Android, generar la aplicación y comenzar en un dispositivo o emulador.

    Tenga en cuenta que la interfaz de usuario de la aplicación proporciona un conjunto de alterna que le permite elegir las categorías para suscribirse a.

2. Habilitar uno o más alterna de categorías, haga clic en **suscribirse**.

    La aplicación convierte las categorías seleccionadas en las etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas desde el hub de notificación. Las categorías registradas se devuelven y se muestran en una notificación.

4. Enviar una notificación de nuevo, ejecute la aplicación de consola de .NET.  Como alternativa, puede enviar notificaciones de plantilla etiquetado con la pestaña de depuración de su centro de notificación en el [Portal de Azure clásico].

    Las notificaciones de las categorías seleccionadas aparecen como las notificaciones de aviso.

##<a name="next-steps"></a>Pasos siguientes

En este tutorial hemos visto cómo difundir noticias por categoría. Considere la posibilidad de finalización de uno de los siguientes tutoriales que resaltan otros escenarios de notificación Hubs avanzadas:

+ [Usar notificación Hubs difundir noticias localizados]

    Obtenga información sobre cómo ampliar la aplicación de noticias de más reciente para habilitar enviar notificaciones localizadas.





<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Usar notificación Hubs difundir noticias localizados]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Portal de clásico de Azure]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
