<properties
    pageTitle="Cargar datos en la búsqueda de Azure mediante .NET SDK | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Obtenga información sobre cómo cargar datos en un índice de búsqueda de Azure mediante .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Cargar datos en la búsqueda de Azure usando el SDK de .NET
> [AZURE.SELECTOR]
- [Información general](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)

En este artículo le mostrará cómo usar la [Búsqueda de Azure .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) para importar datos en un índice de búsqueda de Azure.

Antes de comenzar este tutorial, debe tener ya [creó un índice de búsqueda de Azure](search-what-is-an-index.md). En este artículo también se supone que ya ha creado un `SearchServiceClient` objeto, como se muestra en [crear un índice de búsqueda de Azure mediante .NET SDK](search-create-index-dotnet.md#CreateSearchServiceClient).

Tenga en cuenta que todo el código de ejemplo en este artículo se escribe en C#. Puede encontrar el origen completo código [en GitHub](http://aka.ms/search-dotnet-howto).

Para poder insertar documentos en el índice mediante .NET SDK, necesitará:

  1. Crear un `SearchIndexClient` objeto para conectar con el índice de búsqueda.
  2. Crear un `IndexBatch` que contiene los documentos que se pueden agregar, modificar o eliminar.
  3. Llamar a la `Documents.Index` método de su `SearchIndexClient` para enviar la `IndexBatch` para el índice de búsqueda.

## <a name="i-create-an-instance-of-the-searchindexclient-class"></a>Puedo. Crear una instancia de la clase SearchIndexClient
Para importar datos en el índice mediante búsqueda .NET SDK de Azure, debe crear una instancia de la `SearchIndexClient` clase. Puede crear esta instancia usted mismo, pero es más fácil si ya tiene un `SearchServiceClient` instancia para llamar a su `Indexes.GetClient` método. Por ejemplo, le mostramos cómo obtener un `SearchIndexClient` para el índice denominado "hoteles" desde una `SearchServiceClient` denominado `serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] En una aplicación de búsqueda típica, población y administración de índices se gestionó un componente independiente de las consultas de búsqueda. `Indexes.GetClient`es conveniente para rellenar un índice porque se guarda el problema de proporcionar otro `SearchCredentials`. Esto hace pasando la clave de administrador que usó para crear la `SearchServiceClient` al nuevo `SearchIndexClient`. Sin embargo, en la parte de la aplicación que ejecuta las consultas, es mejor crear la `SearchIndexClient` directamente para que pueda pasar de una clave de consulta en lugar de una clave de administración. Esto es coherente con el [principio de menor privilegio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) y le ayudará a que la aplicación sea más segura. Puede encontrar más información sobre las claves de administración y consulta en la [referencia de la API de REST de búsqueda de Azure en MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

`SearchIndexClient`tiene una `Documents` propiedad. Esta propiedad proporciona todos los métodos que necesite agregar, modificar, eliminar o documentos en el índice de la consulta.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Decidir qué acción indización usar
Para importar datos con el SDK de .NET, debe empaquetar los datos en un `IndexBatch` objeto. Un `IndexBatch` encapsula una colección de `IndexAction` objetos, cada una de las cuales contiene un documento y una propiedad que indica la búsqueda de Azure qué acción debe realizar en el documento (carga, combinar, eliminar, etcetera). Dependiendo de cuál de las debajo de acciones que elija, sólo algunos campos deben incluirse para cada documento:

Acción | Descripción | Campos necesarios para cada documento | Notas
--- | --- | --- | ---
`Upload` | Un `Upload` acción es similar a un "upsert" donde se inserta si es nuevo y actualice o reemplace si existe el documento. | clave, además de todos los campos que desea definir | Al actualizar o reemplazar un documento existente, cualquier campo que no se especifica en la solicitud tendrá su campo establecido en `null`. Esto ocurre incluso cuando el campo se estableció anteriormente como un valor no nulo.
`Merge` | Actualiza un documento existente con los campos especificados. Si el documento no existe en el índice, se producirá un error en la combinación. | clave, además de todos los campos que desea definir | Cualquier campo que especifique en una combinación reemplazará el campo existente en el documento. Esto incluye los campos de tipo `DataType.Collection(DataType.String)`. Por ejemplo, si el documento contiene un campo `tags` con valor `["budget"]` y ejecutar una combinación con el valor `["economy", "pool"]` para `tags`, el valor final de la `tags` campo será `["economy", "pool"]`. No será `["budget", "economy", "pool"]`.
`MergeOrUpload` | Esta acción se comporta como `Merge` si ya existe un documento con la clave especificada en el índice. Si el documento no existe, se comporta como `Upload` con un nuevo documento. | clave, además de todos los campos que desea definir | -
`Delete` | Quita del documento especificado desde el índice. | sólo clave | Los campos se especifica que el campo de clave se pasará por alto. Si desea quitar un campo individual de un documento, use `Merge` en su lugar y simplemente establece el campo explícitamente en null.

Puede especificar qué acción que desea usar con los distintos métodos estáticos de la `IndexBatch` y `IndexAction` clases, como se muestra en la siguiente sección.

## <a name="iii-construct-your-indexbatch"></a>III. Crear su IndexBatch
Ahora que sabe qué acciones que puede realizar en los documentos, está listo para crear el `IndexBatch`. El ejemplo siguiente muestra cómo crear un lote con algunas acciones diferentes. Observe que el ejemplo utiliza una clase personalizada denominada `Hotel` que se asigna a un documento en el índice "hoteles".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

En este caso, estamos usando `Upload`, `MergeOrUpload`, y `Delete` como nuestros acciones de búsqueda, según lo especificado por los métodos llamados en el `IndexAction` clase.

Supongamos que este índice de ejemplo "hoteles" se ha rellenado con un número de documentos. Observe cómo no ha habido especificar todos los campos del documento posibles al utilizar `MergeOrUpload` y cómo se especifica solo la clave de documento (`HotelId`) cuando se usa `Delete`.

Además, tenga en cuenta que solo puede incluir hasta 1000 documentos en una sola solicitud de indización.

> [AZURE.NOTE] En este ejemplo, estamos aplicando diferentes acciones para diferentes documentos. Si desea realizar las mismas acciones en todos los documentos en el lote, en lugar de llamada `IndexBatch.New`, podría usar los otros métodos estáticos de `IndexBatch`. Por ejemplo, podría crear lotes llamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, o `IndexBatch.Delete`. Estos métodos toman un conjunto de documentos (objetos de tipo `Hotel` en este ejemplo) en lugar de `IndexAction` objetos.

## <a name="iv-import-data-to-the-index"></a>IV. Importar datos en el índice
Ahora que ya tiene inicializado `IndexBatch` objeto, puede enviar al índice llamando `Documents.Index` en su `SearchIndexClient` objeto. En el ejemplo siguiente se muestra cómo llamar a `Index`, así como algunos pasos adicionales que debe llevar a cabo:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Nota la `try` / `catch` que rodea la llamada a la `Index` método. El bloqueo de capturas controladores de un caso de error importante para la indización. Si se produce un error en el servicio de búsqueda de Azure indizar algunos de los documentos en el lote, un `IndexBatchException` iniciada por `Documents.Index`. Esto puede suceder si indiza documentos mientras el servicio está bajo una carga intensa. **Le recomendamos encarecidamente tratamiento explícitamente este caso en el código.** Puede retrasar y vuelva a intentar la indización de los documentos que no se pudo, o puede iniciar sesión y continuar como no de la muestra, o puede hacer algo distinto dependiendo de los requisitos de coherencia de datos de la aplicación.

Por último, el código en el ejemplo anterior retrasos durante dos segundos. Indización realiza de forma asincrónica en el servicio de búsqueda de Azure, por lo que necesita espere un momento para garantizar que los documentos estén disponibles para buscar la aplicación de ejemplo. Retrasos así normalmente solo son necesarios en demostraciones, pruebas y aplicaciones de ejemplo.

<a name="HotelClass"></a>
### <a name="how-the-net-sdk-handles-documents"></a>Cómo administra el SDK de .NET los documentos

Es podrán que se pregunte cómo Azure búsqueda .NET SDK es capaz de cargar instancias de una clase definida por el usuario como `Hotel` al índice. Para ayudarle a responder a esa pregunta, echemos un vistazo a la `Hotel` clase, que asigna el esquema de índice definido en [crear un índice de búsqueda de Azure mediante .NET SDK](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

Lo primero que debe tener en cuenta que es cada propiedad pública de `Hotel` corresponde a un campo en la definición del índice, pero con una diferencia fundamental: el nombre de cada campo comienza con una letra minúscula ("CamelCase"), mientras que el nombre de cada propiedad pública de `Hotel` empieza por una letra en mayúsculas ("mayúsculas y minúsculas Pascal"). Este es un escenario común en las aplicaciones de .NET que realizar el enlace de datos en el esquema de destino está fuera del control del desarrollador de la aplicación. En lugar de tener infringir .NET las directrices de nomenclatura realizando los nombres de propiedad y minúscula camel, puede indicar el SDK para asignar los nombres de propiedad a minúscula camel automáticamente con la `[SerializePropertyNamesAsCamelCase]` atributo.

> [AZURE.NOTE] La búsqueda de Azure .NET SDK utiliza la biblioteca de [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para serializar y deserializar los objetos de modelo personalizado a y desde JSON. Puede personalizar esta serialización si es necesario. Puede encontrar más detalles en la sección [actualización en el SDK de .NET de búsqueda de Azure versión 1.1](search-dotnet-sdk-migration.md#WhatsNew). Un ejemplo de esto es el uso de la `[JsonProperty]` atributo en la `DescriptionFr` propiedad en el código de ejemplo anterior.

La segunda cosa importante sobre la `Hotel` clase son los tipos de datos de las propiedades públicas. Los tipos de .NET estas propiedades asignan a los tipos de campo equivalente en la definición de índice. Por ejemplo, el `Category` cadena propiedad se asigna a la `category` campo, que es de tipo `DataType.String`. Hay asignaciones de tipo similares entre `bool?` y `DataType.Boolean`, `DateTimeOffset?` y `DataType.DateTimeOffset`, etcetera. Las reglas específicas para la asignación de tipo se documentación con la `Documents.Get` método en [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Esta capacidad de usar sus propias clases como documentos funciona en ambas direcciones; También puede recuperar los resultados de búsqueda y tiene el SDK deserializa automáticamente para un tipo de su elección, como se muestra en el [siguiente artículo](search-query-dotnet.md).

> [AZURE.NOTE] Azure búsqueda .NET SDK también admite tipos dinámicamente documentos mediante la `Document` clase, que es una asignación de clave y valor de los nombres de campo en valores de campo. Esto es útil en escenarios donde no se conoce el esquema de índice en tiempo de diseño o donde sería conveniente enlazar a las clases de modelo específico. Todos los métodos en el SDK que tratan los documentos tienen sobrecargas que funcionan con la `Document` clase, así como inflexible sobrecargas que toman un parámetro de tipo genérico. Solo el último se utiliza en el código de ejemplo en este artículo. Puede encontrar más información sobre la `Document` de clase [en MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Una nota importante acerca de los tipos de datos**

Cuando diseñe sus propias clases de modelo para asignar a un índice de búsqueda de Azure, se recomienda declarar propiedades de tipos de valor como `bool` y `int` ser nullable (por ejemplo, `bool?` en lugar de `bool`). Si usa una propiedad no nullable, debe **garantizar** documentos en el índice que no contienen un valor nulo para el campo correspondiente. El SDK ni el servicio de búsqueda de Azure le ayudará a exigir esto.

Esto no es un problema de hipotético: Imagine un escenario donde agregar un nuevo campo a un índice existente que es de tipo `DataType.Int32`. Después de actualizar la definición del índice, todos los documentos tendrá un valor nulo para el nuevo campo (ya que todos los tipos son nullable en búsqueda de Azure). Si utiliza una clase modelo con no nullable `int` propiedad del campo, obtendrá una `JsonSerializationException` así al intentar recuperar documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por este motivo, recomendamos que utilice tipos nullable en las clases de modelo como práctica recomendada.

## <a name="next"></a>Siguiente
Tras rellenar el índice de búsqueda de Azure, estará listo para empezar a emitir consultas para buscar documentos. Para obtener información detallada, vea [Consulta el índice de búsqueda de Azure](search-query-overview.md) .
