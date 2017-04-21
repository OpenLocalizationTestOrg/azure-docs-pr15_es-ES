<properties 
    pageTitle="Información general sobre los eventos de Azure Hubs | Microsoft Azure"
    description="Introducción y descripción general de Azure evento Hubs."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm" />

# <a name="azure-event-hubs-overview"></a>Introducción a Azure Hubs de evento

Muchas soluciones moderna intención para proporcionar experiencias de cliente adaptación o para mejorar los productos mediante comentarios continua y telemetría automatizada. Estas soluciones se enfrentan al reto de cómo muy grandes cantidades de información desde muchos editores simultáneos de proceso de forma segura y fiable. Evento Hubs de Microsoft Azure es un servicio de plataforma administrada que proporciona una base de admisión de datos a gran escala en una amplia variedad de escenarios. Ejemplos de estas situaciones son comportamiento de seguimiento en aplicaciones móviles, información de tráfico de granjas de servidores web, captura de eventos de juego en juegos de consola, o los datos de telemetría recopilan máquinas industriales o conectado vehículos. La función comunes que se reproduce evento Hubs en arquitecturas de soluciones es que actúe como la "puerta de entrada" para una canalización de evento, a menudo se denomina una *ingestor del evento*. Un ingestor de evento es un componente o servicio que se encuentra entre editores de evento y los consumidores de eventos desacoplar la producción de una secuencia de evento desde el consumo de los eventos.

![Evento Hubs](./media/event-hubs-overview/IC759856.png)

Azure Hubs de evento es un servicio que proporciona el evento y telemetría entrada en la nube a gran escala, baja latencia y alta confiabilidad de procesamiento de evento. Este servicio, que se usa con otros servicios de nivel inferiores, es especialmente útil en instrumentación de aplicaciones, procesamiento de flujo de trabajo o de la experiencia del usuario y escenarios de Internet de cosas (IoT). Evento Hubs proporciona una secuencia de mensaje tratamiento capacidad y aunque un concentrador de evento es una entidad similar a las colas y temas, tiene las características que son muy diferentes de mensajería empresarial tradicional. Escenarios de mensajería de empresa suelen requieran capacidades sofisticadas como secuencias, no enviados letras, compatibilidad con transacciones y garantías de entrega seguro, mientras que el problema principal para admisión de evento es alto rendimiento y flexibilidad para las secuencias de eventos de procesamiento. Por lo tanto, las capacidades de evento Hubs diferencian de los temas de Bus de servicio encarecidamente dependen hacia alto rendimiento y escenarios de procesamiento de eventos. Por lo tanto, Hubs evento implementar algunas de las capacidades de mensajería que están disponibles para temas. Si necesita estas capacidades, temas seguirán la mejor opción.

Se crea un concentrador de eventos en el nivel de espacio de nombres de evento Hubs, similar a temas y colas de Bus de servicio. Evento Hubs usa AMQP y HTTP como sus interfaces API principales. El diagrama siguiente muestra la relación entre Hubs de evento y Bus de servicio.

![Evento Hubs](./media/event-hubs-overview/IC741188.png)

## <a name="conceptual-overview"></a>Información general y conceptual

