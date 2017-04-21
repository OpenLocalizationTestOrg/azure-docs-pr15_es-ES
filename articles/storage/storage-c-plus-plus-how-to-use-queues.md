<properties
    pageTitle="Cómo usar el almacenamiento de cola (C++) | Microsoft Azure"
    description="Obtenga información sobre cómo usar el servicio de almacenamiento de cola en Azure. Ejemplos están escritos en C++."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-queue-storage-from-c"></a>Cómo usar el almacenamiento de la cola de C++  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Información general
Esta guía le mostrará cómo llevar a cabo escenarios comunes mediante el servicio de almacenamiento de cola de Azure. Los ejemplos están escritos en C++ y usar la [Biblioteca de cliente de almacenamiento de Azure para C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Los escenarios cubiertos incluyen **Insertar**, **leerlo**, **Introducción**y **Eliminar** mensajes de la cola, así como **crear y eliminar colas**.

>[AZURE.NOTE] Esta guía está destinado a la biblioteca de cliente de almacenamiento de Azure para C++ versión 1.0.0 y superiores. La versión recomendada es biblioteca de cliente de almacenamiento 2.2.0, que está disponible a través de [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](http://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Crear una aplicación de C++  
En esta guía, que usa las funciones de almacenamiento que se pueden ejecutar en una aplicación de C++.

Para ello, debe instalar la biblioteca de cliente de almacenamiento de Azure para C++ y cree una cuenta de almacenamiento de Azure en la suscripción de Azure.

Para instalar la biblioteca de cliente de almacenamiento de Azure para C++, puede usar los métodos siguientes:

-   **Linux:** Siga las instrucciones proporcionadas en la página de la [Biblioteca de cliente de almacenamiento de Azure para C++ Léame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
-   **Windows:** En Visual Studio, haga clic en **Herramientas > Administrador de paquetes de NuGet > consola del Administrador de paquete**. Escriba el comando siguiente en la [consola del Administrador de paquetes de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) y presione **ENTRAR**.

        Install-Package wastorage

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar la aplicación para tener acceso al almacenamiento de cola
Agregue que las siguientes instrucciones a la parte superior del archivo C++ donde desea usar el almacenamiento de Azure API para tener acceso a colas include:  

    #include "was/storage_account.h"
    #include "was/queue.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar una cadena de conexión de almacenamiento de Azure

Un cliente de almacenamiento de Azure utiliza una cadena de conexión de almacenamiento para almacenar los extremos y las credenciales para acceder a los servicios de administración de datos. Cuando se ejecuta en una aplicación de cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, con el nombre de su cuenta de almacenamiento y la tecla de acceso de almacenamiento de la cuenta de almacenamiento aparece en el [Portal de Azure](https://portal.azure.com) para los valores de *nombre de la cuenta* y *AccountKey* . Para obtener información sobre las cuentas de almacenamiento y las teclas de acceso, vea [Acerca de cuentas de almacenamiento de Azure](storage-create-storage-account.md). Este ejemplo muestra cómo puede declarar un campo estático para almacenar la cadena de conexión:  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para probar la aplicación en el equipo local de Windows, puede usar Microsoft Azure [emulador de almacenamiento](storage-use-emulator.md) que se instala con el [SDK de Azure](https://azure.microsoft.com/downloads/). El emulador de almacenamiento es una herramienta que simula los servicios de blobs, cola y tabla disponibles en Azure en el equipo de desarrollo local. En el ejemplo siguiente se muestra cómo puede declarar un campo estático para almacenar la cadena de conexión para su emulador almacenamiento local:  

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar el emulador de almacenamiento de Azure, seleccione el botón **Inicio** o presione la tecla de **Windows** . Empiece a escribir **Emulador de almacenamiento de Azure**y seleccione **Emulador de Microsoft Azure almacenamiento** de la lista de aplicaciones.

Los ejemplos siguientes, se supone que ha utilizado uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## <a name="retrieve-your-connection-string"></a>Recuperar la cadena de conexión
Puede usar la clase **cloud_storage_account** para representar la información de su cuenta de almacenamiento. Para recuperar información de su cuenta de almacenamiento de la cadena de conexión de almacenamiento, puede usar el método **analizar** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-queue"></a>Cómo: crear una cola
Un objeto **cloud_queue_client** le permite obtener objetos de referencia para colas. El código siguiente crea un objeto **cloud_queue_client** .

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create a queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

Utilice el objeto **cloud_queue_client** para obtener una referencia a la cola que desea usar. Puede crear la cola si no existe.

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();  

## <a name="how-to-insert-a-message-into-a-queue"></a>Cómo: insertar un mensaje en una cola
Para insertar un mensaje en una cola existente, cree primero una nueva **cloud_queue_message**. A continuación, llame al método **add_message** . Puede crear un **cloud_queue_message** desde una cadena o una matriz de **bytes** . Aquí es el código que crea una cola (si no existe) y se inserta el mensaje 'Hola a todos':

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();

    // Create a message and add it to the queue.
    azure::storage::cloud_queue_message message1(U("Hello, World"));
    queue.add_message(message1);  

## <a name="how-to-peek-at-the-next-message"></a>Cómo: inspeccionar el mensaje siguiente
Puede leer el mensaje de la parte delantera de una cola sin quitarlo de la cola llamando al método **peek_message** .

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Peek at the next message.
    azure::storage::cloud_queue_message peeked_message = queue.peek_message();

    // Output the message content.
    std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Cómo: cambiar el contenido de un mensaje en cola
Puede cambiar el contenido de un mensaje en lugar de en la cola. Si el mensaje representa una tarea de trabajo, puede usar esta característica para actualizar el estado de la tarea de trabajo. El siguiente código actualiza el mensaje de cola con contenido nuevo y establece el tiempo de espera de la visibilidad para extender otro 60 segundos. Esto guarda el estado de trabajo asociado con el mensaje y da al cliente otro minuto para continuar trabajando en el mensaje. Podría usar esta técnica para realizar un seguimiento de los flujos de trabajo de varios pasos en los mensajes de la cola, sin tener que volver a empezar desde el principio si se produce un error en un paso de procesamiento debido a errores de hardware o software. Normalmente, debería mantener también un número de reintentos y si el mensaje se vuelve a intentar más de n veces, debería eliminarlo. Esto protege un mensaje que provoca un error de aplicación cada vez que se procesa.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the message from the queue and update the message contents.
    // The visibility timeout "0" means make it visible immediately.
    // The visibility timeout "60" means the client can get another minute to continue
    // working on the message.
    azure::storage::cloud_queue_message changed_message = queue.get_message();

    changed_message.set_content(U("Changed message"));
    queue.update_message(changed_message, std::chrono::seconds(60), true);

    // Output the message content.
    std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## <a name="how-to-de-queue-the-next-message"></a>Cómo: anule la cola el mensaje siguiente
El código anule la cola un mensaje de una cola en dos pasos. Cuando llame a **get_message**, recibe el siguiente mensaje de una cola. Se convierte en un mensaje devuelto desde **get_message** invisible a cualquier otro código leer los mensajes de esta cola. Para terminar de quitar el mensaje de la cola, también deberá llamar **delete_message**. Este proceso de dos pasos de la eliminación de un mensaje asegura que si se produce un error en el código procesar un mensaje debido a un error de software o hardware, otra instancia de su código puede obtener el mismo mensaje y vuelva a intentarlo. El código llama **delete_message** derecha después de que el mensaje se ha procesado.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the next message.
    azure::storage::cloud_queue_message dequeued_message = queue.get_message();
    std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

    // Delete the message.
    queue.delete_message(dequeued_message);

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Cómo: aprovechar opciones adicionales para los mensajes en la cola
Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo término, puede establecer un tiempo de espera de tiempo mayor o menor, permitir que el código más o menos tiempo para procesar cada mensaje por completo. En el ejemplo siguiente se utiliza el método **get_messages** para recibir mensajes de 20 en una llamada. A continuación, procesa cada mensaje utilizando un bucle **for** . También se establece el tiempo de espera de tiempo en cinco minutos para cada mensaje. Tenga en cuenta que se inicia el 5 minutos para todos los mensajes al mismo tiempo, pasa después tiene cinco minutos desde la llamada a **get_messages**, todos los mensajes que se han eliminado no estará visible de nuevo.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
    // 5 minutes (300 seconds).
    azure::storage::queue_request_options options;
    azure::storage::operation_context context;

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

    for (auto it = messages.cbegin(); it != messages.cend(); ++it)
    {
        // Display the contents of the message.
        std::wcout << U("Get: ") << it->content_as_string() << std::endl;
    }

## <a name="how-to-get-the-queue-length"></a>Cómo: obtener la longitud de cola
Puede obtener una estimación del número de mensajes de una cola. El método **download_attributes** solicita el servicio de cola para recuperar los atributos de cola, incluidos el número de mensajes. El método **approximate_message_count** Obtiene el número aproximado de los mensajes en la cola.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Fetch the queue attributes.
    queue.download_attributes();

    // Retrieve the cached approximate message count.
    int cachedMessageCount = queue.approximate_message_count();

    // Display number of messages.
    std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## <a name="how-to-delete-a-queue"></a>Cómo: eliminar una cola
Para eliminar una cola y todos los mensajes contenidos en él, llame al método de **delete_queue_if_exists** en el objeto de cola.

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // If the queue exists and delete it.
    queue.delete_queue_if_exists();  

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de cola, siga estos vínculos para obtener más información sobre el almacenamiento de Azure.

-   [Cómo usar el almacenamiento de blobs de C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Cómo usar el almacenamiento de tablas desde C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Lista de recursos de almacenamiento de Azure en C++](storage-c-plus-plus-enumeration.md)
-   [Biblioteca de cliente de almacenamiento para referencia de C++](http://azure.github.io/azure-storage-cpp)
-   [Documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)

