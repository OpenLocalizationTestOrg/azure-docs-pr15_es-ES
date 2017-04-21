<properties
    pageTitle="Introducción a almacenamiento de la cola y Visual Studio conectado servicios (ASP.NET) | Microsoft Azure"
    description="Cómo empezar a usar el almacenamiento en cola de Azure en un proyecto ASP.NET en Visual Studio después de conectar con una cuenta de almacenamiento mediante Visual Studio conectado servicios"
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
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services"></a>Introducción a Azure cola de almacenamiento y Visual Studio conectan servicios

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Información general

Este artículo se describe cómo empezar a usar el almacenamiento en cola de Azure en Visual Studio después de haber creado o hace referencia a una cuenta de almacenamiento de Azure en un proyecto ASP.NET mediante el cuadro de diálogo de Visual Studio **Agregar servicios conectados** .

Le mostraremos cómo crear y tener acceso a una cola de Azure en la cuenta de almacenamiento. También le mostraremos cómo llevar a cabo operaciones de cola básicas, como agregar, modificar, leer y eliminar mensajes de la cola. Los ejemplos se escriben en código C# y usar la [Biblioteca de cliente de Microsoft Azure almacenamiento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Para obtener más información acerca de ASP.NET, consulte [ASP.NET](http://www.asp.net).

Almacenamiento de cola Azure es un servicio para almacenar grandes cantidades de mensajes que pueden tener acceso desde cualquier lugar del mundo a través de llamadas autenticadas mediante HTTP o HTTPS. Mensaje de una sola cola puede ser de tamaño hasta 64 KB y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento.

## <a name="access-queues-in-code"></a>Colas de acceso en el código

Para obtener acceso a colas en proyectos ASP.NET, debe incluir los elementos siguientes a cualquier archivo de origen de C# que tener acceso al almacenamiento de cola de Azure.

1. Asegúrese de que las declaraciones de espacio de nombres en la parte superior del archivo C# incluyen estas instrucciones **using** .

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenga un objeto **CloudStorageAccount** que representa la información de su cuenta de almacenamiento. Utilice el siguiente código para obtener la su cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtener un objeto de **CloudQueueClient** para hacer referencia a los objetos de cola en su cuenta de almacenamiento.  

        // Create the CloudQueueClient object for this storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtener un objeto de **CloudQueue** para hacer referencia a una cola específica.

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**Nota** Utilizar todo el código anterior delante del código en los ejemplos siguientes.

## <a name="create-a-queue-in-code"></a>Crear una cola de código

Para crear una cola de Azure en el código, basta con agregar una llamada a **CreateIfNotExists** para el código anterior.

    // Create the messageQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Agregar un mensaje a una cola

Para insertar un mensaje en una cola existente, cree un nuevo objeto **CloudQueueMessage** , a continuación, llame al método **AddMessage** .

Un objeto **CloudQueueMessage** puede crearse desde una cadena (en formato UTF-8) o una matriz de bytes.

Aquí tenemos un ejemplo que inserta el mensaje 'Hola a todos'.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Leer un mensaje de una cola

Puede leer el mensaje de la parte delantera de una cola sin quitarlo de la cola llamando al método PeekMessage().

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Leer y quitar un mensaje de una cola

Puede quitar su código (anule la cola) un mensaje de una cola en dos pasos.
1. Llamar a GetMessage() para obtener el siguiente mensaje de una cola. Se convierte en un mensaje devuelto desde GetMessage() invisible a cualquier otro código leer los mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos.
2.  Para terminar de quitar el mensaje de la cola, llame a **DeleteMessage**.

Este proceso de dos pasos de la eliminación de un mensaje asegura que si se produce un error en el código procesar un mensaje debido a un error de software o hardware, otra instancia de su código puede obtener el mismo mensaje y vuelva a intentarlo. El siguiente código llama **DeleteMessage** derecha después de que el mensaje se ha procesado.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-for-de-queuing-messages"></a>Utilizar opciones adicionales para los mensajes en la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola.
En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo término, puede establecer un tiempo de espera de tiempo mayor o menor, permitir que el código más o menos tiempo para procesar cada mensaje por completo. En el ejemplo siguiente se utiliza el método **GetMessages** para recibir mensajes de 20 en una llamada. A continuación, procesa cada mensaje con **un bucle** . También se establece el tiempo de espera de tiempo en cinco minutos para cada mensaje. Tenga en cuenta que se inicia el 5 minutos para todos los mensajes al mismo tiempo, pasa después tiene cinco minutos desde la llamada a **GetMessages**, todos los mensajes que no se ha eliminado estará visible de nuevo.

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Obtener la longitud de cola

Puede obtener una estimación del número de mensajes de una cola. El método **FetchAttributes** solicita la queueservice para recuperar los atributos de cola, incluidos el número de mensajes. La propiedad **ApproximateMethodCount** devuelve el último valor recuperado por el método **FetchAttributes** , sin llamar a la queueservice.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-async-await-pattern-with-common-queueapis"></a>Usar trama espera asincrónico con queueAPIs comunes

En este ejemplo se muestra cómo usar el patrón de espera asincrónico con queueAPIs comunes. El ejemplo llama a la versión asincrónica de cada uno de los métodos determinados, se puede ver por la revisión posterior a la asincrónica de cada método. Cuando se utiliza un método asincrónico la asincrónico-espera trama suspende la ejecución local hasta que se complete la llamada. Este comportamiento permite que el subproceso actual realizar otras tareas que ayuda a evitar botella de rendimiento y mejora la capacidad de respuesta general de la aplicación. Para más información sobre cómo utilizar el modelo asincrónico espera en .NET vea [asincrónico y espera (C# y Visual Basic)] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Eliminar una cola

Para eliminar una cola y todos los mensajes contenidos en él, llame al método **Delete** en el objeto de cola.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]