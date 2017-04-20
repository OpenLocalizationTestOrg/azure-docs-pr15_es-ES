<properties
    pageTitle="Azure desencadenadores funciones y enlaces para el almacenamiento de Azure | Microsoft Azure"
    description="Entender cómo usar desencadenadores de almacenamiento de Azure y enlaces en las funciones de Azure."
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
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-for-azure-storage"></a>Azure desencadenadores funciones y enlaces para el almacenamiento de Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y desencadenadores de Azure almacenamiento de código y enlaces en las funciones de Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="storagequeuetrigger"></a>Azure desencadenador de cola de almacenamiento

#### <a name="functionjson-for-storage-queue-trigger"></a>Function.JSON desencadenador de cola de almacenamiento

El archivo *function.json* especifica las siguientes propiedades.

- `name`: El nombre de variable que se utiliza en código de la función de la cola o el mensaje de cola. 
- `queueName`: El nombre de la cola de sondeo. Para las reglas de nomenclatura de cola, vea [colas de nomenclatura y metadatos](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: El nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si deja `connection` está vacío, el desencadenador funciona con la cadena de conexión de almacenamiento predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
- `type`: Debe estar establecida en *queueTrigger*.
- `direction`: Debe establecerse para *en*. 

Ejemplo *function.json* para un desencadenador de cola de almacenamiento:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### <a name="queue-trigger-supported-types"></a>Tipos de desencadenador de cola compatibles

El mensaje de cola se puede deserializar a cualquiera de los siguientes tipos:

* Objeto (desde JSON)
* Cadena
* Matriz de bytes 
* `CloudQueueMessage`(C#) 

#### <a name="queue-trigger-metadata"></a>Metadatos de desencadenador de cola

Puede obtener metadatos de cola de la función con estos nombres de variable:

* expirationTime
* insertionTime
* nextVisibleTime
* Id.
* popReceipt
* dequeueCount
* queueTrigger (otra forma de recuperar el texto de mensaje de cola como una cadena)

Este ejemplo de código C# recupera y metadatos de cola de registros:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### <a name="handling-poison-queue-messages"></a>Tratamiento de mensajes dudosos cola

Mensajes cuyo contenido hace que una función errores se denominan *mensajes dudosos*. Cuando se produce un error en la función, el mensaje de cola no se elimina y finalmente recoge nuevamente, causando el ciclo se repita. El SDK puede interrumpir automáticamente el ciclo después de un número limitado de iteraciones o puede hacerlo manualmente.

El SDK llamará a una función hasta 5 veces para procesar un mensaje de cola. Si se produce un error en la quinta try, el mensaje se mueve a una cola de daños.

La cola dudoso se denomina *{originalqueuename}*-dudoso. Puede escribir una función para procesar los mensajes de la cola dudoso mediante registrar ellos o enviar una notificación que atención manual es necesaria. 

Si desea controlar mensajes dudosos manualmente, puede obtener el número de veces que se ha seleccionado un mensaje hacia arriba para procesamiento activando `dequeueCount`.

## <a id="storagequeueoutput"></a>Enlace de salida de cola de almacenamiento de Azure

#### <a name="functionjson-for-storage-queue-output-binding"></a>enlace de salida Function.JSON de cola de almacenamiento

El archivo *function.json* especifica las siguientes propiedades.

- `name`: El nombre de variable que se utiliza en código de la función de la cola o el mensaje de cola. 
- `queueName`: El nombre de la cola. Para las reglas de nomenclatura de cola, vea [colas de nomenclatura y metadatos](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: El nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si deja `connection` está vacío, el desencadenador funciona con la cadena de conexión de almacenamiento predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
- `type`: Debe estar establecida en *cola*.
- `direction`: Debe establecerse a *fuera*. 

Enlace que usa un desencadenador de cola y escribe un mensaje de cola de salida de ejemplo *function.json* de una cola de almacenamiento:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="queue-output-binding-supported-types"></a>Tipos de enlace de salida compatible de cola

La `queue` enlace puede serializar los siguientes tipos de una cola de mensajes:

* Objeto (`out T` en C#, crea un mensaje con un objeto null si el parámetro es null cuando finaliza la función)
* Cadena (`out string` en C#, se crea la cola de mensajes si el valor del parámetro no es nulo cuando finaliza la función)
* Matriz de bytes (`out byte[]` en C#, funciona como cadena) 
* `out CloudQueueMessage`(C#, funciona como cadena) 

En C# también puede enlazar con `ICollector<T>` o `IAsyncCollector<T>` donde `T` es uno de los tipos admitidos.

#### <a name="queue-output-binding-code-examples"></a>Ejemplos de código de enlace de salida de cola

Este ejemplo de código C# escribe un mensaje de cola de salida único para cada mensaje de cola de entrada.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Este ejemplo de código C# escribe varios mensajes mediante `ICollector<T>` (usar `IAsyncCollector<T>` en una función asincrónica):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a>Desencadenador de blobs de Windows Azure de almacenamiento

#### <a name="functionjson-for-storage-blob-trigger"></a>Function.JSON desencadenador de blobs de almacenamiento

El archivo *function.json* especifica las siguientes propiedades.

- `name`: El nombre de variable usado en el código de la función para el blob. 
- `path`: Una ruta de acceso que especifica el contenedor para supervisar y, opcionalmente, un patrón de nombre de blobs de Windows.
- `connection`: El nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si deja `connection` está vacío, el desencadenador funciona con la cadena de conexión de almacenamiento predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
- `type`: Debe estar establecida en *blobTrigger*.
- `direction`: Debe establecerse para *en*.

Ejemplo *function.json* para un desencadenador de blobs de almacenamiento que busca BLOB que se agrega al contenedor de elementos de trabajo de ejemplos:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### <a name="blob-trigger-supported-types"></a>Tipos de desencadenador BLOB compatibles

Puede deserializar el blob a cualquiera de los siguientes tipos de funciones de nodo o C#:

* Objeto (desde JSON)
* Cadena

En las funciones de C# también puede enlazar a cualquiera de los siguientes tipos:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Otros tipos de deserializar [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

#### <a name="blob-trigger-c-code-example"></a>Ejemplo código BLOB desencadenador de C#

Este ejemplo de código C# registra el contenido de cada blob que se agrega al contenedor supervisado.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### <a name="blob-trigger-name-patterns"></a>Patrones de nombre de desencadenador BLOB

Puede especificar un patrón de nombre de blobs de la `path` propiedad. Por ejemplo:

```json
"path": "input/original-{name}",
```

Esta ruta de acceso encontrará un blob llamado *Blob1.txt original* en el contenedor de *entrada* y el valor de la `name` sería variable en el código de la función `Blob1`.

Otro ejemplo:

```json
"path": "input/{blobname}.{blobextension}",
```

Esta ruta de acceso también encontrará un blob llamado *Blob1.txt original*y el valor de la `blobname` y `blobextension` variables en el código de la función sería *Blob1 original* y *txt*.

Puede restringir los tipos de BLOB que activar la función especificando una trama con un valor fijo para la extensión de archivo. Si establece la `path` *a/ejemplos / {nombre} .png*, solo *.png* BLOB en el contenedor de *muestras* activará la función.

Si necesita especificar un patrón de nombre para los nombres de blobs de Windows que tiene llaves en el nombre, duplicar las llaves. Por ejemplo, si desea buscar BLOB en el contenedor de *imágenes* que tengan nombres similar a esta:

        {20140101}-soundfile.mp3

Use esta opción para la `path` propiedad:

        images/{{20140101}}-{name}

En el ejemplo, el `name` valor variable sería *soundfile.mp3*. 

#### <a name="blob-receipts"></a>Confirmaciones de blobs

El tiempo de ejecución de funciones de Azure se asegura de que ninguna función de desencadenador blob se llama varias veces para el mismo blob nuevo o actualizado. Para ello, mantener *confirmaciones de blobs* para determinar si se ha procesado una versión blob determinado.

Confirmaciones de BLOB se almacenan en un contenedor denominado *hosts de webjobs de azure* en la cuenta de almacenamiento de Azure especificada por la cadena de conexión AzureWebJobsStorage. Una confirmación de blobs tiene la información siguiente:

* La función que se denominaba para el blob ("*{nombre de aplicación de la función}*. Funciones. *{nombre de la función}*", por ejemplo:"functionsf74b96f7. Functions.CopyBlob")
* El nombre del contenedor
* El tipo de blob ("BlockBlob" o "PageBlob")
* El nombre de blob
* ETag (un identificador de versión de blobs de Windows, por ejemplo: "0x8D1DC6E70A277EF")

Si desea forzar volver a procesar de un blob, puede eliminar manualmente la confirmación de blobs para blob desde el contenedor de *hosts de webjobs de azure* .

#### <a name="handling-poison-blobs"></a>Tratamiento dudoso BLOB

Cuando se produce un error en una función de desencadenador blobs, el SDK de llamadas de nuevo, en caso de que el error se produjo por un error temporal. Si el error se produce por el contenido del objeto binario, se produce un error en la función cada vez intenta procesar el blob. De forma predeterminada, el SDK llama a una función hasta 5 veces para un blob determinado. Si se produce un error en la quinta try, el SDK agrega un mensaje a una cola denominada *webjobs-blobtrigger-toxicología*.

El mensaje de cola para blobs dudosos es un objeto JSON que contiene las siguientes propiedades:

* FunctionId (en el formato *{nombre de aplicación de la función}*. Funciones. *{nombre de la función}*)
* BlobType ("BlockBlob" o "PageBlob")
* Nombre del contenedor
* BlobName
* ETag (un identificador de versión de blobs de Windows, por ejemplo: "0x8D1DC6E70A277EF")

#### <a name="blob-polling-for-large-containers"></a>Sondeo de grandes contenedores de BLOB

Si el contenedor de blob que supervisa el desencadenador contiene más de 10.000 BLOB, los análisis de tiempo de ejecución de funciones archivos de registro para ver para blobs nuevas o modificadas. Este proceso no está en tiempo real; una función podría no volverse hasta varios minutos o más después de crea el blob. Además, base de [almacenamiento registros se crean en "esfuerzos"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; No hay ninguna garantía de todos los eventos que se va a capturar. En algunas condiciones, es posible que perdido registros. Si las limitaciones de velocidad y confiabilidad de desencadenadores de blobs de grandes contenedores no son aceptables para su aplicación, es el método recomendado crear un mensaje de cola al crear el blob y usar un desencadenador de cola en lugar de un desencadenador de blobs para procesar el blob.
 
## <a id="storageblobbindings"></a>Blobs de Windows Azure almacenamiento de entrada y salida enlaces

#### <a name="functionjson-for-a-storage-blob-input-or-output-binding"></a>Function.JSON para un blob de almacenamiento de entrada o salida enlace

El archivo *function.json* especifica las siguientes propiedades.

- `name`: El nombre de variable usado en el código de la función para el blob. 
- `path`: Una ruta de acceso que especifica el contenedor para leer el blob desde o escribir el blob a y, opcionalmente, un patrón de nombre de blobs.
- `connection`: El nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si deja `connection` está vacío, el enlace funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
- `type`: Debe estar establecida en *blob*.
- `direction`: Establecer a *o de *salida** . 

Ejemplo *function.json* para el almacenamiento de un entrada de manchas o enlace, con un desencadenador de cola para copiar un blob de salida:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="blob-input-and-output-supported-types"></a>Tipos de compatibles de blobs de entrada y salida

La `blob` enlace puede serializar o deserializar los siguientes tipos de funciones Node.js o C#:

* Objeto (`out T` en C# para blobs de salida: crea un blob como objeto null si el valor del parámetro es null cuando finaliza la función)
* Cadena (`out string` en C# para blobs de salida: crea un blob solo si el parámetro de cadena no es nulo cuando se devuelve la función)

En las funciones de C#, también puede enlazar a los siguientes tipos:

* `TextReader`(solo entrada)
* `TextWriter`(solo de salida)
* `Stream`
* `CloudBlobStream`(solo de salida)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### <a name="blob-output-c-code-example"></a>BLOB C# código ejemplo de salida

Este ejemplo de código C# copia un blob cuyo nombre se ha recibido en el mensaje de una cola.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a>Tablas de Azure almacenamiento de entrada y salida enlaces

#### <a name="functionjson-for-storage-tables"></a>Function.JSON para tablas de almacenamiento

El *function.json* especifica las siguientes propiedades.

- `name`: El nombre de variable usado en el código de la función para el enlace de la tabla. 
- `tableName`: El nombre de la tabla.
- `partitionKey`y `rowKey` : utilizar conjuntamente para leer una sola entidad en una función de C# o nodo o escribir una sola entidad en una función de nodo.
- `take`: El número máximo de filas que quiere leer para la tabla de entrada en una función de nodo.
- `filter`: Expresión de filtro OData de tabla en una función de nodo de entrada.
- `connection`: El nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. Si deja `connection` está vacío, el enlace funcionará con la cadena de conexión de almacenamiento predeterminada para la aplicación de la función, que se especifica mediante la configuración de la aplicación AzureWebJobsStorage.
- `type`: Debe estar establecida en *tabla*.
- `direction`: Establecer a *o de *salida** . 

El ejemplo siguiente *function.json* utiliza un desencadenador de cola para leer una sola fila de tabla. El JSON proporciona un valor de clave de partición modificable y especifica que la clave de la fila proviene de la cola de mensajes.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="storage-tables-input-and-output-supported-types"></a>Tipos de tablas de almacenamiento de entrada y salida compatibles

La `table` enlace puede serializar o deserializar objetos en funciones Node.js o C#. Los objetos tendrá propiedades RowKey y PartitionKey. 

En las funciones de C#, también puede enlazar a los siguientes tipos:

* `T`donde `T` implementa`ITableEntity`
* `IQueryable<T>`(solo entrada)
* `ICollector<T>`(solo de salida)
* `IAsyncCollector<T>`(solo de salida)

#### <a name="storage-tables-binding-scenarios"></a>Almacenamiento de tablas de escenarios de enlace

El enlace de tabla admite las situaciones siguientes:

* Leer una sola fila en una función de nodo o C#.

    Establecer `partitionKey` y `rowKey`. La `filter` y `take` propiedades no se usan en este escenario.

* Lea varias filas en una función de C#.

    El tiempo de ejecución de funciones proporciona un `IQueryable<T>` objeto dependiente de la tabla. Tipo de `T` debe derivarse de `TableEntity` o implementar `ITableEntity`. El `partitionKey`, `rowKey`, `filter`, y `take` propiedades no se usan en este escenario; Puede usar el `IQueryable` objeto que haga cualquier filtrado obligatorio. 

* Lea varias filas en una función de nodo.

    Establecer el `filter` y `take` propiedades. No establece `partitionKey` o `rowKey`.

* Escribir una o varias filas en una función de C#.

    El tiempo de ejecución de funciones proporciona un `ICollector<T>` o `IAsyncCollector<T>` enlazado a la tabla donde `T` especifica el esquema de las entidades que desea agregar. Normalmente, escriba `T` se deriva de `TableEntity` o implementa `ITableEntity`, pero no tiene por qué. El `partitionKey`, `rowKey`, `filter`, y `take` propiedades no se usan en este escenario.

#### <a name="storage-tables-example-read-a-single-table-entity-in-c-or-node"></a>Ejemplo de tablas de almacenamiento: leer una entidad única tabla en C# o nodo

El siguiente ejemplo de código de C# funciona con el archivo *function.json* anterior que se muestra una versión anterior de leer una entidad de tabla. El mensaje de cola con el valor de clave de fila y la tabla entidad se lee en un tipo que se define en el archivo *run.csx* . El tipo de incluye `PartitionKey` y `RowKey` propiedades y no se deriva de `TableEntity`. 

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

El siguiente ejemplo de código de F # también funciona con el archivo *function.json* anterior para leer una entidad de la tabla.

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

En el siguiente ejemplo nodo también funciona con el archivo *function.json* anterior para leer una entidad de la tabla.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### <a name="storage-tables-example-read-multiple-table-entities-in-c"></a>Ejemplo de tablas de almacenamiento: leer varias entidades de tabla en C# 

La siguiente *function.json* y C# código entidades de lecturas de ejemplo para una clave de partición especificado en el mensaje de cola.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

El código C# agrega una referencia en el SDK de almacenamiento de Azure para que el tipo de entidad puede derivarse de `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### <a name="storage-tables-example-create-table-entities-in-c"></a>Ejemplo de tablas de almacenamiento: crear entidades de tabla en C# 

El ejemplo siguiente *function.json* y *run.csx* muestra cómo escribir entidades de tabla en C#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### <a name="storage-tables-example-create-table-entities-in-f"></a>Ejemplo de tablas de almacenamiento: crear entidades de tabla en F#

El ejemplo siguiente *function.json* y *run.fsx* muestra cómo escribir entidades de tabla en F #.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### <a name="storage-tables-example-create-a-table-entity-in-node"></a>Ejemplo de tablas de almacenamiento: crear una entidad de tabla de nodo

El ejemplo siguiente *function.json* y *run.csx* muestra cómo escribir una entidad de tabla de nodo.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
