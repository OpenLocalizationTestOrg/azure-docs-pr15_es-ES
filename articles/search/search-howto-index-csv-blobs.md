<properties
pageTitle="Indización BLOB CSV con indizador de blobs de Windows Azure búsqueda | Microsoft Azure"
description="Obtenga información sobre cómo indizar BLOB CSV con búsqueda de Azure"
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
ms.date="07/12/2016"
ms.author="eugenesh" />

# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indización BLOB CSV con indizador de blobs de Windows Azure búsqueda 

De forma predeterminada, analiza [indizador de blobs de Windows Azure búsqueda](search-howto-indexing-azure-blob-storage.md) delimitados BLOB de texto como un bloque de texto. Sin embargo, con BLOB que contiene datos CSV, con qué frecuencia desea tratar cada línea en el blob como un documento diferente. Por ejemplo, si el siguiente texto delimitado: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

es posible que desee analizar en los documentos de 2, cada uno que contiene los campos de "etiquetas", "datePublished" y "id".

En este artículo aprenderá cómo analizar BLOB CSV con un indizador de blobs de Windows Azure búsqueda. 

> [AZURE.IMPORTANT] Esta funcionalidad está en vista previa. Está disponible únicamente en la API de REST con versión **2015 02-28-vista previa**. Por favor, recuerde, obtener una vista previa API están pensadas para probar y evaluación y no debe usarse en entornos de producción. 

## <a name="setting-up-csv-indexing"></a>Configuración de indización CSV

Indizar BLOB CSV, crear o actualizar una definición del indizador con la `delimitedText` modo de análisis:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Para obtener más detalles sobre la API de indizador crear, consulte [Crear indizador](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders`indica que la primera línea (en blanco) de cada blob contiene encabezados.
Si BLOB no contiene una línea de cabecera inicial, se deben especificar los encabezados en la configuración del indizador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Actualmente, solo la codificación UTF-8 es compatible. Además, solo la coma `','` se admite el carácter como delimitador. Si necesita soporte técnico para otras codificaciones o delimitadores, háganoslo saber en [nuestro sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Cuando se utiliza el modo de análisis de texto delimitado, búsqueda de Azure supone que todos los blobs en el origen de datos será CSV. Si necesita admitir una mezcla de CSV y no CSV BLOB en el mismo origen de datos, háganoslo saber en [nuestro sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="request-examples"></a>Ejemplos de solicitud

Poner este todas las juntas, estos son los ejemplos de carga completa. 

Origen de datos: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indizador:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Ayúdenos a mejorar la búsqueda de Azure

Si tiene solicitudes de características o ideas para mejorar, por favor, llegar a nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).