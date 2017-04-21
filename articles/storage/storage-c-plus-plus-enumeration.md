<properties
    pageTitle="La lista de recursos de almacenamiento de Azure con la biblioteca de cliente de Microsoft Azure almacenamiento de C++ | Microsoft Azure"
    description="Obtenga información sobre cómo usar la API de lista en la biblioteca de cliente de Microsoft Azure almacenamiento de C++ para enumerar los contenedores, BLOB, colas, tablas y entidades."
    documentationCenter=".net"
    services="storage"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="list-azure-storage-resources-in-c"></a>Lista de recursos de almacenamiento de Azure en C++

Operaciones de lista son clave para muchos escenarios de desarrollo con el almacenamiento de Azure. En este artículo se describe cómo enumerar más eficazmente los objetos de almacenamiento de Azure mediante el listado API proporcionadas en la biblioteca de cliente de Microsoft Azure almacenamiento de C++.

>[AZURE.NOTE] Esta guía está destinado a la biblioteca de cliente de almacenamiento de Azure para C++ versión 2.x, que está disponible a través de [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp).

La biblioteca de cliente de almacenamiento proporciona una gran variedad de métodos de objetos de consulta o lista en el almacenamiento de Azure. Este artículo tratan las situaciones siguientes:

-   Contenedores de lista en una cuenta
-   Lista BLOB en un contenedor o directorio virtual blob
-   Colas de lista en una cuenta
-   Lista de tablas en una cuenta
-   Entidades de consulta en una tabla

Cada uno de estos métodos se muestra con distintas cargas para diferentes escenarios.

## <a name="asynchronous-versus-synchronous"></a>Asincrónica frente a sincrónico

Debido a la biblioteca de cliente de almacenamiento para C++ se basa en la [biblioteca de C++ resto](https://github.com/Microsoft/cpprestsdk), admitimos inherente operaciones asincrónicas utilizando [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Por ejemplo:

    pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;

Operaciones sincrónicas ajustar las operaciones asincrónicas correspondientes:

    list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
    {
        return list_blobs_segmented_async(token).get();
    }

Si está trabajando con varias aplicaciones o servicios de multithreading, recomendamos usar la API asíncronas directamente en lugar de crear un subproceso para llamar a la API, lo que afecta considerablemente el rendimiento de la sincronización.

## <a name="segmented-listing"></a>Listado segmentado

La escala de almacenamiento cloud requiere listado segmentado. Por ejemplo, puede tener sobre un millón BLOB en un contenedor de blobs de Windows Azure o sobre las entidades de mil millones de una tabla de Azure. No son números teóricos, pero los casos de uso de cliente real.

Por lo tanto, resulta práctico para obtener una lista de todos los objetos en una sola respuesta. En su lugar, puede mostrar objetos con paginación. Cada uno de la lista de las API de tiene un *segmentado* sobrecarga.

La respuesta para una operación de anuncio segmentado incluye:

-   <i>_segment</i>, que contiene el conjunto de resultados para una única llamada a la API de entrada.
-   *continuation_token*, que se pasa a la llamada siguiente con el fin de obtener la siguiente página de resultados. Si no hay ningún más resultados para devolver, el token de continuación es nulo.

Por ejemplo, una llamada típica a todos los blobs en un contenedor de lista tendrá el aspecto del fragmento de código siguiente. El código está disponible en nuestros [ejemplos](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

    // List blobs in the blob container
    azure::storage::continuation_token token;
    do
    {
        azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_diretory(it->as_directory());
        }
    }

        token = segment.continuation_token();
    }
    while (!token.empty());

Tenga en cuenta que el número de resultados en una página puede controlar el parámetro *max_results* en la sobrecarga de cada API, por ejemplo:

    list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
        blob_listing_details::values includes, int max_results, const continuation_token& token,
        const blob_request_options& options, operation_context context)

Si no especifica el parámetro *max_results* , se devuelve el valor máximo predeterminado de hasta 5.000 resultados en una sola página.

Tenga en cuenta que puede devolver una consulta en el almacenamiento de tablas de Azure sin registros o menos registros que el valor del parámetro *max_results* especificada, incluso si el token de continuación no está vacío. Puede ser una razón por la que no pudo completar la consulta en cinco segundos. Mientras el token de continuación no está vacío, la consulta debe continuar y su código no debería suponer el tamaño de los resultados de segmento.

