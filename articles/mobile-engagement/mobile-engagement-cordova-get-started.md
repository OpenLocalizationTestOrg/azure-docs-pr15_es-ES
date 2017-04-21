<properties
    pageTitle="Introducción a Azure compromiso móvil para Cordova/Phonegap"
    description="Aprenda a usar Azure Mobile compromiso con análisis y notificaciones Push para las aplicaciones de Cordova/Phonegap."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="js"
    ms.topic="hero-article" 
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Introducción a Azure compromiso móvil para Cordova/Phonegap

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tema muestra cómo usar el compromiso de Azure Mobile comprender el uso de la aplicación y enviar notificaciones de inserción a los usuarios segmentados para una aplicación móvil desarrolladas con Cordova.

En este tutorial, se crearán una aplicación Cordova en blanco con Mac y, a continuación, integrar Mobile compromiso SDK. Recopila los datos de análisis básico y recibe las notificaciones de inserción con el sistema de notificación de inserción de Apple (APN) para iOS y mensajería de nube de Google (GCM) para Android. Se implementará en un iOS o un dispositivo Android para realizar pruebas. 

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).

Este tutorial requiere lo siguiente:

+ XCode, que se puede instalar desde el Mac App Store (para implementar para iOS)
+ [Emulador & SDK android](http://developer.android.com/sdk/installing/index.html) (para implementar para Android)
+ Certificado de notificación de inserción (. p12) que puede obtener desde el centro de desarrollo de Apple APN
+ Número de proyecto de GCM que puede obtener en la consola del programador de Google para GCM
+ [Complemento de Cordova de contratación móvil](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] Puede encontrar el código fuente y el archivo Léame para el complemento de Cordova en [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Compromiso de móvil de configuración de la aplicación Cordova

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Conectar la aplicación con el compromiso de móvil de back-end

Este tutorial presenta una "integración básica" que es el mínimo conjunto necesarios para recopilar datos y enviar una notificación de inserción. 

Se creará una aplicación básica con Cordova para demostrar la integración:

###<a name="create-a-new-cordova-project"></a>Crear un nuevo proyecto de Cordova

1. Iniciar la ventana de *Terminal* en su equipo Mac y escriba lo siguiente en la que se cree un nuevo proyecto de Cordova desde la plantilla predeterminada. Asegúrese de que el perfil de publicación que utiliza finalmente para implementar la aplicación iOS utiliza 'com.mycompany.myapp' como la ID. App 

        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova

2. Ejecute el siguiente para configurar el proyecto para **iOS** y ejecutar en el iOS Simulator:

        $ cordova platform add ios 
        $ cordova run ios

3. Ejecute el siguiente para configurar el proyecto para **Android** y ejecutar en el emulador Android. Asegúrese de que la configuración de Android emulador SDK tiene su objetivo como Google APIs (Google Inc.) con la CPU / ABI como Google APIs ARM.  

        $ cordova platform add android
        $ cordova run android

4.  Agregar el complemento de consola de Cordova. 

        $ cordova plugin add cordova-plugin-console 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar la aplicación a Mobile compromiso back-end

1. Instale el complemento de Azure Mobile compromiso Cordova mientras proporciona los valores de variables para configurar el complemento:

        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
            --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Icono de llegar a Android* : debe ser el nombre del recurso sin cualquier extensión ni puede dibujar prefijo (ex: mynotificationicon), y se debe copiar el archivo de icono en su proyecto android (plataformas android o recursos y activa)

*iOS alcance icono* : debe ser el nombre del recurso con su extensión (ex: mynotificationicon.png), y el archivo de icono debe agregarse a su proyecto iOS con XCode (usando el menú de archivos agregar)

##<a id="monitor"></a>Habilitar la supervisión en tiempo real

1. En el proyecto Cordova - editar **www/js/index.js** para agregar se recibe la llamada a Mobile compromiso declarar una nueva actividad una vez el evento *deviceReady* .

         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }

2. Ejecute la aplicación:
        
    - **Para iOS**
    
        En `Terminal` ventana inicie la aplicación en una nueva instancia de Simulator ejecutando los siguientes:

            cordova run ios

    - **Para Android**
        
        En `Terminal` ventana inicie la aplicación en una nueva instancia de emulador ejecutando los siguientes:

            cordova run android

3. Puede ver las siguientes acciones en los registros de consola:

        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitar las notificaciones de inserción y en la aplicación de mensajería

Compromiso de móvil le permite interactuar con los usuarios con notificaciones Push y la aplicación en el contexto de las campañas de mensajería. Este módulo se denomina alcance en el portal de compromiso de móvil.
Las secciones siguientes realizará la instalación de la aplicación para recibirlas.

###<a name="configure-push-credentials-for-mobile-engagement"></a>Configurar las credenciales de inserción para compromiso de móvil

Para permitir que compromiso móvil enviar notificaciones de inserción en su nombre, debe conceder acceso al certificado de Apple iOS o clave de API de servidor de GCM. 
    
1. Desplácese hasta el portal de compromiso de móvil. Asegúrese de que está en la aplicación se usa para este proyecto y, a continuación, haga clic en el botón **activa** en la parte inferior:
    
    ![][1]
    
2. Se colocará en la página de configuración en el Portal de contratación. Desde allí, haga clic en la sección **Push nativa** :
    
    ![][2]

3. Configurar la clave de certificado o GCM servidor API de iOS

    **[iOS]**

    una. Seleccione su. p12, cargarla y escriba su contraseña:
    
    ![][3]

    **[Android]**

    una. Haga clic en el icono de edición delante **Clave API** en la sección Configuración de GCM y en el menú emergente que se muestra, pegue la clave del servidor GCM y haga clic en **Aceptar**. 
        
    ![][4]

###<a name="enable-push-notifications-in-the-cordova-app"></a>Habilitar las notificaciones de inserción en la aplicación de Cordova

Editar **www/js/index.js** para agregar la llamada a Mobile compromiso solicitar notificaciones de inserción y declarar un controlador:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                // on OpenUrl  
                function(_url) {   
                alert(_url);   
                });  
            Engagement.startActivity("myPage",{});  
        }

