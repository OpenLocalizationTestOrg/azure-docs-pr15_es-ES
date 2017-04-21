<properties
    pageTitle="Recopilación de datos para probar su modelo: aprendizaje recomendaciones API de máquina | Microsoft Azure"
    description="Máquina Azure recomendaciones de aprendizaje: recopilar datos para entrenar el modelo"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="luisca"/>

#  <a name="collecting-data-to-train-your-model"></a>Recopilar datos para entrenar el modelo #

La API de recomendaciones aprende de las transacciones pasadas para buscar elementos que deben ser recomendado para un usuario concreto.

Después de haber creado un modelo, debe proporcionar dos fragmento de información antes de hacer cualquier formación: un archivo de catálogo y datos de uso.

>   Si no lo ha hecho ya, le recomendamos que para completar la [Guía de inicio rápido](cognitive-services-recommendations-quick-start.md).


## <a name="catalog-data"></a>Catálogo de datos ##

### <a name="catalog-file-format"></a>Formato de archivo de catálogo ###

El archivo de catálogo contiene información acerca de los elementos que ofrece al cliente.
Los datos del catálogo deberían seguir el siguiente formato:

- Sin funciones:`<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Con las características de:`<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### <a name="sample-rows-in-a-catalog-file"></a>Filas de ejemplo en un archivo de catálogo

Sin características:

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Con las características:

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### <a name="format-details"></a>Detalles de formato

| Nombre | Obligatorio | Tipo |  Descripción |
|:---|:---|:---|:---|
| Id. de elemento |Sí | [A-z], [a-z], [0-9], [_] & #40; Subrayado & #41; [-] & #40; guión & #41;<br> La longitud máxima: 50 | Identificador único de un elemento. |
| Nombre del elemento | Sí | Cualquier carácter alfanumérico<br> La longitud máxima: 255 | Nombre del elemento. |
| Categoría de producto | Sí | Cualquier carácter alfanumérico <br> La longitud máxima: 255 | Categoría a la que pertenece (por ejemplo, cocina libretas de series...) de este elemento; puede estar vacía. |
| Descripción | No, a menos que son características presentar (pero puede estar en blanco) | Cualquier carácter alfanumérico <br> La longitud máxima: 4000 | Descripción de este elemento. |
| Lista de características | No | Cualquier carácter alfanumérico <br> La longitud máxima: 4000; Número máximo de características: 20 | Lista de valores separados por comas de nombre de la función = valor de la característica que se pueden usar para mejorar la recomendación del modelo.|

#### <a name="uploading-a-catalog-file"></a>Cargar un archivo de catálogo

Busque la [referencia de la API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) para cargar un archivo de catálogo.  

Tenga en cuenta que se debe pasar el contenido del archivo de catálogo como el cuerpo de la solicitud.

Si cargar varios archivos de catálogo al mismo modelo con varias llamadas, se insertará los nuevos elementos de catálogo. Los elementos existentes permanecerán con los valores originales. No se puede actualizar el catálogo de datos con este método.

>   Nota: El tamaño de archivo máximo es 200MB.
>   El número máximo de elementos en el catálogo compatible es 100000 elementos.


## <a name="why-add-features-to-the-catalog"></a>¿Por qué agregar características al catálogo?

El motor de recomendaciones crea un modelo de estadístico que le indica qué elementos están probable que se le ha gustado o adquiere un cliente. Cuando haya un nuevo producto que nunca ha interactúe con él no es posible crear un modelo de repeticiones compañeros únicamente. Supongamos que comenzar a ofrecer un nuevo "violín infantiles" en la tienda, ya que nunca ha vendido ese violín antes de que no indica qué otros artículos recomendar con ese violín.

Dicho esto, si sabe que el motor de información sobre esa violín (es decir, es un instrumentos musicales, es para elementos secundarios edades 7-10, no es una cara violín, etc.), a continuación, el motor puede obtener información de otros productos con características similares. Por ejemplo, ha vendido de violín en el pasado y normalmente que compran violines tendemos a comprar música clásica CD y soportes de música de la hoja.  El sistema puede buscar estas conexiones entre las características y recomendaciones basadas en las características, mientras que el nuevo violín tiene poco uso.

Características se importan como parte de los datos del catálogo y, a continuación, su rango (o la importancia de la función en el modelo) está asociada cuando se realiza una compilación JERARQUIA. Jerarquía de característica puede cambiar según el modelo de datos de uso y tipo de elementos. Pero, para uso coherentes y elementos, la clasificación debe tener solo las variaciones pequeñas. El rango de funciones es un número no negativo. El número 0 significa que la característica no se ha de rango (ocurre si se invoca esta API antes de completar la primera generación JERARQUIA). La fecha en que se atribuidos la clasificación se denomina la actualización de puntuación.


