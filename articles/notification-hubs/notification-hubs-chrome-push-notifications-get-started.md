<properties
    pageTitle="Enviar notificaciones de inserción a aplicaciones de Chrome con Hubs de notificación de Azure | Microsoft Azure"
    description="Aprenda a usar Hubs de notificación de Azure para enviar notificaciones de inserción a una aplicación de Chrome."
    services="notification-hubs"
    keywords="notificación de inserción de notificaciones de inserción móvil, las notificaciones de inserción, las notificaciones de inserción de chrome"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-chrome"
    ms.devlang="JavaScript"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Enviar notificaciones de inserción a aplicaciones de Chrome con Hubs de notificación de Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tema muestra cómo usar Hubs de notificación de Azure para enviar notificaciones de inserción a una aplicación de Chrome, que se mostrará en el contexto del explorador de Google Chrome. En este tutorial, creamos una aplicación de Chrome que recibe las notificaciones de inserción mediante [Mensajería de nube de Google (GCM)](https://developers.google.com/cloud-messaging/). 

>[AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

El tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

* [Habilitar mensajería de nube de Google](#register)
* [Configurar su centro de notificación](#configure-hub)
* [Conectar la aplicación de Chrome al concentrador de notificación](#connect-app)
* [Enviar una notificación de inserción a la aplicación de Chrome](#send)
* [Funciones y funciones adicionales](#next-steps)

>[AZURE.NOTE] Notificaciones de inserción de aplicación de Chrome no son genéricas notificaciones en el explorador, son específicos de la capacidad de ampliación de explorador modelo (consulte [Información general sobre aplicaciones de Chrome] para obtener información detallada). Además del explorador de escritorio, aplicaciones de Chrome ejecutan en teléfono móvil (Android y iOS) Cordova Apache. Consulte [Aplicaciones de Chrome en teléfono móvil] para obtener más información.

Configurar GCM y Hubs de notificación de Azure es idéntica a la configuración para Android, ya que desusado [Mensajería de nube de Google para Chrome] y la misma GCM admite dispositivos Android e instancias de Chrome.

##<a id="register"></a>Habilitar mensajería de nube de Google

1. Vaya al sitio Web de [Consola de nube de Google] , inicie sesión con sus credenciales de cuenta de Google y, a continuación, haga clic en el botón **Crear proyecto** . Proporcionar un **Nombre de proyecto**de apropiado y, a continuación, haga clic en el botón **crear** .

    ![Nube de Google consola: crear proyectos][1]

2. Tome nota del **Número de proyecto** en la página de **proyectos** para el proyecto que acaba de crear. Se utiliza como el **Identificador del remitente GCM** en la aplicación de Chrome registrar con GCM.

    ![Consola de nube de Google - número de proyecto][2]

3. En el panel izquierdo, haga clic en **las API de & auth**y, a continuación, desplácese hacia abajo y haga clic en el botón de alternancia para activar la **Mensajería de nube de Google para Android**. No debe habilitar **Mensajería de nube de Google para Chrome**.

    ![Consola de nube de Google - clave de servidor][3]

4. En el panel izquierdo, haga clic en **credenciales** > **Crear nueva clave** > **Clave servidor** > **crear**.

    ![Consola de nube de Google - credenciales][4]

5. Anote la **Clave API**de servidor. Va a configurar esto en el hub de notificación a continuación, para que pueda enviar notificaciones de inserción a GCM.

    ![Consola de nube de Google - clave API][5]

##<a id="configure-hub"></a>Configurar su centro de notificación

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. en el módulo de **configuración** , seleccione **Los servicios de notificación** y **Google (GCM)**. Escriba la clave de API y guardar.

&emsp;&emsp;![Notificación de Azure Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

##<a id="connect-app"></a>Conectar la aplicación de Chrome al concentrador de notificación

El concentrador de notificación ahora está configurado para trabajar con GCM y tiene las cadenas de conexión para registrar la aplicación para recibir y enviar notificaciones de inserción. LK

###<a name="create-a-new-chrome-app"></a>Crear una nueva aplicación de Chrome

En el ejemplo siguiente se basa en el [Ejemplo de Chrome aplicación GCM] y usa la forma recomendada para crear una aplicación de Chrome. Nos centraremos en los pasos específicamente para Hubs de notificación de Azure. 

>[AZURE.NOTE] Le recomendamos que descargue el origen de esta aplicación Chrome de [Ejemplo de concentrador de notificación de aplicación de Chrome].

Se crea la aplicación de Chrome a través de JavaScript y puede utilizar cualquiera de los editores de word preferido para crearlo. A continuación se muestra el aspecto que tendrá esta aplicación Chrome.

![Aplicación de Google Chrome][15]

1. Crear una carpeta y asígnele el nombre `ChromePushApp`. Por supuesto, el nombre es arbitrario: si el nombre algo diferente, asegúrese de que reemplaza la ruta de acceso en los segmentos de código necesario.

2. Descargar la [biblioteca crypto js] en la carpeta que creó en el segundo paso. Esta carpeta de biblioteca contiene dos subcarpetas: `components` y `rollups`.

3. Crear un `manifest.json` archivo. Todas las aplicaciones de Chrome se copia un archivo manifiestos que contiene los metadatos de la aplicación y, más importante, todos los permisos que se conceden a la aplicación cuando el usuario instala.

        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }

    Aviso de la `permissions` elemento, que especifica que esta aplicación Chrome podrán recibir notificaciones de inserción de GCM. También debe especificar el URI de Hubs de notificación de Azure donde la aplicación de Chrome llamará resto registrar.
    Nuestra aplicación de ejemplo también usa un archivo de icono `gcm_128.png`, que encontrará en el origen que se reutiliza desde el ejemplo GCM original. Puede sustituirlo por cualquier imagen que cumple los [criterios de icono](https://developer.chrome.com/apps/manifest/icons).

4. Cree un archivo llamado `background.js` con el siguiente código:

        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }

        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.

          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);

          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }

        var registerWindowCreated = false;

        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {

            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }

        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);

        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);

    Este es el archivo que aparece la ventana de la aplicación de Chrome HTML (**register.html**) y también define el controlador **messageReceived** para controlar la notificación de inserción entrante.

5. Cree un archivo llamado `register.html` -define la interfaz de usuario de la aplicación de Chrome. 

   >[AZURE.NOTE] Este ejemplo usa **CryptoJS v3.1.2**. Si ha descargado otra versión de la biblioteca, asegúrese de que reemplaza correctamente la versión en la `src` ruta de acceso.

        <html>

        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>

        <body>

        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>

        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

        <br/>

        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

        <br/>
        <br/>

        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

        </body>

        </html>

6. Cree un archivo llamado `register.js` con el código siguiente. Este archivo especifica la secuencia de comandos detrás `register.html`. Aplicaciones de Chrome no permitir la ejecución de en línea, para que tenga que crear una secuencia de comandos de copia de seguridad independiente para la interfaz de usuario.

        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }

        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }

          document.getElementById("console").innerHTML = currentStatus  + status;
        }

        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);

          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }

        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;

          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }

          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;

          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }

        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }

        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";

          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });

          originalUri = endpoint + hubName;
        }

        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration

          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;

          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);

          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }

        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";

          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();

          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };

          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }

          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");

          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }

    La secuencia de comandos anterior tiene los siguientes parámetros de clave:
    - **Window.OnLoad** define los eventos de clic de botón de los dos botones en la interfaz de usuario. Uno registra con GCM y el otro utiliza el identificador de registro que se devuelve después de registrarse con GCM registrar con Hubs de notificación de Azure.
    - **updateLog** es la función que nos permite controlar las capacidades de registro simple.
    - **registerWithGCM** es el primer controlador de clic de botón, lo que hace que la `chrome.gcm.register` llamar a GCM para registrar la instancia actual de la aplicación de Chrome.
    - **registerCallback** es la función de devolución de llamada se llama cuando finaliza la llamada de registro GCM.
    - **registerWithNH** es el segundo controlador de clic de botón que se registra con Hubs de notificación. Obtiene `hubName` y `connectionString` (que el usuario ha especificado) y elabora la llamada API de REST de registro de Hubs de notificación.
    - **splitConnectionString** y **generateSaSToken** son ayudantes que representan la implementación de JavaScript de un proceso de creación del token SA, que debe usarse en todas las llamadas de API de REST. Para obtener más información, vea [Conceptos comunes](http://msdn.microsoft.com/library/dn495627.aspx).
    - **sendNHRegistrationRequest** es la función que permite un resto HTTP llamar Hubs de notificación de Azure.
    - **registrationPayload** define la carga de XML de registro. Para obtener más información, vea [Crear API de REST de NH registro]. Actualizamos el identificador de registro en él con recibimos de GCM.
    - **cliente** es una instancia de **XMLHttpRequest** que usamos para realizar la solicitud de HTTP POST. Tenga en cuenta que actualizamos la `Authorization` encabezado con `sasToken`. Finalice la llamada registrará esta instancia de la aplicación de Chrome con Hubs de notificación de Azure.


La estructura de carpetas general para este proyecto debe ser similar a esta:     ![aplicación de Google Chrome: estructura de carpetas][21]

###<a name="set-up-and-test-your-chrome-app"></a>Configurar y probar la aplicación de Chrome

1. Abra el Explorador de Chrome. Abrir **las extensiones de Chrome** y habilitar el **modo para programadores**.

    ![Google Chrome - habilitar el modo para programadores][16]

2. Haga clic en **cargar la extensión sin empaquetar** y vaya a la carpeta donde se crean los archivos. Opcionalmente, también puede usar la **herramienta de programador de extensiones y aplicaciones de Chrome**. Esta herramienta es una aplicación de Chrome en Sí (se instala desde el almacén de Web Chrome) y proporciona las capacidades avanzadas de depuración para el desarrollo de aplicaciones de Chrome.

    ![Google Chrome - cargar la extensión descomprimir][17]

3. Si se crea la aplicación de Chrome sin errores, a continuación, verá la aplicación de Chrome mostrarse.

    ![Google Chrome - mostrar de la aplicación de Chrome][18]

4. Escriba el **Número de proyecto** que obtuvo una versión anterior de la **Consola de nube de Google** como el identificador del remitente y haga clic en **registrar con GCM**. Verá el mensaje **registro con GCM se realizó correctamente.**

    ![Google Chrome - personalización de la aplicación de Chrome][19]

5. Escriba su **Nombre de concentrador de notificación** y **DefaultListenSharedAccessSignature** que obtuvo desde el portal anteriormente y haga clic en **registrar con concentrador de notificación de Azure**. Verá el mensaje **notificación concentrador registro realizado!** y los detalles de la respuesta de registro que contiene el registro de Azure notificación Hubs Id.

    ![Google Chrome - especificar detalles de concentrador de notificación][20]  

##<a name="send"></a>Enviar una notificación a su aplicación de Chrome

Con fines de pruebas, le enviaremos la aplicación de la consola de notificaciones de inserción de Chrome mediante un .NET. 

>[AZURE.NOTE] Puede enviar notificaciones de inserción con notificación Hubs desde cualquier back-end a través de nuestro público <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>. Consulte nuestro [portal de documentación](https://azure.microsoft.com/documentation/services/notification-hubs/) para consultar más ejemplos entre plataformas.

1. En Visual Studio, en el menú **archivo** , seleccione **nuevo** y, a continuación, en **proyecto**. En **Visual C#**, haga clic en **Windows** y **Aplicación de consola**y, a continuación, haga clic en **Aceptar**.  Esto crea un nuevo proyecto de aplicación de consola.

2. En el menú **Herramientas** , haga clic en **Administrador de paquetes de biblioteca** y, a continuación, **Consola del Administrador de paquetes**. Muestra la consola del Administrador de paquetes.

3. En la ventana de la consola, ejecute el siguiente comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Esto agrega una referencia en el SDK de Bus de servicio de Azure con el <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">paquete WindowsAzure.ServiceBus NuGet</a>.

4. Abrir `Program.cs` y agregue las siguientes `using` instrucción:

        using Microsoft.Azure.NotificationHubs;

5. En la `Program` de clase, agregue el método siguiente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

    Asegúrese de reemplazar el `<hub name>` marcador de posición con el nombre del concentrador de notificación que aparece en el [portal](https://portal.azure.com) en el módulo de concentrador de notificación. Además, reemplace el marcador de posición de cadena de conexión con la cadena de conexión denominada `DefaultFullSharedAccessSignature` que obtuvo en la sección Configuración de concentrador de notificación.

    >[AZURE.NOTE] Asegúrese de que usa la cadena de conexión con acceso **completo** , no **escuchar** el acceso. La cadena de conexión de acceso **escuchar** no conceder permisos para enviar notificaciones de inserción.

5. Agregar las llamadas siguientes en la `Main` método:

         SendNotificationAsync();
         Console.ReadLine();
         
6. Asegúrese de que se está ejecutando Chrome y ejecute la aplicación de consola.

7. Verá la siguiente notificación emergente en el escritorio.

    ![Google Chrome - notificación][13]

8. También puede ver todas las notificaciones mediante la ventana de notificaciones de Chrome en la barra de tareas (en Windows) cuando se está ejecutando Chrome.

    ![Google Chrome - lista de notificaciones][14]

>[AZURE.NOTE] No es necesario tener la ejecución de la aplicación de Chrome o abrir en el explorador (aunque debe estar ejecutándose el explorador Chrome propio). Obtendrá una vista consolidada de todas las notificaciones en la ventana de Chrome notificaciones.

## <a name="next-steps"> </a>Pasos siguientes

Más información sobre la notificación Hubs en [Visión general de Hubs de notificación].

Para dirigirse a usuarios específicos, consulte el tutorial de [Azure notificación Hubs notificar a usuarios] . 

Si desea agrupar los usuarios por grupos de interés, puede seguir el tutorial de [Azure notificación Hubs noticias] .

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Ejemplo de concentrador de notificación de aplicación de Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Consola de nube de Google]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[Información general de Hubs de notificación]: notification-hubs-push-notification-overview.md
[Información general sobre aplicaciones de Chrome]: https://developer.chrome.com/apps/about_apps
[Ejemplo de aplicación GCM de Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Aplicaciones de Chrome en teléfono móvil]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Crear registro NH API de REST]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[biblioteca de Crypto js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Nube de Google mensajería de Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Notificación de Azure Hubs notificar a los usuarios]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Noticias de notificación Hubs Azure]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
