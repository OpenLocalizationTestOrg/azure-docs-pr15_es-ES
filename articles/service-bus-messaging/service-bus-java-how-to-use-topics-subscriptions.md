<properties
    pageTitle="Cómo usar temas de Bus de servicio con Java | Microsoft Azure"
    description="Obtenga información sobre cómo usar temas de Bus de servicios y suscripciones en Azure. Ejemplos de código se escriben para las aplicaciones de Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Cómo utilizar las suscripciones y temas de Bus de servicio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Esta guía describe cómo utilizar las suscripciones y temas de Bus de servicio. Los ejemplos están escritos en Java y usan el [SDK de Azure para Java][]. Las situaciones de ejemplo incluyen la **creación de temas y suscripciones**, **crear filtros de suscripción**, **enviar mensajes a un tema**, **recibir mensajes de una suscripción**y **eliminar temas y suscripciones**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>¿Cuáles son las suscripciones y temas de Bus de servicio?

Las suscripciones y temas de Bus de servicio de soporte técnico una *publicación o suscripción* mensajería modelo de comunicación. Al usar temas y suscripciones, componentes de una aplicación distribuida no comunican directamente con otros; en su lugar intercambiar mensajes a través de un tema, que actúa como intermediario.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

A diferencia de colas de Bus de servicio, en el que cada mensaje se procesa por un único consumidor, temas y suscripciones proporcionan una forma de "uno a varios" de comunicación, utilizando un modelo de publicación y suscripción. Es posible registrar varias suscripciones a un tema. Cuando se envía un mensaje a un tema, a continuación, estará disponible para cada suscripción con controlador/proceso de forma independiente.

Una suscripción a un tema se asemeja a una cola virtual que recibe copia de los mensajes que se han enviado al tema. Si lo desea, puede registrar reglas de filtro para un tema en una base de cada suscripción, lo que le permite filtrar o restringir las suscripciones de tema reciben los mensajes en un tema.

Temas de Bus de servicios y suscripciones le permiten escalar para procesar un gran número de mensajes a través de un gran número de usuarios y aplicaciones.

## <a name="create-a-service-namespace"></a>Crear un espacio de nombres de servicio

Para empezar a usar temas de Bus de servicios y suscripciones en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres proporciona un contenedor de ámbito para el direccionamiento de recursos de Bus de servicio de la aplicación.

Para crear un espacio de nombres:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar la aplicación para usar Bus de servicio

Asegúrese de que ha instalado el [SDK de Azure para Java][] antes de generar este ejemplo. Si está utilizando Eclipse, puede instalar el [Kit de herramientas de Azure para Eclipse][] que incluye el SDK de Azure para Java. A continuación, puede agregar las **Bibliotecas de Microsoft Azure de Java** a un proyecto:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Agregue las siguientes instrucciones de importación a la parte superior del archivo Java:

```
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Agregar las bibliotecas de Azure para Java a la ruta de acceso de compilación e incluirlo en su conjunto de implementación de proyecto.

## <a name="create-a-topic"></a>Crear un tema

A través de la clase **ServiceBusContract** , se pueden realizar operaciones de administración de temas de Bus de servicio. Se crea un objeto de **ServiceBusContract** con una configuración adecuada encapsulado el token de SA con permisos para administrar y la clase **ServiceBusContract** es el único punto de comunicación con Azure.

La clase **ServiceBusService** proporciona métodos para crear, enumerar y eliminar temas. En el ejemplo siguiente se muestra cómo un objeto **ServiceBusService** puede utilizarse para crear un tema denominado `TestTopic`, con un espacio de nombres denominado `HowToSample`:

    Configuration config =
        ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

    ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
    try  
    {
        CreateTopicResult result = service.createTopic(topicInfo);
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Métodos de **TopicInfo** habilitar las propiedades del tema se almacenen (por ejemplo: establecer el valor predeterminado de time to live (TTL) que se aplique a los mensajes enviados al tema). En el ejemplo siguiente se muestra cómo crear un tema denominado `TestTopic` con un tamaño máximo de 5 GB:

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

Tenga en cuenta que puede usar el método **listTopics** en **ServiceBusContract** objetos para comprobar si ya existe un tema con un nombre especificado dentro de un espacio de nombres de servicio.

## <a name="create-subscriptions"></a>Crear suscripciones

Suscripciones a temas también se crean con la clase **ServiceBusService** . Las suscripciones se denominan y pueden tener un filtro opcional que restringe el conjunto de mensajes que se pasan a cola virtual de la suscripción.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Crear una suscripción con el filtro predeterminado (MatchAll)

El filtro de **MatchAll** es el filtro predeterminado que se usa si no se especifica ningún filtro cuando se crea una nueva suscripción. Cuando se utiliza el filtro **MatchAll** , todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción denominada "AllMessages" y se usa el filtro de **MatchAll** predeterminado.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### <a name="create-subscriptions-with-filters"></a>Crear suscripciones con los filtros

También puede crear filtros que le permiten ámbito que se deben mostrar los mensajes enviados a un tema de la suscripción de un tema específico.

El tipo de filtro admitido por suscripciones más flexible es [SqlFilter][], que implementa un subconjunto de SQL92. Filtros SQL funcionan en las propiedades de los mensajes que se publican en el tema. Para obtener más detalles acerca de las expresiones que se pueden usar con un filtro SQL, revise la sintaxis de la [SqlFilter.SqlExpression][] .

En el ejemplo siguiente se crea una suscripción denominada `HighMessages` con un objeto [SqlFilter][] que solo selecciona los mensajes que tienen una propiedad personalizada del **MessageNumber** superior a 3:

```
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

