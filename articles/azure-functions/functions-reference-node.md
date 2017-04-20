<properties
    pageTitle="Referencia del programador de Azure funciones NodeJS | Microsoft Azure"
    description="Entender cómo desarrollar funciones de Azure con NodeJS."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, cálculo dinámica, sin servidor arquitectura"/>

<tags
    ms.service="functions"
    ms.devlang="nodejs"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-nodejs-developer-reference"></a>Referencia del programador de Azure NodeJS de funciones

> [AZURE.SELECTOR]
- [Secuencia de comandos de C#](../articles/azure-functions/functions-reference-csharp.md)
- [Script de F #](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)

La experiencia de nodo/JavaScript para las funciones de Azure facilita exportar una función que se pasa una `context` objeto para comunicarse con el tiempo de ejecución y para recibir y enviar datos a través de enlaces.

En este artículo se supone que ya ha leído la [referencia del programador de funciones de Azure](functions-reference.md).

## <a name="exporting-a-function"></a>Exportar una función

Todas las funciones de JavaScript deben exportar un único `function` a través de `module.exports` para el tiempo de ejecución de la función de buscar y ejecutarla. Esta función siempre debe incluir un `context` objeto.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Enlaces de `direction === "in"` se pasan como argumentos de función, lo que significa que puede usar [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) controlar dinámicamente nuevas entradas (por ejemplo, mediante `arguments.length` a iteración todas sus entradas). Esta funcionalidad es muy útil si solo tiene un desencadenador con sin entradas adicionales, como predecible que puede tener acceso a los datos de desencadenador sin hacer referencia a su `context` objeto.

Los argumentos siempre se pasan a lo largo de la función en el orden en que se producen en *function.json*, incluso si no especifica en la instrucción de exportación. Por ejemplo, si tiene `function(context, a, b)` y cámbielo a `function(context, a)`, aún puede obtener el valor de `b` en el código de la función puede hacer referencia a `arguments[3]`.

Todos los enlaces, independientemente de la dirección, también se pasan a lo largo la `context` objeto (vea a continuación). 

## <a name="context-object"></a>objeto de contexto

El tiempo de ejecución usa un `context` objeto para pasar datos a su función y para permitirle comunicarse con el tiempo de ejecución.

El objeto de contexto siempre es el primer parámetro a una función y siempre deben incluirse porque tiene métodos como `context.done` y `context.log` que se requieren para utilizar correctamente el tiempo de ejecución. Puede denominar al objeto lo que desee (por ejemplo `ctx` o `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>Context.Bindings

La `context.bindings` objeto recopila todos los datos de entrada y salidos. Los datos se agregan a la `context.bindings` objeto a través de la `name` propiedad del enlace. Por ejemplo, dada la siguiente definición de enlace de *function.json*, puede tener acceso el contenido de la cola a través de `context.bindings.myInput`. 

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

La `context.done` función indica el tiempo de ejecución que termine ejecución. Esto es importante para llamar cuando haya terminado con la función; Si no, el tiempo de ejecución aún nunca sabrán que su función se ha completado. 

La `context.done` función le permite devolver un error definido por el usuario para el tiempo de ejecución, así como un contenedor de propiedades de propiedades que se sobrescribirá las propiedades en el `context.bindings` objeto.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>Context.log(Message)

La `context.log` método permite la salida de instrucciones de registro que estén en correspondencia conjuntamente con fines de registro. Si usa `console.log`, los mensajes solo mostrará para el registro de nivel de proceso, que no es tan útil.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

La `context.log` método es compatible con el mismo formato de parámetro que admita el nodo [util.format método](https://nodejs.org/api/util.html#util_util_format_format) . Por lo tanto, por ejemplo, código similar a esta:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

puede escribirse similar a esta:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>Desencadenadores HTTP: context.req y context.res

En el caso de desencadenadores de HTTP, porque es como un patrón común para usar `req` y `res` para los objetos de solicitud y respuesta HTTP, hemos decidido que sea más fácil tener acceso a los que están en el objeto de contexto, en lugar de a usar completo `context.bindings.name` trama.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>Administración de paquete y versión de nodo

La versión de nodo está bloqueada en `5.9.1`. Estamos investigando agregar compatibilidad para versiones más y lo que puede configurar.

Puede incluir paquetes en su función al cargar un archivo de *package.json* a la carpeta de la función sistema de la aplicación de la función de archivos. Archivo de cargar las instrucciones, consulte la sección **cómo actualizar archivos de aplicación de función** del [tema de referencia del programador de funciones de Azure](functions-reference.md#fileupdate). 

También puede usar `npm install` en la interfaz de línea de comandos de la aplicación función SCM (Kudu):

1. Vaya a: `https://<function_app_name>.scm.azurewebsites.net`.

2. Haga clic en **consola de depuración > CMD**.

3. Vaya a `D:\home\site\wwwroot\<function_name>`.

4. Ejecutar `npm install`.

Una vez instalados los paquetes que necesita, importarlos a la función de la manera habitual (es decir, a través de `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## <a name="environment-variables"></a>Variables de entorno

Para obtener una variable de entorno o una aplicación de valor, use `process.env`, como se muestra en el ejemplo siguiente:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    
    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="typescriptcoffeescript-support"></a>Soporte de máquina/CoffeeScript

No hay todavía necesita compatibilidad directa para compilar automática máquina/CoffeeScript mediante el tiempo de ejecución, por lo debería todos corregirse fuera de la ejecución en tiempo de implementación. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Referencia del programador de Azure funciones](functions-reference.md)
* [Referencia del programador de Azure funciones C#](functions-reference-csharp.md)
* [Referencia del programador de Azure funciones F #](functions-reference-fsharp.md)
* [Enlaces y azure desencadenadores de funciones](functions-triggers-bindings.md)