###<a name="run-the-app"></a>Ejecute la aplicación

**[iOS]**

1. Vamos a utilizar XCode para compilar e implementar la aplicación en el dispositivo para probar las notificaciones de inserción como iOS solo permite las notificaciones de inserción en un dispositivo real. Vaya a la ubicación donde se crea el proyecto Cordova y vaya a la ubicación **...\platforms\ios** . Abra el archivo .xcodeproj nativa en XCode. 
    
2. Compile e implemente la aplicación Cordova el dispositivo iOS utilizando la cuenta que tiene el aprovisionamiento perfil que contiene el certificado que acaba cargado en el portal de compromiso de móvil y el identificador de aplicación que coincide con el proporcionado al crear la aplicación Cordova. Puede comprobar el *identificador de paquete* en su **recursos\*-info.plist** archivo en XCode para coincidan. 

3. Se muestra el menú emergente de iOS estándar en el dispositivo que indica que la aplicación solicita permiso para enviar notificaciones. Conceder el permiso. 

**[Android]**

Simplemente puede usar el emulador para ejecutar la aplicación Android como notificaciones GCM son compatibles con el emulador Android. 

    cordova run android

##<a id="send"></a>Enviar una notificación a la aplicación

Ahora crearemos una campaña de notificación de inserción sencilla que le enviará un comando a la aplicación que se ejecuta en el dispositivo:

1. Vaya a la pestaña **alcance** en el portal de compromiso de móvil

2. Haga clic en **Nuevo anuncio** para crear su campaña de inserción

    ![][6]

3. Proporcionar entradas para crear su campaña **[Android]**
    
    - Proporcionar un **nombre** de la campaña. 
    - Seleccione el **Tipo de entrega** de *notificación del sistema* *Simple*
    - Seleccione la **hora de entrega** como *"cualquier hora"*
    - Proporcionar un **título** para la notificación de que será la primera línea en la inserción.
    - Proporcione un **mensaje** para la notificación que servirá como el cuerpo del mensaje. 

    ![][11]

4. Proporcionar entradas para crear su campaña **[iOS]**

    - Proporcionar un **nombre** de la campaña. 
    - Seleccione la **hora de entrega** como *"fuera de la aplicación solo"*
    - Proporcionar un **título** para la notificación de que será la primera línea en la inserción.
    - Proporcione un **mensaje** para la notificación que servirá como el cuerpo del mensaje. 
 
    ![][12]

5. Desplácese hacia abajo y en la sección contenido seleccione **notificación solo**

    ![][8]

6. [Opcional] También puede proporcionar una dirección URL de la acción. Asegúrese de que utiliza una combinación de dirección URL proporcionada al configurar el complemento **AZME\_REDIRIGIR\_URL** variable, por ejemplo, *myapp://test*.  

7. Finalice la configuración de la campaña más básica posibles. Ahora, desplácese hacia abajo de nuevo y haga clic en el botón **crear** para guardar su campaña.

8. Por último **Activar** su campaña
    
    ![][10]

9. Ahora debe ver una notificación de inserción en un dispositivo o emulador como parte de la campaña. 

##<a id="next-steps"></a>Pasos siguientes
[Información general de todos los métodos disponibles con Cordova Mobile compromiso SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

