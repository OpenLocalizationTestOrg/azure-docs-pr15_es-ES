<properties
    pageTitle="Enlaces de funciones evento concentrador Azure | Microsoft Azure"
    description="Comprender cómo usar enlaces de concentrador de evento de Azure en las funciones de Azure."
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
    ms.date="10/17/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-event-hub-bindings"></a>Azure enlaces de concentrador de evento de funciones

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y enlaces de [Azure evento concentrador](../event-hubs/event-hubs-overview.md) de código para las funciones de Azure. Funciones de Azure admiten enlaces desencadenador y salida para Hubs de evento de Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Si es nuevo en Azure evento Hubs, vea la [información general de concentrador de evento de Azure](../event-hubs/event-hubs-overview.md).

## <a name="azure-event-hub-trigger-binding"></a>Hub de evento Azure desencadenar enlace

Un desencadenador de Azure evento concentrador puede usarse para responder a un evento que se envían a una secuencia de eventos de concentrador de evento. Debe tener acceso de lectura en el hub de evento para configurar un enlace de desencadenador.

#### <a name="functionjson-for-event-hub-trigger-binding"></a>Function.JSON de evento concentrador desencadenar enlace

El archivo *function.json* para un desencadenador de Azure evento concentrador especifica las siguientes propiedades:

- `type`: Debe estar establecida en *eventHubTrigger*.
- `name`: El nombre de variable usado en el código de la función para el mensaje de concentrador de evento. 
- `direction`: Debe establecerse para *en*. 
- `path`: El nombre del concentrador de evento.
- `consumerGroup`: Esta es una propiedad opcional que se usa para establecer el [grupo de consumidor](../event-hubs-overview.md#consumer-groups) utilizado para suscribirse a eventos en el hub. Si se omite, el `$Default` se utiliza el grupo de consumidores. 
- `connection`: El nombre de una configuración de aplicación que contiene la cadena de conexión para el espacio de nombres que reside el hub de evento. Copie esta cadena de conexión haciendo clic en el botón de **Información de conexión** para el espacio de nombres, no el hub de evento.  Esta cadena de conexión debe tener al menos permisos de lectura para activar el desencadenador.

        {
          "bindings": [
            {
              "type": "eventHubTrigger",
              "name": "myEventHubMessage",
              "direction": "in",
              "path": "MyEventHub",
              "connection": "myEventHubReadConnectionString"
            }
          ],
          "disabled": false
        }

#### <a name="azure-event-hub-trigger-c-example"></a>Ejemplo de Azure concentrador de evento desencadenador C#
 
Usando la function.json de ejemplo anterior, el cuerpo del mensaje de evento se registra con la función código de C# siguiente:
 
    using System;
    
    public static void Run(string myEventHubMessage, TraceWriter log)
    {
        log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
    }

#### <a name="azure-event-hub-trigger-f-example"></a>Ejemplo de Azure concentrador de evento desencadenador F #

Usando la function.json de ejemplo anterior, el cuerpo del mensaje de evento se registra con la función código de F # a continuación:

    let Run(myEventHubMessage: string, log: TraceWriter) =
        log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### <a name="azure-event-hub-trigger-nodejs-example"></a>Desencadenador de evento concentrador Azure Node.js ejemplo
 
Usando la function.json de ejemplo anterior, el cuerpo del mensaje de evento se registra con el código de la función Node.js siguiente:
 
    module.exports = function (context, myEventHubMessage) {
        context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
        context.done();
    };


## <a name="azure-event-hub-output-binding"></a>Enlace de salida de Azure concentrador de evento

Un concentrador de evento de Azure enlace de salida se usa para escribir eventos en una secuencia de eventos de concentrador de evento. Debe tener permiso de enviar a un concentrador de evento para escribir eventos en él. 

#### <a name="functionjson-for-event-hub-output-binding"></a>enlace de salida Function.JSON de concentrador de evento

El archivo *function.json* para un concentrador de evento de Azure de salida enlace especifica las siguientes propiedades:

- `type`: Debe estar establecida en *eventHub*.
- `name`: El nombre de variable usado en el código de la función para el mensaje de concentrador de evento. 
- `path`: El nombre del concentrador de evento.
- `connection`: El nombre de una configuración de aplicación que contiene la cadena de conexión para el espacio de nombres que reside el hub de evento. Copie esta cadena de conexión haciendo clic en el botón de **Información de conexión** para el espacio de nombres, no el hub de evento.  Esta cadena de conexión debe tener permisos de enviar para enviar el mensaje a la secuencia de concentrador de evento.
- `direction`: Debe establecerse a *fuera*. 

        {
          "type": "eventHub",
          "name": "outputEventHubMessage",
          "path": "myeventhub",
          "connection": "MyEventHubSend",
          "direction": "out"
        }


#### <a name="azure-event-hub-c-code-example-for-output-binding"></a>Azure ejemplo de código evento concentrador C# para el enlace de salida
 
El siguiente código de función de ejemplo de C# muestra cómo escribir un evento en una secuencia de eventos de concentrador de evento. En este ejemplo se representan el Hub de evento el enlace mostrado arriba de salida se aplica a un desencadenador de temporizador de C#.  
 
    using System;
    
    public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
    {
        String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    
        log.Verbose(msg);   
        
        outputEventHubMessage = msg;
    }

#### <a name="azure-event-hub-f-code-example-for-output-binding"></a>Azure ejemplo de código evento concentrador F # para el enlace de salida

F # función código de ejemplo siguiente muestra cómo escribir un evento en una secuencia de eventos de concentrador de evento. En este ejemplo se representan el Hub de evento el enlace mostrado arriba de salida se aplica a un desencadenador de temporizador de C#.

    let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
        let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
        log.Verbose(msg);
        outputEventHubMessage <- msg;

#### <a name="azure-event-hub-nodejs-code-example-for-output-binding"></a>Ejemplo de código de Azure Node.js concentrador de evento para el enlace de salida
 
El siguiente código de función de ejemplo Node.js muestra cómo escribir un evento en una secuencia de eventos de concentrador de evento. En este ejemplo se representan el Hub de evento el enlace mostrado arriba de salida se aplica a un desencadenador de temporizador Node.js.  
 
    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
        
        if(myTimer.isPastDue)
        {
            context.log('TimerTriggerNodeJS1 is running late!');
        }

        context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
        
        context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
    
        context.done();
    };

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
