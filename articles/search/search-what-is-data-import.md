<properties
    pageTitle="Cargar datos en la búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Obtenga información sobre cómo cargar datos en un índice de búsqueda de Azure."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search"></a>Cargar datos en la búsqueda de Azure
> [AZURE.SELECTOR]
- [Información general](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)


## <a name="data-upload-models-in-azure-search"></a>Datos cargar modelos en búsqueda de Azure
Hay dos formas para rellenar el índice de búsqueda de Azure con los datos. La primera opción es insertar manualmente los datos en el índice con la [API de REST](search-import-data-rest-api.md) de búsqueda de Azure o [.NET SDK](search-import-data-dotnet.md). La segunda opción es que [Seleccione un origen de datos compatibles](search-indexer-overview.md) para el índice de búsqueda de Azure y permitir la búsqueda de Azure extraer automáticamente los datos en el servicio de búsqueda.

Esta guía sólo cubrirá instrucciones sobre cómo usar el modelo de inserción de carga de datos (que es compatible únicamente con la [API de REST](search-import-data-rest-api.md) y [.NET SDK](search-import-data-dotnet.md)), pero aún puede obtener más información acerca del modelo de extracción a continuación.

### <a name="push-data-to-an-index"></a>Introducir datos en un índice

Este enfoque se refiere a enviar los datos a la búsqueda de Azure para ponerlo a disposición para realizar búsquedas de programación. Para las aplicaciones con los requisitos de latencia muy baja (por ejemplo, si necesita buscar operaciones para que esté sincronizado con bases de datos de inventario dinámico), el modelo de inserción es su única opción.

Puede usar la [API de REST](https://msdn.microsoft.com/library/azure/dn798930.aspx) o [.NET SDK](search-import-data-dotnet.md) para introducir datos en un índice. Actualmente no hay ninguna compatibilidad de herramientas para insertar datos a través del portal.

Este enfoque es más flexible que el modelo de extracción porque puede cargar documentos de forma individual o en lotes (hasta 1000 por lotes o 16 MB, el límite venga primero). El modelo de inserción, también puede cargar documentos en búsqueda de Azure independientemente de dónde están los datos.

### <a name="pull-data-into-an-index"></a>Extraer datos en un índice

El modelo de extracción rastrea un origen de datos compatibles y carga automáticamente los datos en el índice de búsqueda de Azure para usted. Mediante el seguimiento de cambios y eliminaciones a documentos existentes, además de reconocer documentos nuevos, indizadores quitar la necesidad de administrar los datos en el índice de forma activa.

En búsqueda de Azure, esta capacidad se implementa mediante *indizadores*, están disponibles para [el almacenamiento de blobs (vista preliminar)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [base de datos de SQL Azure y SQL Server en máquinas virtuales de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

La funcionalidad del indizador se expone en el [Portal de Azure](search-import-data-portal.md) , así como en la [API de REST](https://msdn.microsoft.com/library/azure/dn946891.aspx).
