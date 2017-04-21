<properties 
    pageTitle="Transacciones de Bus de servicio | Microsoft Azure" 
    description="Información general sobre las transacciones atómicas de Bus de servicio de Azure y enviar por" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="10/04/2016"
    ms.author="clemensv;sethm"/>

# <a name="overview-of-service-bus-transaction-processing"></a>Información general sobre el procesamiento de transacciones de Bus de servicio

Este artículo describe las capacidades de transacción de Bus de servicio de Azure. Gran parte de la discusión se ilustra por las [Transacciones atómica con muestras de Bus de servicio](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions). En este artículo se limita a una descripción general de procesamiento de transacciones y la característica *Enviar a través* de Bus de servicio, mientras la muestra de transacciones atómica es más amplio y complejo en el ámbito.

## <a name="transactions-in-service-bus"></a>Transacciones de Bus de servicio

Una [transacción](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions) de grupos de dos o más operaciones juntos en un *ámbito de ejecución*. Naturaleza, como una transacción debe asegurarse de que todas las operaciones que pertenecen a un grupo determinado de las operaciones de éxito o error conjuntamente. En este sentido transacciones actúen como una unidad, que a menudo se denomina *atomicidad*. 

Bus de servicio es el agente de mensaje transacciones y garantiza la integridad de las transacciones para todas las operaciones internas frente a su almacenamiento de mensajes. Todas las transferencias de mensajes dentro de Bus de servicio, como mover mensajes a una [cola](service-bus-dead-letter-queues.md) o [reenvío automático](service-bus-auto-forwarding.md) de mensajes entre entidades, son transacciones. Por lo tanto, si Bus de servicio acepta un mensaje, tiene ya se han almacenado y etiquetados con un número de secuencia. Luego de las transferencias de cualquier mensaje dentro de Bus de servicio son operaciones coordinadas entre entidades, y ninguna llevará a pérdida (se realiza correctamente de origen y destino falla) o para duplicación (se produce un error de origen y destino se realiza correctamente) del mensaje.

Bus de servicio es compatible con operaciones de agrupación con una sola entidad mensajería (cola, tema, suscripción) en el ámbito de una transacción. Por ejemplo, puede enviar varios mensajes a una cola de dentro de un ámbito de transacción y los mensajes solo se compromete a registro de la cola cuando la transacción se completa correctamente.

## <a name="operations-within-a-transaction-scope"></a>Operaciones dentro de un ámbito de transacción 

Las operaciones que pueden realizar en un ámbito de transacción son los siguientes:

- ** [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx), [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)**: enviar, SendAsync, SendBatch, SendBatchAsync 

- **[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)**: completado, CompleteAsync, abandonar, AbandonAsync, con problemas de entrega, DeadletterAsync, aplazar, DeferAsync, RenewLock, RenewLockAsync 

Recibir operaciones no se incluyen, porque se supone que la aplicación adquiere mensajes mediante el modo [ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , dentro de algunos recibir bucle o con un [OnMessage](https://msdn.microsoft.com/library/azure/dn369601.aspx) de devolución de llamada y sólo entonces se abre un ámbito de transacción para procesar el mensaje.

La eliminación del mensaje (completo, abandonar, entregados, aplazar), a continuación, aparece en el ámbito de y dependientes en el resultado de la transacción global.

## <a name="transfers-and-send-via"></a>"Enviar a través de" y transferencias

Para habilitar la entrega de transacciones de datos de una cola a un procesador y, a continuación, a otra cola, Bus de servicio admite *transferencias*. En una operación de transferencia, un remitente primero envía un mensaje a una cola de transferencia de"" y la cola de transferencia inmediatamente mueve el mensaje a la cola de destino con la misma implementación de transferencia sólida que depende de la capacidad de reenvío automático. El mensaje nunca se compromete a registro de la cola de transferencia de manera que estará visible para los consumidores de la cola de transferencia.

La potencia de esta capacidad de transacciones se hace evidente cuando la propia cola de transferencia es el origen de los mensajes de entrada del remitente. En otras palabras, Bus de servicio pueden transferir el mensaje en la cola de destino "por" la cola de transferencia, mientras lleva a cabo una completa (o aplazar, o entregados) operación en el mensaje de entrada, todo en una operación atómica. 

### <a name="see-it-in-code"></a>Ver en el código

Para configurar estas transferencias, cree el remitente de un mensaje que dirige a la cola de destino a través de la cola de transferencia. También tendrá un receptor que extrae los mensajes de esa misma cola. Por ejemplo:

```
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

A continuación, en una transacción simple usa estos elementos, como en el ejemplo siguiente:

```
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Pasos siguientes

Consulte los artículos siguientes para obtener más información acerca de las colas de Bus de servicio:

- [Encadenar entidades de Bus de servicio con reenvío automático](service-bus-auto-forwarding.md)
- [Ejemplo de reenvío automático](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
- [Transacciones atómicas con muestras de Bus de servicio](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
- [Azure colas y Bus de servicio colas en comparación](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Cómo usar colas de Bus de servicio](service-bus-dotnet-get-started-with-queues.md)