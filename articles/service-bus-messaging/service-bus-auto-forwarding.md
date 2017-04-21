<properties 
    pageTitle="Reenvío automático Bus de servicios de mensajería entidades | Microsoft Azure"
    description="Cómo encadenar una suscripción a otra cola o tema o cola."
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
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Encadenar entidades de Bus de servicio con reenvío automático

La característica de *reenvío automático* permite encadenar una cola o una suscripción a otra cola o tema que forma parte del mismo espacio de nombres. Cuando se habilita el reenvío automático, Bus de servicio quita los mensajes que se colocan en la primera cola o suscripción (origen) automáticamente y se coloca en la segunda cola o tema (destino). Tenga en cuenta que es posible enviar un mensaje a la entidad de destino directamente. Además, no es posible encadenar una subcola, como una cola con problemas de entrega, a otra cola o tema.

## <a name="using-auto-forwarding"></a>Utilizar el reenvío automático

Puede habilitar el reenvío automático al establecer las propiedades de [SubscriptionDescription.ForwardTo][] o [QueueDescription.ForwardTo][] en los objetos [QueueDescription][] o [SubscriptionDescription][] para el origen como en el ejemplo siguiente.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

La entidad de destino debe existir en el momento de que crea la entidad de origen. Si no existe la entidad de destino, Bus de servicio devuelve una excepción cuando se le solicite para crear la entidad de origen.

Puede utilizar el reenvío automático escalar un tema individual. Bus de servicio limita el [número de suscripciones de un tema determinado](service-bus-quotas.md) de 2.000. Puede alojar suscripciones adicionales mediante la creación de temas de segundo nivel. Tenga en cuenta que incluso si no están limitados por la limitación de Bus de servicio del número de suscripciones, agregar un segundo nivel de temas puede mejorar el rendimiento general del tema.

![Escenario de reenvío automático][0]

También puede utilizar el reenvío automático para desacoplar los remitentes de los receptores. Por ejemplo, considere la posibilidad de sistemas ERP que consta de tres módulos: procesamiento de administración de inventario y administración de relaciones de cliente. Cada uno de estos módulos genera mensajes que están en cola en un tema correspondiente. Ana y Bob es representantes de ventas que están interesados en todos los mensajes que se relacionan con sus clientes. Para recibir los mensajes, Ana y Bob crean una cola de personal y una suscripción en cada uno de los temas ERP que reenvían automáticamente todos los mensajes a su cola.

![Escenario de reenvío automático][1]

Si Ana va de vacaciones, su cola personal, en lugar del tema ERP, rellena hacia arriba. En este escenario, porque un representante de ventas no ha recibido los mensajes, ninguno de los temas ERP nunca alcance cuota.

## <a name="auto-forwarding-considerations"></a>Consideraciones de reenvío automático

Si la entidad de destino acumulado muchos mensajes y supere la cuota o se desactiva la entidad de destino, la entidad de origen agrega los mensajes a su [cola](service-bus-dead-letter-queues.md) hasta que hay espacio en el destino (o se vuelve a activar la entidad). Los mensajes seguirá live en la cola, lo que debe explícitamente recibir procesan desde la cola.

Cuando encadenar temas individuales para obtener un tema compuesto con muchas de las suscripciones, se recomienda que tiene un número moderado de las suscripciones en el tema de primer nivel y muchos de los temas de segundo nivel. Por ejemplo, un tema de primer nivel con 20 suscripciones, cada uno de ellos encadenada a un tema de segundo nivel con suscripciones a 200, permite un mayor rendimiento de un tema de primer nivel con suscripciones a 200, cada encadenada a un tema de segundo nivel con suscripciones a 20.

Bus de servicio factura una operación de cada mensaje reenviado. Por ejemplo, enviar un mensaje a un tema con 20 suscripciones, cada uno de ellos configurado para reenviar automáticamente mensajes a otra cola o tema, se factura como 21 operaciones si todas las suscripciones de primer nivel reciben una copia del mensaje.

Para crear una suscripción que está encadenada a otra cola o tema, el creador de la suscripción debe tener permisos de **administración** en el origen y la entidad de destino. Enviar mensajes al tema de origen, solo requiere permisos de **envío** en el tema de origen.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información detallada sobre el reenvío automático, vea los temas de referencia siguientes:

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Para obtener más información acerca de las mejoras de rendimiento de Bus de servicio, vea [con particiones mensajería entidades][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Entidades de mensajería divididas]: service-bus-partitioning.md