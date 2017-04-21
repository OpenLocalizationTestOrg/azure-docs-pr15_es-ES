<properties
    pageTitle="Configuración avanzada de Azure Mobile compromiso Android SDK"
    description="Describe las opciones de configuración avanzada incluidos los manifiestos Android con Android SDK de Azure Mobile compromiso"
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
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Configuración avanzada de Azure Mobile compromiso Android SDK

> [AZURE.SELECTOR]
- [Windows universal](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Este procedimiento describe cómo configurar diversas opciones de configuración para las aplicaciones de Azure Mobile compromiso Android.

## <a name="prerequisites"></a>Requisitos previos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Requisitos de permisos
Algunas opciones requieren permisos específicos, que se muestran aquí para referencia y en línea en la función específica. Agregar estos permisos a los AndroidManifest.xml del proyecto inmediatamente antes o después de la `<application>` etiqueta.

El código de permiso debe el siguiente aspecto, donde se rellena el permiso adecuado de la tabla siguiente.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Permisos | Cuando se utiliza |
| ---------- | --------- |
| INTERNET | Obligatorio. Para los informes básicos |
| ACCESS_NETWORK_STATE | Obligatorio. Para los informes básicos |
| RECEIVE_BOOT_COMPLETED | Obligatorio. Para mostrar el centro de notificaciones después de reiniciar el dispositivo |
| WAKE_LOCK | Se recomienda. Permite recopilar datos cuando se utiliza Wi-Fi o cuando la pantalla está desactivada |
| VIBRACIÓN | Opcional. Permite vibración cuando se reciben notificaciones |
| DOWNLOAD_WITHOUT_NOTIFICATION | Opcional. Habilita la notificación de panorama Android |
| WRITE_EXTERNAL_STORAGE | Opcional. Habilita la notificación de panorama Android |
| ACCESS_COARSE_LOCATION | Opcional. Permite la generación de informes de ubicación en tiempo real |
| ACCESS_FINE_LOCATION | Opcional. Permite la generación de informes de ubicación basada en GPS |

Comenzando con Android M, [algunos permisos se administran en tiempo de ejecución](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Si ya está usando ``ACCESS_FINE_LOCATION``, no necesitará también usar ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Opciones de configuración de manifiestos Android

### <a name="crash-report"></a>Informe de errores

Para deshabilitar los informes de bloqueo, agregue este código entre el `<application>` y `</application>` etiquetas:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Umbral de ráfaga

De forma predeterminada, los informes de servicio de contratación registros en tiempo real. Si los registros de aplicación informe varían con frecuencia, es mejor para los registros del búfer y para informar de ellas a la vez en una base de tiempo regular (denominada "modo de ráfaga"). Para ello, agregue este código entre el `<application>` y `</application>` etiquetas:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Modo de ráfaga ligeramente aumenta la batería, pero tiene un gran impacto en el Monitor de compromiso: todas las sesiones y trabajos la duración se redondean el umbral de ráfaga (por lo tanto, sesiones y trabajos menos tiempo que el umbral de ráfaga puede no ser visible). El umbral de ráfaga debe tener no más de 30000 (30s).

### <a name="session-timeout"></a>Tiempo de espera de sesión

 Puede finalizar una actividad presionando la tecla **Inicio** o **hacia atrás** , estableciendo el teléfono inactivo o saltar a otra aplicación. De forma predeterminada, una sesión se finaliza diez segundos después del final de su última actividad. Esto evita una división de sesión cada vez que el usuario sale y vuelve a la aplicación rápidamente, que puede ocurrir cuando el usuario selecciona una imagen, comprueba si hay una notificación, etcetera. Desea modificar este parámetro. Para ello, agregue este código entre el `<application>` y `</application>` etiquetas:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Deshabilitar los informes de registro

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
-   La `engagement:agent:settings:mode` clave se usa para definir el modo del archivo compartido preferencias. Usar el mismo modo como en su `PreferenceActivity`. El modo se debe pasar como un número: si está utilizando una combinación de indicadores constantes en el código, compruebe el valor total.

Compromiso siempre utiliza la `engagement:key` clave booleana en el archivo de preferencias para administrar esta configuración.

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
