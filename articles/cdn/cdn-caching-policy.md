<properties
    pageTitle="CDN de directiva en la extensión de servicios de medios de caché"
    description="Este tema ofrece una descripción general de una CDN directiva de extensión de servicios de medios de caché."
    services="media-services,cdn"
    documentationCenter=".NET"
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="juliako"/>
 
#<a name="cdn-caching-policy-in-media-services-extension"></a>CDN de directiva en la extensión de servicios de medios de caché

Azure Media Services proporciona HTTP basada en Streaming adaptación y progresiva descarga. Transmisión basados en HTTP es muy scalable con beneficios de almacenamiento en caché en proxy y capas CDN, así como caché del cliente. Transmisión extremos ofrece capacidades de streaming generales y también la configuración de encabezados de caché HTTP. Transmisión extremos establece el Control de caché HTTP: max edad y caduca encabezados. Puede obtener más información para los encabezados de caché HTTP de [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

##<a name="default-caching-headers"></a>Encabezados de almacenamiento en caché predeterminada

De forma predeterminada extremos streaming aplicarán encabezados de caché de 3 días a petición streaming datos (fragmentos de medios real/fragmentos) y manifest(playlist). Para la transmisión de live extremos streaming aplicarán encabezados de caché de 3 días para los datos (fragmentos de medios real/fragmentos) y 2 segundos caché encabezado para las solicitudes de manifest(playlist). Cuando programa directo se convierte en a petición (archivo live) aplicarán streaming encabezados de caché a petición.

##<a name="azure-cdn-integration"></a>Integración de Azure CDN

Azure Media Services proporciona [CDN integrado](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para extremos de transmisión. Encabezados de control de caché se aplica en la misma manera como streaming extremos de CDN habían habilitado streaming extremos. Usos CDN Azure transmisión extremo configurado valores de caché para definir el tiempo de vida de los objetos almacenados en caché internamente y también utiliza este valor para establecer los encabezados de caché de la entrega. Cuando usa CDN habilitado extremos streaming no se recomienda para establecer los valores de caché pequeña. Configurar valores pequeños se disminuirá el rendimiento y reducir el beneficio de CDN. No se permite establecer encabezados de caché menor de 600 segundos para CDN habían habilitado streaming extremos.

>[AZURE.IMPORTANT] Integración de Azure Media Services con Azure CDN se implementa en **Azure CDN de Verizon**.  Si desea usar **Azure CDN de Akamai** para Azure Media Services, debe [Configurar el extremo de forma manual](cdn-create-new-endpoint.md).  Para obtener más información sobre las características de Azure CDN, vea la [información general CDN](cdn-overview.md).

##<a name="configuring-cache-headers-with-azure-media-services"></a>Configuración de caché de encabezados con Azure Media Services

Puede usar el portal de administración de Azure o Azure Media Services API para configurar los valores de encabezado de la memoria caché.

1. Para configurar los encabezados de caché con administración de portal consulte [cómo administrar extremos Streaming](../media-services/media-services-portal-manage-streaming-endpoints.md) sección Configuración del extremo de transmisión.
2. API de REST, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl)de servicios multimedia de Azure.
3. .NET SDK, [StreamingEndpointCacheControl propiedades](http://go.microsoft.com/fwlink/?LinkId=615302)de servicios multimedia de Azure.

##<a name="cache-configuration-precedence-order"></a>Orden de prioridad de configuración de caché

1. Valor de caché de Azure Media Services configurado reemplaza el valor predeterminado.
2. Si no hay ninguna configuración manual, se aplica valores predeterminados.
3. Caché de 2 segundos de forma predeterminada se aplica encabezados para live manifest(playlist) streaming independientemente de la configuración multimedia de Azure o el almacenamiento de Azure y reemplazar este valor no está disponible.