Asimismo, en el ejemplo siguiente se crea una suscripción denominada `LowMessages` con un objeto de [SqlFilter][] que selecciona sólo los mensajes que tienen una propiedad **MessageNumber** o igual a 3:

```
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Cuando un mensaje ahora se envía a `TestTopic`, siempre se entregarán a receptores suscritos a la `AllMessages` suscripción y selectivamente entregado a receptores suscritos a la `HighMessages` y `LowMessages` suscripciones (según el contenido del mensaje).

## <a name="send-messages-to-a-topic"></a>Enviar mensajes a un tema

Para enviar un mensaje a un tema de Bus de servicio, la aplicación obtiene un objeto **ServiceBusContract** . El código siguiente muestra cómo enviar un mensaje el `TestTopic` tema creado previamente dentro de la `HowToSample` espacio de nombres:

```
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Los mensajes enviados a temas de Bus de servicio son instancias de la clase [BrokeredMessage][] . [BrokeredMessage][] *objetos tienen un conjunto de métodos estándar (como * *setLabel* * y * *período de vida**), un diccionario que se utiliza para contener propiedades personalizadas de específicos de la aplicación y un cuerpo de datos de aplicación arbitrario. Una aplicación puede establecer el cuerpo del mensaje pasando cualquier objeto serializable al constructor de la [BrokeredMessage][]y la correspondiente * *DataContractSerializer* * se utilizará para serializar el objeto. Como alternativa, un * *java.io.InputStream** puede proporcionar.

En el ejemplo siguiente se muestra cómo enviar cinco probar mensajes a la `TestTopic` **MessageSender** hemos recopilado del fragmento de código anterior.
Observe cómo varía el valor de propiedad **MessageNumber** de cada mensaje en la iteración del bucle (Esto determinará qué suscripciones reciban):

```
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Temas de Bus de servicio compatible con el tamaño máximo de mensaje de 256 KB en el [nivel estándar](service-bus-premium-messaging.md) y 1 MB en el [nivel de Premium](service-bus-premium-messaging.md). El encabezado, que incluye las propiedades de la aplicación estándar y personalizados, puede tener un tamaño máximo de 64 KB. No hay ningún límite del número de mensajes en un tema, pero hay un límite en el tamaño total de los mensajes que llevan a cabo por un tema. El tamaño de este tema se define en la hora de creación, con un límite máximo de 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Cómo recibir mensajes de una suscripción

Para recibir mensajes de una suscripción, use un objeto **ServiceBusContract** . Mensajes recibidos pueden trabajar en dos modos: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo de **ReceiveAndDelete** recibir es una operación de captura solo - es decir, cuando Bus de servicio recibe una solicitud de lectura para un mensaje, se marca el mensaje como consumida y devuelve a la aplicación. Modo de **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar una aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Dado que Bus de servicio habrá marcar el mensaje como consumida, cuando la aplicación se reinicia y empieza a utilizar de nuevo, los mensajes habrá perdido el mensaje que se ha utilizado antes el bloqueo.

En modo de **PeekLock** , recibir se convierte en una operación de dos fases que permite a las aplicaciones de soporte técnico que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe una solicitud, encuentra el mensaje siguiente consumir, bloquea para evitar que otros consumidores recibirlo y, a continuación, se devuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción llamando **Eliminar** en el mensaje recibido. Cuando Bus de servicio ve la llamada **Eliminar** , se marca el mensaje como consumida y quitar del tema.

El ejemplo siguiente muestra cómo mensajes se pueden recibir y procesar utilizando el modo de **PeekLock** (no en el modo de forma predeterminada). El ejemplo siguiente realiza un bucle y procesa los mensajes en la suscripción "HighMessages" y, a continuación, se cierra cuando no hay mensajes sin más (como alternativa, puede establecerse que esperar para mensajes nuevos).

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Cómo controlar los mensajes no se puede leer y bloqueos de la aplicación

Bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de errores en la aplicación o dificultades para procesar un mensaje. Si una aplicación de receptor no puede procesar el mensaje por algún motivo, puede llamar al método **unlockMessage** en el mensaje recibido (en lugar de en el método **deleteMessage** ). Esto provocará Bus de servicio desbloquear el mensaje dentro del tema y que esté disponible para recibir nuevamente, por la misma aplicación consumir o por otra aplicación consume.

También es un tiempo de espera asociado a un mensaje bloqueado dentro del tema, y si se produce un error en la aplicación procesar el mensaje antes del tiempo de espera de bloqueo caduca (por ejemplo, si se bloquea la aplicación), se desbloquear el mensaje automáticamente y que esté disponible para recibir nuevo Bus de servicio.

En caso de que la aplicación se bloquea después de procesar el mensaje, pero antes de que se envía la solicitud de **deleteMessage** , a continuación, el mensaje se volverá a entregar a la aplicación cuando se reinicia. A menudo se denomina **Al menos una vez procesamiento**, es decir, cada mensaje se procesará al menos una vez pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, los desarrolladores de aplicaciones deben agregar lógica adicional a su aplicación para controlar la entrega de mensajes duplicados. A menudo, esto se logra utilizando el método **getMessageId** del mensaje, que permanecerá constante entre intentos de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar temas y suscripciones

La forma principal para eliminar los temas y las suscripciones es utilizar un objeto **ServiceBusContract** . Eliminación de un tema, también se eliminarán las suscripciones que están registradas con el tema. Suscripciones también se pueden eliminar de forma independiente.

```
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de colas de Bus de servicio, consulte [suscripciones, temas y colas de Bus de servicio][] para obtener más información.

  [Azure SDK de Java]: http://azure.microsoft.com/develop/java/
  [Kit de herramientas de Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [Suscripciones, temas y colas de Bus de servicio]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx 
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  
  [0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
