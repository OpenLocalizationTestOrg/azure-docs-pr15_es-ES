<properties 
    pageTitle="Procedimientos recomendados para mejorar el rendimiento con Bus de servicio | Microsoft Azure"
    description="Describe cómo utilizar Bus de servicio de Azure para optimizar el rendimiento al intercambiar mensajes con."
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
    ms.date="10/25/2016"
    ms.author="sethm" />

# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Prácticas recomendadas para mejorar el rendimiento por mensajería de Bus de servicio

En este tema se describe cómo usar la mensajería de Bus de servicio de Azure para optimizar el rendimiento al intercambiar mensajes con. La primera parte de este tema describe los distintos mecanismos que se ofrecen para ayudar a mejorar el rendimiento. La segunda parte proporciona instrucciones sobre cómo usar Bus de servicio de manera que puede ofrecer el mejor rendimiento en un escenario determinado.

En este tema, el término "cliente" se refiere a cualquier entidad que tiene acceso a Bus de servicio. Un cliente puede realizar la función de un remitente o un receptor. Se usa el término "remitente" para un cliente de cola o tema de Bus de servicio que envía mensajes a una cola de Bus de servicio o un tema. El término "receptor" se refiere a un cliente de cola o suscripción de Bus de servicio que recibe mensajes de una cola de Bus de servicio o la suscripción.

Estas secciones presentan algunos conceptos Bus de servicio se utiliza para ayudar a aumentar el rendimiento.

## <a name="protocols"></a>Protocolos

Bus de servicio permite a los clientes enviar y recibir mensajes a través de tres protocolos

1. Mensaje avanzada Queue protocolo (AMQP)

2. Bus de servicios de mensajería de protocolo (SBMP)

3. HTTP

AMQP y SBMP son más eficaz porque mantienen la conexión al servicio Bus siempre y cuando existe el generador de mensajería. También se implementa por lotes y captura previa. A menos que se mencione explícitamente, todo el contenido de este tema supone el uso de AMQP o SBMP.

## <a name="reusing-factories-and-clients"></a>Volver a utilizar fábricas y clientes

Objetos de cliente de Bus de servicio, como [QueueClient][] o [MessageSender][], se crean a través de un objeto [MessagingFactory][] , que también proporciona administración interna de conexiones. No debe cerrar mensajería fábricas o cola, tema y clientes de suscripción después de enviar un mensaje y, a continuación, volver a crearlos cuando envíe el mensaje siguiente. Cerrar un generador mensajería elimina la conexión con el servicio de Bus de servicio y, a continuación, se establece una nueva conexión al volver a crear la fábrica. Establecer una conexión es una operación cara que puede evitar volver a utilizar la misma fábrica y objetos de cliente para varias operaciones. Puede usar el objeto [QueueClient][] para enviar mensajes de operaciones asincrónicas simultáneas y varios subprocesos de forma segura. 

## <a name="concurrent-operations"></a>Operaciones simultáneas

Realizar una operación (enviar, recibir, eliminar, etc.) tarda algún tiempo. Este tiempo incluye el procesamiento de la operación por el servicio de Bus de servicio, además de la latencia de la solicitud y la respuesta. Para aumentar el número de operaciones por hora, operaciones deben ejecutar simultáneamente. Puede hacerlo de varias maneras diferentes:

-   **Operaciones asincrónicas**: el cliente de programar las operaciones al realizar operaciones asincrónicas. La siguiente solicitud se inicia antes de que se haya completado la solicitud anterior. A continuación se muestra un ejemplo de una operación de envío asincrónica:

    ```
    BrokeredMessage m1 = new BrokeredMessage(body);
    BrokeredMessage m2 = new BrokeredMessage(body);
    
    Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
      {
        Console.WriteLine("Sent message #1");
      });
    Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
      {
        Console.WriteLine("Sent message #2");
      });
    Task.WaitAll(send1, send2);
    Console.WriteLine("All messages sent");
    ```

    Este es un ejemplo de operación de recepción asincrónica:
    
    ```
    Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
    Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
    
    Task.WaitAll(receive1, receive2);
    Console.WriteLine("All messages received");
    
    async void ProcessReceivedMessage(Task<BrokeredMessage> t)
    {
      BrokeredMessage m = t.Result;
      Console.WriteLine("{0} received", m.Label);
      await m.CompleteAsync();
      Console.WriteLine("{0} complete", m.Label);
    }
    ```

