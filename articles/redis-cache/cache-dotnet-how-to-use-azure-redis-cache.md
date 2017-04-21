<properties 
    pageTitle="Cómo usar caché Redis Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo mejorar el rendimiento de las aplicaciones de Azure con Azure Redis caché" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache"></a>Cómo usar caché Redis Azure

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Esta guía le muestra cómo empezar a usar **Azure Redis caché**. Microsoft Azure Redis caché se basa en el código abierto popular Redis caché. Le proporciona acceso a una segura y dedicada Redis caché, administrada por Microsoft. Una caché creada con Azure Redis caché es accesible desde cualquier aplicación de Microsoft Azure.

Caché de Microsoft Azure Redis está disponible en los niveles siguientes:

-   **Básica** : nodo único. Varios tamaños 53 GB.
-   **Estándar** : dos nodos principal o réplica. Varios tamaños 53 GB. 99,9 SLA %.
-   **Premium** : dos nodos principal o réplica con hasta 10 shards. Varios tamaños de 6 GB a 530 GB (póngase en contacto con nosotros para obtener más información). Todas las características de nivel estándar y más incluido el soporte para [Redis clúster](cache-how-to-premium-clustering.md) [Redis persistencia](cache-how-to-premium-persistence.md)y [Red Virtual de Azure](cache-how-to-premium-vnet.md). 99,9 SLA %.

Cada nivel de la diferencia en términos de características y precios. Para obtener información sobre precios, consulte [Detalles de precios de caché][].

Esta guía le muestra cómo usar el cliente de [StackExchange.Redis][] mediante C\# código. Los escenarios cubiertos incluyen **crear y configurar una caché**, **configuración de clientes de caché**y **Agregar y quitar objetos de la memoria caché**. Para obtener más información sobre el uso de la memoria caché Redis de Azure, consulte la sección [Pasos siguientes][] . Para obtener un tutorial paso a paso de la creación de un ASP.NET MVC web app con caché Redis, vea [cómo crear una aplicación Web con Redis caché](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>
## <a name="get-started-with-azure-redis-cache"></a>Introducción a Azure Redis caché

Introducción a Azure Redis caché es fácil. Para empezar, aprovisionar y configurar una memoria caché. A continuación, configure los clientes de la memoria caché para que puedan tener acceso a la memoria caché. Una vez configurados los clientes de caché, puede comenzar a trabajar con ellos.

-   [Crear la caché][]
-   [Configurar a los clientes de caché][]

<a name="create-cache"></a>
## <a name="create-a-cache"></a>Crear una caché

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>Para obtener acceso a la memoria caché después de se crea

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Para obtener más información acerca de cómo configurar la memoria caché, consulte [cómo configurar la caché de Azure Redis](cache-configure.md).

<a name="NuGet"></a>
## <a name="configure-the-cache-clients"></a>Configurar a los clientes de caché

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Una vez que el proyecto de cliente está configurado para el almacenamiento en caché, puede usar las técnicas descritas en las secciones siguientes para trabajar con la memoria caché.

<a name="working-with-caches"></a>
## <a name="working-with-caches"></a>Trabajar con la caché

Los pasos de esta sección describen cómo realizar tareas comunes con la memoria caché.

