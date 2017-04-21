<properties
    pageTitle="Integración de Azure compromiso móviles Android SDK"
    description="Últimas actualizaciones y procedimientos para Android SDK para Azure Mobile contratación"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-reach-on-android"></a>Cómo integrar alcance de contratación en Android

> [AZURE.IMPORTANT] Debe seguir el procedimiento de integración descrito en cómo integrar compromiso en Android documento antes de seguir a esta guía.

##<a name="standard-integration"></a>Integración estándar

El SDK de llegar a requiere la **biblioteca de compatibilidad Android (v4)**.

La manera más rápida de agregar la biblioteca a su proyecto en **Eclipse** es `Right click on your project -> Android Tools -> Add Support Library...`.

Si no usa Eclipse, puede leer las instrucciones [aquí].

Copiar archivos de recursos de alcance del SDK en su proyecto:

-   Copie los archivos de la `res/layout` carpeta entregado con el SDK en el `res/layout` carpeta de la aplicación.
-   Copie los archivos de la `res/drawable` carpeta entregado con el SDK en el `res/drawable` carpeta de la aplicación.

Editar su `AndroidManifest.xml` archivo:

-   Agregue la siguiente sección (entre el `<application>` y `</application>` etiquetas):

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
              <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
              </intent-filter>
            </receiver>

-   Necesita este permiso para reproducir las notificaciones del sistema que se ha hecho clic en el inicio (en caso contrario, se guardará en disco, pero no se muestra ya, realmente debe incluir esto).

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   Especificar un icono que se usa para las notificaciones (tanto en la aplicación y el sistema los), copiando y editando la sección siguiente (entre el `<application>` y `</application>` etiquetas):

            <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT] Esta sección es **obligatorio** si va a usar las notificaciones del sistema al crear campañas de alcance. Android impide que las notificaciones del sistema sin iconos que se muestran. Así que si se omite esta sección, los usuarios finales no podrán recibirlos.

