<properties 
    pageTitle="Bus de servicio de facturación y precios | Microsoft Azure"
    description="Información general sobre la estructura de precios de Bus de servicio."
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
    ms.date="10/06/2016"
    ms.author="sethm" />

# <a name="service-bus-pricing-and-billing"></a>Bus de servicio de facturación y precios

Bus de servicio se ofrece en los niveles básico, estándar y [Premium](service-bus-premium-messaging.md) . Puede elegir un nivel de servicio para cada espacio de nombres de servicio de Bus de servicio que cree, y se aplica esta selección de nivel en todas las entidades creadas dentro de ese espacio de nombres.

>[AZURE.NOTE] Para obtener información detallada sobre precios actual de Bus de servicio, consulte la [página de precios de Azure Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/)y las [Preguntas más frecuentes de Bus de servicio](service-bus-faq.md#service-bus-pricing).

Bus de servicio usa los siguientes dos medidores de colas y temas/suscripciones:

1. **Operaciones de mensajería**: definido como llamadas a API en cola o suscripción de tema extremos de servicio. Este medidor reemplazarán los mensajes enviados o recibidos como unidad principal de uso facturable para colas y temas/suscripciones.

2. **Conexiones negociada**: definido como el número máximo de conexiones persistentes abre frente a colas, temas o suscripciones durante un período de muestreo de una hora determinada. Este medidor solo se aplicará en el nivel estándar, en el que puede abrir conexiones adicionales (anteriormente, las conexiones se limita a 100 por suscripción, cola de tema) por una cuota nominal por conexión.

El nivel **estándar** presenta aforado precios para las operaciones realizadas con colas y temas/suscripciones, de descuentos basados en volumen de 80% en los niveles de uso más altos. También es un cargo base nivel estándar de 10 $ al mes, lo que permite realizar operaciones de 12,5 millones por mes sin costo adicional.

El nivel de **Premium** proporciona aislamiento de recursos en el nivel de CPU y la memoria para que se ejecute cada carga de trabajo de cliente por separado. Este contenedor de recursos se denomina una *unidad de mensajería*. Cada espacio de nombres de premium está asignada al menos una unidad de mensajería. Puede adquirir 1, 2 o 4 unidades de mensajería para cada espacio de nombres de servicio Bus Premium. Una sola carga de trabajo o la entidad puede abarcar varias unidades de mensajería y el número de unidades de mensajería puede cambiarse cuando lo desee, aunque es de facturación de cargos de tasa diaria o de 24 horas. El resultado es un desempeño reproducible y predecible para la solución de Bus de servicio. No solo es este rendimiento más predecible y está disponible, pero también es más rápido. Mensajería de Azure Premium de Bus de servicio se basa en el motor de almacenamiento que introdujo en Azure evento Hubs. Con la mensajería Premium pleno rendimiento es mucho más rápido que el nivel estándar.

Tenga en cuenta que el gasto de base estándar se cargará solo una vez al mes por suscripción Azure. Esto significa que después de crear un único estándar o espacio de nombres de Bus de servicios de nivel de Premium, podrá crear tantas estándar o Premium nivel espacios de nombres adicionales como necesite en esa misma suscripción Azure, evitando cargos bases adicionales.

Todos los espacios de Bus de servicio existentes creados antes del 1 de noviembre de 2014 se colocan automáticamente en la capa estándar. Así se garantiza que sigue teniendo acceso a todas las características están disponibles con Bus de servicio. Posteriormente, puede usar el [portal clásica Azure][] degradar al nivel básico si lo desea.

La siguiente tabla resume las diferencias funcionales entre los niveles básico y estándar o Premium.

|Capacidad|Básico|Estándar y Premium|
|---|---|---|
|Colas|Sí|Sí|
|Mensajes programados|Sí|Sí|
|Temas y suscripciones|No|Sí|
|Transmisiones|No|Sí|
|Transacciones|No|Sí|
|Desduplicación|No|Sí|
|Sesiones|No|Sí|
|Mensajes de gran tamaño|No|Sí|
|Hacia delantePara|No|Sí|
|SendVia|No|Sí|
|Conexiones con (incluidas)|100 por espacio de nombres de Bus de servicio|1.000 por suscripción de Azure|
|Conexiones con (excedente permitido)|No|Sí (facturable)|

## <a name="messaging-operations"></a>Operaciones de mensajería

Como parte del nuevo modelo de precio, está cambiando facturación para colas y temas/suscripciones. Estas entidades están cambiando de facturación por mensaje a facturación por operación. Una operación de"" se refiere a cualquier llamada API realizada en un extremo de servicio cola o suscripción de tema. Esto incluye las operaciones de estado de administración, de envío o recepción y sesión.

|Tipo de operación|Descripción|
|---|---|
|Administración|Crear, leer, actualizar, eliminar (CRUD) contra colas o temas/suscripciones.|
|Mensajería|Enviar y recibir mensajes con colas o temas/suscripciones.|
|Estado de la sesión|Obtener o establecer el estado de la sesión de una cola o un tema o suscripción.|

Los siguientes precios estaban en vigor iniciando el 1 de noviembre de 2014:

|Básico|Costo|
|---|---|
|Operaciones|0,05 dólares por millones de operaciones|

|Estándar|Costo|
|---|---|
|Cargo base|10 dólares al mes|
|En primer lugar 12,5 millones operaciones, mes|Incluido|
|12,5 operaciones de-100 millones y mes|0,80 USD por millones de operaciones|
|operaciones de 100-2500 millones y mes|0,50 USD por millones de operaciones|
|Operaciones de más de 2500 millones y mes|0,20 USD por millones de operaciones|

|Premium|Costo|
|---|---|
|Diaria|$11.13 fijo tasa por unidad de mensaje|

## <a name="brokered-connections"></a>Conexiones con

*Conexiones de Brokered* adaptarse a los patrones de uso de clientes que implican un gran número de "continuamente conectado" remitentes/receptores contra colas, temas o suscripciones. Remitentes y receptores continuamente conectados son los que se conectan con un cero en AMQP o HTTP reciban el tiempo de espera (por ejemplo, HTTP larga de sondeo). Remitentes HTTP y receptores con un tiempo de espera de inmediato no generan con conexiones.

Anteriormente, colas y temas/suscripciones tenían un límite de 100 conexiones simultáneas por dirección URL. La combinación de facturación actual elimina el límite de URL, para colas y temas/suscripciones e implementa cuotas y medición en conexiones con en el espacio de nombres de Bus de servicio y niveles de suscripción de Azure.

El nivel básico incluye y se limitan estrictamente a 100 conexiones con por espacio de nombres de Bus de servicio. Conexiones superior a este número será rechazadas en el nivel básico. El nivel estándar elimina el límite por espacio de nombres y recuentos de uso de la conexión con agregado a través de la suscripción de Azure. En el nivel estándar, se le permitirá conexiones con 1.000 por suscripción Azure sin costo (más allá de la carga de la base) adicional. Usar más de un total de 1.000 conexiones con a través de Bus de servicio de nivel estándar espacios de nombres en una suscripción de Azure se cargará en una programación aforada, tal como se muestra en la tabla siguiente.

|Conexiones con (nivel estándar)|Costo|
|---|---|
|Primera 1.000 por mes|Incluido con cargo base|
|1.000-100,000, mes|0,03 dólares al mes o conexión|
|100,000-500 000 y mes|0.025 $ al mes/conexión|
|Sobre 500.000 por mes|0,015 $ al mes/conexión|

>[AZURE.NOTE] 1.000 conexiones con se incluyen con el nivel de mensajería estándar (a través de la carga de la base) y se pueden compartir en todas las colas, temas y suscripciones dentro de la suscripción de Azure asociada.

<br />

>[AZURE.NOTE] Facturación se basa en el número máximo de conexiones simultáneas y es prorrateo según cada hora 744 horas por mes.

|Nivel de Premium
|---|
|Con conexiones no se cargan en el nivel Premium.|

Para obtener más información acerca de las conexiones con, consulte la sección [preguntas más frecuentes sobre](#faq) más adelante en este tema.

## <a name="relay"></a>Retransmisión

Transmisiones sólo están disponibles en los espacios de nombres de nivel estándar. En caso contrario, las cuotas de conexión y precios para transmisiones permanezcan igual. Esto significa que transmisiones seguirán cobrará en el número de mensajes (no las operaciones) y a continuación, horas de retransmisión.

|Retransmisión de precios|Costo|
|---|---|
|Horas de retransmisión|$0,10 para cada horas de 100 retransmisión|
|Mensajes|$0,01 por cada 10.000 mensajes|

## <a name="faq"></a>Preguntas más frecuentes

### <a name="how-is-the-relay-hours-meter-calculated"></a>¿Cómo se calcula el medidor de retransmisión horas?

Vea [este tema](service-bus-faq.md#how-is-the-relay-hours-meter-calculated).

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>¿Qué son negociada conexiones y cómo obtener cargará para ellos?

Una conexión con se define como uno de estos procedimientos:

1. Una conexión de AMQP desde un cliente a una cola de Bus de servicio o la suscripción de tema.

2. Una llamada HTTP para recibir un mensaje de una cola que tiene un valor de tiempo de espera de recepción mayor que cero o un tema de Bus de servicio.

Cargos de Bus de servicio para el número máximo de conexiones con simultáneas que exceden la cantidad incluye (1.000 en el nivel estándar). Picos se mide en cada hora, Prorrateo divide por 744 horas en un mes y sumados durante el período de facturación mensual. La cantidad incluye (1.000 conexiones al mes) se aplica al final del período de facturación con la suma de los picos prorrateados por hora.

Por ejemplo:

1. Cada uno de los dispositivos de 10.000 se conecta a través de una única conexión AMQP y recibe comandos de un tema de Bus de servicio. Los dispositivos envían eventos de telemetría a un concentrador de evento. Si todos los dispositivos que se conectan por 12 horas al día, los gastos de conexión siguientes aplicarán (además de los otros gastos de tema Bus de servicio): *12 horas* de 10.000 conexiones 31 días / 744 = 5.000 negociada conexiones. Después de la permitida mensual de 1.000 conexiones con, le cobrará para las conexiones con 4.000 a la velocidad de 0,03 dólares por con conexión, para un total de 120 $.

2. 10.000 dispositivos reciben mensajes de una cola de Bus de servicio a través de HTTP, que especifica un tiempo de espera distinto de cero. Si todos los dispositivos conectan para todos los días de 12 horas, verá los gastos de conexión siguiente (además de los cargos de Bus de servicio): 10.000 HTTP recibir conexiones *12 horas por día* horas 744/31 días = 5.000 con las conexiones.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>¿Gastos de conexión con que se aplican a colas y temas/suscripciones?

Sí. No hay ningún gastos de conexión para el envío de eventos utilizando HTTP, independientemente del número de envío de sistemas o dispositivos. Recibir eventos con HTTP utilizando un tiempo de espera mayor que cero, a veces denominado "tiempo de sondeo," genera cargos con conexión. Conexiones de AMQP generan gastos de conexión con independientemente de si las conexiones se que se utiliza para enviar o recibir. Tenga en cuenta que 100 conexiones con permitidos sin cargo en un espacio de nombres básico. También es el número máximo de conexiones con permitidas para la suscripción de Azure. Las primeros 1.000 conexiones con a través de todos los espacios de nombres estándares de una suscripción de Azure se incluyen sin costo adicional (más allá de la carga de la base). Porque estos subsidios son suficientes para cubrir varios escenarios de mensajería de servicio de servicio, cargos de conexión con normalmente solo será pertinentes si piensa utilizar AMQP o HTTP sondeo mucho tiempo con un gran número de clientes; Por ejemplo, para lograr la transmisión de evento más eficaz o habilitar la comunicación bidireccional con muchos dispositivos o instancias de la aplicación.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más detalles sobre precios de Bus de servicio, consulte la [página de precios de Bus de servicio de Azure](https://azure.microsoft.com/pricing/details/service-bus/).

- Consulte las [Preguntas más frecuentes de Bus de servicio](service-bus-faq.md#service-bus-pricing) para algunas preguntas frecuentes comunes alrededor de bus de servicio de facturación y precios.

[Portal de clásico de Azure]: http://manage.windowsazure.com