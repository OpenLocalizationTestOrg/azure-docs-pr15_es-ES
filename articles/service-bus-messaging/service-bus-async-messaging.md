<properties 
    pageTitle="Bus de servicios de mensajería asincrónica | Microsoft Azure"
    description="Descripción de mensajería asincrónica de Bus de servicio."
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

# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Alta disponibilidad y los modelos de mensajería asincrónicos

Puede implementar la mensajería asincrónica de varias formas diferentes. Con colas, temas y suscripciones, Bus de servicio de Azure admite comportamiento asincrónico a través de una tienda y mecanismo de reenvío. En el funcionamiento normal (sincrónico), enviar mensajes a colas y temas y recibir mensajes de colas y suscripciones. Escribir las aplicaciones dependen de estas entidades siempre que se encuentre disponible. Cuando cambia el estado de la entidad, debido a una amplia variedad de circunstancias, necesita una manera de proporcionar una entidad de funcionalidad reducida que puede satisfacer la mayoría de las necesidades.

Aplicaciones suele utilizan patrones de mensajería asincrónicas para habilitar a un número de escenarios de comunicación. Puede crear aplicaciones en el que los clientes pueden enviar mensajes a los servicios, incluso cuando no se está ejecutando el servicio. Para las aplicaciones de esa experiencia ráfagas de comunicaciones de una cola pueden ayudar a redistribuir la carga proporcionando un lugar comunicaciones de búfer. Por último, puede obtener un equilibrador de carga sencilla pero efectiva para distribuir mensajes en varios equipos.

Para mantener la disponibilidad de cualquiera de estas entidades, considere la posibilidad de un número de diferentes maneras en que pueden aparecer disponibles para un sistema de mensajería resistente estas entidades. Por lo general, vemos la entidad no estarán disponibles para las aplicaciones, escribir en las siguientes maneras:

- No se puede enviar mensajes.

- No se puede recibir mensajes.

- No se puede administrar entidades (crear, recuperar, actualizar o eliminar entidades).

- No se puede ponerse en contacto con el servicio.

Para cada uno de estos errores, los modos de error diferente hay que habilitar una aplicación continuar realizar el trabajo en algún nivel de funcionalidad reducida. Por ejemplo, un sistema que puede enviar mensajes pero no recibirlos aún puede recibir pedidos de clientes, pero no puede procesar los pedidos. Este tema describen los posibles problemas que pueden ocurrir y cómo se han eliminado dichos problemas. Bus de servicio ha introducido a un número de soluciones que deben participar en y este tema describe las reglas que rigen el uso de las soluciones de alta.

## <a name="reliability-in-service-bus"></a>Confiabilidad de Bus de servicio

Hay varias formas de administrar los problemas de mensaje y de entidad y hay directrices que rigen el uso adecuado de las soluciones. Para conocer las instrucciones, primero debe conocer qué puede fallar en Bus de servicio. Por el diseño de sistemas de Azure, todos estos problemas suelen ser de corta duración. En un nivel alto, las causas diferentes de disponibilidad aparecerán como sigue:

-   Limitación de un sistema externo que depende Bus de servicio. Limitación se produce de la interacción con los recursos de almacenamiento y el cálculo.

-   Problema para un sistema que depende Bus de servicio. Por ejemplo, un elemento determinado de almacenamiento puede encontrar problemas.

-   Error de Bus de servicio en el subsistema único. En esta situación, un nodo de cálculo puede entrar en un estado incoherente y debe reiniciar, la causa de todas las entidades sirve para cargar saldo a otros nodos. A su vez, esto puede provocar un breve período de procesamiento de mensajes lento.

-   Error de Bus de servicio dentro de un centro de datos de Azure. Se trata de un "Error grave" durante el que el sistema no es accesible para estos minutos o unas horas.

> [AZURE.NOTE] El **almacenamiento** de términos puede significar el almacenamiento de Azure y SQL Azure.

