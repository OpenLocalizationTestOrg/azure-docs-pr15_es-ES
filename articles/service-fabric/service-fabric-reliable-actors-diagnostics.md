<properties
   pageTitle="Diagnósticos de actores y supervisar | Microsoft Azure"
   description="Este artículo describen los diagnósticos y características en tiempo de ejecución de servicio tela confiable actores, incluidos los eventos y contadores emitidos por ella de supervisión de rendimiento."
   services="service-fabric"
   documentationCenter=".net"
   authors="abhishekram"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="abhisram"/>

# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnósticos y supervisión de rendimiento para actores confiable
El tiempo de ejecución de actores confiable emite eventos de [origen de eventos](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) y [contadores de rendimiento](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Estas proporcionan información sobre cómo funciona el tiempo de ejecución y ayudarán con la solución de problemas y la supervisión de rendimiento.

## <a name="eventsource-events"></a>Eventos de origen de eventos
El nombre del proveedor de origen de eventos para el tiempo de ejecución de actores confiable es "Actores de ServiceFabric de Microsoft". Eventos de este origen de evento aparecen en la ventana de [Eventos de diagnósticos](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) cuando la aplicación de actor se [Depurar en Visual Studio](service-fabric-debugging-your-application.md).

Ejemplos de herramientas y tecnologías de ayuda en recopilar y ver los eventos de origen de eventos son [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [Registro semántico](https://msdn.microsoft.com/library/dn774980.aspx)y la [Biblioteca de Microsoft TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Palabras clave
Todos los eventos que pertenecen a en el origen de actores confiable eventos están asociados a una o más palabras clave. Esto permite filtrar los eventos que se recopilan. Se definen los siguientes bits de palabra clave.

|Bit|Descripción|
|---|---|
|0 x 1|Conjunto de los eventos importantes que resumen el funcionamiento de la ejecución de actores tela.|
|0 x 2|Conjunto de eventos que describen actor método llamadas. Para obtener más información, vea el [tema de introducción en actores](service-fabric-reliable-actors-introduction.md#actors).|
|0 x 4|Conjunto de eventos relacionados con el estado de actor. Para obtener más información, vea el tema sobre la [administración de estado de actor](service-fabric-reliable-actors-state-management.md).|
|0 x 8|Conjunto de eventos relacionados con la simultaneidad basada en activar el actor. Para obtener más información, vea el tema de [simultaneidad](service-fabric-reliable-actors-introduction.md#concurrency).|

## <a name="performance-counters"></a>Contadores de rendimiento
El tiempo de ejecución de actores confiable define las siguientes categorías de contador de rendimiento.

|Categoría|Descripción|
|---|---|
|Servicio tela Actor|Contadores específicos para los actores de Azure tela de servicio, por ejemplo, tiempo necesario para guardar el estado de actor|
|Método de Actor tela de servicio|Contadores específicos de métodos implementados por actores tela de servicio, por ejemplo, ¿con qué frecuencia actor se invoca un método|

Cada una de las categorías anteriores tiene uno o más contadores.

La aplicación de [Monitor de rendimiento de Windows](https://technet.microsoft.com/library/cc749249.aspx) que está disponible de forma predeterminada en el sistema operativo Windows puede usarse para recopilar y ver datos de contador de rendimiento. [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) es otra opción para recopilar datos de contador de rendimiento y enviarlo a las tablas de Azure.

### <a name="performance-counter-instance-names"></a>Nombres de instancia de contador de rendimiento
Un clúster que tiene una gran cantidad de servicios de actor o las particiones del servicio de actor tendrá un gran número de instancias de contador de rendimiento de actor. Los nombres de instancia de contador de rendimiento pueden ayudar a identificar el método de [partición](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) y actor específico (si procede) que está asociada la instancia de contador de rendimiento.

#### <a name="service-fabric-actor-category"></a>Categoría de servicio tela Actor
Para la categoría `Service Fabric Actor`, los nombres de instancia de contador están en el siguiente formato:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* es la representación de cadena de Id. de partición de servicio tela que está asociada la instancia de contador de rendimiento. El identificador de partición es un GUID y la representación de cadena se genera a través de la [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) método con el especificador de formato "D".

*ActorRuntimeInternalID* es la representación de cadena de un entero de 64 bits generado por el tiempo de ejecución de tela actores para su uso interno. Esto se incluye en el nombre de instancia de contador de rendimiento para garantizar su exclusividad y evitar conflictos con otros nombres de instancia de contador de rendimiento. Los usuarios no deberían intentar interpretar esta parte del nombre de instancia de contador de rendimiento.

El siguiente es un ejemplo de un nombre de instancia de contador de un contador que pertenece el `Service Fabric Actor` categoría:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

En el ejemplo anterior, `2740af29-78aa-44bc-a20b-7e60fb783264` es la representación de cadena del identificador de partición de servicio tela, y `635650083799324046` es usar el identificador de 64 bits que se genera para el tiempo de ejecución interno.

#### <a name="service-fabric-actor-method-category"></a>Categoría de servicio tela Actor método
Para la categoría `Service Fabric Actor Method`, los nombres de instancia de contador están en el siguiente formato:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* es el nombre del método de actor que está asociada la instancia de contador de rendimiento. El formato del nombre del método se determina en función de la lógica en tiempo de ejecución de tela actores que equilibra la legibilidad del nombre con restricciones en la longitud máxima de los nombres de instancia de contador de rendimiento en Windows.

*ActorsRuntimeMethodId* es la representación de cadena de un entero de 32 bits generado por el tiempo de ejecución de tela actores para su uso interno. Esto se incluye en el nombre de instancia de contador de rendimiento para garantizar su exclusividad y evitar conflictos con otros nombres de instancia de contador de rendimiento. Los usuarios no deberían intentar interpretar esta parte del nombre de instancia de contador de rendimiento.

*ServiceFabricPartitionID* es la representación de cadena de Id. de partición de servicio tela que está asociada la instancia de contador de rendimiento. El identificador de partición es un GUID y la representación de cadena se genera a través de la [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) método con el especificador de formato "D".

*ActorRuntimeInternalID* es la representación de cadena de un entero de 64 bits generado por el tiempo de ejecución de tela actores para su uso interno. Esto se incluye en el nombre de instancia de contador de rendimiento para garantizar su exclusividad y evitar conflictos con otros nombres de instancia de contador de rendimiento. Los usuarios no deberían intentar interpretar esta parte del nombre de instancia de contador de rendimiento.

El siguiente es un ejemplo de un nombre de instancia de contador de un contador que pertenece el `Service Fabric Actor Method` categoría:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

En el ejemplo anterior, `ivoicemailboxactor.leavemessageasync` es el nombre del método `2` es el identificador de 32 bits generado para uso interno del tiempo de ejecución, `89383d32-e57e-4a9b-a6ad-57c6792aa521` es la representación de cadena del identificador de partición de servicio tela, y `635650083804480486` es usar el identificador de 64 bits generado para el tiempo de ejecución interno.

## <a name="list-of-events-and-performance-counters"></a>Lista de eventos y contadores de rendimiento

### <a name="actor-method-events-and-performance-counters"></a>Eventos de método de actor y contadores de rendimiento
El tiempo de ejecución de actores confiable emite los siguientes eventos relacionados con [los métodos de actor](service-fabric-reliable-actors-introduction.md#actors).

|Nombre del evento|Id. de evento|Nivel|Palabra clave|Descripción|
|---|---|---|---|---|
|ActorMethodStart|7|Detallado|0 x 2|Tiempo de ejecución de actores se va a invocar un método de actor.|
|ActorMethodStop|8|Detallado|0 x 2|Un método de actor haya terminado de ejecutarse. Es decir, ha devuelto llamada asincrónica de tiempo de ejecución para el método de actor y la tarea devuelta por el método de actor ha terminado.|
|ActorMethodThrewException|9|Advertencia|0 x 3|Se produce una excepción durante la ejecución de un método de actor durante la llamada asincrónica de tiempo de ejecución para el método de actor o durante la ejecución de la tarea devuelto por el método de actor. Este evento indica a algún tipo de error en el código de actor que necesita investigaciones.|

El tiempo de ejecución de actores confiable publica los siguientes contadores de rendimiento relacionados con la ejecución de métodos de actor.

|Nombre de categoría|Nombre de contador|Descripción|
|---|---|---|
|Método de Actor tela de servicio|Las llamadas por segundo|Número de veces que se invoca el método de servicio de actor por segundo|
|Método de Actor tela de servicio|Promedio milisegundos por invocación de funciones|Tiempo necesario para ejecutar el método de servicio de actor en milisegundos.|
|Método de Actor tela de servicio|Excepciones iniciadas por segundo|Número de veces que el actor servicio método produjo una excepción por segundo|

### <a name="concurrency-events-and-performance-counters"></a>Eventos de simultaneidad y contadores de rendimiento
El tiempo de ejecución de actores confiable emite los siguientes eventos relacionados con la [simultaneidad](service-fabric-reliable-actors-introduction.md#concurrency).

|Nombre del evento|Id. de evento|Nivel|Palabra clave|Descripción|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Detallado|0 x 8|Este evento se escribe al comienzo de cada nuevo activar un actor. Contiene el número de pendientes llamadas actor que esperan para adquirir el bloqueo por actor que exige simultaneidad basada en Activar.|

El tiempo de ejecución de actores confiable publica los siguientes contadores de rendimiento relacionados con la simultaneidad.

|Nombre de categoría|Nombre de contador|Descripción|
|---|---|---|
|Servicio tela Actor|# llamadas de actor esperando el bloqueo de actor|Número de pendientes llamadas actor esperando para adquirir el bloqueo por actor que exige simultaneidad basada en Activar|
|Servicio tela Actor|Espere promedio milisegundos por bloquear|Tiempo necesario (en milisegundos) para adquirir el bloqueo por actor que exige simultaneidad basada en Activar|
|Servicio tela Actor|Bloqueo de actor milisegundos promedio|Tiempo (en milisegundos) que se mantiene el bloqueo por actor|

### <a name="actor-state-management-events-and-performance-counters"></a>Contadores de rendimiento y eventos de administración de estado de actor
El tiempo de ejecución de actores confiable emite los siguientes eventos relacionados con la [administración de estado de actor](service-fabric-reliable-actors-state-management.md).

|Nombre del evento|Id. de evento|Nivel|Palabra clave|Descripción|
|---|---|---|---|---|
|ActorSaveStateStart|10|Detallado|0 x 4|Tiempo de ejecución de actores va a guardar el estado de actor.|
|ActorSaveStateStop|11|Detallado|0 x 4|Tiempo de ejecución de actores ha terminado de guardar el estado de actor.|

El tiempo de ejecución de actores confiable publica los siguientes contadores de rendimiento relacionados con la administración de estado de actor.

|Nombre de categoría|Nombre de contador|Descripción|
|---|---|---|
|Servicio tela Actor|Promedio milisegundos por guardar la operación de estado|Tiempo necesario para guardar el estado de actor en milisegundos.|
|Servicio tela Actor|Promedio milisegundos por operación de estado de carga|Tiempo necesario para cargar el estado de actor en milisegundos.|

### <a name="events-related-to-actor-replicas"></a>Eventos relacionados con réplicas actor
El tiempo de ejecución de actores confiable emite los siguientes eventos relacionados con [réplicas actor](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Nombre del evento|Id. de evento|Nivel|Palabra clave|Descripción|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|Informativo|0 x 1|Réplica de actor cambian rol principal. Esto implica que los actores para esta partición se creará dentro de esta réplica.|
|ReplicaChangeRoleFromPrimary|2|Informativo|0 x 1|Réplica de actor cambiado rol a no principal. Esto significa que ya no se creará los actores para esta partición dentro de esta réplica. No hay nuevas solicitudes se entregarán a actores ya creados de esta réplica. Se perderá los actores una vez completadas las solicitudes en curso.|

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Contadores de rendimiento y eventos de activación y desactivación de actor
El tiempo de ejecución de actores confiable emite los siguientes eventos relacionados con la [desactivación y activación de actor](service-fabric-reliable-actors-lifecycle.md).

|Nombre del evento|Id. de evento|Nivel|Palabra clave|Descripción|
|---|---|---|---|---|
|ActorActivated|5|Informativo|0 x 1|Se ha activado un actor.|
|ActorDeactivated|6|Informativo|0 x 1|Un actor se ha desactivado.|

El tiempo de ejecución de actores confiable publica los siguientes contadores de rendimiento relacionados con la desactivación y activación de actor.

|Nombre de categoría|Nombre de contador|Descripción|
|---|---|---|
|Servicio tela Actor|Calcular el promedio de milisegundos OnActivateAsync|Tiempo necesario para ejecutar el método OnActivateAsync en milisegundos.|

### <a name="actor-request-processing-performance-counters"></a>Contadores de rendimiento de procesamiento de la solicitud de actor
Cuando un cliente invoca un método a través de un objeto de proxy de actor, da como resultado un mensaje de solicitud que se envían por la red con el servicio de actor. El servicio procesa el mensaje de convocatoria y envía una respuesta al cliente. El tiempo de ejecución de actores confiable publica los siguientes contadores de rendimiento relacionados con el procesamiento de la solicitud de actor.

|Nombre de categoría|Nombre de contador|Descripción|
|---|---|---|
|Servicio tela Actor|# de solicitudes pendientes|Número de solicitudes que se procesan en el servicio|
|Servicio tela Actor|Promedio milisegundos por solicitud|Tiempo necesario (en milisegundos) en el servicio para procesar una solicitud|
|Servicio tela Actor|Promedio milisegundos para la deserialización de solicitud|Tiempo necesario (en milisegundos) para deserializar el mensaje de solicitud de actor cuando sea recibida en el servicio|
|Servicio tela Actor|Promedio en milisegundos para serialización de respuesta|Tiempo necesario (en milisegundos) para serializar el mensaje de respuesta de actor en el servicio antes de enviar la respuesta al cliente|

## <a name="next-steps"></a>Pasos siguientes
 - [¿Cómo actores fiable usa la plataforma de tela de servicio](service-fabric-reliable-actors-platform.md)
 - [Documentación de referencia de actor API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de ejemplo](https://github.com/Azure/servicefabric-samples)
