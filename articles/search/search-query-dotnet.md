<properties
    pageTitle="Consultar el índice de búsqueda de Azure mediante .NET SDK | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Crear una consulta de búsqueda de Azure y use los parámetros de búsqueda para filtrar y ordenar los resultados de búsqueda."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Consultar el índice de búsqueda de Azure mediante el SDK de .NET
> [AZURE.SELECTOR]
- [Información general](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

En este artículo le mostrará cómo consultar un índice con el [SDK de .NET de búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Antes de comenzar este tutorial, debe tener ya [creó un índice de búsqueda de Azure](search-what-is-an-index.md) y [se rellena con datos](search-what-is-data-import.md).

Tenga en cuenta que todo el código de ejemplo en este artículo se escribe en C#. Puede encontrar el origen completo código [en GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>Puedo. Identificar la clave de api de consulta de su servicio de búsqueda de Azure
Ahora que ha creado un índice de búsqueda de Azure, ya está casi listo para emitir consultas con el SDK de .NET. En primer lugar, necesitará obtener uno de la consulta-claves api generado para el servicio de búsqueda que se aprovisione. El SDK de .NET enviará esta clave api en cada solicitud al servicio. Tener una clave válida establece la confianza, de forma por solicitud, entre la aplicación que envía la solicitud y el servicio que la administra.

1. Para buscar claves api de su servicio debe iniciar sesión en el [Portal de Azure](https://portal.azure.com/)
2. Vaya a módulo de su servicio de búsqueda de Azure
3. Haga clic en el icono "Teclas"

Su servicio tendrá *claves de administración* y *consulta*.

  - Las *claves de administración* de primario y secundario conceder derechos completos a todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indizadores y orígenes de datos. Existen dos claves para que puedan continuar usar la clave secundaria si decide regenerar la clave principal y viceversa.
  - Las *teclas de consulta* conceder acceso de solo lectura a índices y documentos y se distribuye normalmente a aplicaciones cliente que emiten solicitudes de búsqueda.

A los efectos de la consulta a un índice, puede usar una de las claves de consulta. También pueden utilizarse las claves de administración de consultas, pero debe usar una clave de consulta en el código de aplicación como esta mejor sigue el [principio de menor privilegio](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-create-an-instance-of-the-searchindexclient-class"></a>II. Crear una instancia de la clase SearchIndexClient
Para ejecutar consultas con el SDK de .NET de búsqueda de Azure, debe crear una instancia de la `SearchIndexClient` clase. Esta clase tiene varios constructores. Desea que tendrá el nombre de servicio de búsqueda, índice y una `SearchCredentials` objeto como parámetros. `SearchCredentials`ajusta la clave api.

El código siguiente crea un nuevo `SearchIndexClient` para el índice de "hoteles" (creado en [crear un índice de búsqueda de Azure mediante .NET SDK](search-create-index-dotnet.md)) con valores para el nombre de servicio de búsqueda y la clave de api que se almacenan en el archivo de configuración de la aplicación (`app.config` o `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient`tiene una `Documents` propiedad. Esta propiedad proporciona todos los métodos que necesita para consultar los índices de búsqueda de Azure.

## <a name="iii-query-your-index"></a>III. El índice de la consulta
Realizar búsquedas con el SDK de .NET es tan sencilla como llamar a la `Documents.Search` método en su `SearchIndexClient`. Este método toma unos parámetros, incluido el texto de búsqueda, junto con una `SearchParameters` objeto que puede usarse para refinar la consulta.

#### <a name="types-of-queries"></a>Tipos de consultas
Los dos principales [tipos de consultas](search-query-overview.md#types-of-queries) que se usa son `search` y `filter`. A `search` para uno o varios términos de búsqueda en todos los campos de _búsqueda_ en el índice de la consulta. A `filter` consulta evalúa una expresión booleana todos _pueden filtrarse_ campos en un índice.

Se realizan búsquedas y filtros con la `Documents.Search` método. Una consulta de búsqueda puede pasar en el `searchText` parámetro, aunque puede pasar una expresión de filtro en la `Filter` propiedad de la `SearchParameters` clase. Para filtrar sin buscar, simplemente pase `"*"` para el `searchText` parámetro. Para buscar sin filtrado, deje el `Filter` propiedad anular, o no pasa de una `SearchParameters` instancia en absoluto.

#### <a name="example-queries"></a>Consultas de ejemplo

El código de ejemplo siguiente muestra unos cuantos métodos para consultar el índice de "hoteles" definido en [crear un índice de búsqueda de Azure mediante .NET SDK](search-create-index-dotnet.md#DefineIndex). Tenga en cuenta que los documentos que se devuelve con los resultados de búsqueda son instancias de la `Hotel` clase, que se ha definido en la [Importación de datos en la búsqueda de Azure mediante .NET SDK](search-import-data-dotnet.md#HotelClass). El código de ejemplo utiliza un `WriteDocuments` método de los resultados de búsqueda en la consola. Este método se describe en la siguiente sección.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="iv-handle-search-results"></a>IV. Resultados de la búsqueda de identificador
La `Documents.Search` método devuelve un `DocumentSearchResult` objeto que contiene los resultados de la consulta. El ejemplo en la sección anterior utiliza un método denominado `WriteDocuments` a los resultados de búsqueda en la consola:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Esto es el aspecto de los resultados de las consultas de la sección anterior, suponiendo que el índice "hoteles" se rellena con los datos de ejemplo en [La importación de datos en la búsqueda de Azure mediante .NET SDK](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

El código de ejemplo anterior, usa la consola para mostrar los resultados de búsqueda. Del mismo modo, necesitará mostrar resultados de búsqueda en su propia aplicación. Vea [este ejemplo en GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) para obtener un ejemplo de cómo representar los resultados de búsqueda en una aplicación web de ASP.NET MVC.
