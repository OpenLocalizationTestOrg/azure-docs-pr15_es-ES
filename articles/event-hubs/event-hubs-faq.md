<properties 
    pageTitle="Evento Hubs preguntas más frecuentes (P+F) | Microsoft Azure"
    description="Evento Hubs preguntas más frecuentes."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/01/2016"
    ms.author="sethm" />

# <a name="event-hubs-faq"></a>Evento Hubs preguntas más frecuentes

Evento Hubs proporciona admisión a gran escala, persistencia y procesamiento de eventos de datos de orígenes de datos de alto rendimiento o millones de dispositivos. Cuando sincronizado con temas y colas de Bus de servicio, Hubs evento permite implementaciones de comando y control persistentes para escenarios de [Internet de cosas (IoT)](https://azure.microsoft.com/services/iot-hub/) .

En este artículo se describe la información de precios y se da respuesta a algunas preguntas más frecuentes sobre Hubs de evento:

## <a name="pricing-information"></a>Información de precios

Para obtener información completa sobre precios Hubs de evento, consulte los [Detalles de precios Hubs de evento](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="how-are-event-hubs-ingress-events-calculated"></a>¿Cómo se calculan los eventos de entrada de evento Hubs?

Cada evento enviado a un concentrador de evento cuenta como mensaje facturable. Un *evento de entrada* se define como una unidad de datos que es menor o igual a 64 KB. Cualquier evento que es menor o igual a 64 KB de tamaño se considera un evento facturable. Si el evento es mayor que 64 KB, el número de eventos facturables se calcula según el tamaño del evento, en múltiplos de 64 KB. Por ejemplo, un evento de 8 KB enviado al evento concentrador se factura como un evento, pero se factura 96 KB enviado un mensaje al concentrador de eventos como dos eventos.

Eventos consumidos desde un concentrador de evento, como así como las operaciones de administración y controlan las llamadas como puntos, no se cuentan como eventos de entrada facturable, pero acumulan hasta la permitida de unidad de rendimiento.

## <a name="what-are-event-hubs-throughput-units"></a>¿Qué unidades de rendimiento Hubs de eventos?

Seleccionar explícitamente unidades de rendimiento de evento Hubs, a través del portal de Azure o plantillas de administrador de recursos de evento Hubs. Unidades de rendimiento que se aplican a todos los Hubs de evento en un espacio de nombres de evento Hubs, y cada unidad de rendimiento permita el espacio de nombres de las siguientes capacidades:

- Arriba a 1 MB por segundo de eventos de entrada (eventos enviados a un concentrador de eventos), pero no más de 1000 eventos de entrada, las operaciones de administración o control API llamadas por segundo.

- Hasta 2 MB por segundo de eventos de salida (eventos consumidos desde un concentrador de evento).

- Hasta 84 GB de almacenamiento de eventos (suficiente para el período de retención de 24 horas de forma predeterminada).

Unidades de rendimiento de evento Hubs se cargarán por horas, en función del número máximo de unidades seleccionado durante la hora especificada.

## <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>¿Cómo se aplican límites de unidad de rendimiento de evento Hubs?

Si el rendimiento de entrada total o de la velocidad de evento de entrada total entre todos los Hubs de evento en un espacio de nombres supera los derechos de emisión de unidad de rendimiento agregado, remitentes reducirá y reciben errores que indica que se ha superado la cuota de entrada.

Si el rendimiento de salida total o de la tasa de salida de evento total entre todos los Hubs de evento en un espacio de nombres supera los derechos de emisión de unidad de rendimiento agregado, receptores son acelerados y reciben errores que indica que se ha superado la cuota de salida. Las cuotas de entrada y salida se aplican por separado, para que no remitente puede provocar consumo de evento ralentizar ni puede evitar un receptor de eventos se envíen a un concentrador de evento.

Observe que la selección de la unidad de rendimiento es independiente del número de particiones Hubs de evento. Aunque cada partición ofrece un rendimiento máximo de 1 MB por segundo entrada (con un máximo de 1.000 eventos por segundo) y 2 MB por segundo salida, no hay ningún cargo fijo para las particiones a sí mismos. El cargo es para las unidades de rendimiento agregado en todos los Hubs de evento en un espacio de nombres de evento Hubs. Con este modelo, puede crear las particiones suficientes para admitir la carga máxima prevista para sus sistemas, evitando los gastos de la unidad de rendimiento hasta que la carga de evento en el sistema que realmente requiere números superiores de rendimiento y sin tener que cambiar la estructura y la arquitectura de los sistemas de la carga en el sistema aumenta.

## <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>¿Hay algún límite en el número de unidades de rendimiento que pueden seleccionarse?

Hay una cuota predeterminada de 20 unidades de rendimiento por espacio de nombres. Puede solicitar una cuota mayor de unidades de rendimiento al archivar una incidencia de soporte técnico. Más allá del límite de unidad de 20 rendimiento paquetes están disponibles en 20 y 100 unidades de rendimiento. Observe que el uso de más de 20 unidades de rendimiento quita la capacidad de cambiar el número de unidades de rendimiento sin archivar una incidencia de soporte técnico.

## <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>¿Hay un cargo para retener eventos Hubs de evento de más de 24 horas?

El nivel de evento Hubs estándar permiten períodos más de 24 horas, hasta un máximo de 30 días de retención de mensajes. Si el tamaño del número total de eventos almacenados supera el margen de almacenamiento para el número de unidades de rendimiento seleccionada (84 GB por unidad de rendimiento), el tamaño que supera la permitida se cargará a la tasa de almacenamiento de blobs de Windows Azure publicada. Margen de almacenamiento en cada unidad de rendimiento cubre todos los costes de almacenamiento para períodos de retención de 24 horas (opción predeterminada) incluso si se utiliza la unidad de rendimiento hacia arriba a la permitida máximo de entrada.

## <a name="what-is-the-maximum-retention-period"></a>¿Qué es el período de retención máximo?

Nivel de evento Hubs estándar admite actualmente un período de retención máximo de 7 días. Tenga en cuenta que no se pretende Hubs de evento como un almacén de datos permanente. Períodos de retención más de 24 horas están pensados para escenarios en los que es adecuado reproducir una secuencia de evento en los sistemas de la misma; Por ejemplo, para formar o comprobar un nuevo modelo de aprendizaje de equipo en los datos existentes.

## <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>¿Cómo calcula y cargará el tamaño de almacenamiento de evento Hubs?

El tamaño total de todos los eventos almacenados, incluyendo cualquier sobrecarga interno para los encabezados de evento o en las estructuras de almacenamiento de disco en todos los centros de evento, se mide durante todo el día. Al final del día, se calcula el tamaño máximo de almacenamiento. El margen de almacenamiento diaria se calcula en función del número mínimo de unidades de rendimiento que se han seleccionado durante el día (cada unidad de rendimiento proporciona un ajuste del 84 GB). Si el tamaño total supera el margen de almacenamiento diarias calculado, se cobra el almacenamiento exceso con tasas de almacenamiento de blobs de Windows Azure (a la velocidad de **Almacenamiento redundante localmente** ).

## <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-event-hubs-and-service-bus-queuestopics"></a>¿Puedo usar una conexión AMQP para enviar y recibir de evento Hubs y colas y temas de Bus de servicio?

Sí, siempre y cuando todos los eventos Hubs, colas y temas están en el mismo espacio de nombres. Por lo tanto, puede implementar bidireccional, con conectividad con multitud de dispositivos con latencia subsecond, de manera rentable y gran escalabilidad.

## <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>¿Se aplican cargos con conexión a Hubs de eventos?

Para remitentes, aplicar cargos de conexión solo cuando se usa el protocolo AMQP. No hay ningún gastos de conexión para el envío de eventos utilizando HTTP, independientemente del número de envío de sistemas o dispositivos. Si planea usar AMQP (por ejemplo, para lograr la transmisión de evento más eficaz o para habilitar la comunicación bidireccional en escenarios de comando y control IoT), consulte la página de [información de precios de Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/) para obtener información sobre qué constituye una conexión con y cómo están uso medidas.

## <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>¿Cuál es la diferencia entre evento Hubs básica y niveles estándares?

Nivel de Hubs estándar de evento proporciona características más allá de lo que está disponible en evento Hubs básico y, en algunos sistemas competitivos. Estas características incluyen períodos de retención de más de 24 horas y la capacidad de usar una conexión AMQP enviar comandos a un gran número de dispositivos con latencia subsecond, así como enviar telemetría de esos dispositivos en Hubs de evento. Para obtener la lista de características, vea los [Detalles de precios Hubs de evento](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="geographic-availability"></a>Disponibilidad geográfica

Evento Hubs está disponible en las siguientes regiones:

|Geo|Regiones|
|---|---|
|Estados Unidos|Central de Estados Unidos, US oriental, Estados Unidos oriental 2, sur Central de EE., oeste de Estados Unidos|
|Europa|Europa del Norte, Europa occidental|
|Asia Pacífico|Asia oriental, sudeste asiático|
|Japón|Japón, este Japón oeste|
|Brasil|Sur de Brasil|
|Australia|Australia Oriental, sudeste de Australia|

## <a name="support-and-sla"></a>SLA y soporte técnico

Soporte técnico para Hubs de evento está disponible a través de los [foros de la Comunidad](https://social.msdn.microsoft.com/forums/azure/home). Proporciona soporte de administración de facturación y suscripción sin costo.

Para obtener más información acerca de nuestro SLA, consulte la página de [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el evento Hubs, consulte los siguientes artículos:

- [Información general de Hubs de evento][].
- Una [aplicación de ejemplo que usa el evento Hubs][]completa.

[Información general de Hubs de evento]: event-hubs-overview.md
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