-   Si crear campañas con las notificaciones del sistema con el panorama general, debe agregar los siguientes permisos (después de la `</application>` etiqueta) si faltan:

            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
            <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

  -   En M Android y si la aplicación dirige a nivel de la API Android 23 o mayor, ``WRITE_EXTERNAL_STORAGE`` permiso requiere la aprobación del usuario. Lea [esta sección](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

-   Para las notificaciones de sistema también puede especificar la campaña alcance si el dispositivo debe llamar o vibración. Para que funcione, debe asegurarse de que declara el permiso siguiente (después de la `</application>` etiqueta):

            <uses-permission android:name="android.permission.VIBRATE" />

    Sin este permiso, Android impide que las notificaciones del sistema que se muestra si seleccionó la llamada o la opción vibrate en el Administrador de la campaña de llegar a.

-   Si genera una aplicación mediante **ProGuard** y tiene errores relacionados con la biblioteca de compatibilidad Android o jar de contratación, agregue las siguientes líneas a su `proguard.cfg` archivo:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

## <a name="native-push"></a>Inserción nativo

Ahora que ha configurado el módulo de alcance, necesita configurar la inserción nativo para poder recibir las campañas en el dispositivo.

Se admiten dos servicios en Android:

  - Dispositivos de Google Play: usar la [Mensajería de nube de Google] siguiendo la Guía de [cómo integrar GCM con la Guía de contratación](mobile-engagement-android-gcm-integrate.md) .
  - Dispositivos de Amazon: usar la [Mensajería de dispositivo Amazon] siguiendo la Guía de [cómo integrar ADM con la Guía de contratación](mobile-engagement-android-adm-integrate.md) .

Si desea dispositivos de Amazon y Google Play, es posible tener todo en 1 AndroidManifest.xml/APK para el desarrollo de destino. Pero, al enviar a Amazon, puede rechazar la aplicación si encuentra el código GCM.

Debe usar varios APKs en ese caso.

**La aplicación está preparada para recibir y mostrar las campañas de alcance!**

##<a name="how-to-handle-data-push"></a>Cómo controlar la inserción de datos

### <a name="integration"></a>Integración

Si desea que la aplicación para que pueda recibir alcance Push de datos, debe crear una clase subcarpetas de `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` y hacer referencia a él en el `AndroidManifest.xml` archivo (entre el `<application>` o `</application>` etiquetas):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

A continuación, puede reemplazar el `onDataPushStringReceived` y `onDataPushBase64Received` devoluciones de llamada. Aquí tenemos un ejemplo:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Categoría

El parámetro de categoría es opcional al crear una campaña de inserción de datos y permite filtrar los datos que inserta. Esto es útil si tiene varios receptores difusión tratamiento diferentes tipos de datos Push, o si desea insertar diferentes tipos de `Base64` datos y desea identificar su tipo antes de analizar ellos.

### <a name="callbacks-return-parameter"></a>Parámetro de devolución de devoluciones de llamada

Estas son algunas reglas para controlar correctamente el parámetro de devolución `onDataPushStringReceived` y `onDataPushBase64Received`:

-   Debe devolver un receptor difusión `null` en la devolución de llamada si no sabe cómo manejar insertar datos. Debe usar la categoría para determinar si el receptor difusión debe controlar la inserción de datos o no.
-   Uno de la difusión receptor debe devolver `true` en la devolución de llamada si acepta la inserción de datos.
-   Uno de la difusión receptor debe devolver `false` en la devolución de llamada si reconoce la inserción de datos, pero descarta por cualquier razón. Por ejemplo, devolver `false` cuando los datos recibidos no son válidos.
-   Si una difusión receptor devuelve `true` mientras otro uno devuelve `false` para la misma inserción de datos, el comportamiento está definido, nunca debe hacerlo.

El tipo devuelto se usa solamente para las estadísticas de alcance:

-   `Replied`se incrementa si uno de los receptores difusión devuelve `true` o `false`.
-   `Actioned`se incrementa sólo si uno de los receptores difusión devuelto `true`.

##<a name="how-to-customize-campaigns"></a>Cómo personalizar campañas

Para personalizar las campañas, puede modificar los diseños que se proporcionan en el SDK de llegar a.

Debe conservar todos los identificadores utilizados en los diseños y mantener los tipos de las vistas que utilizan un identificador, especialmente para vistas de texto y vistas de la imagen. Algunas vistas solo se usan para mostrar u ocultar áreas para que se puede cambiar su tipo. Compruebe el código fuente si va a cambiar el tipo de una vista en los diseños de proporcionado.

### <a name="notifications"></a>Notificaciones

Existen dos tipos de notificaciones: las notificaciones de sistema y de la aplicación que usar archivos de diseño distinto.

#### <a name="system-notifications"></a>Notificaciones del sistema

Para personalizar las notificaciones del sistema que debe usar las **categorías**. Puede ir a las [categorías](#categories).

#### <a name="in-app-notifications"></a>Notificaciones de la aplicación

De forma predeterminada, una notificación de la aplicación es una vista que se agrega dinámicamente a la interfaz de usuario de actividad actual gracias al método Android `addContentView()`. Esto se denomina una superposición de notificación. Las superposiciones de notificación son una buena opción para una rápida integración porque no requieren modificar cualquier diseño de la aplicación.

Para modificar el aspecto de las superposiciones de la notificación, simplemente puede modificar el archivo `engagement_notification_area.xml` a sus necesidades.

> [AZURE.NOTE] El archivo `engagement_notification_overlay.xml` es la que se usa para crear una superposición de notificación, incluye el archivo `engagement_notification_area.xml`. También puede personalizar para adaptarla a sus necesidades (por ejemplo, posicionamiento del área de notificación en la superposición).

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Incluir diseño notificación como parte de un diseño de actividad

Superposiciones son una buena opción para una rápida integración pero pueden ser conveniente o tener efectos secundarios en casos especiales. Puede personalizar el sistema superpuesto en un nivel de actividad, lo que posibilita evitar efectos secundarios para actividades especiales.

Puede decidir incluir nuestro diseño de notificación en el diseño existente gracias a la declaración de Android **incluir** . El siguiente es un ejemplo de un modificado `ListActivity` diseño que contiene solo una `ListView`.

**Antes de la integración de compromiso:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Después de integración de compromiso:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

En este ejemplo, hemos agregado un contenedor principal porque el diseño original usa una vista de lista como el elemento de nivel superior. También se ha agregado `android:layout_weight="1"` para que pueda agregar una vista de una vista de lista configurada con `android:layout_height="fill_parent"`.

El SDK de alcance de contratación detecta automáticamente que el diseño de la notificación se incluye en la actividad y no agregará una superposición de esta actividad.

> [AZURE.TIP] Si utiliza un ListActivity en la aplicación, una superposición de alcance visible le impedirá solucionando ya hizo clic en elementos de la vista de lista. Se trata de un problema conocido. Para evitar este problema, le recomendamos que para insertar el diseño de la notificación en su propio diseño de actividad de la lista como, en el ejemplo anterior.

##### <a name="disabling-application-notification-per-activity"></a>Deshabilitar la notificación de aplicación por actividad

Si no desea que la superposición se agregue a su actividad, y si no incluye el diseño de la notificación en su propio diseño, puede deshabilitar la superposición de esta actividad en la `AndroidManifest.xml` agregando una `meta-data` sección como en el ejemplo siguiente:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a>Categorías

Cuando se modifican los diseños proporcionados, modifique el aspecto de todas las notificaciones. Las categorías permiten definir varios aspectos destinadas (posiblemente comportamientos) para las notificaciones. Una categoría puede especificarse cuando se crea una campaña de alcance. Tenga en cuenta que categorías también le permiten personalizar anuncios y sondeos, que se describe más adelante en este documento.

Para registrar un controlador de categoría para las notificaciones, debe agregar una llamada cuando la aplicación está inicializada.

> [AZURE.IMPORTANT] Lea la advertencia sobre el atributo de android: proceso \<android sdk compromiso proceso\> en cómo integrar compromiso en Android tema antes de continuar.

En el ejemplo siguiente se supone confirmada la advertencia anterior y utilizar una clase subcarpetas de `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

La `MyNotifier` objeto es la implementación del controlador de categoría de notificación. Está en una implementación de la `EngagementNotifier` interfaz o una clase secundaria de la implementación predeterminada: `EngagementDefaultNotifier`.

Tenga en cuenta que el mismo notificador puede controlar varias categorías, puede registrar similar a esta:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Para reemplazar la implementación de la categoría predeterminada, puede registrar su implementación como en el ejemplo siguiente:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

La categoría actual que se utiliza en un controlador se pasa como un parámetro en la mayoría de los métodos se pueden anular en `EngagementDefaultNotifier`.

Se pasa como un `String` parámetro o indirectamente en un `EngagementReachContent` objeto que tiene un `getCategory()` método.

Puede cambiar la mayoría del proceso de creación de notificación volviendo a definir métodos en `EngagementDefaultNotifier`, para la personalización avanzada más no dude echar un vistazo a la documentación técnica y el código fuente.

##### <a name="in-app-notifications"></a>Notificaciones de la aplicación

Si desea utilizar diseños alternativos para una categoría específica, puede implementar como en el ejemplo siguiente:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Ejemplo de `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Como puede ver, el identificador de la vista superpuesta es diferente del estándar. Es importante que cada diseño de utiliza un identificador único para superposiciones.

**Ejemplo de `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Como puede ver, el identificador de la vista del área de notificación es diferente de la estándar. Es importante que cada diseño utiliza un identificador único para áreas de notificación.

Este sencillo ejemplo de categoría hace notificaciones de aplicación (o de la aplicación) aparece en la parte superior de la pantalla. No se ha cambiado los identificadores estándares utilizados en el área de notificación.

Si desea cambiar esto, debe volver a definir el `EngagementDefaultNotifier.prepareInAppArea` método. Es recomendable para buscar en la documentación técnica y en el código fuente de `EngagementNotifier` y `EngagementDefaultNotifier` si desea que este nivel de personalización avanzada.

##### <a name="system-notifications"></a>Notificaciones del sistema

Ampliar `EngagementDefaultNotifier`, puede reemplazar `onNotificationPrepared` alterar la notificación que se ha preparado la implementación predeterminada.

Por ejemplo:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

En este ejemplo se hace una notificación del sistema para un contenido que se muestra como un evento en curso cuando se usa la categoría "en curso".

Si desea crear la `Notification` objeto desde cero, puede devolver `false` a la llamada y el método `notify` usted mismo en el `NotificationManager`. En ese caso es importante mantener un `contentIntent`, un `deleteIntent` y el identificador de notificación utilizado por `EngagementReachReceiver`.

Este es un ejemplo correcto de una implementación de este tipo:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Anuncios solo de notificación

La administración de la haga clic en una notificación anuncio solo puede personalizarse reemplazando `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` para modificar el preparado `Intent`. Con este método le permite ajustar los indicadores fácilmente.

Por ejemplo, para agregar la `SINGLE_TOP` marca:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Para usuarios de contratación heredados, tenga en cuenta que las notificaciones del sistema sin acción URL ahora inicia la aplicación si estaba en segundo plano, por lo que puede llamar a este método con un anuncio sin dirección URL de la acción. Debe tener en cuenta al personalizar el objetivo.

También puede implementar `EngagementNotifier.executeNotifAnnouncementAction` desde cero.

##### <a name="notification-life-cycle"></a>Ciclo de vida de notificación

Cuando utiliza la categoría de forma predeterminada, se denominan algunos métodos de ciclo de vida en el `EngagementReachInteractiveContent` a objeto estadísticas de informe y actualizar el estado de la campaña:

-   Cuando la notificación se muestra en la aplicación o se coloca en la barra de estado, el `displayNotification` método se denomina (que informa estadísticas) por `EngagementReachAgent` si `handleNotification` devuelve `true`.
-   Si se descarta la notificación, la `exitNotification` se denomina método, se notifica estadístico y ahora se pueden procesar campañas siguientes.
-   Si se hace clic en la notificación, `actionNotification` es llama, se notifica estadístico y se inicia la intención asociada.

Si su implementación de `EngagementNotifier` que omita el comportamiento predeterminado, debe llamar a estos métodos de ciclo de vida por usted mismo. Los siguientes ejemplos muestran algunos casos donde se omite el comportamiento predeterminado:

-   No extender `EngagementDefaultNotifier`, por ejemplo, ¿ha implementado tratamiento de categoría desde cero.
-   Para las notificaciones de sistema, invalidar el `onNotificationPrepared` y modificado `contentIntent` o `deleteIntent` en la `Notification` objeto.
-   Para las notificaciones de la aplicación, invalidar `prepareInAppArea`, asegúrese de asignar al menos `actionNotification` a uno de sus I.U controles.

> [AZURE.NOTE] Si `handleNotification` inicia una excepción, el contenido se elimina y `dropContent` se denomina. Esto se notifica en estadísticas y ahora se pueden procesar campañas siguientes.

### <a name="announcements-and-polls"></a>Anuncios y sondeos

#### <a name="layouts"></a>Diseños

Puede modificar el `engagement_text_announcement.xml`, `engagement_web_announcement.xml` y `engagement_poll.xml` archivos para personalizar los anuncios de texto, anuncios web y sondeos.

Estos archivos compartan dos diseños comunes para el área de título y el área del botón. El diseño del título es `engagement_content_title.xml` y usa el archivo puede dibujar epónimo para el fondo. El diseño de los botones de acción y salida es `engagement_button_bar.xml` y usa el archivo puede dibujar epónimo para el fondo.

En una encuesta, el diseño de la pregunta y sus opciones son dinámicamente aumenta con varias veces el `engagement_question.xml` archivo de diseño de las preguntas y la `engagement_choice.xml` archivo para las opciones.

#### <a name="categories"></a>Categorías

##### <a name="alternate-layouts"></a>Diseños alternativos

Como las notificaciones, categoría de la campaña puede usarse para diseños alternativos para los anuncios y sondeos.

Por ejemplo, para crear una categoría para un anuncio de texto, puede ampliar `EngagementTextAnnouncementActivity` y hacer referencia a él los `AndroidManifest.xml` archivo:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Tenga en cuenta que la categoría en la intención de filtro se utiliza para marcar la diferencia con la actividad de anuncio de forma predeterminada.

El SDK de llegar a utiliza el sistema intención resolver la actividad de la derecha de una categoría específica y pasa en la categoría predeterminada si la resolución del error.

Tiene que implementar `MyCustomTextAnnouncementActivity`, si solo desea cambiar el diseño (pero mantener los mismos identificadores de vista), tiene que definir la clase como en el ejemplo siguiente:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Para reemplazar la categoría predeterminada de los anuncios de texto, simplemente reemplazar `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` por su implementación.

Sondeos y anuncios de web se pueden personalizar de manera similar.

Anuncios de web se puede ampliar `EngagementWebAnnouncementActivity` y declare la actividad de la `AndroidManifest.xml` igual que en el ejemplo siguiente:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Sondeos se puede ampliar `EngagementPollActivity` y declarar su en la `AndroidManifest.xml` igual que en el ejemplo siguiente:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementación desde cero

Puede implementar categorías para sus actividades de anuncio (y sondeo) sin extender uno de los `Engagement*Activity` clases proporcionadas por el SDK de alcance. Esto es útil por ejemplo si desea definir un diseño que no utilice las mismas vistas como los diseños estándar.

Al igual que para la personalización avanzada de notificación, se recomienda para buscar el código fuente de la implementación estándar.

Estas son algunas cosas que debe tener en cuenta: alcance iniciará la actividad con un color específico (correspondiente a la intención filtro) además de un parámetro adicional que es el identificador de contenido.

Para recuperar el objeto de contenido que contienen los campos que especificó al crear la campaña en el sitio web puede hacerlo:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Estadísticas, debe informar el contenido se muestra en la `onResume` evento:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

A continuación, no olvide llamar a ninguno `actionContent(this)` o `exitContent(this)` en el objeto de contenido antes de la actividad pasa a fondo.

Si no se llama cualquiera `actionContent` o `exitContent`, estadísticas no irá (es decir, sin análisis en la campaña) y lo más importante, las campañas siguientes no se notificará hasta que se reinicia el proceso de aplicación.

Orientación u otros cambios de configuración pueden hacer que el código difícil de determinar si la actividad entra en segundo plano o no, la implementación estándar asegura de que el contenido se notifica como terminado si el usuario deja la actividad (ya sea presionando `HOME` o `BACK`), pero no si cambia la orientación.

Aquí es la parte interesante de la implementación:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Como puede ver, si llama `actionContent(this)` , a continuación, finalice la actividad `exitContent(this)` puede llamar de forma segura sin tener ningún efecto.

[aquí]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Mensajería de nube de Google]:http://developer.android.com/guide/google/gcm/index.html
[Mensajería de dispositivo de Amazon]:https://developer.amazon.com/sdk/adm.html
