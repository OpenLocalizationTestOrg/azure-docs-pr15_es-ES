<properties
    pageTitle="Cómo usar colas de Bus de servicio con Java | Microsoft Azure"
    description="Obtenga información sobre cómo usar colas de Bus de servicio en Azure. Ejemplos de código escritos en Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Cómo usar colas de Bus de servicio

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artículo describe cómo usar colas de Bus de servicio. Los ejemplos están escritos en Java y usan el [SDK de Azure para Java][]. Los escenarios cubiertos incluyen **crear colas**, **Enviar y recibir mensajes**y **eliminar colas**.

## <a name="what-are-service-bus-queues"></a>¿Cuáles son las colas de Bus de servicio?

Colas de Bus de servicio de soporte técnico un modelo de comunicación **con mensajería** . Al usar colas, componentes de una aplicación distribuida no comunican directamente con otros; en su lugar intercambiar mensajes a través de una cola, que actúa como intermediario (agente). Un productor de mensaje (remitente) entrega un mensaje a la cola y continúa su procesamiento.
De forma asincrónica, un consumidor de mensaje (receptor) extrae el mensaje de la cola y lo procesa. El productor no tiene que esperar una respuesta del consumidor para continuar para procesar y enviar más mensajes. Colas ofrecen **primero en ENTRAR, primera salida (FIFO)** de entrega de mensajes a uno o varios de los consumidores competencia. Es decir, mensajes normalmente se recibió y procesados por los receptores en el orden en que se agregaron a la cola y cada mensaje se ha recibido y procesado por los consumidores de un solo mensaje.