Bus de servicio contiene un número de soluciones para estos problemas. Las secciones siguientes describen cada problema y sus respectivos reducciones.

### <a name="throttling"></a>Límite

Con Bus de servicio, limitación permite la administración de la tasa de mensaje cooperación. Cada nodo de Bus de servicio individual aloja muchas entidades. Cada una de esas entidades realiza solicitudes en el sistema en términos de CPU, memoria, almacenamiento y otros aspectos. Cuando cualquiera de estas facetas detecta el uso que supera los umbrales definidos, Bus de servicio puede denegar una solicitud determinada. La persona que llama recibe una [ServerBusyException][] y reintentos después de 10 segundos.

Como mitigación, el código debe leer el error y detener los reintentos del mensaje durante al menos 10 segundos. Dado que el error puede ocurrir en todas partes de la aplicación de cliente, se espera que cada fragmento independiente ejecuta la lógica de reintento. El código puede reducir la probabilidad de que se está limitando habilitando partición de una cola o un tema.

### <a name="issue-for-an-azure-dependency"></a>Problema para una dependencia de Azure

Otros componentes de Azure en ocasiones pueden tener problemas de servicio. Por ejemplo, cuando se actualiza un sistema que utiliza Bus de servicio, ese sistema temporalmente puede experimentar capacidades reducidas. Para evitar estos tipos de problemas, Bus de servicio con regularidad investiga e implementa su eliminación. Aparecen los efectos secundarios de estas soluciones. Por ejemplo, para tratar problemas temporales con el almacenamiento, Bus de servicio implementa un sistema que permite operaciones de envío de mensajes trabajar de forma uniforme. Debido a la naturaleza de la mitigación, un mensaje enviado puede tardar hasta 15 minutos que aparezca en la suscripción o cola afectado y esté listo para una operación de recepción. Por lo general, la mayoría de las entidades no experimentará este problema. Sin embargo, dado el número de entidades en Bus de servicio dentro de Azure, esta mitigación es a veces es necesario para un pequeño subconjunto de los clientes de Bus de servicio.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Error de Bus de servicio en un único subsistema

Con cualquier aplicación, circunstancias pueden provocar un componente interno del Bus de servicio incoherentes. Cuando Bus de servicio detecta esto, recopila datos de la aplicación para ayudar a diagnosticar ¿qué ha ocurrido. Una vez que se recopilan los datos, se reinicia la aplicación en un intento de volver a un estado coherente. Este proceso se produce con rapidez y resultados de una entidad que aparecen para que esté disponible para unos minutos, aunque típico tiempos de inactividad son mucho más cortos.

En estos casos, la aplicación cliente genera una excepción [System.TimeoutException][] o [MessagingException][] . Bus de servicio contiene una mitigación para este problema en el formulario de lógica de reintento automática del cliente. Una vez que se ha agotado el período de reintento y no se ha entregado el mensaje, puede explorar con otras características como [pareja espacios de nombres][]. Espacios de nombres parejas tienen otros aspectos que se describen en este artículo.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Error de Bus de servicio dentro de un centro de datos de Azure

Los motivos más probables para un error en un centro de datos de Azure es un error de actualización implementación de Bus de servicio o un sistema de dependiente. Como la plataforma ha madurado y se ha reducido la probabilidad de que este tipo de error. También puede ocurrir un error de centro de datos por razones que incluyen las siguientes:

-   Interrupción de electricidad (alimentación y generar power desaparecen).
-   Conectividad (salto de internet entre los clientes y Azure).

En ambos casos, desastres naturales o artificiales causaron el problema. Para solucionar este problema y asegúrese de que aún se pueden enviar mensajes, puede usar [pareja espacios de nombres][] para habilitar los mensajes se envíen a otra ubicación mientras la ubicación principal se realiza correcta de nuevo. Para obtener más información, consulte [prácticas recomendadas para aplicaciones aislantes contra interrupciones de Bus de servicio y problemas][].

