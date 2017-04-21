<properties
   pageTitle="Actualizar a la versión 1.1 de Azure búsqueda .NET SDK | Microsoft Azure | Servicio de nube hospedado de búsqueda"
   description="Actualizar a la versión 1.1 de Azure búsqueda .NET SDK"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/15/2016"
   ms.author="brjohnst"/>

# <a name="upgrading-to-the-azure-search-net-sdk-version-20-preview"></a>Actualizar a la versión de Azure búsqueda .NET SDK 2.0-preview

Si está utilizando la versión 1.1 o anterior de la [Búsqueda de Azure .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx), este artículo le ayudará a actualizar la aplicación para utilizar la próxima versión, 2.0 de vista previa.

Para obtener un tutorial más general de SDK, incluidos los ejemplos, vea [cómo usar la búsqueda de Azure desde una aplicación .NET](search-howto-dotnet-sdk.md).

Versión 2.0 vista previa de la búsqueda de Azure .NET SDK contiene algunos cambios de versiones anteriores. Estos son principalmente secundarios, para cambiar el código necesario solo un esfuerzo mínimo. Consulte los [pasos para actualizar](#UpgradeSteps) para que obtener instrucciones sobre cómo cambiar el código usar la nueva versión SDK.

> [AZURE.NOTE] Si está utilizando la versión preview 0,13 o anterior, debe actualizar a la versión 1.1 primero y, a continuación, actualice a 2.0 de vista previa. Consulte [Apéndice: pasos para actualizar a la versión 1.1](#UpgradeStepsV1) para obtener instrucciones.

<a name="WhatsNew"></a>
## <a name="whats-new-in-version-20-preview"></a>Novedades de la versión 2.0: vista previa

Vista previa versión 2.0 es la primera versión de Azure búsqueda .NET SDK para una versión de vista previa de la API de REST de búsqueda de Azure, específicamente 2015 02-28-vista previa. Esto permite utilizar muchas características de vista previa de la búsqueda de Azure desde una aplicación .NET, incluyendo lo siguiente:

- [Analizadores personalizados](https://aka.ms/customanalyzers)
- Soporte de indizador de [Almacenamiento de blobs de Windows Azure](search-howto-indexing-azure-blob-storage.md) y [Azure almacenamiento de tablas](search-howto-indexing-azure-tables.md)
- Personalización de indizador a través de [las asignaciones de campos](search-indexer-field-mappings.md)
- Soporte técnico ETags para habilitar la actualización simultáneas seguros de definiciones de índice, indizadores y orígenes de datos
- Compatibilidad con núcleo de .NET y .NET portátil perfil 111

<a name="UpgradeSteps"></a>
## <a name="steps-to-upgrade"></a>Pasos para la actualización

En primer lugar, actualice su referencia NuGet para `Microsoft.Azure.Search` mediante la consola de administrador de paquetes de NuGet o el botón secundario en las referencias del proyecto y seleccionando "Administrar NuGet paquetes …" en Visual Studio. Asegúrese de que la opción Habilitar paquetes de versión preliminar seleccionando "Incluir preliminar" en la NuGet "Administrar paquetes" ventana en Visual Studio, o mediante el `-IncludePrerelease` cambiar si está utilizando la consola del Administrador de paquetes de NuGet.

Una vez que haya descargado NuGet los nuevos paquetes y sus dependencias, volver a crear el proyecto. Dependiendo de cómo está estructurado en su código, puede volver a generar correctamente. Si es así, está listo.

Si se produce un error en la compilación, verá un error de compilación similar al siguiente:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

El siguiente paso es corregir este error de compilación. Para obtener más información sobre las causas del error y cómo corregirlo, vea [cambios importantes en la versión 2.0: vista previa](#ListOfChanges) .

Es posible que vea advertencias de compilación adicionales relacionadas con propiedades o métodos obsoletos. Las advertencias incluyen instrucciones sobre lo que puede utilizar en lugar de la función eliminada. Por ejemplo, si la aplicación utiliza la `SearchRequestOptions.RequestId` propiedad, obtendrá una advertencia que indica que`"This property is deprecated. Please use ClientRequestId instead."`

Una vez que haya corregido los errores de compilación, puede realizar cambios en la aplicación para aprovechar las ventajas de la nueva funcionalidad si lo desea. Nuevas características en el SDK se detallan en [Novedades de vista previa versión 2.0](#WhatsNew).

<a name="ListOfChanges"></a>
## <a name="breaking-changes-in-version-20-preview"></a>Cambios importantes en la versión 2.0 de vista previa

Hay solo un cambio en la versión 2.0-vista previa que puede requerir cambios en el código además de volver a generar la aplicación.

### <a name="indexesgetclient-return-type"></a>Tipo de valor devuelto Indexes.GetClient

La `Indexes.GetClient` método tiene un nuevo tipo de valor devuelto. Anteriormente, devuelve `SearchIndexClient`, pero esto se ha cambiado a `ISearchIndexClient` en la versión 2.0: vista previa. Esto es compatible con los clientes que deseen simular la `GetClient` método para pruebas unitarias devolver una implementación ficticio de `ISearchIndexClient`.

#### <a name="example"></a>Ejemplo

Si el código es similar a esta:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Puede cambiarlo a esta opción para corregir los errores de compilación:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

## <a name="conclusion"></a>Conclusión
Si necesita más información sobre el uso de la búsqueda de Azure .NET SDK, consulte nuestra actualizados recientemente [procedimientos](search-howto-dotnet-sdk.md).

Agradecemos sus comentarios en el SDK. Si tiene problemas, no dude en nosotros pedir ayuda en el [foro de MSDN de búsqueda de Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuresearch). Si encuentra un error, puede archivar un problema en el [repositorio de Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Asegúrese de incluir prefijo en el título de problema con "búsqueda SDK:".

Gracias por utilizar la búsqueda de Azure.

<a name="UpgradeStepsV1"></a>
## <a name="appendix-steps-to-upgrade-to-version-11"></a>Apéndice: Pasos para actualizar a la versión 1.1 

> [AZURE.NOTE] En esta sección se aplica solo a los usuarios de la versión de Azure búsqueda .NET SDK 0,13 preview y versiones anterior.

En primer lugar, actualice su referencia NuGet para `Microsoft.Azure.Search` mediante la consola de administrador de paquetes de NuGet o el botón secundario en las referencias del proyecto y seleccionando "Administrar NuGet paquetes …" en Visual Studio.

Una vez que haya descargado NuGet los nuevos paquetes y sus dependencias, volver a crear el proyecto.

Si anteriormente estaba utilizando versión 1.0.0-preview, 1.0.1-preview o 1.0.2-preview, la generación debe ser correcta y esté listo para ir!

Si anteriormente estaba utilizando la versión 0.13.0-preview o anterior, debería ver errores como la siguiente:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

El siguiente paso es corregir los errores de compilación uno a uno. La mayoría requieren cambiar algunos nombres de clase y método que han cambiado de nombre en el SDK. [Lista de cambios importantes en la versión 1.1](#ListOfChangesV1) contiene una lista de estos cambios de nombre.

Si está usando clases personalizadas a los documentos del modelo y las clases tienen propiedades de los tipos simples no nullable (por ejemplo, `int` o `bool` en C#), hay corregir un error en la versión 1.1 del SDK que debe tener en cuenta. Para obtener más detalles, consulte [correcciones de errores en la versión 1.1](#BugFixesV1) .

Por último, una vez que haya corregido los errores de compilación, puede realizar cambios en la aplicación para aprovechar las ventajas de la nueva funcionalidad si lo desea.

<a name="ListOfChangesV1"></a>
### <a name="list-of-breaking-changes-in-version-11"></a>Lista de cambios importantes en la versión 1.1

La siguiente lista se ordena por la probabilidad de que el cambio afectará el código de la aplicación.

#### <a name="indexbatch-and-indexaction-changes"></a>Cambios de IndexBatch y IndexAction

`IndexBatch.Create`ha cambiado a `IndexBatch.New` y ya no tiene un `params` argumento. Puede usar `IndexBatch.New` para lotes que mezclar distintos tipos de acciones (combinaciones, eliminaciones, etcetera). Además, hay nuevos métodos estáticos para crear lotes donde todas las acciones son los mismos: `Delete`, `Merge`, `MergeOrUpload`, y `Upload`.

`IndexAction`ya no tiene constructores públicos y sus propiedades ahora son inmutables. Debe usar los nuevos métodos estáticos para crear acciones para diferentes propósitos: `Delete`, `Merge`, `MergeOrUpload`, y `Upload`. `IndexAction.Create`se ha quitado. Si utiliza la sobrecarga que toma solo un documento, asegúrese de usar `Upload` en su lugar.

##### <a name="example"></a>Ejemplo

Si el código es similar a esta:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Puede cambiarlo a esta opción para corregir los errores de compilación:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Si lo desea, puede simplificar aún más a este:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>Cambios de IndexBatchException

La `IndexBatchException.IndexResponse` propiedad ha cambiado a `IndexingResults`, y su tipo es ahora `IList<IndexingResult>`.

##### <a name="example"></a>Ejemplo

Si el código es similar a esta:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Puede cambiarlo a esta opción para corregir los errores de compilación:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
#### <a name="operation-method-changes"></a>Cambios de método de operación

Cada operación en el SDK de .NET de búsqueda de Azure se expone como un conjunto de sobrecargas del método para autores de llamada sincrónicos y asincrónicos. Las firmas y división de estas sobrecargas método ha cambiado en la versión 1.1.

Por ejemplo, la operación de "Obtener estadísticas de índice" en versiones anteriores de SDK expone estas firmas:

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Las firmas de método para la misma operación en la versión 1.1 tiene el siguiente aspecto:

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

A partir de la versión 1.1, el SDK de .NET de búsqueda de Azure organiza los métodos de operación diferente:

 - Parámetros opcionales ahora modelados como predeterminado en su lugar los parámetros que sobrecargas del método adicionales. Esto reduce el número de sobrecargas del método, a veces considerablemente.
 - Los métodos de extensión ocultan ahora una gran cantidad de los detalles extraños de HTTP desde el llamador. Por ejemplo, las versiones anteriores de SDK devuelven un objeto de respuesta con un código de estado HTTP, que a menudo no necesita comprobar porque operación inician `CloudException` para cualquier código de estado que indica un error. Los nuevos métodos de extensión solo devuelven los objetos de modelo, lo que le ahorra el problema de tener que les desajustar en el código.
 - Por el contrario, el núcleo interfaces ahora métodos de exposición que ofrecen más control en el nivel HTTP si lo necesita. Ahora puede pasar en encabezados HTTP personalizados que se incluirán en las convocatorias y las nuevas `AzureOperationResponse<T>` tipo devuelto proporciona acceso directo a la `HttpRequestMessage` y `HttpResponseMessage` para la operación. `AzureOperationResponse`se define en la `Microsoft.Rest.Azure` espacio de nombres y reemplaza `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>Cambios de ScoringParameters

Una nueva clase denominada `ScoringParameter` se ha agregado en el SDK más reciente para que sea más fácil proporcionar parámetros de puntuación perfiles en una consulta de búsqueda. Previamente la `ScoringProfiles` propiedad de la `SearchParameters` clase escribió como `IList<string>`; Ahora se escribe como `IList<ScoringParameter>`.

##### <a name="example"></a>Ejemplo

Si el código es similar a esta:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Puede cambiarlo a esta opción para corregir los errores de compilación: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Cambios del modelo de clase

Debido a la firma cambia descrito en [el método de operación cambia](#OperationMethodChanges), muchas clases de la `Microsoft.Azure.Search.Models` se han cambiado el nombre o quitar espacio de nombres. Por ejemplo:

 - `IndexDefinitionResponse`se ha sustituido por`AzureOperationResponse<Index>`
 - `DocumentSearchResponse`ha cambiado a`DocumentSearchResult`
 - `IndexResult`ha cambiado a`IndexingResult`
 - `Documents.Count()`ahora devuelve un `long` con el número de documento en lugar de un`DocumentCountResponse`
 - `IndexGetStatisticsResponse`ha cambiado a`IndexGetStatisticsResult`
 - `IndexListResponse`ha cambiado a`IndexListResult`

En resumen, `OperationResponse`-clases derivadas que existían solo para ajustar un objeto de modelo que se han quitado. Las clases restantes han tenido su sufijo cambiado de `Response` a `Result`.

##### <a name="example"></a>Ejemplo

Si el código es similar a esta:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Puede cambiarlo a esta opción para corregir los errores de compilación:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>Las clases de respuesta y IEnumerable

Un cambio adicional que puede afectar el código es que ya no implementan las clases de respuesta que contienen colecciones `IEnumerable<T>`. En su lugar, puede acceder a la propiedad de la colección directamente. Por ejemplo, si el código es similar a esta:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Puede cambiarlo a esta opción para corregir los errores de compilación:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="important-note-for-web-applications"></a>Nota importante para las aplicaciones web

Si tiene una aplicación web que serializa `DocumentSearchResponse` directamente para enviar los resultados de búsqueda en el explorador, debe cambiar el código o los resultados no serializará correctamente. Por ejemplo, si el código es similar a esta:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Puede cambiar obteniendo el `.Results` propiedad de la respuesta de búsqueda para la corrección de representación de resultado de búsqueda:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Tendrá que buscar esos casos en su código usted mismo; **El compilador no le avisará** porque `JsonResult.Data` es de tipo `object`.

#### <a name="cloudexception-changes"></a>Cambios de CloudException

La `CloudException` clase se ha movido desde el `Hyak.Common` espacio de nombres para la `Microsoft.Rest.Azure` espacio de nombres. Además, su `Error` propiedad ha cambiado a `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>Cambios de SearchServiceClient y SearchIndexClient

El tipo de la `Credentials` propiedad ha cambiado de `SearchCredentials` a su clase base, `ServiceClientCredentials`. Si necesita obtener acceso a la `SearchCredentials` de un `SearchIndexClient` o `SearchServiceClient`, use la nueva `SearchCredentials` propiedad.

En versiones anteriores de SDK, `SearchServiceClient` y `SearchIndexClient` tenía constructores que realizó un `HttpClient` parámetro. Estos se han reemplazado con constructores que toman un `HttpClientHandler` y una matriz de `DelegatingHandler` objetos. Esto facilita instalar controladores personalizados para procesar previamente las solicitudes HTTP si es necesario.

Por último, los constructores que realizó un `Uri` y `SearchCredentials` han cambiado. Por ejemplo, si tiene código que es similar a esta:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Puede cambiarlo a esta opción para corregir los errores de compilación:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Observe también que ha cambiado el tipo de parámetro de credenciales a `ServiceClientCredentials`. Esto es probable que afecten a su código desde `SearchCredentials` se deriva de `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Envío de un identificador de solicitud

En las versiones anteriores de SDK, puede establecer un identificador de solicitud en la `SearchServiceClient` o `SearchIndexClient` y se incluiría en cada solicitud a la API de REST. Esto es útil para solucionar problemas con el servicio de búsqueda si necesita ponerse en contacto con soporte técnico. Sin embargo, es más útil para establecer un identificador único de solicitud para cada operación en lugar de utilizar el mismo identificador para todas las operaciones. Por este motivo, el `SetClientRequestId` métodos de `SearchServiceClient` y `SearchIndexClient` se han quitado. En su lugar, puede pasar un identificador de solicitud para cada método de operación a través de la opcional `SearchRequestOptions` parámetro.

> [AZURE.NOTE] En una versión futura del SDK, agregaremos un nuevo mecanismo para establecer un identificador de solicitud global en el cliente objetos que sea coherente con el enfoque usado por otros SDK de Azure.

#### <a name="example"></a>Ejemplo

Si tiene código que es similar a esta:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Puede cambiarlo a esta opción para corregir los errores de compilación:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Cambios de nombre de la interfaz

Los nombres de interfaz de grupo de operación han cambiado para que sea coherente con sus nombres de propiedad correspondientes:

 - El tipo de `ISearchServiceClient.Indexes` se ha cambiado de `IIndexOperations` a `IIndexesOperations`.
 - El tipo de `ISearchServiceClient.Indexers` se ha cambiado de `IIndexerOperations` a `IIndexersOperations`.
 - El tipo de `ISearchServiceClient.DataSources` se ha cambiado de `IDataSourceOperations` a `IDataSourcesOperations`.
 - El tipo de `ISearchIndexClient.Documents` se ha cambiado de `IDocumentOperations` a `IDocumentsOperations`.

Este cambio es probable que afecten a su código a menos que cree simulacros de estas interfaces para fines de pruebas.

<a name="BugFixesV1"></a>
### <a name="bug-fixes-in-version-11"></a>Correcciones de errores en la versión 1.1

Se ha producido un error en las versiones anteriores de Azure búsqueda .NET SDK relacionado con la serialización de clases de modelo personalizado. El error podría ocurrir si creó una clase modelo personalizado con una propiedad de un tipo de valor no es null.

#### <a name="steps-to-reproduce"></a>Pasos para reproducir

Crear una clase modelo personalizado con una propiedad de tipo de valor no es null. Por ejemplo, agregar un público `UnitCount` propiedad de tipo `int` en lugar de `int?`.

Si el índice de un documento con el valor predeterminado de ese tipo (por ejemplo, 0 para `int`), el campo será nulo en la búsqueda de Azure. Si desea buscar posteriormente dicho documento, la `Search` iniciará la llamada `JsonSerializationException` queja que no se puede convertir `null` a `int`.

Además, filtros pueden no funcionar según lo esperado desde que se escribió null al índice en lugar del valor previsto.

#### <a name="fix-details"></a>Corregir detalles

Este problema nos hemos fijado en la versión 1.1 del SDK. Ahora, si tiene una clase de modelo similar a esta:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

y configurar `IntValue` a 0, ese valor es ahora correctamente serie como 0 en el cable y almacena como 0 en el índice. De ida y vuelta también funciona como se esperaba.

Hay un problema potencial que debería considerar con este método: si usa un tipo de modelo con una propiedad no nullable, deberá **garantizar** documentos en el índice que no contienen un valor nulo para el campo correspondiente. El SDK ni la API de REST de búsqueda de Azure le ayudará a exigir esto.

Esto no es un problema de hipotético: Imagine un escenario donde agregar un nuevo campo a un índice existente que es de tipo `Edm.Int32`. Después de actualizar la definición del índice, todos los documentos tendrá un valor nulo para el nuevo campo (ya que todos los tipos son nullable en búsqueda de Azure). Si utiliza una clase modelo con no nullable `int` propiedad del campo, obtendrá una `JsonSerializationException` así al intentar recuperar documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos aún utilizar tipos nullable en las clases de modelo como práctica recomendada.

Para obtener más detalles sobre este error y la corrección, vea [este problema en GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).
