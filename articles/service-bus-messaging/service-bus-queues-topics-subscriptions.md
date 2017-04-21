<properties 
    pageTitle="Colas de Bus de servicio, temas y suscripciones | Microsoft Azure"
    description="Descripción general de Bus de servicios de mensajería entidades."
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
    ms.date="10/14/2016"
    ms.author="sethm" />

# <a name="service-bus-queues-topics-and-subscriptions"></a>Suscripciones, temas y colas de Bus de servicio

Bus de servicio de Microsoft Azure es compatible con un conjunto de tecnologías de middleware orientado el mensaje, en la nube como fiable message Queue y resistentes de publicación o suscripción mensajería. Estas funciones de mensajería con pueden considerarse desacopladas mensajería características de publicación de suscripción de soporte técnico, temporal disociación y escenarios de uso del Bus de servicio de mensajería a tela de equilibrio de carga. Comunicación desacoplada tiene muchas ventajas; Por ejemplo, clientes y servidores pueden conectar según sea necesario y realizar sus operaciones de forma asincrónica.

Las entidades de mensajería que forman el núcleo de las capacidades de mensajería con en Bus de servicio son colas, temas y las suscripciones y reglas/acciones.

## <a name="queues"></a>Colas

Colas ofrecen primera en, entrega de mensajes de la primera salida (FIFO) a uno o varios de los consumidores competencia. Es decir, mensajes normalmente se espera que se recibió y procesados por los receptores en el orden en que se agregaron a la cola y cada mensaje se ha recibido y procesado por los consumidores de un solo mensaje. Una ventaja clave de uso de colas es lograr "disociación temporal" de los componentes de la aplicación. En otras palabras, el productor (remitentes) y los consumidores (receptores) no tienen a enviar y recibir mensajes al mismo tiempo, porque los mensajes se almacenan duradera en la cola. Además, el productor no tiene que esperar una respuesta del consumidor para continuar para procesar y enviar mensajes.

Una ventaja relacionada es "cargar redistribución," que posibilita el productor y los consumidores para enviar y recibir mensajes a tasas diferentes. En muchas aplicaciones, la carga del sistema varía con el tiempo; Sin embargo, el tiempo de procesamiento necesario para cada unidad de trabajo es normalmente constante. Intermediating productor de mensaje y los consumidores de una cola significa que sólo tiene la aplicación que estén configurados para poder controlar carga promedio en lugar de carga de recursos asignadas. La profundidad de la cola crece y contratos como varía la carga entrante. Esto directamente ahorra dinero con respecto a la cantidad de infraestructura necesaria para la carga de la aplicación de servicio. A medida que aumenta la carga, más procesos de trabajo pueden agregarse a leer en la cola. Cada mensaje se procesa solamente mediante uno de los procesos de trabajo. Además, este equilibrio de carga en función de extracción permite para un uso óptimo de los equipos de trabajo incluso si difieren de los equipos de trabajo con respecto a la potencia de procesamiento, como extraerá los mensajes a su propia velocidad máxima. Este patrón a menudo se denomina el modelo "competir consumidor".

Uso de colas a intermedio entre los consumidores y productor mensaje ofrece un acoplamiento flexible inherente entre los componentes. Porque productor y los consumidores no tienen constancia de los demás, un consumidor puede actualizarse sin tener ningún efecto en el productor.

