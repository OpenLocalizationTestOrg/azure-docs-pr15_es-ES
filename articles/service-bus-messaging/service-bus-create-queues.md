<properties 
    pageTitle="Escribir aplicaciones que utilicen colas de Bus de servicio | Microsoft Azure"
    description="Cómo escribir una aplicación sencilla basada en cola que usa Bus de servicio."
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-queues"></a>Crear aplicaciones que usen colas de Bus de servicio

Este tema describe colas de Bus de servicio y muestra cómo escribir una aplicación sencilla basada en cola que usa Bus de servicio.

Considere la posibilidad de un escenario del mundo de comercio por menor en la que los datos de ventas de terminales individuales de Point (OC) deben dirigirse a un sistema de administración de inventario que usa los datos para determinar si tiene material necesario reponer. Esta solución utiliza Bus de servicios de mensajería para la comunicación entre los terminales y el sistema de administración de inventario, tal como se muestra en la siguiente ilustración:

![Imagen de colas de Bus de servicio 1](./media/service-bus-create-queues/IC657161.gif)

Cada terminal OC informes de sus datos de ventas mediante el envío de mensajes a la **DataCollectionQueue**. Estos mensajes permanecen en esta cola hasta que se recuperan por el sistema de administración de inventario. Este patrón a menudo se denomina *mensajería asincrónica*, porque el terminal OC no tiene que esperar una respuesta desde el sistema de administración de inventario para continuar el proceso.

## <a name="why-queuing"></a>¿Por qué Queue?

Antes de que observamos el código que es necesario configurar esta aplicación, tenga en cuenta las ventajas del uso de una cola en este escenario en lugar de tener los terminales OC hablar directamente (sincrónicamente) el sistema de administración de inventario.

### <a name="temporal-decoupling"></a>Desacoplar temporal

Con el modelo de mensajería asincrónico, productor y los consumidores no deben estar en línea al mismo tiempo. La infraestructura de mensajería confiable almacena los mensajes hasta que la parte que está preparada para recibirlos. Esto significa que los componentes de la aplicación distribuida pueden estar desconectados, o bien voluntario; Por ejemplo, para realizar el mantenimiento o debido a un bloqueo de componente, sin que afecte a todo el sistema. Además, la aplicación que puede tener solo a estar en línea durante determinadas horas del día. Por ejemplo, en este escenario minorista, el sistema de administración de inventario sólo deba conectarse al final del día de la empresa.

### <a name="load-leveling"></a>Cargar la redistribución

En muchas aplicaciones de carga del sistema varía con el tiempo, mientras que el tiempo de procesamiento necesario para cada unidad de trabajo se suelen constante. Intermediating productor de mensaje y los consumidores de una cola significa que la aplicación consume (trabajador) solo estén configurados para atender un promedio de carga en lugar de una carga de recursos asignadas. La profundidad de la cola crecerá y contrato como varía la carga entrante. Esto directamente ahorra dinero con respecto a la cantidad de infraestructura necesaria para la carga de la aplicación de servicio.

