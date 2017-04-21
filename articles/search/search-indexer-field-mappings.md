<properties
pageTitle="Asignaciones de campos en indizadores de búsqueda de Azure"
description="Configurar las asignaciones de campos de búsqueda de Azure indizador para tener en cuenta las diferencias de los nombres de campo y las representaciones de datos"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="10/17/2016"
ms.author="eugenesh" />

# <a name="field-mappings-in-azure-search-indexers"></a>Asignaciones de campos en indizadores de búsqueda de Azure

Al utilizar indizadores de búsqueda de Azure, en ocasiones puede encontrar usted mismo en situaciones donde los datos de entrada bastante no coincide con el esquema del índice de destino. En estos casos, puede usar **las asignaciones de campos** para transformar los datos en la forma deseada. 

Algunas situaciones en las asignaciones de campos son útiles:
 
- El origen de datos tiene un campo `_id`, pero la búsqueda de Azure no permite nombres de campo, comenzando por un carácter de subrayado. Una asignación de campos le permite "Cambiar nombre" un campo. 
- Que desea rellenar varios campos en el índice con los mismos datos de origen de datos, por ejemplo, porque desea aplicar diferentes analizadores en esos campos. Asignaciones de campos le permiten "horquilla" es un campo de origen de datos.
- Necesite base 64 codificar o descodificar los datos. Asignaciones de campos admiten varias **funciones de asignación**, incluidas las funciones de base 64 codificar y descodificar.   


> [AZURE.IMPORTANT] Actualmente, la funcionalidad de las asignaciones de campo está en vista previa. Está disponible únicamente en la API de REST con versión **2015 02-28-vista previa**. Por favor, recuerde, obtener una vista previa API están pensadas para probar y evaluación y no debe usarse en entornos de producción.

## <a name="setting-up-field-mappings"></a>Configurar las asignaciones de campos

Puede agregar las asignaciones de campos al crear un nuevo indizador con la API de [Indizador crear](search-api-indexers-2015-02-28-preview.md#create-indexer) . Puede administrar las asignaciones de campo en un indizador indización con la API de [Indizador de actualización](search-api-indexers-2015-02-28-preview.md#update-indexer) . 

Una asignación de campo consta de 3 partes: 

1. A `sourceFieldName`, que representa un campo en el origen de datos. Se requiere esta propiedad. 

2. Opcional `targetFieldName`, que representa un campo en el índice de búsqueda. Si se omite, se utiliza el mismo nombre que el origen de datos. 

3. Opcional `mappingFunction`, que puede transformar los datos mediante una de las funciones predefinidas. La lista completa de funciones está [por debajo](#mappingFunctions).

Asignaciones de campos se agregan a la `fieldMappings` matriz en la definición del indizador. 

Por ejemplo, le mostramos cómo puede adaptar las diferencias en los nombres de campo: 

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

Un indizador puede tener varias asignaciones de campo. Por ejemplo, he aquí cómo se puede "bifurcar" un campo:

```JSON

"fieldMappings" : [ 
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [AZURE.NOTE] Búsqueda de Azure usa comparación entre mayúsculas y minúsculas para resolver los nombres de campo y en función de las asignaciones de campos. Esto es conveniente (no es necesario obtener el uso de mayúsculas derecha), pero significa que el índice o el origen de datos no puede tener campos que sólo se diferencian en mayúsculas y minúsculas.  

<a name="mappingFunctions"></a>
## <a name="field-mapping-functions"></a>Funciones de asignación de campo

Estas funciones se admiten actualmente: 

- [base64Encode](#base64EncodeFunction)
- [base64Decode](#base64DecodeFunction)
- [extractTokenAtPosition](#extractTokenAtPositionFunction)
- [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>
### <a name="base64encode"></a>base64Encode 

Realiza una codificación base 64 de *seguridad de la dirección URL* de la cadena de entrada. Se supone que la entrada codificada UTF-8. 

#### <a name="sample-use-case"></a>Caso de uso de ejemplo 

Caracteres de seguridad de URL sólo pueden aparecer en una clave de búsqueda de Azure documento (ya que los clientes puedan solucionar el documento con la API de búsqueda, por ejemplo). Si los datos contienen caracteres no seguros URL y que desea usar para rellenar un campo de clave en el índice de búsqueda, use esta función.   

#### <a name="example"></a>Ejemplo 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>
### <a name="base64decode"></a>base64Decode

Realiza descodificar base 64 de la cadena de entrada. La entrada se supone que la *seguridad de la dirección URL de* cadena codificado base 64. 

#### <a name="sample-use-case"></a>Caso de uso de ejemplo 

Valores de metadatos personalizados BLOB deben ser codificado en ASCII. Puede utilizar la codificación de base 64 para representar cadenas de Unicode arbitrarias de blobs de metadatos personalizados. Sin embargo, para hacer la búsqueda sentido, puede usar esta función para volver a activar los datos codificados en cadenas de "normales" al rellenar el índice de búsqueda.  

#### <a name="example"></a>Ejemplo 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>
### <a name="extracttokenatposition"></a>extractTokenAtPosition

Divide un campo de cadena utilizando el delimitador especificado y a continuación, elige el token en la posición especificada en la división resultante.

Por ejemplo, si la entrada es `Jane Doe`, el `delimiter` es `" "`(espacio) y la `position` es 0, el resultado es `Jane`; Si el `position` es 1, el resultado es `Doe`. Si la posición hace referencia a un símbolo que no existe, se devolverá un error.

#### <a name="sample-use-case"></a>Caso de uso de ejemplo 

El origen de datos contiene un `PersonName` campo y desea indizar como separado dos `FirstName` y `LastName` campos. Puede usar esta función para dividir la entrada con el carácter de espacio como delimitador.

#### <a name="parameters"></a>Parámetros

- `delimiter`: una cadena que se utilizará como separador cuando se divide la cadena de entrada.
- `position`: una posición de enteros basado en cero del token elegir después de que se divide la cadena de entrada.    

#### <a name="example"></a>Ejemplo

```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>
### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection

Las transformaciones de una cadena con formato como una matriz JSON de cadenas en una matriz de cadena que se pueden usar para rellenar un `Collection(Edm.String)` campo en el índice. 

Por ejemplo, si la cadena de entrada es `["red", "white", "blue"]`, a continuación, el campo de destino de tipo `Collection(Edm.String)` se rellena con los tres valores `red`, `white` y `blue`. Para valores de entrada que no se puede analizar como matrices de cadena JSON, se devolverá un error. 

#### <a name="sample-use-case"></a>Caso de uso de ejemplo

Base de datos SQL Azure no tiene un tipo de datos integrado que naturalmente se asigna a `Collection(Edm.String)` campos de búsqueda de Azure. Rellenar los campos de la colección de cadena, aplicar formato a los datos de origen como una matriz de cadena JSON y usar esta función. 

#### <a name="example"></a>Ejemplo 

```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## <a name="help-us-make-azure-search-better"></a>Ayúdenos a mejorar la búsqueda de Azure

Si tiene solicitudes de características o ideas para mejorar, por favor, llegar a nosotros en nuestro [sitio UserVoice](https://feedback.azure.com/forums/263029-azure-search/).