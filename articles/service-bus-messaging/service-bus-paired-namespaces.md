<properties 
    pageTitle="Bus de servicio emparejadas espacios | Microsoft Azure"
    description="Detalles de la implementación de espacio de nombres pareja y costo"
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

# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Observaciones pareadas detalles de la implementación de espacio de nombres e implicaciones de costo

El método [PairNamespaceAsync][] , usando una instancia de [SendAvailabilityPairedNamespaceOptions][] , realiza tareas visibles en su nombre. Porque hay coste consideraciones al usar la característica, resulta útil entender esas tareas para que esperar el comportamiento cuando sucede. La API activa el siguiente comportamiento automático en su nombre:

-   Creación de colas de trabajo pendiente.
-   Creación de un objeto [MessageSender][] que se comunica con colas o temas.
-   Cuando una entidad mensajería no está disponible, envía mensajes a la entidad de ping en un intento de detectar cuando vuelva a estar disponible esa entidad.
-   Opcionalmente, se crea un conjunto de "bombas de mensaje" que mover los mensajes de las colas de trabajo pendiente a las colas principales.
-   Coordenadas cierre/con errores de las instancias de [MessagingFactory][] primaria y secundarias.

En un nivel alto, la característica funciona como sigue: cuando la entidad principal es correcto, se produce ningún cambio de comportamiento. Cuando la duración de [FailoverInterval][] transcurre y la entidad principal ve correcta no envía después una no transitorios [MessagingException][] o [TimeoutException][], ocurre lo siguiente:

1.  Enviar se deshabilitan las operaciones de la entidad principal y el sistema hace ping la entidad principal hasta que el ping pueden ser entregados correctamente.

2.  Se selecciona una cola de registro aleatorio.

3.  Objetos de [BrokeredMessage][] se enrutan a la cola de registro elegido.

1.  Si se produce un error en una operación de envío a la cola de registro elegido, se extrae cola de la rotación y una nueva cola está seleccionada. Obtenga información sobre todos los remitentes en la instancia de [MessagingFactory][] del error.

Las figuras siguientes muestran la secuencia. En primer lugar, el remitente envía mensajes.

![Pareja espacios de nombres][0]

Después de un error al enviar a la cola principal, el remitente comienza a enviar mensajes a una cola de registro aleatorio. Simultáneamente, se inicia una tarea de ping.

![Pareja espacios de nombres][1]

En este momento, los mensajes permanecen en la cola secundaria y no se han entregado a la cola principal. Una vez que la cola principal nuevo es correcto, debe ejecutar al menos un proceso la syphon. La syphon entrega los mensajes de las diversas colas de trabajo pendiente a las entidades de destino NOMPROPIO (colas y temas).

![Pareja espacios de nombres][2]

El resto de este tema trata los detalles sobre cómo funcionan estos elementos específicos.

## <a name="creation-of-backlog-queues"></a>Creación de colas de trabajo pendiente

El objeto [SendAvailabilityPairedNamespaceOptions][] pasado al método [PairNamespaceAsync][] indica el número de colas de registro que desea usar. Cada cola de registro se crea explícitamente con las siguientes propiedades de conjunto (todos los demás valores se establecen los valores predeterminados de [QueueDescription][] ):

| Ruta de acceso                                   | [espacio de nombres primario] / transferencia de servicebus x / [index] donde [index] es un valor en [0, BacklogQueueCount) |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes                     | 5120                                                                                                 |
| MaxDeliveryCount                       | int. MaxValue                                                                                         |
| DefaultMessageTimeToLive               | TimeSpan.MaxValue                                                                                    |
| AutoDeleteOnIdle                       | TimeSpan.MaxValue                                                                                    |
| LockDuration                           | 1 minuto                                                                                             |
| EnableDeadLetteringOnMessageExpiration | Verdadero                                                                                                 |
| EnableBatchedOperations                | Verdadero                                                                                                 |

Por ejemplo, la primera cola de registro creados para el espacio de nombres de **contoso** se denomina `contoso/x-servicebus-transfer/0`.

Al crear las colas, el código comprueba primero si existe una cola de este tipo. Si la cola no existe, se crea la cola. El código no limpiar colas de trabajo pendiente "extra". En concreto, si la aplicación con el espacio de nombres primario **contoso** solicita cinco colas pero una cola de registro con la ruta de acceso de registro `contoso/x-servicebus-transfer/7` existe, esa cola de registro adicional todavía está presente pero no se utiliza. El sistema permite explícitamente colas de trabajo pendiente adicional existen que no se utilizan. Como propietario del espacio de nombres, usted es responsable de limpiar las colas de trabajo pendiente sin usar o no deseados. El motivo de esta decisión es que Bus de servicio no se puede saber qué fines existen para todas las colas en el espacio de nombres. Además, si una cola existe con el nombre especificado, pero no cumple el supuesto [QueueDescription][], a continuación, los motivos son sus propios para cambiar el comportamiento predeterminado. Ninguna garantía se realiza las modificaciones a las colas de trabajo pendiente por el código. Asegúrese de probar sus cambios completamente.

