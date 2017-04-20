<properties
    pageTitle="Desencadenadores de Bus de servicio de funciones de Azure y enlaces | Microsoft Azure"
    description="Entender cómo usar desencadenadores de Bus de servicio de Azure y enlaces en las funciones de Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
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
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-service-bus-triggers-and-bindings-for-queues-and-topics"></a>Azure desencadenadores de Bus de servicio de funciones y enlaces para colas y temas

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar y desencadenadores de Azure Bus de servicio de código y enlaces en las funciones de Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="sbtrigger"></a>Desencadenador de cola o tema de Bus de servicio

#### <a name="functionjson"></a>Function.JSON

El archivo *function.json* especifica las siguientes propiedades.

- `name`: El nombre de variable que se utiliza en el código de la función de la cola o tema o el mensaje de cola o tema. 
- `queueName`: Para cola desencadenar solo, el nombre de la cola de sondeo.
- `topicName`: Para el tema desencadenar solo, el nombre del tema para sondear.
- `subscriptionName`: Para el tema desencadenar solo, el nombre de suscripción.
- `connection`: El nombre de una configuración de aplicación que contiene una cadena de conexión de Bus de servicio. La cadena de conexión debe ser un espacio de nombres de Bus de servicio, que no se limita a una cola específica o un tema. Si la cadena de conexión no dispone de derechos de administración, establecer el `accessRights` propiedad. Si deja `connection` está vacío, el desencadenador o enlace funcionará con la cadena de conexión de Bus de servicio predeterminado para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsServiceBus.
- `accessRights`: Especifica los derechos de acceso disponibles para la cadena de conexión. Valor predeterminado es `manage`. Establecer `listen` si está usando una cadena de conexión no proporciona administrar permisos. En caso contrario, las funciones que puede intentar en tiempo de ejecución y no realizar operaciones que requieren derechos de administración.
- `type`: Debe estar establecida en *serviceBusTrigger*.
- `direction`: Debe establecerse para *en*. 

Ejemplo *function.json* para un desencadenador de cola de Bus de servicio:

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-code-example-that-processes-a-service-bus-queue-message"></a>Ejemplo de código de C# que procesa un mensaje de cola de Bus de servicio

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### <a name="f-code-example-that-processes-a-service-bus-queue-message"></a>Ejemplo de código de F # que procesa un mensaje de cola de Bus de servicio

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### <a name="nodejs-code-example-that-processes-a-service-bus-queue-message"></a>Ejemplo de código de Node.js que procesa un mensaje de cola de Bus de servicio

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### <a name="supported-types"></a>Tipos compatibles

El mensaje de cola de Bus de servicio se puede deserializar a cualquiera de los siguientes tipos:

* Objeto (desde JSON)
* cadena
* matriz de bytes 
* `BrokeredMessage`(C#) 

#### <a id="sbpeeklock"></a>Comportamiento de PeekLock

El tiempo de ejecución de funciones recibe un mensaje de `PeekLock` modo y llamadas `Complete` en el mensaje si la función finaliza correctamente, o llamadas `Abandon` si se produce un error en la función. Si la función se ejecuta más de la `PeekLock` tiempo de espera, el bloqueo se renovará automáticamente.

#### <a id="sbpoison"></a>Tratamiento de mensajes dudosos

Bus de servicio tiene su propio mensaje dudoso tratamiento que no controlada o configurado en la configuración de las funciones de Azure o código. 

#### <a id="sbsinglethread"></a>Solo de subprocesos

De forma predeterminada, las funciones en tiempo de ejecución procesa simultáneamente varios mensajes de cola. Para dirigir el tiempo de ejecución de proceso solo una sola cola o mensaje de tema a la vez, establecer `serviceBus.maxConcurrrentCalls` en 1 en el archivo *host.json* . Para obtener información acerca del archivo *host.json* , vea la [Estructura de carpetas](functions-reference.md#folder-structure) en el artículo de referencia del programador y [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) en el wiki de repositorio de WebJobs.Script.

## <a id="sboutput"></a>Enlace de salida de cola de Bus de servicio o tema

#### <a name="functionjson"></a>Function.JSON

El archivo *function.json* especifica las siguientes propiedades.

- `name`: El nombre de variable que se utiliza en código de la función de la cola o el mensaje de cola. 
- `queueName`: Para cola desencadenar solo, el nombre de la cola de sondeo.
- `topicName`: Para el tema desencadenar solo, el nombre del tema para sondear.
- `subscriptionName`: Para el tema desencadenar solo, el nombre de suscripción.
- `connection`: Desencadenar igual que para Bus de servicio.
- `accessRights`: Especifica los derechos de acceso disponibles para la cadena de conexión. Valor predeterminado es `manage`. Establecer `send` si está usando una cadena de conexión no proporciona administrar permisos. En caso contrario, las funciones que puede intentar en tiempo de ejecución y no realizar operaciones que requieren administrar derechos, como crear colas.
- `type`: Debe estar establecida en *serviceBus*.
- `direction`: Debe establecerse a *fuera*. 

Ejemplo *function.json* para usar un desencadenador de temporizador para escribir mensajes de cola de Bus de servicio:

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="supported-types"></a>Tipos compatibles

Funciones de Azure pueden crear un mensaje de cola de Bus de servicio desde cualquiera de los siguientes tipos.

* Objeto (siempre crea un mensaje JSON, crea el mensaje con un objeto null si el valor es null cuando finaliza la función)
* cadena (crea un mensaje si el valor no es nulo cuando finaliza la función)
* matriz de bytes (funciona como cadena) 
* `BrokeredMessage`(C#, funciona como cadena)

Para crear varios mensajes en una función de C#, puede usar `ICollector<T>` o `IAsyncCollector<T>`. Se crea un mensaje cuando llame a la `Add` método.

#### <a name="c-code-examples-that-create-service-bus-queue-messages"></a>Ejemplos de código de C# que cree mensajes de cola de Bus de servicio

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### <a name="f-code-example-that-creates-a-service-bus-queue-message"></a>Ejemplo de código de F # que crea un mensaje de cola de Bus de servicio

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### <a name="nodejs-code-example-that-creates-a-service-bus-queue-message"></a>Ejemplo de código de Node.js que crea un mensaje de cola de Bus de servicio

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