-   **Varias fábricas**: todos los clientes (remitentes además de los receptores) que se crean en la misma fábrica compartan una conexión de TCP. El rendimiento máximo de los mensajes está limitado por el número de operaciones que puede ir a través de esta conexión TCP. El rendimiento que se puede obtener con una sola fábrica varía en gran medida con tiempos de ida y vuelta de TCP y tamaño del mensaje. Para obtener mayores tasas de rendimiento, debe usar varias fábricas mensajería.

## <a name="receive-mode"></a>Modo de recepción

Al crear un cliente de cola o de suscripción, puede especificar el modo de recepción: *Bloquear información* o *recibir y eliminar*. Recibir el valor predeterminado es de modo [PeekLock][]. Cuando se trabaja en este modo, el cliente envía una solicitud para recibir un mensaje de Bus de servicio. Después de que el cliente ha recibido el mensaje, envía una solicitud para completar el mensaje.

Al establecer el modo de recepción [ReceiveAndDelete][], ambos pasos se combinan en una sola solicitud. Esto reduce el número total de operaciones y puede mejorar el rendimiento general del mensaje. Esta mejora del rendimiento viene riesgo perder mensajes.

Bus de servicio no admite las transacciones para operaciones de recepción y eliminar. Además, semántica de bloqueo de la información es necesaria para los escenarios en los que el cliente desea aplazar o [entregados](service-bus-dead-letter-queues.md) un mensaje.

## <a name="client-side-batching"></a>Procesamiento por lotes del cliente

Procesamiento por lotes del cliente permite a un cliente de cola o tema retrasar el envío de un mensaje de un período de tiempo determinado. Si el cliente envía mensajes adicionales durante este período de tiempo, transmite los mensajes en un único lote. También por lotes del cliente hace que un cliente de cola o suscripción por lotes varias solicitudes **completado** en una sola solicitud. Procesamiento por lotes solo está disponible para operaciones de **Enviar** y **completado** asincrónica. Operaciones sincrónicas se envían inmediatamente al servicio de Bus de servicio. Procesamiento por lotes se produce para el método peek ni recibir operaciones ni por lotes produce entre clientes.

Si el lote supera el tamaño máximo de mensaje, el último mensaje se quitará el lote y el cliente envía inmediatamente el lote. El último mensaje se convierte en el primer mensaje de la sección siguiente. De forma predeterminada, un cliente utiliza un intervalo de lote de 20 ms. Puede cambiar el intervalo de proceso por lotes estableciendo la propiedad [BatchFlushInterval][] antes de crear el generador de mensajería. Esta configuración afecta a todos los clientes que se crean con este generador.

Para deshabilitar el procesamiento por lotes, establezca la propiedad [BatchFlushInterval][] a **TimeSpan.Zero**. Por ejemplo:

```
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Procesamiento por lotes no afecta a la cantidad de operaciones de mensajería facturables y solo está disponible para el protocolo de cliente de Bus de servicio. El protocolo HTTP no admite el procesamiento por lotes.

## <a name="batching-store-access"></a>Acceso de almacenamiento por lotes

Para mejorar el rendimiento de una cola, el tema o la suscripción, Bus de servicio lotes varios mensajes cuando escribe a su almacenamiento interno. Si está habilitado en una cola o un tema, se procesarse escribir mensajes en la tienda. Si está habilitado en una suscripción o cola, eliminar mensajes de la tienda se ser por lotes. Si access almacén por lotes está habilitada para una entidad, Bus de servicio retrasa una operación de escritura de la tienda relacionada con esa entidad por hasta 20 ms. Operaciones de almacenamiento adicional que se producen durante este intervalo se agregan al lote. Acceso al almacén por lotes sólo afecta a las operaciones de **Enviar** y **completado** ; recibir operaciones no se ven afectadas. Acceso al almacén por lotes es una propiedad de una entidad. Procesamiento por lotes se produce en todas las entidades que habilitar el acceso de la tienda por lotes.

Al crear una nueva cola, tema o suscripción, acceso de almacenamiento por lotes está habilitado de forma predeterminada. Para deshabilitar el acceso de la tienda por lotes, establezca la propiedad [EnableBatchedOperations][] en **false** antes de crear la entidad. Por ejemplo:

```
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Acceso al almacén por lotes no afecta a la cantidad de operaciones de mensajería facturables y es una propiedad de una cola, un tema o una suscripción. Es independiente del modo de recepción y el protocolo que se utiliza entre un cliente y el servicio de Bus de servicio.

