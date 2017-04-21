<properties
    pageTitle="Conexión DocumentDB con búsqueda de Azure utilizar indizadores | Microsoft Azure"
    description="En este artículo se muestra cómo usar al indizador de búsqueda de Azure con DocumentDB como origen de datos."
    services="documentdb"
    documentationCenter=""
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="rest-api"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="07/08/2016"
    ms.author="denlee"/>

#<a name="connecting-documentdb-with-azure-search-using-indexers"></a>Conexión DocumentDB con búsqueda de Azure utilizar indizadores

Si está buscando para implementar una gran búsqueda experiencias sobre los datos DocumentDB, utilice indizador de búsqueda de Azure para DocumentDB! En este artículo, le mostraremos cómo integrar DocumentDB de Azure con búsqueda de Azure sin tener que escribir código para mantener la infraestructura de indización!

Para configurar esto, debe [Configurar una cuenta de Azure búsqueda](../search/search-create-service-portal.md) (no es necesario actualizar a búsqueda estándar) y, a continuación, llame a la [API de REST de búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) para crear un **origen de datos** de DocumentDB y un **indizador** para ese origen de datos.

En las solicitudes de enviar pedido para interactuar con las API de REST, puede usar [Postman](https://www.getpostman.com/), [Fiddler](http://www.telerik.com/fiddler)o cualquier otra herramienta de sus preferencias.


##<a id="Concepts"></a>Azure conceptos del indizador de búsqueda

Azure Search es compatible con la creación y administración de datos de orígenes (incluidos DocumentDB) e indizadores que funcionan con los orígenes de datos.

Un **origen de datos** especifica qué datos se necesitan indizar, credenciales para tener acceso a los datos y directivas para habilitar la búsqueda de Azure identificar eficazmente los cambios en los datos (como modificar o eliminar documentos dentro de la colección). El origen de datos está definido como recurso independiente para que se puede usar por varios indizadores.

Un **indizador** describe cómo fluyen los datos del origen de datos en un índice de búsqueda de destino. Debe planear sobre la creación de un indizador para cada combinación de origen de datos y el índice de destino. Aunque puede tener varios indizadores escribiendo en el mismo índice, un indizador sólo puede escribir en un índice único. Un indizador se utiliza para:

- Realizar una copia de los datos para rellenar un índice única.
- Sincronizar un índice con los cambios del origen de datos en una programación. La programación es parte de la definición del indizador.
- Invocar las actualizaciones a petición de un índice según sea necesario.

##<a id="CreateDataSource"></a>Paso 1: Crear un origen de datos

Emitir una solicitud de HTTP POST para crear un nuevo origen de datos en el servicio de búsqueda de Azure, incluidos los encabezados de solicitud siguiente.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

La `api-version` es necesario. Los valores válidos son `2015-02-28` o una versión posterior. Visite [las versiones API de búsqueda de Azure](../search/search-api-versions.md) para ver todas las versiones de la API de búsqueda.

El cuerpo de la solicitud contiene la definición de origen de datos, que debe incluir los siguientes campos:

- **nombre**: elija cualquier nombre para representar la base de datos de DocumentDB.

- **tipo**: usar `documentdb`.

- **credenciales**:

    - **cadena de conexión**: obligatorio. Especifique la información de conexión a la base de datos de Azure DocumentDB en el siguiente formato:`AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **contenedor**:

    - **nombre**: obligatorio. Especificar el identificador de la colección de DocumentDB indizar.

    - **consulta**: opcional. Puede especificar una consulta para acoplar un documento JSON arbitrario en un esquema plano que puede especificar un índice búsqueda de Azure.

- **dataChangeDetectionPolicy**: opcional. Ver [datos directiva de detección de cambio](#DataChangeDetectionPolicy) siguiente.

- **dataDeletionDetectionPolicy**: opcional. Consulte la [política de detección de eliminación de datos](#DataDeletionDetectionPolicy) .

Siga leyendo para un [cuerpo de la solicitud de ejemplo](#CreateDataSourceExample).

###<a id="DataChangeDetectionPolicy"></a>Captura de documentos modificados

El propósito de una directiva de detección de cambio de datos es identificar los elementos de datos modificados de forma eficaz. Actualmente, la única directiva compatible es la `High Water Mark` directiva mediante la `_ts` propiedad de marca de hora de última modificación proporcionada por DocumentDB - que se especifica como sigue:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

También necesitará agregar `_ts` en la proyección y `WHERE` cláusula de la consulta. Por ejemplo:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Capturar documentos eliminados

Cuando se eliminan filas de la tabla de origen, debe eliminar las filas del índice de búsqueda. El propósito de una directiva de detección de eliminación de datos es identificar los elementos de datos eliminados de forma eficaz. Actualmente, la única directiva compatible es la `Soft Delete` directiva (eliminación se marca con un indicador de algún tipo), que se especifica como sigue:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [AZURE.NOTE] Debe incluir la propiedad softDeleteColumnName en la cláusula SELECT si está utilizando una proyección personalizada.

###<a id="CreateDataSourceExample"></a>Ejemplo de cuerpo de solicitud

En el ejemplo siguiente se crea un origen de datos con una sugerencias de consulta y la directiva personalizada:

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###<a name="response"></a>Respuesta

Recibirá una respuesta HTTP 201 creado si el origen de datos se creó correctamente.

##<a id="CreateIndex"></a>Paso 2: Crear un índice

Crear un índice de búsqueda de Azure de destino si aún no tiene una. Puede hacerlo desde la [Interfaz de usuario de Portal de Azure](../search/search-create-index-portal.md) o mediante la [API de índice de crear](https://msdn.microsoft.com/library/azure/dn798941.aspx).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Asegúrese de que el esquema del índice de destino es compatible con el esquema de los documentos de origen JSON o el resultado de la proyección de consulta personalizada.

>[AZURE.NOTE] Colecciones divididas, la clave de documento predeterminado es de DocumentDB `_rid` propiedad, que le cambia el nombre a `rid` en búsqueda de Azure. De Además, DocumentDB `_rid` valores contienen caracteres no válidos en claves de búsqueda de Azure; por lo tanto, la `_rid` valores son base 64 codificado.

###<a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>Figura A: asignación entre tipos de datos JSON y tipos de datos de búsqueda de Azure

| TIPO DE DATOS JSON|   TIPOS DE CAMPO DE DESTINO COMPATIBLES CON ÍNDICE|
|---|---|
|BOOL|Edm.Boolean, Edm.String|
|Números con el aspecto de los enteros|Edm.Int32, Edm.Int64, Edm.String|
|Números del aspecto en ese puntos flotantes|Edm.Double, Edm.String|
|Cadena|Edm.String|
|Por ejemplo, "a", "b", "c" tipos de matrices de simple |Collection(EDM.String)|
|Cadenas que parecen fechas| Edm.DateTimeOffset, Edm.String|
|GeoJSON objetos, por ejemplo, {"tipo": "Punto", "coordenadas": [largo, lat]} | Edm.GeographyPoint |
|Otros objetos JSON|N/A.|

###<a id="CreateIndexExample"></a>Ejemplo de cuerpo de solicitud

En el ejemplo siguiente se crea un índice con un campo de identificador y una descripción:

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###<a name="response"></a>Respuesta

Recibirá una respuesta HTTP 201 creado si se ha creado correctamente el índice.

##<a id="CreateIndexer"></a>Paso 3: Crear un indizador

Puede crear un nuevo indizador dentro de un servicio de búsqueda de Azure mediante una solicitud HTTP POST con los siguientes encabezados.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

El cuerpo de la solicitud contiene la definición del indizador, que debe incluir los siguientes campos:

- **nombre**: obligatorio. El nombre del indizador.

- **NombreDeOrigenDeDatos**: obligatorio. El nombre de un origen de datos existente.

- **targetIndexName**: obligatorio. El nombre de un índice existente.

- **programación**: opcional. Consulte a continuación la [programación de indización](#IndexingSchedule) .

###<a id="IndexingSchedule"></a>Ejecuta indizadores en una programación

Un indizador opcionalmente, puede especificar una programación. Si hay una programación, el indizador se ejecutará periódicamente según la programación. Programación tiene los siguientes atributos:

- **intervalo**: obligatorio. Se ejecuta un valor de duración que especifica un intervalo o un período de indizador. El intervalo más pequeño permitido es 5 minutos. la más larga es un día. Debe tener el formato como un valor de "dayTimeDuration" XSD (un subconjunto de un valor de [duración ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) restringido). El modelo de esto es: `P(nD)(T(nH)(nM))`. Ejemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

- **hora de inicio**: obligatorio. Una fecha y hora UTC que especifica cuándo debe comenzar el indizador ejecutando.

###<a id="CreateIndexerExample"></a>Ejemplo de cuerpo de solicitud

En el ejemplo siguiente se crea un indizador que copie los datos de la colección al que hace referencia la `myDocDbDataSource` de origen de datos a la `mySearchIndex` índice en una programación que empieza el 1 de enero de 2015 UTC y se ejecutará cada hora.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###<a name="response"></a>Respuesta

Recibirá una respuesta HTTP 201 creado si se ha creado correctamente el indizador.

##<a id="RunIndexer"></a>Paso 4: Ejecutar un indizador

Además de ejecutar periódicamente en una programación, un indizador puede invocar también a petición emitir la solicitud de HTTP POST siguiente:

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Respuesta

Recibirá una respuesta HTTP 202 aceptado si el indizador se invoca correctamente.

##<a name="GetIndexerStatus"></a>Paso 5: Obtener el estado del indizador

Puede emitir una solicitud HTTP GET para recuperar el historial de estado y ejecución actual de un indizador:

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Respuesta

Verá una respuesta HTTP 200 OK devuelve junto con el cuerpo de la respuesta que contiene información sobre el estado general del indizador, la última invocación de indizador, así como el historial de las llamadas a indizador recientes (si está presente).

La respuesta debe ser similar al siguiente:

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Historial de ejecución contiene hasta el 50 ejecuciones más recientes completadas se ordenan en orden cronológico inverso (de modo que la última ejecución venga primera en la respuesta).

##<a name="NextSteps"></a>Pasos siguientes

¡Felicidades! Acaba de aprender cómo integrar DocumentDB de Azure con búsqueda de Azure con el indizador para DocumentDB.

 - Para conocer cómo más DocumentDB de Azure, consulte la [página de servicio de DocumentDB](https://azure.microsoft.com/services/documentdb/).

 - Para obtener información sobre cómo más información acerca de la búsqueda de Azure, consulte la [página de servicio de búsqueda](https://azure.microsoft.com/services/search/).