![Imagen de colas de Bus de servicio 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Equilibrio de carga

A medida que aumenta la carga, más procesos de trabajo pueden agregarse a leer en la cola de trabajo. Cada mensaje se procesa solamente mediante uno de los procesos de trabajo. Además, este equilibrio de carga en función de extracción permite un uso óptimo de los equipos de trabajo incluso si difieren de los equipos de trabajo con respecto a la potencia de procesamiento, tal como extraerá los mensajes a su propia velocidad máxima. Este patrón a menudo se denomina el modelo consumidor competencia.

![Imagen de colas de Bus de servicio 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Acoplamiento flexible

Utilizando message Queue Server a intermedio entre los consumidores y productor mensaje proporciona un acoplamiento flexible intrínseca entre los componentes. Porque productor y los consumidores no tienen constancia de los demás, un consumidor puede actualizarse sin tener ningún efecto en el productor. Además, la topología de mensajería puede evolucionar sin afectar a los extremos existentes. Trataremos este más cuando hablemos de publicación o suscripción.

## <a name="show-me-the-code"></a>Mostrar el código

En la sección siguiente muestra cómo usar Bus de servicio para generar esta aplicación.

### <a name="sign-up-for-an-azure-account"></a>Registrarse para una cuenta de Azure

Tendrá una cuenta de Azure para empezar a trabajar con Bus de servicio. Si ya tiene una, puede registrarse para una cuenta gratuita [aquí](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Crear un espacio de nombres

Una vez que tenga una suscripción, puede [crear un nuevo espacio de nombres](service-bus-create-namespace-portal.md). Cada espacio de nombres actúa como un contenedor de ámbito de un conjunto de entidades de Bus de servicio. Dé un nombre único para el nuevo espacio de nombres en todas las cuentas de Bus de servicio. 

### <a name="install-the-nuget-package"></a>Instalar el paquete de NuGet

Para usar el espacio de nombres de Bus de servicio, una aplicación debe hacer referencia al ensamblado de Bus de servicio, específicamente Microsoft.ServiceBus.dll. Puede encontrar este ensamblado como parte de Microsoft Azure SDK y la descarga está disponible en la [página de descarga de Azure SDK](https://azure.microsoft.com/downloads/). Sin embargo, el [paquete de servicio Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) es la manera más sencilla de obtener la API de Bus de servicio y configurar la aplicación con todas las dependencias de Bus de servicio.

### <a name="create-the-queue"></a>Crear la cola

Operaciones de administración de Bus de servicios de mensajería entidades (colas y publicación o suscripción temas) se realizan a través de la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Bus de servicio usa un modelo de seguridad basada en [Firma de acceso compartido (SA)](service-bus-sas-overview.md) . La clase [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa un proveedor de token de seguridad con métodos de fábrica integrados devolver algunos proveedores de tokens conocidos. Usaremos un método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para almacenar las credenciales de SA. A continuación, se crea la instancia [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) con la dirección base para el espacio de nombres de Bus de servicio y el proveedor de tokens.

La clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) proporciona métodos para crear, enumerar y eliminar entidades mensajería. El código que se muestra aquí muestra cómo se crea y utilizada para crear la cola de **DataCollectionQueue** la instancia [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Tenga en cuenta que existen sobrecargas del método [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) que habilitar las propiedades de la cola de ajustarse. Por ejemplo, puede establecer el valor predeterminado de time to live (TTL) para los mensajes enviados a la cola.

### <a name="send-messages-to-the-queue"></a>Enviar mensajes a la cola

Las operaciones de tiempo de ejecución de entidades de Bus de servicio; Por ejemplo, enviar y recibir mensajes, una aplicación primero debe crear un objeto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . De forma similar a la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , se crea la instancia de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) de la dirección base del espacio de nombres de servicio y el proveedor de tokens.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

Los mensajes envían a y recibidos de Bus de servicio colas son instancias de la clase [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Esta clase consta de un conjunto de propiedades estándar (como [etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) y [período de vida](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un diccionario que se utiliza para contener propiedades de la aplicación y un conjunto de datos de aplicación arbitrario. Una aplicación puede establecer el cuerpo pasando cualquier objeto serializable (en el ejemplo siguiente se pasa un objeto **SalesData** que representa los datos de ventas desde el terminal OC), que usará [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) para serializar el objeto. Como alternativa, puede proporcionar un objeto de [secuencia](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) .

Enviar mensajes a una cola determinada, en nuestro caso el **DataCollectionQueue**, la manera más sencilla es usar [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) para crear un objeto de [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) directamente desde la instancia de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Recibir mensajes de la cola

Para recibir mensajes de la cola, puede usar un objeto de [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que se crea directamente desde la [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) con [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Los receptores de mensaje pueden trabajar en dos modos: **ReceiveAndDelete** y **PeekLock**. La [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) se establece cuando se crea el receptor del mensaje, como un parámetro a la llamada [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) .


Cuando se utiliza el modo de **ReceiveAndDelete** , la recepción es una operación de captura solo; es decir, cuando Bus de servicio recibe la solicitud, marca el mensaje como consumida y devuelve a la aplicación. Modo de **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar la aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Debido a que Bus de servicio marca el mensaje como consumido, cuando el reinicio de la aplicación e inicia consumo mensajes nuevamente, habrá perdido el mensaje que se ha utilizado antes el bloqueo.

En el modo de **PeekLock** , la recepción se convierte en una operación de dos fases, lo que permite la compatibilidad con aplicaciones que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe la solicitud, encuentra el mensaje siguiente consumir, bloquea para evitar que otros consumidores recibirlo y, a continuación, se devuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción llamando [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) en el mensaje recibido. Cuando Bus de servicio detecta el [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) llamar, marca el mensaje como consumida.

Existen dos otros resultados. En primer lugar, si la aplicación no se puede procesar el mensaje por algún motivo, puede llamar [abandonar](https://msdn.microsoft.com/library/azure/hh181837.aspx) en el mensaje recibido (en lugar de [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Esto hace Bus de servicio desbloquear el mensaje y que esté disponible para recibir de nuevo, el mismo consumidor o por otro consumidor completar. En segundo término, hay un tiempo de espera asociado con el bloqueo y la aplicación no puede procesar el mensaje antes el bloqueo expire (por ejemplo, si se bloquea la aplicación), entonces se desbloquear el mensaje y que esté disponible para recibir nuevo Bus de servicio (básicamente, realizar una operación de [abandonar](https://msdn.microsoft.com/library/azure/hh181837.aspx) de forma predeterminada).

Tenga en cuenta que si la aplicación se bloquea después de que procesa el mensaje, pero antes de la [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) se emitió la petición, el mensaje se volverá a entregar a la aplicación cuando se reinicia. A menudo se denomina procesamiento *Al menos una vez* . Esto significa que cada mensaje se procesará al menos una vez, pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, se requiere la lógica adicional en la aplicación para detectar duplicados. Esto se puede conseguir en función de la propiedad de [Id. de mensaje](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) del mensaje. El valor de esta propiedad permanece constante entre intentos de entrega. Esto se denomina procesamiento *Exactamente una vez* .

El código que se muestra aquí recibe y procesa un mensaje con el modo de **PeekLock** , que es el valor predeterminado si explícitamente se proporciona ningún valor de [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) .

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

### <a name="use-the-queue-client"></a>Utilizar al cliente de cola

Los ejemplos anteriormente en esta sección crean objetos [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) y [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) directamente desde la [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) a enviar y recibir mensajes de la cola, respectivamente. Un enfoque alternativo es usar la clase [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) , que es compatible con enviar y recibir operaciones además de las características más avanzadas, como las sesiones.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de colas, vea [crear las aplicaciones que utilizan suscripciones y temas de Bus de servicio](service-bus-create-topics-subscriptions.md) para continuar esta discusión mediante las capacidades de publicación o suscripción de temas de Bus de servicios y suscripciones.