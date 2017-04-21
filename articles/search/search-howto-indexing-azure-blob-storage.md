<properties
pageTitle="Indización de almacenamiento de blobs de Windows Azure con búsqueda de Azure"
description="Obtenga información sobre cómo indizar el almacenamiento de blobs de Windows Azure y extraer texto de documentos con búsqueda de Azure"
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
ms.date="10/16/2016"
ms.author="eugenesh" />

# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indización de documentos en el almacenamiento de blobs de Windows Azure con búsqueda de Azure

Este artículo le muestra cómo usar la búsqueda de Azure a los documentos de índice (como archivos PDF, documentos de Microsoft Office y otros formatos comunes) almacenados en el almacenamiento de blobs de Windows Azure. El nuevo indizador de blobs de Windows Azure búsqueda facilita este proceso rápido y transparente. 

> [AZURE.IMPORTANT] Actualmente esta funcionalidad está en vista previa. Está disponible únicamente en la API de REST con versión **2015 02-28-vista previa**. Por favor, recuerde, obtener una vista previa API están pensadas para probar y evaluación y no debe usarse en entornos de producción.

## <a name="supported-document-formats"></a>Formatos de documento admitidos

El indizador blob puede extraer texto de los siguientes formatos de documento:

- PDF
- Formatos de Microsoft Office: DOC/DOCX XLS/XLSX PPTX/PPT, msj (correos electrónicos de Outlook)  
- HTML
- XML
- ZIP
- EML
- Archivos de texto sin formato  
- JSON (consulte [blobs de indización JSON](search-howto-index-json-blobs.md) para obtener información detallada)
- CSV (consulte [blobs de indización CSV](search-howto-index-csv-blobs.md) para obtener información detallada)

## <a name="setting-up-blob-indexing"></a>Configuración de indización blob

Puede configurar un indizador de almacenamiento de blobs de Windows Azure usando:

