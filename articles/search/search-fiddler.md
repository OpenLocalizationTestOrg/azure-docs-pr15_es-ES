<properties
    pageTitle="Cómo utilizar Fiddler para evaluar y probar las API de REST de búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Utilizar Fiddler un enfoque sin código para comprobar la disponibilidad de búsqueda de Azure y probando las API de REST."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="10/17/2016"
    ms.author="heidist"/>

# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>Utilizar Fiddler para evaluar y probar las API de REST de búsqueda de Azure
> [AZURE.SELECTOR]
- [Información general](search-query-overview.md)
- [Búsqueda del explorador](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

En este artículo se explica cómo usar Fiddler, disponible como [descarga gratuita en Telerik](http://www.telerik.com/fiddler), las solicitudes HTTP problema a y ver respuestas con la API de REST de búsqueda de Azure, sin tener que escribir código. Búsqueda de Azure está totalmente administrados, hospedado el servicio de búsqueda de nube de Microsoft Azure, fácilmente programable a través de .NET y las API de REST. El servicio de búsqueda de Azure API de REST son documentado en [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

En los pasos siguientes, deberá crear un índice, cargar documentos, el índice de la consulta y, a continuación, la consulta del sistema para obtener información de servicio.

Para completar estos pasos, tendrá un servicio de búsqueda de Azure y `api-key`. Consulte [crear un servicio de búsqueda de Azure en el portal](search-create-service-portal.md) para obtener instrucciones sobre cómo empezar a trabajar.

## <a name="create-an-index"></a>Crear un índice

1. Iniciar Fiddler. En el menú **archivo** , desactive el **Tráfico capturar** ocultar extraño actividad HTTP está relacionado con la tarea actual.

3. En la pestaña **Compositor** , deberá formular una solicitud de es similar a la pantalla siguiente.

    ![][1]

2. Seleccione **poner**.

3. Escriba una dirección URL que especifica la dirección URL del servicio, atributos de la solicitud y la versión de la api. Algunos punteros a tener en cuenta:
   + Use HTTPS como prefijo.
   + Atributo de petición es "/ índices/hoteles". Esto indica la búsqueda para crear un índice con nombre 'hoteles'.
   + Versión de la API está en minúscula, especificado como "? api versión = 28 de 02 de 2015". Las versiones API son importantes porque búsqueda de Azure implementa actualizaciones periódicamente. En algunas ocasiones, una actualización de servicio puede introducir un cambio de más reciente a la API. Por este motivo, la búsqueda de Azure requiere una versión de api en cada solicitud para que se encuentra en el control total sobre cuál se usa.

    La dirección URL completa debería tener un aspecto similar al siguiente ejemplo.

            https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.  Especificar el encabezado de la solicitud, reemplazar el host y la clave de api con valores que son válidos para su servicio.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

5.  En el cuerpo de la solicitud, pegue en los campos que conforman la definición de índice.
            
             {
            "name": "hotels",  
            "fields": [
              {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
              {"name": "baseRate", "type": "Edm.Double"},
              {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
              {"name": "hotelName", "type": "Edm.String"},
              {"name": "category", "type": "Edm.String"},
              {"name": "tags", "type": "Collection(Edm.String)"},
              {"name": "parkingIncluded", "type": "Edm.Boolean"},
              {"name": "smokingAllowed", "type": "Edm.Boolean"},
              {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
              {"name": "rating", "type": "Edm.Int32"},
              {"name": "location", "type": "Edm.GeographyPoint"}
             ]
            }

6.  Haga clic en **Ejecutar**.

En unos segundos, debería ver una respuesta HTTP 201 en la lista de sesión, que indica que se creó el índice.

Si recibe HTTP 504, compruebe que la dirección URL especifica HTTPS. Si ve HTTP 400 o 404, compruebe el cuerpo de la solicitud para comprobar que no ha habido errores de copiar y pegar. Un HTTP 403 normalmente indica un problema con la clave api (una clave no válida o un problema de sintaxis con cómo se especifica la clave de api).

## <a name="load-documents"></a>Cargar documentos

En la pestaña **Compositor** , su solicitud para enviar documentos tendrá un aspecto similar al siguiente. El cuerpo de la solicitud contiene los datos de búsqueda para 4 hoteles.

   ![][2]

1. Seleccione **Publicar**.

2.  Escriba una dirección URL que empieza por HTTPS, seguido de la dirección URL del servicio, seguido por "/indexes/ <'indexname ' >/documentos/índice? api versión = 28 de 02 de 2015". La dirección URL completa debería tener un aspecto similar al siguiente ejemplo.

            https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.  Encabezado de la solicitud debe ser igual que antes. Recuerde reemplaza el host y la clave de api con valores que son válidos para su servicio.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  El cuerpo de la solicitud contiene cuatro documentos que se agregan al índice de hoteles.

            {
            "value": [
            {
                "@search.action": "upload",
                "hotelId": "1",
                "baseRate": 199.0,
                "description": "Best hotel in town",
                "hotelName": "Fancy Stay",
                "category": "Luxury",
                "tags": ["pool", "view", "wifi", "concierge"],
                "parkingIncluded": false,
                "smokingAllowed": false,
                "lastRenovationDate": "2010-06-27T00:00:00Z",
                "rating": 5,
                "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
              },
              {
                "@search.action": "upload",
                "hotelId": "2",
                "baseRate": 79.99,
                "description": "Cheapest hotel in town",
                "hotelName": "Roach Motel",
                "category": "Budget",
                "tags": ["motel", "budget"],
                "parkingIncluded": true,
                "smokingAllowed": true,
                "lastRenovationDate": "1982-04-28T00:00:00Z",
                "rating": 1,
                "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
              },
              {
                "@search.action": "upload",
                "hotelId": "3",
                "baseRate": 279.99,
                "description": "Surprisingly expensive",
                "hotelName": "Dew Drop Inn",
                "category": "Bed and Breakfast",
                "tags": ["charming", "quaint"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
              },
              {
                "@search.action": "upload",
                "hotelId": "4",
                "baseRate": 220.00,
                "description": "This could be the one",
                "hotelName": "A Hotel for Everyone",
                "category": "Basic hotel",
                "tags": ["pool", "wifi"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
              }
             ]
            }

8.  Haga clic en **Ejecutar**.

En unos segundos, debe ver una respuesta HTTP 200 en la lista de sesión. Esto indica que se crearon los documentos correctamente. Si recibe un 207, documento al menos un error al cargar. Si recibe un error 404, tiene un error de sintaxis en el encabezado o el cuerpo de la solicitud.

## <a name="query-the-index"></a>El índice de la consulta

Ahora que se cargan un índice y documentos, puede emitir consultas en ellos.  En la pestaña **Compositor** , un comando **GET** que consulta su servicio tendrá un aspecto similar a la pantalla siguiente.

   ![][3]

1.  Seleccione **obtener**.

2.  Escriba una dirección URL que empieza por HTTPS, seguido de la dirección URL del servicio, seguido por "/indexes/ <'indexname ' > / docs?", seguido de los parámetros de la consulta. A modo de ejemplo, utilice la siguiente URL, reemplazar el nombre de host de ejemplo con uno que sea válido para el servicio.
    
            https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Esta consulta busca el término "granja." y recupera categorías de faceta para clasificaciones.

3.  Encabezado de la solicitud debe ser igual que antes. Recuerde reemplaza el host y la clave de api con valores que son válidos para su servicio.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

El código de respuesta debe ser 200 y los resultados de la respuesta deben ser similar a la pantalla siguiente.

   ![][4]

La siguiente consulta de ejemplo es desde la [operación de índice de búsqueda (API de búsqueda de Azure)](http://msdn.microsoft.com/library/dn798927.aspx) en MSDN. Muchas de las consultas de ejemplo en este tema incluyen espacios, que no se permiten en Fiddler. Reemplazar cada espacio con un carácter + antes de pegar en la consulta de cadena antes de intentar la consulta en Fiddler.

**Antes de que se sustituyen espacios:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Después de espacios se sustituyen por +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## <a name="query-the-system"></a>El sistema de la consulta

También puede consultar el sistema para obtener recuentos de documento y consumo de almacenamiento. En la pestaña **Compositor** , tendrá un aspecto similar al siguiente la solicitud y la respuesta devolverá un recuento del número de documentos y el espacio utilizado.

 ![][5]

1.  Seleccione **obtener**.

2.  Escriba una dirección URL que incluya la dirección URL del servicio, seguida de "/ índices, hoteles, estadísticas? api versión = 28 de 02 de 2015":

            https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.  Especificar el encabezado de la solicitud, reemplazar el host y la clave de api con valores que son válidos para su servicio.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Deje en blanco el cuerpo de la solicitud.

5.  Haga clic en **Ejecutar**. Verá un código de estado HTTP 200 en la lista de sesión. Seleccione la entrada que se publicó el comando.

6.  Haga clic en la pestaña **inspectores** , haga clic en la ficha **encabezados** y, a continuación, seleccione el formato JSON. Debería ver el tamaño de almacenamiento y el recuento de documento (en KB).

## <a name="next-steps"></a>Pasos siguientes

Consulte [administrar el servicio de búsqueda en Azure](search-manage.md) para sin código enfoque para la administración y el uso de la búsqueda de Azure.


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
