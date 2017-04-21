<properties
    pageTitle="Crear un índice de búsqueda de Azure mediante .NET SDK | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Crear un índice en el código mediante el SDK de .NET de búsqueda de Azure."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Crear un índice de búsqueda de Azure usando el SDK de .NET
> [AZURE.SELECTOR]
- [Información general](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


En este artículo le guiará a través del proceso de creación de una búsqueda de Azure [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) mediante el [SDK de .NET de búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Antes de seguir esta guía y crear un índice, debe tener ya [creó un servicio de búsqueda de Azure](search-create-service-portal.md).

Tenga en cuenta que todo el código de ejemplo en este artículo se escribe en C#. Puede encontrar el origen completo código [en GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Puedo. Identificar la clave de api de administración del servicio de búsqueda de Azure
Ahora que ha proporcionado un servicio de búsqueda de Azure, ya está casi listo para emitir solicitudes en el extremo de servicio mediante el SDK de .NET. En primer lugar, necesitará obtener uno de la administración de claves api generado para el servicio de búsqueda que se aprovisione. El SDK de .NET enviará esta clave api en cada solicitud al servicio. Tener una clave válida establece la confianza, de forma por solicitud, entre la aplicación que envía la solicitud y el servicio que la administra.

1. Para buscar claves api de su servicio debe iniciar sesión en el [Portal de Azure](https://portal.azure.com/)
2. Vaya a módulo de su servicio de búsqueda de Azure
3. Haga clic en el icono "Teclas"

Su servicio tendrá *claves de administración* y *consulta*.

  - Las *claves de administración* de primario y secundario conceder derechos completos a todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indizadores y orígenes de datos. Existen dos claves para que puedan continuar usar la clave secundaria si decide regenerar la clave principal y viceversa.
  - Las *teclas de consulta* conceder acceso de solo lectura a índices y documentos y se distribuye normalmente a aplicaciones cliente que emiten solicitudes de búsqueda.

Para el propósito de crear un índice, puede usar la clave de administración primaria o secundaria.

<a name="CreateSearchServiceClient"></a>
## <a name="ii-create-an-instance-of-the-searchserviceclient-class"></a>II. Crear una instancia de la clase SearchServiceClient
Para empezar a usar el SDK de .NET de búsqueda de Azure, debe crear una instancia de la `SearchServiceClient` clase. Esta clase tiene varios constructores. Aquel que desee toma el nombre de servicio de búsqueda y una `SearchCredentials` objeto como parámetros. `SearchCredentials`ajusta la clave api.

El código siguiente crea un nuevo `SearchServiceClient` con valores para el nombre de servicio de búsqueda y la clave de api que se almacenan en el archivo de configuración de la aplicación (`app.config` o `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient`tiene una `Indexes` propiedad. Esta propiedad proporciona todos los métodos que necesita para crear, lista, actualización o eliminarán los índices de búsqueda de Azure.

> [AZURE.NOTE] La `SearchServiceClient` clase administra las conexiones con el servicio de búsqueda. Para evitar la apertura demasiadas conexiones, debe intentar compartir una única instancia de `SearchServiceClient` en la aplicación si es posible. Sus métodos son subprocesos para habilitar este uso compartido.

<a name="DefineIndex"></a>
## <a name="iii-define-your-azure-search-index-using-the-index-class"></a>III. Definir el índice de búsqueda de Azure con los `Index` clase
Una sola llamada a la `Indexes.Create` método creará el índice. Este método toma como parámetro un `Index` objeto que define el índice de búsqueda de Azure. Tiene que crear una `Index` objeto e inicialización como sigue:

1. Establecer el `Name` propiedad de la `Index` objeto en el nombre del índice.
2. Establecer el `Fields` propiedad de la `Index` objeto a una matriz de `Field` objetos. Cada uno de los `Field` objetos define el comportamiento de un campo en el índice. Puede proporcionar el nombre del campo al constructor, junto con el tipo de datos (o el analizador de campos de cadena). También puede establecer otras propiedades, como `IsSearchable`, `IsFilterable`, etcetera.

Es importante tener sus necesidades de negocio y experiencia de usuario de búsqueda en cuenta al diseñar el índice cada `Field` debe asignar las [propiedades apropiadas](https://msdn.microsoft.com/library/azure/dn798941.aspx). Estos control de propiedades que buscar características (filtrado, faceting, ordenación de búsqueda de texto, etc.) que se aplica a los campos. Para cualquier propiedad no establece explícitamente, la `Field` valores predeterminados para deshabilitar la característica de búsqueda correspondiente a menos que habilite específicamente de clase.

En nuestro ejemplo, hemos denominado nuestro índice "hoteles" y define los campos como sigue:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Hemos elegido cuidadosamente los valores de propiedad para cada `Field` en función de cómo creemos que se van a utilizar en una aplicación. Por ejemplo, es probable que personas buscando hoteles interesado en coincidencias de palabra clave en el `description` campo para habilitar la búsqueda de texto completo de ese campo estableciendo `IsSearchable` a `true`.

Tenga en cuenta que exactamente un campo en el índice de tipo `DataType.String` debe estar designado como el campo de _clave_ estableciendo `IsKey` a `true` (consulte `hotelId` en el ejemplo anterior).

La definición de índice anterior utiliza un analizador de lenguaje personalizado para la `description_fr` campo porque está pensado para almacenar texto en francés. Ver [el idioma del tema en MSDN de soporte técnico](https://msdn.microsoft.com/library/azure/dn879793.aspx) así como la correspondiente [entrada de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) para obtener más información acerca de los analizadores de idioma.

> [AZURE.NOTE]  Tenga en cuenta que pasando `AnalyzerName.FrLucene` en el constructor, el `Field` automáticamente será de tipo `DataType.String` y tendrán `IsSearchable` establecido en `true`.

## <a name="iv-create-the-index"></a>IV. Crear el índice
Ahora que ya tiene inicializado `Index` objeto, puede crear el índice simplemente llamando `Indexes.Create` en su `SearchServiceClient` objeto:

```csharp
serviceClient.Indexes.Create(definition);
```

Para una solicitud de éxito, el método devolverá normalmente. Si hay un problema con la solicitud como un parámetro no válido, el método producirá `CloudException`.

Cuando haya terminado con un índice y desea eliminarla, simplemente llame a la `Indexes.Delete` método en su `SearchServiceClient`. Por ejemplo, esto es cómo se elimina el índice "hoteles":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] El código de ejemplo en este artículo utiliza los métodos sincrónicos de Azure búsqueda .NET SDK para simplificar. Se recomienda usar los métodos asincrónicos en sus propias aplicaciones para mantenerlas a scalable y capacidad de respuesta. Por ejemplo, en los ejemplos anteriores podría usar `CreateAsync` y `DeleteAsync` en lugar de `Create` y `Delete`.

## <a name="next"></a>Siguiente
Después de crear un índice de búsqueda de Azure, estará listo para [cargar el contenido en el índice](search-what-is-data-import.md) para poder empezar a buscar los datos.
