<properties 
    pageTitle="Crear aplicaciones que utilizan temas de Bus de servicios y suscripciones | Microsoft Azure"
    description="Introducción a la publicación-suscribirse capacidades ofrecidas por temas de Bus de servicios y suscripciones."
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
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Crear aplicaciones que utilicen las suscripciones y temas de Bus de servicio

Bus de servicios de Azure es compatible con un conjunto de tecnologías de software intermedio en la nube, orientados al mensaje incluyendo confiable message Queue y resistentes de publicación o suscripción de mensajería. En este artículo se basa en la información suministrada en [aplicaciones de creación que utilizan colas de Bus de servicio](service-bus-create-queues.md) y ofrece una introducción a las capacidades de publicación o suscripción ofrecidas por temas de Bus de servicio.

## <a name="evolving-retail-scenario"></a>Escenario de comercio por menor en evolución

En este artículo continúa el escenario minorista utilizado en [aplicaciones de creación que utilizan colas de Bus de servicio](service-bus-create-queues.md). Recuperar datos ventas desde cada punto de venta (OC) terminales deben dirigirse a un sistema de administración de inventario que esos datos se utiliza para determinar si tiene material necesario reponer. Cada terminal OC informes de sus datos de ventas mediante el envío de mensajes a la cola **DataCollectionQueue** , donde permanecen hasta que se reciben por el sistema de administración de inventario, como se muestra aquí:

![Bus de servicio 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Para evolucionando en este escenario, se ha agregado un nuevo requisito al sistema: el propietario de la tienda desea que puedan supervisar el rendimiento de la tienda en tiempo real.

Para solucionar este requisito, el sistema debe "puntee" la secuencia de datos de ventas. Queremos sigue cada mensaje enviado por los terminales de PC que se envíen en el sistema de administración de inventario como antes, pero queremos otra copia de cada mensaje que podemos utilizar para presentar la vista del panel al propietario de la tienda.

En cualquier situación como esta, en el que requieren cada mensaje para ser consumido por varias partes, puede usar *temas*de Bus de servicio. Temas proporcionan un modelo de publicación o suscripción en el que estará disponible para una o más suscripciones registradas con el tema de cada mensaje publicado. En cambio, con colas cada mensaje por un único consumidor.

Mensajes se envían a un tema de la misma manera se envían a una cola. Sin embargo, no se reciben mensajes desde el tema directamente; se reciben de suscripciones. Puede considerar que una suscripción a un tema como una cola virtual que recibe copias de los mensajes que se envían a ese tema. Reciben los mensajes de una suscripción de la misma manera en que se reciben de una cola.

Volver al escenario minorista, la cola se reemplaza por un tema y se agrega una suscripción, que puede utilizar el componente del sistema de administración de inventario. El sistema aparece ahora como sigue:

![Bus de servicio 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

La configuración aquí realiza idéntico al diseño basado en cola anterior. Es decir, se enrutan los mensajes enviados al tema de la suscripción de **inventario** , desde la que el **Sistema de administración de inventario** consume.

Para admitir el panel de administración, creamos una segunda suscripción en el tema, como se muestra aquí:

![Bus de servicio 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Con esta configuración, cada mensaje de los terminales OC estará disponible para suscripciones de **inventario** y **paneles** .

## <a name="show-me-the-code"></a>Mostrar el código

El artículo [crear las aplicaciones que utilizan colas de Bus de servicio](service-bus-create-queues.md) describe cómo crear un espacio de nombres de servicio e iniciar sesión en una cuenta de Azure. Para usar un espacio de nombres de Bus de servicio, una aplicación debe hacer referencia al ensamblado de Bus de servicio, específicamente Microsoft.ServiceBus.dll. Es la manera más sencilla de hacer referencia a las dependencias de Bus de servicio instalar el [paquete Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)de Bus de servicio. También puede encontrar el ensamblado como parte del SDK de Azure. La descarga está disponible en la [página de descarga de Azure SDK](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Crear el tema y suscripciones

Operaciones de administración de Bus de servicios de mensajería entidades (colas y publicación o suscripción temas) se realizan a través de la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Las credenciales adecuadas son necesarios para crear una instancia de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para un espacio de nombres concreto. Bus de servicio usa un modelo de seguridad basada en [Firma de acceso compartido (SA)](service-bus-sas-overview.md) . La clase [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa un proveedor de token de seguridad con métodos de fábrica integrados devolver algunos proveedores de tokens conocidos. Usaremos un método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para almacenar las credenciales de SA. A continuación, se crea la instancia [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) con la dirección base para el espacio de nombres de Bus de servicio y el proveedor de tokens.

La clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) proporciona métodos para crear, enumerar y eliminar entidades mensajería. El código que se muestra aquí muestra cómo se crea y utilizada para crear el tema **DataCollectionTopic** la instancia [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Tenga en cuenta que existen sobrecargas del método [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) que le permiten establecer las propiedades del tema. Por ejemplo, puede establecer el valor predeterminado de time to live (TTL) para los mensajes enviados al tema. A continuación, agregue las suscripciones de **inventario** y **paneles** .

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Enviar mensajes al tema

Las operaciones de tiempo de ejecución de entidades de Bus de servicio; Por ejemplo, enviar y recibir mensajes, una aplicación primero debe crear un objeto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . De forma similar a la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , se crea la instancia de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) de la dirección base del espacio de nombres de servicio y el proveedor de tokens.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Los mensajes envían a y recibidos de temas de Bus de servicio, son instancias de la clase [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Esta clase consta de un conjunto de propiedades estándar (como [etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) y [período de vida](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un diccionario que se utiliza para contener propiedades de la aplicación y un conjunto de datos de aplicación arbitrario. Una aplicación puede establecer el cuerpo pasando cualquier objeto serializable (en el ejemplo siguiente se pasa un objeto **SalesData** que representa los datos de ventas desde el terminal OC), que usará [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) para serializar el objeto. Como alternativa, puede proporcionar un objeto de [secuencia](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) .

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

Enviar mensajes al tema de la manera más sencilla es usar [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) para crear un objeto de [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) directamente desde la instancia de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Recibir mensajes de una suscripción

De forma similar al uso de colas, para recibir mensajes de una suscripción que puede utilizar un objeto [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) que se crea directamente desde la [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) con [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Puede usar uno de los dos diferentes recibir modos (**ReceiveAndDelete** y **PeekLock**), como se describe en [aplicaciones de creación que utilizan colas de Bus de servicio](service-bus-create-queues.md).

Tenga en cuenta que cuando se crea un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) para suscripciones, el parámetro *entityPath* del formulario `topicPath/subscriptions/subscriptionName`. Por lo tanto, para crear un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) para la suscripción de **inventario** del tema **DataCollectionTopic** , *entityPath* debe estar establecida en `DataCollectionTopic/subscriptions/Inventory`. El código aparece como sigue:

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
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

## <a name="subscription-filters"></a>Filtros de suscripción

Hasta ese momento, en este escenario todos los mensajes enviados al tema estarán disponibles en todas las suscripciones registradas. La frase clave aquí "estará disponible." Mientras las suscripciones de Bus de servicio ver todos los mensajes enviados al tema, puede copiar únicamente un subconjunto de los mensajes en la cola de suscripción virtual. Esto se realiza con *filtros*de suscripción. Cuando se crea una suscripción, puede proporcionar una expresión de filtro en el formulario de un predicado de estilo de SQL92 funciona a través de las propiedades del mensaje, las propiedades del sistema (por ejemplo, la [etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) y las propiedades de la aplicación, como **StoreName** en el ejemplo anterior.

Evolucionando el escenario para ilustrar esto, es un segundo almacén agregarse a nuestro escenario de comercio por menor. Datos de ventas de todos los terminales Extras de ambos stores sigue tendrán a enrutar el sistema de administración de inventario centralizada, pero un administrador de almacén mediante la herramienta de paneles solo está interesado en el rendimiento de ese almacén. Puede usar el filtrado para conseguirlo de suscripción. Tenga en cuenta que, cuando los terminales OC publicación mensajes, establezca la propiedad de aplicación **StoreName** en el mensaje. Dado dos almacena, por ejemplo, **Redmond** y **Seattle**, terminales de PC en el Redmond almacenan marca sus mensajes de datos de ventas con un **StoreName** igual a **Redmond**, mientras que los terminales de Seattle almacén OC usan un **StoreName** es igual a **Seattle**. El Administrador de almacén de la tienda de Redmond solo desea ver los datos de sus terminales de PC. El sistema aparece como sigue:

![Bus4 de servicio](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Para configurar esta ruta, se crea la suscripción de **panel** como sigue:

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Con este filtro de suscripción, solo los mensajes que tengan la propiedad **StoreName** establecer a **Redmond** se copiarán en la cola de la suscripción de **escritorio** virtual. Hay mucho más para filtrar la suscripción, no obstante. Aplicaciones pueden tener varias reglas de filtro por suscripción además de la capacidad de modificar las propiedades de un mensaje como pasa a cola virtual de suscripción.

## <a name="summary"></a>Resumen

Todas las razones para usar Queue descrito en [crear aplicaciones que utilizan colas de Bus de servicio](service-bus-create-queues.md) también se aplican a temas, específicamente:

- Desacoplar temporal: productor de mensaje y los consumidores no tienen que estar en línea al mismo tiempo.

- Cargar la redistribución: el tema habilitar las aplicaciones estén configurados para carga promedio en lugar de carga de recursos asignadas suaviza los picos de carga.

- Equilibrio de carga: similar a una cola, puede tener varios consumidores competencia escucha en una sola suscripción con cada mensaje de entrega a solo uno de los consumidores, con lo que Equilibrio de carga.

- Acoplamiento flexible: pueden evolucionar la red de mensajería sin que ello afecte extremos existentes; Por ejemplo, agregando suscripciones o cambiando filtros a un tema para permitir que los consumidores de nuevos.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo usar colas en el caso de minorista de PC, vea [crear aplicaciones que utilizan colas de Bus de servicio](service-bus-create-queues.md) .