Creación de una cola es un proceso de varios pasos. Realizar operaciones de administración de Bus de servicios de mensajería entidades (colas y temas) a través de la clase [Microsoft.ServiceBus.NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , que tiene la construcción proporcionando la dirección base del espacio de nombres de Bus de servicio y las credenciales de usuario. [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) proporciona métodos para crear, enumerar y eliminar entidades mensajería. Después de crear un objeto [Microsoft.ServiceBus.TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) desde el nombre de SA y clave y un objeto de administración de espacio de nombres de servicio, puede usar el método [Microsoft.ServiceBus.NamespaceManager.CreateQueue](https://msdn.microsoft.com/library/azure/hh293157.aspx) para crear la cola. Por ejemplo:

```
// Create management credentials
TokenProvider credentials = TokenProvider. CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

A continuación, puede crear un objeto de cola y un generador de mensajería con el URI de Bus de servicio como un argumento. Por ejemplo:

```
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

A continuación, puede enviar mensajes a la cola. Por ejemplo, si tiene una lista de mensajes con denominado `MessageList`, el código aparece similar al siguiente:

```
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

A continuación, puede recibir mensajes de la cola, como sigue:

```
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

En el modo de [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , la operación de recepción es captura de un solo; es decir, cuando Bus de servicio recibe la solicitud, marca el mensaje como consumida y devuelve a la aplicación. Modo de [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) es el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar la aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Porque Bus de servicio marca el mensaje como consumido, cuando la aplicación se reinicia y empieza a utilizar mensajes nuevamente, habrá perdido el mensaje que se ha utilizado antes el bloqueo.

En el modo de [PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , la operación de recepción se convierte en dos fases, que permite a las aplicaciones de soporte técnico que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe la solicitud, encuentra el mensaje siguiente consumir, lo bloquea para impedir que otros consumidores recibirlo y vuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción llamando [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) en el mensaje recibido. Cuando Bus de servicio detecta el [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) llamar, marca el mensaje como consumida.

Si la aplicación no se puede procesar el mensaje por algún motivo, puede llamar al método [abandonar](https://msdn.microsoft.com/library/azure/hh181837.aspx) en el mensaje recibido (en lugar de [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Esto permite Bus de servicio desbloquear el mensaje y que esté disponible para recibir de nuevo, el mismo consumidor o por otro consumidor competencia. En segundo lugar, hay un tiempo de espera asociado al bloqueo y si se produce un error en la aplicación procesar el mensaje antes el bloqueo de tiempo de espera expira (por ejemplo, si se bloquea la aplicación) y luego Bus de servicio desbloquea el mensaje y hace que esté disponible para volver a recibir (básicamente, realizar una operación de [abandonar](https://msdn.microsoft.com/library/azure/hh181837.aspx) de forma predeterminada).

Tenga en cuenta que en caso de que la aplicación se bloquea después de procesar el mensaje, pero antes de que haya emitido la solicitud [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) , el mensaje se vuelve a entregar a la aplicación cuando se reinicia. A menudo se denomina *Al menos una vez *procesamiento; es decir, cada mensaje se procesa al menos una vez. Sin embargo, en determinadas situaciones puede entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicados, a continuación, se requiere la lógica adicional en la aplicación para detectar duplicados, lo que pueden lograr en función de la propiedad de **Id. de mensaje** del mensaje, que se mantiene constante en todos los intentos de entrega. Esto se conoce como *Una sola vez* de procesamiento.

Para obtener más información y un ejemplo de cómo crear y enviar mensajes a y desde colas, vea el [Bus de servicio había negociada mensajería .NET Tutorial](service-bus-brokered-tutorial-dotnet.md).

## <a name="topics-and-subscriptions"></a>Temas y suscripciones

A diferencia de colas, en el que cada mensaje se procesa por un único consumidor, *temas* y *suscripciones* proporcionan un formulario de uno a varios de comunicación en un modelo *de publicación o suscripción* . Útil para escalar a un gran número de destinatarios, cada mensaje publicado estará disponible para cada suscripción registrado con el tema. Los mensajes se envían a un tema y entrega a una o varias suscripciones asociadas, según las reglas de filtro que se pueden establecer por suscripción. Las suscripciones pueden usar filtros adicionales para restringir los mensajes que desea recibir. Los mensajes se envían a un tema de la misma manera se envían a una cola, pero no reciben los mensajes desde el tema directamente. En su lugar, se reciben de suscripciones. Una suscripción de tema se asemeja a una cola virtual que recibe copias de los mensajes que se envían al tema. Reciben los mensajes de una suscripción idéntica a la forma en que se reciben de una cola.

Efectos de comparación, la funcionalidad de envío de mensajes de una cola asigna directamente a un tema y su funcionalidad de recepción de mensajes a una suscripción. Entre otras cosas, esto significa que las suscripciones con los mismos patrones descritos anteriormente en esta sección con respecto a las colas: consumidor competencia, disociación temporal, redistribución de carga y equilibrio de carga.

Crear un tema es similar a la creación de una cola, tal como se muestra en el ejemplo en la sección anterior. Crear el URI del servicio y, a continuación, use la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para crear al cliente de espacio de nombres. A continuación, puede crear un tema con el método [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) . Por ejemplo:

```
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

A continuación, agregue suscripciones como desee:

```
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

A continuación, puede crear a un cliente de tema. Por ejemplo:

```
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

El remitente del mensaje puede enviar y recibir mensajes a y desde el tema, como se muestra en la sección anterior. Por ejemplo:

```
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

De forma similar a las colas, reciben los mensajes de una suscripción con un objeto [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) en lugar de un objeto [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Crear al cliente de suscripción, pasando el nombre del tema, el nombre de la suscripción y (opcionalmente) el modo de recepción como parámetros. Por ejemplo, con la suscripción de **inventario** :

```
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Reglas y acciones

En muchos casos, se deben procesar mensajes que tienen características específicas de diferentes formas. Para habilitar esta opción, puede configurar las suscripciones para buscar mensajes que han deseado propiedades y, a continuación, realizan ciertas modificaciones en las propiedades. Mientras las suscripciones de Bus de servicio ver todos los mensajes enviados al tema, solo puede copiar un subconjunto de los mensajes a la cola de suscripción virtual. Esto se logra con filtros de suscripción. Las modificaciones se denominan *acciones de filtrado*. Cuando se crea una suscripción, puede proporcionar una expresión de filtro que opera en las propiedades del mensaje, las propiedades de sistema (por ejemplo, la **etiqueta**) y propiedades de la aplicación personalizado (por ejemplo, **StoreName**.) La expresión de filtro SQL es opcional en este caso. sin una expresión de filtro SQL, cualquier acción de filtro definida en una suscripción se realizará en todos los mensajes de la suscripción.

Usando el ejemplo anterior, para filtrar los mensajes procedentes de sólo de **1**, deberá crear la suscripción de panel como sigue:

```
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Con este filtro de suscripción en su lugar, solo los mensajes que tengan la `StoreName` propiedad establecida en `Store1` se copian en la cola virtual para el `Dashboard` suscripción.

Para obtener más información acerca de los valores de filtro posibles, consulte la documentación de las clases [SqlFilter](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx) y [SqlRuleAction](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlruleaction.aspx) . Vea también el [negociada mensajería: filtros avanzados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) y ejemplos de [Filtros de tema](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) .

## <a name="next-steps"></a>Pasos siguientes

Vea los siguientes temas para obtener más información y ejemplos de uso de Bus de servicio con mensajería entidades avanzados.

- [Introducción a mensajería Bus de servicio](service-bus-messaging-overview.md)
- [Tutorial de Bus de servicio con mensajería .NET](service-bus-brokered-tutorial-dotnet.md)
- [Tutorial de Bus de servicio con mensajería REST](service-bus-brokered-tutorial-rest.md)
- [Ejemplo de filtros de tema](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters)
- [Con la mensajería: Avanzada muestra filtros](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

