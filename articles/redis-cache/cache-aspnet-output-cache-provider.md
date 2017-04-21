<properties
    pageTitle="Proveedor de caché de caché de resultados de ASP.NET"
    description="Aprenda a caché con Azure Redis caché de resultados de páginas de ASP.NET"
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
    ms.date="09/27/2016"
    ms.author="sdanie" />

# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Proveedor de caché de resultados de ASP.NET para Azure Redis caché

El proveedor de caché de resultados Redis es un mecanismo de almacenamiento de fuera de proceso de datos de la caché de resultados. Estos datos están específicamente para las respuestas HTTP completas (página de la caché de resultados). El proveedor conecta en el punto de capacidad de ampliación del proveedor del caché de resultados nueva que se introdujo en ASP.NET 4.

Para utilizar el proveedor de caché de resultados Redis, configurar primero la memoria caché y, a continuación, configure su aplicación ASP.NET mediante el paquete Redis NuGet de proveedor de caché de resultados. Este tema proporciona instrucciones sobre cómo configurar la aplicación para utilizar el proveedor de caché de resultados Redis. Para obtener más información sobre cómo crear y configurar una instancia de Azure Redis caché, consulte [crear una caché](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Almacenar el resultado de la página ASP.NET en la caché.

Para configurar una aplicación de cliente en Visual Studio mediante el paquete Redis NuGet de proveedor de caché de resultados, haga clic en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**.

![Caché de Azure Redis administrar paquetes de NuGet](./media/cache-aspnet-output-cache-provider/redis-cache-manage-nuget-menu.png)

Escriba **RedisOutputCacheProvider** en el cuadro de texto de búsqueda, seleccione desde los resultados y haga clic en **instalar**.

![Azure Redis proveedor de caché de resultados de caché](./media/cache-aspnet-output-cache-provider/redis-cache-page-output-provider.png)

El paquete de NuGet de proveedor de caché de resultados Redis tiene una dependencia en el paquete StackExchange.Redis.StrongName. Si no está presente en el proyecto que se van a instalar el paquete StackExchange.Redis.StrongName. Tenga en cuenta que además del paquete de StackExchange.Redis.StrongName con nombre seguro también hay la versión de StackExchange.Redis no-nombre seguro. Si su proyecto está usando la versión de StackExchange.Redis no-nombre seguro, que debe desinstalar, antes o después de instalar el paquete Redis NuGet de proveedor de caché de resultados, en caso contrario, se le obtener conflictos de nombres en el proyecto. Para obtener más información acerca de estos paquetes, vea [Configurar .NET clientes de caché](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

El paquete NuGet descargas y agrega las referencias a ensamblados necesarios y la sección siguiente en el archivo web.config que contiene la configuración necesaria para la aplicación de ASP.NET para utilizar el proveedor de caché de resultados Redis.

    <caching>
      <outputCachedefault Provider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

La sección comentario, proporciona un ejemplo de los atributos y valores de ejemplo para cada atributo.

Configure los atributos con los valores de la hoja de caché en el portal de Microsoft Azure y los demás valores como desee. Para obtener instrucciones sobre cómo acceder a sus propiedades de caché, consulte [Configurar Redis configuración de la caché](cache-configure.md#configure-redis-cache-settings).

-   **host** : especifique el extremo de la caché.
-   **puerto** : use el puerto no SSL o su SSL el puerto, según la configuración de ssl.
-   **accessKey** : usar la clave principal o secundaria de la memoria caché.
-   **ssl** : true si desea proteger comunicaciones de caché/cliente con ssl; en caso contrario false. Asegúrese de especificar el puerto correcto.
    -   El puerto SSL no está deshabilitado de forma predeterminada para la caché de nuevas. Especifique true para esta opción Usar el puerto SSL. Para obtener más información acerca de cómo habilitar el puerto no SSL, vea la sección de [Puertos de acceso](cache-configure.md#access-ports) en el tema [Configurar una caché](cache-configure.md) .
-   **databaseId** : especificar qué base de datos para los datos de salida de la memoria caché. Si no se especifica, se usa el valor predeterminado de 0.
-   **applicationName** : claves se almacenan en redis como <AppName>_<SessionId>_Data. Esto permite que varias aplicaciones compartir la misma clave. Este parámetro es opcional y si no se proporcionan se usa un valor predeterminado.
-   **connectionTimeoutInMilliseconds** : esta opción permite invalidar la configuración connectTimeout en el cliente StackExchange.Redis. Si no se especifica, se usa la configuración de connectTimeout predeterminado de 5000. Para obtener más información, vea [modelo de configuración de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** : esta opción permite invalidar la configuración de syncTimeout en el cliente StackExchange.Redis. Si no se especifica, se usa la configuración de syncTimeout predeterminada de 1000. Para obtener más información, vea [modelo de configuración de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Agregar una directiva a cada página que desee en caché el resultado.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

En este ejemplo datos de la página en caché permanecerá en la caché durante 60 segundos y una versión diferente de la página se almacena en caché para cada combinación de parámetros. Para obtener más información acerca de la directiva, consulte [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Una vez que se realizan estos pasos, la aplicación está configurada para utilizar el proveedor de caché de resultados Redis.

## <a name="next-steps"></a>Pasos siguientes

Consulte el [Proveedor de estado de sesión de ASP.NET para Azure Redis caché](cache-aspnet-session-state-provider.md).