## <a name="prefetching"></a>Captura previa

Captura previa permite que el cliente de cola o de suscripción cargar mensajes adicionales del servicio cuando realiza una operación de recepción. El cliente almacena estos mensajes en una caché local. El tamaño de la caché depende de las propiedades [QueueClient.PrefetchCount][] o [SubscriptionClient.PrefetchCount][] . Cada cliente que permite la captura previa mantiene su propia caché. No se comparte una caché a través de los clientes. Si el cliente inicia una operación de recepción y su caché está vacía, el servicio transmite un lote de mensajes. El tamaño del lote es igual al tamaño de la memoria caché o 256KB, lo que sea menor. Si el cliente inicia una operación de recepción y la memoria caché contiene un mensaje, el mensaje se toma de la memoria caché.

Cuando un mensaje se recupera previamente, el servicio bloquea el mensaje había. Haciendo lo siguiente: no se puede recibir el mensaje había un receptor diferente. Si el destinatario no puede completar el mensaje antes de que expire el bloqueo, el mensaje está disponible para otros receptores. La había copia del mensaje permanece en la memoria caché. El receptor que consume la copia almacenada en caché expirada recibirá una excepción cuando intenta completar dicho mensaje. De forma predeterminada, el bloqueo de mensaje caduca después de 60 segundos. Este valor se puede ampliar a 5 minutos. Para evitar que el consumo de los mensajes expirados, el tamaño de caché siempre debe ser menor que el número de mensajes que puede utilizar un cliente dentro del intervalo de tiempo de espera de bloqueo.

Cuando se usa la caducidad de bloqueo predeterminado de 60 segundos, un buen valor para [SubscriptionClient.PrefetchCount][] es 20 veces el procesamiento máximo tasas de todos los receptores de la fábrica. Por ejemplo, un generador crea 3 receptores y cada receptor puede procesar hasta 10 mensajes por segundo. El recuento de búsqueda no debe superar 20\*3\*10 = 600. De forma predeterminada, [QueueClient.PrefetchCount][] se establece en 0, lo que significa que no hay mensajes adicionales se obtienen del servicio.

Captura previa mensajes aumenta el rendimiento general de una cola o de suscripción porque reduce el número total de operaciones de mensajes o de ida y vuelta. Recuperar el primer mensaje, sin embargo, se realizará (debido al tamaño de mensaje mayor). Recibir mensajes había será más rápida porque ya se ha descargado estos mensajes por el cliente.

El servidor comprueba la time to live (TTL) (propiedad) de un mensaje en el momento en que el servidor envía el mensaje al cliente. El cliente no comprueba la propiedad del mensaje TTL cuando reciba el mensaje. En su lugar, se pueden recibir el mensaje incluso si ha pasado TTL del mensaje mientras el mensaje fue almacenados en caché por el cliente.

Captura previa no afecta a la cantidad de operaciones de mensajería facturables y solo está disponible para el protocolo de cliente de Bus de servicio. El protocolo HTTP no admite la captura previa de datos. Captura previa está disponible para las operaciones de recepción sincrónico y asincrónico.

## <a name="express-queues-and-topics"></a>Express colas y temas