- [Portal de Azure](https://ms.portal.azure.com)
- [API de REST](https://msdn.microsoft.com/library/azure/dn946891.aspx) de búsqueda de Azure
- Azure búsqueda .NET SDK [versión 2.0: vista previa](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) 

> [AZURE.NOTE] Algunas características (por ejemplo, las asignaciones de campo) no ya están disponibles en el portal y tienen que utilizar mediante programación. 

En este artículo, se configurará un indizador con la API de REST siguiendo los tres pasos: crear un origen de datos, crear un índice, configurar el indizador.

### <a name="step-1-create-a-data-source"></a>Paso 1: Crear un origen de datos

Un origen de datos especifica los datos que indizar credenciales necesarias para tener acceso a los datos y directivas para identificar eficazmente los cambios en los datos (filas nuevas, modificados o eliminados). Un origen de datos puede ser usado por varios indizadores en el mismo servicio de búsqueda.

Para la indización de blobs, el origen de datos debe tener las siguientes propiedades necesarias: 

- **nombre** es el único nombre del origen de datos dentro de su servicio de búsqueda. 

- **tipo** debe ser `azureblob`. 

- **credenciales** proporciona la cadena de conexión de cuenta de almacenamiento como el `credentials.connectionString` parámetro. Puede obtener la cadena de conexión desde el portal de Azure desplazándose hasta el módulo de almacenamiento que quiera cuenta > **configuración** > **claves** y use el valor "Cadena de conexión principal" o "Cadena de conexión secundaria". 

- **contenedor** especifica un contenedor en su cuenta de almacenamiento. De forma predeterminada, todos los blobs dentro del contenedor son recuperables. Si solo desea BLOB de índice en un directorio virtual determinado, puede especificar dicho directorio mediante el parámetro de **consulta** de opcional. 

En el ejemplo siguiente se muestra una definición de origen de datos:

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Para obtener más información acerca de la API de origen de datos cree, vea [Crear origen de datos](search-api-indexers-2015-02-28-preview.md#create-data-source).

### <a name="step-2-create-an-index"></a>Paso 2: Crear un índice 

El índice especifica los campos en un documento, atributos, y otras construcciones que la búsqueda de forma experiencia.  

Para la indización de blobs, asegúrese de que el índice tiene una búsqueda `content` campo para almacenar el blob.

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
        ]
    }

Para obtener más información acerca de la API de índice crear, vea [Crear un índice](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="step-3-create-an-indexer"></a>Paso 3: Crear un indizador 

Un indizador conecta a orígenes de datos con los índices de búsqueda de destino y proporciona información de programación que pueden automatizar la actualización de datos. Una vez creado el índice y origen de datos, es relativamente sencillo crear un indizador que hace referencia al origen de datos y un índice de destino. Por ejemplo:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Este indizador ejecutará cada dos horas (intervalo de programación está establecida en "PT2H"). Para ejecutar un indizador cada 30 minutos, establezca el intervalo de "PT30M". Intervalo compatible más corta es 5 minutos. Programación es opcional - si se omite, un indizador se ejecuta una sola vez cuando crea. Sin embargo, puede ejecutar un indizador a petición en cualquier momento.   

Para obtener más detalles sobre la API de indizador crear, consulte [Crear indizador](search-api-indexers-2015-02-28-preview.md#create-indexer).


## <a name="document-extraction-process"></a>Proceso de extracción de documento

Búsqueda de Azure índices cada documento (blob) como sigue:

- El contenido de todo el texto del documento se extrae en un campo de cadena denominado `content`. Actualmente no ofrecemos soporte técnico para extraer varios documentos desde un único blob:
    - Por ejemplo, un archivo CSV se numera como un único documento. Si necesita tratar cada línea de un CSV como un documento independiente, votar por [esta sugerencia UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/13865325-please-treat-each-line-in-a-csv-file-as-a-separate).
    - También se indiza de un documento compuesto o incrustado (como un archivo ZIP o un documento de Word incrustado que contiene los datos adjuntos de correo de Outlook) como un único documento.

- Propiedades de metadatos especificado por el usuario presentes en el blob, si la hay, se extraen literalmente. Las propiedades de metadatos también pueden usarse para controlar determinados aspectos del proceso de extracción de documento: vea [Usando metadatos personalizados a la extracción de documento de Control](#CustomMetadataControl) para obtener más detalles.

- Propiedades de metadatos de blobs estándar se extraen en los siguientes campos:

    - **metadatos\_almacenamiento\_nombre** (Edm.String) - el nombre de archivo del objeto binario. Por ejemplo, si tiene un /my-container/my-folder/subfolder/resume.pdf blobs, el valor de este campo es `resume.pdf`.

    - **metadatos\_almacenamiento\_ruta** (Edm.String) - URI completo del blob, incluida la cuenta de almacenamiento. Por ejemplo,`https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

    - **metadatos\_almacenamiento\_contenido\_tipo** (Edm.String) - tipo especificado por el código que usaste para cargar el blob de contenido. Por ejemplo, `application/octet-stream`.

    - **metadatos\_almacenamiento\_última\_modificado** (Edm.DateTimeOffset) - modificó por última vez marca de tiempo para el blob. Búsqueda de Azure utiliza esta marca de hora para identificar blobs modificados, para evitar todo reindización después de la indización inicial.

    - **metadatos\_almacenamiento\_tamaño** (Edm.Int64) - tamaño en bytes blob.

    - **metadatos\_almacenamiento\_contenido\_md5** (Edm.String) - hash MD5 del contenido blobs, si está disponible.

- Propiedades de metadatos específicos para cada formato del documento se extraen en los campos mostrados [aquí](#ContentSpecificMetadata).

No es necesario definir campos para todas las propiedades anteriores en el índice de búsqueda, simplemente capturar las propiedades que necesarias para su aplicación. 

> [AZURE.NOTE] A menudo, los nombres de campo en el índice existente será diferentes de los nombres de campo generados durante la extracción de documento. Puede usar **las asignaciones de campos** para asignar los nombres de propiedad proporcionados por la búsqueda de Azure a los nombres de campo en el índice de búsqueda. Se muestra un ejemplo de asignaciones use debajo del campo. 

## <a name="picking-the-document-key-field-and-dealing-with-different-field-names"></a>Elegir el campo de clave del documento y tratar con nombres de campo diferente

En búsqueda de Azure, la clave de documento identifica un documento. Cada índice de búsqueda debe tener exactamente un campo de clave de tipo Edm.String. El campo de clave se requiere para todos los documentos que se agrega al índice (es realmente el único campo obligatorio).  
   
Debe considerar qué campo extraído se debe asignar al campo de clave para el índice. Los candidatos son:

- **metadatos\_almacenamiento\_nombre** : puede ser conveniente candidato, pero tenga en cuenta que 1) los nombres no pueden ser únicos, como es posible que tenga BLOB con el mismo nombre en distintas carpetas y (2) el nombre puede contener caracteres no válidos en las teclas de documento, como guiones. Tratar los caracteres no válidos mediante el uso de la `base64Encode` [función de asignación de campo](search-indexer-field-mappings.md#base64EncodeFunction) - si hace esto, no olvide codificar claves del documento al pasarlos en API llamadas como la búsqueda. (Por ejemplo, en .NET puede usar el [método UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) a tal fin).

- **metadatos\_almacenamiento\_ruta** : mediante la ruta de acceso completa garantiza la exclusividad pero definitivamente contiene la ruta de acceso `/` caracteres [no válidos en una clave del documento](https://msdn.microsoft.com/library/azure/dn857353.aspx).  Como se indicó anteriormente, tiene la opción de codificación las teclas utilizando la `base64Encode` [función](search-indexer-field-mappings.md#base64EncodeFunction).

- Si ninguna de las opciones anteriores es válida, puede agregar una propiedad de metadatos personalizada para el BLOB. Sin embargo, esta opción requiere el proceso de carga de blob agregar esa propiedad de metadatos a todos los blobs. Dado que la clave es una propiedad necesaria, se producirá un error en todos los blobs que no tengan la propiedad indizar.

> [AZURE.IMPORTANT] Si no hay ninguna asignación explícita para el campo de clave en el índice, búsqueda de Azure usa automáticamente `metadata_storage_path` como clave y base 64 codifica los valores de clave (la segunda opción anterior).

En este ejemplo, vamos a seleccionar el `metadata_storage_name` como la clave del documento. Supongamos también el índice tiene un campo de clave denominado `key` y un campo `fileSize` para almacenar el tamaño del documento. Para conectar las cosas como desee, especifique las asignaciones de campos siguientes al crear o actualizar su indizador:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Para que aparezca esto juntos, le mostramos cómo puede agregar las asignaciones de campos y habilitar la codificación base 64 de claves para un indizador existente:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [AZURE.NOTE] Para obtener más información sobre las asignaciones de campos, consulte [este artículo](search-indexer-field-mappings.md).

## <a name="incremental-indexing-and-deletion-detection"></a>Detección de indexación y eliminación incremental

Cuando configura un indizador blob ejecuten en una programación, vuelve a indizar los BLOB modificados según lo determinado por el blob `LastModified` marca de tiempo.

> [AZURE.NOTE] No tiene que especificar una directiva de detección de cambios: indización incremental se habilita automáticamente para usted.

Para eliminar documentos de soporte técnico, use un enfoque "eliminar suaves". Si elimina el BLOB directamente, documentos correspondientes no se quitará el índice de búsqueda. En su lugar, use los siguientes pasos:  

1. Agregar una propiedad de metadatos personalizada para el blob para indicar a Azure búsqueda que lógicamente se ha eliminado
2. Configurar una directiva de detección de eliminación suaves en el origen de datos
3. Una vez que el indizador ha procesado el blob (como se muestra en el estado del indizador API), puede eliminar el blob física

Por ejemplo, la siguiente directiva considera un blob se eliminarán si tiene una propiedad de metadatos `IsDeleted` con el valor `true`:

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",   
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Propiedades de metadatos específicos del tipo de contenido

La siguiente tabla resume procesamiento realizado para cada formato del documento y describe las propiedades de metadatos extraídas por la búsqueda de Azure.

Formato de documento o tipo de contenido | Propiedades de metadatos específicos del tipo de contenido | Detalles de procesamiento
-------------------------------|-------------------------------------------|-------------------
HTML (`text/html`) | `metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` | Quitar el formato HTML y extraer el texto
PDF (`application/pdf`) | `metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`| Extraer texto, incluidos los documentos incrustados (excepto imágenes)
DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Extraer texto, incluidos los documentos incrustados
DOCUMENTO (aplicación/msword) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Extraer texto, incluidos los documentos incrustados
XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Extraer texto, incluidos los documentos incrustados
XLS (aplicación/vnd.ms-excel) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Extraer texto, incluidos los documentos incrustados
PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Extraer texto, incluidos los documentos incrustados
PPT (aplicación/vnd.ms-powerpoint) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Extraer texto, incluidos los documentos incrustados
Msj (aplicación/vnd.ms-outlook) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` | Extraer texto, incluidos los datos adjuntos
POSTAL (aplicación postal) | `metadata_content_type` | Extraer texto de todos los documentos en el archivo
XML (aplicación/xml) | `metadata_content_type`</br>`metadata_content_encoding`</br> | Quitar el formato XML y extraer el texto
JSON (aplicación/json) | `metadata_content_type`</br>`metadata_content_encoding` | Extraer texto<br/>Nota: Si necesita extraer varios campos de documento de un blob JSON, vea [BLOB de indización JSON](search-howto-index-json-blobs.md) para obtener más información
EML (mensaje/rfc822) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` | Extraer texto, incluidos los datos adjuntos
Texto sin formato (texto sin formato) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 

<a name="CustomMetadataControl"></a>
## <a name="using-custom-metadata-to-control-document-extraction"></a>Uso de metadatos personalizados a la extracción de documento de control

Puede agregar propiedades de metadatos a un blob controlar algunos aspectos del proceso de extracción de indización y documentos de blobs de Windows. Actualmente se admiten las siguientes propiedades:

Nombre de propiedad | Valor de propiedad | Explicación
--------------|----------------|------------
AzureSearch_Skip | "true" | Indica que el indizador blob omitir completamente el blob; se intentó realizar la extracción de metadatos ni contenido. Esto es útil cuando desea omitir determinados tipos de contenido, o cuando un determinado blob falla varias veces e interrumpe el proceso de indización.
AzureSearch_SkipContent | "true" | Indica que el indizador blob al índice de solo los metadatos y Omitir extracción de contenido del objeto binario. Esto es útil si el contenido de blob no es interesante, pero desea indizar los metadatos anexados al objeto binario.

<a name="IndexerParametersConfigurationControl"></a>
## <a name="using-indexer-parameters-to-control-document-extraction"></a>Usar parámetros de indizador para controlar la extracción de documento

Configuración del indizador varios parámetros están disponibles para controlar qué partes de un blob contenido y metadatos, y que blobs estén indizados. 

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Solo los BLOB con determinadas extensiones de archivo de índice

Puede especificar un índice solo los BLOB con las extensiones de nombre de archivo que especifique mediante la `indexedFileNameExtensions` parámetro de configuración del indizador. El valor es una cadena que contiene una lista de valores separados por comas de extensiones de archivo (con un punto inicial). Por ejemplo, para el índice solo la. PDF y. BLOB DOCX, haga lo siguiente: 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions-from-indexing"></a>Excluir BLOB con determinadas extensiones de archivo de indización

Puede excluir BLOB con extensiones de nombre de archivo específico de indización mediante la `excludedFileNameExtensions` parámetro de configuración. El valor es una cadena que contiene una lista de valores separados por comas de extensiones de archivo (con un punto inicial). Por ejemplo, al índice de todos los blobs, excepto los con el. PNG y. Extensiones JPEG, haga lo siguiente: 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Si ambos `indexedFileNameExtensions` y `excludedFileNameExtensions` parámetros están presentes, primero examina Azure búsqueda `indexedFileNameExtensions`, a continuación, en `excludedFileNameExtensions`. Esto significa que si la misma extensión de archivo se encuentra en dos listas, se excluyen de indización. 

### <a name="index-storage-metadata-only"></a>Solo los metadatos de almacenamiento de índice

Puede indizar sólo los metadatos de almacenamiento y omitir completamente el proceso de extracción de documento mediante la `indexStorageMetadataOnly` propiedad de configuración. Esto es útil cuando no es necesario el contenido del documento, ni necesita cualquiera de las propiedades de metadatos de específicos del tipo de contenido. Para ello, establezca la `indexStorageMetadataOnly` propiedad `true`: 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexStorageMetadataOnly" : true } }
    }

### <a name="index-both-storage-and-content-type-metadata-but-skip-content-extraction"></a>Índice de almacenamiento y metadatos de tipo de contenido, pero omitir la extracción de contenido

Si necesita extraer todos los metadatos pero omitir la extracción de contenido para todos los blobs, puede solicitar este comportamiento mediante la configuración del indizador, en lugar de tener que agregar `AzureSearch_SkipContent` blob de metadatos a cada una individualmente. Para ello, establezca la `skipContent` propiedad de configuración de indizador a `true`: 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "skipContent" : true } }
    }

## <a name="help-us-make-azure-search-better"></a>Ayúdenos a mejorar la búsqueda de Azure

Si tiene solicitudes de características o ideas para mejorar, por favor, llegar a nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
