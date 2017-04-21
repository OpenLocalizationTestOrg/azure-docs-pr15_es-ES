<properties 
    pageTitle="Caché de migrar a Redis | Microsoft Azure"
    description="Obtenga información sobre cómo migrar aplicaciones de servicio de caché administrado a Azure Redis caché"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="09/30/2016"
    ms.author="sdanie" />

# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrar desde el servicio de caché administrada a Azure Redis caché

Migrar las aplicaciones que utilicen el servicio de caché de Azure administradas a Azure Redis caché se puede realizar con un mínimo de los cambios a la aplicación, dependiendo de las características de servicio de caché administrado utilizada la aplicación de almacenamiento en caché. Mientras las API no son exactamente iguales son similares y gran parte de su código existente que utiliza administra el servicio de caché para tener acceso a una caché se puede reutilizar con un mínimo de cambios. Este tema muestra cómo hacer que la configuración y los cambios de la aplicación para migrar sus aplicaciones de servicio de caché administrado usar Azure Redis caché y muestra cómo algunas de las características de Azure Redis caché pueden utilizarse para implementar la funcionalidad de caché de servicio de caché administrado.

## <a name="migration-steps"></a>Pasos de migración

Los pasos siguientes son necesarios para migrar una aplicación de servicio de caché administrado usar Azure Redis caché.

-   Asignar las características de servicio de caché administrado a Azure Redis caché
-   Elija una oferta de caché
-   Crear una caché
-   Configurar a los clientes de caché
    -   Quitar la configuración del servicio de caché administrada
    -   Configurar a un cliente de caché mediante el paquete de NuGet StackExchange.Redis
-   Migrar código administrado servicio de caché
    -   Conectarse a la caché mediante la clase ConnectionMultiplexer
    -   Tipos de datos simples de acceso en la caché.
    -   Trabajar con objetos .NET en la caché.
-   Migrar el estado de la sesión de ASP.NET y la caché de resultados en caché Redis de Azure 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Asignar las características de servicio de caché administrado a Azure Redis caché

Administra el servicio de caché de Azure y Azure Redis caché son similares pero implementan algunas de sus características de diferentes formas. En esta sección se describe algunas de las diferencias y proporciona instrucciones sobre cómo implementar las características del servicio de caché administradas en Azure Redis caché.