Evento Hubs proporciona mensaje transmisión a través de un modelo de consumidor divididas. Colas y temas de usan un modelo de [Una competición de consumidor](https://msdn.microsoft.com/library/dn568101.aspx) en el que cada consumidor intenta leer de la misma cola o recursos. Esta competencia para recursos finalmente resultados en complejidad y límites de escala de secuencia de procesamiento de aplicaciones. Evento Hubs usa un patrón de consumidor dividida en la que cada consumidor sólo lee un subconjunto específico, o partición, de la secuencia de mensajes. Este modelo permite escala horizontal procesamiento de eventos y proporciona otras características centrados en secuencia que no están disponibles en colas y temas.

### <a name="partitions"></a>Particiones

Una partición es una secuencia ordenada de eventos que se mantiene en un concentrador de evento. Al recibir eventos más recientes, que se hayan agregado al final de esta secuencia. Una partición puede considerarse como "registro de confirmación".

![Evento Hubs](./media/event-hubs-overview/IC759857.png)

Particiones conservan los datos durante un tiempo de retención configurada se establece en el nivel de concentrador de evento. Esta configuración aplica en todas las particiones en el evento concentrador. Eventos caducan en una base de tiempo; explícitamente no puede eliminarlos. Un concentrador de evento contiene varias particiones. Cada partición es independiente y contiene su propia secuencia de datos. Como resultado, particiones a menudo crecen a tasas diferentes.

![Evento Hubs](./media/event-hubs-overview/IC759858.png)

El número de particiones se especifica en la hora de creación de concentrador de evento y debe estar entre 2 y 32 (el valor predeterminado es 4). Las particiones son un mecanismo de organización de datos y más relacionadas con el grado de paralelismo descendente requerido en otros programas que el rendimiento de Hubs de evento. Esto hace la elección del número de particiones en un concentrador de evento directamente relacionadas con el número de lectores que espera tener. Tras la creación de concentrador de evento, el recuento de partición no es modificable; debe tener en cuenta este número de escala esperado a largo plazo. Puede aumentar el límite de 32 partición póngase en contacto con el equipo de Bus de servicio.

Mientras las particiones identificar y pueden enviarse a directamente, es mejor evitar enviar datos a particiones específicas. En su lugar, puede usar construcciones de nivel superior descritos en las secciones de [publisher de evento](#event-publisher) y [Directiva de Publisher](#capacity-and-security) .

En el contexto de evento Hubs, los mensajes se denominan *datos del evento*. Datos de evento contienen el cuerpo del evento, un usuario definido bolsa de propiedades y varios metadatos sobre el evento como su desplazamiento en la partición y su número de la secuencia de la secuencia. Las particiones se rellenan con una secuencia de datos del evento.

## <a name="event-publisher"></a>Publisher de evento

Cualquier entidad que envía datos o eventos a un concentrador de evento es un *Editor de evento*. Editores de evento pueden publicar eventos utilizando HTTPS o AMQP 1.0. Editores de evento utilizan un símbolo de firma de acceso compartido (SA) para identificarse a un concentrador de evento y puede tener una identidad única o utilice un símbolo de SA comunes, dependiendo de los requisitos del escenario.

Para obtener más información sobre cómo trabajar con las SA, vea [Autenticación de firma de acceso compartida con Bus de servicio](../service-bus-messaging/service-bus-shared-access-signature-authentication.md).

### <a name="common-publisher-tasks"></a>Tareas comunes de publisher

Esta sección describen las tareas comunes de editores de evento.

#### <a name="acquire-a-sas-token"></a>Adquirir un token SA

Firma de acceso compartido (SA) es el mecanismo de autenticación para Hubs de evento. Bus de servicio proporciona directivas de asociaciones de seguridad en el nivel de concentrador de evento y el espacio de nombres. Un símbolo de SA se genera a partir de una clave de SA y es un hash SHA de una dirección URL, codificado en un formato específico. Con el nombre de la clave (directiva) y el token, Bus de servicio puede volver a crear el hash y, por tanto, autenticar al remitente. Normalmente, tokens SA para editores de eventos se crean con sólo **Enviar** privilegios en un concentrador de evento específico. Este mecanismo de SA token dirección URL es la base para la identificación de publisher que introdujo en la directiva de publisher. Para obtener más información sobre cómo trabajar con las SA, vea [Autenticación de firma de acceso compartida con Bus de servicio](../service-bus-messaging/service-bus-shared-access-signature-authentication.md).

#### <a name="publishing-an-event"></a>Publicar un evento

Puede publicar un evento a través de AMQP 1.0 o HTTPS. Bus de servicio proporciona una clase [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) para publicar los eventos a un concentrador de eventos de clientes de .NET. Para otras plataformas y tiempo de ejecución, puede utilizar a cualquier cliente AMQP 1.0, como [Apache Qpid](http://qpid.apache.org/). Puede publicar eventos de forma individual o por lotes. Una sola publicación (instancia de datos de evento) tiene un límite de 256KB, independientemente de si se trata de un evento único o un lote. Eventos mayores de lo que se produce un error de publicación. Es una práctica recomendada para editores constancia de particiones en el Hub de evento y especificar solo una *partición clave* (introducida en la siguiente sección) o su identidad a través de su token SA.

La opción Usar AMQP o HTTPS es específica del escenario de uso. AMQP requiere el establecimiento de un socket bidireccional persistente además de transporte (TLS) seguridad o SSL/TLS. Puede ser una operación costosa en términos de tráfico de red, pero sólo se produce al principio de una sesión AMQP. HTTPS tiene una sobrecarga inicial menor, pero requiere SSL adicional sobrecarga para cada solicitud. Para los editores que publican con frecuencia eventos AMQP ofrece importantes ahorros de rendimiento, latencia y rendimiento.

### <a name="partition-key"></a>Clave de partición

Una clave de partición es un valor que se usa para asignar datos entrantes del evento en particiones específicas a efectos de la organización de datos. La clave de partición es un valor proporcionado por el remitente que se pasa a un concentrador de evento. Se procesa a través de una función de hash estática, el resultado de las cuales crea la asignación de partición. Si no especifica una clave de partición cuando se publica un evento, se usa una asignación de turnos. Cuando utilice las teclas de partición, el Editor de evento sólo tiene constancia de su clave de partición, no la partición a la que se publiquen los eventos. Esta desvinculación de clave y partición aísla el remitente de la necesidad de saber mucho sobre el procesamiento de nivel inferior y el almacenamiento de eventos. Claves de partición son importantes para organizar los datos para el procesamiento de nivel inferior, pero no relacionadas con básicamente particiones a sí mismos. Una por dispositivo o usuario único identidad constituye una clave de partición correcta, pero otros atributos como geografía también pueden utilizarse para agrupar los eventos relacionados en una sola partición. La imagen siguiente muestra el uso de claves de partición para anclar a particiones de remitentes de eventos.

![Evento Hubs](./media/event-hubs-overview/IC759859.png)

Evento Hubs garantiza que todos los eventos que comparten el mismo valor de clave de partición se entregan en orden y a la misma partición. Importante, si se utilizan claves de partición con las directivas de publisher, que se describe en la siguiente sección, a continuación, la identidad de la compañía y el valor de la clave de partición deben coincidir. En caso contrario, se produce un error.

### <a name="event-consumer"></a>Consumidor de eventos

Cualquier entidad que lee los datos de evento de un concentrador de evento es un consumidor de eventos. Todos los consumidores de eventos leer la secuencia de eventos a través de particiones en un grupo de consumidores. Cada partición debería tener solo un lector activo en un momento. Todos los consumidores de evento Hubs conectan a través de la sesión de AMQP 1.0, en el que se entregarán eventos cuando estén disponibles. El cliente no es necesario sondear la disponibilidad de los datos.

#### <a name="consumer-groups"></a>Grupos de consumidores

Se habilita el mecanismo de publicación o suscripción de evento Hubs a través de los grupos de consumidores. Un grupo de consumidor es una vista (estado, posición o desplazamiento) de un concentrador de evento todo. Habilitar grupos de consumidores varias aplicaciones mucho a cada una tienen una vista independiente de la secuencia de eventos y leer la secuencia de manera independiente a su ritmo y con sus propios desplazamientos. En una secuencia de arquitectura de proceso, cada aplicación equivale a un grupo de consumidores. Si desea escribir los datos del evento en almacenamiento a largo plazo, esa aplicación escritor de almacenamiento es un grupo de consumidores. Procesamiento de evento complejas se realiza por grupo de consumidores de otro, independiente. Solo puede tener acceso a las particiones a través de un grupo de consumidores. Siempre hay un grupo de consumidor de forma predeterminada en un concentrador de evento y puede crear grupos de consumidores hasta 20 para un evento concentrador de nivel estándar.

Los siguientes son ejemplos de la convención URI del grupo de consumidor:

    //<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
    //<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>

La imagen siguiente muestra los consumidores de eventos dentro de los grupos de consumidores.

![Evento Hubs](./media/event-hubs-overview/IC759860.png)

#### <a name="stream-offsets"></a>Desplazamientos de secuencia

Un desplazamiento es la posición de un evento dentro de una partición. Puede considerar que un desplazamiento como un cursor de cliente. El desplazamiento es de un byte numeración del evento. Esto permite un consumidor de eventos (lector) especificar un punto de secuencia de eventos de la que desean empezar a leer los eventos. Puede especificar el desplazamiento como una marca de tiempo o como un valor de desplazamiento. Los consumidores son responsables de almacenar sus propios valores de desplazamiento fuera del servicio Hubs de evento.

![Evento Hubs](./media/event-hubs-overview/IC759861.png)

Dentro de una partición, cada evento incluye un desplazamiento. Este desplazamiento se usa los consumidores para mostrar la ubicación en la secuencia de eventos para una partición determinada. Desplazamientos pueden pasar al evento concentrador como un número o como un valor de marca de tiempo cuando se conecta un lector.

#### <a name="checkpointing"></a>Puntos de control

*Puntos de control* es un proceso que los lectores marcar o confirmación su posición dentro de una secuencia de eventos de partición. Puntos de control es responsabilidad del consumidor y se produce en una función de las particiones dentro de un grupo de consumidores. Esto significa que para cada grupo de consumidores, cada lector partición debe realizar un seguimiento de su posición actual en el evento transmita y puede informarle del servicio cuando considere el flujo de datos completa. Si se desconecta un lector de una partición cuando se vuelve a conectar comienza a leer en el punto de comprobación previamente enviada por el lector de último de dicha partición de ese grupo de consumidores. Cuando se conecta al lector, pasa este desplazamiento al evento concentrador para especificar la ubicación en la que se va a empezar a leer. De esta forma, puede usar puntos de control a ambos eventos, marcar como "completado" con las aplicaciones de nivel inferiores y para proporcionar resistencia en caso de un error entre lectores que se ejecutan en diferentes equipos. Dado que se conservan los datos de evento para el intervalo de retención especificado en el momento en que se crea el Hub de evento es posible volver a los datos más antiguos especificando un desplazamiento inferior de este proceso de puntos de control. A través de este mecanismo, puntos de control permite la resistencia de migración tras error y reproducción de secuencia de evento controlado.

#### <a name="common-consumer-tasks"></a>Tareas comunes de consumidor

Esta sección describen las tareas comunes para los consumidores de eventos del evento Hubs o lectores. Todos los consumidores de evento Hubs conectan vía AMQP 1.0. AMQP 1.0 es una sesión y el canal de comunicación bidireccional tener en cuenta el estado. Cada partición tiene una sesión de vínculo de AMQP 1.0 que facilita el transporte de eventos aísla por partición.

##### <a name="connect-to-a-partition"></a>Conectarse a una partición

Para consumir eventos de un concentrador de evento, consumidor debe conectarse a una partición. Como se mencionó anteriormente, que siempre tener acceso a las particiones a través de un grupo de consumidores. Como parte del modelo de consumidor divididas, solo un único lector debe estar activo en una partición en cualquier momento dentro de un grupo de consumidores. Es muy común cuando se conectan directamente a las particiones para usar un mecanismo financiero para coordinar conexiones de lector a particiones específicas. De este modo, es posible que todas las particiones en un grupo de consumidor para que solo un lector activo. Administrar la posición en la secuencia de un lector es una tarea importante que se logra a través de puntos de control. Esta funcionalidad se ha simplificado mediante el uso de la clase [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) para clientes de .NET. [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) es un agente inteligente consumidor y se describe en la sección siguiente.

##### <a name="read-events"></a>Eventos de lectura

Después de abrir una sesión de AMQP 1.0 y un vínculo para una partición específica, eventos se entregarán en el cliente AMQP 1.0 por el servicio de evento Hubs. Este mecanismo de entrega permite un mayor rendimiento y menor latencia que mecanismos basados en la extracción como HTTP GET. Como los eventos se envían al cliente, cada instancia de datos de evento contiene metadatos importantes como el número de desplazamiento y la secuencia que se usan para facilitar a los puntos de control en la secuencia de eventos.

![Evento Hubs](./media/event-hubs-overview/IC759862.png)

Es su responsabilidad para administrar este desplazamiento de manera que mejor permite administrar progreso en la secuencia de procesamiento.

## <a name="capacity-and-security"></a>Seguridad y capacidad

Evento Hubs es una arquitectura de paralelo altamente scalable para entrada de la secuencia. Por lo tanto, existen varios aspectos clave a tener en cuenta al tamaño y escala una solución basada en Hubs de evento. El primero de estos controles de capacidad se denomina *unidades de rendimiento*, que se describe en la sección siguiente.

### <a name="throughput-units"></a>Unidades de rendimiento

Unidades de rendimiento controla la capacidad de rendimiento de Hubs de evento. Unidades de rendimiento previamente se adquieren unidades de capacidad. Una unidad de rendimiento solo incluye lo siguiente:

- Entrada: Hasta 1 MB por segundo o 1000 eventos por segundo.

- Salida: Hasta 2 MB por segundo.

Entrada está limitado a la cantidad de capacidad proporcionada por el número de unidades de rendimiento comprado. Enviar los datos por encima de los resultados de la cantidad en una excepción "Cuota superada". Esta cantidad es uno 1 MB por segundo o 1000 eventos por segundo, lo que suceda primero. Salida no genera excepciones limitación, pero se limita a la cantidad de transferencia de datos prevista por las unidades de rendimiento comprados: 2 MB por segundo por unidad de rendimiento. Si recibe excepciones de tasa de publicación o que espera ver más salida Compruebe cuántas unidades de rendimiento que ha comprado para el espacio de nombres en el que se creó el Hub de evento. Para obtener más unidades de rendimiento, puede ajustar la configuración de la página de **espacios de nombres** en la pestaña **escala** en el [portal de clásico de Azure][]. También puede cambiar esta configuración mediante las API de Azure.

Aunque las particiones son un concepto de organización de datos, unidades de rendimiento son puramente un concepto de capacidad. Unidades de rendimiento se cargarán por hora y se previamente comprados. Una vez compradas, se cargarán unidades de rendimiento durante un mínimo de una hora. Hasta 20 rendimiento se pueden adquirir unidades para un espacio de nombres de evento Hubs y hay un límite de cuenta de Azure de 20 unidades de rendimiento. Estas unidades de rendimiento se comparten en todos los Hubs de evento en un espacio de nombres.

Unidades de rendimiento aprovisionados en mejor y no siempre estén disponibles para su compra inmediata. Si necesita una capacidad específica, se recomienda que compre las unidades de rendimiento antes de tiempo. Si necesita más de 20 unidades de rendimiento, póngase en contacto con soporte técnico de Azure para comprar más unidades de rendimiento en una base de compromiso en bloques de 20 hasta la primera unidades de 100 rendimiento. Además, también puede adquirir bloques de 100 unidades de rendimiento.

Se recomienda equilibrar cuidadosamente unidades de rendimiento y particiones para lograr óptimo escala con Hubs de evento. Una sola partición tiene una escala máxima de unidad de un rendimiento. El número de unidades de rendimiento debe ser menor o igual que el número de particiones en un concentrador de evento.

Para más información sobre los precios, consulte [Evento Hubs precios](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="publisher-policy"></a>Directiva de Publisher

Evento Hubs permite control detallado de editores de eventos a través de *directivas de publisher*. Directivas de Publisher son un conjunto de características de tiempo de ejecución diseñado para facilitar grandes cantidades de editores de eventos independientes. Con las directivas de publisher, cada publisher utiliza su propio identificador único al publicar eventos a un concentrador de evento, utilizando el mecanismo siguiente:

    //<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>

No tiene que crear nombres de publisher adelantado, pero deben coincidir con el token de SA que se utiliza cuando se publica un evento para garantizar las identidades de publisher independientes. Para obtener más información sobre las asociaciones de seguridad, vea [Autenticación de firma de acceso compartida con Bus de servicio](../service-bus-messaging/service-bus-shared-access-signature-authentication.md). Al utilizar las directivas de publisher, el valor de **PartitionKey** se establece en el nombre del Editor. Para que funcione correctamente, estos valores deben coincidir.

## <a name="summary"></a>Resumen

Azure Hubs de evento proporciona un evento de gran escala y servicio que se puede usar para el usuario y la aplicación de flujo de trabajo común supervisión en cualquier escala de procesamiento de telemetría. Con la capacidad para proporcionar publicación suscripción capacidades con baja latencia y a gran escala, Hubs evento servir como "rampa en" datos grande. Con la identidad basada en publisher y listas de revocación, estas funciones se extienden en escenarios comunes de Internet de las cosas. Para obtener más información sobre cómo desarrollar aplicaciones Hubs de evento, consulte la [Guía de programación de eventos Hubs](event-hubs-programming-guide.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido acerca de los conceptos de evento Hubs, puede mover a las situaciones siguientes:

- Introducción a un [evento Hubs tutorial].
- Una [aplicación de ejemplo que usa el evento Hubs]completa.

[Portal de clásico de Azure]: http://manage.windowsazure.com
[Tutorial de Hubs de evento]: event-hubs-csharp-ephcs-getstarted.md
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
