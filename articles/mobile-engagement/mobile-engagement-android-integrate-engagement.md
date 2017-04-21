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

#<a name="how-to-integrate-engagement-on-android"></a>Cómo integrar compromiso en Android

> [AZURE.SELECTOR]
- [Universal de Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Este procedimiento describe la manera más sencilla de activar el análisis de contratación y la supervisión de funciones en la aplicación Android.

> [AZURE.IMPORTANT] El nivel mínimo de Android API SDK debe ser 10 o posterior (Android 2.3.3 o posterior).

Los pasos siguientes son suficientes para el informe de registros necesarios para calcular todas las estadísticas acerca de los usuarios, sesiones, actividades, se bloquea y Technicals se activa. El informe de registros necesarios para calcular otros datos estadísticos como eventos, errores y trabajos debe hacerse manualmente con la API de contratación (consulte [cómo usar el compromiso Mobile avanzadas etiquetado API en Android](mobile-engagement-android-use-engagement-api.md) ya que estas estadísticas depende de la aplicación.

##<a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Integrar el SDK de contratación y el servicio en su proyecto Android

Descargar el SDK Android desde [aquí](https://aka.ms/vq9mfn) Get `mobile-engagement-VERSION.jar` y colocarlas en la `libs` carpeta de su proyecto Android (crear la carpeta de bibliotecas si aún no existe).

> [AZURE.IMPORTANT]
> Si crea el paquete de aplicación con ProGuard, debe conservar algunas clases. Puede usar el siguiente fragmento de configuración:
>
>
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
            <methods>;
            }

Especifique la cadena de conexión de contratación llamando al método siguiente en la actividad de iniciador:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el Portal de Azure.

-   Si falta, agregue los siguientes permisos Android (antes de la `<application>` etiqueta):

            <uses-permission android:name="android.permission.INTERNET"/>
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Agregue la siguiente sección (entre el `<application>` y `</application>` etiquetas):

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

-   Cambiar `<Your application name>` por el nombre de la aplicación.

> [AZURE.TIP] La `android:label` atributo le permite elegir el nombre del servicio de contratación tal como aparecerá para los usuarios finales en la pantalla "Ejecutar servicios" de su teléfono. Se recomienda establecer este atributo en `"<Your application name>Service"` (por ejemplo, `"AcmeFunGameService"`).

Especificar el `android:process` atributo garantiza que el servicio de contratación se ejecutará en su propio proceso (ejecución compromiso en el mismo proceso como la aplicación le será el subproceso de la interfaz de usuario principal responde potencialmente menos).

> [AZURE.NOTE] Cualquier código que se colocan en `Application.onCreate()` y otras devoluciones de llamada de la aplicación se ejecutará para los procesos de toda la aplicación, incluido el servicio de contratación. Puede tener efectos secundarios no deseados (por ejemplo, las asignaciones de memoria innecesarias y subprocesos en proceso de contratación, receptores de difusión duplicados o servicios).

Si se reemplaza `Application.onCreate()`, es recomendable para agregar fragmento de código siguiente al principio de la `Application.onCreate()` función:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Puede hacer lo mismo para `Application.onTerminate()`, `Application.onLowMemory()` y `Application.onConfigurationChanged(...)`.

También puede ampliar `EngagementApplication` en lugar de ampliación `Application`: la devolución de llamada `Application.onCreate()` realiza la comprobación de proceso y llamadas `Application.onApplicationProcessCreate()` solo si el proceso actual no es el servicio de contratación de hospedaje, se aplican las mismas reglas de otras devoluciones de llamada.

##<a name="basic-reporting"></a>Informes básicos

### <a name="recommended-method-overload-your-activity-classes"></a>Método recomendado: sobrecargue su `Activity` clases

Para activar el informe de todos los registros necesarios para compromiso para calcular los usuarios, sesiones, actividades, se bloquea y las estadísticas técnicas, solo tiene que todas las su `*Activity` clases secundarias heredan de los correspondientes `Engagement*Activity` clases (por ejemplo, si se extiende la actividad heredada `ListActivity`, realizar extiende `EngagementListActivity`).

**Sin compromiso:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Con compromiso:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [AZURE.IMPORTANT] Cuando se usa `EngagementListActivity` o `EngagementExpandableListActivity`, asegúrese de que cualquier llamada a `requestWindowFeature(...);` se realiza antes de la llamada a `super.onCreate(...);`, en caso contrario, se producirá un error.

Puede encontrar estas clases en la `src` carpeta y puede copiarlos en su proyecto. Las clases también están en el **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Método alternativo: llamar `startActivity()` y `endActivity()` manualmente

Si no puede o no desea sobrecarga su `Activity` clases, en su lugar puede iniciar y terminar las actividades llamando `EngagementAgent`de métodos directamente.

> [AZURE.IMPORTANT] El SDK Android nunca llama la `endActivity()` método, incluso cuando se cierra la aplicación (en Android, aplicaciones realmente nunca se cierran). Por tanto, es *muy* recomendada llamar a la `startActivity()` método en la `onResume` devolución de llamada de *todos los* sus actividades y la `endActivity()` método en la `onPause()` devolución de llamada de *todos los* sus actividades. Esta es la única manera de asegurarse de que no se perderá sesiones. Si se pierde una sesión, el servicio de contratación nunca se desconecta del servidor de contratación (ya que el servicio permanece conectado como una sesión está pendiente).

Aquí tenemos un ejemplo:

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

En este ejemplo muy similar a la `EngagementActivity` clase y sus variantes, cuyo código fuente está disponible en la `src` carpeta.

##<a name="test"></a>Prueba

Ahora compruebe la integración, ejecute la aplicación móvil en un emulador o dispositivo y comprobar que se registra una sesión en la pestaña Monitor.

Las secciones siguientes son opcionales.

##<a name="location-reporting"></a>Reporting de ubicación

Si desea ubicaciones para registrarse, debe agregar algunas líneas de configuración (entre el `<application>` y `</application>` etiquetas).

### <a name="lazy-area-location-reporting"></a>Reporting de ubicación de área diferida

Reporting de ubicación de área diferida permite para informar del país, región y ubicación asociadas a los dispositivos. Informes de la ubicación de este tipo solo usa ubicaciones de red (según el identificador de celda o Wi-Fi). El área del dispositivo se notifica al menos una vez por sesión. Nunca se usa el GPS y, por tanto, este tipo de informe de ubicación tiene muy pocos (no quiere decir que no) influencia sobre la batería.

Áreas notificadas se usan para calcular estadísticas geográficas acerca de los usuarios, sesiones, eventos y errores. También se puede utilizar como criterio en las campañas de alcance.

Para habilitar el área diferida informe de ubicación, puede hacerlo mediante la configuración previamente mencionada en este procedimiento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

También debe agregar el siguiente permiso si faltan:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

O puede seguir usando ``ACCESS_FINE_LOCATION`` si ya se usa en la aplicación.

### <a name="real-time-location-reporting"></a>Ubicación informes en tiempo real

Informes en tiempo real ubicación permiten informar la latitud y longitud asociados a los dispositivos. De forma predeterminada, informes de la ubicación de este tipo solo usa ubicaciones de red (según el identificador de celda o Wi-Fi) y los informes solo esté activo cuando la aplicación se ejecuta en primer plano (es decir, durante una sesión).

*No* utilizada para calcular estadísticas son ubicaciones en tiempo real. Su único propósito es permitir el uso de tiempo real geo-barrera \<geofencing de audiencia de alcance\> criterio en las campañas de alcance.

Para habilitar la ubicación de tiempo real informes, puede hacer con la configuración previamente mencionada en este procedimiento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

También debe agregar el siguiente permiso si faltan:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

O puede seguir usando ``ACCESS_FINE_LOCATION`` si ya se usa en la aplicación.

#### <a name="gps-based-reporting"></a>En función de GPS informes

De forma predeterminada, solo ubicación informes en tiempo real utiliza ubicaciones de red. Para habilitar el uso de ubicaciones de GPS según (que son mucho más preciso), utilice el objeto de configuración:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

También debe agregar el siguiente permiso si faltan:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Informes de fondo

De forma predeterminada, el informes en tiempo real ubicación solo esté activa cuando la aplicación se ejecuta en primer plano (es decir, durante una sesión). Para habilitar la notificación también en segundo plano, utilice el objeto de configuración:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Cuando la aplicación se ejecuta en segundo plano, sólo ubicaciones basada en la red se notifican, incluso si ha habilitado el GPS.

El informe de ubicación de fondo se detendrá si el usuario reinicia su dispositivo, puede agregar este para que sea reiniciar automáticamente durante el inicio:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

También debe agregar el siguiente permiso si faltan:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Permisos de M Android

A partir de M Android, algunos permisos se administran en tiempo de ejecución y necesita la aprobación del usuario.

Los permisos en tiempo de ejecución se desactivará para nuevas instalaciones de la aplicación de forma predeterminada si desea que el nivel de API Android 23. En caso contrario se activará de forma predeterminada.

El usuario puede habilitar o deshabilitar los permisos en el menú de configuración de dispositivo. La desactivación de permisos en el menú de sistema elimina los procesos en segundo plano de la aplicación, se trata de un comportamiento del sistema y no tiene ningún efecto en la capacidad de recibir push en segundo plano.

En el contexto de contratación Mobile, los permisos que requieren aprobación en tiempo de ejecución son:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE`(solo cuando se dirige a nivel de API Android 23 para este)

El almacenamiento externo se usa solamente para la característica de alcance panorama general. Si encuentra pedir este permiso a problemas de los usuarios, puede quitarlo si utiliza solo para el compromiso de móvil, pero a costa de deshabilitar la característica de panorama general.

Las características de ubicación, debe solicitar permisos de usuario con un cuadro de diálogo estándar del sistema. Si el usuario aprueba, necesitará saber ``EngagementAgent`` para realizar ese cambio en cuenta en tiempo real (en caso contrario, el cambio se procesará la próxima vez que el usuario inicia la aplicación).

Este es un ejemplo de código para usar en una actividad de la aplicación para solicitar permisos y reenviar el resultado si es positivo para ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##<a name="advanced-reporting"></a>Informes avanzados

Opcionalmente, si desea informar eventos específicos de la aplicación, los errores y trabajos, debe usar la API de compromiso a través de los métodos de la `EngagementAgent` clase. Un objeto de esta clase podrá reconocer llamando el `EngagementAgent.getInstance()` método estático.

La API de contratación permite usar todas las capacidades avanzadas de contratación y se detallan cómo usar la API de contratación en Android (así como en la documentación técnica de la `EngagementAgent` clase).

##<a name="advanced-configuration-in-androidmanifestxml"></a>Configuración avanzada (en AndroidManifest.xml)

### <a name="wake-locks"></a>Activar bloqueos

Si desea asegurarse de que se envían las estadísticas en tiempo real al usar Wi-Fi o cuando la pantalla está desactivada, agregue el permiso opcional siguiente:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Informe de errores

Si desea deshabilitar los informes de bloqueo, agregar este (entre el `<application>` y `</application>` etiquetas):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Umbral de ráfaga

De forma predeterminada, los informes de servicio de contratación registros en tiempo real. Si su aplicación informes registros con frecuencia, es mejor para los registros del búfer y para informar de a la vez en una base de tiempo regular (Esto se denomina "modo de ráfaga"). Para ello, agregue este (entre el `<application>` y `</application>` etiquetas):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

El modo de ráfaga ligeramente aumentar la duración de la batería, pero tiene un gran impacto en el Monitor de compromiso: todas las sesiones y trabajos la duración se redondeará al umbral de ráfaga (por lo tanto, sesiones y trabajos menos tiempo que el umbral de ráfaga puede no ser visible). Se recomienda usar un umbral de ráfaga ya no que 30000 (30s).

### <a name="session-timeout"></a>Tiempo de espera de sesión

De forma predeterminada, una sesión es 10s finalizada después del final de su última actividad (que normalmente se produce al presionar la tecla Inicio o atrás, estableciendo inactivo el teléfono o saltar a otra aplicación). Esto es evitar una división de sesión cada vez que la salida de usuario y volver a la aplicación muy rápidamente (que puede ocurrir cuando seleccione una imagen, active una notificación, etcetera.). Desea modificar este parámetro. Para ello, agregue este (entre el `<application>` y `</application>` etiquetas):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

##<a name="disable-log-reporting"></a>Deshabilitar los informes de registro

### <a name="using-a-method-call"></a>Mediante una llamada de método

Si desea que el compromiso para dejar de enviar los registros, puede llamar:

            EngagementAgent.getInstance(context).setEnabled(false);

Esta llamada es persistente: usa un archivo compartido preferencias.

Si el compromiso esté activa cuando llame a esta función, puede tardar un minuto detener el servicio. Sin embargo no iniciar el servicio en absoluto la próxima vez que inicie la aplicación.

Puede habilitar el registro nuevo informe mediante una llamada a la misma función con `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integración en su propia`PreferenceActivity`

En lugar de llamar a esta función, también puede integrar esta configuración directamente en el existente `PreferenceActivity`.

Puede configurar compromiso para usar el archivo Preferencias (con el modo deseado) en el `AndroidManifest.xml` el archivo con `application meta-data`:

-   La `engagement:agent:settings:name` clave se utiliza para definir el nombre del archivo compartido preferencias.
-   La `engagement:agent:settings:mode` clave se usa para definir el modo del archivo compartido preferencias, debe usar el mismo modo como en su `PreferenceActivity`. El modo se debe pasar como un número: si está utilizando una combinación de indicadores constantes en el código, compruebe el valor total.

Usar siempre compromiso la `engagement:key` clave booleana en el archivo de preferencias para administrar esta configuración.

El siguiente ejemplo de `AndroidManifest.xml` muestra los valores predeterminados:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Puede agregar un `CheckBoxPreference` en el diseño de preferencia como la siguiente:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
