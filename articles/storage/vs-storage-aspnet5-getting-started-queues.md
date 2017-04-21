<properties
    pageTitle="Introducción a almacenamiento de la cola y Visual Studio conectado servicios (5 ASP.NET) | Microsoft Azure"
    description="Cómo empezar a usar el almacenamiento de Azure cola en un proyecto de 5 ASP.NET en Visual Studio"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-5"></a>Introducción a almacenamiento de la cola y Visual Studio conectado servicios (5 ASP.NET)

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

##<a name="overview"></a>Información general

En este artículo se describe cómo empezar a usar el almacenamiento en cola de Azure en Visual Studio después de haber creado o hace referencia a una cuenta de almacenamiento de Azure en un proyecto de 5 ASP.NET mediante el cuadro de diálogo de Visual Studio **Agregar servicios conectados** . La operación de **Agregar servicios conectados** instala los paquetes de NuGet adecuados para tener acceso a almacenamiento de Azure en su proyecto y agrega la cadena de conexión para la cuenta de almacenamiento a sus archivos de configuración de proyecto.

Almacenamiento de cola Azure es un servicio para almacenar grandes cantidades de mensajes que pueden tener acceso desde cualquier lugar del mundo a través de llamadas autenticadas mediante HTTP o HTTPS. Un mensaje de cola puede tener hasta 64 kilobytes (KB) y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento.

Para empezar, debe crear una cola de Azure en su cuenta de almacenamiento. Le mostraremos cómo crear una cola en código. También le mostraremos cómo llevar a cabo operaciones de cola básicas, como agregar, modificar, leer y eliminar mensajes de la cola. Los ejemplos están escritos en C\# código y usar la biblioteca de cliente de almacenamiento de Azure para .NET. Para obtener más información acerca de ASP.NET, consulte [ASP.NET](http://www.asp.net).

**Nota:** Algunas de las API que realizan llamadas a almacenamiento de Azure en 5 ASP.NET son asincrónicas. Para obtener más información, vea [programación asincrónico con asincrónico y espera](http://msdn.microsoft.com/library/hh191443.aspx) . El código siguiente asume que se utilizan métodos de programación asincrónica.

- Para obtener más información sobre la manipulación mediante programación de colas, vea [Introducción a almacenamiento de Azure cola mediante .NET](storage-dotnet-how-to-use-queues.md) .
- Consulte la [documentación de almacenamiento](https://azure.microsoft.com/documentation/services/storage/) para obtener información general sobre el almacenamiento de Azure.
- Consulte la [documentación de servicios de nube](https://azure.microsoft.com/documentation/services/cloud-services/) para obtener información general sobre servicios de nube de Azure.
- Para obtener más información sobre la programación de aplicaciones de ASP.NET, consulte [ASP.NET](http://www.asp.net) .





##<a name="access-queues-in-code"></a>Colas de acceso en el código

Para obtener acceso a colas en proyectos de 5 ASP.NET, debe incluir los elementos siguientes a cualquier archivo de origen C# que tiene acceso a almacenamiento de Azure cola.

1. Asegúrese de que las declaraciones de espacio de nombres en la parte superior del archivo C# incluyen estas instrucciones **using** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenga un objeto **CloudStorageAccount** que representa la información de su cuenta de almacenamiento. Utilice el siguiente código para obtener la su cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtener un objeto de **CloudQueueClient** para hacer referencia a los objetos de cola en su cuenta de almacenamiento.  

        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtener un objeto de **CloudQueue** para hacer referencia a una cola específica.

        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Nota:** Utilizar todo el código anterior delante del código en los ejemplos siguientes.

###<a name="create-a-queue-in-code"></a>Crear una cola de código

Para crear la cola de Azure en el código, basta con agregar una llamada a **CreateIfNotExistsAsync**.

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

##<a name="add-a-message-to-a-queue"></a>Agregar un mensaje a una cola

Para insertar un mensaje en una cola existente, cree un nuevo objeto **CloudQueueMessage** , a continuación, llame al método **AddMessageAsync** .

Un objeto **CloudQueueMessage** puede crearse desde una cadena (en formato UTF-8) o una matriz de bytes.

Aquí tenemos un ejemplo que inserta el mensaje 'Hola a todos'.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

##<a name="read-a-message-in-a-queue"></a>Leer un mensaje de una cola

Puede leer el mensaje de la parte delantera de una cola sin quitarlo de la cola llamando al método **PeekMessageAsync** .

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


##<a name="read-and-remove-a-message-in-a-queue"></a>Leer y quitar un mensaje de una cola

Puede quitar su código (sacar de cola) un mensaje de una cola en dos pasos.
1. Llamar a **GetMessageAsync** para obtener el siguiente mensaje de una cola. Se convierte en un mensaje devuelto desde **GetMessageAsync** invisible a cualquier otro código leer los mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos.
2.  Para terminar de quitar el mensaje de la cola, llame a **DeleteMessageAsync**.

Este proceso de dos pasos de la eliminación de un mensaje asegura que si se produce un error en el código procesar un mensaje debido a un error de software o hardware, otra instancia de su código puede recibirá el mismo mensaje y vuelva a intentarlo. El siguiente código llama **DeleteMessageAsync** derecha después de que el mensaje se ha procesado.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>Aproveche las opciones adicionales para estos mensajes

Hay dos formas de personalizar la recuperación de mensajes de una cola.
En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo término, puede establecer un tiempo de espera de tiempo mayor o menor, permitir que el código más o menos tiempo para procesar cada mensaje por completo. En el ejemplo siguiente se utiliza el método **GetMessages** para recibir mensajes de 20 en una llamada. A continuación, procesa cada mensaje con **un bucle** . También se establece el tiempo de espera de tiempo a 5 minutos para cada mensaje. Observe que el inicio de 5 minutos para todos los mensajes al mismo tiempo, se pasan después tiene cinco minutos después de la llamada a **GetMessages**, todos los mensajes que no se ha eliminado se hacen visibles nuevamente.

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Obtener la longitud de cola

Puede obtener una estimación del número de mensajes de una cola. El método **FetchAttributes** solicita el servicio de cola para recuperar los atributos de cola, incluidos el número de mensajes. La propiedad **ApproximateMethodCount** devuelve el último valor recuperado por el método **FetchAttributes** , sin llamar al servicio de cola.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Usar el patrón de espera asincrónico con las API de cola común

Este ejemplo muestra cómo usar el modelo de espera asincrónica con la API de cola común. El ejemplo llama a la versión asincrónica de cada uno de los métodos determinados. Esto se puede ver por la revisión posterior a la asincrónica de cada método. Cuando se utiliza un método asincrónico, la trama espera asincrónico suspende la ejecución local hasta que se complete la llamada. Este comportamiento permite que el subproceso actual realizar otras tareas que ayuda a evitar botella de rendimiento y mejora la capacidad de respuesta general de la aplicación. Para obtener más detalles sobre cómo utilizar el modelo asincrónico espera en .NET, consulte [asincrónico y espera (C# y Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to add to the queue.
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message.
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## <a name="delete-a-queue"></a>Eliminar una cola

Para eliminar una cola y todos los mensajes contenidos en él, llame al método **Delete** en el objeto de cola.

    // Delete the queue.
    messageQueue.Delete();


##<a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
