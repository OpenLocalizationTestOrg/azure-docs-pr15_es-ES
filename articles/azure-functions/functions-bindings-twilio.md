<properties
    pageTitle="Enlace de funciones Twilio Azure | Microsoft Azure"
    description="Entender cómo usar Twilio enlaces con funciones de Azure."
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
    ms.date="10/20/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-twilio-output-binding"></a>Enlace de salida de Azure Twilio de funciones

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y usar enlaces Twilio con funciones de Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Azure admite funciones Twilio enlaces para habilitar las funciones enviar mensajes de texto SMS con unas pocas líneas de código y una cuenta de [Twilio](https://www.twilio.com/) de salida. 
 

## <a name="functionjson-for-azure-notification-hub-output-binding"></a>enlace de salida Function.JSON de concentrador de notificación de Azure

El archivo function.json proporciona las siguientes propiedades:

- `name`: Nombre variable usado en el código de la función para el mensaje de texto SMS Twilio.
- `type`: debe estar establecida en *"twilioSms"*.
- `accountSid`: Este valor se debe establecer el nombre de una configuración de aplicación que contiene al Sid de la cuenta Twilio.
- `authToken`: Este valor se debe establecer el nombre de una configuración de aplicación que contiene el token de autenticación Twilio.
- `to`: Este valor se establece en el número de teléfono que se envía el texto SMS.
- `from`: Este valor se establece en el número de teléfono que el texto SMS se envía desde.
- `direction`: debe estar establecida en *"a"*.
- `body`: Este valor se puede utilizar para codificar el mensaje de texto SMS si no es necesario establecer de forma dinámica en el código de la función. 

 
Ejemplo function.json:

    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "+1704XXXXXXX",
      "from": "+1425XXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Ejemplo C# cola desencadenador con Twilio el enlace de salida

#### <a name="synchronous"></a>Sincrónico

Este ejemplo sincrónico de código para un desencadenador de cola de almacenamiento de Azure usa un parámetro de salida para enviar un mensaje de texto a un cliente que ha realizado un pedido.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"

    using System;
    using Newtonsoft.Json;
    using Twilio;

    public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        message = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        message.Body = msg;
        message.To = order.mobileNumber;
    }

#### <a name="asynchronous"></a>Asincrónica

El código de este ejemplo asincrónico para un desencadenador de cola de almacenamiento de Azure envía un mensaje de texto a un cliente que ha realizado un pedido.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"
     
    using System;
    using Newtonsoft.Json;
    using Twilio;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        SMSMessage smsText = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        smsText.Body = msg;
        smsText.To = order.mobileNumber;
        
        await message.AddAsync(smsText);
    }


## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Desencadenador de cola de Node.js de ejemplo con Twilio el enlace de salida

En este ejemplo Node.js envía un mensaje de texto a un cliente que ha realizado un pedido.

    module.exports = function (context, myQueueItem) {
        context.log('Node.js queue trigger function processed work item', myQueueItem);
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        var msg = "Hello " + myQueueItem.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the message binding.
        context.bindings.message = {};
    
        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        context.bindings.message = {
            body : msg,
            to : myQueueItem.mobileNumber
        };
    
        context.done();
    };

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
