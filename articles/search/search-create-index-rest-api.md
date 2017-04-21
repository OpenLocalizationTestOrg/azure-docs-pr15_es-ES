<properties
    pageTitle="Crear un índice de búsqueda de Azure con la API de REST | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Crear un índice en código con la API de REST de Azure búsqueda HTTP."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-rest-api"></a>Crear un índice de búsqueda de Azure con la API de REST
> [AZURE.SELECTOR]
- [Información general](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


En este artículo le guiará a través del proceso de creación de una búsqueda de Azure [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) con la API de REST de búsqueda de Azure.

Antes de seguir esta guía y crear un índice, debe tener ya [creó un servicio de búsqueda de Azure](search-create-service-portal.md).

Para crear un índice de búsqueda de Azure con la API de REST, emite una solicitud HTTP POST al extremo de la dirección URL de su servicio Búsqueda de Azure. La definición de índice se incluirán en el cuerpo de la solicitud como contenido JSON correcto.


## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Puedo. Identificar la clave de api de administración del servicio de búsqueda de Azure
Ahora que ha proporcionado un servicio de búsqueda de Azure, puede emitir solicitudes HTTP en el extremo de la dirección URL de su servicio con la API de REST. Sin embargo, las solicitudes de *todas las* API deben incluir la clave de api generado para el servicio de búsqueda que se aprovisione. Tener una clave válida establece la confianza, de forma por solicitud, entre la aplicación que envía la solicitud y el servicio que la administra.

1. Para buscar claves api de su servicio debe iniciar sesión en el [Portal de Azure](https://portal.azure.com/)
2. Vaya a módulo de su servicio de búsqueda de Azure
3. Haga clic en el icono "Teclas"

Su servicio tendrá *claves de administración* y *consulta*.

 - Las *claves de administración* de primario y secundario conceder derechos completos a todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indizadores y orígenes de datos. Existen dos claves para que puedan continuar usar la clave secundaria si decide regenerar la clave principal y viceversa.
 - Las *teclas de consulta* conceder acceso de solo lectura a índices y documentos y se distribuye normalmente a aplicaciones cliente que emiten solicitudes de búsqueda.

Para el propósito de crear un índice, puede usar la clave de administración primaria o secundaria.

## <a name="ii-define-your-azure-search-index-using-well-formed-json"></a>II. Definir el índice de búsqueda de Azure con JSON correcto
Una sola solicitud de HTTP POST en el servicio creará el índice. El cuerpo de la solicitud de HTTP POST contendrá un único objeto JSON que define el índice de búsqueda de Azure.

1. La primera propiedad de este objeto JSON es el nombre del índice.
2. La segunda propiedad de este objeto JSON es una matriz JSON denominada `fields` que contiene un objeto JSON independiente para cada campo en el índice. Cada uno de estos objetos JSON contiene varios pares de nombre/valor para cada uno de los atributos del campo incluidos "nombre", "tipo", etcetera.

Es importante tener sus necesidades de negocio y experiencia de usuario de búsqueda en cuenta al diseñar el índice como cada campo debe tener asignado los [atributos adecuados](https://msdn.microsoft.com/library/azure/dn798941.aspx). Estos control de atributos que buscar características (filtrado, faceting, ordenación de búsqueda de texto, etc.) que se aplica a los campos. Para cualquier atributo que no se especifica, el valor predeterminado será habilitar la característica de búsqueda correspondiente a menos que se deshabilite específicamente.

En nuestro ejemplo, hemos denominado nuestro índice "hoteles" y define los campos como sigue:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Hemos elegido cuidadosamente los atributos de índice para cada campo en función de cómo creemos que se van a utilizar en una aplicación. Por ejemplo, `hotelId` es una clave única que los usuarios buscar hoteles es muy probable que no conoce, para deshabilitar la búsqueda de texto completo de ese campo estableciendo `searchable` a `false`, que ahorra espacio en el índice.

Tenga en cuenta que exactamente un campo en el índice de tipo `Edm.String` debe estar designado como campo 'clave'.

La definición de índice anterior utiliza un analizador de lenguaje personalizado para la `description_fr` campo porque está pensado para almacenar texto en francés. Ver [el idioma del tema en MSDN de soporte técnico](https://msdn.microsoft.com/library/azure/dn879793.aspx) así como la correspondiente [entrada de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) para obtener más información acerca de los analizadores de idioma.

## <a name="iii-issue-the-http-request"></a>III. Enviar la solicitud HTTP
1. Con la definición de índice como el cuerpo de la solicitud, emitir una solicitud de HTTP POST a la URL de extremo de servicio de búsqueda de Azure. En la dirección URL, asegúrese de usar su nombre de servicio como el nombre de host y a continuación, coloque el propio `api-version` como un parámetro de cadena de consulta (la versión actual de la API es `2015-02-28` en el momento de la publicación de este documento).
2. En los encabezados de la solicitud, especifique la `Content-Type` como `application/json`. También debe proporcionar la clave de administración del servicio que identifica en el paso I en la `api-key` encabezado.


Tendrá que proporcionar su propia clave de api y el nombre de servicio para enviar la solicitud a continuación:


    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [api-key]


Si la solicitud es correcta, debería aparecer el código de estado 201 (creado). Para obtener más información sobre cómo crear un índice a través de la API de REST, visite la referencia de la API en [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx). Para obtener más información sobre otros códigos de estado HTTP que se podría devolver en caso de error, consulte [códigos de estado HTTP (búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

Cuando haya terminado con un índice y desea eliminarla, simplemente emitir una solicitud HTTP DELETE. Por ejemplo, esto es cómo se elimina el índice "hoteles":

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]


## <a name="next"></a>Siguiente
Después de crear un índice de búsqueda de Azure, estará listo para [cargar el contenido en el índice](search-what-is-data-import.md) para poder empezar a buscar los datos.
