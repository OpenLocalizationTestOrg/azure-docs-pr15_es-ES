<properties
    pageTitle="Consultar el índice de búsqueda de Azure con la API de REST | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Crear una consulta de búsqueda de Azure y use los parámetros de búsqueda para filtrar y ordenar los resultados de búsqueda."
    services="search"
    documentationCenter=""
    manager="jhubbard"
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index-using-the-rest-api"></a>Consultar el índice de búsqueda de Azure con la API de REST
> [AZURE.SELECTOR]
- [Información general](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

En este artículo le mostrará cómo consultar un índice con la [API de REST de búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Antes de comenzar este tutorial, debe tener ya [creó un índice de búsqueda de Azure](search-what-is-an-index.md) y [se rellena con datos](search-what-is-data-import.md).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>Puedo. Identificar la clave de api de consulta de su servicio de búsqueda de Azure
Un componente clave de cada operación de búsqueda con la API de REST de búsqueda de Azure es la *clave de api* generado para el servicio que se aprovisione. Tener una clave válida establece la confianza, de forma por solicitud, entre la aplicación que envía la solicitud y el servicio que la administra.

1. Para buscar claves api de su servicio debe iniciar sesión en el [Portal de Azure](https://portal.azure.com/)
2. Vaya a módulo de su servicio de búsqueda de Azure
3. Haga clic en el icono "Teclas"

Su servicio tendrá *claves de administración* y *consulta*.

 - Las *claves de administración* de primario y secundario conceder derechos completos a todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indizadores y orígenes de datos. Existen dos claves para que puedan continuar usar la clave secundaria si decide regenerar la clave principal y viceversa.
 - Las *teclas de consulta* conceder acceso de solo lectura a índices y documentos y se distribuye normalmente a aplicaciones cliente que emiten solicitudes de búsqueda.

A los efectos de la consulta a un índice, puede usar una de las claves de consulta. También pueden utilizarse las claves de administración de consultas, pero debe usar una clave de consulta en el código de aplicación como esta mejor sigue el [principio de menor privilegio](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-formulate-your-query"></a>II. Formular una consulta
Hay dos maneras de [Buscar en el índice con la API de REST](https://msdn.microsoft.com/library/azure/dn798927.aspx). Una forma es emitir una solicitud de HTTP POST donde se definirá los parámetros de la consulta en un objeto JSON en el cuerpo de la solicitud. La otra forma es emitir una solicitud HTTP GET donde se definirá los parámetros de la consulta en la dirección URL de la solicitud. Tenga en cuenta que una publicación tiene más [más flexible límites](https://msdn.microsoft.com/library/azure/dn798927.aspx) en el tamaño de los parámetros de consulta que GET. Por este motivo, recomendamos usar entrada a menos que tenga circunstancias especiales donde utilizando GET sería más adecuado.

Para publicar y obtener, debe proporcionar el *nombre de servicio*, *índice*y la correcta *versión API* (la versión actual de la API es `2015-02-28` en el momento de la publicación de este documento) en la dirección URL de la solicitud. Para obtener, la *cadena de consulta* al final de la dirección URL será donde proporcionar los parámetros de consulta. Siga leyendo para el formato de dirección URL.

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

El formato de entrada es el mismo, pero con solo api versión en los parámetros de cadena de consulta.



#### <a name="example-queries"></a>Consultas de ejemplo

Estas son algunas consultas de ejemplo en un índice con el nombre "hoteles". Estas consultas se muestran en formato GET y POST.

Buscar todo el índice para el término 'presupuesto' y devolver solo los `hotelName` campo:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Aplicar un filtro al índice para buscar más económico que 150 dólares por noche hoteles y devolver la `hotelId` y `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Buscar todo el índice, ordenar por un campo específico (`lastRenovationDate`) en orden descendente, tomar los dos primeros resultados y mostrar solo `hotelName` y `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="iii-submit-your-http-request"></a>III. Enviar su solicitud de HTTP
Ahora que ha formular la consulta como parte de la dirección URL de la solicitud HTTP (para obtener) o cuerpo (para publicación), puede definir los encabezados de la solicitud y enviar la consulta.

#### <a name="request-and-request-headers"></a>Solicitud y los encabezados de solicitud
Debe definir dos encabezados de la solicitud GET o tres para publicación:
1. La `api-key` encabezado debe estar establecido en la consulta clave que encuentra en el paso anteriores. Tenga en cuenta que también puede usar una clave de administración como el `api-key` encabezado, pero se recomienda usar una clave de consulta como exclusivamente concede acceso de solo lectura a índices y documentos.
2. La `Accept` encabezado debe estar establecido en `application/json`.
3. Para registrar sólo, el `Content-Type` también se debe establecer el encabezado en `application/json`.

Siga leyendo para que una solicitud HTTP GET para buscar el índice de "hoteles" mediante la API de REST de búsqueda de Azure mediante una consulta simple que busca el término "granja.".

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Aquí es la misma consulta de ejemplo, esta vez utilizando HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Da una solicitud de consulta correcta de un código de estado de `200 OK` y los resultados de búsqueda se devuelven como JSON en el cuerpo de la respuesta. Aquí es lo que los resultados de la anterior consulta aspecto, suponiendo que el índice "hoteles" se rellena con los datos de ejemplo en [La importación de datos de uso de la API de REST de búsqueda de Azure](search-import-data-rest-api.md) (tenga en cuenta que se ha formateado el JSON para claridad).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Para obtener más información, visite la sección "Respuesta" de [Buscar documentos](https://msdn.microsoft.com/library/azure/dn798927.aspx). Para obtener más información sobre otros códigos de estado HTTP que se podría devolver en caso de error, consulte [códigos de estado HTTP (búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798925.aspx).
