<properties 
    pageTitle="Cómo usar el almacenamiento de Azure cola con el SDK WebJobs" 
    description="Aprenda a usar el almacenamiento de Azure cola con el SDK de WebJobs. Crear y eliminar colas; Insertar, inspeccionar, obtener y eliminar mensajes de la cola y mucho más." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-queue-storage-with-the-webjobs-sdk"></a>Cómo usar el almacenamiento de Azure cola con el SDK WebJobs

## <a name="overview"></a>Información general

Esta guía proporciona ejemplos de código de C# que muestran cómo utilizar la versión de Azure WebJobs SDK 1.x con el servicio de almacenamiento de Azure cola.

La guía se supone que sabe [cómo crear un proyecto WebJob en Visual Studio con las cadenas de conexión que apunten a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md#configure-storage) o a [varias cuentas de almacenamiento](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

La mayoría de los fragmentos de código mostrar solo las funciones, no el código que crea la `JobHost` objeto como en este ejemplo:

        static void Main(string[] args)
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }
        
La guía incluye los siguientes temas:

-   [Cómo activar una función cuando se recibe un mensaje de cola](#trigger)
    - Mensajes de cola de cadena
    - Mensajes de cola POCO
    - Funciones de asincrónica
    - El atributo QueueTrigger funciona con los tipos
    - Algoritmo de sondeo
    - Instancias múltiples
    - Ejecución en paralelo
    - Obtener cola o metadatos de mensaje de cola
    - Se está saliendo
-   [Cómo crear un mensaje de cola al procesar un mensaje de cola](#createqueue)
    - Mensajes de cola de cadena
    - Mensajes de cola POCO
    - Crear varios mensajes o en las funciones asíncronas
    - El atributo de cola funciona con los tipos
    - Usar atributos de WebJobs SDK en el cuerpo de una función
-   [Cómo leer y escribir datos BLOB al procesar un mensaje de cola](#blobs)
    - Mensajes de cola de cadena
    - Mensajes de cola POCO
    - El atributo Blob funciona con los tipos
-   [Cómo manejar mensajes dudosos](#poison)
    - Control de mensajes dudosos automática
    - Control de mensajes dudosos manual
-   [Cómo configurar las opciones de configuración](#config)
    - Establecer las cadenas de conexión de SDK en el código
    - Configurar las opciones de QueueTrigger
    - Establecer los valores para SDK WebJobs parámetros de constructor en el código
-   [Cómo activar una función de forma manual](#manual)
-   [Cómo escribir registros](#logs) 
-   [Cómo controlar los errores y configurar los tiempos de espera](#errors)
-   [Pasos siguientes](#nextsteps)

## <a id="trigger"></a>Cómo activar una función cuando se recibe un mensaje de cola

Para escribir una función que el SDK WebJobs llama cuando se recibe un mensaje de cola, utilice el `QueueTrigger` atributo. El constructor de atributos tiene un parámetro de cadena que especifica el nombre de la cola de sondeo. También puede [establecer el nombre de cola dinámicamente](#config).

### <a name="string-queue-messages"></a>Mensajes de cola de cadena

En el ejemplo siguiente, la cola contiene un mensaje de cadena, así que `QueueTrigger` se aplica a un parámetro de cadena denominado `logMessage` que contiene el contenido del mensaje de cola. La función [escribe un mensaje de registro al escritorio](#logs).
 

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

Además `string`, el parámetro puede ser una matriz de bytes una `CloudQueueMessage` objeto o un POCO que defina.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(sin formato objeto CLR antiguo](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) mensajes en cola

En el ejemplo siguiente, el mensaje de cola contiene JSON para un `BlobInformation` objeto que incluye una `BlobName` propiedad. El SDK automáticamente deserializa el objeto.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

El SDK utiliza el [paquete Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) para serializar y deserializar mensajes. Si se crea la cola de mensajes en un programa que no usa el SDK WebJobs, puede escribir código como el siguiente ejemplo para crear un mensaje de cola POCO que puede analizar el SDK. 

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Funciones de asincrónica

La siguiente asincrónico función [escribe un registro en el panel](#logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Funciones asíncronas pueden tardar un [token de cancelación](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), tal como se muestra en el ejemplo siguiente que copia un blob. (Para obtener una explicación de la `queueTrigger` marcador de posición, vea la sección [BLOB](#blobs) .)

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName, 
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

### <a id="qtattributetypes"></a>El atributo QueueTrigger funciona con los tipos

Puede usar `QueueTrigger` con los siguientes tipos:

* `string`
* Un tipo POCO serializado como JSON
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a>Algoritmo de sondeo

El SDK implementa un aleatorio exponencial interrupción algoritmo para reducir el efecto de sondeo en los costos de transacción de almacenamiento de cola de inactividad.  Cuando se encuentra un mensaje, el SDK espera dos segundos y, a continuación, comprueba si hay otro mensaje; Cuando se encuentra ningún mensaje espera unos cuatro segundos antes de intentar nuevo. Después de intentos subsiguientes para obtener un mensaje de cola, el tiempo de espera sigue aumentando hasta que alcance el tiempo de espera máximo predeterminado es un minuto. [El tiempo de espera máximo es configurable](#config).

### <a id="instances"></a>Instancias múltiples

Si la aplicación web se ejecuta en varias instancias, un WebJob continuo se ejecuta en cada equipo y cada equipo se espere desencadenadores e intente ejecutar funciones. El desencadenador de cola WebJobs SDK automáticamente impide que una función de procesamiento de un mensaje de cola varias veces; funciones no deben escribirse con idempotente. Sin embargo, si desea asegurarse de que solo una instancia de una función que se ejecuta incluso cuando hay varias instancias de la aplicación web de host, puede usar el `Singleton` atributo. 

### <a id="parallel"></a>Ejecución en paralelo

Si tiene varias funciones escucha en distintas colas, el SDK llamará ellos en paralelo cuando se reciban mensajes al mismo tiempo. 

El mismo sucede cuando se reciben varios mensajes de una sola cola. De forma predeterminada, el SDK Obtiene un lote de 16 mensajes en cola en un momento y ejecuta la función que procesa en paralelo. [El tamaño del lote es configurable](#config). Cuando el número que se está procesando obtiene hacia abajo a la mitad del tamaño del lote, el SDK obtiene otro lote y comienza a procesar los mensajes. Por lo tanto, el número máximo de mensajes simultáneos que se procesan por función es el tamaño del lote una vez y medio. Este límite se aplica por separado a cada función que tiene un `QueueTrigger` atributo. 

Si no quiere ejecución en paralelo de los mensajes recibidos de una cola, puede establecer el tamaño del lote a 1. Vea también **más control sobre el procesamiento de cola** en [Azure WebJobs SDK 1.1.0 RTM](/blog/azure-webjobs-sdk-1-1-0-rtm/).

### <a id="queuemetadata"></a>Obtener cola o metadatos de mensaje de cola

Puede obtener las siguientes propiedades de mensaje al agregar parámetros a la firma del método:

* `DateTimeOffset`expirationTime
* `DateTimeOffset`insertionTime
* `DateTimeOffset`nextVisibleTime
* `string`queueTrigger (contiene el texto del mensaje)
* `string`Id.
* `string`popReceipt
* `int`dequeueCount

Si desea trabajar directamente con la API de almacenamiento de Azure, también puede agregar un `CloudStorageAccount` parámetro.

En el ejemplo siguiente se escribe todos estos metadatos a un registro de información de la aplicación. En el ejemplo, logMessage y queueTrigger contienen el contenido del mensaje de cola.

        public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
            DateTimeOffset expirationTime,
            DateTimeOffset insertionTime,
            DateTimeOffset nextVisibleTime,
            string id,
            string popReceipt,
            int dequeueCount,
            string queueTrigger,
            CloudStorageAccount cloudStorageAccount,
            TextWriter logger)
        {
            logger.WriteLine(
                "logMessage={0}\n" +
            "expirationTime={1}\ninsertionTime={2}\n" +
                "nextVisibleTime={3}\n" +
                "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
                "queue endpoint={7} queueTrigger={8}",
                logMessage, expirationTime,
                insertionTime,
                nextVisibleTime, id,
                popReceipt, dequeueCount,
                cloudStorageAccount.QueueEndpoint,
                queueTrigger);
        }

Esto es un ejemplo de registro de escribiendo el código de ejemplo:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

### <a id="graceful"></a>Se está saliendo

Puede aceptar una función que se ejecuta en una continua WebJob una `CancellationToken` parámetro que permite que el sistema operativo notificar a la función cuando la WebJob está a punto de terminar. Puede usar esta notificación para asegurarse de que la función no finalizar inesperadamente de manera que deja los datos en un estado incoherente.

En el ejemplo siguiente se muestra cómo revisar la próxima terminación WebJob en una función.

    public static void GracefulShutdownDemo(
                [QueueTrigger("inputqueue")] string inputText,
                TextWriter logger,
                CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(1000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }

**Nota:** El panel no puede mostrar correctamente el estado y el resultado de las funciones que ha cerrado.
 
Para obtener más información, vea [WebJobs está saliendo](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a>Cómo crear un mensaje de cola al procesar un mensaje de cola

Para escribir una función que crea un nuevo mensaje de cola, utilice el `Queue` atributo. Como `QueueTrigger`, pase el nombre de cola como una cadena o puede [establecer el nombre de cola dinámicamente](#config).

### <a name="string-queue-messages"></a>Mensajes de cola de cadena

En el ejemplo de código siguiente no asincrónica crea un nuevo mensaje de cola en la cola denominada "outputqueue" con el mismo contenido que el mensaje de cola recibido en la cola denominada "inputqueue". (Para asincrónico usan funciones `IAsyncCollector<T>` tal como se muestra más adelante en esta sección.)


        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }
  
### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(sin formato objeto CLR antiguo](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) mensajes en cola

Para crear un mensaje de cola que contiene un POCO en lugar de una cadena, pase el tipo POCO como un parámetro de salida a la `Queue` constructor de atributos.
 
        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

El SDK serializa automáticamente el objeto en JSON. Siempre se crea un mensaje de cola, incluso si el objeto es nulo.

### <a name="create-multiple-messages-or-in-async-functions"></a>Crear varios mensajes o en las funciones asíncronas

Para crear varios mensajes, convierta el tipo de parámetro para la cola de salida `ICollector<T>` o `IAsyncCollector<T>`, como se muestra en el ejemplo siguiente.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Cada mensaje de cola se crea inmediatamente cuando el `Add` se denomina método.

### <a name="types-that-the-queue-attribute-works-with"></a>Tipos de que el atributo de cola funciona con

Puede usar el `Queue` atributo en los siguientes tipos de parámetro:

* `out string`(si el valor del parámetro no es nulo cuando finaliza la función se crea el mensaje de cola)
* `out byte[]`(funciona como `string`) 
* `out CloudQueueMessage`(funciona como `string`) 
* `out POCO`(un tipo serializable, crea un mensaje con un objeto null si el parámetro es null cuando finaliza la función)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue`(para crear mensajes manualmente con la API de almacenamiento de Azure directamente)

### <a id="ibinder"></a>Usar atributos de WebJobs SDK en el cuerpo de una función

Si necesita trabajar un poco en la función antes de utilizar un atributo WebJobs SDK como `Queue`, `Blob`, o `Table`, puede usar el `IBinder` interfaz.

En el ejemplo siguiente se toma el mensaje de una cola de entrada y crea un nuevo mensaje con el mismo contenido de una cola de salida. El nombre de la cola de salida se establece mediante código en el cuerpo de la función.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

La `IBinder` interfaz también pueden utilizarse con la `Table` y `Blob` atributos.

## <a id="blobs"></a>Cómo leer y escribir tablas y BLOB al procesar un mensaje de cola

La `Blob` y `Table` atributos le permiten leer y escribir BLOB y tablas. Los ejemplos de esta sección se aplican a BLOB. Para obtener ejemplos de código que muestran cómo desencadenar procesos cuando se crea o actualiza BLOB, vea [cómo usar el almacenamiento de blobs de Windows Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)y para obtener ejemplos de código que leer y escribir tablas, vea [cómo usar el almacenamiento de tablas Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### <a name="string-queue-messages-triggering-blob-operations"></a>Mensajes de cola desencadenante blob operaciones de cadena

Para un mensaje de cola que contiene una cadena `queueTrigger` es un marcador de posición que puede usar en la `Blob` del atributo `blobPath` parámetro que contiene el contenido del mensaje. 

En el ejemplo siguiente se utiliza `Stream` objetos para leer y escribir datos BLOB. El mensaje de cola es el nombre de un blob que se encuentra en el contenedor textblobs. Una copia del objeto binario con "-nuevo" anexado a se crea el nombre en el mismo contenedor. 

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName, 
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

La `Blob` atributo constructor tiene un `blobPath` parámetro que especifica el nombre del contenedor y blob. Para obtener más información acerca de este marcador de posición, consulte [cómo usar el almacenamiento de blobs de Windows Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), 

Cuando el atributo decora un `Stream` objeto, otro parámetro de constructor especifica la `FileAccess` modo de lectura, escritura o lectura y escritura. 

En el ejemplo siguiente se utiliza un `CloudBlockBlob` objeto para eliminar un blob. El mensaje de cola es el nombre del objeto binario.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a id="pocoblobs"></a>POCO [(sin formato objeto CLR antiguo](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) mensajes en cola

Para un POCO almacenado como JSON en la cola de mensajes, puede utilizar marcadores de posición de nombres de las propiedades del objeto en el `Queue` del atributo `blobPath` parámetro. También puede usar los [nombres de propiedad de metadatos de cola](#queuemetadata) como marcadores de posición. 

En el ejemplo siguiente se copia un blob a un nuevo blob con una extensión diferente. El mensaje de cola es una `BlobInformation` objeto que incluye `BlobName` y `BlobNameWithoutExtension` propiedades. Los nombres de propiedad se usan como marcadores de posición en la ruta de acceso de blobs para la `Blob` atributos. 
 
        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

El SDK utiliza el [paquete Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) para serializar y deserializar mensajes. Si se crea la cola de mensajes en un programa que no usa el SDK WebJobs, puede escribir código como el siguiente ejemplo para crear un mensaje de cola POCO que puede analizar el SDK.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Si necesita trabajar un poco en la función antes de un blob de enlace a un objeto, puede usar el atributo en el cuerpo de la función, [tal como se muestra anteriormente para el atributo de cola](#ibinder).

### <a id="blobattributetypes"></a>Puede usar el atributo Blob con los tipos
 
La `Blob` atributo puede usarse con los siguientes tipos:

* `Stream`(lectura o escritura, especificada mediante el parámetro de constructor FileAccess)
* `TextReader`
* `TextWriter`
* `string`(más)
* `out string`(escriba; crea un blob solo si el parámetro de cadena no es nulo cuando se devuelve la función)
* POCO (lectura)
* los POCO (escribir; siempre crea un blob, crea como objeto nulo si POCO parámetro es null cuando la función devuelve)
* `CloudBlobStream`(escritura)
* `ICloudBlob`(lectura o escritura)
* `CloudBlockBlob`(lectura o escritura) 
* `CloudPageBlob`(lectura o escritura) 

## <a id="poison"></a>Cómo manejar mensajes dudosos

Mensajes cuyo contenido hace que una función errores se denominan *mensajes dudosos*. Cuando se produce un error en la función, el mensaje de cola no se elimina y finalmente recoge nuevamente, causando el ciclo se repita. El SDK puede interrumpir automáticamente el ciclo después de un número limitado de iteraciones o puede hacerlo manualmente.

### <a name="automatic-poison-message-handling"></a>Control de mensajes dudosos automática

El SDK llamará a una función hasta 5 veces para procesar un mensaje de cola. Si se produce un error en la quinta try, el mensaje se mueve a una cola de daños. [El número máximo de reintentos es configurable](#config). 

La cola dudoso se denomina *{originalqueuename}*-dudoso. Puede escribir una función para procesar los mensajes de la cola dudoso mediante registrar ellos o enviar una notificación que atención manual es necesaria. 

En el ejemplo siguiente la `CopyBlob` función se producirá un error cuando un mensaje de cola contiene el nombre de un objeto binario que no existe. Cuando esto ocurre, el mensaje se mueve de la cola de copyblobqueue a la cola de toxicología copyblobqueue. La `ProcessPoisonMessage` luego registre el mensaje erróneo.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }
        
        public static void ProcessPoisonMessage(
            [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
        {
            logger.WriteLine("Failed to copy blob, name=" + blobName);
        }

La ilustración siguiente muestra el resultado de la consola de estas funciones cuando se procesa un mensaje dudoso.

![Resultado de la consola de control de mensajes dudosos](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### <a name="manual-poison-message-handling"></a>Control de mensajes dudosos manual

Puede obtener el número de veces que se ha seleccionado un mensaje hacia arriba para procesamiento agregando una `int` parámetro denominado `dequeueCount` a la función. A continuación, puede comprobar el recuento de quitar en el código de la función y realizar su propio tratamiento de mensajes dudosos cuando el número supera un umbral, tal como se muestra en el ejemplo siguiente.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
            TextWriter logger)
        {
            if (dequeueCount > 3)
            {
                logger.WriteLine("Failed to copy blob, name=" + blobName);
            }
            else
            {
            blobInput.CopyTo(blobOutput, 4096);
            }
        }

## <a id="config"></a>Cómo configurar las opciones de configuración

Puede usar el `JobHostConfiguration` tipo para establecer las siguientes opciones de configuración:

* Establecer las cadenas de conexión de SDK en código.
* Configurar `QueueTrigger` configuración como máximo quitar cola recuento.
* Obtener los nombres de cola de configuración.

### <a id="setconnstr"></a>Establecer las cadenas de conexión de SDK en el código

Establecer las cadenas de conexión de SDK en código le permite utilizar sus propios nombres de cadena de conexión en archivos de configuración o variables de entorno, tal como se muestra en el ejemplo siguiente.

        static void Main(string[] args)
        {
            var _storageConn = ConfigurationManager
                .ConnectionStrings["MyStorageConnection"].ConnectionString;
        
            var _dashboardConn = ConfigurationManager
                .ConnectionStrings["MyDashboardConnection"].ConnectionString;
        
            var _serviceBusConn = ConfigurationManager
                .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
        
            JobHostConfiguration config = new JobHostConfiguration();
            config.StorageConnectionString = _storageConn;
            config.DashboardConnectionString = _dashboardConn;
            config.ServiceBusConnectionString = _serviceBusConn;
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a id="configqueue"></a>Configurar las opciones de QueueTrigger

Puede configurar las siguientes opciones que se aplican al procesamiento de mensaje de cola:

- El número máximo de mensajes que se seleccionan al mismo tiempo que se pueden ejecutar en paralelo (el valor predeterminado es 16).
- El número máximo de reintentos antes de enviar un mensaje de cola a una cola de daños (5 de forma predeterminada).
- El número máximo de espera antes de sondear nuevo cuando una cola está vacía (el valor predeterminado es 1 minuto).

En el ejemplo siguiente se muestra cómo configurar estas opciones:

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a id="setnamesincode"></a>Establecer los valores para SDK WebJobs parámetros de constructor en el código

A veces desea especificar un nombre de cola, un nombre de blob o contenedor o una tabla en código en lugar de codificar asígnele el nombre. Por ejemplo, desea especificar el nombre de cola de `QueueTrigger` en una variable de entorno o de archivo de configuración. 

Puede hacerlo pasando un `NameResolver` objeto a la `JobHostConfiguration` tipo. Incluir marcadores de posición especiales rodeadas por signos de porcentaje (%) en los parámetros de constructor de atributo WebJobs SDK y su `NameResolver` código especifica los valores reales en lugar de los marcadores de posición.

Por ejemplo, suponga que desea usar una cola denominada logqueuetest en el entorno de prueba y una logqueueprod con nombre en producción. En lugar de un nombre de cola modificable, que desea especificar el nombre de una entrada de la `appSettings` colección que debe tener el nombre de cola real. Si el `appSettings` clave es logqueue, la función puede tener un aspecto similar al ejemplo siguiente.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

Su `NameResolver` clase a continuación, puede obtener el nombre de cola de `appSettings` tal como se muestra en el ejemplo siguiente:

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

Pase el `NameResolver` en la clase para la `JobHost` objeto como se muestra en el ejemplo siguiente.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }
 
**Nota:** Cola, nombres de tabla y blob se resuelven cada vez que se llama una función, pero se resuelven los nombres de contenedor de blob solo cuando se inicia la aplicación. No puede cambiar el nombre del contenedor de blob mientras se está ejecutando el trabajo. 

## <a id="manual"></a>Cómo activar una función de forma manual

Para activar manualmente una función, utilice la `Call` o `CallAsync` método en la `JobHost` objeto y la `NoAutomaticTrigger` atributo en la función, tal como se muestra en el ejemplo siguiente. 

        public class Program
        {
            static void Main(string[] args)
            {
                JobHost host = new JobHost();
                host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
            }
        
            [NoAutomaticTrigger]
            public static void CreateQueueMessage(
                TextWriter logger, 
                string value, 
                [Queue("outputqueue")] out string message)
            {
                message = value;
                logger.WriteLine("Creating queue message: ", message);
            }
        }

## <a id="logs"></a>Cómo escribir registros

El panel muestra registros en dos lugares: la página de la WebJob y la página de una invocación WebJob particular. 

![Inicie sesión en la página WebJob](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Inicie sesión en la página de invocación de función](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Resultado de métodos de consola que llame a una función o en la `Main()` método aparece en la página de panel para el WebJob, no en la página de invocación de un método determinado. Resultado del objeto TextWriter que recibe de un parámetro de la firma de método aparece en la página de panel para invocar un método.

Resultado de la consola no se pueden vincular a una llamada de método concreto porque la consola tiene un único subproceso, mientras muchas de las funciones de trabajo pueden estar ejecutándose al mismo tiempo. Por eso el SDK proporciona cada invocación de la función con su propio objeto de escritor de registro único.

Para escribir [los registros de seguimiento de aplicación](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), utilice `Console.Out` (crea registros marcados como información) y `Console.Error` (crea registros marcados como ERROR). Una alternativa es usar el [seguimiento o TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), que proporciona niveles detallado, advertencia y crítico además de información y de Error. Registros de seguimiento de aplicación aparecen en los archivos de registro de aplicación web, las tablas de Azure, o blobs de Azure dependiendo de cómo configure la aplicación web de Azure. Como ocurre con todos los resultados de la consola, los registros de 100 aplicaciones más recientes también aparecerán en la página de panel para el WebJob, no a la página para una llamada a función. 

Resultado de la consola aparece en el panel únicamente si el programa se ejecuta en una WebJob de Azure, no si está ejecutando el programa localmente o en otro entorno.

Deshabilitar el registro de paneles para escenarios de alto rendimiento. De forma predeterminada, el SDK escribe registros en el almacenamiento y, a continuación, esta actividad puede degradar el rendimiento cuando se procesan muchos mensajes. Para deshabilitar el registro, establezca la cadena de conexión de panel en un valor nulo como se muestra en el ejemplo siguiente.

        JobHostConfiguration config = new JobHostConfiguration();       
        config.DashboardConnectionString = "";        
        JobHost host = new JobHost(config);
        host.RunAndBlock();

En el ejemplo siguiente se muestra varias maneras de escribir registros:

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

En el panel de SDK WebJobs, el resultado de la `TextWriter` objeto se muestra cuando vaya a la página para una determinada función invocación de funciones y haga clic en **Conmutar salida**:

![Haga clic en el vínculo de invocación de función](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Inicie sesión en la página de invocación de función](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

En el panel de SDK WebJobs, las líneas de 100 más recientes de la consola de habían salida mostrar hacia arriba cuando vaya a la página de la WebJob (no disponible para la invocación de la función) y haga clic en **Los resultados de alternancia**.
 
![Haga clic en los resultados de alternancia](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

En un WebJob continuo, registros de la aplicación aparecerán en/datos/trabajos/continua /*{webjobname}*/job_log.txt en el sistema de archivos de aplicación web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

En un Azure blob la aplicación registros tienen este aspecto: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hola a todos!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hola a todos!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hola a todos!,

Y en una tabla de Azure la `Console.Out` y `Console.Error` registros tiene este aspecto:

![Registro de información de tabla](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Registro de errores de tabla](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

Si desea conectar su propio registrador, vea [este ejemplo](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Program.cs).

## <a id="errors"></a>Cómo controlar los errores y configurar los tiempos de espera

El SDK de WebJobs también incluye un [tiempo de espera de](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs) atributo que puede usar para hacer que la función que se pueden cancelar si no se completa en un período de tiempo especificado. Y si desea generar una alerta cuando se producen muchos errores dentro de un período de tiempo especificado, puede usar el `ErrorTrigger` atributo. Aquí tenemos un [ejemplo de ErrorTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Error-Monitoring).

```
public static void ErrorMonitor(
[ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
[SendGrid(
    To = "admin@emailaddress.com",
    Subject = "Error!")]
 SendGridMessage message)
{
    // log last 5 detailed errors to the Dashboard
   log.WriteLine(filter.GetDetailedMessage(5));
   message.Text = filter.GetDetailedMessage(1);
}
```

También dinámicamente puede deshabilitar y habilitar funciones de control, si bien pueden ser desencadenados, mediante un modificador de configuración que podría ser una configuración de aplicación o el nombre de variable de entorno. Código de ejemplo, consulte la `Disable` atributo en [el repositorio de muestras WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs).

## <a id="nextsteps"></a>Pasos siguientes

Esta guía proporciona ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con colas de Azure. Para obtener más información sobre cómo usar WebJobs de Azure y el SDK WebJobs, vea [Azure WebJobs recomienda recursos](http://go.microsoft.com/fwlink/?linkid=390226).
 
