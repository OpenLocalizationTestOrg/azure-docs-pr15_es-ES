<properties
pageTitle="Indización BLOB JSON con indizador de blobs de Windows Azure búsqueda"
description="Indización BLOB JSON con indizador de blobs de Windows Azure búsqueda"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="07/26/2016"
ms.author="eugenesh" />

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indización BLOB JSON con indizador de blobs de Windows Azure búsqueda 

Este artículo le muestra cómo configurar indizador de blobs de Windows Azure búsqueda para extraer contenido estructurado de BLOB que contienen JSON.

## <a name="scenarios"></a>Escenarios

De forma predeterminada, [indizador de blobs de Windows Azure búsqueda](search-howto-indexing-azure-blob-storage.md) analiza BLOB JSON como un bloque de texto. A menudo, desea conservar la estructura de los documentos JSON. Por ejemplo, si el documento JSON 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

desea analizar en un documento de búsqueda de Azure con los campos de "etiquetas", "datePublished" y "texto".

Como alternativa, cuando el BLOB contiene una **matriz de objetos JSON**, puede que prefiera cada elemento de la matriz puede convertirse en un documento independiente de búsqueda de Azure. Por ejemplo, si un blob con este JSON:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

puede rellenar el índice de búsqueda de Azure con 3 documentos independientes, cada una con campos "id" y "texto". 

> [AZURE.IMPORTANT] Esta funcionalidad está en vista previa. Está disponible únicamente en la API de REST con versión **2015 02-28-vista previa**. Por favor, recuerde, obtener una vista previa API están pensadas para probar y evaluación y no debe usarse en entornos de producción. 

## <a name="setting-up-json-indexing"></a>Configuración de indización de JSON

Indizar BLOB JSON, establezca el `parsingMode` parámetro de configuración para `json` (para cada blob como un único documento de índice) o `jsonArray` (si su BLOB contiene una matriz JSON): 

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

Si es necesario, use **las asignaciones de campos** para elegir las propiedades del documento de origen JSON utilizado para rellenar el índice de búsqueda de destino.  Esto se describe en detalle a continuación. 

> [AZURE.IMPORTANT] Cuándo usar `json` o `jsonArray` modo de análisis, búsqueda de Azure supone que todos los blobs en el origen de datos será JSON. Si necesita admitir una mezcla de JSON y JSON no BLOB en el mismo origen de datos, háganoslo saber en [nuestro sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="using-field-mappings-to-build-search-documents"></a>Uso de las asignaciones de campos para crear documentos de búsqueda 

Actualmente, búsqueda de Azure no es posible indizar arbitrarios documentos JSON directamente, ya que admite tipos de datos solo simples, matrices de cadenas y GeoJSON puntos. Sin embargo, puede usar **las asignaciones de campos** Seleccione partes del documento JSON y "Levantar" ellos en los campos de nivel superior del documento de búsqueda. Para obtener información sobre los conceptos básicos de las asignaciones de campo, consulte [asignaciones de campos de búsqueda de Azure indizador puente de las diferencias entre los orígenes de datos y los índices de búsqueda](search-indexer-field-mappings.md).

Volver a nuestro documento JSON de ejemplo: 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Supongamos que tiene un índice de búsqueda con los siguientes campos: `text` de tipo Edm.String, `date` de tipo Edm.DateTimeOffset, y `tags` de tipo Collection(Edm.String). Para asignar el JSON en la forma deseada, utilice las asignaciones de campos siguientes: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]

Los nombres de campo de origen en las asignaciones se especifican utilizando la notación [JSON puntero](http://tools.ietf.org/html/rfc6901) . Empezar con una barra diagonal para hacer referencia a la raíz del documento JSON y explorar en profundidad la propiedad deseada (nivel arbitrario de anidamiento) usando ruta separados barra diagonal hacia delante. 

También puede hacer referencia a elementos individuales de la matriz mediante un índice basado en cero. Por ejemplo, para seleccionar el primer elemento de la matriz "etiquetas" del ejemplo anterior, use una asignación de campo similar a esta:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Si un nombre de campo de origen en una ruta de acceso de asignación de campo hace referencia a una propiedad que no existe en JSON, se omite esa asignación sin un error. Esto se hace para que podamos admitimos documentos con un esquema diferente (que es un caso de uso común). Porque no hay ninguna validación, debe tener cuidado para evitar errores tipográficos en la especificación de la asignación de campo. 

Si los documentos JSON solo contienen propiedades simples de nivel superior, no tendrá las asignaciones de campos en absoluto. Por ejemplo, si su JSON es similar a esta, las propiedades de nivel superior "texto", "datePublished" y "etiquetas" directamente asignará a los campos correspondientes del índice de búsqueda: 
 
    { 
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
    }

## <a name="indexing-nested-json-arrays"></a>Indización de matrices JSON anidadas

¿Qué sucede si desea indizar una matriz de objetos JSON, pero dicha matriz está anidado en algún lugar dentro del documento? Puede elegir qué propiedad contiene la matriz mediante la `documentRoot` propiedad de configuración. Por ejemplo, si su BLOB tiene este aspecto: 

    { 
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" }, 
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    } 

Use esta configuración para indizar la matriz de la propiedad "nivel 2": 

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


## <a name="request-examples"></a>Ejemplos de solicitud

Poner este todas las juntas, estos son los ejemplos de cargas completa. 

Origen de datos: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Indizador:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
    }

## <a name="help-us-make-azure-search-better"></a>Ayúdenos a mejorar la búsqueda de Azure

Si tiene solicitudes de características o ideas para mejorar, por favor, llegar a nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).