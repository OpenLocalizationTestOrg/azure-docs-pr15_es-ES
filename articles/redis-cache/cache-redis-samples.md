<properties 
    pageTitle="Ejemplos de caché Redis Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo usar la memoria caché Redis de Azure" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-samples"></a>Ejemplos de caché Redis Azure 

Este tema proporciona una lista de ejemplos de Azure Redis caché, que tratan escenarios como conectarse a una caché, leer y escribir datos a y desde una caché y el uso de los proveedores de caché de ASP.NET Redis. Algunos ejemplos son proyectos que se pueden descargar y algunos proporcionan instrucciones paso a paso e incluyen fragmentos de código, pero no se vincula a un proyecto descargable.

## <a name="hello-world-samples"></a>Ejemplos del mundo Hola a todos

Los ejemplos de esta sección muestran los aspectos básicos de conectarse a una instancia de Azure Redis caché y leer y escribir datos en la caché con una amplia variedad de idiomas y Redis clientes.

Ejemplo de [Hola a todos](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) , muestra cómo realizar distintas operaciones de caché mediante el cliente de .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

Este ejemplo muestra cómo:

-   Usar distintas opciones de conexión
-   Leer y escribir objetos de la caché mediante operaciones sincrónicas y asincrónicas
-   Usar los comandos de Redis MGET/MSET para devolver valores de las claves especificadas
-   Realizar operaciones de transacciones Redis
-   Trabajar con listas Redis y conjuntos ordenados
-   Almacenar objetos de .NET mediante JsonConvert serializadores
-   Usar Redis conjuntos para implementar el etiquetado
-   Trabajar con clúster Redis

Para obtener más información, consulte la documentación de [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) en github y obtenga más escenarios de uso en las pruebas de unidad [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) .

[Cómo usar Azure Redis caché con Python](cache-python-get-started.md) muestra cómo empezar a trabajar con Azure Redis caché mediante Python y el cliente de [Copiar redis](https://github.com/andymccurdy/redis-py) .

[Trabajar con objetos .NET en la caché](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) se muestra una manera de serializar objetos .NET para que pueda escribirles y leerlos desde una instancia de Azure Redis caché. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Usar caché Redis como un plano de escalado de ASP.NET SignalR

El ejemplo de [Usar caché Redis como una escala a placa para ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) muestra cómo puede usar la caché de Azure Redis como una placa SignalR. Para obtener más información acerca de plano posterior, vea [SignalR escala con Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Ejemplo de consulta de cliente de caché de Redis

Este ejemplo muestra rendimiento compara entre acceso a los datos de la memoria caché y acceso a los datos de almacenamiento de persistencia. En este ejemplo tiene dos proyectos.

-   [¿Cómo Redis caché puede mejorar el rendimiento en caché de datos de demostración](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-   [Inicialización de la base de datos y la caché de la demostración](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Estado de la sesión de ASP.NET y la caché de resultados

El ejemplo [Usar Azure Redis caché para almacenar ASP.NET SessionState y OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) se muestra cómo utilizar Azure Redis caché para almacenar sesión ASP.NET y con los proveedores de SessionState y OutputCache para Redis de caché de resultados.

## <a name="manage-azure-redis-cache-with-maml"></a>Administrar la memoria caché Redis Azure con MAML

El ejemplo [Administrar Azure Redis caché con las bibliotecas de administración de Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) se muestra cómo puede usar las bibliotecas de administración de Azure para administrar - (crear / actualizar / eliminar) la memoria caché. 

## <a name="custom-monitoring-sample"></a>Personalizar supervisión de ejemplo

El ejemplo de [control de acceso Redis caché de datos](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) muestra cómo tener acceso a datos de supervisión de la memoria caché Redis Azure fuera del Portal de Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Un duplicado de estilo de Twitter escrita con PHP y Redis

El ejemplo de [Retwis](https://github.com/SyntaxC4-MSFT/retwis) es la Redis Hola a todos. Es una mínima clonar de redes sociales de Twitter estilo escrita mediante Redis y PHP con el cliente de [Predis](https://github.com/nrk/predis) . El código fuente está diseñado para ser muy sencillos y al mismo tiempo para mostrar distintos Redis estructuras de datos.

## <a name="bandwidth-monitor"></a>Monitor de ancho de banda

El ejemplo [monitor de ancho de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) le permite supervisar el ancho de banda utilizado en el cliente. Para medir el ancho de banda, ejecute el ejemplo en el equipo cliente de caché, realizar llamadas a la memoria caché y observe el ancho de banda notificado por el ejemplo de monitor de ancho de banda.