Entidades Express habilitar de alto rendimiento y escenarios de latencia de reducido. Con entidades express, si se envía un mensaje a una cola o un tema, el mensaje no es almacenan inmediatamente en el almacén de mensajes. En su lugar, se almacena en caché en memoria. Si un mensaje permanece en la cola durante más de unos segundos, se escribe automáticamente al almacenamiento, lo que se protege contra la pérdida debido a un corte estable. Escribir el mensaje en la memoria caché aumenta el rendimiento y reduce la latencia porque no hay ningún acceso al almacenamiento estable en el momento en que se envió el mensaje. Los mensajes que se consumen en unos segundos no se escriben en el almacén de mensajes. En el ejemplo siguiente se crea un tema express.

```
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Si se envía un mensaje que contiene información importante que no se pueden perder a una entidad express, el remitente puede forzar Bus de servicio para conservar inmediatamente el mensaje al almacenamiento estable estableciendo la propiedad [ForcePersistence][] en **true**.

## <a name="use-of-partitioned-queues-or-topics"></a>Uso de temas o colas divididas

Internamente, Bus de servicio usa el mismo nodo y mensajería almacenar para procesar y almacenar todos los mensajes de una entidad de mensajería (cola o tema). Una cola dividida o un tema, por otro lado, se distribuye entre varios nodos y mensajería stores. Temas y colas divididas RENDTO no solo un rendimiento mayor que colas normales y temas, también muestran disponibilidad superior. Para crear una entidad dividida, establezca la propiedad de [EnablePartitioning][] en **true**, tal como se muestra en el ejemplo siguiente. Para obtener más información sobre las entidades divididas, consulte [particiones entidades mensajería][].

```
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="use-of-multiple-queues"></a>Uso de varias colas

Si no es posible usar dividida cola o tema o no se puede controlar la carga esperada por una sola cola dividida o un tema, debe usar varias entidades mensajería. Al usar varias entidades, cree a un cliente dedicado para cada entidad, en lugar de usar al mismo cliente para todas las entidades.

## <a name="development--testing-features"></a>Características de pruebas y desarrollo

Bus de servicio tiene una característica que se utiliza para el desarrollo que **nunca debe usarse en configuraciones de producción**.

[TopicDescription.EnableFilteringMessagesBeforePublishing](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing.aspx)
- Cuando se agregan nuevas reglas o filtros al tema, EnableFilteringMessagesBeforePublishing puede usarse para comprobar que la nueva expresión de filtro funciona según lo esperado.

## <a name="scenarios"></a>Escenarios

Las secciones siguientes describen situaciones típicas de mensajería y la configuración de Bus de servicio preferida de esquema. Tasas de rendimiento se clasifican como pequeña (menos de 1 mensaje/segundo), moderado (1 mensaje por segundo o mayor pero menor que 100 mensajes/segundo) y alto (100 mensajes/segundo o mayor). El número de clientes se clasifica como pequeño (5 o menos), moderado (más de 5 pero menor o igual a 20) y grandes (más de 20).

### <a name="high-throughput-queue"></a>Cola de alto rendimiento

Objetivo: Maximizar el rendimiento de una sola cola. El número de remitentes y destinatarios es pequeño.

-   Use una cola dividida para disponibilidad y mejor rendimiento.

-   Para aumentar la velocidad de envío global en la cola, use varias fábricas de mensaje para crear remitentes. Para cada remitente, use las operaciones asincrónica o varios subprocesos.

-   Para aumentar la velocidad de recepción global de la cola, use varias fábricas de mensaje para crear los receptores.

-   Utilizar operaciones asíncronas sacar partido de procesamiento por lotes del cliente.

-   Establecer el intervalo de procesamiento por lotes a 50 ms. para reducir el número de transmisiones de protocolo de cliente de Bus de servicio. Si se usan varios remitentes, aumente el intervalo por lotes a 100 ms.

-   Deje habilitado el acceso de almacenamiento por lotes. Esto aumenta la velocidad global a la que se pueden escribir mensajes en la cola.

-   Establecer el recuento de búsqueda en 20 veces el procesamiento máximo tasas de todos los receptores de una fábrica. Esto reduce el número de transmisiones de protocolo de cliente de Bus de servicio.

### <a name="multiple-high-throughput-queues"></a>Varias colas de alto rendimiento

Objetivo: Maximizar el rendimiento general de varias colas. El rendimiento de una cola individual es moderado o alto.