-   [Conectarse a la caché][]
-   [Agregar y recuperar objetos de la caché][]
-   [Trabajar con objetos .NET en la caché.](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## <a name="connect-to-the-cache"></a>Conectarse a la caché

Para trabajar con una caché mediante programación, necesita una referencia a la memoria caché. Agregue lo siguiente a la parte superior de cualquier archivo desde el que desea utilizar al cliente de StackExchange.Redis para tener acceso a una caché Redis de Azure.

    using StackExchange.Redis;

>[AZURE.NOTE] El cliente de StackExchange.Redis requiere .NET Framework 4 o posterior.

La conexión a la caché de Azure Redis es administrada por la `ConnectionMultiplexer` clase. Esta clase está diseñada para compartir y volver a usar en la aplicación cliente y no es necesario crear de forma por la operación. 

Para conectarse a una caché de Azure Redis y devolver una instancia de un conectada `ConnectionMultiplexer`, llamar estático `Connect` método y pase el punto final de la memoria caché y la clave como el siguiente ejemplo. Use la clave generada desde el Portal de Azure como el parámetro de contraseña.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Advertencia: Nunca almacén de credenciales en el código fuente. Para que este ejemplo sencillo, estoy mostrando en el código fuente. Vea [cómo las cadenas de la aplicación y trabajos de cadenas de conexión][] para obtener información sobre cómo almacenar las credenciales.

Si no desea utilizar SSL, establecer `ssl=false` o se omite la `ssl` parámetro.

>[AZURE.NOTE] El puerto SSL no está deshabilitado de forma predeterminada para la caché de nuevas. Para obtener instrucciones sobre cómo habilitar el puerto no SSL, vea los [Puertos de acceso](cache-configure.md#access-ports)...

Un enfoque para compartir un `ConnectionMultiplexer` instancia de la aplicación es tener una propiedad estática que devuelve una instancia conectada, similar al ejemplo siguiente. Proporciona una manera de subprocesos iniciar un sólo conectado `ConnectionMultiplexer` instancia. En estos ejemplos `abortConnect` está establecida en false, lo que significa que la llamada se realizará correctamente incluso si no se establece una conexión a la memoria caché Redis de Azure. Una característica clave de `ConnectionMultiplexer` es que restaurar automáticamente conectividad a la caché de una vez el problema de red o se resuelven otras causas.

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

Para obtener más información sobre las opciones de configuración de conexión avanzada, vea el [modelo de configuración de StackExchange.Redis][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Una vez establecida la conexión, devuelve una referencia a la base de datos de caché redis llamando el `ConnectionMultiplexer.GetDatabase` método. El objeto devuelto por la `GetDatabase` método es un objeto ligero paso a través y no necesita almacenarse.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Ahora que sabe cómo conectarse a una instancia de Azure Redis caché y devolver una referencia a la base de datos de caché, echemos un vistazo a trabajar con la memoria caché.

<a name="add-object"></a>
## <a name="add-and-retrieve-objects-from-the-cache"></a>Agregar y recuperar objetos de la caché

Pueden almacenarse en elementos y recuperados de una caché mediante el uso de la `StringSet` y `StringGet` métodos.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis almacena datos mayoría como cadenas Redis, pero estas cadenas pueden contener varios tipos de datos, incluidos el número de serie datos binarios que se pueden usar para almacenar .NET objetos en la memoria caché.

Al llamar a `StringGet`, si existe el objeto, se devuelve, y si no lo hace, `null` se devuelve. En este caso puede recuperar el valor del origen de datos que desee y almacenar en la caché para su uso posterior. Esto se conoce como el modelo de caché separada.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Para especificar la caducidad de un elemento en la caché, utilice el `TimeSpan` parámetro de `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Trabajar con objetos .NET en la caché.

Caché de Azure Redis caché de objetos. NET, así como tipos de datos simples, pero antes de que un objeto .NET puede almacenarse en caché debe ser serie. Esto es responsabilidad del desarrollador de la aplicación y le ofrece la flexibilidad del programador en la opción del serializador.

Una forma sencilla de serializar objetos es usar la `JsonConvert` métodos de serialización en [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) y serializar a y desde JSON. En el ejemplo siguiente se muestra un get y conjunto mediante una `Employee` instancia del objeto.


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos, siga estos vínculos para obtener más información acerca de la caché Redis Azure.

-   Consulte los proveedores ASP.NET para Azure Redis caché.
    -   [Proveedor de estado de la sesión de Azure Redis](cache-aspnet-session-state-provider.md)
    -   [Azure Redis proveedor de caché de caché de resultados de ASP.NET](cache-aspnet-output-cache-provider.md)
-   [Habilitar diagnósticos de memoria caché](cache-how-to-monitor.md#enable-cache-diagnostics) para que pueda [supervisar](cache-how-to-monitor.md) el mantenimiento de la memoria caché. Puede ver las mediciones en el Portal de Azure y también se pueden [descargar y revisar](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) con las herramientas de su elección.
-   Consulte la [documentación del cliente de caché StackExchange.Redis][].
    -   Caché de Azure Redis puede tener acceso desde muchos clientes Redis y lenguajes de desarrollo. Para obtener más información, consulte [http://redis.io/clients][].
-   Caché de Azure Redis también puede usarse con herramientas como Redsmin Redis administrador y escritorio y servicios de terceros.
    -   Para obtener más información sobre Redsmin, consulte [cómo recuperar una cadena de conexión Redis Azure y usarlo con Redsmin][].
    -   Acceso e inspeccionar los datos en caché de Azure Redis con un gráfico mediante [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
-   Consulte la documentación [redis][] y obtenga información sobre [redis tipos de datos][] y [una introducción de quince minutos para Redis tipos de datos][].



<!-- INTRA-TOPIC LINKS -->
[Pasos siguientes]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Crear la caché]: #create-cache
[Configure the cache]: #enable-caching
[Configurar a los clientes de caché]: #NuGet
[Working with Caches]: #working-with-caches
[Conectarse a la caché]: #connect-to-cache
[Agregar y recuperar objetos de la caché]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://Redis.IO/Clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Cómo recuperar una cadena de conexión Redis Azure y usarlo con Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modelo de configuración de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalles de los precios de caché]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Documentación de cliente de caché StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis tipos de datos]: http://redis.io/topics/data-types
[una introducción quince minutos para Redis tipos de datos]: http://redis.io/topics/data-types-intro

[¿Cómo funcionan las cadenas de la aplicación y cadenas de conexión]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


