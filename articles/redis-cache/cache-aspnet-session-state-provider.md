<properties
    pageTitle="Proveedor de estado de la sesión de caché ASP.NET | Microsoft Azure"
    description="Obtenga información sobre cómo almacenar el estado de la sesión de ASP.NET con Azure Redis caché"
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
    ms.date="09/01/2016"
    ms.author="sdanie" />

# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Proveedor de estado de la sesión de ASP.NET para Azure Redis caché

Caché de Redis Azure proporciona un proveedor de estado de sesión que puede usar para almacenar el estado de la sesión en la memoria caché en lugar de en la memoria o en una base de datos de SQL Server. Para utilizar el proveedor de estado de sesión almacenamiento en caché, configurar primero la memoria caché y, a continuación, configure la aplicación de ASP.NET para caché mediante el paquete Redis NuGet de estado de sesión de caché.

A menudo no es práctica en una aplicación de nube reales para evitar almacenar algún tipo de estado de una sesión de usuario, pero algunos enfoques afectan al rendimiento y escalabilidad más que otras personas. Si tiene que almacenar el estado, es la mejor solución mantener la cantidad de estado pequeño y almacenar en cookies. Si no es factible, la siguiente solución mejor es utilizar el estado de la sesión ASP.NET con un proveedor de caché distribuida en memoria. La solución peor un rendimiento y escalabilidad es usar una base de datos copia el proveedor de estado de sesión. Este tema proporciona instrucciones sobre cómo usar el proveedor de estado de la sesión de ASP.NET para Azure Redis caché. Para obtener información sobre otras opciones de estado de sesión, consulte [Opciones de estado de la sesión de ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Almacenar el estado de la sesión ASP.NET en la caché.

Para configurar una aplicación de cliente en Visual Studio mediante el paquete Redis NuGet de estado de sesión de caché, haga clic en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**.

![Caché de Azure Redis administrar paquetes de NuGet](./media/cache-aspnet-session-state-provider/redis-cache-manage-nuget-menu.png)

Escriba **RedisSessionStateProvider** en el cuadro de texto de búsqueda, seleccione desde los resultados y haga clic en **instalar**.

>[AZURE.IMPORTANT] Si está utilizando la característica de agrupación desde el nivel premium, debe usar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 o posterior, o una excepción se inicia. Este es un cambio de interrupción; Para obtener más información, vea [v2.0.0 Cambiar detalles de romper](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

![Proveedor de estado de la sesión de caché de Azure Redis](./media/cache-aspnet-session-state-provider/redis-cache-session-state-provider.png)

El paquete de NuGet de proveedor de estado de sesión Redis tiene una dependencia en el paquete StackExchange.Redis.StrongName. Si no está presente en el proyecto que se van a instalar el paquete StackExchange.Redis.StrongName. Tenga en cuenta que además del paquete de StackExchange.Redis.StrongName con nombre seguro también hay la versión de StackExchange.Redis no-nombre seguro. Si su proyecto está usando la versión de StackExchange.Redis no-nombre seguro, que debe desinstalar, antes o después de instalar el paquete Redis NuGet de proveedor de estado de sesión, en caso contrario, se le obtener conflictos de nombres en el proyecto. Para obtener más información acerca de estos paquetes, vea [Configurar .NET clientes de caché](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

El paquete de NuGet descarga y agrega el ensamblado necesario hace referencia y se agrega que la siguiente agrega la sección siguiente en el archivo web.config que contiene la configuración necesaria para la aplicación ASP.NET para utilizar el proveedor de estado de sesión de caché Redis.

    <sessionState mode="Custom" customProvider="MySessionStateStore">
        <providers>
        <!--
        <add name="MySessionStateStore"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
        />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
    </sessionState>

La sección comentario, proporciona un ejemplo de los atributos y valores de ejemplo para cada atributo.

Configure los atributos con los valores de la hoja de caché en el Portal de Microsoft Azure y los demás valores como desee. Para obtener instrucciones sobre cómo acceder a sus propiedades de caché, consulte [Configurar Redis configuración de la caché](cache-configure.md#configure-redis-cache-settings).

-   **host** : especifique el extremo de la caché.
-   **puerto** : use el puerto no SSL o su SSL el puerto, según la configuración de ssl.
-   **accessKey** : usar la clave principal o secundaria de la memoria caché.
-   **ssl** : true si desea proteger comunicaciones de caché/cliente con ssl; en caso contrario false. Asegúrese de especificar el puerto correcto.
    -   El puerto SSL no está deshabilitado de forma predeterminada para la caché de nuevas. Especifique true para esta opción Usar el puerto SSL. Para obtener más información acerca de cómo habilitar el puerto no SSL, vea la sección de [Puertos de acceso](cache-configure.md#access-ports) en el tema [Configurar una caché](cache-configure.md) .
-   **throwOnError** : true si desea una excepción en caso de un error o falso si desea que la operación de un error. Puede comprobar si un error comprobando la propiedad Microsoft.Web.Redis.RedisSessionStateProvider.LastException estática. El valor predeterminado es true.
-   **retryTimeoutInMilliseconds** – operaciones que no se vuelve a intentar durante este intervalo, que se especifica en milisegundos. Se produce el primer reintento después de 20 milisegundos y reintentos producirán cada segundo hasta que expire el intervalo de retryTimeoutInMilliseconds. Inmediatamente después de este intervalo, la operación se vuelve a intentar una vez final. Si aún no la operación, la excepción volver a la llamada, dependiendo de la configuración de throwOnError. El valor predeterminado es 0, lo que no significa reintentos.
-   **databaseId** : especifica la caché de base de datos que se usa para mostrar los datos. Si no se especifica, se usa el valor predeterminado de 0.
-   **applicationName** : claves se almacenan en redis como `{<Application Name>_<Session ID>}_Data`. Esto permite que varias aplicaciones compartir la misma clave. Este parámetro es opcional y si no se proporcionan se usa un valor predeterminado.
-   **connectionTimeoutInMilliseconds** : esta opción permite invalidar la configuración connectTimeout en el cliente StackExchange.Redis. Si no se especifica, se usa la configuración de connectTimeout predeterminado de 5000. Para obtener más información, vea [modelo de configuración de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-   **operationTimeoutInMilliseconds** : esta opción permite invalidar la configuración de syncTimeout en el cliente StackExchange.Redis. Si no se especifica, se usa la configuración de syncTimeout predeterminada de 1000. Para obtener más información, vea [modelo de configuración de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Para obtener más información acerca de estas propiedades, consulte el anuncio de entrada de blog original en [Anuncio de proveedor de estado de sesión ASP.NET para Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

No olvide comentario la sección de proveedor estándar de estado sesión de proceso en el archivo web.config.

    <!-- <sessionState mode="InProc"
         customProvider="DefaultSessionProvider">
         <providers>
            <add name="DefaultSessionProvider"
                  type="System.Web.Providers.DefaultSessionStateProvider,
                        System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                        PublicKeyToken=31bf3856ad364e35"
                  connectionStringName="DefaultConnection" />
          </providers>
    </sessionState> -->

Una vez que se realizan estos pasos, la aplicación está configurada para utilizar el proveedor de estado de sesión de caché Redis. Cuando se utiliza el estado de sesión en la aplicación, se almacenarán en una instancia de Azure Redis caché.

>[AZURE.NOTE] Observe que los datos almacenados en la memoria caché deben ser serializables, a diferencia de los datos que se pueden almacenar en el valor predeterminado proveedor de estado de sesión ASP.NET en memoria. Cuando se utiliza el proveedor de estado de sesión para Redis, asegúrese de que los tipos de datos almacenados en el estado de sesión son serializables.

## <a name="aspnet-session-state-options"></a>Opciones de estado de la sesión de ASP.NET

- En proveedor de estado de la sesión de memoria - almacena el estado de sesión en la memoria. La ventaja de usar este proveedor es rápida y sencilla. Sin embargo no puede escalar las aplicaciones Web si está utilizando el proveedor de memoria ya que no se distribuye.

- Proveedor de estado de sesión de SQL Server - este proveedor almacena el estado de sesión en Sql Server. Debe utilizar este proveedor si desea conservar el estado de sesión en un almacenamiento persistente. Puede ajustar el tamaño de la aplicación Web, pero con Sql Server para sesión tendrá un impacto de rendimiento en la aplicación Web.

- Distribuido en memoria sesión estado proveedor como Redis proveedor de estado de caché de sesión - este proveedor le ofrece lo mejor de ambos ámbitos. La aplicación Web puede tener un proveedor de estado de sesión simple, rápida y scalable. Sin embargo debe tener en cuenta que almacena el estado de sesión en la memoria caché y la aplicación tiene que tener en cuenta todas las características asociadas cuando se trata de una caché distribuida en memoria, como errores de red transitorias. Prácticas recomendadas sobre el uso de la memoria caché, vea [Guía de almacenamiento en caché](../best-practices-caching.md) de Microsoft Patterns & prácticas de [Diseño de aplicaciones de nube de Azure y Guía de implementación](https://github.com/mspnp/azure-guidance).

Para obtener más información sobre el estado de sesión y otras prácticas recomendadas, consulte [Prácticas recomendadas de desarrollo de Web (aplicaciones de nube de creación reales con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Pasos siguientes

Consulte el [Proveedor de caché de resultados de ASP.NET para Azure Redis caché](cache-aspnet-output-cache-provider.md).
