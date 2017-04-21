<properties
    pageTitle="Cargar datos de uso de la API de REST de búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Obtenga información sobre cómo cargar datos en un índice de búsqueda de Azure con la API de REST."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Cargar datos con la API de REST de búsqueda de Azure
> [AZURE.SELECTOR]
- [Información general](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)

En este artículo le mostrará cómo usar la [API de REST de búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) para importar datos en un índice de búsqueda de Azure.

Antes de comenzar este tutorial, debe tener ya [creó un índice de búsqueda de Azure](search-what-is-an-index.md).

Para poder insertar documentos en el índice con la API de REST, emite una solicitud HTTP POST al extremo de dirección URL de su índice. El texto del cuerpo de la solicitud HTTP es un objeto JSON que contiene los documentos para agregar, modificar o eliminar.

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Puedo. Identificar la clave de api de administración del servicio de búsqueda de Azure
Cuando se emiten solicitudes HTTP con el servicio con la API de REST, *cada* API solicitud debe incluir la clave de api generado para el servicio de búsqueda que se aprovisione. Tener una clave válida establece la confianza, de forma por solicitud, entre la aplicación que envía la solicitud y el servicio que la administra.

1. Para buscar claves api de su servicio debe iniciar sesión en el [Portal de Azure](https://portal.azure.com/)
2. Vaya a módulo de su servicio de búsqueda de Azure
3. Haga clic en el icono "Teclas"

Su servicio tendrá *claves de administración* y *consulta*.

  - Las *claves de administración* de primario y secundario conceder derechos completos a todas las operaciones, incluida la capacidad para administrar el servicio, crear y eliminar índices, indizadores y orígenes de datos. Existen dos claves para que puedan continuar usar la clave secundaria si decide regenerar la clave principal y viceversa.
  - Las *teclas de consulta* conceder acceso de solo lectura a índices y documentos y se distribuye normalmente a aplicaciones cliente que emiten solicitudes de búsqueda.

A los efectos de importación de datos en un índice, puede usar la clave de administración primaria o secundaria.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Decidir qué acción indización usar
Cuando se usa la API de REST, se emiten solicitudes de HTTP POST con organismos de solicitud JSON a la dirección URL del extremo del índice de la búsqueda de Azure. El objeto JSON en el cuerpo de la solicitud HTTP contendrá una matriz JSON denominada "valor" que contiene objetos JSON que representan los documentos que desea agregar el índice, actualizar o eliminar.

Cada objeto JSON de la matriz "valor" representa un documento que se van a indizar. Cada uno de estos objetos contiene la clave del documento y especifica la acción que desee de la indización (carga, combinar, eliminar, etcetera). Dependiendo de cuál de las debajo de acciones que elija, sólo algunos campos deben incluirse para cada documento:

@search.action | Descripción | Campos necesarios para cada documento | Notas
--- | --- | --- | ---
`upload` | Un `upload` acción es similar a un "upsert" donde se inserta si es nuevo y actualice o reemplace si existe el documento. | clave, además de todos los campos que desea definir | Al actualizar o reemplazar un documento existente, cualquier campo que no se especifica en la solicitud tendrá su campo establecido en `null`. Esto ocurre incluso cuando el campo se estableció anteriormente como un valor no nulo.
`merge` | Actualiza un documento existente con los campos especificados. Si el documento no existe en el índice, se producirá un error en la combinación. | clave, además de todos los campos que desea definir | Cualquier campo que especifique en una combinación reemplazará el campo existente en el documento. Esto incluye los campos de tipo `Collection(Edm.String)`. Por ejemplo, si el documento contiene un campo `tags` con valor `["budget"]` y ejecutar una combinación con el valor `["economy", "pool"]` para `tags`, el valor final de la `tags` campo será `["economy", "pool"]`. No será `["budget", "economy", "pool"]`.
`mergeOrUpload` | Esta acción se comporta como `merge` si ya existe un documento con la clave especificada en el índice. Si el documento no existe, se comporta como `upload` con un nuevo documento. | clave, además de todos los campos que desea definir | -
`delete` | Quita del documento especificado desde el índice. | sólo clave | Los campos se especifica que el campo de clave se pasará por alto. Si desea quitar un campo individual de un documento, use `merge` en su lugar y simplemente establece el campo explícitamente en null.

## <a name="iii-construct-your-http-request-and-request-body"></a>III. Crear su solicitud HTTP y el cuerpo de la solicitud
Ahora que ha recopilado los valores de campo necesarios para las acciones de índice, ya está listo para crear la solicitud HTTP real y el cuerpo de la solicitud JSON para importar los datos.

#### <a name="request-and-request-headers"></a>Solicitud y los encabezados de solicitud
En la dirección URL, debe proporcionar el nombre de servicio, el nombre de índice ("hoteles" en este caso), así como la versión correcta de la API (la versión actual de la API es `2015-02-28` en el momento de la publicación de este documento). Debe definir el `Content-Type` y `api-key` encabezados de solicitud. Para la segunda, utilice una de las claves de administración del servicio.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Cuerpo de la solicitud

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
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
            "description_fr": "Hôtel le moins cher en ville",
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
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

En este caso, estamos usando `upload`, `mergeOrUpload`, y `delete` como nuestras acciones de búsqueda.

Supongamos que este índice de ejemplo "hoteles" se ha rellenado con un número de documentos. Observe cómo no ha habido especificar todos los campos del documento posibles al utilizar `mergeOrUpload` y cómo se especifica solo la clave de documento (`hotelId`) cuando se usa `delete`.

Además, tenga en cuenta que solo puede incluir hasta 1000 documentos (o 16 MB) en una sola solicitud de indización.

## <a name="iv-understand-your-http-response-code"></a>IV. Entender el código de respuesta HTTP
#### <a name="200"></a>200
Después de enviar una solicitud de indización correcta recibirán una respuesta HTTP con código de estado de `200 OK`. El cuerpo JSON de la respuesta HTTP será como sigue:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Un código de estado de `207` se devolverán cuando no se ha indizado correctamente al menos un elemento. El cuerpo JSON de la respuesta HTTP contendrá información sobre los documentos incorrectas.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] A menudo, esto significa que la carga en el servicio de búsqueda se comunique con un punto donde se iniciará la indización solicitudes para devolver `503` las respuestas. En este caso, se altamente recomienda el código de cliente volver a desactivar y esperar antes de Reintentar. Esto le proporcionará el sistema algún tiempo para recuperar, aumenta las posibilidades que se realice correctamente las solicitudes futuras. Reintentar rápidamente las convocatorias, solo se prolongan la situación.

#### <a name="429"></a>429
Un código de estado de `429` se devolverán cuando se ha superado la cuota del número de documentos por índice.

#### <a name="503"></a>503
Un código de estado de `503` se devolverá si ninguno de los elementos de la solicitud se han indizado correctamente. Este error significa que el sistema tiene mucha carga y no se puede procesar su solicitud en este momento.

> [AZURE.NOTE] En este caso, se altamente recomienda el código de cliente volver a desactivar y esperar antes de Reintentar. Esto le proporcionará el sistema algún tiempo para recuperar, aumenta las posibilidades que se realice correctamente las solicitudes futuras. Reintentar rápidamente las convocatorias, solo se prolongan la situación.

Para obtener más información sobre las acciones de documentos y las respuestas de éxito o error, vea [Agregar, actualizar o eliminar documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx). Para obtener más información sobre otros códigos de estado HTTP que se podría devolver en caso de error, consulte [códigos de estado HTTP (búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## <a name="next"></a>Siguiente
Tras rellenar el índice de búsqueda de Azure, estará listo para empezar a emitir consultas para buscar documentos. Para obtener información detallada, vea [Consulta el índice de búsqueda de Azure](search-query-overview.md) .
