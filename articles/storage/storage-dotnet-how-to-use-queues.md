<properties
    pageTitle="Introducción a almacenamiento de Azure cola mediante .NET | Microsoft Azure"
    description="Colas Azure proporcionan mensajería confiable y asincrónica entre los componentes de la aplicación. En la nube permite mensajería componentes de la aplicación para escalar de forma independiente."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="robinsh"/>

# <a name="get-started-with-azure-queue-storage-using-net"></a>Introducción a almacenamiento de Azure cola mediante .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Información general

Almacenamiento de cola Azure proporciona mensajería entre componentes de la aplicación de nube. En el diseño de aplicaciones de escala, con qué frecuencia se separan componentes de la aplicación, por lo que puede escalar de manera independiente. Almacenamiento de cola proporciona mensajería asincrónica para la comunicación entre componentes de la aplicación, si se están ejecutando en la nube, en el escritorio, en un servidor local o en un dispositivo móvil. Almacenamiento de cola también admite la administración de tareas asincrónicas y la creación de flujos de trabajo del proceso.

### <a name="about-this-tutorial"></a>Acerca de este tutorial

Este tutorial muestra cómo escribir código .NET para algunos escenarios comunes mediante el almacenamiento de Azure cola. Escenarios cubiertos incluyen crear y eliminar colas y agregar, leer y eliminar la cola de mensajes.

**Tiempo estimado para completar:** 45 minutos

**Requisitos previos:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Biblioteca de cliente de almacenamiento de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Administrador de configuración de Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Una [cuenta de almacenamiento de Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Agregue las declaraciones de espacio de nombres

Agregue las siguientes `using` instrucciones a la parte superior de la `program.cs` archivo:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types

### <a name="parse-the-connection-string"></a>Analizar la cadena de conexión

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Crear al cliente de servicios de cola

La clase **CloudQueueClient** permite recuperar colas almacenadas en cola. Esta es una forma para crear al cliente de servicio:

    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Ahora está listo para escribir código que lee y escribe datos en el almacenamiento de cola.

## <a name="create-a-queue"></a>Crear una cola

Este ejemplo muestra cómo crear una cola si aún no existe:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a container.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-a-message-into-a-queue"></a>Inserte un mensaje de una cola

Para insertar un mensaje en una cola existente, primero, cree un nuevo **CloudQueueMessage**. A continuación, llame al método **AddMessage** . Puede crear un **CloudQueueMessage** desde una cadena (en formato UTF-8) o una matriz de **bytes** . Aquí es el código que crea una cola (si no existe) y se inserta el mensaje 'Hola a todos':

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## <a name="peek-at-the-next-message"></a>Inspeccionar el mensaje siguiente

Puede leer el mensaje de la parte delantera de una cola sin quitarlo de la cola llamando al método **PeekMessage** .

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## <a name="change-the-contents-of-a-queued-message"></a>Cambiar el contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje en lugar de en la cola. Si el mensaje representa una tarea de trabajo, puede usar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de cola con contenido nuevo y establece el tiempo de espera de la visibilidad para extender otro 60 segundos. Esto guarda el estado de trabajo asociado con el mensaje y da al cliente otro minuto para continuar trabajando en el mensaje. Podría usar esta técnica para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes de la cola, sin tener que volver a empezar desde el principio si se produce un error en un paso de procesamiento debido a errores de hardware o software. Normalmente, debería mantener también un número de reintentos y si el mensaje se vuelve a intentar más de *n* veces, debería eliminarlo. Esto protege un mensaje que provoca un error de aplicación cada vez que se procesa.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## <a name="de-queue-the-next-message"></a>Anule la cola el mensaje siguiente

El código anule la cola un mensaje de una cola en dos pasos. Cuando llame a **GetMessage**, recibe el mensaje siguiente de una cola. Se convierte en un mensaje devuelto de **GetMessage** invisible a cualquier otro código leer los mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola, también deberá llamar **DeleteMessage**. Este proceso de dos pasos de la eliminación de un mensaje asegura que si se produce un error en el código procesar un mensaje debido a un error de software o hardware, otra instancia de su código puede obtener el mismo mensaje y vuelva a intentarlo. El código llama **DeleteMessage** derecha después de que el mensaje se ha procesado.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Usar espera asincrónico trama con las API de almacenamiento de cola comunes

En este ejemplo se muestra cómo usar el patrón de espera asincrónico con las API de almacenamiento de cola comunes. El ejemplo llama a la versión asincrónica de cada uno de los métodos determinados, según indica el sufijo *asincrónica* de cada método. Cuando se utiliza un método asincrónico, la asincrónico-espera trama suspende la ejecución local hasta que se complete la llamada. Este comportamiento permite que el subproceso actual realizar otras tareas, lo que ayuda a evitar botella de rendimiento y mejora la capacidad de respuesta general de la aplicación. Para obtener más detalles sobre cómo utilizar el modelo asincrónico espera en .NET vea [asincrónico y espera (C# y Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Aproveche las opciones adicionales para los mensajes en la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola.
En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo término, puede establecer un tiempo de espera de tiempo mayor o menor, permitir que el código más o menos tiempo para procesar cada mensaje por completo. En el ejemplo siguiente se utiliza el método **GetMessages** para recibir mensajes de 20 en una llamada. A continuación, procesa cada mensaje con **un bucle** . También se establece el tiempo de espera de tiempo en cinco minutos para cada mensaje. Tenga en cuenta que se inicia el 5 minutos para todos los mensajes al mismo tiempo, pasa después tiene cinco minutos desde la llamada a **GetMessages**, todos los mensajes que no se ha eliminado estará visible de nuevo.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

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

Puede obtener una estimación del número de mensajes de una cola. El método **FetchAttributes** solicita el servicio de cola para recuperar los atributos de cola, incluidos el número de mensajes. La propiedad **ApproximateMessageCount** devuelve el último valor recuperado por el método **FetchAttributes** , sin llamar al servicio de cola.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-a-queue"></a>Eliminar una cola

Para eliminar una cola y todos los mensajes contenidos en él, llame al método **Delete** en el objeto de cola.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de cola, siga estos vínculos para obtener información sobre las tareas de almacenamiento más complejas.

- Ver la documentación de referencia del servicio de cola para obtener información detallada acerca de la API disponibles:
    - [Biblioteca de cliente de almacenamiento para la referencia de .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Referencia de la API de REST](http://msdn.microsoft.com/library/azure/dd179355)
- Aprenda a simplificar el código que escriba para trabajar con el almacenamiento de Azure mediante el [SDK de WebJobs de Azure](../app-service-web/websites-dotnet-webjobs-sdk.md).
- Ver más características guías para obtener información sobre las opciones adicionales para almacenar los datos en Azure.
    - [Introducción a almacenamiento de tablas de Azure con .NET](storage-dotnet-how-to-use-tables.md) para almacenar datos estructurados.
    - [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md) para almacenar datos no estructurados.
    - [Conectarse a la base de datos de SQL mediante .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) para almacenar datos relacionales.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
