<properties
    pageTitle="Funciones de HTTP y webhook los enlaces de Azure | Microsoft Azure"
    description="Entender cómo usar desencadenadores HTTP y webhook y enlaces en las funciones de Azure."
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
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-http-and-webhook-bindings"></a>Funciones de HTTP y webhook los enlaces de Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y programar desencadenadores HTTP y webhook y enlaces en las funciones de Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-http-and-webhook-bindings"></a>Function.JSON para enlaces HTTP y webhook

El archivo *function.json* proporciona propiedades que pertenecen a la solicitud y la respuesta.

Propiedades de la solicitud HTTP:

- `name`: Nombre variable usado en el código de la función para el objeto de solicitud (o el cuerpo de la solicitud en el caso de las funciones de Node.js).
- `type`: Debe estar establecida en *httpTrigger*.
- `direction`: Debe establecerse para *en*. 
- `webHookType`: Para desencadenadores WebHook, los valores válidos son *github*, *genericJson*y *margen de demora*. Para un desencadenador HTTP no es una WebHook, establecer esta propiedad en una cadena vacía. Para obtener más información sobre WebHooks, vea la siguiente sección [desencadenadores WebHook](#webhook-triggers) .
- `authLevel`: No se aplica a desencadenadores WebHook. Establecer a "función" para requerir la clave de API "anónima" para colocar la API de requisito clave, o "administrador" para requerir la clave principal de la API. Para obtener más información, consulte [claves API](#apikeys) debajo.

Propiedades de la respuesta HTTP:

- `name`: Nombre variable utilizada en el código de la función para el objeto de respuesta.
- `type`: Debe estar establecida en *http*.
- `direction`: Debe establecerse a *fuera*. 
 
Ejemplo *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="webhook-triggers"></a>WebHook desencadenadores

Un desencadenador de WebHook es un HTTP que tiene las siguientes funciones diseñadas para WebHooks:

* Para proveedores de WebHook específicos (GitHub y margen de demora están compatible actualmente), el tiempo de ejecución de funciones valida firma del proveedor.
* Para las funciones de Node.js, el tiempo de ejecución de funciones proporciona el cuerpo de la solicitud en lugar del objeto de la solicitud. No hay ningún control especial para las funciones de C#, porque controlar lo que se proporcionan que especifica el tipo de parámetro. Si especifica `HttpRequestMessage` obtener el objeto de la solicitud. Si especifica un tipo POCO, el tiempo de ejecución de funciones intenta analizar un objeto JSON en el cuerpo de la solicitud para rellenar las propiedades del objeto.
* Para desencadenar un WebHook función la solicitud HTTP debe incluir una clave de API. Desencadenadores no WebHook HTTP, este requisito es opcional.

Para obtener información acerca de cómo configurar un GitHub WebHook, vea [GitHub programador - crear WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

## <a name="url-to-trigger-the-function"></a>Dirección URL para desencadenar la función

Para desencadenar una función, enviar una solicitud HTTP a una dirección URL que es una combinación de la URL de aplicación de la función y el nombre de función:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## <a name="api-keys"></a>Claves API

De forma predeterminada, una clave API debe incluir con una solicitud HTTP para desencadenar una función HTTP o WebHook. La clave se puede incluir en una variable de cadena de consulta denominada `code`, o se puede incluir en una `x-functions-key` encabezado HTTP. Para las funciones no WebHook, puede indicar que una clave API no es necesario estableciendo la `authLevel` propiedad "anónimo" en el archivo *function.json* .

Puede encontrar los valores de clave de API en la carpeta *D:\home\data\Functions\secrets* en el sistema de archivos de la aplicación de la función.  La clave principal y una tecla de función se establecen en el archivo *host.json* , tal como se muestra en este ejemplo. 

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

La tecla de función de *host.json* puede utilizarse para desencadenar cualquier función pero no desencadena una función deshabilitada. La clave principal puede utilizarse para desencadenar cualquier función y se activará una función incluso si está deshabilitado. Puede configurar una función para requerir la clave principal estableciendo la `authLevel` propiedad a "administrador". 

Si la carpeta de *información confidencial* contiene un archivo JSON con el mismo nombre que una función, el `key` propiedad en el archivo puede utilizarse también para desencadenar la función y, a continuación, esta clave sólo funcionan con la función que hace referencia. Por ejemplo, la clave de API para una función denominada `HttpTrigger` especificado en *HttpTrigger.json* en la carpeta de *información confidencial* . Aquí tenemos un ejemplo:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Cuando está configurando un desencadenador WebHook, no compartir la clave principal con el proveedor de WebHook. Usar una clave que solo funcionan con la función que procesa el WebHook.  Puede utilizar la clave principal para desencadenar cualquier función incluso deshabilitar funciones.

## <a name="example-c-code-for-an-http-trigger-function"></a>Código de ejemplo C# para una función de desencadenador HTTP 

El código de ejemplo se busca un `name` parámetro en la cadena de consulta o en el cuerpo de la solicitud HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## <a name="example-f-code-for-an-http-trigger-function"></a>Código de ejemplo F # para una función de desencadenador HTTP

El código de ejemplo se busca un `name` parámetro en la cadena de consulta o en el cuerpo de la solicitud HTTP.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Necesitará una `project.json` archivo que usa NuGet para hacer referencia a la `FSharp.Interop.Dynamic` y `Dynamitey` ensamblados, similar a esta:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Esto usará NuGet para capturar las dependencias y hará referencia a ellos en la secuencia de comandos.

## <a name="example-nodejs-code-for-an-http-trigger-function"></a>Código de Node.js de ejemplo para una función de desencadenador HTTP 

El código de este ejemplo se busca un `name` parámetro en la cadena de consulta o en el cuerpo de la solicitud HTTP.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## <a name="example-c-code-for-a-github-webhook-function"></a>Código de ejemplo C# de una función de GitHub WebHook 

Este código de ejemplo registra los comentarios del problema GitHub.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## <a name="example-f-code-for-a-github-webhook-function"></a>Código de ejemplo F # de una función de GitHub WebHook

Este código de ejemplo registra los comentarios del problema GitHub.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## <a name="example-nodejs-code-for-a-github-webhook-function"></a>Código de Node.js de ejemplo de una función de GitHub WebHook 

Este código de ejemplo registra los comentarios del problema GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
