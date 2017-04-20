<properties 
    pageTitle="Estado de sesión con la caché de Azure Redis en servicio de la aplicación de Azure" 
    description="Obtenga información sobre cómo usar el servicio de caché de Azure para admitir el estado de sesión ASP.NET almacenamiento en caché." 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="Rick-Anderson" 
    manager="wpickett" 
    editor="none"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="get-started-article" 
    ms.date="06/27/2016" 
    ms.author="riande"/>


# <a name="session-state-with-azure-redis-cache-in-azure-app-service"></a>Estado de sesión con la caché de Azure Redis en servicio de la aplicación de Azure


En este tema se explica cómo usar el servicio de caché de Azure Redis para el estado de la sesión.

Si la aplicación web de ASP.NET utiliza el estado de sesión, debe configurar un proveedor de estado de sesión externos (el servicio de caché Redis o un proveedor de estado de sesión de SQL Server). Si utiliza el estado de sesión y no usa un proveedor de servicios externo, se limita a una instancia de la aplicación web. El servicio de caché Redis es el más rápido y sencillo habilitar.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

##<a id="createcache"></a>Crear la caché
Siga [estas instrucciones](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) para crear la caché.

##<a id="configureproject"></a>Agregar el paquete de RedisSessionStateProvider NuGet a la aplicación web
Instalar la NuGet `RedisSessionStateProvider` paquete.  Use el comando siguiente para instalar desde la consola del Administrador de paquete (**Herramientas** > **Administrador de paquetes de NuGet** > **Consola del Administrador de paquete**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Instalar **Herramientas**de > **Administrador de paquetes de NuGet** > **Administrar paquetes Consejo para la solución**, busque `RedisSessionStateProvider`.

Para obtener más información, consulte la [página de NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) y [Configurar el cliente de caché](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>Modificar el archivo Web.Config
Además de las referencias de ensamblado de caché, el paquete NuGet agrega entradas de código auxiliar en el archivo *web.config* . 

1. Abra *web.config* y busque el elemento **sessionState** .

1. Escriba los valores para `host`, `accessKey`, `port` (el puerto SSL debe ser 6380) y establecer `SSL` a `true`. Estos valores pueden obtenerse el módulo [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) de la instancia de caché. Para obtener más información, vea [conectarse a la memoria caché](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Observe que el puerto SSL no está deshabilitado de forma predeterminada para la caché de nuevas. Para obtener más información acerca de cómo habilitar el puerto no SSL, vea la sección de [Puertos de acceso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) en el tema [Configurar una caché en Azure Redis caché](https://msdn.microsoft.com/library/azure/dn793612.aspx) . El siguiente marcado muestra los cambios en el archivo *web.config* , específicamente los cambios en *puerto*, *host*, accessKey*, y *ssl *.

          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;


##<a id="usesessionobject"></a>Usar el objeto de sesión en el código
El último paso es empezar a usar el objeto de sesión en el código ASP.NET. Agregar objetos al estado de sesión a través del método **método Session.Add** . Este método usa pares de valor clave para almacenar elementos en la caché de estado de sesión.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

El siguiente código recupera este valor de estado de sesión.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue; 

También puede usar la caché Redis a objetos de caché de la aplicación web. Para obtener más información, vea [aplicación de película MVC con Azure Redis caché en 15 minutos](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Para obtener más detalles acerca de cómo usar el estado de la sesión ASP.NET, vea [Información general sobre el estado de sesión de ASP.NET][].

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

  *Por [Enriquecido Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net  
  [Información general del estado de la sesión de ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 
