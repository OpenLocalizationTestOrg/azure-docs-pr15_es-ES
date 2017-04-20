<properties
 pageTitle="Cómo administrar la caducidad del contenido de servicios de nube o aplicaciones Web de Azure, ASP.NET e IIS en Azure CDN | Microsoft Azure"
 description="Describe cómo administrar la caducidad del contenido de servicio de nube de CDN de Azure"
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# <a name="how-to-manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Cómo administrar la caducidad del contenido de servicios de nube o aplicaciones Web de Azure, ASP.NET o IIS en CDN de Azure

> [AZURE.SELECTOR]
- [Servicios de nube o aplicaciones de Azure Web, ASP.NET o IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Servicio de almacenamiento de blobs de Windows Azure](cdn-manage-expiration-of-blob-content.md)

Archivos desde cualquier servidor web de origen accesible públicamente pueden almacenarse en caché en Azure CDN hasta que transcurre el tiempo de vida (TTL).  El [encabezado de *Control de caché* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) en la respuesta HTTP desde el servidor de origen determina el valor de TTL.  En este artículo se describe cómo configurar `Cache-Control` encabezados para aplicaciones Web de Azure, servicios de nube de Azure, las aplicaciones y los sitios de Internet Information Services, que se han configurado de manera similar.

>[AZURE.TIP] Puede no establecer TTL en un archivo.  En este caso, CDN Azure aplica automáticamente un TTL predeterminado de siete días.
>
>Para obtener más información acerca del funcionamiento de Azure CDN para acelerar el acceso a archivos y otros recursos, vea la [Información general de CDN de Azure](./cdn-overview.md).

## <a name="setting-cache-control-headers-in-configuration"></a>Establecer encabezados de Control de caché de configuración

En contenido estático, como imágenes y hojas de estilo, puede controlar la frecuencia de actualización modificando los archivos **applicationHost.config** o **web.config** para la aplicación web.  El elemento **system.webServer\staticContent\clientCache** en el archivo de configuración se establece la `Cache-Control` encabezado para el contenido. Para **web.config**, la configuración afectará a todo el contenido en la carpeta y todas sus subcarpetas, a menos que se reemplaza en el nivel de la subcarpeta.  Por ejemplo, puede establecer un valor predeterminado time to live en la raíz para que todo el contenido estático en caché para 3 días, pero tiene una subcarpeta que tiene el contenido más variable con una configuración de caché de 6 horas.  Para **applicationHost.config**, todas las aplicaciones en el sitio se verá afectadas, pero se pueden reemplazar en archivos **web.config** de las aplicaciones.

La siguiente muestra XML y ejemplo de configuración **clientCache** para especificar una duración máxima de 3 días:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Especificar **UseMaxAge** agrega un `Cache-Control: max-age=<nnn>` según el valor especificado en el atributo **CacheControlMaxAge** encabezado a la respuesta. El formato del intervalo de tiempo es para el atributo **cacheControlMaxAge** es <days>. <hours>:<min>:<sec>. Para obtener más información sobre el nodo **clientCache** , consulte [caché cliente <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Establecer encabezados de Control de caché en código.

Para las aplicaciones ASP.NET, puede establecer la CDN comportamiento almacenamiento en caché mediante programación estableciendo **la propiedad** . Para obtener más información sobre **la propiedad** , consulte [Propiedad](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) y [Clase HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Si desea almacenar en caché contenido de la aplicación de ASP.NET mediante programación, asegúrese de el contenido se marca como caché estableciendo HttpCacheability en *público*. Además, asegúrese de que se establece un control de validación de caché. El control de validación de caché puede ser una última modificación establece marca de tiempo que se establece mediante una llamada SetLastModified o un valor de etag llamando SetETag. Si lo desea, también puede especificar una fecha de caducidad de caché llamando SetExpires o puede confiar en la heurística de caché predeterminada que se describe más adelante en este documento.  

Por ejemplo, en caché del contenido de una hora, agregue lo siguiente:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Pasos siguientes

- [Lea los detalles sobre el elemento **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [Leer la documentación de **la propiedad**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
- [Lea la documentación de la **Clase HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  