###<a name="features-are-categorical"></a>Características están Categorical

Esto significa que se deben crear características que se parezca a una categoría. Por ejemplo, precio = 9.34 no es una característica por categorías. Por otro lado, una característica como priceRange = Under5Dollars es una característica por categorías. Otro error común es usar el nombre del elemento como característica. Esto haría que el nombre de un elemento como único, de modo que pueda describir una categoría. Asegúrese de que las características representan las categorías de elementos.


###<a name="how-manywhich-features-should-i-use"></a>¿Características de cómo varios/que debo usar?


Finalmente las recomendaciones generación es compatible con la creación de un modelo de características hasta 20. Puede asignar características más de 20 a los elementos del catálogo, pero se espera hacer una clasificación de generación y seleccione solo las características cuyo alto del rango. (Una característica con un rango de 2.0 o más es una característica buena realmente usar!). 


###<a name="when-are-features-actually-used"></a>¿Cuando se utilizan realmente características?

El modelo utiliza características cuando no hay suficientes datos de transacciones para proporcionar recomendaciones en información de la transacción por sí solo. Así que características tendrá el mayor impacto en "y"elementos: artículos con menos transacciones. Si todos los elementos tienen suficiente información de la transacción que no necesite enriquecer el modelo mediante características.


###<a name="using-product-features"></a>Usar las funciones de producto

Para usar características como parte de la compilación que necesita:

1. Asegúrese de que el catálogo tiene características cuando se carga.

2. Desencadenar una compilación de clasificación. Va a hacer el análisis en la importancia y la clasificación de las características.

3. Desencadenar una generación de recomendaciones, establecer los siguientes parámetros de compilación: establece useFeaturesInModel en true, allowColdItemPlacement es true, y se debe establecer modelingFeatureList en la lista separada por comas de características que desea utilizar para mejorar el modelo. Para obtener más información, vea [parámetros de tipo de generación de recomendaciones](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0) .





## <a name="usage-data"></a>Datos de uso ##
Un archivo de uso contiene información acerca de cómo se usan los elementos o las transacciones de su empresa.

#### <a name="usage-format-details"></a>Detalles de formato de uso
Un uso archivo es un CSV (valores separados por comas) donde cada fila de un archivo de uso representa una interacción entre un usuario y un elemento. Cada fila tiene el formato siguiente:<br>
`<User Id>,<Item Id>,<Time>,[<Event>]`



| Nombre  | Obligatorio | Tipo | Descripción
|-------|------------|------|---------------
|Identificador de usuario|         Sí|[A-z], [a-z], [0-9], [_] & #40; Subrayado & #41; [-] & #40; guión & #41;<br> La longitud máxima: 255 |Identificador único de un usuario.
|Id. de elemento|Sí|[A-z], [a-z], [0-9], [& #95;] & #40; Subrayado & #41; [-] & #40; guión & #41;<br> La longitud máxima: 50|Identificador único de un elemento.
|Hora|Sí|Fecha en formato: aaaa/MM/ddTHH (por ejemplo, 2013/06/20T10:00:00)|Tiempo de datos.
|Evento|No | Uno de estos procedimientos:<br>• Haga clic en<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Compra| El tipo de transacción. |

#### <a name="sample-rows-in-a-usage-file"></a>Filas de ejemplo en un archivo de uso

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### <a name="uploading-a-usage-file"></a>Cargar un archivo en uso

Busque la [referencia de la API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) para cargar archivos de uso.
Tenga en cuenta que debe pasar el contenido del archivo de uso como el cuerpo de la llamada HTTP.

>  Nota:

>  Tamaño de archivo máximo: 200MB. Puede cargar varios archivos de uso.

>  Necesita cargar un archivo de catálogo antes de comenzar a agregar datos de uso en el modelo. Sólo los elementos del archivo de catálogo se utilizará durante la fase de aprendizaje. Se omitirán todos los demás elementos.

## <a name="how-much-data-do-you-need"></a>¿Cuántos datos necesita?

La calidad del modelo es depende en gran medida la calidad y la cantidad de los datos.
El sistema aprende cuando los usuarios comprar elementos diferentes (llamamos esta con repeticiones). Versiones de FBT, también es importante saber qué elementos se adquieren en las transacciones de la misma. 

Una buena regla general es que la mayoría de elementos en las transacciones de 20 o más, por lo que si tiene 10.000 elementos del catálogo, recomendamos que tiene al menos 20 horas ese número de transacciones o aproximadamente 200.000. Una vez más, se trata de una regla general. Debe experimentar con sus datos.

Una vez que ha creado un modelo, puede realizar una [evaluación sin conexión](cognitive-services-recommendations-buildtypes.md) para comprobar cómo es probable que realizar el modelo.
