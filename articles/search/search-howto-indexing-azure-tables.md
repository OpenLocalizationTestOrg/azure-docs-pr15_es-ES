<properties
pageTitle="Almacenamiento de tablas de Azure indización con búsqueda de Azure"
description="Obtenga información sobre cómo indizar datos almacenados en las tablas de Azure con búsqueda de Azure"
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
ms.date="08/16/2016"
ms.author="eugenesh" />

# <a name="indexing-azure-table-storage-with-azure-search"></a>Almacenamiento de tablas de Azure indización con búsqueda de Azure

Este artículo le muestra cómo usar la búsqueda de Azure para indizar los datos almacenados en el almacenamiento de tablas de Azure. El nuevo indizador de tabla de búsqueda de Azure facilita este proceso rápido y transparente. 

> [AZURE.IMPORTANT] Actualmente esta funcionalidad está en vista previa. Está disponible únicamente en la API de REST con versión **2015 02-28-vista preliminar** y en la versión 2.0 vista previa de .NET SDK. Por favor, recuerde, obtener una vista previa API están pensadas para probar y evaluación y no debe usarse en entornos de producción.

## <a name="setting-up-azure-table-indexing"></a>Configuración de indización de tablas de Azure

Para instalar y configurar un indizador de tablas de Azure, puede usar la API de REST de búsqueda de Azure para crear y administrar **orígenes de datos** e **indizadores** como se describe en [las operaciones de indizador](https://msdn.microsoft.com/library/azure/dn946891.aspx). También puede usar la [versión 2.0: vista previa](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) de .NET SDK. En el futuro, la compatibilidad con la indización de la tabla se agregará el portal de Azure.

Un origen de datos especifica los datos que indizar credenciales necesarias para tener acceso a los datos y las directivas que habilitar la búsqueda de Azure identificar eficazmente los cambios en los datos (nuevo, modificar o eliminar filas).

Un indizador lee los datos de un origen de datos y carga en un índice de búsqueda de destino.

Para configurar la indización de la tabla:

1. Crear un origen de datos
    - Establecer el `type` parámetro`azuretable`
    - Pasar de la cadena de conexión de cuenta de almacenamiento como el `credentials.connectionString` parámetro
    - Especifique el nombre de tabla con la `container.name` parámetro
    - Opcionalmente, especifique una consulta mediante el `container.query` parámetro. Siempre que sea posible, use un filtro PartitionKey para mejorar el rendimiento; cualquier otra consulta dará como resultado un recorrido de tabla completa, que puede dar lugar a un rendimiento deficiente de tablas de gran tamaño.
2. Crear un índice de búsqueda con el esquema que corresponde a las columnas de la tabla que desea indizar. 
3. Crear el indizador mediante una conexión de origen de datos al índice de búsqueda.

### <a name="create-data-source"></a>Crear origen de datos

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para obtener más información acerca de la API de origen de datos cree, vea [Crear origen de datos](search-api-indexers-2015-02-28-preview.md#create-data-source).

### <a name="create-index"></a>Crear índice 

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
        ]
    }

Para obtener más información acerca de la API de índice crear, vea [Crear un índice](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Crear indizador 

Por último, cree el indizador que hace referencia al origen de datos y el índice de destino. Por ejemplo:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Para obtener más detalles sobre la API de indizador crear, consulte [Crear indizador](search-api-indexers-2015-02-28-preview.md#create-indexer).

Eso es todo esto - indización feliz!

## <a name="dealing-with-different-field-names"></a>Tratar con nombres de campo diferente

A menudo, los nombres de campo en el índice existente será diferentes de los nombres de propiedad de la tabla. Puede usar **las asignaciones de campos** para asignar los nombres de propiedades de la tabla a los nombres de campo en el índice de búsqueda. Para obtener más información sobre las asignaciones de campos, consulte [asignaciones de campos de búsqueda de Azure indizador puente de las diferencias entre los orígenes de datos y los índices de búsqueda](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Trabajar con teclas de documento

En búsqueda de Azure, la clave de documento identifica un documento. Cada índice de búsqueda debe tener exactamente un campo de clave de tipo `Edm.String`. El campo de clave se requiere para cada documento que se agrega al índice (de hecho, es el único campo obligatorio).

Dado que filas de la tabla tienen una clave compuesta, búsqueda de Azure genera un campo síntesis denominado `Key` que es una concatenación partición clave y fila de valores de clave. Por ejemplo, si una fila del PartitionKey es `PK1` y RowKey es `RK1`, a continuación, `Key` será el valor del campo `PK1RK1`. 

> [AZURE.NOTE] La `Key` valores pueden contener caracteres no válidos en las teclas de documento, como guiones. Tratar los caracteres no válidos mediante el uso de la `base64Encode` [función de la asignación de campo](search-indexer-field-mappings.md#base64EncodeFunction). Si hace esto, recuerde utilizar base 64 de seguridad de URL codificación cuando pasando claves de documento en API llama como búsqueda.

## <a name="incremental-indexing-and-deletion-detection"></a>Detección de indexación y eliminación incremental
 
Cuando configura un indizador de tabla para que se ejecute en una programación, indexar filas solo nuevos o actualizados, según lo determinado por una fila `Timestamp` valor. No tiene que especificar una directiva de detección de cambios: indización incremental se habilita automáticamente para usted. 

Para indicar que se deben quitar determinados documentos del índice, puede usar una estrategia de eliminar suaves: en lugar de eliminar una fila, agregue una propiedad para indicar que se elimina y configurar una directiva de detección de eliminación suaves en el origen de datos. Por ejemplo, la directiva se muestra a continuación se considere la posibilidad de que se elimina una fila si tiene una propiedad `IsDeleted` con el valor `"true"`: 

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Ayúdenos a mejorar la búsqueda de Azure

Si tiene solicitudes de características o ideas para mejorar, por favor, llegar a nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).