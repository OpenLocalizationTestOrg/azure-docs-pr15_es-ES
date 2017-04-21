<properties 
    pageTitle="Compatibilidad con AMQP 1.0 Bus de servicio particiones colas y temas | Microsoft Azure" 
    description="Aprenda a usar los temas y colas avanzadas mensaje Queue protocolo (AMQP) 1.0 con Bus de servicio particionado." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="hillaryc" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="hillaryc;sethm"/>

# <a name="amqp-10-support-for-service-bus-partitioned-queues-and-topics"></a>Compatibilidad con AMQP 1.0 Bus de servicio particiones colas y temas 

Bus de servicios de Azure ahora admite el avanzado mensaje Queue protocolo (**AMQP**) 1.0 para Bus de servicio **particiones colas y temas.**

**AMQP** es un protocolo de cola de mensaje estándar abierto que permite desarrollar aplicaciones entre plataformas con diferentes lenguajes de programación. Para obtener información general sobre AMQP en Bus de servicio de soporte técnico, consulte [1.0 AMQP en Bus de servicio de soporte técnico](service-bus-amqp-overview.md).

**Con particiones colas y temas**, también conocido como *entidades divididas*, ofrecen mayor disponibilidad, la confiabilidad y rendimiento que convencional colas sin particiones y temas. Para obtener más información sobre las entidades divididas, vea [Entidades mensajería divididas](service-bus-partitioning.md).

La adición de AMQP 1.0 como un protocolo para comunicarse con los temas y colas divididas permite generar aplicaciones interoperabilidad que pueden aprovechar las ventajas de la mayor disponibilidad, la confiabilidad y a lo largo ofrecen por entidades de Bus de servicio particiones.

Una guía detallada de protocolo de AMQP 1.0 hilo nivel, donde se explica cómo Bus de servicio implementa y se basa en la especificación de OASIS AMQP técnica, consulte la [Guía de protocolo AMQP 1.0 en Bus de servicio de Azure y Hubs de evento](service-bus-amqp-protocol-guide.md).    

## <a name="use-amqp-with-partitioned-queues"></a>Usar AMQP con colas divididas

Colas son útiles para escenarios que requieren disociación temporal, redistribución de carga, equilibrio de carga y acoplamiento flexible. Con una cola editores envían mensajes a la cola y los consumidores de reciban mensajes de la cola en situaciones donde un mensaje se solo pueden recibir una vez. Un buen ejemplo de esto es un sistema de inventario en la que terminales de punto de venta publicación datos a una cola en lugar de directamente en el sistema de administración de inventario. El sistema de administración de inventario, a continuación, consume los datos en cualquier momento para administrar reposición cotizaciones. Esto tiene varias ventajas, incluido el sistema de administración de inventario no tener que estar en línea en todo momento. Para obtener más detalles acerca de las colas de Bus de servicio, vea [crear las aplicaciones que utilizan colas de Bus de servicio](service-bus-create-queues.md). 

Una cola dividida más aumenta la disponibilidad, la confiabilidad y el rendimiento para aplicaciones, como estas colas se dividen en varios agentes de mensaje y mensajería stores.     

### <a name="create-partitioned-queues"></a>Crear colas divididas

Puede crear una cola con particiones con el [portal clásica Azure][] y el SDK de Bus de servicio. Para crear una cola dividida, establezca la propiedad de [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) en **true** en la instancia de [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) . El código siguiente muestra cómo crear una cola de particiones con el SDK de Bus de servicio. 
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Enviar y recibir mensajes con AMQP

Puede enviar mensajes y recibir mensajes de una cola de particiones con AMQP como protocolo, estableciendo la propiedad de [tipo de transporte](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) en [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) tal como se muestra en el código siguiente.  

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="use-amqp-with-partitioned-topics"></a>Usar AMQP con temas divididas

Temas son conceptualmente similares a las colas, pero temas pueden redirigir una copia del mismo mensaje a varias *suscripciones*. En un tema, editores envían mensajes al tema y los consumidores de reciban mensajes de suscripciones. En el caso de punto de venta del sistema de inventario, terminales publicación datos en el tema. A continuación, el sistema de administración de inventario recibe mensajes de una suscripción. Además, un sistema de supervisión puede recibir el mismo mensaje de una suscripción diferente. Para obtener más información sobre temas de Bus de servicios y suscripciones, vea [crear las aplicaciones que utilizan suscripciones y temas de Bus de servicio](service-bus-create-topics-subscriptions.md). 

Al igual que con colas, aún más temas divididas aumentan la disponibilidad, la confiabilidad y el rendimiento para aplicaciones, como estos temas y sus suscripciones se dividen en varios agentes de mensaje y almacena mensajería. 

### <a name="create-partitioned-topics"></a>Crear temas divididas

Puede crear un tema con particiones con el [portal clásica Azure][] y el SDK de Bus de servicio. Para crear un tema dividido, establezca la propiedad de [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) en **true** en la instancia de [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . El código siguiente muestra cómo crear un tema con particiones mediante el SDK de Bus de servicio.
    
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Enviar y recibir mensajes con AMQP

Puede enviar mensajes y recibir mensajes de una suscripción de tema divididas mediante AMQP como protocolo, estableciendo la propiedad de [tipo de transporte](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) en [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), como se muestra en el siguiente código.  

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
    
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
    
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="next-steps"></a>Pasos siguientes

Vea la siguiente información adicional para obtener más información sobre entidades mensajería divididas, así como AMQP.

*    [Entidades de mensajería divididas](service-bus-partitioning.md)
*    [OASIS avanzada mensaje Queue protocolo (AMQP) versión 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Soporte técnico de AMQP 1.0 de Bus de servicio](service-bus-amqp-overview.md)
*    [AMQP 1.0 en Guía de protocolo de Bus de servicio de Azure y Hubs de evento](service-bus-amqp-protocol-guide.md)
*    [Cómo usar la API de Java Message Service (JMS) con Bus de servicio y AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Cómo usar AMQP 1.0 con la API de .NET de Bus de servicio](service-bus-dotnet-advanced-message-queuing.md)

[Portal de clásico de Azure]: http://manage.windowsazure.com
