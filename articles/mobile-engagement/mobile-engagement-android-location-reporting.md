<properties
    pageTitle="Ubicación de informes para SDK de Azure compromiso móvil Android"
    description="Describe cómo configurar informes de Azure Mobile compromiso Android SDK de ubicación"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Ubicación de informes para SDK de Azure compromiso móvil Android

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

Este tema describe cómo llevar a cabo la ubicación de generación de informes para la aplicación de Android.

## <a name="prerequisites"></a>Requisitos previos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Reporting de ubicación

Si desea ubicaciones para registrarse, debe agregar algunas líneas de configuración (entre el `<application>` y `</application>` etiquetas).

### <a name="lazy-area-location-reporting"></a>Reporting de ubicación de área diferida

Reporting de ubicación de área diferida permite informar el país y región ubicación asociados con dispositivos. Informes de la ubicación de este tipo solo usa ubicaciones de red (según el identificador de celda o Wi-Fi). El área del dispositivo se notifica al menos una vez por sesión. Nunca se utiliza el GPS y, por tanto, este tipo de informe de ubicación tiene bajo impacto en la batería.

Áreas notificadas se usan para calcular estadísticas geográficas acerca de los usuarios, sesiones, eventos y errores. También se puede utilizar como criterio en las campañas de alcance.

Habilitar la ubicación del área diferida informes mediante la configuración previamente mencionada en este procedimiento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

También debe especificar un permiso de ubicación. Este código utiliza ``COARSE`` permiso:

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Si su aplicación lo requiere, puede usar ``ACCESS_FINE_LOCATION`` en su lugar.

### <a name="real-time-location-reporting"></a>Informes de ubicación en tiempo real

Informes de ubicación en tiempo real permiten informes la latitud y longitud asociados con dispositivos. De forma predeterminada, este tipo de informes de ubicación sólo utiliza ubicaciones de red, según el identificador de la celda o Wi-Fi. Los informes solo esté activo cuando la aplicación se ejecuta en primer plano (por ejemplo, durante una sesión).

*No* usado para calcular estadísticas son ubicaciones en tiempo real. Su único propósito es permitir el uso de barrera de geo en tiempo real \<geofencing de audiencia de alcance\> criterio en las campañas de alcance.

Para habilitar los informes de ubicación en tiempo real, agregue una línea de código donde establecer la cadena de conexión de contratación en la actividad de selector. El resultado es similar a la siguiente:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>En función de GPS informes

De forma predeterminada, reporting de ubicación en tiempo real sólo utiliza ubicaciones de red. Para habilitar el uso de ubicaciones basado en GPS, que son mucho más preciso, utilice el objeto de configuración:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

También debe agregar el siguiente permiso si faltan:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Informes de fondo

De forma predeterminada, el informe de ubicación en tiempo real sólo está activo cuando la aplicación se ejecuta en primer plano (por ejemplo, durante una sesión). Para habilitar la notificación también en segundo plano, use este objeto de configuración:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Cuando la aplicación se ejecuta en segundo plano, se notifican sólo ubicaciones basada en la red, incluso si ha habilitado el GPS.

Si el usuario reinicia el dispositivo, se detiene el informe de ubicación de fondo. Para que sea reiniciar automáticamente durante el inicio, agregue este código.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

También debe agregar el siguiente permiso si faltan:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Permisos de M Android

A partir de M Android, algunos permisos se administran en tiempo de ejecución y necesitan la aprobación del usuario.

Si desea que el nivel de API Android 23, los permisos en tiempo de ejecución están desactivados de forma predeterminada para nuevas instalaciones de la aplicación. En caso contrario, se activan de forma predeterminada.

Puede habilitar o deshabilitar los permisos en el menú de configuración de dispositivo. La desactivación de permisos en el menú sistema elimina los procesos de fondo de la aplicación, que es un comportamiento del sistema y no tiene ningún efecto en la capacidad de recibir push en segundo plano.

En el contexto de la ubicación de contratación Mobile informes, los permisos que requieren aprobación en tiempo de ejecución son:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Solicitar permisos de usuario mediante un cuadro de diálogo estándar del sistema. Si el usuario aprueba, saber ``EngagementAgent`` para realizar ese cambio en cuenta en tiempo real. En caso contrario, el cambio se procesa la próxima vez que el usuario inicia la aplicación.

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
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
