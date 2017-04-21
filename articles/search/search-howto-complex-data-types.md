<properties
    pageTitle="Cómo modelar tipos de datos complejos en búsqueda de Azure | Búsqueda de Microsoft Azure"
    description="Anidar o se pueden modelar estructuras de datos jerárquicos en un índice de búsqueda de Azure con el conjunto de filas plana y tipo de datos de colecciones."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="complex data types; compound data types; aggregate data types"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="09/07/2016"
    ms.author="liamca"
/>

# <a name="how-to-model-complex-data-types-in-azure-search"></a>Cómo los tipos de datos complejos de modelo de búsqueda de Azure

Conjuntos de datos externos usados para rellenar un índice de búsqueda de Azure a veces incluyen subestructuras jerárquicas o anidadas que no dividir perfectamente en un conjunto de filas tabular. Ejemplos de estas estructuras pueden incluir varias ubicaciones y números de teléfono para un solo cliente, varios colores y tamaños para un único SKU, varios autores de un único libro y así sucesivamente. En términos de modelos, es posible que vea estas estructuras denominadas a *tipos de datos complejos*, *compuesto de tipos de datos*, *tipos de datos compuestos*o *Agregar tipos de datos*, entre otros.

Tipos de datos complejos no se admiten en búsqueda de Azure, pero una solución comprobada incluye un proceso de dos pasos de acoplar la estructura y, a continuación, usando un tipo de datos de la **colección** para reconstituir la estructura interior. Siguiendo la técnica descrita en este artículo permite que el contenido que se va a buscar, facetas, filtrar y ordenar.

## <a name="example-of-a-complex-data-structure"></a>Ejemplo de una estructura de datos complejos

Normalmente, los datos en cuestión residen como un conjunto de documentos JSON o XML o como elementos de un almacén como DocumentDB. Una estructura, el desafío proviene de tener varios elementos secundarios que deben realizar la búsqueda y filtrado.  Como punto de partida para ilustrar la solución alternativa, realice el siguiente documento JSON que muestra un conjunto de contactos como ejemplo:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Aunque los campos nombre 'id', 'name' y 'empresa' pueden asignarse fácilmente uno a uno como campos en un índice de búsqueda de Azure, el campo 'ubicaciones' contiene una matriz de ubicaciones, tener ambos un conjunto de identificadores de ubicación, así como las descripciones de ubicación. Dado que la búsqueda de Azure no tiene un tipo de datos que admita esto, necesitamos una forma distinta de este modelo de búsqueda de Azure. 

> [AZURE.NOTE] Esta técnica también se describe por Kirk Evans en una entrada de blog [Indización DocumentDB con búsqueda de Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), que muestra una técnica denominada "acoplar los datos", que tendrá un campo denominado `locationsID` y `locationsDescription` que son [colecciones](https://msdn.microsoft.com/library/azure/dn798938.aspx) (o una matriz de cadenas).   

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Parte 1: Acoplar la matriz en campos individuales

Para crear un índice de búsqueda de Azure que se adapte a este conjunto de datos, crear campos individuales para la subestructura anidada: `locationsID` y `locationsDescription` con un tipo de datos de [colecciones](https://msdn.microsoft.com/library/azure/dn798938.aspx) (o una matriz de cadenas). En estos campos que indiza los valores '1' y '2' la `locationsID` de campo para John Smith y los valores '3' y '4' en el `locationsID` campo Jen Campbell.  

Los datos dentro de la búsqueda de Azure tendría un aspecto similar a este: 

![datos de ejemplo, 2 filas](./media/search-howto-complex-data-types/sample-data.png)


## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Parte 2: Agregar un campo de la colección en la definición de índice

En el esquema de índice, las definiciones de campo pueden tener un aspecto similares a este ejemplo.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Validar comportamientos de búsqueda y, opcionalmente, extender el índice

Suponiendo que ha creado el índice y carga los datos, ahora puede probar la solución para comprobar la ejecución de la consulta de búsqueda en el conjunto de datos. Cada campo de la **colección** debe ser **que permiten búsquedas**, **pueden filtrarse** y **facetable**. Debería poder ejecutar consultas como:

* Buscar todas las personas que trabajan en la 'sede Adventureworks'.
* Obtener un recuento del número de personas que trabajan en una oficina.  
* De las personas que trabajan en una oficina, mostrar qué otras oficinas funcionan junto con un recuento de las personas en cada ubicación.  

Lugar esta técnica correspondiente separadas es cuando tenga que realizar una búsqueda que combina el id de ubicación así como la descripción de la ubicación. Por ejemplo:

* Buscar todas las personas que tengan una oficina y tiene un ID de ubicación de 4.  

Si recuperar el contenido original tenía el siguiente aspecto:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Sin embargo, ahora que hemos separado los datos en campos separados, no hay ninguna manera de saber if el inicio de Office para Jen Campbell se relaciona con `locationsID 3` o `locationsID 4`.  

Para controlar este caso, defina otro campo en el índice que combina todos los datos en una sola colección.  En nuestro ejemplo, hemos llamará este campo `locationsCombined` y se separa el contenido con un `||` aunque puede elegir cualquier separador que cree que sería un conjunto único de caracteres para el contenido. Por ejemplo: 

![datos de ejemplo, 2 filas con separador](./media/search-howto-complex-data-types/sample-data-2.png)

Utilizar esta `locationsCombined` campo, ahora nos podemos acomodar aún más consultas, como por ejemplo:

* Mostrar un recuento de las personas que trabajan en una 'oficina de inicio' con el Id de ubicación de '4'.  
* Buscar personas que trabajan en una oficina con ubicación Id '4'. 

## <a name="limitations"></a>Limitaciones

Esta técnica es útil para un número de escenarios, pero no es aplicable en cada caso.  Por ejemplo:

1. Si no tiene un conjunto de campos estático en el tipo de datos complejos y no había ningún modo para asignar los posibles tipos a un solo campo. 
2. Actualizar los objetos anidados requiere trabajo adicional para determinar exactamente lo que necesita actualizarse en el índice de búsqueda de Azure

## <a name="sample-code"></a>Código de ejemplo

Puede ver un ejemplo de cómo indizar un conjunto de datos JSON complejo en búsqueda de Azure y realizar un número de consultas sobre este conjunto de datos en este [GitHub repo](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Siguiente paso

[Voto para compatibilidad nativa para tipos de datos complejos](https://feedback.azure.com/forums/263029-azure-search) en la búsqueda de Azure para UserVoice página y proporcionar cualquier entrada adicional que le gustaría que nosotros en relación con la implementación de características. Puede ponerse en contacto con me directamente en Twitter en @liamca.


 