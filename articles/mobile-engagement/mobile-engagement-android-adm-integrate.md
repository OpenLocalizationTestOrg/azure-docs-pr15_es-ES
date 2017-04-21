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


#<a name="how-to-integrate-adm-with-engagement"></a>Cómo integrar ADM con compromiso

> [AZURE.IMPORTANT] Debe seguir el procedimiento de integración descrito en cómo integrar compromiso en Android documento antes de seguir a esta guía.
>
> Este documento solo es útil si ya integrado el módulo de alcance y un plan de inserción dispositivos de Amazon. Para integrar las campañas de alcance de la aplicación, lea primero cómo integrar compromiso llegar en Android.

##<a name="introduction"></a>Introducción

Integración de ADM permite la aplicación enviarse cuando el destino dispositivos Android de Amazon.

Cargas ADM insertadas siempre en el SDK contienen el `azme` clave en el objeto de datos. Por lo tanto si utiliza ADM para otro propósito en la aplicación, puede filtrar Push basándose en esa clave.

> [AZURE.IMPORTANT] Solo Amazon Kindle dispositivos que ejecutan 4.0.3 Android o superior son compatibles con mensajería de dispositivo Amazon; Sin embargo, puede integrar este código de forma segura en otros dispositivos.

##<a name="sign-up-to-adm"></a>Suscribirse a ADM

Si no lo ha hecho, debe habilitar ADM en su cuenta de Amazon.

El procedimiento se detalla en: [<https://developer.amazon.com/sdk/adm/credentials.html>].

Al finalizar el procedimiento, obtendrá:

-   Credenciales de OAuth (un identificador de cliente y un secreto de cliente) para contratación puedan push sus dispositivos.
-   Una clave de API que se debe integrar en la aplicación.

##<a name="sdk-integration"></a>Integración de SDK

### <a name="managing-device-registrations"></a>Administración de registros de dispositivo

Cada dispositivo debe enviar un comando de registro a los servidores ADM, en caso contrario, no se puede llegar.

Si ya usa la [biblioteca de cliente ADM]y ya tiene [integrado ADM] puede ir directamente a recibir una adm de sdk android.

Si aún no ha integrado ADM, contratación tiene una forma más sencilla de habilitar en la aplicación:

Editar su `AndroidManifest.xml` archivo:

-   Agregar el espacio de nombres de Amazon, el archivo debe comenzar similar a esta:

        <?xml version="1.0" encoding="utf-8"?>
        <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   Dentro de la `<application/>` , agregue esta sección:

        <amazon:enable-feature
           android:name="com.amazon.device.messaging"
           android:required="false"/>

        <meta-data android:name="engagement:adm:register" android:value="true" />

-   Después de agregar la etiqueta de amazon, puede que tenga un error de generación de si el destino de compilación de proyecto está por debajo de 2.1 Android. Debe usar un destino de generación de **Android 2.1 +** (no se preocupe, aún puede tener un `minSdkVersion` establecido en 4).
-   Integrar la clave de API ADM como activo por [este procedimiento].

A continuación, siga las instrucciones de las siguientes secciones.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicar el identificador de registro del servicio de notificaciones de contratación y recibir notificaciones

Para comunicar el identificador de registro del dispositivo al servicio de notificaciones de contratación y recibir sus notificaciones, agregue lo siguiente a su `AndroidManifest.xml` de archivo, en la `<application/>` etiquetar (incluso si usa ADM sin compromiso):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Asegúrese de que tiene los siguientes permisos en el `AndroidManifest.xml` (antes de la `</application>` etiqueta).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##<a name="grant-engagement-oauth-credentials"></a>Conceder compromiso OAuth credenciales

Enviar sus credenciales de OAuth (identificador de cliente y secreto de cliente) en el Portal de contratación.

[< https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[Biblioteca de cliente ADM]:https://developer.amazon.com/sdk/adm/setup.html
[ADM integrado]:https://developer.amazon.com/sdk/adm/integrating-app.html
[Este procedimiento]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
