<properties
    pageTitle="Azure desencadenadores funciones y enlaces | Microsoft Azure"
    description="Entender cómo usar desencadenadores y enlaces en las funciones de Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, cálculo dinámica, sin servidor arquitectura"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="10/27/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Azure funciones desencadenadores y enlaces de referencia del programador


Este tema proporciona referencia general para desencadenadores y enlaces. Incluye algunas de las características avanzadas de enlace y sintaxis compatible con todos los tipos de enlace.  

Si desea obtener información detallada alrededor de configuración y un tipo específico de desencadenador o enlace de codificación, desea haga clic en uno de los desencadenador o enlaces que se muestren en su lugar:

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)] 

Estos artículos se suponen que ha leído la [referencia del programador de funciones de Azure](functions-reference.md)y los artículos de referencia del programador [C#](functions-reference-csharp.md), [F #](functions-reference-fsharp.md)o [Node.js](functions-reference-node.md) .



## <a name="overview"></a>Información general

Desencadenadores son respuestas de evento usadas para desencadenar el código personalizado. Le permiten responder a eventos a través de la plataforma Windows Azure o local. Los enlaces representan los metadatos necesarios usados para conectar su código a la que desee desencadenar o asociado entrada o datos de salida.

Para obtener una idea de las diferentes enlaces que puede integrar con la aplicación de la función de Azure, consulte la tabla siguiente.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]  

Para entender mejor activa y enlaces en general, suponga que desea ejecutar código al proceso de un nuevo elemento se coloca en una cola de almacenamiento de Azure. Funciones de Azure proporciona un desencadenador de cola de Azure que admita esto. ¿Necesita, la siguiente información para supervisar la cola:
 
- La cuenta de almacenamiento donde existe la cola.
- El nombre de la cola.
- Nombre de variable que usaría el código para hacer referencia al elemento nuevo que se coloca en la cola.  
 
Un desencadenador de cola enlace contiene esta información para una función de Azure. El archivo *function.json* para cada función contiene todos los enlaces relacionados.  Aquí tenemos un ejemplo *function.json* que contiene una cola desencadenar enlace. 

    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        }
      ],
      "disabled": false
    }

El código puede enviar distintos tipos de resultado dependiendo de cómo se procesa el elemento de cola nuevo. Por ejemplo, que desea escribir un nuevo registro a una tabla de almacenamiento de Azure.  Para ello, puede configurar un enlace de salida a una tabla de almacenamiento de Azure. Esto es un ejemplo *function.json* que incluye un enlace de salida de tabla de almacenamiento que puede usarse con un desencadenador de cola. 


    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        },
        {
          "type": "table",
          "name": "myNewUserTableBinding",
          "tableName": "newUserTable",
          "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
          "direction": "out"
        }
      ],
      "disabled": false
    }


La función de C# siguiente responde a un nuevo elemento que se coloca en la cola y escribe una nueva entrada de usuario en una tabla de almacenamiento de Azure.

    #r "Newtonsoft.Json"

    using System;
    using Newtonsoft.Json;

    public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                    TraceWriter log)
    {
        // In this example the queue item is a JSON string representing an order that contains the name, 
        // address and mobile number of the new customer.
        dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);
    
        await myNewUserTableBinding.AddAsync(
            new Person() { 
                PartitionKey = "Test", 
                RowKey = Guid.NewGuid().ToString(), 
                Name = order.name,
                Address = order.address,
                MobileNumber = order.mobileNumber }
            );
    }
    
    public class Person
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public string MobileNumber { get; set; }
    }

Para obtener ejemplos de código e información más específica sobre los tipos de almacenamiento de Azure que son compatibles, vea [funciones de Azure desencadenadores y enlaces para el almacenamiento de Azure](functions-bindings-storage.md).


Para usar las características más avanzadas de enlace en el portal de Azure, haga clic en la opción de **editor avanzado** en la pestaña **integración** de la función. El editor avanzado le permite editar el *function.json* directamente en el portal.

## <a name="dynamic-parameter-binding"></a>Enlace de parámetro dinámico 

En lugar de estática configuración para las propiedades de enlace de salida, puede configurar la configuración de datos que forma parte de enlace de entrada de su desencadenador enlazar dinámicamente. Considere la posibilidad de un escenario que los nuevos pedidos se procesan mediante una cola de almacenamiento de Azure. Cada nuevo elemento de cola es una cadena JSON que contiene al menos las siguientes propiedades:

    {
      name : "Customer Name",
      address : "Customer's Address".
      mobileNumber : "Customer's mobile number."
    }