El modelo de codificación recomendado para la mayoría de los escenarios está segmentado listado, que proporciona información de progreso explícito de entrada o consulta y cómo el servicio responde a cada solicitud. Especialmente para aplicaciones de C++ o servicios, puede ayudar a control de nivel inferior listado del progreso de la memoria de control y el rendimiento.

## <a name="greedy-listing"></a>Listado gracias

Las versiones anteriores de la biblioteca de cliente de almacenamiento para C++ (versiones 0.5.0 obtener una vista previa y versiones anteriores) incluido no segmentado listado API para las tablas y colas, como en el ejemplo siguiente:

    std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
    std::vector<table_entity> execute_query(const table_query& query) const;
    std::vector<cloud_queue> list_queues() const;

Estos métodos se implementaron como contenedores de API segmentados. Para cada respuesta de anuncio segmentado, el código anexa los resultados a un vector y devuelve todos los resultados después de que se han analizado los contenedores completos.

Este enfoque funcionará cuando la cuenta de almacenamiento o la tabla contiene un pequeño número de objetos. Sin embargo, con un aumento en el número de objetos, la memoria necesaria podría aumentar sin límite, porque permanecieron todos los resultados en la memoria. Una operación de lista puede tardar mucho tiempo, durante el que el llamador no tenía ninguna información sobre el progreso.

Estas API en el SDK de anuncio gracias no existen en C#, Java o el entorno de JavaScript Node.js. Para evitar los posibles problemas de uso de estas API gracias, se quitó en versión 0.6.0 vista previa.

Si el código llama a estas API gracias:

    std::vector<azure::storage::table_entity> entities = table.execute_query(query);
    for (auto it = entities.cbegin(); it != entities.cend(); ++it)
    {
        process_entity(*it);
    }

A continuación, debe modificar el código para usar el listado segmentado API:

    azure::storage::continuation_token token;
    do
    {
        azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
        {
            process_entity(*it);
        }

        token = segment.continuation_token();
    } while (!token.empty());

Al especificar el parámetro *max_results* del segmento, puede equilibrar entre los números de peticiones y el uso de la memoria para cumplir con las consideraciones de rendimiento de la aplicación.

Además, si está usando listado segmentado API, pero almacenar los datos en una colección local en un estilo "Gracias", también recomendamos encarecidamente que refactorizar el código para controlar los datos se almacenan en una colección local con cuidado en escala.

## <a name="lazy-listing"></a>Listado diferida

Aunque listado gracias produce posibles problemas, es conveniente si no hay demasiados objetos en el contenedor.

Si también usa C# o Oracle Java SDK, debe estar familiarizado con el modelo de programación Enumerable, que ofrece un listado, donde los datos en un determinado desplazamiento sólo se recuperan si es necesario diferida estilo. En C++, la plantilla de iterador también proporciona un enfoque similar.

Una lista diferida típica API, utilizando **list_blobs** como ejemplo, el siguiente aspecto:

    list_blob_item_iterator list_blobs() const;

Un fragmento de código típicos que utiliza el modelo de anuncio diferida podría tener este aspecto:

    // List blobs in the blob container
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_directory(it->as_directory());
        }
    }

Tenga en cuenta que listado diferida sólo está disponible en el modo sincrónico.

En comparación con gracias listado, listado diferida recupera datos solo cuando sea necesario. En segundo plano, recupera los datos de almacenamiento de Azure solo cuando el iterador siguiente pasará a la siguiente segmento. Por lo tanto, el uso de memoria está controlado con un tamaño limitado y la operación es rápida.

Listado diferida API se incluyen en la biblioteca de cliente de almacenamiento para C++ en versión 2.2.0.

## <a name="conclusion"></a>Conclusión

En este artículo, hemos tratado distintas cargas para incluir las API para varios objetos de la biblioteca de cliente de almacenamiento para C++. En resumen:

-   Async APIs se recomiendan en varios escenarios de multithreading.
-   Listado segmentado se recomienda para la mayoría de los escenarios.
-   Listado diferida está disponible en la biblioteca como un contenedor adecuado en escenarios sincrónicos.
-   Listado gracias no es aconsejable y se ha quitado de la biblioteca.

##<a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el almacenamiento de Azure y la biblioteca de cliente de C++, vea los siguientes recursos.

-   [Cómo usar el almacenamiento de blobs de C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Cómo usar el almacenamiento de tablas desde C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Cómo usar el almacenamiento de la cola de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Biblioteca de cliente Azure almacenamiento para la documentación de la API de C++.](http://azure.github.io/azure-storage-cpp/)
-   [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   [Documentación de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
