<properties 
    pageTitle="Cómo solucionar problemas de Azure Redis caché | Microsoft Azure" 
    description="Obtenga información sobre cómo resolver problemas comunes con Azure Redis caché." 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-troubleshoot-azure-redis-cache"></a>Cómo solucionar problemas de Azure Redis caché

Este artículo proporcionan pautas para solucionar problemas de las siguientes categorías de Azure Redis caché problemas.

-   [Solución de problemas de lado cliente](#client-side-troubleshooting) - esta sección proporciona instrucciones sobre cómo identificar y resolver problemas causados por la aplicación que se conecta a Azure Redis caché.
-   [Solución de problemas de lado de servidor](#server-side-troubleshooting) - esta sección proporciona instrucciones sobre cómo identificar y resolver problemas producidos en el servidor caché Redis de Azure.
-   [Excepciones de tiempo de espera de StackExchange.Redis](#stackexchangeredis-timeout-exceptions) - esta sección proporciona información sobre solución de problemas al usar al cliente de StackExchange.Redis.


>[AZURE.NOTE] Algunos de los pasos de solución de problemas en esta guía incluyen instrucciones para ejecutar comandos Redis y supervisar varias métricas de rendimiento. Para obtener más información, vea los artículos de la sección [información adicional](#additional-information) .

## <a name="client-side-troubleshooting"></a>Solución de problemas del lado de cliente


Esta sección describe los problemas que se producen debido a una condición en la aplicación cliente.

-   [Presión de memoria en el cliente](#memory-pressure-on-the-client)
-   [Ráfaga de tráfico](#burst-of-traffic)
-   [Uso de CPU del cliente alta](#high-client-cpu-usage)
-   [Ha superado el ancho de banda del lado cliente](#client-side-bandwidth-exceeded)
-   [Tamaño de la convocatoria y respuesta grande](#large-requestresponse-size)
-   [¿Qué ha ocurrido con mis datos en Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Presión de memoria en el cliente

#### <a name="problem"></a>Problema

Presión de memoria en el equipo cliente se lleva a todos los tipos de problemas de rendimiento que se pueden retrasar el procesamiento de datos que ha enviado la instancia Redis sin demora. Cuando llega la presión de memoria, el sistema tiene normalmente a los datos de la página de la memoria física de memoria virtual que se encuentra en el disco. Esta *página con errores* hace que el sistema de disminuir considerablemente.

#### <a name="measurement"></a>Medida 

1.  Supervisar el uso de memoria en el equipo para asegurarse de que no supere la memoria disponible. 
2.  Supervisar el `Page Faults/Sec` contador de rendimiento. La mayoría de los sistemas se tiene algunos errores de página incluso durante el funcionamiento normal, así que busque picos de este contador de rendimiento de errores de página que se corresponden con los tiempos de espera.

#### <a name="resolution"></a>Resolución

Actualizar al cliente a un cliente de mayor tamaño de memoria virtual con más memoria o profundizar en los modelos de uso de la memoria para reducir la memoria consuption.


### <a name="burst-of-traffic"></a>Ráfaga de tráfico

#### <a name="problem"></a>Problema

Momentos de mucho tráfico combinan con deficiente `ThreadPool` configuración puede provocar retrasos en el procesamiento de los datos ya enviadas por el servidor Redis pero aún no se ha utilizado en el cliente.

#### <a name="measurement"></a>Medida 

Supervisar cómo su `ThreadPool` estadísticas cambian con el tiempo mediante código [como este](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). También puede mirar el `TimeoutException` mensaje de StackExchange.Redis. Aquí tenemos un ejemplo:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

En el mensaje anterior, hay varios problemas que son interesantes:

 1. Tenga en cuenta que en el `IOCP` sección y la `WORKER` sección tiene un `Busy` valor mayor que el `Min` valor. Esto significa que su `ThreadPool` configuración necesita ajustar.
 2. También puede ver `in: 64221`. Esto indica que 64211 bytes recibidos en la capa de sockets núcleo pero aún no ha leído todavía por la aplicación (por ejemplo, StackExchange.Redis). Normalmente, esto significa que su aplicación no lee datos desde la red tan pronto como el servidor es enviárselo.

#### <a name="resolution"></a>Resolución

Configurar la [Configuración de grupo de subprocesos](https://gist.github.com/JonCole/e65411214030f0d823cb) para asegurarse de que su grupo de subprocesos se escala rápidamente en escenarios de ráfaga.


### <a name="high-client-cpu-usage"></a>Uso de CPU del cliente alta

#### <a name="problem"></a>Problema

Uso de CPU alto en el cliente es una indicación de que el sistema no puede mantener el trabajo que se ha solicitado. Esto significa que el cliente no puede procesar una respuesta de Redis puntualmente aunque Redis envió la respuesta muy rápidamente.

#### <a name="measurement"></a>Medida

Supervisar el uso de CPU de gran sistema a través del Portal de Azure o a través del contador de rendimiento asociado. Tenga cuidado de no supervisar CPU del *proceso* como un único proceso puede tener el uso de CPU bajo al mismo tiempo que la CPU general del sistema puede ser alto. Vea los picos de uso de CPU que se corresponden con los tiempos de espera. Como resultado de la CPU alta, también puede ver alto `in: XXX` valores en `TimeoutException` mensajes de error, como se describe en la sección de [ráfaga de tráfico](#burst-of-traffic) .

>[AZURE.NOTE] StackExchange.Redis 1.1.603 y versiones posteriores incluyen la `local-cpu` métrica en `TimeoutException` mensajes de error. Asegúrese de que está utilizando la versión más reciente del [paquete StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Hay errores constantemente se fija en el código para que sea más rigurosa a los tiempos de espera para que es importante tener la última versión.

#### <a name="resolution"></a>Resolución

Actualizar a un tamaño VM con más capacidad de CPU o investigar la causa picos de CPU. 



### <a name="client-side-bandwidth-exceeded"></a>Ha superado el ancho de banda del lado cliente

#### <a name="problem"></a>Problema

Equipos cliente de tamaño diferentes tienen limitaciones en cuánto ancho de banda tiene disponible. Si el cliente supera el ancho de banda disponible, a continuación, datos no procesará del cliente tan pronto como el servidor envía. Esto puede provocar tiempos de espera.

#### <a name="measurement"></a>Medida

Supervisar cómo cambiar el uso de ancho de banda con el tiempo mediante código [como este](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Tenga en cuenta que este código no funcionen correctamente en algunos entornos con permisos restringidos (por ejemplo, sitios web Azure).

#### <a name="resolution"></a>Resolución 

Aumentar el tamaño del cliente VM o reducir el consumo de ancho de banda de red.


### <a name="large-requestresponse-size"></a>Tamaño de la convocatoria y respuesta grande

#### <a name="problem"></a>Problema

Una solicitud y la respuesta grande pueden provocar los tiempos de espera. Por ejemplo, supongamos que el valor de tiempo de espera configurado en el cliente es 1 segundo. La aplicación solicita dos claves (por ejemplo, 'A' y 'B') al mismo tiempo (con la misma conexión de red física). La mayoría de los clientes de soporte técnico "Canalización" de solicitudes, que ambas solicitudes 'A' y 'B' se envían en la conexión al servidor uno tras otro sin tener que esperar para las respuestas. El servidor enviará las respuestas en el mismo orden. Si es grande respuesta 'A' suficientemente se pueden comer la mayor parte del tiempo de espera para las solicitudes subsiguientes. 

En el ejemplo siguiente se muestra este escenario. En este escenario, se envía rápidamente solicitud 'A' y 'B', el servidor empieza a enviar respuestas 'A' y 'B' rápidamente, pero debido a los tiempos de transferencia de datos, «B» se quedan atascado detrás de los demás solicitud y tiempo de espera aunque el servidor respondió rápidamente.

  	|-------- 1 Second Timeout (A)----------|
  	|-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Medida

Esto es difícil medir. Básicamente, debe preparar el código de cliente para realizar un seguimiento de las respuestas y solicitudes grandes. 

#### <a name="resolution"></a>Resolución

1.  Redis está optimizado para una gran cantidad de valores pequeños, en lugar de algunos valores de gran tamaño. La mejor solución es dividir los datos en los valores más pequeños relacionados. Consulte la [¿Qué es el intervalo de tamaño de valor ideal para redis? Es de 100KB demasiado grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) entrada de detalles de por qué se recomiendan los valores más pequeños.
2.  Aumentar el tamaño de la máquina virtual (para cliente y servidor de caché Redis), para obtener más capacidades de ancho de banda, reducir los tiempos de transferencia de datos para las respuestas más grandes. Tenga en cuenta que recibe más ancho de banda sólo del servidor o solo en el cliente puede no ser suficiente. Medir el uso de ancho de banda y compárelo con las capacidades del tamaño de la máquina virtual que tiene actualmente.
3.  Aumentar el número de `ConnectionMultiplexer` objetos solicitudes de turnos y usar sobre conexiones diferentes.


### <a name="what-happened-to-my-data-in-redis"></a>¿Qué ha ocurrido con mis datos en Redis?

#### <a name="problem"></a>Problema

Esperaba para algunos datos de la instancia de Azure Redis caché pero no parecen estar ahí.

##### <a name="resolution"></a>Resolución

Consulte [¿Qué ha ocurrido con mis datos en Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para posibles causas y resoluciones.


## <a name="server-side-troubleshooting"></a>Solución de problemas de lado servidor

Esta sección describe los problemas que se producen debido a una condición en el servidor de caché.

-   [Presión de memoria en el servidor](#memory-pressure-on-the-server)
-   [Uso de CPU alta / servidor cargar](#high-cpu-usage-server-load)
-   [Excedido el ancho de banda del lado servidor](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Presión de memoria en el servidor

#### <a name="problem"></a>Problema

Para todos los tipos de problemas de rendimiento que se pueden retrasar el procesamiento de solicitudes de clientes potenciales presión de memoria en el servidor. Cuando llega la presión de memoria, el sistema tiene normalmente a los datos de la página de la memoria física de memoria virtual que se encuentra en el disco. Esta *página con errores* hace que el sistema de disminuir considerablemente. Hay varias causas posibles de este presión de memoria: 

1.  La caché de la capacidad total que haya rellenado con datos. 
2.  Redis está viendo fragmentación de memoria alta - suele deberse a almacenar grandes objetos (Redis está optimizada para una pequeña objetos, consulte la [¿Qué es el intervalo de tamaño de valor ideal para redis? Es de 100KB demasiado grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) entrada para obtener información detallada). 

#### <a name="measurement"></a>Medida

Redis expone dos métricas que pueden ayudarle a identificar este problema. La primera es `used_memory` y la otra es `used_memory_rss`. [Estas métricas](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) están disponibles en el Portal de Azure o mediante el comando [Redis información](http://redis.io/commands/info) .

#### <a name="resolution"></a>Resolución

Hay varios cambios posibles que puede hacer para ayudar a mantener el uso de memoria correcto:

1. [Configurar una directiva de memoria](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) y establecer los períodos de expiración de las claves. Tenga en cuenta que esto puede no ser suficiente si tiene fragmentación.
2. [Configurar un valor reservados maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) que es suficientemente grande para compensa la fragmentación de memoria.
3. Dividir los objetos de caché grandes en pequeños objetos relacionados.
4. [Escala](cache-how-to-scale.md) a un tamaño de caché más grande.
5. Si está utilizando una [caché premium con clúster Redis habilitado](cache-how-to-premium-clustering.md) puede [aumentar el número de shards](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Uso de CPU alta / servidor cargar

#### <a name="problem"></a>Problema

Uso de CPU elevado significa que el cliente puede fallar procesar una respuesta de Redis puntualmente aunque Redis envió la respuesta muy rápidamente.

#### <a name="measurement"></a>Medida

Supervisar el uso de CPU de gran sistema a través del Portal de Azure o a través del contador de rendimiento asociado. Tenga cuidado de no supervisar CPU del *proceso* como un único proceso puede tener el uso de CPU bajo al mismo tiempo que la CPU general del sistema puede ser alto. Vea los picos de uso de CPU que se corresponden con los tiempos de espera.

#### <a name="resolution"></a>Resolución

[Escala](cache-how-to-scale.md) a una caché mayor nivel con más capacidad de CPU o investigar la causa picos de CPU. 

### <a name="server-side-bandwidth-exceeded"></a>Excedido el ancho de banda del lado servidor

#### <a name="problem"></a>Problema

Instancias de caché de tamaño diferente tienen limitaciones en cuánto ancho de banda tiene disponible. Si el servidor supera el ancho de banda disponible, no se enviarán datos al cliente como rápidamente. Esto puede provocar tiempos de espera.

#### <a name="measurement"></a>Medida

Puede supervisar la `Cache Read` lee métrica, que es la cantidad de datos de la caché en Megabytes por segundo (MB/s) durante el intervalo informes especificado. Este valor corresponde al ancho de banda de red usado por esta caché. Si desea configurar las alertas de los límites de ancho de banda de red de lado de servidor, puede crearlos mediante esta `Cache Read` contador. Comparar los lecturas con los valores de [esta tabla](cache-faq.md#cache-performance) para los límites de ancho de banda observado para caché distintos niveles y tamaños de precios.

#### <a name="resolution"></a>Resolución

Si es coherente cerca de ancho de banda máximo observado para el tamaño de caché y de nivel de precios, considere la posibilidad de [ajuste de escala](cache-how-to-scale.md) a un nivel de precios o tamaño con mayor ancho de banda de red, con los valores de [esta tabla](cache-faq.md#cache-performance) como guía.


## <a name="stackexchangeredis-timeout-exceptions"></a>Excepciones de tiempo de espera de StackExchange.Redis

StackExchange.Redis utiliza una configuración con nombre `synctimeout` para operaciones sincrónicas que tiene un valor predeterminado de 1000 ms. Si no se completa una llamada sincrónica en el tiempo estipulado, el cliente de StackExchange.Redis produce un error de tiempo de espera similar al siguiente ejemplo.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Este mensaje de error contiene métricas que pueden ayudar a apunte a la causa y posible solución del problema. En la tabla siguiente contiene información detallada acerca de las métricas del mensaje de error.

| Métrica de mensaje de error | Detalles                                                                                                                                                                                                                                          |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| inst       | En el último intervalo de tiempo: se han emitido comandos 0                                                                                                                                                                                              |
| jefe        | El Administrador de socket está realizando `socket.select` lo que significa que le pregunta si el sistema operativo para indicar un socket que tiene algo hacer; básicamente: el lector no está leyendo activa de la red porque no piense hay que hacer nada |
| cola      | Hay 73 total de operaciones en curso                                                                                                                                                                                                        |
| qu         | 6 de las operaciones en curso en la cola no enviada y aún no se han escrito a la red de salida                                                                                                                                                           |
| QS         | 67 de operaciones de en curso se han enviado al servidor pero aún no está disponible una respuesta. La respuesta podría ser `Not yet sent by the server` o`sent by the server but not yet processed by the client.`                                                   |
| QC         | ha visto respuestas 0 de las operaciones en curso, pero aún no se han marcado como completado debido a que espera en el bucle de finalización                                                                                                                                      |
| wR         | Hay un escritor activo (es decir, que no se omiten las solicitudes no enviadas 6) bytes/activewriters                                                                                                                                                   |
| en         | No hay ningún lectores activas y cero bytes están disponibles para leer en la NIC bytes/activereaders                                                                                                                                               |


### <a name="steps-to-investigate"></a>Pasos para investigar

1. Como práctica recomendada Asegúrese de que está utilizando el modelo siguiente para conectarse al utilizar al cliente de StackExchange.Redis.


        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


    Para obtener más información, vea [conectarse a la caché mediante StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

2. Asegúrese de que la memoria caché Redis de Azure y la aplicación cliente están en la misma región en Azure. Por ejemplo, podría estar obteniendo los tiempos de espera cuando la memoria caché está en Estados Unidos oriental pero el cliente se encuentra en Estados Unidos oeste y no se completa la solicitud en la `synctimeout` intervalo o podría estar obteniendo tiempos de espera de depuración de su equipo de desarrollo local. 

    Es muy recomendable tener la memoria caché y en el cliente en la misma región de Azure. Si tiene un escenario que incluye las llamadas de región cruzada, debe establecer el `synctimeout` intervalo en un valor mayor que el intervalo predeterminado de 1000 ms incluyendo un `synctimeout` propiedad en la cadena de conexión. En el ejemplo siguiente se muestra un fragmento de cadena de conexión de caché StackExchange.Redis con un `synctimeout` de ms 2000.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...

3. Asegúrese de que está utilizando la versión más reciente del [paquete StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Hay errores constantemente se fija en el código para que sea más rigurosa a los tiempos de espera para que es importante tener la última versión.

4. Si hay solicitudes obtener enlazados por las limitaciones de ancho de banda en el servidor o cliente, tardará más tiempo para completar y causar tiempos de espera. Para ver si el tiempo de espera es por ancho de banda de red en el servidor, vea [superado el ancho de banda del lado servidor](#server-side-bandwidth-exceeded). Para ver si el tiempo de espera es por ancho de banda de red de cliente, vea [superado el ancho de banda del lado cliente](#client-side-bandwidth-exceeded).

6. ¿Se siente CPU enlazados en el servidor o en el cliente?
    -   Compruebe si está recibiendo enlazado por CPU en el cliente que puede hacer que no se puede procesar dentro de la solicitud del `synctimeout` intervalo, lo que provoca un tiempo de espera. Mover a un tamaño de cliente más grande o distribuir la carga puede ayudar a controlar esto. 
    -   Comprobar si se está CPU enlazado en el servidor de supervisión la `CPU` [métrica de rendimiento de la memoria caché](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Peticiones mientras Redis CPU es pueden provocar las solicitudes de tiempo de espera. Para solucionar esto puede distribuir la carga en varios shards en caché premium o actualizar a una mayor tamaño o el nivel de precios. Para obtener más información, vea [Supere de ancho de banda de lado de servidor](#server-side-bandwidth-exceeded).

7. ¿Hay comandos tarda mucho tiempo para procesar en el servidor? Comandos que se tardan mucho tiempo en procesarse en el servidor redis largas en ejecución puede provocar los tiempos de espera. Algunos ejemplos de tiempo de ejecución de comandos son `mget` con grandes cantidades de claves, `keys *` o mal escrito secuencias de comandos de lua. Puede conectarse a su instancia de Azure Redis caché mediante el cliente cli redis o utilizar la [Consola Redis](cache-configure.md#redis-console) y ejecute el comando [SlowLog](http://redis.io/commands/slowlog) para ver si hay solicitudes tardando más de lo esperado. Servidor Redis y StackExchange.Redis están optimizados para muchas solicitudes pequeñas en lugar de menos solicitudes de gran tamaño. Dividir los datos en fragmentos más pequeños puede mejorar cosas aquí. 

    Para obtener información sobre cómo conectar al extremo SSL de caché de Azure Redis mediante cli redis y stunnel, consulte la entrada de blog de [Anuncio de proveedor de estado de sesión ASP.NET Redis versión de vista previa](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) . Para obtener más información, consulte [SlowLog](http://redis.io/commands/slowlog).

8. Carga del servidor alta Redis puede provocar los tiempos de espera. Puede supervisar la carga del servidor de supervisión la `Redis Server Load` [métrica de rendimiento de la memoria caché](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Carga del servidor de 100 (valor máximo) significa que el servidor redis ha sido procesar las solicitudes de ocupado, sin tiempo de inactividad. Para ver si ciertas solicitudes ocupan toda la capacidad de servidor, ejecute el comando SlowLog, como se describe en el párrafo anterior. Para obtener más información, vea [uso de CPU alta / servidor cargar](#high-cpu-usage-server-load).

9. ¿Hubo cualquier otro evento en el cliente que causaron una señalización visual de red? ¿Compruebe en el cliente (web, rol de trabajo o una VM Iaas) si se ha producido un evento como el número de instancias de cliente de ajuste de escala hacia arriba o hacia abajo o implementar una nueva versión del cliente o ajustar está habilitado? En nuestra prueba que hemos encontrado que puede provocar Autoescala o escalar barras ascendentes o descendentes se puede perder la conectividad de red de salida durante unos segundos. Código de StackExchange.Redis es resistente a los eventos y se volverá a conectar. Durante este período de conexión volver a las solicitudes en la cola pueden tiempo de espera.

10. ¿Ha sido una solicitud grande anterior varias solicitudes pequeñas en la caché de Redis agotado? El parámetro `qs` el error mensaje le indica que el número de solicitudes enviado desde el cliente en el servidor, pero aún no ha procesado una respuesta. Este valor puede mantener aumentando porque StackExchange.Redis utiliza una única conexión de TCP y solo se puede leer una respuesta a la vez. Aunque la primera operación ha agotado el tiempo de espera, no impide que los datos enviados desde el servidor y otras solicitudes se bloquean hasta que termine, provocando tiempos de espera. Es una solución minimizar las posibilidades de tiempos de asegurarse de que la memoria caché es lo suficientemente grande para la carga de trabajo y dividiendo valores grandes en partes más pequeñas. Otra solución posible es utilizar un grupo de `ConnectionMultiplexer` los objetos de cliente y a continuación, elija menos carga `ConnectionMultiplexer` al enviar una solicitud de nueva. Esto debe evitar que el tiempo de espera de una sola causen otras solicitudes que también en el tiempo de espera.

11. Si está utilizando `RedisSessionStateprovider`, asegúrese de que ha configurado el tiempo de espera de reintento correctamente. `retrytimeoutInMilliseconds`debe ser mayor que `operationTimeoutinMilliseonds`, en caso contrario, no se producirán reintentos. En el ejemplo siguiente `retrytimeoutInMilliseconds` se establece en 3000. Para obtener más información, vea [Proveedor de estado de sesión de ASP.NET para Azure Redis caché](cache-aspnet-session-state-provider.md) y [cómo usar los parámetros de configuración del proveedor de estado de sesión y proveedor de caché de resultados](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).


    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


12. Compruebe el uso de memoria en el servidor de Azure Redis caché mediante la [supervisión de](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` y `Used Memory`. Si una directiva de expulsión es en su lugar, Redis comienza expulsar teclas cuándo `Used_Memory` alcance el tamaño de caché. Lo ideal es que, `Used Memory RSS` solo debe ser ligeramente por encima del `Used memory`. Una gran diferencia significa que no hay fragmentación de la memoria (interna o externa. Cuando `Used Memory RSS` es menor que `Used Memory`, significa que se ha intercambiado parte de la memoria caché por el sistema operativo. En este caso, que puede esperar algunos latencia significativa. Ya no tiene control sobre cómo se asignan sus asignaciones a páginas de memoria, altos Redis `Used Memory RSS` a menudo es el resultado de un aumento en el uso de memoria. Cuando Redis libera memoria, la memoria se le envía a la asignación y la asignación puede o no puede proporcionar la memoria volver al sistema. Puede haber una discrepancia entre la `Used Memory` valor y consumo de memoria notificado por el sistema operativo. Puede ser debido al hecho de memoria se ha usado y publicado Redis, pero no dada al sistema. Puede realizar los pasos siguientes para ayudar a mitigar los problemas de memoria.
    -   Actualizar la memoria caché a un tamaño más grande para que no se ejecuta frente a las limitaciones de memoria en el sistema.
    -   Establecer los períodos de expiración de las claves para que los valores anteriores se hayan retirado de manera proactiva.
    -   Supervisar el la `used_memory_rss` métrica en caché. Cuando este valor aproxima el tamaño de la caché, es probable que empezar a ver problemas de rendimiento. Distribuir los datos en varias shards si está usando una caché premium o actualizar a un tamaño de caché más grande.

    Para obtener más información, vea [Presión de memoria en el servidor](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Información adicional

-   [¿Qué oferta de caché Redis y tamaño debo usar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
-   [¿Cómo puedo evaluar y probar el rendimiento de la memoria caché?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [¿Cómo puedo ejecutar comandos Redis?](cache-faq.md#how-can-i-run-redis-commands)
-   [Cómo supervisar Azure Redis caché](cache-how-to-monitor.md)