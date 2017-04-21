<properties 
    pageTitle="Dividir colas y temas | Microsoft Azure"
    description="Describe cómo crear particiones colas de Bus de servicio y temas mediante varios agentes de mensaje."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm;hillaryc" />

# <a name="partitioned-queues-and-topics"></a>Temas y colas divididas

Bus de servicios de Azure emplea varios agentes de mensaje para procesar los mensajes y varios stores mensajería para almacenar los mensajes. Una cola convencional o tema se trata por el agente de un único mensaje y se almacena en un almacén de mensajería. Bus de servicio también permite colas o temas dividir en varios agentes de mensaje y mensajería stores. Esto significa que el rendimiento general de una cola dividida o tema ya no está limitado por el rendimiento de un agente de mensaje o el almacén de mensajería. Además, un corte temporal de una tienda mensajería no representa una cola dividida o tema disponible. Temas y colas divididas pueden contener todas las Bus de servicio funciones avanzadas, como la compatibilidad con las transacciones y sesiones.

Para obtener más detalles sobre aspectos internos de Bus de servicio, vea el tema de la [arquitectura de Bus de servicio][] .

## <a name="how-it-works"></a>Cómo funciona

Cada cola dividida o tema consta de varios fragmentos. Cada fragmento se almacena en otro almacén de mensajería y controlado por un agente de mensaje diferente. Cuando se envía un mensaje a una cola dividida o el tema, Bus de servicio asigna el mensaje a uno de los fragmentos. La selección se realiza aleatoriamente Bus de servicio o mediante una clave de partición que puede especificar el remitente.

Cuando un cliente desea recibir un mensaje de una cola dividida o de una suscripción de un tema dividida, consultas de Bus de servicio todos los fragmentos de mensajes, a continuación, devuelve el primer mensaje que se obtiene desde cualquiera de la comercios mensajería al destinatario. Caché de Bus de servicio otra mensajes y los devuelve cuando recibe adicionales reciben solicitudes. Un cliente de recepción no es conocimiento de la partición; el comportamiento de cliente a través de una cola dividida o tema (por ejemplo, leer, completar, aplazar con problemas de entrega, captura previa de datos) es idéntico al comportamiento de una entidad normal.

No hay ningún costo adicional cuando envíe un mensaje a o recibir un mensaje de una cola con particiones o un tema.

## <a name="enable-partitioning"></a>Habilitar particiones

Para usar colas divididas y temas con Bus de servicio de Azure, usar el SDK de Azure versión 2.2 o posterior, o bien especificar `api-version=2013-10` en su HTTP solicita.

Puede crear colas de Bus de servicio y temas en tamaños de 1, 2, 3, 4 o 5 GB (el valor predeterminado es 1 GB). Con la partición habilitada, Bus de servicio crea 16 particiones para cada GB que especifique. Por lo tanto, si crea una cola que es 5 GB en tamaño, con 16 particiones el tamaño máximo de cola se convierte en (5 \* 16) = 80 GB. Puede ver el tamaño máximo del tema o cola dividida consultando su entrada en el [portal de Azure][].

Existen varias formas de crear una cola con particiones o un tema. Cuando se crea la cola o tema de la aplicación, puede habilitar particiones para la cola o tema estableciendo la propiedad [QueueDescription.EnablePartitioning][] o [TopicDescription.EnablePartitioning][] respectivamente en **true**. Estas propiedades deben estar establecidas en el momento en la cola o tema se crea. No es posible cambiar las propiedades de una cola existente o un tema. Por ejemplo:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Como alternativa, puede crear una cola dividida o tema en Visual Studio o en el [portal de Azure][]. Cuando se crea una nueva cola o un tema en el portal, establezca la opción **Habilitar particiones** en el módulo **configuración General** de la cola o tema de la ventana de **configuración** en **true**. En Visual Studio, haga clic en la casilla de verificación **Habilitar particiones** en el cuadro de diálogo **Nueva cola** o **Tema nuevo** .

## <a name="use-of-partition-keys"></a>Uso de claves de partición

Cuando un mensaje en cola en una cola con particiones o un tema, Bus de servicio comprueba la presencia de una clave de partición. Si encuentra uno, selecciona el fragmento basado en dicha clave. Si no encuentra una clave de partición, selecciona el fragmento basándose en un algoritmo interno.

### <a name="using-a-partition-key"></a>Usar una clave de partición

Algunos escenarios, como las sesiones o las transacciones, requieran que los mensajes se almacenen en un fragmento específico. Todos estos escenarios requieren el uso de una clave de partición. Todos los mensajes que utilizan la misma clave de partición se asignan al mismo fragmento. Si el fragmento está disponible temporalmente, Bus de servicio devuelve un error.

Según el escenario, se usan las propiedades de mensaje diferente como una clave de partición:

**Id. de sesión**: si un mensaje tiene establecida la propiedad de [BrokeredMessage.SessionId][] , Bus de servicio usa esta propiedad como la clave de partición. De este modo, todos los mensajes que pertenecen a la misma sesión se administran por el mismo agente de mensaje. Esto permite Bus de servicio garantizar la ordenación, así como la coherencia de los Estados de sesión del mensaje.

**PartitionKey**: si un mensaje con la propiedad [BrokeredMessage.PartitionKey][] pero no la propiedad de [BrokeredMessage.SessionId][] establecer, Bus de servicio, se utiliza la propiedad [PartitionKey][] como la clave de partición. Si el mensaje con el [Id. de sesión][] y el conjunto de propiedades de [PartitionKey][] , ambas propiedades deben ser idénticas. Si la propiedad [PartitionKey][] se establece en un valor distinto de la propiedad [ID][] , Bus de servicio devuelve una excepción **InvalidOperationException** . La propiedad [PartitionKey][] debe usarse si un remitente envía mensajes de transacciones de sesión sin tener en cuenta. La clave de partición garantiza que todos los mensajes que se envían dentro de una transacción se administran por el mismo agente de mensajería.

**Id. de mensaje**: si la cola o tema tiene la propiedad [QueueDescription.RequiresDuplicateDetection][] establecida en **true** y no se establecen las propiedades [BrokeredMessage.SessionId][] o [BrokeredMessage.PartitionKey][] , la propiedad [BrokeredMessage.MessageId][] sirve como clave de partición. (Tenga en cuenta que las bibliotecas de Microsoft .NET y AMQP asigne automáticamente un identificador de mensaje si la aplicación de envío no). En este caso, todas las copias del mismo mensaje se administran por el mismo agente de mensaje. Esto permite Bus de servicio detectar y eliminar los mensajes duplicados. Si la propiedad [QueueDescription.RequiresDuplicateDetection][] no se establece en **true**, Bus de servicio no tiene en cuenta la propiedad de [Id. de mensaje][] como una clave de partición.

### <a name="not-using-a-partition-key"></a>No usa una clave de partición

En ausencia de una clave de partición, Bus de servicio distribuye mensajes en turnos para todos los fragmentos de la cola dividida o tema. Si el fragmento elegido no está disponible, Bus de servicio asigna el mensaje a un fragmento de diferentes. De este modo, se realiza correctamente la operación de envío a pesar de la disponibilidad de un almacén de mensajería.

Para dar a Bus de servicio tiempo suficiente para poner el mensaje en un fragmento diferente, el valor de [MessagingFactorySettings.OperationTimeout][] especificado por el cliente que envía el mensaje debe ser mayor que 15 segundos. Se recomienda que establezca la propiedad [OperationTimeout][] en el valor predeterminado de 60 segundos.

Tenga en cuenta que una clave de partición "fija" un mensaje a un fragmento de específico. Si el almacén de mensajes que contiene este fragmento no está disponible, Bus de servicio devuelve un error. En ausencia de una clave de partición, Bus de servicio puede elegir un fragmento diferente y la operación se realiza correctamente. Por lo tanto, se recomienda que no se proporciona una clave de partición a menos que sea necesario.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Temas avanzados: utilizar las transacciones con entidades divididas

Los mensajes que se envían como parte de una transacción deben especificar una clave de partición. Esto puede ser una de las siguientes propiedades: [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][]o [BrokeredMessage.MessageId][]. Todos los mensajes que se envían como parte de la misma transacción deben especificar la misma clave de partición. Si intenta enviar un mensaje sin una clave de partición dentro de una transacción, Bus de servicio devuelve una excepción **InvalidOperationException** . Si intenta enviar varios mensajes en la misma transacción que tienen claves de partición diferente, Bus de servicio devuelve una excepción **InvalidOperationException** . Por ejemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Si cualquiera de las propiedades que servir como una clave de partición se establecen, Bus de servicio fija el mensaje a un fragmento de específico. Esto ocurre si no se utiliza una transacción. Se recomienda que no se especifica una clave de partición si no es necesario.

## <a name="using-sessions-with-partitioned-entities"></a>Utilizar las sesiones con entidades divididas

Para enviar un mensaje de transacciones a tener en cuenta la sesión de tema o colas, el mensaje debe tener la propiedad [BrokeredMessage.SessionId][] establecer. Si también se especifica la propiedad [BrokeredMessage.PartitionKey][] , debe ser igual a la propiedad de [ID][] . Si difieren, Bus de servicio devuelve una excepción **InvalidOperationException** .

A diferencia de colas (sin particiones) normales o temas, no es posible utilizar una sola transacción para enviar varios mensajes a sesiones diferentes. Si intenta, Bus de servicio devuelve una excepción **InvalidOperationException **. Por ejemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Reenvío de mensajes automático con entidades divididas