![QueueConcepts](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

Colas de Bus de servicio son una tecnología de propósito general que puede usarse para una amplia variedad de escenarios:

- Comunicación entre los roles de web y trabajador en una aplicación de Azure de varios niveles.
- Comunicación entre aplicaciones locales y Azure había hospeda aplicaciones en una solución híbrida.
- Comunicación entre los componentes de una aplicación distribuida que se ejecuta en local en diferentes organizaciones o departamentos de una organización.

Uso de colas permite escalar las aplicaciones con más facilidad y habilitar la resistencia de la arquitectura de.

## <a name="create-a-service-namespace"></a>Crear un espacio de nombres de servicio

Para empezar a usar colas de Bus de servicio en Azure, primero debe crear un espacio de nombres. Un espacio de nombres proporciona un contenedor de ámbito para el direccionamiento de recursos de Bus de servicio de la aplicación.

Para crear un espacio de nombres:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar la aplicación para usar Bus de servicio

Asegúrese de que ha instalado el [SDK de Azure para Java][] antes de generar este ejemplo. Si está utilizando Eclipse, puede instalar el [Kit de herramientas de Azure para Eclipse][] que incluye el SDK de Azure para Java. A continuación, puede agregar las **Bibliotecas de Microsoft Azure de Java** a un proyecto:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Agregue las siguientes `import` instrucciones a la parte superior del archivo Java:

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Crear una cola

A través de la clase **ServiceBusContract** , se pueden realizar operaciones de administración de colas de Bus de servicio. Se crea un objeto de **ServiceBusContract** con una configuración adecuada encapsulado el token de SA con permisos para administrar y la clase **ServiceBusContract** es el único punto de comunicación con Azure.

La clase **ServiceBusService** proporciona métodos para crear, enumerar y eliminar colas. El ejemplo siguiente muestra cómo un objeto **ServiceBusService** puede utilizarse para crear una cola denominada "TestQueue" con un espacio de nombres "HowToSample":

```
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Hay métodos en **QueueInfo** que permite que las propiedades de la cola para ajustarse (por ejemplo: establecer el valor predeterminado de time to live (TTL) que se aplique a los mensajes enviados a la cola). En el ejemplo siguiente se muestra cómo crear una cola denominada `TestQueue` con un tamaño máximo de 5GB:

````
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Tenga en cuenta que puede usar el método **listQueues** en **ServiceBusContract** objetos para comprobar si ya existe una cola con un nombre especificado dentro de un espacio de nombres de servicio.

## <a name="send-messages-to-a-queue"></a>Enviar mensajes a una cola

Para enviar un mensaje a una cola de Bus de servicio, la aplicación obtiene un objeto **ServiceBusContract** . El código siguiente muestra cómo enviar un mensaje el `TestQueue` cola creado previamente en el `HowToSample` espacio de nombres:

```
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Los mensajes envían a y recibidos de Bus de servicio colas son instancias de la clase [BrokeredMessage][] . Objetos de [BrokeredMessage][] tienen un conjunto de propiedades estándar (como [etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) y [período de vida](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un diccionario que se utiliza para contener propiedades específicas de aplicación personalizada y un cuerpo de datos de aplicación arbitrario. Una aplicación puede establecer el cuerpo del mensaje pasando cualquier objeto serializable al constructor de la [BrokeredMessage][]y, a continuación, se utilizará el serializador adecuado para serializar el objeto. Como alternativa, puede proporcionar un java **. IO. InputStream** objeto.

En el ejemplo siguiente se muestra cómo enviar cinco probar mensajes a la `TestQueue` **MessageSender** hemos recopilado en el fragmento de código anterior:

```
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

Colas de Bus de servicio compatible con el tamaño máximo de mensaje de 256 KB en el [nivel estándar](service-bus-premium-messaging.md) y 1 MB en el [nivel de Premium](service-bus-premium-messaging.md). El encabezado, que incluye las propiedades de la aplicación estándar y personalizados, puede tener un tamaño máximo de 64 KB. No hay ningún límite del número de mensajes de una cola, pero hay un límite en el tamaño total de los mensajes que llevan a cabo una cola. Tamaño de la cola se define en la hora de creación, con un límite máximo de 5 GB.

## <a name="receive-messages-from-a-queue"></a>Recibir mensajes de una cola

La forma principal para recibir mensajes de una cola es utilizar un objeto **ServiceBusContract** . Mensajes recibidos pueden trabajar en dos modos: **ReceiveAndDelete** y **PeekLock**.

Al usar el modo de **ReceiveAndDelete** recibir es una operación de captura solo - es decir, cuando Bus de servicio recibe una solicitud de lectura para un mensaje de una cola, marca el mensaje como consumida y devuelve a la aplicación. Modo de **ReceiveAndDelete** (que es el modo predeterminado) es el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar una aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento.
Dado que Bus de servicio habrá marcar el mensaje como consumida, cuando la aplicación se reinicia y empieza a utilizar de nuevo, los mensajes habrá perdido el mensaje que se ha utilizado antes el bloqueo.

En modo de **PeekLock** , recibir se convierte en una operación de dos fases que permite a las aplicaciones de soporte técnico que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe una solicitud, encuentra el mensaje siguiente consumir, bloquea para evitar que otros consumidores recibirlo y, a continuación, se devuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción llamando **Eliminar** en el mensaje recibido. Cuando Bus de servicio ve la llamada **Eliminar** , se marca el mensaje como consumida y quitar de la cola.

En el ejemplo siguiente se muestra cómo mensajes se pueden recibir y procesar utilizando el modo de **PeekLock** (no en el modo de forma predeterminada). El ejemplo siguiente es un bucle infinito y procesa los mensajes que llegan a nuestro "TestQueue":

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
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
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
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

Bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de errores en la aplicación o dificultades para procesar un mensaje. Si una aplicación de receptor no puede procesar el mensaje por algún motivo, puede llamar al método **unlockMessage** en el mensaje recibido (en lugar de en el método **deleteMessage** ). Esto provocará Bus de servicio desbloquear el mensaje en la cola y que esté disponible para recibir nuevamente, por la misma aplicación consumir o por otra aplicación consume.

También es un tiempo de espera asociado a un mensaje bloqueado en la cola y si se produce un error en la aplicación procesar el mensaje antes el tiempo de espera de bloqueo caduca (por ejemplo, si se bloquea la aplicación), se desbloquear el mensaje automáticamente y que esté disponible para recibir nuevo Bus de servicio.

En caso de que la aplicación se bloquea después de procesar el mensaje, pero antes de que se envía la solicitud de **deleteMessage** , a continuación, el mensaje se volverá a entregar a la aplicación cuando se reinicia. A menudo se denomina **Al menos una vez procesamiento**, es decir, cada mensaje se procesará al menos una vez pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, los desarrolladores de aplicaciones deben agregar lógica adicional a su aplicación para controlar la entrega de mensajes duplicados. A menudo, esto se logra utilizando el método **getMessageId** del mensaje, que permanecerá constante entre intentos de entrega.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de colas de Bus de servicio, vea [colas, temas y las suscripciones][] para obtener más información.

Para obtener más información, consulte el [Centro para desarrolladores de Java](/develop/java/).


  [Azure SDK de Java]: http://azure.microsoft.com/develop/java/
  [Kit de herramientas de Azure para Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

