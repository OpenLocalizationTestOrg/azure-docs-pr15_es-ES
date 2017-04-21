<properties
    pageTitle="Integración de Azure compromiso móviles Android SDK"
    description="Últimas actualizaciones y procedimientos para Android SDK para Azure Mobile contratación"
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
    ms.date="10/10/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-gcm-with-mobile-engagement"></a>Cómo integrar GCM con compromiso móvil

> [AZURE.IMPORTANT] Debe seguir el procedimiento de integración descrito en cómo integrar compromiso en Android documento antes de seguir a esta guía.
>
> Este documento solo es útil si ya integrado el módulo de alcance y un plan de Google Play dispositivos de inserción. Para integrar las campañas de alcance de la aplicación, lea primero cómo integrar compromiso llegar en Android.

##<a name="introduction"></a>Introducción

Integración de GCM permite a la aplicación que se pueden insertar.

Cargas GCM insertadas siempre en el SDK contienen el `azme` clave en el objeto de datos. Por lo tanto si utiliza GCM para otro propósito en la aplicación, puede filtrar Push basándose en esa clave.

> [AZURE.IMPORTANT] Sólo los dispositivos que ejecutan Android 2.2 o superior, tener Google Play instalado y teniendo Google conexión de fondo habilitada se puede insertar mediante GCM; Sin embargo, puede integrar este código de forma segura en dispositivos no admitidos (simplemente utiliza color).

##<a name="create-a-google-cloud-messaging-project-with-api-key"></a>Crear un proyecto de mensajería de nube de Google con clave API

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

##<a name="sdk-integration"></a>Integración de SDK

### <a name="managing-device-registrations"></a>Administración de registros de dispositivo

Cada dispositivo debe enviar un comando de registro a los servidores de Google, en caso contrario, no se puede llegar.

También puede anular el registro de un dispositivo de notificaciones de GCM (el dispositivo se elimina automáticamente si se desinstala la aplicación).

Si no usa [Google Play SDK] o que ya no envíe la intención de registro, puede hacer compromiso registrar el dispositivo automáticamente para usted.

Para habilitar esta opción, agregue lo siguiente a su `AndroidManifest.xml` de archivo, en la `<application/>` etiqueta:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicar el identificador de registro del servicio de notificaciones de contratación y recibir notificaciones

Para comunicar el identificador de registro del dispositivo al servicio de notificaciones de contratación y recibir sus notificaciones, agregue lo siguiente a su `AndroidManifest.xml` de archivo, en la `<application/>` etiqueta (aunque administrar registros de dispositivo):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Asegúrese de que tiene los siguientes permisos en el `AndroidManifest.xml` (después de la `</application>` etiqueta).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##<a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Conceder acceso de contratación móvil a la clave de API GCM

Siga [esta guía](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) para conceder acceso de contratación móvil a la clave de la API de GCM.

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
