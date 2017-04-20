<properties
    pageTitle="Enlace de concentrador de notificación de funciones de Azure | Microsoft Azure"
    description="Entender cómo utilizar el enlace de concentrador de notificación de Azure en funciones de Azure."
    services="functions"
    documentationCenter="na"
    authors="wesmc7777"
    manager="erikre"
    editor=""
    tags=""
    keywords="funciones de Azure, funciones, el procesamiento de eventos, cálculo dinámica, sin servidor arquitectura"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="10/27/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-notification-hub-output-binding"></a>Enlace de salida de Azure concentrador de notificación de funciones

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y enlaces de Azure notificación concentrador de código en las funciones de Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Las funciones pueden enviar notificaciones de inserción usando un concentrador de notificación de Azure configurado con algunas líneas de código. Sin embargo, el concentrador de notificación de Azure debe estar configurado para los servicios de notificaciones de plataforma (PNS) que desea usar. Para obtener más información sobre cómo configurar un concentrador de notificación de Azure y aplicaciones cliente que se registran para recibir notificaciones, consulte [Introducción a Hubs de notificación](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) y haga clic en la plataforma de cliente de destino en la parte superior.

Pueden ser las notificaciones que enviar notificaciones nativas o notificaciones de plantilla. Notificaciones nativas destino una plataforma de notificación específica, como se ha configurado en el `platform` propiedad del enlace de salida. Una notificación de la plantilla se puede utilizar para varias plataformas de destino.   

## <a name="notification-hub-output-binding-properties"></a>Propiedades de enlace de salida de concentrador de notificación

El archivo function.json proporciona las siguientes propiedades:

