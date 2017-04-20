<properties
    pageTitle="Azure enlaces funciones DocumentDB | Microsoft Azure"
    description="Entender cómo usar enlaces DocumentDB de Azure en las funciones de Azure."
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

# <a name="azure-functions-documentdb-bindings"></a>Azure enlaces DocumentDB de funciones

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y enlaces de Azure DocumentDB código en las funciones de Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="docdbinput"></a>Enlace de entrada de Azure DocumentDB

Enlaces de entrada pueden cargar un documento de una colección de DocumentDB y pasar directamente a su enlace. Puede determinar el identificador de documento basado en el desencadenador que invoca la función. En una función de C#, los cambios realizados en el registro se enviarán automáticamente a la colección cuando sale de la función correctamente.

#### <a name="functionjson-for-documentdb-input-binding"></a>Function.JSON para DocumentDB enlace de entrada

El archivo *function.json* proporciona las siguientes propiedades:

- `name`: Nombre variable utilizada en el código de la función para el documento.
- `type`: debe estar establecida en "documentdb".
- `databaseName`: La base de datos que contiene el documento.
- `collectionName`: La colección que contiene el documento.
- `id`: El identificador del documento a recuperar. Esta propiedad admite enlaces similares a "{queueTrigger}", que utiliza el valor de cadena del mensaje de cola como el Id. de documento
- `connection`: Esta cadena debe ser un conjunto de configuración de la aplicación al extremo de la cuenta DocumentDB. Si elige la cuenta desde la pestaña integrar, se creará una nueva configuración de aplicación para usted con un nombre que tiene el siguiente formato, yourAccount_DOCUMENTDB. Si necesita crear manualmente la configuración de la aplicación, la cadena de conexión real debe tener el formato siguiente, AccountEndpoint =<Endpoint for your account>; AccountKey =<Your primary access key>;.
- «dirección: debe estar establecida en *"in"*.

Ejemplo *function.json*:
 
    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "id" : "{queueTrigger}",
          "connection": "MyAccount_DOCUMENTDB",     
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### <a name="azure-documentdb-input-code-example-for-a-c-queue-trigger"></a>Ejemplo de códigos de entrada DocumentDB Azure para un desencadenador de cola C#
 
Usando la function.json de ejemplo anterior, el enlace de entrada de DocumentDB recuperará el documento con el identificador que coincide con la cadena de mensaje de la cola y pasar al parámetro 'documento'. Si no se encuentra el documento, el parámetro de 'documento' es nulo. El documento, a continuación, se actualiza con el nuevo valor de texto cuando sale de la función.
 
    public static void Run(string myQueueItem, dynamic document)
    {   
        document.text = "This has changed.";
    }

#### <a name="azure-documentdb-input-code-example-for-an-f-queue-trigger"></a>Ejemplo de códigos de entrada DocumentDB Azure para un desencadenador de cola F #

Usando la function.json de ejemplo anterior, el enlace de entrada de DocumentDB recuperará el documento con el identificador que coincide con la cadena de mensaje de la cola y pasar al parámetro 'documento'. Si no se encuentra el documento, el parámetro de 'documento' es nulo. El documento, a continuación, se actualiza con el nuevo valor de texto cuando sale de la función.

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- "This has changed."

Necesitará una `project.json` archivo que utiliza NuGet para especificar el `FSharp.Interop.Dynamic` y `Dynamitey` paquetes como dependencias de paquetes, así:

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

Esto usará NuGet para capturar las dependencias y hará referencia a ellos en la secuencia de comandos.

#### <a name="azure-documentdb-input-code-example-for-a-nodejs-queue-trigger"></a>Ejemplo de códigos de entrada DocumentDB Azure para un desencadenador de cola Node.js
 
Usando la function.json de ejemplo anterior, el enlace de entrada de DocumentDB recuperará el documento con el identificador que coincide con la cadena de mensaje de la cola y pasar a la `documentIn` propiedad binding. En las funciones de Node.js documentos actualizados no se envían a la colección. Sin embargo, puede pasar el enlace de entrada directamente en un resultado de DocumentDB enlace con nombre `documentOut` para admitir actualizaciones. En este ejemplo de código actualiza la propiedad de texto del documento de entrada y la establece como el documento de salida.
 
    module.exports = function (context, input) {   
        context.bindings.documentOut = context.bindings.documentIn;
        context.bindings.documentOut.text = "This was updated!";
        context.done();
    };

