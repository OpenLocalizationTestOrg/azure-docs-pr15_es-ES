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


#<a name="upgrade-procedures"></a>Procedimientos de actualización

Si ya ha integrado una versión anterior de nuestro SDK en la aplicación, debe tener en cuenta los siguientes puntos al actualizar el SDK.

Deberá seguir diversos procedimientos si hay varias versiones del SDK. Por ejemplo, si se migra de 1.4.0 a 1.6.0 que debe primero siga el procedimiento "de 1.4.0 a 1.5.0", a continuación, el procedimiento "de 1.5.0 a 1.6.0".

Independientemente de la versión actualizar desde, debe reemplazar la `mobile-engagement-VERSION.jar` con uno nuevo.

##<a name="from-420-to-421"></a>Desde 4.2.0 a 4.2.1

En realidad se puede realizar este paso de cualquier versión de SDK, es una mejora de seguridad al integrar alcance actividades.

Ahora debe agregar `exported="false"` a todas las actividades de alcance.

Actividades de alcance ahora tendrá este aspecto su `AndroidManifest.xml`:

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

##<a name="from-400-to-410"></a>Desde 4.0.0 a 4.1.0

El SDK ahora controlador nuevo permiso modelo de M Android.

Si utiliza características de ubicación o notificaciones panorama general, lea [esta sección](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Además del nuevo modelo de permisos, ahora se admiten las características de ubicación configuración en tiempo de ejecución.
Estamos siendo compatibles con los parámetros de manifiestos de ubicación, pero ya no se utiliza. Para usar la configuración de tiempo de ejecución, quite las siguientes secciones de su ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

y lea [este procedimiento actualizada](mobile-engagement-android-integrate-engagement.md#location-reporting) para usar la configuración de tiempo de ejecución en su lugar.

##<a name="from-300-to-400"></a>Desde 3.0.0 a 4.0.0

### <a name="native-push"></a>Inserción nativo

Inserción nativa (GCM/ADM) ahora también se usa para en notificaciones de la aplicación por lo que debe configurar las credenciales de inserción nativa para cualquier tipo de campaña de inserción.

Si aún no siga [este procedimiento](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Integración de alcance se ha modificado en ``AndroidManifest.xml``.

Reemplazar este:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Por

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
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

Hay posiblemente una pantalla de carga ahora al hacer clic en un anuncio (con texto y contenido web) o un sondeo.
Tiene que agregar este para las campañas trabajar en 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Recursos

Para insertar la nueva `res/layout/engagement_loading.xml` archivo en su proyecto.

##<a name="from-240-to-300"></a>Desde 2.4.0 a 3.0.0

La siguiente describe cómo migrar una integración SDK desde el servicio de Capptain ofrecido por Capptain SAS a una aplicación con tecnología de Azure Mobile compromiso. Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a 2.4.0 en primer lugar y, a continuación, aplique el procedimiento siguiente.

>[AZURE.IMPORTANT] Capptain y Mobile compromiso no son los mismos servicios y el procedimiento siguiente sólo resalta cómo migrar la aplicación de cliente. Migrar el SDK en la aplicación no se migrarán los datos de los servidores de Capptain a los servidores de compromiso de móvil.

### <a name="jar-file"></a>Archivo JAR

Reemplazar `capptain.jar` por `mobile-engagement-VERSION.jar` en su `libs` carpeta.

### <a name="resource-files"></a>Archivos de recursos

Todos los archivos de recursos proporcionamos (con el prefijo `capptain_`) se reemplazará por los nuevos (con el prefijo `engagement_`).

Si ha personalizado los archivos, debe volver a aplicar la personalización de los nuevos archivos, **todos los identificadores de los archivos de recursos también ha cambiado el nombre**.

### <a name="application-id"></a>Identificador de la aplicación

Ahora compromiso utiliza una cadena de conexión para configurar los identificadores SDK como el identificador de aplicación.

Debe usar `EngagementAgent.init` método en su actividad selector similar a esta:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el Portal de Azure.

Quite las llamadas a `CapptainAgent.configure` como `EngagementAgent.init` reemplaza ese método.

La `appId` ya no se puede configurar mediante `AndroidManifest.xml`.

Quite esta sección desde el `AndroidManifest.xml` si lo tiene:

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>API de Java

Todas las llamadas a cualquier clase de Java de nuestro SDK tiene que puede cambiar el nombre; Por ejemplo, `CapptainAgent.getInstance(this)` debe cambiarse `EngagementAgent.getInstance(this)`, `extends CapptainActivity` debe cambiarse `extends EngagementActivity` etcetera...

Si se han integrado con archivos de preferencias de agente de forma predeterminada, el nombre de archivo predeterminado es ahora `engagement.agent` y la clave es `engagement:agent`.

Al crear anuncios de web, el cuaderno Javascript está ahora `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml

Ha ocurrido una gran cantidad de cambios, el servicio no se comparte ya, y una gran cantidad de receptores no puede exportar ya.

La declaración de servicio ahora es más sencilla; Quite el filtro por intención y todos los metadatos dentro de ella y agregue `exportable=false`.

Además de todo lo que se cambia el nombre para usar compromiso.

Ahora aparece como:

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

Cuando desee habilitar registros de prueba, los metadatos ahora se ha movido a la etiqueta de la aplicación y ha cambiado de nombre:

            <application>
            
              <meta-data android:name="engagement:log:test" android:value="true" />
            
              <service/>
            
            </application>

Todos los otros metadatos solo ha cambiado el nombre, aquí encontrará la lista completa (de cambiar el nombre del curso sólo los que usa):

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
            
            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play y SmartAd seguimiento se ha quitado de SDK debe quitar esto sin reemplazo:

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

Las actividades de alcance ahora se declaran así:

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            
Si tiene actividades personalizadas de alcance, sólo debe cambiar la intención acciones para que coincida con uno `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` o `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Ha cambiado el nombre los receptores de difusión, además de agregamos ahora `exported=false`. Aquí es la lista completa de los receptores con la nueva especificación (de cambiar el nombre del curso sólo los que usa):

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Se ha quitado la receptor de seguimiento, para que tenga que quitar esta sección:

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Tenga en cuenta que ha cambiado la declaración de la implementación del receptor difusión **EngagementMessageReceiver** en la `AndroidManifest.xml`. Esto es porque se han quitado la API para enviar y eliminar mensajes XMPP arbitrarios de entidades XMPP arbitrarias y la API para enviar y recibir mensajes entre dispositivos. Por lo tanto, tiene también eliminar las siguientes devoluciones de llamada de la implementación de **EngagementMessageReceiver** :

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

y

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

a continuación, elimine cualquier llamada en **EngagementAgent** para:

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

y

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard

Configuración de proguard puede verse afectado por reconfiguración de marca, ahora están viendo las reglas como:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }
            
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }
 
