<properties 
    pageTitle="Azure colas y Bus de servicio colas - comparados y diferencia | Microsoft Azure"
    description="Analiza las diferencias y similitudes entre dos tipos de colas ofrecidas por Azure."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="09/23/2016"
    ms.author="sethm" />

# <a name="azure-queues-and-service-bus-queues---compared-and-contrasted"></a>Azure colas y Bus de servicio colas - comparados y diferencia

En este artículo se analiza las diferencias y similitudes entre los dos tipos de colas ofrecidas por Microsoft Azure hoy: colas colas de Azure y Bus de servicio. Utilizando esta información, puede comparar y contrastar las tecnologías correspondientes y podrá tomar una decisión más informada acerca de qué solución mejor adapte a sus necesidades.

## <a name="introduction"></a>Introducción

Microsoft Azure es compatible con dos tipos de mecanismos de cola: **Colas de Azure** y **Bus de servicio**.

**Colas de azure**, que son parte de la infraestructura de [almacenamiento de Azure](https://azure.microsoft.com/services/storage/) , cuentan con una interfaz de basado en REST superponer/Get/Peek simple, que proporciona mensajería confiable y persistente dentro y entre los servicios.

**Colas de Bus de servicio** son parte de una infraestructura de [mensajería de Azure](https://azure.microsoft.com/services/service-bus/) más amplia que admita Queue así como patrones de integración, remota de servicio Web y de publicación o suscripción. Para obtener más información sobre colas de Bus de servicio, temas y las suscripciones y transmisiones, vea la [información general sobre mensajería Bus de servicio](service-bus-messaging-overview.md).

Aunque ambas tecnologías cola existen simultáneamente, colas de Azure introducidas en primer lugar, como mecanismo de almacenamiento de cola dedicada creado a partir de los servicios de almacenamiento de Azure. Colas de Bus de servicio se generan sobre la infraestructura de "negociada mensajería" más amplia diseñada para integrar aplicaciones o componentes de la aplicación que pueden abarcar varias protocolos de comunicaciones, los contratos de datos, dominios de confianza o entornos de red.

Este artículo compara las tecnologías de dos colas ofrecidas por Azure analizando las diferencias en el comportamiento y la implementación de las características proporcionadas por cada uno. El artículo también proporciona orientación para elegir qué características pueden satisfacer mejor las necesidades de desarrollo de aplicaciones.

## <a name="technology-selection-considerations"></a>Consideraciones de selección de tecnología

Colas colas de Azure y Bus de servicio son implementaciones de la cola de servicio ofrecido actualmente en Microsoft Azure de mensajes. Cada una tiene un conjunto de características ligeramente diferente, lo que significa que puede elegir uno u otro, o utilizar ambos, según las necesidades de su solución en particular o un problema empresarial o técnico que va a resolver.

Al determinar qué tecnología cola se ajusta el propósito de una solución determinada, los desarrolladores y arquitectos de soluciones deben tener en cuenta las siguientes recomendaciones. Para obtener más detalles, consulte la sección siguiente.

Como solución architect/desarrollador, **debe considerar usar colas de Azure** cuando:

- La aplicación debe almacenar más de 80 GB de mensajes de una cola, donde los mensajes tienen una duración de menos de 7 días.

- La aplicación desea hacer un seguimiento de un mensaje dentro de la cola de procesamiento. Esto es útil si se bloquea el trabajo de procesamiento de un mensaje. Trabajador posteriores, a continuación, puede utilizar esa información para continuar desde donde la dejó el trabajo anterior.

- Requerir registros del lado servidor de todas las transacciones ejecutadas colas.

Como solución architect/desarrollador, **debe considerar usar colas de Bus de servicio** cuando:

- La solución debe ser capaz de recibir mensajes sin tener que sondear la cola. Con Bus de servicio, puede hacerlo mediante el uso del sondeo larga operación mediante los protocolos de TCP que admita Bus de servicio de recepción.

- La solución requiere la cola para proporcionar una garantía primero-en-primero en salir (FIFO) entrega ordenada.

- Desea una experiencia simétrica en Azure y en Windows Server (de nube privada). Para obtener más información, vea [Bus de servicio de Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- La solución debe ser compatible con la detección automática de duplicados.

- Desea que su aplicación para procesar los mensajes como paralelo secuencias de ejecución larga (mensajes están asociados con un flujo de uso de la propiedad [ID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) en el mensaje). En este modelo, cada nodo de la aplicación que compite por secuencias, en lugar de mensajes. Cuando un nodo que le proporcionará una secuencia, el nodo puede examinar el estado del estado de flujo de la aplicación con las transacciones.

- La solución requiere comportamiento transacciones y atomicidad al enviar o recibir varios mensajes de una cola.

- La característica de (TTL) time to live de la carga de trabajo específicos de la aplicación puede superar el período de 7 días.

- La aplicación controla los mensajes que pueden superar 64 KB pero no es probable enfoque 256 KB limitan.

- Tratar un requisito para proporcionar un modelo de acceso basado en roles a las colas y permisos diferentes para remitentes y destinatarios.

- El tamaño de la cola no crecerá más de 80 GB.

- Desea usar el protocolo de mensajería basado en estándares AMQP 1.0. Para obtener más información sobre AMQP, vea [Información general de AMQP de Bus de servicio](./service-bus-amqp-overview.md).

- Podrá hacerse una idea una migración final de la comunicación de punto a punto basadas en cola a un modelo de exchange de mensaje que permite la integración perfecta de receptores adicionales (suscriptores), que reciben copias independientes de algunos o todos los mensajes enviados a la cola. El segundo se refiere a la capacidad de publicación o suscripción suministra Bus de servicio.

- La solución de mensajería debe ser compatible con la garantía de entrega "En más de una vez" sin necesidad de crear los componentes de la infraestructura adicional.

- Que le gustaría que pueda publicar y consumir lotes de mensajes.

- Requerir la integración completa con la pila de comunicación de Windows Communication Foundation (WCF) en .NET Framework.

## <a name="comparing-azure-queues-and-service-bus-queues"></a>Comparación de colas colas de Azure y Bus de servicio

Las tablas en las siguientes secciones proporcionan una agrupación lógica de características de cola y le permiten comparar de un vistazo, las funciones disponibles en las colas de Azure y Bus de servicio.

## <a name="foundational-capabilities"></a>Capacidades básicas

Esta sección comparan algunas de las capacidades de cola fundamentales proporcionadas por colas colas de Azure y Bus de servicio.

|Criterios de comparación|Colas de Azure|Colas de Bus de servicio|
|---|---|---|
|Garantía de ordenación|**No** <br/><br>Para obtener más información, vea la primera nota en la sección "Información adicional".</br>|**Sí - primero en el primero en salir (FIFO)**<br/><br>(mediante el uso de sesiones de mensajería)|
|Garantía de entrega|**Por lo menos de una vez**|**Por lo menos de una vez**<br/><br/>**En la mayoría de una vez**|
|Soporte de operación atómica|**No**|**Sí**<br/><br/>|
|Comportamiento de recepción|**Sin bloqueo**<br/><br/>(finaliza inmediatamente si se encuentra ningún mensaje nuevo)|**Con/sin tiempo de espera de bloqueo**<br/><br/>(ofertas long sondeo o la ["Técnica de Comet"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Sin bloqueo**<br/><br/>(mediante el uso de .NET API administrada solo)|
|API de estilo de inserción|**No**|**Sí**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx) y **OnMessage** sesiones .NET API.|
|Modo de recepción|**Información y concesión**|**Inspeccionar y bloquear**<br/><br/>**Recibir y eliminar**|
|Modo de acceso exclusivo.|**Basada en concesión**|**En función de bloqueo**|
|Duración de concesión/bloqueo|**30 segundos (predeterminado)**<br/><br/>**7 días (máximo)** (Puede renovar o liberar una concesión de mensaje con la API [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) .)|**60 segundos (predeterminado)**<br/><br/>Puede renovar un bloqueo de mensaje con la API [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .|
|Concesión/bloquear precisión|**Nivel de mensaje**<br/><br/>(cada mensaje puede tener un valor de tiempo de espera diferente, que puede actualizar según sea necesario al procesar el mensaje, mediante el uso de la API [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) )|**Nivel de cola**<br/><br/>(cada cola tiene una precisión de bloqueo aplicada a todos sus mensajes, pero puede renovar el bloqueo con la API [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) ).|
|Por lotes recibir|**Sí**<br/><br/>(explícitamente que especifica el número de mensajes al recuperar mensajes, hasta un máximo de 32 mensajes)|**Sí**<br/><br/>(habilitar implícitamente una propiedad anticipada o explícitamente mediante el uso de transacciones)|
|Enviar por lotes|**No**|**Sí**<br/><br/>(mediante el uso de transacciones o por lotes del cliente)|

### <a name="additional-information"></a>Información adicional

- Mensajes de Azure colas son normalmente primero-en-primero en salir, pero a veces puede ser ordenadas; Por ejemplo, cuando expira duración de tiempo de espera de visibilidad del mensaje (por ejemplo, como resultado de una aplicación de cliente que se bloquea durante el proceso). Cuando se agota el tiempo de espera de la visibilidad, el mensaje se hace visible nuevo en la cola para otro trabajador quitarlo de la cola. En ese momento, el mensaje recién visible puede colocarse en la cola (que se pueden quitar de la cola nuevamente) después de un mensaje que estaba originalmente en cola después de él.

- Si ya está usando Azure almacenamiento de Blobs o tablas y empezar a usar colas, se garantiza 99,9% de disponibilidad. Si usas BLOB o tablas con colas de Bus de servicio, tendrá menor disponibilidad.

- El modelo de FIFO garantizado en colas de Bus de servicio requiere el uso de sesiones de mensajería. En caso de que la aplicación se bloquea al procesar un mensaje recibido en el modo de **información y bloquear** , la próxima vez que un receptor de cola acepta una sesión de mensajería, se iniciará con el mensaje error después de que su time to live (TTL) expire.

- Colas Azure están diseñadas para admitir escenarios de cola estándares, como desacoplamiento componentes de la aplicación para aumentar la escalabilidad y tolerancia a errores, carga redistribución y la creación de flujos de trabajo del proceso.

- Colas de Bus de servicio compatible con la garantía de entrega *En-menos una vez* . Además, la *Mayoría-continua* semántico pueden incluir mediante el estado de sesión para almacenar el estado de la aplicación y mediante las transacciones de forma atómica reciban mensajes y actualizar el estado de sesión.

- Colas Azure proporcionan un modelo de programación uniforme y coherente en colas, tablas y BLOBs: para los programadores y equipos de operaciones.

- Colas de Bus de servicio proporcionan compatibilidad para las transacciones locales en el contexto de una sola cola.

- El modo de **recepción y eliminar** compatible con Bus de servicio proporciona la capacidad para reducir el número de operaciones de mensajería (y costos asociados) a cambio de la garantía de entrega reducidos.

- Colas Azure proporcionan arrendamientos con la capacidad de extender la arrendamientos para los mensajes. Esto permite que los trabajadores mantener breves arrendamientos en los mensajes. Por lo tanto, si se bloquea un trabajo, el mensaje puede rápidamente procese vuelva a otro trabajador. Además, un trabajador puede ampliar la concesión de un mensaje si es necesario procesarlo más larga que el tiempo de concesión actual.

- Colas Azure ofrecen visibilidad tiempo de espera que se puede establecer en la enqueueing o cola de un mensaje. Además, puede actualizar un mensaje con valores de concesión diferente en tiempo de ejecución y actualizarse distintos valores en los mensajes en la misma cola. Tiempo de espera de bloqueo de Bus de servicio se define en los metadatos de cola; Sin embargo, puede renovar el bloqueo llamando al método [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) .

- El tiempo de espera máximo para un bloqueo de la operación de recepción en colas de Bus de servicio es de 24 días. Sin embargo, el tiempo de espera del resto tiene un valor máximo de 55 segundos.

- Cliente por lotes proporcionados por Bus de servicio permite a los clientes de cola por lotes varios mensajes en una operación de envío único. Procesamiento por lotes solo está disponible para las operaciones de envío asincrónico.

- Características como el límite máximo de 200 TB de Azure colas (más cuando virtualización de cuentas) e ilimitado que sea una plataforma ideal para los proveedores de SaaS.

- Colas Azure proporcionan un flexible y eficaz mecanismo de control de acceso de delegado.

## <a name="advanced-capabilities"></a>Capacidades avanzadas

Esta sección compara las capacidades avanzadas de colas colas de Azure y Bus de servicio.

|Criterios de comparación|Colas de Azure|Colas de Bus de servicio|
|---|---|---|
|Entrega programada|**Sí**|**Sí**|
|Letras inactivas automática|**No**|**Sí**|
|Aumentar el valor de tiempo de vida de cola|**Sí**<br/><br/>(a través de la actualización en contexto de tiempo de espera de visibilidad)|**Sí**<br/><br/>(proporcionado a través de una función de API dedicada)|
|Compatibilidad con los mensajes de daños|**Sí**|**Sí**|
|Actualización en contexto|**Sí**|**Sí**|
|Registro de transacciones del servidor|**Sí**|**No**|
|Métrica de almacenamiento|**Sí**<br/><br/>**Minuto métricas**: proporciona métricas en tiempo real para la API de disponibilidad, TPS, llamar recuentos, recuentos de error etc., en tiempo de real (agregado por minuto y notificado dentro de unos minutos de ¿qué ocurrió justo en producción. Para obtener más información, vea [Información sobre el análisis de métrica de almacenamiento](https://msdn.microsoft.com/library/azure/hh343258.aspx).|**Sí**<br/><br/>(masa consultas llamando [GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx))|
|Administración de estado|**No**|**Sí**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)|
|Reenvío de mensajes automático|**No**|**Sí**|
|Función de cola de purga|**Sí**|**No**|
|Grupos de mensajes|**No**|**Sí**<br/><br/>(mediante el uso de sesiones de mensajería)|
|Estado de la aplicación por grupo de mensaje|**No**|**Sí**|
|Detección de duplicados|**No**|**Sí**<br/><br/>(puede configurar en el lado de remitente)|
|Integración de WCF|**No**|**Sí**<br/><br/>(ofrece enlaces de cuadro WCF)|
|Integración de WF|**Personalizado**<br/><br/>(requiere la creación de una actividad WF personalizada)|**Nativo**<br/><br/>(ofrece actividades de WF de cuadro)|
|Exploración de grupos de mensajes|**No**|**Sí**|
|Obtención de sesiones de mensajería por Id.|**No**|**Sí**|

### <a name="additional-information"></a>Información adicional

- Ambas tecnologías de cola habilitar un mensaje Programar entrega en un momento posterior.

- Reenvío automático de cola permite miles de colas para reenviar automáticamente sus mensajes a una sola cola, desde la que la aplicación receptora consume el mensaje. Puede utilizar este mecanismo para lograr la seguridad, flujo de control y aislar almacenamiento entre cada editor del mensaje.

- Colas Azure proporcionan asistencia técnica para actualizar el contenido del mensaje. Puede usar esta funcionalidad para guardar información de estado y las actualizaciones del progreso incremental en el mensaje para que se puede procesar desde el último control conocido, en lugar de empezar desde cero. Con colas de Bus de servicio, puede habilitar la misma situación mediante el uso de las sesiones de mensaje. Sesiones le permiten guardar y recuperar el estado de procesamiento de la aplicación (mediante [SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx) y [GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- [Letras inactivas](service-bus-dead-letter-queues.md), que solo admite colas de Bus de servicio, puede ser útil para aislar los mensajes que no se puede procesar correctamente la aplicación receptora o cuando los mensajes no pueden llegar a su destino debido a una expirada time to live (TTL) (propiedad). El valor TTL especifica un mensaje cuánto tiempo permanece en la cola. Con Bus de servicio, el mensaje se moverá a una cola especial denominada $DeadLetterQueue cuando expira el período de vida.

- Para buscar mensajes "dudosos" colas de Azure, cuando cola un mensaje la aplicación examina la propiedad **[DequeueCount](https://msdn.microsoft.com/library/azure/dd179474.aspx)** del mensaje. Si supera un determinado umbral **DequeueCount** , la aplicación mueve el mensaje a una aplicación "cola mensajes no enviados".

- Colas Azure le permiten obtener un registro detallado de todas las transacciones ejecutadas la cola, como métricas así como agregados. Ambas de estas opciones son útiles para depurar y comprender cómo utiliza su aplicación colas de Azure. También son útiles para ajuste de rendimiento de la aplicación y reducir los costos de usar colas.

- El concepto de "sesiones de mensaje" compatible con Bus de servicio permite que los mensajes que pertenecen a un determinado grupo lógico estar asociadas a un determinado receptor, que a su vez crea una sesión de la afinidad entre los mensajes y sus respectivos receptores. Puede habilitar esta funcionalidad avanzada en Bus de servicio estableciendo la propiedad [ID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) de un mensaje. Receptores pueden escuchar un ID específico y recibir mensajes que comparten el identificador de sesión especificado.

- La funcionalidad de detección de duplicación compatible con colas de Bus de servicio automáticamente quita duplicados mensajes enviados a una cola o un tema, en función del valor de la propiedad [identificador del mensaje](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) .

## <a name="capacity-and-quotas"></a>Capacidad y cuotas

Esta sección compara colas colas de Azure y Bus de servicio desde la perspectiva de [capacidad y cuotas](service-bus-quotas.md) que se puede aplicar.

|Criterios de comparación|Colas de Azure|Colas de Bus de servicio|
|---|---|---|
|Tamaño máximo de cola|**200 GB**<br/><br/>(limitado a una sola cuenta de capacidad de almacenamiento)|**1 GB a 80 GB**<br/><br/>(definido tras la creación de una cola y [Habilitar particiones](service-bus-partitioning.md) : vea la sección "Información adicional")|
|Tamaño máximo de mensaje|**64 KB**<br/><br/>(48 KB cuando se utiliza codificación **base 64** )<br/><br/>Azure es compatible con los mensajes de gran tamaño combinando colas y blobs: momento en que puede poner un máximo de 200GB para un solo elemento.|**256 KB** o **1 MB**<br/><br/>(incluido el encabezado y cuerpo, el tamaño máximo de encabezado: 64 KB).<br/><br/>Depende del [nivel de servicio](service-bus-premium-messaging.md).|
|TTL máximo de los mensajes|**7 días**|**`TimeSpan.Max`**|
|Número máximo de colas|**Ilimitado**|**10.000**<br/><br/>(por el espacio de nombres de servicio, se puede aumentar)|
|Número máximo de clientes simultáneos|**Ilimitado**|**Ilimitado**<br/><br/>(100 límite de conexión simultánea solo se aplica a la comunicación basada en el protocolo TCP)|

### <a name="additional-information"></a>Información adicional

- Bus de servicio impone límites de tamaño de cola. El tamaño máximo de cola especificado tras la creación de la cola y puede tener un valor entre 1 y 80 GB. Si se alcanza el valor de tamaño de cola establecido en la creación de la cola, los mensajes entrantes adicionales será rechazados y el código de llamada recibirá una excepción. Para obtener más información acerca de las cuotas de Bus de servicio, vea [Las cuotas de Bus de servicio](service-bus-quotas.md).

- Puede crear colas de Bus de servicio en tamaños de 1, 2, 3, 4 o 5 GB (el valor predeterminado es 1 GB). Con partición habilitada (que es el valor predeterminado), Bus de servicio crea 16 particiones para cada GB que especifique. Por lo tanto, si crea una cola que es 5 GB en tamaño, con 16 particiones el tamaño máximo de cola se convierte en (5 * 16) = 80 GB. Puede ver el tamaño máximo del tema o cola dividida consultando su entrada en el [portal de Azure][].

- Con colas de Azure, si el contenido del mensaje no está seguro de XML, a continuación, debe ser codificada **base 64** . Si se **base 64**-codificar el mensaje, la carga de usuario puede ser 48 KB, en lugar de 64 KB.

- Con colas de Bus de servicio, cada mensaje que se almacenan en una cola consta de dos partes: un encabezado y un cuerpo. El tamaño total del mensaje no puede superar el tamaño máximo de mensaje admitido por el nivel de servicio.

- Cuando los clientes comunican con colas de Bus de servicio sobre el protocolo TCP, el número máximo de conexiones simultáneas a una sola cola de Bus de servicio se limita a 100. Este número se comparte entre remitentes y destinatarios. Si se alcanza la cuota, las solicitudes subsiguientes para conexiones adicionales será rechazadas y el código de llamada recibirá una excepción. No se impone este límite en clientes que se conectan a las colas mediante API de REST.

- Si necesita más de 10.000 colas en un único espacio de nombres de Bus de servicio, puede ponerse en contacto con el equipo de soporte de Azure y solicitar un aumento. Para ajustar más allá de 10.000 colas con Bus de servicio, también puede crear espacios de nombres adicionales con el [portal de Azure][].

## <a name="management-and-operations"></a>Administración y operaciones

Esta sección compara las características de administración de colas colas de Azure y Bus de servicio.

|Criterios de comparación|Colas de Azure|Colas de Bus de servicio|
|---|---|---|
|Protocolo de administración|**Coloque a través de HTTP/HTTPS**|**Coloque sobre HTTPS**|
|Protocolo de tiempo de ejecución|**Coloque a través de HTTP/HTTPS**|**Coloque sobre HTTPS**<br/><br/>**AMQP 1.0 estándar (TCP con TLS)**|
|API administrada de .NET|**Sí**<br/><br/>(.NET managed API de cliente de almacenamiento)|**Sí**<br/><br/>(Administrado negociada API de mensajería)|
|C++ nativo|**Sí**|**No**|
|API de Java|**Sí**|**Sí**|
|API DE PHP|**Sí**|**Sí**|
|Node.js API|**Sí**|**Sí**|
|Soporte de metadatos arbitrario|**Sí**|**No**|
|Convenciones de nomenclatura de cola|**Hasta 63 caracteres**<br/><br/>(Las letras de un nombre de cola deben ser minúsculas).|**260 caracteres de longitud**<br/><br/>(Nombres y las rutas de cola distinguen mayúsculas de minúsculas.)|
|Obtener la función de la longitud de cola|**Sí**<br/><br/>(Valor aproximado si mensajes expiren más allá de lo TTL sin eliminarse.)|**Sí**<br/><br/>(Valor exacto, en un momento).|
|Función de la información|**Sí**|**Sí**|

### <a name="additional-information"></a>Información adicional

- Colas Azure proporcionan asistencia técnica para los atributos arbitrarios que se pueden aplicar a la descripción de la cola, en forma de pares nombre/valor.

- Ambas tecnologías de cola ofrecen la capacidad de buscar un mensaje sin tener que bloquear, que puede ser útil al implementar una herramienta de explorador/cola.

- Servicio Bus .NET había negociada mensajería API Aproveche dúplex TCP conexiones para mejorar el rendimiento cuando se comparan con resto a través de HTTP y que admiten el protocolo estándar de AMQP 1.0.

- Nombres de Azure colas puede tener 3-63 caracteres, puede contener letras minúsculas, números y guiones. Para obtener más información, vea [colas de nomenclatura y metadatos](https://msdn.microsoft.com/library/azure/dd179349.aspx).

- Nombres de cola de Bus de servicio pueden tener hasta 260 caracteres y tiene menos restrictivos convenciones de nomenclatura. Nombres de cola de Bus de servicio pueden contener letras, números, puntos, guiones y caracteres de subrayado.

## <a name="authentication-and-authorization"></a>Autenticación y la autorización

Esta sección describen las características de autenticación y la autorización admitidas por colas colas de Azure y Bus de servicio.

|Criterios de comparación|Colas de Azure|Colas de Bus de servicio|
|---|---|---|
|Autenticación|**Clave simétrica**|**Clave simétrica**|
|Modelo de seguridad|Acceso delegado a través de tokens SA.|ASOCIACIONES DE SEGURIDAD|
|Federación de identidades proveedor|**No**|**Sí**|

### <a name="additional-information"></a>Información adicional

- Cada solicitud a cualquiera de las tecnologías de cola se debe autenticar. Colas públicas con acceso anónimo no son compatibles. [Sa](service-bus-sas-overview.md)bien puede tratar este escenario mediante la publicación de una SA sólo escritura, SA de solo lectura o incluso una SA acceso completo.

- El esquema de autenticación proporcionado por colas de Azure implica el uso de una clave simétrica, que es un basado en hash autenticación código mensajes (HMAC), calcula con el algoritmo SHA-256 y se codifica como una cadena de **base 64** . Para obtener más información acerca del protocolo respectivo, vea [autenticación para los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx). Colas de Bus de servicio admiten un modelo de similar con claves simétricas. Para obtener más información, vea [Autenticación de firma de acceso compartida con Bus de servicio](service-bus-shared-access-signature-authentication.md).

## <a name="cost"></a>Costo

Esta sección compara colas colas de Azure y Bus de servicio desde una perspectiva de costo.

|Criterios de comparación|Colas de Azure|Colas de Bus de servicio|
|---|---|---|
|Costo de la transacción de cola|**$0.0036**<br/><br/>(por 100000 transacciones)|**Nivel básico**: **0,05 dólares**<br/><br/>(por millones operaciones)|
|Operaciones facturables|**Todos los**|**Solo envío o recepción**<br/><br/>(sin cargo para otras operaciones)|
|Transacciones inactivas|**Facturable**<br/><br/>(Consultar una cola vacía se cuenta como una transacción facturable.)|**Facturable**<br/><br/>(Una recepción contra una cola vacía se considera un mensaje facturable).|
|Costo de almacenamiento|**0,07 $**<br/><br/>(por GB al mes)|**0,00 $**|
|Costes de transferencia de datos de salida|**$0,12 - $0.19**<br/><br/>(Dependiendo de la geografía.)|**$0,12 - $0.19**<br/><br/>(Dependiendo de la geografía.)|

### <a name="additional-information"></a>Información adicional

- Las transferencias de datos se cargan en función de la cantidad total de datos deja los centros de datos de Azure a través de internet en un período de facturación determinado.

- Transferencias de datos entre los servicios de Azure ubicados dentro de la misma región no están sujetos a cargos.

- En este momento todas las transferencias de datos entrantes no están sujetos a cargos.

- Dada la compatibilidad de sondeo largo, utilizando colas de Bus de servicio puede ser rentable en situaciones donde se requiere la entrega de latencia baja.

>[AZURE.NOTE] Todos los costos están sujetos a cambios. Esta tabla refleja precios actuales y no incluye las ofertas promocionales que actualmente pueden estar disponibles. Para obtener información actualizada sobre precios de Azure, consulte la página de [precios de Azure](https://azure.microsoft.com/pricing/) . Para obtener más información sobre precios de Bus de servicio, consulte [precios de Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="conclusion"></a>Conclusión

Al obtener un conocimiento más profundo de las dos tecnologías, podrá tomar una decisión más informada en la tecnología de cola que utilizará y cuándo. La decisión de cuándo usar colas colas de Azure o Bus de servicio claramente depende de varios factores. Estos factores pueden dependen de las necesidades individuales de la aplicación y su arquitectura. Si su aplicación ya usa las funciones principales de Microsoft Azure, es posible que prefiera elegir colas de Azure, sobre todo si se requiere la comunicación básica y mensajería entre servicios o colas de necesidad que pueden ser mayores que 80 GB de tamaño.

Como colas de Bus de servicio ofrecen una serie de características avanzadas, como las sesiones, las transacciones, duplicar detección automática capacidades inactivas letras y resistentes de publicación o suscripción, podrá ser una elección preferida si va a crear una aplicación híbrida o si la aplicación de lo contrario, requiere estas características.

## <a name="next-steps"></a>Pasos siguientes

Los artículos siguientes proporcionan más información sobre el uso de colas colas de Azure o Bus de servicio y orientación.

- [Cómo usar colas de Bus de servicio](service-bus-dotnet-get-started-with-queues.md)
- [Cómo usar el servicio de almacenamiento de cola](../storage/storage-dotnet-how-to-use-queues.md)
- [Prácticas recomendadas para mejorar el rendimiento con Bus de servicio negociada mensajería](service-bus-performance-improvements.md)
- [Introducción a las colas y los temas de Bus de servicio de Azure](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [La Guía del desarrollador para Bus de servicio](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
- [Mediante el servicio de cola de Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Descripción de facturación de almacenamiento de Azure: ancho de banda, transacciones y capacidad](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

[Portal de Azure]: https://portal.azure.com
 