## <a name="paired-namespaces"></a>Pareja espacios de nombres

La característica de [espacios de nombres pareja][] admite escenarios en los que una entidad de Bus de servicio o implementación dentro de un centro de datos no está disponible. Mientras este evento se produce con poca frecuencia, sistemas distribuidos todavía deben estar preparados para controlar situaciones. Normalmente, este evento ocurre porque algún elemento que depende Bus de servicio está experimentando un problema a corto plazo. Para mantener la disponibilidad de las aplicaciones durante una interrupción, los usuarios de Bus de servicio pueden usar dos espacios de nombres independientes, preferentemente en centros de datos independiente para hospedar sus entidades de mensajería. El resto de esta sección utiliza la siguiente terminología:

-   Espacio de nombres primario: el espacio de nombres con el que la aplicación interactúa, para enviar y recibir operaciones.

-   Espacio de nombres secundario: el espacio de nombres que actúa como una copia de seguridad para el espacio de nombres primario. Lógica de la aplicación no interactúa con este espacio de nombres.

-   Intervalo de migración tras error: la cantidad de tiempo para aceptar normales errores antes de la aplicación cambia de espacio de nombres primario para el espacio de nombres secundario.

Un par de soporte técnico de espacios de nombres *Enviar disponibilidad*. Enviar disponibilidad conserva la capacidad para enviar mensajes. Para usar la disponibilidad de envío, la aplicación debe cumplir los requisitos siguientes:

1.  Solo se reciben los mensajes de espacio de nombres primario.

2.  Enviar mensajes a una determinada cola o tema llegar desordenados.

3.  Si su aplicación utiliza sesiones, mensajes dentro de una sesión pueden llegar desordenados. Se trata de un salto de la funcionalidad normal de sesiones. Esto significa que la aplicación utiliza sesiones mensajes de grupo de forma lógica. Estado de la sesión sólo se mantiene en el espacio de nombres primario.

4.  Mensajes dentro de una sesión pueden llegar desordenados. Se trata de un salto de la funcionalidad normal de sesiones. Esto significa que la aplicación utiliza sesiones mensajes de grupo de forma lógica.

5.  Estado de la sesión sólo se mantiene en el espacio de nombres primario.

6.  La cola principal puede conectarse e iniciar aceptando mensajes antes de que la cola secundaria entrega todos los mensajes en la cola principal.

Las secciones siguientes describen las API, ¿cómo se implementan las API y muestran el código de ejemplo usan la característica. Tenga en cuenta que hay implicaciones de facturación asociados con esta característica.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>La API MessagingFactory.PairNamespaceAsync

La característica de espacios de nombres pareja incluye el método [PairNamespaceAsync][] en la clase [Microsoft.ServiceBus.Messaging.MessagingFactory][] :

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Cuando complete la tarea, el espacio de nombres emparejamiento también es completo y listo para actuar en respuesta cualquier [MessageReceiver][], [QueueClient][], o [TopicClient][] creadas con la instancia de [MessagingFactory][] . [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] es la clase base para los diferentes tipos de par que están disponibles con un objeto [MessagingFactory][] . Actualmente, la única clase derivada es una con nombre [SendAvailabilityPairedNamespaceOptions][], que implementa los requisitos de disponibilidad de envío. [SendAvailabilityPairedNamespaceOptions][] tiene un conjunto de constructores que se basan en cada uno de los otros. Mirar el constructor con la mayoría de los parámetros, podrá comprender el comportamiento de los otros constructores.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Estos parámetros tienen los siguientes significados:

-   *secondaryNamespaceManager*: una instancia de [NamespaceManager][] inicializada para el espacio de nombres secundario que puede usar el método [PairNamespaceAsync][] para configurar el espacio de nombres secundario. El Administrador de espacio de nombres se usa para obtener la lista de colas en el espacio de nombres y para asegurarse de que existen las colas de trabajo pendiente necesarios. Si no existen las colas, se crean. [NamespaceManager][] requiere la capacidad para crear un token con la notificación de **Administrar** .