Bus de servicios de Azure es compatible con el reenvío de mensajes automático de, para o entre entidades divididas. Para habilitar el reenvío de mensajes automático, establezca la propiedad de [QueueDescription.ForwardTo][] en la cola de origen o de suscripción. Si el mensaje especifica una clave de partición ([ID][], [PartitionKey][]o [identificador del mensaje][]), se utilizará dicha clave de partición de la entidad de destino.

## <a name="considerations-and-guidelines"></a>Consideraciones e instrucciones

- **Características de alta coherencia**: si una entidad utiliza características como sesiones, detección de duplicados o control explícito de particiones clave, las operaciones de mensajería siempre se enrutan a fragmentos específicos. Si cualquiera de los fragmentos experimenta tráfico alto o el almacén subyacente está en mal estado, errores de las operaciones y se reduce la disponibilidad. En general, la coherencia es aún muy superior de entidades sin particiones; solo un subconjunto de tráfico está experimentando problemas, en lugar de todo el tráfico.
- **Administración**: operaciones como crear, actualizar y eliminar deben realizarse en todos los fragmentos de la entidad. Si cualquier fragmento está en mal estado podría provocar errores para estas operaciones. Para la operación de obtener información como cuenta de mensaje debe agregarse de todos los fragmentos. Si cualquier fragmento está en mal estado, el estado de disponibilidad de entidad se notifica como limitado.
- **Escenarios de mensaje de bajo volumen**: para estos casos, especialmente cuando se usa el protocolo HTTP, puede que tenga que realizar varias operaciones de recepción con el fin de obtener todos los mensajes. Para las solicitudes de recepción, el front-end realiza una recepción en todos los fragmentos y almacena todas las respuestas que ha recibido. ¿Beneficiarse de esta caché y recibir una solicitud de recepción subsiguientes en la misma conexión latencia será menor. Sin embargo, si tiene varias conexiones o utilizar HTTP, Establece una nueva conexión para cada solicitud. Por lo tanto, no hay ninguna garantía que lo haría land en el mismo nodo. Si todos los mensajes existentes están bloqueados y caché en otro front-end, la operación de recepción devuelve **null**. Finalmente expiren mensajes y recibe nuevamente. Se recomienda HTTP de mantenimiento.
- **Examinar o buscar mensajes**: PeekBatch no siempre devuelve el número de mensajes especificado en la [propiedad MessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecount.aspx). Hay dos motivos habituales para ello. Una razón es que el tamaño de la colección de mensajes agregado supera el tamaño máximo de 256KB. Otra razón es que si la cola o tema tiene la [propiedad EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) establecida en **true**, una partición no tiene suficientes mensajes para completar el número requerido de mensajes. En general, si una aplicación desea recibir un número específico de mensajes, debe llamar [PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) varias veces hasta que se obtiene el número de mensajes o no hay mensajes sin más para inspeccionar. Para obtener más información, incluidos ejemplos de código, vea [QueueClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) o [SubscriptionClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx).

## <a name="latest-added-features"></a>Funciones de agregado más recientes

- Agregar o quitar regla ahora es compatible con entidades divididas. A diferencia de entidades sin particiones, estas operaciones no son compatibles en transacciones. 
- AMQP ahora se puede enviar y recibir mensajes a y desde una entidad dividida.
- AMQP ya es compatible con las siguientes operaciones: [Enviar lote](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.sendbatch.aspx), [Lote recibir](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.receivebatch.aspx), [recepción por número de secuencia](https://msdn.microsoft.com/library/azure/hh330765.aspx), [Peek](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx), [Renovar bloquear](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.renewmessagelock.aspx), [Mensaje de programación](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.schedulemessageasync.aspx), [Cancelar mensaje programada](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync.aspx), [Agregar regla](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Quitar regla](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Bloqueo de renovación de sesión](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx), [Establecer el estado de sesión](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx), [Obtener el estado de sesión](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx), [Inspeccionar mensajes de sesión](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.peek.aspx)y [Enumerar sesiones](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.getmessagesessionsasync.aspx).

## <a name="partitioned-entities-limitations"></a>Limitaciones de entidades divididas

Actualmente Bus de servicio impone las siguientes limitaciones colas divididas y temas:

-   Temas y colas con particiones no admiten envío de los mensajes que pertenecen a diferentes sesiones en una sola transacción.
-   Bus de servicio permite actualmente hasta 100 colas divididas o temas por espacio de nombres. Cada cola dividida o tema cuenta hacia la cuota de 10.000 entidades por espacio de nombres (no se aplica al nivel Premium).

## <a name="next-steps"></a>Pasos siguientes

Vea la descripción de la [compatibilidad con AMQP 1.0 Bus de servicio particiones colas y temas][] para obtener más información sobre entidades mensajería de partición. 

  [Arquitectura de Bus de servicio]: service-bus-architecture.md
  [Portal de Azure]: https://portal.azure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [Id. de sesión]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [Id. de mensaje]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [Compatibilidad con AMQP 1.0 Bus de servicio particiones colas y temas]: service-bus-partitioned-queues-and-topics-amqp-overview.md