Para obtener el máximo rendimiento a través de varias colas, utilice la configuración de esquemas para maximizar el rendimiento de una sola cola. Además, puede usar distintas fábricas para crear a clientes que enviar o reciban desde distintas colas.

### <a name="low-latency-queue"></a>Cola de baja latencia

Objetivo: Minimizar la latencia de llevar a cabo de una cola o un tema. El número de remitentes y destinatarios es pequeño. El rendimiento de la cola es pequeño o moderado.

-   Use una cola con particiones para mejorar la disponibilidad.

-   Deshabilitar por lotes del cliente. El cliente envía un mensaje de inmediato.

-   Deshabilitar el acceso de la tienda por lotes. El servicio escribe inmediatamente el mensaje a la tienda.

-   Si usa a un solo cliente, establezca el recuento de búsqueda en 20 veces la velocidad de procesamiento del receptor. Si varios mensajes llegan a la cola al mismo tiempo, el protocolo de cliente de Bus de servicio transmite todas al mismo tiempo. Cuando el cliente recibe el mensaje siguiente, ese mensaje ya está en la memoria caché local. La memoria caché debería pequeña.

-   Si usa a varios clientes, establezca el recuento de búsqueda en 0. Al hacerlo, el segundo cliente puede recibir el segundo mensaje mientras el primer cliente todavía está procesando el primer mensaje.

### <a name="queue-with-a-large-number-of-senders"></a>Cola con una gran cantidad de remitentes

Objetivo: Maximizar el rendimiento de una cola o un tema con un gran número de remitentes. Cada remitente que envía mensajes con una tasa moderado. El número de receptores es pequeño.

Bus de servicio permite hasta 1000 conexiones simultáneas a una entidad de mensajería (o 5000 con AMQP). Este límite se aplica en el nivel de espacio de nombres y colas, temas y suscripciones se limita por el límite de conexiones simultáneas por espacio de nombres. Para colas, este número se comparte entre remitentes y destinatarios. Si todas las conexiones de 1000 son necesarias para remitentes, debe reemplazar la cola con un tema y una sola suscripción. Un tema acepta hasta 1000 conexiones simultáneas de remitentes, mientras que la suscripción acepta una adicionales 1000 conexiones simultáneas de los receptores. Si se requiere más de 1000 remitentes simultáneas, los remitentes deben enviar mensajes del protocolo de Bus de servicio a través de HTTP.

Para maximizar el rendimiento, haga lo siguiente:

-   Use una cola dividida para disponibilidad y mejor rendimiento.

-   Si cada remitente que reside en un proceso diferente, use solo una única fábrica por el proceso.

-   Utilizar operaciones asíncronas sacar partido de procesamiento por lotes del cliente.

-   Usar el predeterminado por lotes intervalo de 20 ms para reducir el número de transmisiones de protocolo de cliente de Bus de servicio.

-   Deje habilitado el acceso de almacenamiento por lotes. Esto aumenta la velocidad global a la que se pueden escribir mensajes en la cola o tema.

-   Establecer el recuento de búsqueda en 20 veces el procesamiento máximo tasas de todos los receptores de una fábrica. Esto reduce el número de transmisiones de protocolo de cliente de Bus de servicio.

### <a name="queue-with-a-large-number-of-receivers"></a>Con un gran número de destinatarios en cola

Objetivo: Maximizar la velocidad de recepción de una cola o una suscripción con un gran número de receptores. Cada destinatario recibe mensajes a una velocidad moderada. El número de remitentes es pequeño.

Bus de servicio permite hasta 1000 conexiones simultáneas a una entidad. Si una cola requiere más de 1000 receptores, debe reemplazar la cola con un tema y varias suscripciones. Cada suscripción puede admitir 1000 conexiones simultáneas. Como alternativa, los receptores pueden tener acceso a la cola mediante el protocolo HTTP.

Para maximizar el rendimiento, haga lo siguiente:

-   Use una cola dividida para disponibilidad y mejor rendimiento.

-   Si cada receptor reside en un proceso diferente, use solo una única fábrica por el proceso.