-   *messagingFactory*: la instancia de [MessagingFactory][] para el espacio de nombres secundario. El objeto [MessagingFactory][] se usa para enviar y, si la propiedad [EnableSyphon][] está establecida en **true**, recibir mensajes de las colas de trabajo pendiente.

-   *backlogQueueCount*: el número de colas de trabajo pendiente para crear. Este valor debe ser al menos 1. Cuando envíe mensajes al trabajo pendiente, se elige aleatoriamente una de estas colas. Si establece el valor en 1, sólo una cola nunca puede usarse. Cuando esto ocurre y genera errores en la cola de un registro, el cliente no pueda probar una cola de registro diferentes y puede producir un error al enviar el mensaje. Se recomienda establecer este valor a algunos mayor valor y de forma predeterminada el valor 10. Puede cambiar esta opción en un valor mayor o menor dependiendo de cuántos datos de la aplicación que se envía al día. Cada cola de registro puede contener un máximo de 5 GB de mensajes.

-   *failoverInterval*: la cantidad de tiempo durante el cual aceptará errores en el espacio de nombres primario antes de cambiar cualquier entidad el espacio de nombres secundario. Migraciones tras error se produce en una base de la entidad por entidad. Entidades en un único espacio de nombres con frecuencia live en distintos nodos dentro de Bus de servicio. Un error en una entidad no implica un error en otra. Puede establecer este valor en [System.TimeSpan.Zero][] para conmutar por error a la secundaria inmediatamente después de su error en primer lugar, no transitorios. Errores de desencadenan el temporizador de la migración tras error son cualquier [MessagingException][] en el que la propiedad [IsTransient][] es falso, o bien un [System.TimeoutException][]. Otras excepciones, como [UnauthorizedAccessException][] hacen que la migración tras error, porque indican que el cliente está configurado correctamente. No provocar un [ServerBusyException][] migración tras error porque el modelo correcto es esperar 10 segundos, a continuación, envíe el mensaje nuevo.

-   *enableSyphon*: indica que este emparejamiento determinado también debe syphon mensajes desde el espacio de nombres secundario al espacio de nombres primario. En general, aplicaciones que envían mensajes deben establecer este valor en **false**; aplicaciones que reciban mensajes deben establecer este valor en **true**. El motivo es que a menudo, hay menos receptores de mensajes de remitentes de mensajes. Dependiendo del número de receptores, puede tener una instancia de aplicación única controlar los derechos de syphon. Uso de los receptores de muchas tiene implicaciones de facturación para cada cola de registro.

Para usar el código, cree una instancia de [MessagingFactory][] principal, una instancia de [MessagingFactory][] secundaria, una instancia de [NamespaceManager][] secundaria y una instancia de [SendAvailabilityPairedNamespaceOptions][] . La llamada puede ser tan sencilla como lo siguiente:

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Cuando complete la tarea devuelta por el método [PairNamespaceAsync][] , todo está configurado y listo para usar. Antes de que la tarea se devuelve, puede no completó todo el trabajo de fondo necesario para el proceso de emparejamiento que funcionan correctamente. Por tanto, no debe comenzar a enviar mensajes hasta que la tarea se devuelve. Si los errores, como credenciales incorrectas o error al crear colas de trabajo pendiente, esas excepciones se inicia una vez completada la tarea. Una vez que la tarea se devuelve, compruebe que se ha encontrado las colas o que se crearon examinando la propiedad [BacklogQueueCount][] en la instancia de [SendAvailabilityPairedNamespaceOptions][] . Para el código anterior, la operación aparece como sigue:

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de la mensajería asincrónica en Bus de servicio, lea más detalles acerca de [los espacios de nombres pareja][].

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Prácticas recomendadas para aislar aplicaciones contra interrupciones de Bus de servicio y problemas]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]:https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [pareja espacios de nombres]: service-bus-paired-namespaces.md