Desea enviar un mensaje de texto SMS con su cuenta de Twilio como una actualización que se ha recibido el orden de cliente.  Puede configurar la `body` y `to` enlace enlazar dinámicamente de salida de campo de la Twilio la `name` y `mobileNumber` que son parte de la entrada de los siguientes.

    {
      "name": "myNewOrderItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newOrders",
      "connection": "orders_STORAGE"
    },
    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "{mobileNumber}",
      "from": "+XXXYYYZZZZ",
      "body": "Thank you {name}, your order was received",
      "direction": "out"
    },
 
Ahora el código de la función sólo tiene iniciar de manera el parámetro de salida. Durante la ejecución de las propiedades de salida se enlazarán a los datos de entrada que desee.

C#

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message variable.
    message = new SMSMessage();

    // When using dynamic parameter binding no need to set this in code.
    // message.body = msg;
    // message.to = myNewOrderItem.mobileNumber

Node.js

    context.bindings.message = {
        // When using dynamic parameter binding no need to set this in code.
        //body : msg,
        //to : myNewOrderItem.mobileNumber
    };




## <a name="random-guids"></a>GUID aleatorios

Funciones de Azure proporciona una sintaxis para generar GUID aleatorios con los enlaces. La siguiente sintaxis enlace escribirá salida a un BLOB nuevo con un nombre único en un contenedor de almacenamiento de Azure: 

    {
      "type": "blob",
      "name": "blobOutput",
      "direction": "out",
      "path": "my-output-container/{rand-guid}"
    }



## <a name="returning-a-single-output"></a>Devolver un resultado único

En casos donde el código de la función devuelve un único resultado, puede usar un enlace de salida denominado `$return` para conservar una firma de la función más natural en el código. Sólo puede utilizarse con idiomas que admiten un valor devuelto (C#, Node.js, F #). El enlace sería similar a la siguiente enlace de salida blob que se utiliza con un desencadenador de cola.

    {
      "bindings": [
        {
          "type": "queueTrigger",
          "name": "input",
          "direction": "in",
          "queueName": "test-input-node"
        },
        {
          "type": "blob",
          "name": "$return",
          "direction": "out",
          "path": "test-output-node/{id}"
        }
      ]
    }


El siguiente código de C# devuelve el resultado más natural sin usar un `out` parámetro en la firma de la función.


    public static string Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }

    // Async example
    public static Task<string> Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }


A continuación se muestra en este mismo enfoque con Node.js.

    module.exports = function (context, input) {
        var json = JSON.stringify(input);
        context.log('Node.js script processed queue message', json);
        context.done(null, json);
    }

Ejemplo de F # proporcionada a continuación.

    let Run(input: WorkItem, log: TraceWriter) =
        let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
        log.Info(sprintf "F# script processed queue message '%s'" json)
        json

Esto también puede utilizarse con varios parámetros de salida al designar un único resultado con `$return`.


## <a name="route-support"></a>Soporte técnico de ruta

De forma predeterminada cuando se crea una función para un desencadenador HTTP o WebHook, la función es accesible con una ruta del formulario:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Puede personalizar esta ruta mediante la opcional `route` el enlace de entrada de propiedad en el desencadenador HTTP. Por ejemplo, el siguiente archivo de *function.json* define un `route` propiedad para un desencadenador de HTTP:

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [ "get" ],
          "route": "products/{category:alpha}/{id:int?}"
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }

Con esta configuración, la función ahora es accesible con la siguiente ruta en lugar de la ruta original.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Esto permite que el código de la función admitir dos parámetros en la dirección `category` y `id`. Puede usar cualquier [Restricción de Web API ruta](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) con los parámetros. El siguiente código de función de C# utiliza ambos parámetros.

    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
                                                    TraceWriter log)
    {
        if (id == null)
           return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
        else
           return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
    }

Aquí es el código de la función Node.js para usar los mismos parámetros de ruta.

    module.exports = function (context, req) {

        var category = context.bindingData.category;
        var id = context.bindingData.id;
    
        if (!id) {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "All " + category + " items were requested."
            };
        }
        else {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: category + " item with id = " + id + " was requested."
            };
        }
    
        context.done();
    } 

De forma predeterminada, todas las rutas de función prefijo con la *api*. También puede personalizar o quitar el prefijo utilizando la `http.routePrefix` propiedad en el archivo *host.json* . En el ejemplo siguiente se quita el prefijo de ruta *api* utilizando una cadena vacía para el prefijo en el archivo *host.json* .

    {
      "http": {
        "routePrefix": ""
      }
    }

Para obtener información detallada sobre cómo actualizar el archivo *host.json* para la función, consulte [cómo actualizar la aplicación de la función archivos](functions-reference.md#fileupdate). 

Al agregar esta configuración, la función ahora es accesible con la siguiente ruta:

    http://<yourapp>.azurewebsites.net/products/electronics/357

Para obtener información sobre otras propiedades que se pueden configurar en el archivo *host.json* , vea [referencia de host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).





## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Prueba una función](functions-test-a-function.md)
* [Ajustar el tamaño de una función](functions-scale.md)
