<properties
    pageTitle="Información general de Azure CDN | Microsoft Azure"
    description="Obtenga información sobre qué es la red de entrega de contenido (CDN) de Azure y cómo usarlo para proporcionar contenido de ancho de banda almacenando blobs y en contenido estático."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/30/2016"
    ms.author="casoper"/>

# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Información general de la red de Azure entrega de contenido (CDN)

> [AZURE.NOTE] ¿Qué es la red de entrega de contenido (CDN) de Azure, este documento describe cómo funciona y las características de cada producto CDN de Azure.  Si desea omitir esta información e ir directamente a un tutorial acerca de cómo crear un extremo CDN, vea [Usar CDN de Azure](cdn-create-new-endpoint.md).  Si desea ver una lista de ubicaciones de nodo CDN actuales, vea [Ubicaciones de Azure CDN POP](cdn-pop-locations.md).

La red de entrega de contenido (CDN) de Azure almacena contenido web estático en ubicaciones situados de forma estratégica para proporcionar el rendimiento máximo de entrega de contenido a los usuarios.  La CDN ofrece a los desarrolladores una solución global para proporcionar contenido de ancho de banda almacenando el contenido en los nodos físicos en todo el mundo. 

Las ventajas de usar la CDN activos del sitio web de caché incluyen:

- Mejorar el rendimiento y usuario experiencia para los usuarios finales, especialmente cuando se utiliza aplicaciones donde varios ida y vuelta deben cargar contenido.
- Gran escala mejor controlar instantánea carga es alta, como en el inicio de un evento de lanzamiento del producto.
- Distribuir las solicitudes de usuario y servir contenido de servidores perimetrales, menos tráfico se envía al origen.


## <a name="how-it-works"></a>Cómo funciona

![Información general CDN](./media/cdn-overview/cdn-overview.png)

1. Un usuario (Ana) solicita un archivo (también denominado un activo) con una dirección URL con un nombre de dominio especiales, como por ejemplo `<endpointname>.azureedge.net`.  DNS enruta la solicitud a la mejor efectuar la ubicación del punto de presencia (POP).  Suele ser el POP geográfico más cercano al usuario.

2. Si los servidores perimetrales en POP no tiene el archivo en su caché, el servidor perimetral solicita el archivo desde el origen.  El origen puede ser una aplicación Web de Azure, servicio de nube de Azure, cuenta de almacenamiento de Azure o cualquier servidor web accesible públicamente.

3. El origen devuelve el archivo en el servidor perimetral, incluidos los encabezados HTTP opcionales que describe tiempo de vida del archivo (TTL).

4. El servidor perimetral guarda el archivo y devuelve el archivo al solicitante original (Ana).  El archivo permanece en caché en el servidor perimetral hasta que expire el valor de TTL.  Si el origen no especifica un valor de TTL, el TTL predeterminado es siete días.

5. Los usuarios adicionales pueden solicitar el mismo archivo con esa misma dirección URL y también dirigirá a ese mismo POP.

6. Si el valor TTL para el archivo no ha caducado, el servidor perimetral devuelve el archivo de la memoria caché.  El resultado es una experiencia de usuario más rápida, mayor capacidad de respuesta.


## <a name="azure-cdn-features"></a>Características de Azure CDN

Hay tres productos de Azure CDN: **Azure CDN estándar de Akamai**, **Azure CDN estándar de Verizon**y **Azure CDN Premium de Verizon**.  La siguiente tabla enumeran las características disponibles con cada producto.

|       | Akamai estándar | Verizon estándar | Verizon Premium |
|-------|-----------------|------------------|-----------------|
| Servicios de integración fácil con Azure como [almacenamiento de información](cdn-create-a-storage-account-with-cdn.md), [Servicios de nube](cdn-cloud-service-with-cdn.md), [Aplicaciones Web](../app-service-web/cdn-websites-with-cdn.md)y [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;**|
| Administración a través de la [API de REST](https://msdn.microsoft.com/library/mt634456.aspx), [. NET](./cdn-app-dev-net.md), [Node.js](./cdn-app-dev-node.md)o [PowerShell](./cdn-manage-powershell.md). | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Compatibilidad con HTTPS | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Equilibrio de carga | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Protección de [DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| Pila de IPv4 o IPv6 doble | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Soporte de nombre de dominio personalizado](cdn-map-content-to-custom-domain.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Almacenamiento en caché de cadena de consulta](cdn-query-string.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Filtrado de geo](cdn-restrict-access-by-country.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Purgar rápida](cdn-purge-endpoint.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [Cargar previamente de activos](cdn-preload-endpoint.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Análisis de Core](cdn-analyze-usage-patterns.md) |  | **& #x 2713;** | **& #x 2713;** |
| [Compatibilidad con HTTP/2](https://msdn.microsoft.com/library/mt762901.aspx) | **& #x 2713;**  |  |  |
| [Informes HTTP avanzados](cdn-advanced-http-reports.md) | | | **& #x 2713;** |
| [Estadísticas en tiempo real](cdn-real-time-stats.md) | | | **& #x 2713;** |
| [Alertas en tiempo real](cdn-real-time-alerts.md) | | | **& #x 2713;** |
| [Motor de entrega de contenido personalizable basada en la regla,](cdn-rules-engine.md) | | | **& #x 2713;** |
| Configuración de caché/encabezado (con el [motor de reglas](cdn-rules-engine.md))  | | | **& #x 2713;** |
| Dirección URL redirect/reescritura (con el [motor de reglas](cdn-rules-engine.md)) | | | **& #x 2713;** |
| Reglas de dispositivos móviles (con el [motor de reglas](cdn-rules-engine.md))  | | | **& #x 2713;** |

>[AZURE.TIP] ¿Hay una característica que le gustaría ver en Azure CDN?  [Envíenos sus comentarios](https://feedback.azure.com/forums/169397-cdn)! 

## <a name="next-steps"></a>Pasos siguientes

Para empezar con CDN, vea [Usar CDN de Azure](./cdn-create-new-endpoint.md).

Si es un cliente CDN existente, ahora puede administrar los extremos CDN a través del [portal de Microsoft Azure](https://portal.azure.com) o con [PowerShell](cdn-manage-powershell.md).

Para ver la CDN en acción, consulte el [vídeo de la sesión de generación de 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Aprenda a automatizar CDN de Azure con [.NET](./cdn-app-dev-net.md) o [Node.js](./cdn-app-dev-node.md).

Para obtener información sobre precios, consulte [CDN precios](https://azure.microsoft.com/pricing/details/cdn/).
