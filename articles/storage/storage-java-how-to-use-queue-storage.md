<properties
    pageTitle="Cómo usar el almacenamiento de la cola de Java | Microsoft Azure"
    description="Obtenga información sobre cómo usar el servicio de cola de Azure para crear y eliminar colas, insertar, obtener y eliminar mensajes. Ejemplos escritos en Java."
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-java"></a>Cómo usar el almacenamiento de la cola de Java

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Información general

Esta guía le mostrará cómo llevar a cabo escenarios comunes mediante el servicio de almacenamiento de cola de Azure. Los ejemplos están escritos en Java y usan el [SDK de almacenamiento de Azure para Java][]. Los escenarios cubiertos incluyen **Insertar**, **leerlo**, **Introducción**y **Eliminar** mensajes de la cola, así como **crear** y **Eliminar** colas. Para obtener más información sobre colas, vea la sección [pasos siguientes](#Next-Steps) .

Nota: Un SDK está disponible para los desarrolladores que usan el almacenamiento de Azure en dispositivos Android. Para obtener más información, consulte el [SDK de almacenamiento de Azure para Android][].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Crear una aplicación de Java

En esta guía, que usa las funciones de almacenamiento que se pueden ejecutar en una aplicación de Java localmente o en el código que se ejecuta dentro de una función web o trabajo en Azure.

Para ello, debe instalar el Kit de desarrollo de Java (JDK) y crear una cuenta de almacenamiento de Azure en la suscripción de Azure. Una vez hecho, deberá comprobar que el sistema de desarrollo cumple los requisitos mínimos y las dependencias que se muestran en el repositorio de [Azure almacenamiento SDK para Java][] en GitHub. Si su sistema cumple estos requisitos, puede seguir las instrucciones para descargar e instalar las bibliotecas de almacenamiento de Azure para Java en su sistema de repositorio. Cuando haya terminado esas tareas, podrá crear una aplicación de Java que utiliza los ejemplos de este artículo.

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar la aplicación para tener acceso al almacenamiento de cola

Agregue las siguientes instrucciones de importación a la parte superior del archivo Java donde desea usar el almacenamiento de Azure API para tener acceso a las colas:

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## <a name="setup-an-azure-storage-connection-string"></a>Configuración de una cadena de conexión de almacenamiento de Azure

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar los extremos y las credenciales para acceder a los servicios de administración de datos. Cuando se ejecuta en una aplicación de cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, con el nombre de su cuenta de almacenamiento y la tecla de acceso principal de la cuenta de almacenamiento aparece en el [Portal de Azure](https://portal.azure.com) para los valores de *nombre de la cuenta* y *AccountKey* . Este ejemplo muestra cómo puede declarar un campo estático para almacenar la cadena de conexión:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

En una aplicación que se ejecuta dentro de una función en Microsoft Azure, esta cadena se pueden almacenar en el archivo de configuración de servicio, *ServiceConfiguration.cscfg*y se puede acceder con una llamada al método **RoleEnvironment.getConfigurationSettings** . Aquí tiene un ejemplo de obtener la cadena de conexión de un elemento de **configuración** denominado *StorageConnectionString* en el archivo de configuración de servicio:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Los ejemplos siguientes, se supone que ha utilizado uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## <a name="how-to-create-a-queue"></a>Cómo: crear una cola

Un objeto **CloudQueueClient** le permite obtener objetos de referencia para colas. El código siguiente crea un objeto **CloudQueueClient** . (Nota: hay otras maneras de crear objetos **CloudStorageAccount** ; para obtener más información, consulte **CloudStorageAccount** en la [Referencia de SDK de cliente de almacenamiento de Azure].)

Utilice el objeto **CloudQueueClient** para obtener una referencia a la cola que desea usar. Puede crear la cola si no existe.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the queue client.
       CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

       // Retrieve a reference to a queue.
       CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Create the queue if it doesn't already exist.
       queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-a-message-to-a-queue"></a>Cómo: agregar un mensaje a una cola

Para insertar un mensaje en una cola existente, primero, cree un nuevo **CloudQueueMessage**. A continuación, llame al método **addMessage** . Puede crear un **CloudQueueMessage** desde una cadena (en formato UTF-8) o una matriz de bytes. Aquí es el código que crea una cola (si no existe) y se inserta el mensaje "Hola a todos".

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Create the queue if it doesn't already exist.
        queue.createIfNotExists();

        // Create a message and add it to the queue.
        CloudQueueMessage message = new CloudQueueMessage("Hello, World");
        queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-peek-at-the-next-message"></a>Cómo: inspeccionar el mensaje siguiente

Puede leer el mensaje de la parte delantera de una cola sin quitarlo de la cola llamando **peekMessage**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Peek at the next message.
        CloudQueueMessage peekedMessage = queue.peekMessage();

        // Output the message value.
        if (peekedMessage != null)
        {
          System.out.println(peekedMessage.getMessageContentAsString());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Cómo: cambiar el contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje en lugar de en la cola. Si el mensaje representa una tarea de trabajo, puede usar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de cola con contenido nuevo y establece el tiempo de espera de la visibilidad para extender otro 60 segundos. Esto guarda el estado de trabajo asociado con el mensaje y da al cliente otro minuto para continuar trabajando en el mensaje. Podría usar esta técnica para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes de la cola, sin tener que volver a empezar desde el principio si se produce un error en un paso de procesamiento debido a errores de hardware o software. Normalmente, debería mantener también un número de reintentos y si el mensaje se vuelve a intentar más de *n* veces, debería eliminarlo. Esto protege un mensaje que provoca un error de aplicación cada vez que se procesa.

El siguiente ejemplo se código busca a través de la cola de mensajes, localiza el primer mensaje que coincide con "Hola a todos" para el contenido, a continuación, modifica el contenido del mensaje y sale de ella.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32.
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields =
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Como alternativa, en el ejemplo de código siguiente actualiza el primer mensaje visible en la cola.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage message = queue.retrieveMessage();

        if (message != null)
        {
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-get-the-queue-length"></a>Cómo: obtener la longitud de cola

Puede obtener una estimación del número de mensajes de una cola. El método **downloadAttributes** solicita el servicio de cola por varios valores actuales, incluido un recuento de la cantidad de mensajes que se encuentra en la cola. El recuento es aproximado porque los mensajes se pueden agregar o quitar después de que el servicio de cola responde a la solicitud. El método **getApproximateMessageCount** devuelve el último valor recuperado por la llamada a **downloadAttributes**, sin llamar al servicio de cola.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Download the approximate message count from the server.
        queue.downloadAttributes();

        // Retrieve the newly cached approximate message count.
        long cachedMessageCount = queue.getApproximateMessageCount();

        // Display the queue length.
        System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-dequeue-the-next-message"></a>Cómo: quitar el mensaje siguiente de la cola

El código elimina un mensaje de una cola en dos pasos. Cuando llame a **retrieveMessage**, recibe el siguiente mensaje de una cola. Se convierte en un mensaje devuelto desde **retrieveMessage** invisible a cualquier otro código leer los mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola, también deberá llamar **deleteMessage**. Este proceso de dos pasos de la eliminación de un mensaje asegura que si se produce un error en el código procesar un mensaje debido a un error de software o hardware, otra instancia de su código puede obtener el mismo mensaje y vuelva a intentarlo. El código llama **deleteMessage** derecha después de que el mensaje se ha procesado.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage retrievedMessage = queue.retrieveMessage();

        if (retrievedMessage != null)
        {
            // Process the message in less than 30 seconds, and then delete the message.
            queue.deleteMessage(retrievedMessage);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## <a name="additional-options-for-dequeuing-messages"></a>Opciones adicionales para estos mensajes

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo término, puede establecer un tiempo de espera de tiempo mayor o menor, permitir que el código más o menos tiempo para procesar cada mensaje por completo.

En el ejemplo siguiente se utiliza el método **retrieveMessages** para recibir mensajes de 20 en una llamada. A continuación, procesa cada mensaje utilizando un bucle **for** . También se establece el tiempo de espera de tiempo en cinco minutos (300 segundos) para cada mensaje. Tenga en cuenta que los cinco minutos se inicia para todos los mensajes al mismo tiempo, así que cuando cinco minutos transcurridos desde la llamada a **retrieveMessages**, todos los mensajes que se han eliminado no estará visibles de nuevo.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes,
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-queues"></a>Cómo: lista de las colas

Para obtener una lista de las colas actuales, llame al método **CloudQueueClient.listQueues()** , que devuelve una colección de objetos **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-queue"></a>Cómo: eliminar una cola

Para eliminar una cola y todos los mensajes contenidos en él, llame al método de **deleteIfExists** en el objeto **CloudQueue** .

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de cola, siga estos vínculos para obtener información sobre las tareas de almacenamiento más complejas.

- [Almacenamiento de Azure SDK de Java][]
- [Referencia SDK de cliente de almacenamiento de Azure][]
- [API de REST de servicios de almacenamiento de Azure][]
- [Blog del equipo de almacenamiento de Azure][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Almacenamiento de Azure SDK de Java]: https://github.com/azure/azure-storage-java
[Almacenamiento de Azure SDK para Android]: https://github.com/azure/azure-storage-android
[Referencia SDK de cliente de almacenamiento de Azure]: http://dl.windowsazure.com/storage/javadoc/
[API de REST de servicios de almacenamiento de Azure]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
