<properties
    pageTitle="Consultar el índice de búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Crear una consulta de búsqueda de Azure y use los parámetros de búsqueda para filtrar y ordenar los resultados de búsqueda."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index"></a>Consultar el índice de búsqueda de Azure
> [AZURE.SELECTOR]
- [Información general](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Cuando envíe solicitudes de búsqueda Buscar Azure, hay un número de parámetros que se pueden especificar junto con las palabras reales que se escriben en el cuadro de búsqueda de la aplicación. Estos parámetros de consulta permiten lograr algún control más profundo de la experiencia de búsqueda de texto.

A continuación se muestra una lista que explica brevemente usos comunes de los parámetros de consulta de búsqueda de Azure. Para obtener información detallada de los parámetros de consulta y su comportamiento, vea las páginas de detalles de la [API de REST](https://msdn.microsoft.com/library/azure/dn798927.aspx) y [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx).

## <a name="types-of-queries"></a>Tipos de consultas

Búsqueda de Azure ofrece muchas opciones para crear consultas muy eficaces. Los dos tipos principales de consulta que se usa son `search` y `filter`. A `search` consulta busca uno o varios términos en todos los campos de _búsqueda_ en el índice y funciona de la manera que esperaría un motor de búsqueda como Google o Bing para trabajar. A `filter` consulta evalúa una expresión booleana todos _pueden filtrarse_ campos en un índice. A diferencia de `search` consultas, `filter` consultas que coincida con el contenido de un campo, lo que significa que se distinguen mayúsculas de minúsculas para los campos de cadena exacto.

Puede usar filtros y búsquedas conjuntamente o por separado. Si usa conjuntamente, el filtro se aplica primero a todo el índice y, a continuación, se realiza la búsqueda en los resultados del filtro. Filtros, por tanto, pueden ser una técnica útil para mejorar el rendimiento de la consulta desde que reducen el conjunto de documentos que debe procesar la consulta de búsqueda.

La sintaxis de expresiones de filtro es un subconjunto del [lenguaje de filtro de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx). Para las consultas de búsqueda puede usar la [sintaxis simplificada](https://msdn.microsoft.com/library/azure/dn798920.aspx) o la [sintaxis de la consulta de Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) que se describen a continuación.

### <a name="simple-query-syntax"></a>Sintaxis de la consulta simple
La [sintaxis de la consulta simple](https://msdn.microsoft.com/library/azure/dn798920.aspx) es el lenguaje de consulta predeterminado utilizado en búsqueda de Azure. La sintaxis de la consulta simple es compatible con un número de operadores de búsqueda comunes incluidos y, o no, frase, sufijo y operadores de prioridad.

### <a name="lucene-query-syntax"></a>Sintaxis de la consulta de Lucene
La [sintaxis de la consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) le permite utilizar el lenguaje de consulta ampliamente adoptado y expresivos desarrollado como parte de [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Usar la sintaxis de la consulta le permite lograr fácilmente las siguientes capacidades: [consultas de ámbito de campo](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [búsqueda parcial](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [búsqueda de proximidad](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [incremento de términos](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [búsqueda de expresiones regulares](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [búsqueda con caracteres comodín](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [aspectos básicos de la sintaxis](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)y [consultas con operadores booleanos](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## <a name="ordering-results"></a>Ordenar los resultados
Al recibir resultados de una consulta de búsqueda, puede solicitar que la búsqueda de Azure sirve los resultados ordenados por valores en un campo específico. De forma predeterminada, búsqueda de Azure pedidos de los resultados de búsqueda en función de la jerarquía de la puntuación de búsqueda de cada documento, que se deriva de [TF IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Si desea que la búsqueda de Azure para devolver los resultados ordenados por un valor distinto de la puntuación de la búsqueda, puede usar el `orderby` parámetro de búsqueda. Puede especificar el valor de la `orderby` parámetro para incluir los nombres de campo y las llamadas a la [ `geo.distance()` función](https://msdn.microsoft.com/library/azure/dn798921.aspx) geoespaciales valores. Cada expresión puede ir seguido `asc` para indicar que se ha solicitado resultados en orden ascendente, y `desc` para indicar que se ha solicitado resultados en orden descendente. La clasificación predeterminada orden ascendente.

## <a name="paging"></a>Paginación
Búsqueda de Azure facilita la implementar la paginación de resultados de búsqueda. Mediante el uso de la `top` y `skip` parámetros, suavemente emitir solicitudes de búsqueda que le permiten recibir el conjunto total de resultados de búsqueda en subconjuntos manejables, ordenadas que habilitar fácilmente las prácticas de interfaz de usuario de búsqueda eficaces. Al recibir estos subconjuntos de resultados más pequeños, también puede recibir el recuento de documentos en el conjunto total de resultados de búsqueda.

Puede obtener más acerca de resultados de búsqueda en el artículo de la [página cómo buscar resultados de búsqueda de Azure](search-pagination-page-layout.md)de paginación.


## <a name="hit-highlighting"></a>Resaltado
En búsqueda de Azure, enfatizar la parte exacta de los resultados de búsqueda que coincidan con la consulta de búsqueda se facilita mediante el uso de la `highlight`, `highlightPreTag`, y `highlightPostTag` parámetros. Puede especificar qué campos de _búsqueda_ debe enfatizar, así como especificar las etiquetas de cadena exacta anexar al principio y al final del texto coincidente que la búsqueda de Azure devuelve su texto coincidente.
