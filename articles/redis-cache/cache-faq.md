<properties 
    pageTitle="Preguntas más frecuentes de caché Redis Azure | Microsoft Azure" 
    description="Obtenga información sobre las respuestas a preguntas habituales, patrones y prácticas recomendadas para Azure Redis caché" 
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
    ms.date="10/18/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-faq"></a>Caché de Azure Redis preguntas más frecuentes

Obtenga información sobre las respuestas a preguntas habituales, patrones y prácticas recomendadas para Azure Redis caché. 


## <a name="what-if-my-question-isnt-answered-here"></a>¿Qué sucede si no se responde aquí mi pregunta?

Si su pregunta no aparece aquí, infórmenos y le ayudaremos a encontrar una respuesta.

-   Puede publicar una pregunta en el [subproceso Disqus](#comments) al final de estas preguntas más frecuentes y comuníquese con el equipo de caché de Azure y otros miembros de la Comunidad acerca de este artículo.
-   Para llegar a una audiencia más amplia, puede publicar una pregunta en el [Foro de MSDN de caché de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) y comuníquese con el equipo de caché de Azure y otros miembros de la Comunidad.
-   Si desea realizar una solicitud de característica, puede enviar sus solicitudes e ideas a [Azure Redis caché usuario voz](https://feedback.azure.com/forums/169382-cache).
-   También puede enviar un correo electrónico que nos en [Azure caché externo comentarios](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Aspectos básicos de Azure Redis caché

Las preguntas más frecuentes en esta sección abarcan algunos de los conceptos básicos de Azure Redis caché.

-    [¿Qué es la caché de Azure Redis?](#what-is-azure-redis-cache)
-    [¿Cómo puedo empezar con Azure Redis caché?](#how-can-i-get-started-with-azure-redis-cache)

Las siguientes preguntas y respuestas cubren conceptos básicos y preguntas sobre Azure Redis caché y se responden en una de las otras secciones de preguntas más frecuentes.

-   [¿Qué oferta de caché Redis y tamaño debo usar?](#what-redis-cache-offering-and-size-should-i-use)
-   [¿Qué clientes de caché Redis se debe usar?](#what-redis-cache-clients-can-i-use)
-   [¿Hay un emulador para Azure Redis caché local?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [¿Cómo se puede supervisar el estado y el rendimiento de la memoria caché?](#how-do-i-monitor-the-health-and-performance-of-my-cache)



## <a name="planning-faqs"></a>Preguntas más frecuentes de planeación

-   [¿Qué oferta de caché Redis y tamaño debo usar?](#what-redis-cache-offering-and-size-should-i-use)
-   [Rendimiento de la caché Redis de Azure](#azure-redis-cache-performance)
-   [¿En qué región debo buscar mi caché?](#in-what-region-should-i-locate-my-cache)
-   [¿Cómo me factura caché Redis de Azure?](#how-am-i-billed-for-azure-redis-cache)
-   [¿Puedo usar Azure Redis caché con la nube de la administración pública de Azure o China Azure?](#can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud)



## <a name="development-faqs"></a>Preguntas más frecuentes de desarrollo

-   [¿Qué hacer las opciones de configuración de StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
-   [¿Qué clientes de caché Redis se debe usar?](#what-redis-cache-clients-can-i-use)
-   [¿Hay un emulador para Azure Redis caché local?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [¿Cómo puedo ejecutar comandos Redis?](#how-can-i-run-redis-commands)
-   [¿Por qué no Azure Redis caché tiene una referencia de biblioteca de clases MSDN como algunos de los servicios de Azure?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
-   [¿Usar caché de Azure Redis como caché PHP sesión?](#can-i-use-azure-redis-cache-as-a-php-session-cache)


## <a name="security-faqs"></a>Preguntas más frecuentes de seguridad

-   [¿Cuándo debo habilitar el puerto no SSL para conectarse a Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)


## <a name="production-faqs"></a>Preguntas más frecuentes de producción

-   [¿Cuáles son algunas prácticas recomendadas de producción?](#what-are-some-production-best-practices)
-   [¿Cuáles son algunas de las consideraciones al usar comandos Redis comunes?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
-   [¿Cómo puedo evaluar y probar el rendimiento de la memoria caché?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Detalles importantes sobre crecimiento del grupo de subprocesos](#important-details-about-threadpool-growth)
-   [Habilitar catálogo global del servidor obtener el mejor rendimiento en el cliente cuando se usa StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)


## <a name="monitoring-and-troubleshooting-faqs"></a>Supervisión y solución de problemas de preguntas más frecuentes

Las preguntas más frecuentes en esta sección cubren supervisión y solución de problemas de preguntas comunes. Para obtener más información sobre la supervisión y solución de problemas de las instancias de Azure Redis caché, vea [cómo supervisar Azure Redis caché](cache-how-to-monitor.md) y [cómo solucionar problemas de Azure Redis caché](cache-how-to-troubleshoot.md).

-   [¿Cómo se puede supervisar el estado y el rendimiento de la memoria caché?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-   [Caché diagnósticos almacenamiento configuración de mi cuenta ha cambiado, ¿qué ha ocurrido?](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-   [¿Por qué está habilitadas diagnósticos para algunos caché nuevas pero no otros?](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-   [¿Por qué estoy viendo los tiempos de espera?](#why-am-i-seeing-timeouts)
-   [¿Por qué mi cliente se ha desconectado de la memoria caché?](#why-was-my-client-disconnected-from-the-cache)


## <a name="prior-cache-offering-faqs"></a>Preguntas más frecuentes de oferta de caché anterior

-   [¿Qué ofertas de caché de Azure es la adecuada para mí?](#which-azure-cache-offering-is-right-for-me)


### <a name="what-is-azure-redis-cache"></a>¿Qué es la caché de Azure Redis?

Caché de Azure Redis se basa en el origen de abrir popular [Redis caché](http://redis.io). Le proporciona acceso a una segura y dedicada Redis caché, administrada por Microsoft y accesible desde cualquier aplicación de Azure. Para obtener información más detallada, consulte la página de producto de [Azure Redis caché](https://azure.microsoft.com/services/cache/) en Azure.com.


### <a name="how-can-i-get-started-with-azure-redis-cache"></a>¿Cómo puedo empezar con Azure Redis caché?

Existen varias formas de que pueda empezar con Azure Redis caché.

-    Puede desproteger uno de nuestros tutoriales disponibles para [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)y [Python](cache-python-get-started.md). 
-    Puede ver [cómo crear alto rendimiento aplicaciones usando Microsoft Azure Redis caché](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
-    Puede comprobar la documentación del cliente para los clientes que coincidan con el lenguaje de programación del proyecto para ver cómo utilizar Redis. Hay muchos clientes Redis que se pueden usar con Azure Redis caché. Para obtener una lista de clientes Redis, consulte [http://redis.io/clients](http://redis.io/clients).


Si todavía no tiene una cuenta de Azure, puede:

-    [Abrir una cuenta de Azure de forma gratuita](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Obtenga créditos que se pueden usar para probar los servicios de Azure pagados. Incluso después de que se usan los créditos hacia arriba, puede mantener la cuenta y usar las características y servicios de Azure gratuitos.
-    [Activar Visual Studio ventajas de suscriptor](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Su suscripción de MSDN le proporciona créditos cada mes que puede usar para los servicios de Azure pagados.


<a name="cache-size"></a>
### <a name="what-redis-cache-offering-and-size-should-i-use"></a>¿Qué oferta de caché Redis y tamaño debo usar?
Cada oferta de Azure Redis caché proporciona diferentes niveles de **tamaño**, **ancho de banda**, **alta disponibilidad**y opciones de **SLA** .

Los siguientes son consideraciones para elegir una oferta de caché.

-   **Memoria**: niveles estándar y básica ofrecen 250 MB: 53 GB. El nivel Premium ofrece hasta 530 GB con más disponibles [en la solicitud](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para obtener más información, vea [Precios de Azure Redis caché](https://azure.microsoft.com/pricing/details/cache/).
-   **Rendimiento de la red**: si tiene una carga de trabajo que requiere alto rendimiento el nivel Premium ofrece más ancho de banda con respecto al estándar o básico. También dentro de cada nivel de caché de mayor tamaño tienen más ancho de banda debido a la máquina virtual subyacente que hospeda la memoria caché. Consulte la [tabla siguiente](#cache-performance) para obtener más información.
-   **Rendimiento**: nivel Premium la ofrece el máximo rendimiento disponible. Si el servidor de caché o el cliente llega a los límites de ancho de banda, recibirá los tiempos de espera del cliente. Para obtener más información, vea la tabla siguiente.
-   **Alta disponibilidad/SLA**: Azure Redis caché garantiza que una caché estándar y Premium estará disponible al menos un 99,9% del tiempo. Para obtener más información acerca de nuestro SLA, consulte [Precios de Azure Redis caché](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). El SLA sólo cubre conectividad a los extremos de la memoria caché. El SLA no trata sobre la protección contra la pérdida de datos. Se recomienda usar la característica de persistencia Redis datos en el nivel Premium para aumentar la resistencia contra la pérdida de datos.
-   **Redis persistencia de los datos**: nivel Premium la le permite conservar los datos en caché en una cuenta de almacenamiento de Azure. En la memoria caché de básico/estándar todos los datos se almacena en la memoria. En caso de infraestructura subyacente problemas allí pueden ser posibles pérdidas de datos. Se recomienda usar la característica de persistencia Redis datos en el nivel Premium para aumentar la resistencia contra la pérdida de datos. Caché de Azure Redis ofrece opciones de RDB y AOF (próximamente) en la conservación Redis. Para obtener más información, consulte [cómo configurar persistencia para una caché de Redis Premium Azure](cache-how-to-premium-persistence.md).
-   **Redis clúster**: crear almacena mayor que 53 GB o a los datos en varios nodos Redis fragmentar, puede usar Redis clústeres, que está disponible en el nivel Premium. Cada nodo consta de un par de caché de primario/réplica de alta disponibilidad. Para obtener más información, consulte [cómo configurar clústeres para una caché de Redis Premium Azure](cache-how-to-premium-clustering.md).
-   **Seguridad mejorada y aislamiento de red**: implementación de Azure Virtual red (VNET) proporciona aislamiento para la caché de Azure Redis, así como subredes, las directivas de control de acceso y seguridad mejorada y otras características aún más restringen el acceso. Para obtener más información, consulte [cómo configurar la compatibilidad de red Virtual para una caché de Redis Premium Azure](cache-how-to-premium-vnet.md).
-   **Configurar Redis**: en niveles estándar y Premium, puede configurar Redis para las notificaciones de espacio de claves.
-   **El número máximo de conexiones de cliente**: nivel Premium la ofrece el número máximo de clientes que puede conectarse a Redis con un mayor número de conexiones para la caché de tamaño más grandes. [Por favor, consulte la página de precios para obtener más detalles](https://azure.microsoft.com/pricing/details/cache/).
-   **Core dedicado para Redis servidor**: en el nivel de Premium todos los tamaños de caché tienen un núcleo dedicado para Redis. En el estándar de Basic niveles del C1 tamaño y superior tienen un núcleo dedicado para Redis servidor.
-   **Redis tiene un único subproceso** por lo que tener más de dos núcleos no proporciona beneficios adicionales sobre tener solo dos núcleos, pero de mayor tamaño VM normalmente tiene más ancho de banda de menor tamaño. Si el servidor de caché o el cliente llega a los límites de ancho de banda, recibirá los tiempos de espera del cliente.
-   **Mejoras de rendimiento**: se implementan en caché en el nivel de Premium en hardware que tiene procesadores más rápidos y le da mejor rendimiento en comparación con el nivel Basic o estándar. Caché de nivel Premium tienen un mayor rendimiento y latencia inferior.

<a name="cache-performance"></a>
### <a name="azure-redis-cache-performance"></a>Rendimiento de la caché Redis de Azure

La siguiente tabla muestra los valores de ancho de banda máximo observados durante las pruebas de varios tamaños de estándar y Premium almacena con `redis-benchmark.exe` desde una VM Iaas contra el extremo de Azure Redis caché. Tenga en cuenta que no se garantizan que estos valores y no hay ningún SLA para estos números, pero debería típico. Debe cargar probar su propia aplicación para determinar el tamaño de caché adecuada para su aplicación.

Desde esta tabla podemos dibujar las siguientes conclusiones.

-   El rendimiento de la caché que tienen el mismo tamaño es superior en el nivel de Premium en comparación con el nivel estándar. Por ejemplo, con un 6 GB de memoria caché, el rendimiento de P1 es 140K RPS en comparación con 49 K para C3.
-   Lineal con Redis clústeres, el rendimiento aumenta a medida que aumente el número de shards (nodos) en el clúster. Por ejemplo, si crea un clúster P4 de 10 shards, el rendimiento disponible es 250K * 10 = 2,5 millones RPS.
-   El rendimiento para tamaños de clave más grandes es superior en el nivel de Premium en comparación con el nivel estándar.

| Nivel de precios             | Tamaño   | Núcleos de CPU | Ancho de banda disponible                                    | Tamaño de la clave de 1 KB                            |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Tamaños de caché estándar** |        |           | **Megabits por segundo (Mb/s) o Megabytes por segundo (MB/s)** | **Solicitudes por segundo**            |
| C0                       | 250 MB | Compartido    | 5 / 0.625                                              | 600                                      |
| C1                       | 1 GB DE MEMORIA   | 1         | 100 / 12,5                                             | 12200                                    |
| C2                       | 2,5 GB | 2         | 200 / 25                                               | 24000                                    |
| C3                       | 6 GB   | 4         | 400 / 50                                               | 49000                                    |
| C4                       | 13 GB  | 2         | 500 / 62,5                                             | 61000                                    |
| C5                       | 26 GB  | 4         | 1000 / 125                                             | 115000                                   |
| C6                       | 53 GB  | 8         | 2000 / 250                                             | 150000                                   |
| **Tamaños de caché Premium**  |        | **Núcleos de CPU por partes**  |                                         | **Solicitudes por segundo, por partes** |
| P1                       | 6 GB   | 2         | 1000 / 125                                             | 140000                                   |
| P2                       | 13 GB  | 4         | 2000 / 250                                             | 220000                                   |
| P3                       | 26 GB  | 4         | 2000 / 250                                             | 220000                                   |
| P4                       | 53 GB  | 8         | 4000 / 500                                             | 250000                                   |


Para obtener instrucciones sobre la descarga de las herramientas Redis como `redis-benchmark.exe`, consulte la [¿Cómo puedo ejecutar comandos Redis?](#cache-commands) sección.

<a name="cache-region"></a>
### <a name="in-what-region-should-i-locate-my-cache"></a>¿En qué región debo buscar mi caché?

Para obtener el mejor rendimiento y latencia más baja, busque la memoria caché Redis de Azure en la misma región como la aplicación cliente de caché.

<a name="cache-billing"></a>
### <a name="how-am-i-billed-for-azure-redis-cache"></a>¿Cómo me factura caché Redis de Azure?

Precios de Azure Redis caché están [aquí](https://azure.microsoft.com/pricing/details/cache/). La página de precios listas de precios como una tarifa por horas. Caché se cargarán en por minuto desde el momento en que se creó la caché hasta el momento en que se elimina una caché. No hay ninguna opción para detener o pausar la facturación de una caché.


## <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud"></a>¿Puedo usar Azure Redis caché con la nube de la administración pública de Azure o China Azure?

Sí, Azure Redis caché está disponible en la nube de la administración pública de Azure y Azure China nube. Observe que las direcciones URL de acceso y administración de la caché de Azure Redis son diferentes en la nube de la administración pública de Azure y Azure China nube en comparación con la nube pública de Azure. Para obtener más información sobre consideraciones al usar Azure Redis caché con la nube de la administración pública de Azure y Azure China nube, consulte [Bases de datos de la administración pública de Azure - caché Redis de Azure](../azure-government/documentation-government-services-database.md#azure-redis-cache) y [Azure China Cloud - Azure Redis caché](https://www.azure.cn/documentation/services/redis-cache/).

Para obtener información sobre el uso de caché de Azure Redis con PowerShell en la nube de la administración pública de Azure y Azure China nube, consulte [cómo conectarse a la nube de la administración pública de Azure o Azure China](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


<a name="cache-configuration"></a>
### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>¿Qué hacer las opciones de configuración de StackExchange.Redis?

StackExchange.Redis tiene muchas opciones. En esta sección se habla sobre algunos de los ajustes comunes. Para obtener más información acerca de las opciones de StackExchange.Redis, consulte [configuración de StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Descripción|Recomendación
---|---|---
AbortOnConnectFail|Cuando establecida en true, la conexión no volverá a conectarse después de un error de red.|Establecer a false y deje que StackExchange.Redis a volver a conectar automáticamente.
ConnectRetry|Conecte el número de veces que se repite intentos de conexión durante inicial.| Vea las notas siguientes para obtener instrucciones. |
ConnectTimeout|Tiempo de espera en milisegundos para operaciones de conexión.| Vea las notas siguientes para obtener instrucciones. |

En la mayoría de los casos, los valores predeterminados del cliente son suficientes. Puede ajustar las opciones en función de la carga de trabajo.

-   **Reintentos**
    -   Para ConnectRetry y ConnectTimeout las directrices generales son un error rápida y vuelva a intentar nuevamente. Esto se basa en la carga de trabajo y cuánto tiempo en calcular el promedio de tiene el cliente ejecutar un comando Redis y reciba una respuesta.
    -   Permitir StackExchange.Redis volver a conectar automáticamente en lugar de comprobar el estado de conexión y volver a conectar a usted mismo. **Evite el uso de la propiedad ConnectionMultiplexer.IsConnected**.
    -   Snowballing - a veces puede tener un problema donde se Reintentar y esto bolas de nieve y nunca se recupera. En este caso debe considerar mediante un algoritmo de intentos de interrupción exponencial como se describe en [Reintentar directrices generales](best-practices-retry-general.md) publicado por el grupo Microsoft Patterns y prácticas.
-   **Valores de tiempo de espera**
    -   Tenga en cuenta la carga de trabajo y establecer los valores según corresponda. Si va a almacenar valores grandes, establezca el tiempo de espera en un valor más alto.
    -   Establecer `AbortOnConnectFail` StackExchange.Redis false y permiten conectarse para usted.
    -   Usar una única instancia de ConnectionMultiplexer para la aplicación. Puede usar una LazyConnection para crear una instancia única que devuelve una propiedad de conexión, como se muestra en [Conectar con la memoria caché utilizando la clase ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
    -   Establecer el `ConnectionMultiplexer.ClientName` propiedad a un nombre único de instancia de aplicación con fines de diagnóstico.
    -   Usar varios `ConnectionMultiplexer` instancias de cargas de trabajo personalizadas.
    -   Puede seguir este modelo si tiene variar la carga de la aplicación. Por ejemplo:
    -   Puede tener uno multiplexor para tratar grandes claves.
    -   Puede tener uno multiplexor para tratar con teclas pequeñas.
    -   Puede establecer los valores distintos de los tiempos de espera de la conexión y vuelva a intentar lógica para cada ConnectionMultiplexer que usa.
    -   Establecer el `ClientName` propiedad en cada multiplexor para ayudar a diagnósticos.
    -   Esto llevará a más optimizada latencia por `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>¿Qué clientes de caché Redis se debe usar?

Una de las ventajas de Redis es que hay muchos clientes con varios idiomas de desarrollo diferentes. Para obtener una lista de clientes, vea [Redis clientes](http://redis.io/clients). Tutoriales que abarcan varios idiomas diferentes y clientes, vea [cómo usar Azure Redis caché](cache-dotnet-how-to-use-azure-redis-cache.md) y haga clic en el idioma deseado desde el conmutador de idioma en la parte superior de este artículo.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>¿Hay un emulador para Azure Redis caché local?

No hay ningún emulador para Azure Redis caché local, pero puede ejecutar la versión MSOpenTech de server.exe redis desde la [Redis herramientas de línea de comandos](https://github.com/MSOpenTech/redis/releases/) en el equipo local y conectarse a él para obtener una experiencia similar a un emulador de la memoria caché local, como se muestra en el ejemplo siguiente.


    private static Lazy<ConnectionMultiplexer>
        lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Opcionalmente, puede configurar un archivo de [redis.conf](http://redis.io/topics/config) para hacerlos coincidir con la [configuración predeterminada de la caché](cache-configure.md#default-redis-server-configuration) de la memoria caché Redis Azure en línea si lo desea.

<a name="cache-commands"></a>
### <a name="how-can-i-run-redis-commands"></a>¿Cómo puedo ejecutar comandos Redis?

Puede utilizar cualquiera de los comandos que se indican a [Redis comandos](http://redis.io/commands#) excepto para los comandos que se indican a [Redis comandos que no se admite en Azure Redis caché](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Para ejecutar comandos Redis tiene varias opciones.

-   Si tiene una caché estándar o Premium, puede ejecutar comandos Redis mediante la [Consola Redis](cache-configure.md#redis-console). Esto proporciona una forma segura de ejecutar comandos Redis en el portal de Azure.
-   También puede usar las herramientas de línea de comandos Redis. Para usarlos, realice los pasos siguientes.
-   Descargue las [Herramientas de línea de comandos de Redis](https://github.com/MSOpenTech/redis/releases/).
-   Conectarse a la caché mediante `redis-cli.exe`. Pase el extremo de caché utilizando que cambiar -h y la clave se mediante - a como se muestra en el ejemplo siguiente.
-   `redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  - Observe que las herramientas de línea de comandos Redis no funcionan con el puerto SSL, pero puede usar una herramienta como `stunnel` para conectar de forma segura las herramientas para el puerto SSL siguiendo las instrucciones de la entrada de blog de [Anuncio de proveedor de estado de sesión ASP.NET Redis versión de vista previa](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

<a name="cache-reference"></a>
### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>¿Por qué no Azure Redis caché tiene una referencia de biblioteca de clases MSDN como algunos de los servicios de Azure?

Microsoft Azure Redis caché basada en el origen de abrir popular Redis caché y pueden tener acceso a una amplia variedad de [Redis clientes](http://redis.io/clients) que están disponibles para muchos lenguajes de programación. Cada cliente tiene su propia API que hace llamadas a la instancia de caché Redis usando [Redis comandos](http://redis.io/commands).

Dado que cada cliente es diferente, hay no una referencia de clase centralizada en MSDN; en su lugar cada cliente mantiene su propia documentación de referencia. Además de la documentación de referencia, hay varios tutoriales que muestra cómo empezar a usar diferentes idiomas y clientes de caché de caché Redis Azure. Para obtener acceso a estos tutoriales, consulte [cómo usar Azure Redis caché](cache-dotnet-how-to-use-azure-redis-cache.md) y haga clic en el idioma deseado desde el conmutador de idioma en la parte superior de este artículo.


### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>¿Usar caché de Azure Redis como caché PHP sesión?

Sí, para usar caché de Azure Redis como una caché de sesión PHP, especifique la cadena de conexión a su instancia de Azure Redis caché en `session.save_path`.

>[AZURE.IMPORTANT] Cuando se usa Azure Redis caché como una caché de sesión PHP, debe URL codificar la clave de seguridad que se usa para conectarse a la memoria caché, tal como se muestra en el ejemplo siguiente.
>
>`session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
>Si la clave no es una dirección URL codificada, recibirá una excepción similar al siguiente:`Failed to parse session.save_path`

Para obtener más información sobre el uso de caché Redis como caché PHP sesión con el cliente de PhpRedis, consulte [sesión PHP controlador](https://github.com/phpredis/phpredis#php-session-handler).



<a name="cache-ssl"></a>
### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>¿Cuándo debo habilitar el puerto no SSL para conectarse a Redis?

Redis server no admite SSL fuera del cuadro, pero la memoria caché Redis de Azure. Si se está conectando a Azure Redis caché y el cliente es compatible con SSL, como StackExchange.Redis, debe usar SSL.

Observe que el puerto SSL no está deshabilitado de forma predeterminada para las nuevas instancias de Azure Redis caché. Si el cliente no admite SSL, debe habilitar el puerto SSL no siguiendo las instrucciones en la sección [puertos de acceso](cache-configure.md#access-ports) del artículo [Configurar una caché en Azure Redis caché](cache-configure.md) .

Redis herramientas como `redis-cli` con el puerto SSL no funcionan, pero puede usar una herramienta como `stunnel` para conectar de forma segura las herramientas para el puerto SSL siguiendo las instrucciones de la entrada de blog de [Anuncio de proveedor de estado de sesión ASP.NET Redis versión de vista previa](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

Para obtener instrucciones sobre la descarga de las herramientas Redis, consulte la [¿Cómo puedo ejecutar comandos Redis?](#cache-commands) sección.



### <a name="what-are-some-production-best-practices"></a>¿Cuáles son algunas prácticas recomendadas de producción?

-   [Prácticas recomendadas de StackExchange.Redis](#stackexchangeredis-best-practices)
-   [Conceptos y configuración](#configuration-and-concepts)
-   [Pruebas de rendimiento](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Prácticas recomendadas de StackExchange.Redis

-   Establecer `AbortConnect` en false, deje que el ConnectionMultiplexer volver a conectar automáticamente. [Vaya aquí para obtener detalles](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
-   Reutilizar el ConnectionMultiplexer - no crear una nueva para cada solicitud. La `Lazy<ConnectionMultiplexer>` patrón [se muestra aquí](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) es muy recomendable.
-   Redis funciona mejor con valores más pequeños, así que considere la posibilidad de cortar los datos más grandes en varias teclas. En [este Redis discusión](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb se considera "grande". Lea [este artículo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para un problema de ejemplo que puede estar ocasionado por valores de gran tamaño.
-   Configurar la [configuración de grupo de subprocesos](#important-details-about-threadpool-growth) para evitar los tiempos de espera.
-   Use al menos el connectTimeout predeterminado de 5 segundos. Esto daría StackExchange.Redis tiempo suficiente para volver a establecer la conexión, en caso de una señalización visual de la red.
-   Tenga en cuenta los costos de rendimiento asociados con distintas operaciones que se está ejecutando. Por ejemplo, el `KEYS` comando es una operación o (n) y se debe evitar. El [sitio de redis.io](http://redis.io/commands/) tiene detalles sobre la complejidad de tiempo para cada operación que admite. Haga clic en los comandos para ver la complejidad de cada operación.

#### <a name="configuration-and-concepts"></a>Conceptos y configuración

-   Usar estándar o nivel Premium para sistemas de producción. El nivel básico es un sistema de nodo único con ningún SLA y sin replicación de datos. Además, use al menos una caché de C1. Caché de C0 realmente están pensadas para escenarios de desarrollo/prueba simple.
-   Recuerde que Redis es un almacén de datos **En memoria** . Lea [este artículo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para que conozcan de casos donde puede producirse pérdida de datos.
-   Desarrollar el sistema de tal forma que puede controlar conexión señales [debido a correcciones y migración tras error](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Pruebas de rendimiento

-   Iniciar mediante `redis-benchmark.exe` para hacerse una idea de rendimiento posible antes de escribir su propio rendimiento pruebas. Tenga en cuenta que comparativa redis no admite SSL, por lo que tendrá para [Habilitar el puerto SSL no a través del portal de Azure](cache-configure.md#access-ports) antes de ejecutar la prueba. Para obtener ejemplos, vea [cómo ¿evaluar y probar el rendimiento de la memoria caché?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   El cliente VM usado para realizar pruebas debe estar en la misma región como la instancia de caché Redis.
-   Recomendamos usar Dv2 VM serie para su cliente, ya que tienen hardware mejor y dan los mejores resultados.
-   Asegúrese de que su cliente VM elige tiene al menos tantos informática y capacidad de ancho de banda como la memoria caché que está probando. 
-   Habilitar VRSS en el equipo cliente si se encuentra en Windows. [Vaya aquí para obtener detalles](https://technet.microsoft.com/library/dn383582.aspx).
-   Instancias de nivel Premium Redis habrá red mejor latencia y rendimiento porque se están ejecutando en mejorar el hardware para la CPU y de red.

<a name="cache-redis-commands"></a>
### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>¿Cuáles son algunas de las consideraciones al usar comandos Redis comunes?

-   No debe ejecutar determinados comandos Redis que tardar mucho tiempo en completarse sin comprender el impacto de estos comandos.
    -   Por ejemplo, no ejecute el comando de [teclas](http://redis.io/commands/keys) en producción como podría tardar mucho tiempo en devolver dependiendo del número de teclas. Redis es un servidor de un único subproceso y procesa comandos uno a la vez. Si tiene otros comandos emitidos después de teclas, no puede procesar hasta que Redis procesa el comando de teclas. El [sitio de redis.io](http://redis.io/commands/) tiene detalles sobre la complejidad de tiempo para cada operación que admite. Haga clic en los comandos para ver la complejidad de cada operación.
-   ¿Tamaños de clave - debo usar tecla/valores pequeños o grandes o valores de la clave? En general depende del escenario. Si su escenario requiere claves de mayor tamaño, a continuación, puede ajustar ConnectionTimeout y vuelva a intentar valores y ajustar la lógica de reintento. Desde una perspectiva de servidor Redis valores más pequeños se observan tener un mejor rendimiento.
-   Esto significa que no pueden almacenarse valores mayores en Redis; debe tener en cuenta las siguientes consideraciones. Latencia será mayor. Si tiene un conjunto de datos que sea mayores y uno que sea más pequeño, puede usar varias instancias de ConnectionMultiplexer, cada uno configurado con un conjunto diferente de valores de tiempo de espera y vuelva a intentarlo, como se describe en la sección de [las opciones de configuración de StackExchange.Redis ¿qué](#cache-configuration) anterior.



<a name="cache-benchmarking"></a>
### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>¿Cómo puedo evaluar y probar el rendimiento de la memoria caché?

-   [Habilitar diagnósticos de memoria caché](cache-how-to-monitor.md#enable-cache-diagnostics) para que pueda [supervisar](cache-how-to-monitor.md) el mantenimiento de la memoria caché. Puede ver las mediciones en el portal de Azure y también se pueden [descargar y revisar](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) con las herramientas de su elección.
-   Puede usar benchmark.exe redis a prueba de carga de su servidor Redis.
-   Asegúrese de que la carga de prueba de cliente y la caché Redis están en la misma región.
-   Use cli.exe redis y supervisar la caché mediante el comando de información.
-   Si su carga está causando fragmentación de memoria alta deben escalar hacia arriba a un tamaño de caché más grande.
-   Para obtener instrucciones sobre la descarga de las herramientas Redis, consulte la [¿Cómo puedo ejecutar comandos Redis?](#cache-commands) sección.

A continuación se muestra un ejemplo de uso benchmark.exe redis. Para obtener resultados precisos, ejecutar este comando desde una máquina virtual en la misma región como la memoria caché.

-   Solicitudes de pruebas canalizado establecer mediante una carga k 1

    benchmark.exe Redis - h **yourcache**. redis.cache.windows.net - **yourAccesskey** -t establecer 1000000 - n -d 1024 - P 50
    
-   Prueba canalizado obtener solicitudes mediante una carga k 1. 
    Nota: Ejecutar el conjunto de pruebas se indicó anteriormente primero para rellenar la caché
    
    benchmark.exe Redis - h **yourcache**. redis.cache.windows.net - **yourAccesskey** -t GET -n 1000000 - d 1024 - P 50

<a name="threadpool"></a>
### <a name="important-details-about-threadpool-growth"></a>Detalles importantes sobre crecimiento del grupo de subprocesos

El grupo de subprocesos CLR tiene dos tipos de subprocesos - "Trabajo" y "Puerto de finalización de i/OS" (también conocido como IOCP) subprocesos. 

-   Subprocesos de trabajo se usan cuando para cosas como procesamiento `Task.Run(…)` o `ThreadPool.QueueUserWorkItem(…)` métodos. Estos subprocesos también se usan varios componentes de CLR al trabajo hay que realizar en un subproceso de fondo.
-   Subprocesos IOCP se usan cuando IO asincrónica ocurre (por ejemplo, la lectura de la red). 

El grupo de subprocesos proporciona nuevos subprocesos de trabajo o subprocesos de finalización de i/OS a petición (sin limitación) hasta que alcance el valor de "Mínimo" para cada tipo de subproceso. De forma predeterminada, el número mínimo de subprocesos se establece en el número de procesadores en un sistema. 

Una vez que el número de existente (ocupado) subprocesos visitas que el número de subprocesos, el grupo de subprocesos "mínimo" Acelerador es la velocidad con que inserta nuevos subprocesos a un subproceso por cada 500 milisegundos. Esto significa que si el sistema obtiene una ráfaga de trabajo que necesitan un subproceso IOCP, procesará que funcionan muy rápidamente. Sin embargo, si la ráfaga de trabajo es mayor que el valor de "Mínima" configurado, habrá algunos retraso de procesamiento parte del trabajo, como el grupo de subprocesos que espera para uno de dos cosas que puede ocurrir.

1. Se convierte en un subproceso existente libre para procesar el trabajo.
1. Ningún subproceso existente se convierte en libre para 500 ms, por lo que se crea un nuevo subproceso.

Básicamente, significa que cuando el número de subprocesos ocupados es mayor que subprocesos Min, es probable que paga un retraso de 500 ms antes de tráfico de red es procesado por la aplicación. Además, es importante que tenga en cuenta que, cuando un subproceso existente permanece inactivo durante más de 15 segundos (según lo recuerde), se limpia y repita este ciclo de crecimiento y contracción.

Si observamos un mensaje de error de ejemplo de StackExchange.Redis (compilación 1.0.450 o posterior), verá que ahora imprime estadísticas del grupo de subprocesos (consulte IOCP y trabajo los detalles).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
    IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

En el ejemplo anterior, puede ver que subproceso IOCP hay subprocesos ocupados 6 y el sistema está configurado para permitir 4 subprocesos mínimos. En este caso, el cliente probablemente habría visto retrasos de 500 ms dos porque 6 > 4.

Tenga en cuenta que StackExchange.Redis puede presionar tiempos de espera si obtiene acelerado crecimiento de subprocesos IOCP o de trabajo.

### <a name="recommendation"></a>Recomendación

Dada esta información, le recomendamos encarecidamente que los clientes establezcan el valor de configuración mínima para subprocesos IOCP y trabajo en un valor mayor que el valor predeterminado. No podemos dar única orientación sobre lo que este valor debe ser porque el valor correcto para una aplicación será muy alta/baja para otra aplicación. Esta configuración también puede afectar al rendimiento de otros elementos de aplicaciones complicadas, por lo que cada cliente necesita ajustar esta configuración a sus necesidades específicas. Un buen punto de partida es 200 o 300, probar y ajustar según sea necesario.

Cómo configurar esta configuración:

-   En ASP.NET, use la [opción de configuración de "minIoThreads"][] en la `<processModel>` elemento de la configuración de web.config. Si está ejecutando dentro de sitios Web de Azure, esta configuración no se expone a través de las opciones de configuración. Sin embargo, aún podrá establecer mediante programación (consulte a continuación) desde el método Application_Start en global.asax.cs.

> **Nota importante:** el valor especificado en este elemento de configuración es una configuración *por núcleo* . Por ejemplo, si tiene un equipo de 4 núcleo y desea el valor minIOThreads sea 200 en tiempo de ejecución, usaría `<processModel minIoThreads="50"/>`.

-   Fuera de ASP.NET, use la [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API.

<a name="server-gc"></a>
### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Habilitar catálogo global del servidor obtener el mejor rendimiento en el cliente cuando se usa StackExchange.Redis

Habilitar catálogo global de servidor puede optimizar al cliente y proporcionar un mejor rendimiento y StackExchange.Redis. Para obtener más información sobre el servidor de catálogo global y cómo habilitarlo, consulte los artículos siguientes.

-   [Para habilitar el catálogo global del servidor](https://msdn.microsoft.com/library/ms229357.aspx)
-   [Conceptos básicos de recolección](https://msdn.microsoft.com/library/ee787088.aspx)
-   [Recolección y rendimiento](https://msdn.microsoft.com/library/ee851764.aspx)







<a name="cache-monitor"></a>
### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>¿Cómo se puede supervisar el estado y el rendimiento de la memoria caché?

Se pueden supervisar instancias de Microsoft Azure Redis caché en el [portal de Azure](https://portal.azure.com). Puede ver métricas, anclar gráficos métricas a la Startboard, personalizar el intervalo de fecha y hora de supervisión gráficos, agregar y quitar métricas de los gráficos y establecer alertas cuando se cumplan ciertas condiciones. Para obtener más información, consulte [Monitor Azure Redis caché](cache-how-to-monitor.md).

La sección de **soporte técnico + solución de problemas** del módulo de **configuración** de la caché de Redis también contiene varias herramientas para supervisar y solucionar problemas de la caché. 

-   **Solución de problemas** proporciona información sobre problemas y estrategias para resolverlos problemas comunes.
-   **Registros de auditoría** se proporciona información sobre las acciones que se realicen en la memoria caché. También puede usar el filtrado para ampliar esta vista para incluir otros recursos.
-   **Estado de los recursos** inspecciones de los recursos y le indica si se está ejecutando según lo esperado. Para obtener más información sobre el servicio de estado de los recursos de Azure, vea [información general sobre el estado de recursos de Azure](../resource-health/resource-health-overview.md).
-   **Nueva solicitud de soporte técnico** proporciona opciones para abrir una solicitud de soporte técnico de la memoria caché.

Estas herramientas permiten supervisar el estado de las instancias de Azure Redis caché y ayudarle a administrar las aplicaciones de almacenamiento en caché. Para obtener más información, vea [solución de problemas de configuración y soporte técnico](cache-configure.md#support-amp-troubleshooting-settings).

### <a name="my-cache-diagnostics-storage-account-settings-changed-what-happened"></a>Caché diagnósticos almacenamiento configuración de mi cuenta ha cambiado, ¿qué ha ocurrido?

Caché en la misma región y de suscripción comparten la misma configuración de almacenamiento de información de diagnóstico y, si la configuración es modificado (diagnóstico habilitado o deshabilitado o cambiar la cuenta de almacenamiento) se aplica a todas las caché en la suscripción que están en esa región. Si ha cambiado la configuración de diagnósticos de la memoria caché, compruebe si la configuración de diagnóstico de caché de otra en la misma suscripción y región ha cambiado. Una forma de comprobarlo es ver los registros de auditoría para la memoria caché para un `Write DiagnosticSettings` evento. Para obtener más información sobre cómo trabajar con registros de auditoría, consulte [Ver eventos y registros de auditoría](../monitoring-and-diagnostics/insights-debugging-with-events.md) y [auditoría de las operaciones con el Administrador de recursos](../resource-group-audit.md). Para obtener más información sobre la supervisión de eventos de Azure Redis caché, vea [operaciones y alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="why-is-diagnostics-enabled-for-some-new-caches-but-not-others"></a>¿Por qué está habilitadas diagnósticos para algunos caché nuevas pero no otros?

Caché en la misma configuración regional y de suscripción comparten la misma configuración de almacenamiento de información de diagnóstico. Si crea una nueva caché en la misma región y suscripción como otra caché que tiene habilitadas de diagnósticos, diagnósticos está habilitada en la caché de nueva con la misma configuración.


<a name="cache-timeouts"></a>
### <a name="why-am-i-seeing-timeouts"></a>¿Por qué estoy viendo los tiempos de espera?

Tiempo de espera ocurre en el cliente de hablar con Redis. La mayor parte Redis server no expira. Cuando se envía un comando al servidor Redis, el comando está en la cola y servidor Redis finalmente recoge el comando y ejecuta. Sin embargo el cliente puede desaparezca durante este proceso y si hace una excepción se produce en el lado de la llamada. Para obtener más información acerca de cómo solucionar problemas de tiempo de espera, vea [solución de problemas de lado cliente](cache-how-to-troubleshoot.md#client-side-troubleshooting) y [StackExchange.Redis excepciones de tiempo de espera](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).


<a name="cache-disconnect"></a>
### <a name="why-was-my-client-disconnected-from-the-cache"></a>¿Por qué mi cliente se ha desconectado de la memoria caché?

Las siguientes son algunas motivo comunes de una desconexión de caché.

-   Causas del cliente
    -   Se vuelve a instalar la aplicación cliente.
    -   La aplicación cliente realiza una operación de escala.
        -   En el caso de los servicios de nube o aplicaciones Web, puede deberse a escala automática.
    -   Cambia el nivel de red del cliente.
    -   Transitorias errores en el cliente o en los nodos de red entre el cliente y el servidor.
    -   Se han alcanzado los límites de umbral de ancho de banda.
    -   Operaciones con la CPU tardó demasiado tiempo en completarse.
-   Causas de servidor
    -   En la memoria caché estándar ofreciendo, el servicio de caché de Azure Redis inicia una conmutar desde el nodo principal para el nodo secundario.
    -   Azure revisión de la instancia donde se implementa la memoria caché
        -   Esto puede deberse a Redis actualizaciones de servidor o mantenimiento VM general.







### <a name="which-azure-cache-offering-is-right-for-me"></a>¿Qué ofertas de caché de Azure es la adecuada para mí?

>[AZURE.IMPORTANT]Según lo del año pasado [anuncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), se retirará servicio del servicio de caché administrada de Azure y Azure en la función de caché de 30 de noviembre de 2016. Nuestra recomendación es usar la [Memoria caché Redis de Azure](https://azure.microsoft.com/services/cache/). Para obtener información sobre la migración, vea [migrar desde servicio de caché administrado a Azure Redis caché](cache-migrate-to-redis.md).

### <a name="azure-redis-cache"></a>Caché de Azure Redis
Caché de Redis Azure es generalmente disponibles en tamaños de 53 GB y tiene una disponibilidad SLA de 99,9%. El nuevo [nivel de premium](cache-premium-tier-intro.md) ofrece tamaños hasta 530 GB y soporte técnico para clústeres, VNET y persistencia con un SLA 99,9%.

Caché de Redis Azure ofrece a los clientes la capacidad de usar una segura y dedicada Redis caché, administrada por Microsoft. Con esta oferta, obtendrá aprovechar el amplio conjunto de características y ecosistema de Redis y confiable hospedaje y la supervisión de Microsoft.

A diferencia de caché tradicionales que tratan sólo los pares de clave y valor, Redis es popular para los tipos de datos de gran rendimiento. Redis también es compatible con la ejecución de las operaciones atómicas sobre estos tipos, como si se agrega a una cadena; incrementar el valor en un valor de hash; Insertar a una lista; calcular la intersección de conjunto, unión y diferencia; o bien, obteniendo al miembro con mayor clasificación de un conjunto ordenado. Otras características incluyen compatibilidad para las transacciones, pub/sub, Lua secuencias de comandos, claves con un número limitado tiempo de vida y opciones de configuración que Redis se comporte más como una caché tradicional.

Otro aspecto clave para el éxito Redis es el ecosistema correcto, colores vivos Abrir origen integrado a su alrededor. Esto se refleja en el conjunto diverso de clientes Redis disponibles en varios idiomas. Esto le permite utilizar casi cualquier carga de trabajo que crearía dentro de Azure. 

Para obtener más información sobre cómo empezar con Azure Redis caché, vea [cómo usar Azure Redis caché](cache-dotnet-how-to-use-azure-redis-cache.md) y la [documentación de Azure Redis caché](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="managed-cache-service"></a>Servicio de caché administrada
[Administra la caché de servicio está configurado para retirar el 30 de noviembre de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### <a name="in-role-cache"></a>En la función de caché
[En la función de caché está configurado para retirar el 30 de noviembre de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





[configuración de "minIoThreads"]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