## <a name="custom-messagesender"></a>MessageSender personalizada

Al enviar, todos los mensajes se vaya a través de un objeto [MessageSender][] interno que se comporta normalmente cuando todo funciona y se redirige a las colas de trabajo pendiente cuando cosas "romper". Al recibir un error de no transitorios, se inicia un temporizador. Después de un período de [intervalo de tiempo][] que consta de un valor de la propiedad de [FailoverInterval][] durante el cual se envía ningún mensaje correcto, se activa la migración tras error. En este momento, ocurrirá lo siguiente para cada entidad:

- Una tarea ping ejecuta cada [PingPrimaryInterval][] para comprobar si la entidad está disponible. Una vez que esta tarea se realiza correctamente, todo el código de cliente que utiliza la entidad inmediatamente comienza a enviar nuevos mensajes al espacio de nombres primario.

- Las solicitudes futuras para enviar a esa misma entidad de cualquier otros remitentes dará como resultado la [BrokeredMessage][] enviado a modificarse debe sentarse en la cola de registro. La modificación quita algunas propiedades del objeto [BrokeredMessage][] y almacena en otro lugar. Se desactiva y se agregan en un nuevo alias, permitiendo Bus de servicio y el SDK procesar los mensajes de manera uniforme las siguientes propiedades:

| Nombre de propiedad anterior       | Nuevo nombre de propiedad |
|-------------------------|-------------------|
| Id. de sesión               | ID de ms de x    |
| Período de vida              | período de vida x-ms   |
| ScheduledEnqueueTimeUtc | ruta de ms x         |

La ruta de destino original también se almacena en el mensaje como una propiedad con el nombre de ruta de ms x. Este diseño permite mensajes para muchas entidades coexistencia en una cola de registro único. Las propiedades se convierten devueltos por la syphon.

El objeto [MessageSender][] personalizado puede experimentar problemas para los mensajes aproximan al límite de 256 KB y migración tras error está ocupado. El objeto [MessageSender][] personalizado almacena mensajes para todas las colas y temas juntos en las colas de trabajo pendiente. Este objeto mezcla mensajes desde muchos primarios juntos dentro de las colas de trabajo pendiente. Para controlar el equilibrio de carga entre muchos clientes que no conoce entre sí, el SDK elige aleatoriamente una cola de registro para cada [QueueClient][] o [TopicClient][] se crean en código.

## <a name="pings"></a>Ping

Un mensaje de ping es una vacía [BrokeredMessage][] con su propiedad [ContentType][] en aplicación/vnd.ms-servicebus-ping y un valor de [período de vida][] de 1 segundo. Este ping tiene una característica especial en Bus de servicio: el servidor nunca ofrece un ping cuando cualquier llamador solicita un [BrokeredMessage][]. Por lo tanto, nunca debe aprender a recibir y omitir estos mensajes. Se pueden hacer ping en cada entidad (cola único o tema) por cada instancia de [MessagingFactory][] por cliente cuando se consideran no esté disponible. De forma predeterminada, esto ocurre una vez por minuto. Mensajes de ping se consideran mensajes normales de Bus de servicios y pueden provocar gastos de ancho de banda y los mensajes. Tan pronto como los clientes detectan que está disponible el sistema, detenga los mensajes.

## <a name="the-syphon"></a>La syphon

Debe ejecutar el syphon al menos un programa ejecutable en la aplicación de forma activa. La syphon realiza una larga sondeo recibir que dura 15 minutos. Cuando todas las entidades están disponibles y 10 colas de trabajo pendiente, la aplicación que hospeda el syphon llama a la operación de recepción 40 veces por hora, 960 horas por día y a veces 28800 en 30 días. Cuando activa la syphon mueve mensajes desde el trabajo pendiente a la cola principal, cada mensaje experiencias de los siguientes cargos (cargos estándares para tamaño del mensaje y el ancho de banda se aplican en todas las etapas):

1.  Enviar al trabajo pendiente.

2.  Recibir el trabajo pendiente.

3.  Enviar a la principal.

4.  Recibir el principal.

## <a name="closefault-behavior"></a>Comportamiento de error/cerrar

Dentro de una aplicación que hospeda el syphon, una vez los errores de [MessagingFactory][] primario o secundarios o se cierra sin su partner está también errores de cerrado y la syphon detecta este estado, el actúa syphon. Si no se cierra otros [MessagingFactory][] en 5 segundos, la syphon error la aún abierto [MessagingFactory][].

## <a name="next-steps"></a>Pasos siguientes

Para obtener información detallada sobre la mensajería asincrónica de Bus de servicio, consulte [modelos de mensajería asincrónicos y alta disponibilidad][] . 

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [Intervalo de tiempo]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [Período de vida]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Alta disponibilidad y los modelos de mensajería asincrónicos]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png