## <a id="docdboutput"></a>Azure DocumentDB enlaces de salida

Las funciones pueden escribir JSON documentos a una base de datos de Azure DocumentDB usando el **Documento de Azure DocumentDB** el enlace de salida. Para obtener más información sobre DocumentDB de Azure Revise la [Introducción a DocumentDB](../documentdb/documentdb-introduction.md) y el [tutorial de introducción](../documentdb/documentdb-get-started.md).

#### <a name="functionjson-for-documentdb-output-binding"></a>Function.JSON para DocumentDB el enlace de salida

El archivo function.json proporciona las siguientes propiedades:

- `name`: Nombre variable usado en el código de la función para el nuevo documento.
- `type`: debe estar establecida en *"documentdb"*.
- `databaseName`: La base de datos que contiene la colección donde se creará el nuevo documento.
- `collectionName`: La colección donde se creará el nuevo documento.
- `createIfNotExists`: Este es un valor booleano que indica si la colección se creará si no existe. El valor predeterminado es *false*. La razón para esto es nuevo se crean colecciones con rendimiento reservado, que tiene implicaciones de precios. Para obtener más detalles, visite la [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: Esta cadena debe ser que una **Configuración de la aplicación** se establece en el punto final de la cuenta DocumentDB. Si elige la cuenta desde la pestaña **integrar** , se creará una nueva configuración de aplicación para usted con un nombre que tiene el siguiente formato, `yourAccount_DOCUMENTDB`. Si necesita crear manualmente la configuración de la aplicación, la cadena de conexión real debe tener el formato siguiente, `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction`: debe estar establecida en *"a"*. 
 
Ejemplo function.json:

    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "createIfNotExists": false,
          "connection": "MyAccount_DOCUMENTDB",
          "direction": "out"
        }
      ],
      "disabled": false
    }


#### <a name="azure-documentdb-output-code-example-for-a-nodejs-queue-trigger"></a>Ejemplo de código de salida de Azure DocumentDB para un desencadenador de cola Node.js

    module.exports = function (context, input) {
       
        context.bindings.document = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   
     
        context.done();
    };

El documento de salida:

    {
      "text": "I'm running in a Node function! Data: 'example queue data'",
      "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
    }
 

#### <a name="azure-documentdb-output-code-example-for-an-f-queue-trigger"></a>Ejemplo de código de salida de Azure DocumentDB para un desencadenador de cola F #

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### <a name="azure-documentdb-output-code-example-for-a-c-queue-trigger"></a>Ejemplo de código de salida de Azure DocumentDB para un desencadenador de cola C#


    using System;

    public static void Run(string myQueueItem, out object document, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
       
        document = new {
            text = $"I'm running in a C# function! {myQueueItem}"
        };
    }


#### <a name="azure-documentdb-output-code-example-setting-file-name"></a>Nombre del archivo de Azure DocumentDB resultados código ejemplo configuración

Si desea establecer el nombre del documento en la función, solo tiene que configurar la `id` valor.  Por ejemplo, si se quitó JSON contenido para un empleado en la cola similar al siguiente:

    {
      "name" : "John Henry",
      "employeeId" : "123456",
      "address" : "A town nearby"
    }

Puede utilizar el siguiente código C# en una función de cola desencadenador: 
    
    #r "Newtonsoft.Json"
    
    using System;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        
        dynamic employee = JObject.Parse(myQueueItem);
        
        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }

O el código de F # equivalente:

    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
        id: string
        name: string
        employeeId: string
        address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
        log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
        let employee = JObject.Parse(myQueueItem)
        employeeDocument <-
            { id = sprintf "%s-%s" employee?name employee?employeeId
              name = employee?name
              employeeId = employee?id
              address = employee?address }

Ejemplo de salida:

    {
      "id": "John Henry-123456",
      "name": "John Henry",
      "employeeId": "123456",
      "address": "A town nearby"
    }

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
