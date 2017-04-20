<properties
    pageTitle="Agregar las notificaciones de inserción Apache Cordova aplicación con aplicaciones móviles de Azure | Servicio de aplicaciones de Azure"
    description="Aprenda a usar aplicaciones de Azure Mobile enviar notificaciones de inserción a la aplicación Cordova Apache."
    services="app-service\mobile"
    documentationCenter="javascript"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Agregar las notificaciones de inserción a la aplicación de Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Información general

En este tutorial, agregue las notificaciones de inserción para el proyecto de [Inicio rápido de Apache Cordova] para que se envía una notificación de inserción al dispositivo cada vez que se inserta un registro.

Si no utiliza el proyecto de servidor de inicio rápido descargado, tendrá el paquete de extensión de notificación de inserción. Para obtener más información, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Requisitos previos

Este tutorial trata sobre una aplicación de Apache Cordova desarrollada con Visual Studio de 2015 que se ejecuta en el emulador de Google Android, un dispositivo Android, un dispositivo de Windows y un dispositivo iOS.

Para completar este tutorial, necesita:

* Un equipo con la [Comunidad de Visual Studio 2015] o versiones posteriores.
* [Visual Studio Tools para Cordova Apache].
* Una [cuenta de Azure active](https://azure.microsoft.com/pricing/free-trial/).
* Un proyecto completo de [Cordova Apache inicio rápido] .
* (Android) Una [cuenta de Google] con una dirección de correo electrónico superiores.
* (iOS) Pertenencia a un programa de desarrolladores de Apple y un dispositivo iOS (iOS Simulator no admite la inserción).
* (Windows) Una cuenta de Windows Store programador y un dispositivo de Windows 10.

##<a name="configure-hub"></a>Configurar un concentrador de notificación

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Vea un vídeo que muestra los pasos de esta sección](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##<a name="update-the-server-project-to-send-push-notifications"></a>Actualizar el proyecto de servidor para enviar notificaciones de inserción

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>Modificar su aplicación Cordova para recibir notificaciones de inserción

Asegúrese de que el proyecto de la aplicación Cordova Apache está preparado para administrar las notificaciones de inserción al instalar el complemento de inserción Cordova además de los servicios de inserción específica de la plataforma.

#### <a name="update-the-cordova-version-in-your-project"></a>Actualizar la versión de Cordova en su proyecto.

Se recomienda que actualice el proyecto de cliente a Cordova 6.1.1 si su proyecto está configurado con una versión anterior. Para actualizar el proyecto, haga clic con el botón config.xml para abrir el Diseñador de configuración. Seleccione la pestaña plataformas y elija 6.1.1 en el cuadro de texto **Cordova CLI** .

Elija **crear**y luego **Generar solución** para actualizar el proyecto.

#### <a name="install-the-push-plugin"></a>Instale el complemento de inserción

Apache Cordova aplicaciones no controlan forma nativa capacidades de red o el dispositivo.  Complementos que se publican en [npm](https://www.npmjs.com/) o en GitHub proporciona estas capacidades.  La `phonegap-plugin-push` complemento se usa para controlar las notificaciones de inserción de red.

Puede instalar el complemento de inserción de una de las siguientes maneras:

**Desde el símbolo:**

Ejecute el siguiente comando:

    cordova plugin add phonegap-plugin-push

**Desde dentro de Visual Studio:**

1.  En el Explorador de soluciones, abra la `config.xml` archivo, haga clic en **complementos** > **personalizado**, seleccione **Git** como el origen de instalación, a continuación, escriba `https://github.com/phonegap/phonegap-plugin-push` como el origen.

    ![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Haga clic en la flecha junto al origen de instalación.

3. **SENDER_ID**, si ya tiene un ID numérico para el proyecto de consola de desarrollador de Google, se puede agregar aquí. En caso contrario, escriba un valor de marcador de posición, como 777777, y si se dirige a Android puede actualizar este valor en config.xml más adelante.

4. Haga clic en **Agregar**.

Ahora está instalado el complemento de inserción.

####<a name="install-the-device-plugin"></a>Instale el complemento de dispositivo

Siga el mismo procedimiento que usó para instalar el complemento de inserción, pero encontrará el complemento del dispositivo en la lista de complementos principales (haga clic en **complementos** > **Core** encontrarlo). Necesita este complemento para obtener el nombre de la plataforma (`device.platform`).

#### <a name="register-your-device-for-push-on-start-up"></a>Registrar el dispositivo de inserción en el inicio

Inicialmente, incluimos algunos código mínimo para Android. Más adelante, ofrecemos algunas modificaciones pequeñas para que se ejecute en iOS o Windows 10.

1. Agregar una llamada a **registerForPushNotifications** durante la devolución de llamada para el proceso de inicio de sesión, o en la parte inferior del método **onDeviceReady** :

        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    Este ejemplo muestra llamar **registerForPushNotifications** tras autenticación, que se recomienda cuando se usa la aplicación de notificaciones de inserción y la autenticación.

2. Agregar el nuevo método de **registerForPushNotifications** como sigue:

        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.            
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }

3. (Android) En el código anterior, reemplace `Your_Project_ID` por el valor numérico project ID para la aplicación desde la [Consola del programador de Google].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcional) Configurar y ejecutar la aplicación en Android

Completar esta sección para habilitar las notificaciones de inserción para Android.

####<a name="enable-gcm"></a>Habilitar Firebase mensajería en la nube

Dado que inicialmente esperamos poner a la plataforma de Google Android, debe habilitar mensajería de nube de Firebase. Del mismo modo, si se dirige a los dispositivos de Microsoft Windows, debería habilitar la compatibilidad WNS.

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

####<a name="configure-backend"></a>Configurar el servidor de la aplicación móvil para enviar peticiones de inserción mediante FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####<a name="configure-your-cordova-app-for-android"></a>Configurar la aplicación Cordova para Android

En la aplicación Cordova, abra config.xml y reemplace `Your_Project_ID` por el valor numérico project ID para la aplicación desde la [Consola del programador de Google].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Abra index.js y actualizar el código para usar el identificador de proyecto numérico.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

####<a name="configure-device"></a>Configurar el dispositivo Android para depuración USB

Antes de implementar la aplicación en el dispositivo Android, debe habilitar la depuración USB.  En su teléfono Android, siga estos pasos:

1. Vaya a **configuración** > **acerca de teléfono**, a continuación, puntee en el **número de versión** hasta el modo para programadores está habilitado (unas 7 veces).

2. En la **configuración** > **Opciones del desarrollador** habilitar **depuración USB**y después conectar el teléfono Android a su PC con un Cable USB de desarrollo.

Hemos probado con un dispositivo de X Google Nexus 5 ejecutando Android 6.0 (Mallorca).  Sin embargo, las técnicas son comunes a cualquier versión moderna de Android.

#### <a name="install-google-play-services"></a>Instale los servicios de Google Play

El complemento de inserción se basa en Android servicios de Google reproducir las notificaciones de inserción.  

1.  En **Visual Studio**, haga clic en **Herramientas** > **Android** > **Android SDK Manager**, expanda la carpeta **Extras** y Active la casilla para asegurarse de que cada uno de los siguientes SDK está instalado.
    * Android 2.3 o superior
    * Revisión del repositorio de Google 27 o superior
    * Servicios de Google Play 9.0.2 o superior

2.  Haga clic en **Instalar paquetes** y espere a completar la instalación.

Actual necesaria bibliotecas aparecen en la [documentación de instalación del complemento de phonegap de inserción].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Notificaciones de inserción de prueba de la aplicación en Android

Puede notificaciones de inserción de prueba ahora, ejecute la aplicación e insertar elementos en la tabla TodoItem. Puede hacerlo desde el mismo dispositivo o desde un segundo dispositivo, siempre y cuando está utilizando el servidor de la misma. Probar la aplicación Cordova en la plataforma Android en una de las siguientes maneras:

- **En un dispositivo físico:**  
Adjuntar el dispositivo Android con su equipo de desarrollo con un cable USB.  En lugar de **Google Android emulador**, seleccione el **dispositivo**. Implementar la aplicación en el dispositivo de Visual Studio y ejecutarlo.  A continuación, puede interactuar con la aplicación en el dispositivo.  
Mejorar su experiencia de desarrollo.  Pantalla de uso compartido de aplicaciones como [Mobizen] puede ayudarle a desarrollar una aplicación Android mediante la proyección de la pantalla Android en un explorador web en su PC.

- **En un emulador Android:**  
Hay pasos de configuración adicionales necesarios cuando se ejecuta en un emulador.

    Asegúrese de que está implementando a o depuración en un dispositivo virtual que tiene Google APIs establecer como destino, tal como se muestra a continuación, en el administrador del dispositivo Virtual Android (AVD).

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Si desea usar un x86 más rápido emulador, [instale el controlador HAXM](https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM) y configurar el emulador usarlo.

    Agregar una cuenta de Google en el dispositivo Android haciendo clic en **aplicaciones** > **configuración** > **Agregar una cuenta**y, a continuación, siga las indicaciones para agregar un Google existente de la cuenta del dispositivo (se recomienda usar una cuenta existente, en lugar de crear uno nuevo).

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Ejecute la aplicación todolist como antes e insertar un nuevo elemento de todo. En este momento, se muestra un icono de notificación en el área de notificación. Puede abrir el cajón de notificación para ver el texto completo de la notificación.

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##<a name="optional-configure-and-run-on-ios"></a>(Opcional) Configurar y ejecutar en iOS

Esta sección es para ejecutar el proyecto Cordova en dispositivos iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

####<a name="install-and-run-the-ios-remotebuild-agent-on-a-mac-or-cloud-service"></a>Instalar y ejecutar al agente de remotebuild iOS en un servicio de Mac o en la nube

Antes de poder ejecutar una aplicación de Cordova en iOS mediante Visual Studio, vaya a través de los pasos de la [Guía de configuración de iOS](http://taco.visualstudio.com/en-us/docs/ios-guide/) instalar y ejecutar al agente de remotebuild.

Asegúrese de que se puede generar la aplicación para iOS. Los pasos de la Guía de configuración son necesarios para generar para iOS desde Visual Studio. Si no tiene un equipo Mac, puede generar para iOS con el agente de remotebuild en un servicio como MacInCloud. Para obtener más información, vea [ejecutar la aplicación en la nube](http://taco.visualstudio.com/en-us/docs/build_ios_cloud/).

>[AZURE.NOTE] XCode 7 o posterior es necesario para usar el complemento de inserción en iOS.

####<a name="find-the-id-to-use-as-your-app-id"></a>Busque el identificador de utilizar como su ID de aplicación

Antes de registrar su aplicación para las notificaciones de inserción, config.xml abierto en la aplicación Cordova, busque la `id` valor en el elemento de widget de atributo y copiarlo para su uso posterior. En el siguiente código XML, es el identificador de `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
        version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Más adelante, use este identificador cuando cree un identificador de aplicación en el portal de programadores de Apple. (Si crea un ID de aplicación en el portal de programadores y desea usarlo, debe realizar algunos pasos adicionales más adelante en este tutorial para cambiar este ID en config.xml. El identificador del elemento de widget debe coincidir con el identificador de aplicación en el portal de programadores.)

####<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrar la aplicación para las notificaciones de inserción en el portal de programadores de Apple

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Vea un vídeo que muestra los pasos similares](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####<a name="configure-azure-to-send-push-notifications"></a>Configurar Azure para enviar notificaciones de inserción

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

####<a name="verify-that-your-app-id-matches-your-cordova-app"></a>Compruebe que el identificador de aplicación coincide con la aplicación Cordova

Si el identificador de aplicación creada en su cuenta de desarrollador de Apple ya coincide con el identificador del elemento widget en config.xml, puede omitir este paso. Sin embargo, si no coinciden con los identificadores, siga estos pasos:

1. Elimine la carpeta plataformas de su proyecto.

2. Elimine la carpeta de complementos de su proyecto.

3. Elimine la carpeta node_modules de su proyecto.

4. Actualice el atributo id del elemento widget en config.xml para usar el identificador de aplicación que creó en su cuenta de desarrollador de Apple.

5. Volver a crear el proyecto.

#####<a name="test-push-notifications-in-your-ios-app"></a>Notificaciones de inserción de prueba en su aplicación iOS

1. En Visual Studio, asegúrese de que **iOS** está seleccionado como el destino de distribución y, a continuación, elija el **dispositivo** para que se ejecute en su dispositivo iOS conectada.

    Puede ejecutar en un dispositivo iOS conectado a su PC con iTunes. IOS Simulator no admite las notificaciones de inserción.

2. Presione el botón **Ejecutar** o **F5** en Visual Studio para generar el proyecto e inicie la aplicación en un dispositivo iOS, luego haga clic en **Aceptar** para aceptar las notificaciones de inserción.

    >[AZURE.NOTE] Explícitamente debe aceptar las notificaciones de inserción de la aplicación. Esta solicitud sólo se produce la primera vez que se ejecute la aplicación.

3. En la aplicación, escriba una tarea y, a continuación, haga clic en el signo más (+) icono.

4. Compruebe que se recibe una notificación y haga clic en Aceptar para cerrar la notificación.

##<a name="optional-configure-and-run-on-windows"></a>(Opcional) Configurar y ejecutar en Windows

Esta sección es para ejecutar el proyecto de aplicación de Apache Cordova en dispositivos de Windows 10 (el complemento de inserción PhoneGap se admite en Windows 10). Puede omitir esta sección si no está trabajando con los dispositivos de Windows.

####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrar su aplicación de Windows para las notificaciones de inserción con WNS

Para usar las opciones de almacenamiento en Visual Studio, seleccione un destino de Windows en la lista de plataformas de soluciones, como **Windows x64** o **Windows x86** (evitar **Cualquier CPU de Windows** para las notificaciones de inserción).

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Vea un vídeo que muestra los pasos similares](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####<a name="configure-the-notification-hub-for-wns"></a>Configurar el hub de notificación para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####<a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configurar la aplicación Cordova para admitir notificaciones de inserción de Windows

Abra el Diseñador de configuración (ratón en config.xml y seleccione **Diseñador de vistas**), seleccione la pestaña de **Windows** y elija **Windows 10** en la **Versión de destino de Windows**.

>[AZURE.NOTE] Si está usando una versión de Cordova antes de Cordova 5.1.1 (se recomienda 6.1.1), también debe establecer el indicador capaz de brindar true en config.xml.

Para admitir la inserción notificaciones en su valor predeterminado (depurar) crea, build.json abrir archivo. Copiar la configuración de "publicación" a su configuración de depuración.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Tras la actualización, el código anterior debe este aspecto.

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

Crear la aplicación y compruebe que no dispone de ningún error. Aplicación de cliente ahora debe registrarse para las notificaciones de la aplicación móvil de back-end. Repita esta sección para cada proyecto de Windows en su solución.

####<a name="test-push-notifications-in-your-windows-app"></a>Notificaciones de inserción de prueba en la aplicación de Windows

En Visual Studio, asegúrese de que está seleccionada una plataforma de Windows como el destino de distribución, como **Windows x64** o **Windows x86**. Para ejecutar la aplicación en un equipo con Windows 10 hospedaje de Visual Studio, elija el **Equipo Local**.

Presione el botón ejecutar para generar el proyecto e iniciar la aplicación.

En la aplicación, escriba un nombre para una nueva todoitem y, a continuación, haga clic en el signo más (+) icono para agregarlo.

Compruebe que se recibe una notificación cuando se agrega el elemento.

##<a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre [Hubs de notificación] para obtener información sobre las notificaciones de inserción.
* Si no lo ha hecho ya, siga el tutorial de [Autenticación agregar] a su aplicación Cordova Apache.

Obtenga información sobre cómo usar el SDK.

* [Apache Cordova SDK]
* [SDK de servidor de ASP.NET]
* [Node.js Server SDK]

<!-- URLs -->
[Adición de autenticación]: app-service-mobile-cordova-get-started-users.md
[Inicio rápido de Apache Cordova]: app-service-mobile-cordova-get-started.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Cuenta de Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Consola de desarrollador de Google]: https://console.developers.google.com/home/dashboard
[documentación de instalación del complemento de phonegap de inserción]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Comunidad de Visual Studio de 2015]: http://www.visualstudio.com/
[Visual Studio Tools para Cordova Apache]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Notificación Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK de servidor de ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