- `name`: Nombre variable usado en el código de la función para el mensaje de notificación de concentrador.
- `type`: debe estar establecida en *"notificationHub"*.
- `tagExpression`: Expresiones de etiqueta permiten especificar que van a entregar las notificaciones a un conjunto de dispositivos que se han registrado para recibir notificaciones que coinciden con la expresión de etiqueta.  Para obtener más información, vea [expresiones de enrutamiento y la etiqueta](../notification-hubs/notification-hubs-tags-segment-push-message.md).
- `hubName`: Nombre del recurso de concentrador de notificación en el portal de Azure.
- `connection`: Esta cadena de conexión debe ser una cadena de conexión de **Configuración de la aplicación** el valor de *DefaultFullSharedAccessSignature* para su centro de notificación.
- `direction`: debe estar establecida en *"a"*. 
- `platform`: La propiedad de plataforma indica la plataforma de notificación los objetivos de la notificación. Debe ser uno de los siguientes valores:
    - `template`: Esta es la plataforma predeterminada si se omite la propiedad de la plataforma en el enlace de salida. Notificaciones de plantilla pueden usarse para cualquier plataforma configurado en el Hub de notificación de Azure. Para obtener más información sobre el uso de plantillas en general para enviar notificaciones de distintas plataformas con un concentrador de notificación de Azure, consulte [plantillas](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).
    - `apns`: Servicio de notificación de inserción de Apple. Para obtener más información sobre cómo configurar el hub de notificación para APN y recibir la notificación en una aplicación de cliente, consulte [Enviar notificaciones de inserción para iOS con Hubs de notificación de Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
    - `adm`: [Mensajería dispositivo Amazon](https://developer.amazon.com/device-messaging). Para obtener más información sobre cómo configurar el concentrador de notificación de ADM y recibir la notificación en una aplicación Kindle, vea [Introducción a Hubs de notificación para las aplicaciones de Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
    - `gcm`: [Mensajería de nube de Google](https://developers.google.com/cloud-messaging/). Mensajería de nube de firebase, que es la nueva versión de GCM, también es compatible. Para obtener más información sobre cómo configurar el hub de notificación para GCM/FCM y recibir la notificación en una aplicación de cliente Android, consulte [Enviar notificaciones de inserción para Android con Hubs de notificación de Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
    - `wns`: [Servicios de notificación de inserción de Windows](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) identificación plataformas Windows. Windows Phone 8.1 y posterior también es compatible con WNS. Para obtener más información sobre cómo configurar el hub de notificación para WNS y recibir la notificación en una aplicación de la plataforma de Windows Universal (UWP), consulte [Introducción a notificación Hubs para Windows Universal plataforma de aplicaciones](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
    - `mpns`: [Servicio de notificación de inserción de Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Es compatible con esta plataforma Windows Phone 8 y plataformas Windows Phone anteriores. Para obtener más información sobre cómo configurar el hub de notificación para MPNS y recibir la notificación en una aplicación de Windows Phone, consulte [Enviar notificaciones de inserción con Hubs de notificación de Azure en Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)
 
Ejemplo function.json:

    {
      "bindings": [
        {
          "name": "notification",
          "type": "notificationHub",
          "tagExpression": "",
          "hubName": "my-notification-hub",
          "connection": "MyHubConnectionString",
          "platform": "gcm",
          "direction": "out"
        }
      ],
      "disabled": false
    }

## <a name="notification-hub-connection-string-setup"></a>Configuración de cadena de conexión de hub de notificaciones

Para usar un resultado de concentrador de notificación de enlace, debe configurar la cadena de conexión para el concentrador. Esto puede hacerse en la pestaña *integrar* seleccionando el concentrador de notificación o crear uno nuevo. 

Puede agregar manualmente una cadena de conexión para un concentrador existente agregando una cadena de conexión para el *DefaultFullSharedAccessSignature* a su centro de notificación. Esta cadena de conexión proporciona el permiso de acceso de la función para enviar mensajes de notificación. El valor de cadena de conexión de *DefaultFullSharedAccessSignature* puede tener acceso desde el botón de **teclas** en el módulo principal de su recurso de concentrador de notificación en el portal de Azure. Para agregar manualmente una cadena de conexión para su centro, realice los siguientes pasos: 

1. En el módulo de **aplicación de la función** del portal de Azure, haga clic en **configuración de la aplicación de función > Ir a configuración del servicio de aplicación**.

2. En el módulo de **configuración** , haga clic en **Configuración de la aplicación**.

3. Desplácese hacia abajo hasta la sección de **cadenas de conexión** y agregue una entrada con nombre en el valor *DefaultFullSharedAccessSignature* para su centro de notificación. Cambiar el tipo **personalizado**.
4. Hacer referencia a su nombre de la cadena de conexión en los enlaces de salida. Es similar a **MyHubConnectionString** utilizados en el ejemplo anterior.

## <a name="native-notification-examples"></a>Ejemplos de notificación nativo

#### <a name="apns-native-notifications-with-c-queue-triggers"></a>Notificaciones nativas APN con desencadenadores de cola C#

Este ejemplo muestra cómo usar tipos definidos en la [Biblioteca de Microsoft Azure notificación Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar una notificación de APN nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native APNS notification is ...
        // { "aps": { "alert": "notification message" }}  

        log.Info($"Sending APNS notification of a new user");   
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{apnsNotificationPayload}");
        await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
    }

#### <a name="gcm-native-notifications-with-c-queue-triggers"></a>Notificaciones nativas GCM con desencadenadores de cola C#

Este ejemplo muestra cómo usar tipos definidos en la [Biblioteca de Microsoft Azure notificación Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar una notificación de GCM nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native GCM notification is ...
        // { "data": { "message": "notification message" }}  

        log.Info($"Sending GCM notification of a new user");    
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{gcmNotificationPayload}");
        await notification.AddAsync(new GcmNotification(gcmNotificationPayload));       
    }

#### <a name="wns-native-notifications-with-c-queue-triggers"></a>Notificaciones nativas WNS con desencadenadores de cola C#

Este ejemplo muestra cómo usar tipos definidos en la [Biblioteca de Microsoft Azure notificación Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar una notificación de WNS nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The XML format for a native WNS toast notification is ...
        // <?xml version="1.0" encoding="utf-8"?>
        // <toast>
        //   <visual>
        //     <binding template="ToastText01">
        //       <text id="1">notification message</text>
        //     </binding>
        //   </visual>
        // </toast>

        log.Info($"Sending WNS toast notification of a new user");  
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                        "<toast><visual><binding template=\"ToastText01\">" +
                                            "<text id=\"1\">" + 
                                                "A new user wants to be added (" + user.name + ")" + 
                                            "</text>" +
                                        "</binding></visual></toast>";

        log.Info($"{wnsNotificationPayload}");
        await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));       
    }


## <a name="template-notification-examples"></a>Ejemplos de plantillas de notificación

#### <a name="template-example-for-nodejs-timer-triggers"></a>Ejemplo de plantilla para desencadenadores de temporizador Node.js 

En este ejemplo se envía una notificación para un [registro de la plantilla](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contiene `location` y `message`.

    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
       
        if(myTimer.isPastDue)
        {
            context.log('Node.js is running late!');
        }
        context.log('Node.js timer trigger function ran!', timeStamp);  
        context.bindings.notification = {
            location: "Redmond",
            message: "Hello from Node!"
        };
        context.done();
    };

#### <a name="template-example-for-f-timer-triggers"></a>Ejemplo de plantilla para F # temporizador desencadenadores

En este ejemplo se envía una notificación para un [registro de la plantilla](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contiene `location` y `message`.

    let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
        notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]


#### <a name="template-example-using-an-out-parameter"></a>Ejemplo de plantilla con un parámetro de salida 

En este ejemplo se envía una notificación para un [registro de la plantilla](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contiene una `message` el marcador de posición en la plantilla.

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
     
    public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
    }
     
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return templateProperties;
    }

#### <a name="template-example-with-asynchronous-function"></a>Ejemplo de plantilla con la función asincrónica

Si está utilizando código asincrónico, los parámetros no se admiten. En este caso utilice `IAsyncCollector` para devolver la notificación de la plantilla. El código siguiente es un ejemplo asincrónico del código anterior. 

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        log.Info($"Sending Template Notification to Notification Hub");
        await notification.AddAsync(GetTemplateProperties(myQueueItem));    
    }
    
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["user"] = "A new user wants to be added : " + message;
        return templateProperties;
    }

#### <a name="template-example-using-json"></a>Ejemplo de plantilla con JSON 

En este ejemplo se envía una notificación para un [registro de la plantilla](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contiene una `message` el marcador de posición en la plantilla mediante una cadena JSON válida.

    using System;
     
    public static void Run(string myQueueItem,  out string notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
    }


#### <a name="template-example-using-notification-hubs-library-types"></a>Ejemplo de plantilla con tipos de bibliotecas de Hubs de notificación

Este ejemplo muestra cómo usar tipos definidos en la [Biblioteca de Hubs de notificación de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 


    #r "Microsoft.Azure.NotificationHubs"

    using System;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
     
    public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
    {
       log.Info($"C# Queue trigger function processed: {myQueueItem}");
       notification = GetTemplateNotification(myQueueItem);
    }

    private static TemplateNotification GetTemplateNotification(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return new TemplateNotification(templateProperties);
    }

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
