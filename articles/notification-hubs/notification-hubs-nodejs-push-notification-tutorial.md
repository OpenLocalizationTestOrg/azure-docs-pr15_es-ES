<properties
    pageTitle="Enviar notificaciones de inserción con Hubs de notificación de Azure y Node.js"
    description="Obtenga información sobre cómo usar Hubs de notificación para enviar notificaciones de inserción desde una aplicación de Node.js."
    keywords="notificación de inserción, inserción notifications,node.js push, inserción de ios"
    services="notification-hubs"
    documentationCenter="nodejs"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Enviar notificaciones de inserción con Hubs de notificación de Azure y Node.js
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##<a name="overview"></a>Información general

> [AZURE.IMPORTANT] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Esta guía le mostrará cómo enviar notificaciones de inserción con la Ayuda de Azure notificación Hubs directamente desde una aplicación de Node.js. 

Los escenarios cubiertos incluyen el envío de notificaciones de inserción a aplicaciones en las siguientes plataformas:

* Android
* iOS
* Windows Phone
* Plataforma Windows universal 

Para obtener más información sobre hubs de notificación, vea la sección [Pasos siguientes](#next) .

##<a name="what-are-notification-hubs"></a>¿Qué son Hubs de notificación?

Azure Hubs notificación proporcionan una infraestructura de fácil de usar, en varias plataforma, scalable para enviar notificaciones de inserción a dispositivos móviles. Para obtener detalles sobre la infraestructura de servicio, consulte la página de [Hubs de notificación de Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

##<a name="create-a-nodejs-application"></a>Crear una aplicación de Node.js

El primer paso en este tutorial es crear una nueva aplicación Node.js en blanco. Para obtener instrucciones sobre cómo crear una aplicación de Node.js, consulte [crear e implementar una aplicación de Node.js al sitio Web de Azure][nodejswebsite], [Servicio de nube de Node.js] [ Node.js Cloud Service] con Windows PowerShell o [sitio Web con WebMatrix].

##<a name="configure-your-application-to-use-notification-hubs"></a>Configurar la aplicación para usar Hubs de notificación

Para usar Hubs de notificación de Azure, debe descargar y usar el Node.js [paquete azure](https://www.npmjs.com/package/azure), que incluye un conjunto integrado de bibliotecas de ayuda que comunicarse con los servicios REST de notificación de inserción.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilice el nodo paquete Manager (NPM) para obtener el paquete

1.  Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Linux) y vaya a la carpeta donde lo ha creado la aplicación en blanco.

2.  Escriba **npm instalar sb de azure** en la ventana de comandos.

3.  El comando **** o **dir** para comprobar que puede ejecutar manualmente una **nodo\_módulos** se creó la carpeta. Dentro de esa carpeta, busque el paquete de **azure** , que contiene las bibliotecas que debe tener acceso al concentrador de notificación.

>[AZURE.NOTE] Puede obtener más acerca de cómo instalar NPM oficial [NPM blog](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 

### <a name="import-the-module"></a>Importar el módulo

Con un editor de texto, agregue lo siguiente a la parte superior del archivo **server.js** de la aplicación:

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Configurar una conexión de concentrador de notificación de Azure

El objeto **NotificationHubService** le permite trabajar con hubs de notificación. El código siguiente crea un objeto de **NotificationHubService** para el concentrador de notificación denominado **hubname**. Agréguelo cerca de la parte superior del archivo **server.js** , después de la instrucción para importar el módulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Puede obtener el valor de **cadena de conexión** de conexión desde el [Portal de Azure] realizando los siguientes pasos:

1. En el panel de navegación izquierdo, haga clic en **Examinar**.

2. Seleccione **Hubs de notificación**y, a continuación, busque el concentrador que desee usar para la muestra. Si necesita ayuda para crear un nuevo concentrador de notificación, puede hacer referencia al [tutorial de Windows Store Introducción](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .

3. Seleccione **configuración**.

4. Haga clic en **directivas de acceso**. Verá dos cadenas de conexión de acceso compartido y completo.

![Portal de Azure - Hubs de notificación](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] También puede recuperar la cadena de conexión mediante el cmdlet **Get-AzureSbNamespace** proporcionado por [Azure PowerShell](../powershell-install-configure.md) o el comando **Mostrar nombres de azure sb** con la [interfaz de línea de comandos de Azure (Azure CLI)](../xplat-cli-install.md).

##<a name="general-architecture"></a>Arquitectura general

El objeto **NotificationHubService** expone las siguientes instancias de objeto para enviar notificaciones de inserción a aplicaciones y dispositivos específicos:

* **Android** - usar el objeto **GcmService** , que está disponible en **notificationHubService.gcm**
* **iOS** - usar el objeto **ApnsService** , que es accesible en **notificationHubService.apns**
* **Windows Phone** - usar el objeto **MpnsService** , que está disponible en **notificationHubService.mpns**
* **Universal plataforma de Windows** : utilice el objeto **WnsService** , que está disponible en **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Cómo: enviar notificaciones de inserción para las aplicaciones de Android

El objeto **GcmService** proporciona un método **Enviar** que se pueden usar para enviar notificaciones de inserción para las aplicaciones de Android. El método **Enviar** acepta los siguientes parámetros:

* **Etiquetas** : el identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a los clientes al.
* **Carga** - JSON o carga de cadena sin formato del mensaje.
* **Devolución de llamada** : la función de devolución de llamada.

Para obtener más información sobre el formato de carga, vea la sección de **carga** del documento del [Servidor de GCM implementar](http://developer.android.com/google/gcm/server.html#payload) .

El código siguiente utiliza la instancia de **GcmService** expuesta por el **NotificationHubService** para enviar una notificación de inserción a todos los clientes registrados.

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Cómo: enviar notificaciones de inserción para las aplicaciones de iOS

Al igual que aplicaciones Android descritas anteriormente, el objeto de **ApnsService** proporciona un método **Enviar** que se pueden usar para enviar notificaciones de inserción para las aplicaciones de iOS. El método **Enviar** acepta los siguientes parámetros:

* **Etiquetas** : el identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes.
* **Carga** - JSON del mensaje o carga de cadena.
* **Devolución de llamada** : la función de devolución de llamada.

Para obtener más información del formato de carga, vea la sección de **Notificación de carga** del documento [Local y la Guía de programación de notificaciones de inserción](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

El código siguiente utiliza la instancia de **ApnsService** expuesta por el **NotificationHubService** para enviar un mensaje de alerta a todos los clientes:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Cómo: enviar notificaciones de inserción para las aplicaciones de Windows Phone

El objeto de **MpnsService** proporciona un método de **envío** que se pueden usar para enviar notificaciones de inserción para las aplicaciones de Windows Phone. El método **Enviar** acepta los siguientes parámetros:

* **Etiquetas** : el identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes.
* **Carga** - carga XML del mensaje.
* **Nombre de destino**  -  `toast` para las notificaciones de aviso. `token`para las notificaciones de mosaico.
* **Clases de notificación** - la prioridad de la notificación. Vea la sección de **Elementos de encabezado HTTP** del documento [notificaciones desde un servidor de inserción](http://msdn.microsoft.com/library/hh221551.aspx) de valores válidos.
* **Opciones** - encabezados de solicitud opcional.
* **Devolución de llamada** : la función de devolución de llamada.

Para obtener una lista de opciones de **nombre de destino**, **clases de notificación** y encabezado válidas, consulte la página de [notificaciones desde un servidor de inserción](http://msdn.microsoft.com/library/hh221551.aspx) .

El código de ejemplo siguiente utiliza la instancia de **MpnsService** expuesta por el **NotificationHubService** para enviar una notificación de inserción:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Cómo: enviar notificaciones de inserción para las aplicaciones de la plataforma de Windows Universal (UWP)

El objeto de **WnsService** proporciona un método de **envío** que se pueden usar para enviar notificaciones de inserción a aplicaciones de la plataforma de Windows Universal.  El método **Enviar** acepta los siguientes parámetros:

* **Etiquetas** : el identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes registrados.
* **Carga** - la carga del mensaje XML.
* **Tipo** : el tipo de notificación.
* **Opciones** - encabezados de solicitud opcional.
* **Devolución de llamada** : la función de devolución de llamada.

Para obtener una lista de tipos válidos y los encabezados de la solicitud, vea [encabezados de la solicitud y la respuesta del servicio de notificación de inserción](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

El código siguiente utiliza la instancia de **WnsService** expuesta por el **NotificationHubService** para enviar una notificación de inserción a una aplicación UWP:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <a name="next-steps"></a>Pasos siguientes

Los fragmentos de código de ejemplo anteriores permiten crear fácilmente la infraestructura de servicio para entregar notificaciones de inserción a una amplia variedad de dispositivos. Ahora que ha aprendido los aspectos básicos del uso de notificación Hubs con node.js, siga estos vínculos para obtener más información acerca de cómo puede ampliar estas capacidades más.

-   Vea la referencia MSDN para [Hubs de notificación de Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
-   Visite el repositorio de [Azure SDK para nodo] en GitHub para obtener más ejemplos y detalles de la implementación.

  [Azure SDK de nodo]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Sitio Web con WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [Portal de Azure]: https://portal.azure.com