-   Los receptores pueden usar operaciones sincrónicas o asincrónicas. Dado que la tasa de recepción moderado de un receptor individual, por lotes del cliente de una solicitud completa no afecta a rendimiento receptor.

-   Deje habilitado el acceso de almacenamiento por lotes. Esto reduce la carga total de la entidad. También se reduce la velocidad global a la que se pueden escribir mensajes en la cola o tema.

-   Establecer el recuento de búsqueda en un valor pequeño (por ejemplo, PrefetchCount = 10). Esto impide los receptores inactivo mientras otros receptores tienen un gran número de mensajes en caché.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Tema con un pequeño número de suscripciones

Objetivo: Maximizar el rendimiento de un tema con un pequeño número de suscripciones. Se recibe un mensaje muchas de las suscripciones, lo que significa que la tasa de recepción combinada sobre todas las suscripciones es mayor que la tasa de envío. El número de remitentes es pequeño. El número de receptores por suscripción es pequeño.

Para maximizar el rendimiento, haga lo siguiente:

-   Usar un tema con particiones para disponibilidad y mejor rendimiento.

-   Para aumentar la velocidad de envío global en el tema, use varias fábricas de mensaje para crear remitentes. Para cada remitente, use las operaciones asincrónica o varios subprocesos.

-   Para aumentar la velocidad de recepción global de una suscripción, use varias fábricas de mensaje para crear los receptores. Para cada receptor, use las operaciones asincrónica o varios subprocesos.

-   Utilizar operaciones asíncronas sacar partido de procesamiento por lotes del cliente.

-   Usar el predeterminado por lotes intervalo de 20 ms para reducir el número de transmisiones de protocolo de cliente de Bus de servicio.

-   Deje habilitado el acceso de almacenamiento por lotes. Esto aumenta la velocidad global a la que se pueden escribir mensajes en el tema.

-   Establecer el recuento de búsqueda en 20 veces el procesamiento máximo tasas de todos los receptores de una fábrica. Esto reduce el número de transmisiones de protocolo de cliente de Bus de servicio.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Tema con un gran número de suscripciones

Objetivo: Maximizar el rendimiento de un tema con un gran número de suscripciones. Se recibe un mensaje muchas de las suscripciones, lo que significa que la tasa de recepción combinada sobre todas las suscripciones es mucho mayor que la tasa de envío. El número de remitentes es pequeño. El número de receptores por suscripción es pequeño.

Temas con un gran número de suscripciones suelen exponen un rendimiento general bajo si todos los mensajes se enrutan a todas las suscripciones. Esto se debe al hecho de que cada mensaje se ha recibido muchas veces y, a continuación, todos los mensajes que se encuentran en un tema y todas sus suscripciones se almacenan en el mismo almacén. Se supone que el número de remitentes y el número de receptores por suscripción son más pequeños. Bus de servicio es compatible con un máximo de 2.000 suscripciones por tema.

Para maximizar el rendimiento, haga lo siguiente:

-   Usar un tema con particiones para disponibilidad y mejor rendimiento.

-   Utilizar operaciones asíncronas sacar partido de procesamiento por lotes del cliente.

-   Usar el predeterminado por lotes intervalo de 20 ms para reducir el número de transmisiones de protocolo de cliente de Bus de servicio.

-   Deje habilitado el acceso de almacenamiento por lotes. Esto aumenta la velocidad global a la que se pueden escribir mensajes en el tema.

-   Establecer el recuento de búsqueda en la tasa de recepción esperada 20 horas en segundos. Esto reduce el número de transmisiones de protocolo de cliente de Bus de servicio.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la optimización del rendimiento de Bus de servicio, vea [con particiones mensajería entidades][].

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [PeekLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [ReceiveAndDelete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [BatchFlushInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.netmessagingtransportsettings.batchflushinterval.aspx
  [EnableBatchedOperations]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations.aspx
  [QueueClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.prefetchcount.aspx
  [SubscriptionClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.prefetchcount.aspx
  [ForcePersistence]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.forcepersistence.aspx
  [EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [Entidades de mensajería divididas]: service-bus-partitioning.md
  