<properties
    pageTitle="Cómo usar el almacenamiento de la cola de Python | Microsoft Azure"
    description="Obtenga información sobre cómo usar el servicio de cola de Azure de Python para crear y eliminar colas, insertar, obtener y eliminar mensajes."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-python"></a>Cómo usar el almacenamiento de la cola de Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Información general

Esta guía le muestra cómo llevar a cabo escenarios comunes mediante el servicio de almacenamiento de cola de Azure. Los ejemplos se escriben en Python y utilizan el [SDK de Microsoft Azure almacenamiento de Python]. Los escenarios cubiertos incluyen **Insertar**, **leerlo**, **Introducción**y **Eliminar** mensajes de la cola, así como **crear y eliminar colas**. Para obtener más información sobre colas, consulte la sección [pasos siguientes].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="how-to-create-a-queue"></a>Cómo: Crear una cola

El objeto **QueueService** le permite trabajar con colas. El código siguiente crea un objeto **QueueService** . Agregue lo siguiente en la parte superior de cualquier archivo Python en la que desea acceder mediante programación el almacenamiento de Azure:

    from azure.storage.queue import QueueService

El código siguiente crea un objeto **QueueService** con la clave de nombre y la cuenta de la cuenta de almacenamiento. Reemplazar 'myaccount' y 'MiClave' con el nombre de la cuenta y la clave.

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')


## <a name="how-to-insert-a-message-into-a-queue"></a>Cómo: Insertar un mensaje en una cola

Para insertar un mensaje de una cola, utilice el **poner\_mensaje** método para crear un nuevo mensaje y agregar a la cola.

    queue_service.put_message('taskqueue', u'Hello World')


## <a name="how-to-peek-at-the-next-message"></a>Cómo: Inspeccionar el mensaje siguiente

Puede inspeccionar en el mensaje de la parte delantera de una cola sin quitarlo de la cola llamando el **peek\_mensajes** método. De forma predeterminada, **peek\_mensajes** búsqueda peek en un único mensaje.

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.content)


## <a name="how-to-dequeue-messages"></a>Cómo: Eliminar mensajes de la cola

El código quita un mensaje de una cola en dos pasos. Al llamar a **obtener\_mensajes**, recibe el mensaje siguiente de una cola de forma predeterminada. Un mensaje devuelto desde **obtener\_mensajes** se convierte en invisible a cualquier otro código leer los mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos. Para terminar de quitar el mensaje de la cola, también debe llamar **Eliminar\_mensaje**. Este proceso de dos pasos de la eliminación de un mensaje asegura que cuando se produce un error en el código procesar un mensaje debido a un error de software o hardware, otra instancia de su código puede obtener el mismo mensaje y vuelva a intentarlo. Las llamadas de código **Eliminar\_mensaje** inmediatamente después de que el mensaje se ha procesado.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)

Hay dos formas de personalizar la recuperación de mensajes de una cola.
En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo término, puede establecer un tiempo de espera de tiempo mayor o menor, permitir que el código más o menos tiempo para procesar cada mensaje por completo. El siguiente código en el ejemplo se utiliza la **obtener\_mensajes** método para recibir mensajes de 16 en una llamada. A continuación, se procesa cada mensaje con un bucle for. También se establece el tiempo de espera de tiempo en cinco minutos para cada mensaje.

    messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)      


## <a name="how-to-change-the-contents-of-a-queued-message"></a>Cómo: Cambiar el contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje en lugar de en la cola. Si el mensaje representa una tarea de trabajo, puede usar esta característica para actualizar el estado de la tarea de trabajo. El código siguiente utiliza la **actualizar\_mensaje** método para actualizar un mensaje. El tiempo de espera de visibilidad se establece en 0, lo que significa que el mensaje aparece inmediatamente y se actualiza el contenido.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')

## <a name="how-to-get-the-queue-length"></a>Cómo: Obtener la longitud de cola

Puede obtener una estimación del número de mensajes de una cola. La **obtener\_cola\_metadatos** método solicita el servicio de cola para devolver los metadatos acerca de la cola y la **approximate_message_count**. El resultado solo es aproximado porque los mensajes se pueden agregar o quitar después de que el servicio de cola responde a la solicitud.

    metadata = queue_service.get_queue_metadata('taskqueue')
    count = metadata.approximate_message_count

## <a name="how-to-delete-a-queue"></a>Cómo: Eliminar una cola

Para eliminar una cola y todos los mensajes contenidos en él, llame a la **Eliminar\_cola** método.

    queue_service.delete_queue('taskqueue')

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de almacenamiento de cola, siga estos vínculos para obtener más información.

- [Centro para desarrolladores de Python](/develop/python/)
- [API de REST de servicios de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del equipo de almacenamiento de Azure]
- [Almacenamiento de Microsoft Azure SDK para Python]

[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Almacenamiento de Microsoft Azure SDK para Python]: https://github.com/Azure/azure-storage-python