|Función de servicio de caché gestionada|Compatibilidad con el servicio de caché administrada|Soporte de caché Redis Azure|
|---|---|---|
|Caché con nombre|Se ha configurado una caché predeterminado y en la caché estándar y Premium pueden configurarse ofertas hasta nueve denominada caché adicionales si lo desea.|Caché de Azure Redis tienen un número configurable de las bases de datos (valor predeterminado de 16) que se pueden usar para implementar una funcionalidad similar a la caché con nombre. Para obtener más información, consulte [Configuración del servidor predeterminado Redis](cache-configure.md#default-redis-server-configuration).|
|Alta disponibilidad|Proporciona alta disponibilidad de los elementos de la memoria caché en las ofertas de caché estándar y Premium. Si los elementos se pierden debido a un error, copias de seguridad de los elementos de la memoria caché siguen estando disponibles. Escritura en la memoria caché secundaria realizado sincrónicamente.|Alta disponibilidad está disponible en las ofertas de caché estándar y Premium, que tienen una configuración de primario/réplica de dos nodos (cada fragmentar en caché Premium tiene un par de primario/réplica). Escribe a la réplica se realiza de forma asincrónica. Para obtener más información, vea [precios de Azure Redis caché](https://azure.microsoft.com/pricing/details/cache/).|
|Notificaciones|Permite a los clientes recibir notificaciones asincrónicas cuando se produce una gran variedad de operaciones de la caché en una caché denominada.|Aplicaciones cliente pueden utilizar Redis pub/sub o [notificaciones de espacio de claves](cache-configure.md#keyspace-notifications-advanced-settings) para lograr una funcionalidad similar a las notificaciones.|
|Memoria caché local|Almacena una copia de los objetos almacenados en caché localmente en el cliente de acceso muy rápido.|Aplicaciones cliente necesarias para implementar esta funcionalidad utilizando un diccionario o una estructura de datos similares.|
|Directiva de expulsión|Ninguno o LRU. La directiva predeterminada es LRU.|Caché de Azure Redis admite las siguientes directivas de expulsión: volátiles lru, allkeys lru, volátiles aleatorio, aleatorio allkeys volátiles-ttl, noeviction. La directiva predeterminada es lru volátiles. Para obtener más información, consulte [Configuración del servidor predeterminado Redis](cache-configure.md#default-redis-server-configuration).|
|Directiva de caducidad|La directiva de expiración predeterminada es absoluto y el intervalo de caducidad predeterminado es diez minutos. Directivas de deslizamiento y nunca también están disponibles.|De forma predeterminada los elementos de la memoria caché no expiran, pero una caducidad puede configurarse en una base de escritura por utilizando sobrecargas del conjunto de caché. Para obtener más información, vea [Agregar y recuperar objetos de la memoria caché](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache).|
|Regiones y etiquetas temáticas|Las regiones son subgrupos elementos almacenados en caché. Regiones también admiten las anotaciones de elementos almacenados en caché con otras cadenas descriptivas denominadas etiquetas. Regiones admiten la posibilidad de llevar a cabo operaciones de búsqueda en los elementos etiquetados en esa región. Todos los elementos dentro de una región se encuentran dentro de un solo nodo del clúster de caché.|Una caché Redis consta de un único nodo (a menos que Redis clúster está habilitado) para que no se aplica el concepto de regiones de servicio de caché administrado. Redis admite la búsqueda y operaciones de comodín al recuperar claves para que etiquetas descriptivas se pueden incrustadas dentro de los nombres de clave y utiliza para recuperar los elementos más adelante. Para obtener un ejemplo de implementar una solución etiquetada con Redis, vea [implementar caché etiquetado con Redis](http://stackify.com/implementing-cache-tagging-redis/).|
|Serialización|Caché administrada admite NetDataContractSerialize, BinaryFormatter y el uso de serializadores personalizados. El valor predeterminado es NetDataContractSerialize.|Es la responsabilidad de la aplicación cliente para serializar objetos .NET antes de colocarlos en la caché, con la opción del serializador hasta el programador de la aplicación de cliente. Para obtener más información y código de ejemplo, consulte [trabajar con objetos .NET en la caché](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).|
| Emulador de caché | Caché administrada proporciona un emulador de la memoria caché local. | Caché de Redis Azure no tiene un emulador, pero se puede [ejecutar la compilación MSOpenTech de redis-server.exe localmente](cache-faq.md#cache-emulator) para proporcionar una experiencia de emulador. |

## <a name="choose-a-cache-offering"></a>Elija una oferta de caché

Caché de Microsoft Azure Redis está disponible en los niveles siguientes:

-   **Básica** : nodo único. Varios tamaños 53 GB.
-   **Estándar** : dos nodos principal o réplica. Varios tamaños 53 GB. 99,9 SLA %.
-   **Premium** : dos nodos principal o réplica con hasta 10 shards. Varios tamaños de 6 GB a 530 GB (póngase en contacto con nosotros para obtener más información). Todas las características de nivel estándar y más incluido el soporte para [Redis clúster](cache-how-to-premium-clustering.md) [Redis persistencia](cache-how-to-premium-persistence.md)y [Red Virtual de Azure](cache-how-to-premium-vnet.md). 99,9 SLA %.

Cada nivel de la diferencia en términos de características y precios. Las características se tratan más adelante en esta guía y para obtener más información sobre los precios, consulte [Detalles de precios de caché](https://azure.microsoft.com/pricing/details/cache/).

Elija el tamaño que coincida con el tamaño de la memoria caché de servicio de caché administrado anterior y, a continuación, escalar hacia arriba o hacia abajo según los requisitos de la aplicación es un punto de partida para la migración. Para obtener más información sobre cómo elegir la oferta de Azure Redis caché adecuada, consulte [qué oferta de caché Redis y tamaño debo usar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Crear una caché

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurar a los clientes de caché

Una vez creada y configurada la caché, el siguiente paso es quitar la configuración del servicio de caché administrada y a continuación, agregue el agregar la configuración de caché de Azure Redis y referencias para que clientes de caché pueden tener acceso a la memoria caché.

-   Quitar la configuración del servicio de caché administrada
-   Configurar a un cliente de caché mediante el paquete de NuGet StackExchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Quitar la configuración del servicio de caché administrada

Antes de que se pueden configurar las aplicaciones cliente de Azure Redis caché, la configuración del servicio de caché administrado existente y se deben quitar las referencias de ensamblado desinstalar el paquete NuGet de servicio de caché administrada.

Para desinstalar el paquete administrado NuGet de servicio de caché, haga clic en el proyecto de cliente en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**. Seleccione el nodo **paquetes instalados** y a continuación, escriba W**indowsAzure.Caching** en el cuadro de búsqueda paquetes instalados. Seleccione **Windows** **Azure caché** (o **Windows** **Azure almacenamiento en caché** según la versión del paquete NuGet), haga clic en **desinstalar**y, a continuación, haga clic en **Cerrar**.

![Desinstalar paquete NuGet de servicio de caché administrada Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Desinstalar el paquete NuGet de servicio de caché administrada quita los ensamblados administra el servicio de caché y las entradas de servicio de caché administrada en el app.config o web.config de la aplicación cliente. Porque no se pueden quitar algunas configuraciones personalizadas al desinstalar el paquete de NuGet, abra web.config o app.config y asegúrese de que los elementos siguientes se quitan por completo.

Asegúrese de que el `dataCacheClients` se quita de la `configSections` elemento. No se elimina todo el `configSections` elemento; quitar el `dataCacheClients` entrada, si está presente.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

Asegúrese de que el `dataCacheClients` sección se elimina. La `dataCacheClients` sección será similar al siguiente ejemplo.

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Cuando se quita la configuración del servicio de caché administrado, puede configurar al cliente de caché como se describe en la sección siguiente.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurar a un cliente de caché mediante el paquete de NuGet StackExchange.Redis

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrar código administrado servicio de caché

La API para el cliente de caché StackExchange.Redis es similar al servicio de caché administrada. Esta sección proporciona una descripción general de las diferencias.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Conectarse a la caché mediante la clase ConnectionMultiplexer

En administrar el servicio de caché, las conexiones de la memoria caché que gestionó la `DataCacheFactory` y `DataCache` clases. En caché Redis de Azure, estas conexiones se administran por la `ConnectionMultiplexer` clase.

Agregue lo siguiente mediante declaración a la parte superior de cualquier archivo desde la que desea tener acceso a la memoria caché.

    using StackExchange.Redis
                                
Si no se resuelve este espacio de nombres, asegúrese de que ha agregado el paquete StackExchange.Redis NuGet como se describe en [configurar a los clientes de la memoria caché](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

>[AZURE.NOTE] Observe que el cliente StackExchange.Redis requiere .NET Framework 4 o posterior.

Para conectarse a una instancia de Azure Redis caché, llamar estático `ConnectionMultiplexer.Connect` método y pase el punto final y la clave. Un enfoque para compartir un `ConnectionMultiplexer` instancia de la aplicación es tener una propiedad estática que devuelve una instancia conectada, similar al ejemplo siguiente. Proporciona una manera de subprocesos iniciar un sólo conectado `ConnectionMultiplexer` instancia. En este ejemplo `abortConnect` está establecida en false, lo que significa que la llamada se realizará correctamente incluso si no se establece una conexión a la memoria caché. Una característica clave de `ConnectionMultiplexer` es que restaurar automáticamente conectividad a la caché de una vez el problema de red o se resuelven otras causas.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

El extremo de caché, las teclas y puertos pueden obtenerse el módulo de **Caché Redis** para la instancia de caché. Para obtener más información, vea [Propiedades de la caché Redis](cache-configure.md#properties).

Una vez establecida la conexión, devuelve una referencia a la base de datos de caché Redis llamando el `ConnectionMultiplexer.GetDatabase` método. El objeto devuelto por la `GetDatabase` método es un objeto ligero paso a través y no necesita almacenarse.

    IDatabase cache = Connection.GetDatabase();
    
    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);
    
    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

El cliente de StackExchange.Redis utiliza la `RedisKey` y `RedisValue` tipos para obtener acceso y almacenar elementos en la memoria caché. Estos tipos de asignan a más simples tipos de idioma, incluidos la cadena y a menudo no se usan directamente. Redis cadenas son el tipo más básico de valor Redis y pueden contener varios tipos de datos, incluidos el número de serie de secuencias binarias, y mientras no puede usar el tipo directamente, que usa los métodos que contienen `String` en el nombre. Para los tipos de datos más simples almacenar y recuperar los elementos de la memoria caché utilizando la `StringSet` y `StringGet` métodos, a menos que está almacenando colecciones u otros tipos de datos Redis en la memoria caché. 

`StringSet`y `StringGet` son muy similares al servicio de caché administrada `Put` y `Get` métodos, con una gran diferencia es que antes de configurar y obtener un objeto de .NET en la memoria caché se deben serializar en primer lugar. 

Al llamar a `StringGet`, si existe el objeto, se devuelve y, si no es así, se devuelve Nulo. En este caso puede recuperar el valor del origen de datos que desee y almacenar en la caché para su uso posterior. Esto se conoce como el modelo de caché separada.

Para especificar la caducidad de un elemento en la caché, utilice el `TimeSpan` parámetro de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

Caché de Azure Redis puede trabajar con objetos. NET, así como tipos de datos simples, pero antes de que un objeto .NET puede almacenarse en caché debe ser serie. Esto es responsabilidad del desarrollador de la aplicación. Esto le da la flexibilidad del programador en la opción del serializador. Para obtener más información y código de ejemplo, consulte [trabajar con objetos .NET en la caché](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migrar el estado de la sesión de ASP.NET y la caché de resultados en caché Redis de Azure

Caché de Azure Redis tiene proveedores de estado de la sesión de ASP.NET y de caché de resultados de página. Para migrar la aplicación que usa las versiones administra el servicio de caché de estos proveedores, primero quitar las secciones existentes de su web.config y, a continuación, configurar las versiones de Azure Redis caché de los proveedores. Para obtener instrucciones sobre el uso de los proveedores de ASP.NET de Azure Redis caché, vea el [Proveedor de estado de sesión de ASP.NET para Azure Redis caché](cache-aspnet-session-state-provider.md) y [Proveedor de caché de resultados de ASP.NET para Azure Redis caché](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Pasos siguientes

Explorar la [documentación de Azure Redis caché](https://azure.microsoft.com/documentation/services/cache/) para tutoriales, muestras, vídeos y mucho más.

