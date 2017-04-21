<properties 
    pageTitle="Cómo implementar navegación facetas en búsqueda de Azure | Microsoft Azure | categorías de navegación de búsqueda" 
    description="Agregar navegación facetas a aplicaciones que se integran con búsqueda de Azure, un servicio de búsqueda de nube hospedado en Microsoft Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

#<a name="how-to-implement-faceted-navigation-in-azure-search"></a>Cómo implementar navegación facetas en búsqueda de Azure

Navegación facetas es un mecanismo de filtrado que proporciona navegación automática directos de obtención de detalles en las aplicaciones de búsqueda. Aunque el término 'exploración facetas' deben estar familiarizado, es prácticamente una dado que ha utilizado anterior. Como se muestra en el ejemplo siguiente, navegación facetas es nada más que las categorías utilizadas para filtrar los resultados.

## <a name="what-it-looks-like"></a>¿Qué aspecto

 ![][1]
  
Aspectos pueden ayudarle a encontrar lo está buscando, mientras se asegura de que no obtenga los resultados de cero. Como desarrollador, facetas permiten exponer los criterios de búsqueda muy útiles para navegar por el cuerpo de la búsqueda. En las aplicaciones de minorista en línea, navegación facetas se genera a menudo sobre las marcas, departamentos (zapatillas para niños), tamaño, precio, popularidad y clasificaciones. 

Implementación de navegación facetas varía entre tecnologías de búsqueda y puede ser muy compleja. En búsqueda de Azure, navegación facetas se basa en el momento de la consulta, con los campos de atributos especificados anteriormente en el esquema. En las consultas de la aplicación se genera, una consulta debe enviar *parámetros de consulta de faceta* para que reciban los valores de filtro de faceta disponible para ese conjunto de resultados de documento. Recortar realmente el resultado de documento establecer, la aplicación debe aplicar una `$filter` expresión.

En términos de desarrollo de aplicaciones, escribir código que crea consultas constituye la mayor parte del trabajo. Muchos de los comportamientos de la aplicación que desee de navegación facetas proporciona el servicio, incluyendo compatibilidad integrada para configurar los intervalos y obtener recuentos de resultados de faceta. El servicio también incluye valores predeterminados razonables que ayudan a evitar estructuras difíciles de navegación. 

Este artículo contiene las secciones siguientes:

- [Cómo crearlo](#howtobuildit)
- [Crear el nivel de presentación](#presentationlayer)
- [Crear el índice](#buildindex)
- [Comprobar calidad de los datos](#checkdata)
- [Crear una consulta](#buildquery)
- [Obtener sugerencias sobre cómo controlar la navegación por facetas](#tips)
- [Navegación facetas basado en los valores de rango](#rangefacets)
- [Navegación facetas basado en GeoPoints](#geofacets)
- [Pruébelo](#tryitout)

##<a name="why-use-it"></a>¿Por qué usarla
Las aplicaciones de búsqueda más eficaces tienen varios modelos de interacción además de un cuadro de búsqueda. Navegación facetas es un punto de entrada alternativo para la búsqueda, que ofrece una alternativa cómoda a escribir expresiones de búsqueda compleja a mano.

##<a name="know-the-fundamentals"></a>Conozca los aspectos básicos

Si es nuevo en el desarrollo de búsqueda, la mejor manera de pensar en navegación facetas es que muestra las posibilidades de búsqueda automática directos. Es un tipo de experiencia de búsqueda de detalle, en función de los filtros predefinidos, utilizados para restringir rápidamente hacia abajo de resultados de búsqueda mediante y acciones. 

**Modelo de interacción**

La experiencia de búsqueda para la navegación facetas es iterativa, ¡comenzar por comprender como una secuencia de consultas que tendrá la oportunidad respuesta a acciones del usuario.

El punto de partida es una página de aplicación que proporciona navegación facetas, que normalmente se coloca en la será. Navegación facetas a menudo es una estructura de árbol con casillas de verificación para cada valor o texto interactivo. 

1.  Una consulta que se envía a la búsqueda de Azure especifica la estructura de exploración facetas a través de uno o más parámetros de consulta de faceta. Por ejemplo, puede incluir la consulta `facet=Rating`, tal vez con un `:values` o `:sort` la opción que desee para refinar la presentación.
2.  El nivel de presentación representa una página de búsqueda que proporciona navegación facetas, con los aspectos que se especifica en la solicitud.
3.  Dada una estructura de exploración facetas que incluye clasificación, el usuario hace clic en "4" para indicar que deben mostrarse sólo productos con una calificación de 4 o posterior. 
4.  En la respuesta, la aplicación envía una consulta que incluye`$filter=Rating ge 4` 
5.  El nivel de presentación actualiza la página, que muestra un conjunto de resultados reducida, que contiene los elementos que cumplan los criterios nuevos (en este caso, productos calificados 4 o posterior).

Un aspecto es un parámetro de consulta, pero no lo confunda con la entrada de la consulta. Nunca se utiliza como criterios de selección en una consulta. En su lugar, piense en faceta parámetros de consulta como entrada para la estructura de navegación que está en la respuesta. Para cada parámetro de consulta de faceta que proporciona, búsqueda de Azure evaluará cuántos documentos están en los resultados de parciales de cada valor de aspecto.

Aviso de la `$filter` en el paso 4. Se trata de un aspecto importante de navegación facetas. Aunque son independientes de la API de aspectos y filtros, deberá ambos para ofrecer la experiencia que desea. 

**Patrones de diseño**

En el código de la aplicación, la trama es usar parámetros de consulta de faceta para devolver la estructura de exploración facetas junto con los resultados de faceta, además de una expresión de $filter que controla el evento click. Piense en la `$filter` expresión como el código subyacente el recorte real de los resultados de búsqueda se devuelve a la capa de presentación. Dada una faceta de colores, haga clic en el color rojo se implementa mediante un `$filter` expresión que selecciona sólo los elementos que tienen un color de rojo. 

**Conceptos básicos de consulta de búsqueda de Azure**

En búsqueda de Azure, se especifica una solicitud a través de uno o más parámetros de consulta (vea [Documentos de búsqueda](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener una descripción de cada uno). Ninguno de los parámetros de consulta son necesario, pero debe tener al menos una orden de una consulta para ser válidos.

Precisión, que generalmente se entiende como la capacidad de filtrar las visitas relevante, se logra a través de una o ambas de estas expresiones:

- **búsqueda =**<br/>
El valor de este parámetro constituye la expresión de búsqueda. Es posible que un único fragmento de texto o una expresión de búsqueda compleja que incluya varios términos y los operadores. En el servidor, se utiliza una expresión de búsqueda para la búsqueda de texto, campos de búsqueda en el índice para que coincidan con los términos, devolver los resultados en orden de clasificación de consultas. Si establece `search` en null, consulta ejecución es sobre todo el índice (es decir, `search=*`). En este caso, otros elementos de la consulta, como un `$filter` o puntuación perfil, serán los principales factores que afectan a los documentos que se devuelven `($filter`) y en qué orden (`scoringProfile` o `$orderb`y).

- **$filter =**<br/>
Un filtro es un mecanismo eficaz para limitar el tamaño de los resultados de búsqueda en función de los valores de los atributos de documento específico. A `$filter` se evalúa en primer lugar, seguido de lógica faceting que genera los valores disponibles y recuentos correspondientes para cada valor

Expresiones de búsqueda compleja disminuirá el rendimiento de la consulta. Cuando sea posible, utilice expresiones de filtro bien construida para aumentar la precisión y mejorar el rendimiento de la consulta.

Para comprender mejor cómo un filtro agrega más precisión, comparar una expresión de búsqueda compleja que incluye una expresión de filtro:

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Aunque ambas consultas son válidos, el segundo es superior si está buscando no moteles con estacionamiento de Seattle. La primera consulta se basa en esas palabras específicas no mencionados en los campos de cadena como nombre, descripción y cualquier otro campo que contiene los datos que permiten búsquedos o mencionados. La segunda consulta busca coincidencias precisas en datos estructurados y es probable que sea mucho más precisos.

En aplicaciones que incluyan navegación facetas, desea asegurarse de que se acompaña de cada acción del usuario a través de una estructura de exploración facetas de restricción de resultados de búsqueda, que se logra mediante una expresión de filtro.

<a name="howtobuildit"></a>
##<a name="how-to-build-it"></a>Cómo crearlo

Navegación por facetas en búsqueda de Azure se implementa en el código de aplicación que se basa la solicitud, pero se basa en elementos predefinidos en el esquema.

Predefinidas en la búsqueda de índice es la `Facetable [true|false]` índice conjunto de atributos, en los campos seleccionados para habilitar o deshabilitar su uso en una estructura de exploración facetas. Sin `"Facetable" = true`, no se puede utilizar un campo en la navegación de faceta.

En el momento de la consulta, el código de aplicación crea una solicitud que incluye `facet=[string]`, un parámetro de solicitud que proporciona el campo a faceta por. Una consulta puede tener varios aspectos, como por ejemplo `&facet=color&facet=category&facet=rating`, cada uno de ellos separados por un carácter de y comercial (&).

Código de la aplicación debe crear también una `$filter` expresión para controlar los eventos de hacer clic en navegación facetas. A `$filter` reduce los resultados de búsqueda, utilizando el valor de aspecto como criterios de filtro.

Búsqueda de Azure devuelve los resultados de búsqueda, por los términos especificados por el usuario, junto con las actualizaciones de la estructura de exploración facetas. En búsqueda de Azure, navegación facetas es una construcción de un solo nivel, con valores de facetas y recuentos de cuántos resultados se encuentran para cada uno.

El nivel de presentación en el código proporciona la experiencia del usuario. Deben enumerar las partes constituyentes de la navegación facetas, como la etiqueta, valores, casillas de verificación y el recuento. La API de REST de búsqueda de Azure es independiente de la plataforma, así que use cualquier idioma y plataforma que desee. Lo importante es incluir elementos de interfaz de usuario que admiten la actualización incremental, con el estado de interfaz de usuario actualizados cada faceta adicional está activada. 

En las siguientes secciones, deberá realizar más detalles sobre cómo crear cada elemento, comenzando por la capa de presentación.

<a name="presentationlayer"></a>
##<a name="build-the-presentation-layer"></a>Crear el nivel de presentación

Trabajo de la capa de presentación puede ayudarle a descubrir requisitos que podrían perdidos en caso contrario y comprenden las características que son fundamentales para la experiencia de búsqueda.

En términos de navegación facetas, su página web o una aplicación muestra la estructura de exploración facetas, detecta entradas del usuario en la página e inserta los elementos modificados. 

Aplicaciones web, AJAX se usa generalmente en el nivel de presentación, ya que permite actualizar cambios incrementales. También puede usar ASP.NET MVC o cualquier otra plataforma de visualización que puede conectarse a un servicio de búsqueda de Azure a través de HTTP. La aplicación de ejemplo que se hace referencia en este artículo: **AdventureWorks catálogo** : pasa a ser una aplicación de ASP.NET MVC.

El ejemplo siguiente se toman del archivo **index.cshtml** de la aplicación de ejemplo, crea una estructura HTML dinámica para mostrar facetas navegación en la página de resultados de búsqueda. En el ejemplo, navegación facetas integrado en la página de resultados de búsqueda y aparece después de que el usuario ha enviado un término de búsqueda.

Observe que cada aspecto tiene una etiqueta (colores, categorías, precios), un enlace a un campo facetas (color, categoryName, listPrice) y un `.count` parámetro, que se utiliza para devolver el número de elementos encontrados para ese resultado faceta.

  ![][2]
 

> [AZURE.TIP] Al diseñar la página de resultados de búsqueda, no olvide agregar un mecanismo para borrar facetas. Si utiliza casillas de verificación, los usuarios pueden fácilmente intuit cómo borrar los filtros. Otros diseños, es posible que tenga un modelo de ruta de navegación u otro enfoque creative. Por ejemplo, en la aplicación de ejemplo AdventureWorks catálogo, puede hacer clic en el título, el catálogo de AdventureWorks, para restablecer la página de búsqueda.

<a name="buildindex"></a>
##<a name="build-the-index"></a>Crear el índice

Faceting está habilitado en una base de campo: campo en el índice, a través de este atributo de índice: `"Facetable": true`.  
Todos los tipos de campo que se puede utilizar en navegación facetas son `Facetable` de forma predeterminada. Incluyen esos tipos de campo `Edm.String`, `Edm.DateTimeOffset`y todos los tipos de campo numérico (básicamente, todos los tipos de campo son facetable excepto `Edm.GeographyPoint`, que no pueden usarse en navegación facetas). 

Cuando crea un índice, mejores prácticas para la navegación facetas es explícitamente desactivar faceting para los campos que nunca se deben utilizar como un aspecto.  En particular, deben establecer los campos de cadena para valores de singleton, como un identificador o nombre del producto, `"Facetable": false` para impedir su uso accidental (e ineficaz) en un panel de navegación facetas.

A continuación se muestra el esquema de la aplicación de ejemplo AdventureWorks catálogo (recortada de algunos atributos para reducir el tamaño total):

 ![][3]
 
Tenga en cuenta que `Facetable` está desactivada para los campos de cadena que no deben usarse como facetas, como un identificador o nombre. Activar faceting desactivar donde no es necesario ayuda a mantener reducido el tamaño del índice y generalmente mejora el rendimiento.

> [AZURE.TIP] Como práctica recomendada, incluya el conjunto completo de atributos de índice para cada campo. Aunque `Facetable` está activado de forma predeterminada para casi todos los campos, establecer deliberadamente cada atributo le ayudará a pensar a través de las consecuencias de cada decisión de esquema. 

<a name="checkdata"></a>
##<a name="check-for-data-quality"></a>Comprobar calidad de los datos 

Al desarrollar cualquier aplicación centrados en datos, preparar los datos suele ser uno de los elementos mayores de la tarea. Aplicaciones de búsqueda no son una excepción. La calidad de los datos tiene una relación directa en si la estructura de exploración facetas materializa tal como se espera, así como su eficacia para ayudarle a crear filtros que reducen el resultado establecido.

En búsqueda de Azure, el cuerpo de la búsqueda está formado de documentos que rellenar un índice. Documentos proporcionarán los valores que se usan para calcular facetas. Si desea faceta marca o precio, cada documento debe contener valores para *BrandName* y *ProductPrice* que son válidos, coherentes y productivo como una opción de filtro.

A continuación se muestran algunos avisos que debe anular para:

- Para cada campo que desee faceta por, pregúntese si contiene valores que son adecuados como filtros de búsqueda automática directos. Los valores deben ser breve, descriptivo y suficientemente distintiva para ofrecer una opción clara entre las opciones de competencia.
- Errores ortográficos o valores coincidentes casi. Si faceta en Color y valores de campo Incluir naranja y Ornage (un error ortográfico), un aspecto que se basa en el campo de Color obtendría ambas.
- Texto mixto también puede causar el caos en navegación facetas con naranja y naranja que aparecen como dos valores diferentes. 
- Versiones de una única columna y plurales del mismo valor pueden producir un aspecto diferente para cada uno.

Como puede imaginar, diligencia preparar los datos es un aspecto esencial de navegación facetas eficaces.

<a name="buildquery"></a>
##<a name="build-the-query"></a>Crear una consulta

El código que escriba para crear consultas debe especificar todas las partes de una consulta válida, incluyendo expresiones de búsqueda, facetas, filtros, puntuación perfiles: nada usado para formular una solicitud. En esta sección, veremos donde aspectos que se adapte a una consulta y cómo se usan filtros con facetas para ofrecer un conjunto de resultados reducida.

Un ejemplo con frecuencia es un buen lugar para empezar. El ejemplo siguiente se toman del archivo **CatalogSearch.cs** , crea una solicitud que crea la navegación de faceta basada en Color, categoría y precio. 

Observe que facetas integrales en esta aplicación de ejemplo. La experiencia de búsqueda en catálogo de AdventureWorks está diseñada alrededor de filtros y navegación facetas. Esto es evidente en un lugar destacado de facetas navegación de la página. La aplicación de ejemplo incluye los parámetros URI de facetas (color, categoría, precios) como propiedades en el método de búsqueda (tal como se construyen en la aplicación de ejemplo).

  ![][4]
 
Un parámetro de consulta de faceta se establece en un campo y según el tipo de datos, puede tener más parámetros por lista delimitada por comas que incluya `count:<integer>`, `sort:<>`, `intervals:<integer>`, y `values:<list>`. Una lista de valores es compatible con datos numéricos al configurar intervalos. Ver [Documentos de búsqueda (API de búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener detalles de uso.

Junto con los aspectos, la solicitud formulada la aplicación también debe crear filtros para restringir el conjunto de documentos de candidatos basada en una selección de valor de faceta. Para un almacén de bicicleta, navegación facetas proporciona pistas a preguntas como "¿qué colores, fabricantes y tipos de bicicletas están disponibles", filtrando respuestas preguntas como "qué bicicletas exactas aparecen en rojos, bicicletas de montaña, en este precio rango".

Cuando un usuario hace clic en "Rojo" para indicar que se deben mostrar solo los productos de color rojo, la siguiente consulta la aplicación envía incluiría `$filter=Color eq ‘Red’`.

## <a name="best-practices-for-faceted-navigation"></a>Procedimientos recomendados para la navegación facetas

En la lista siguiente se resume algunas prácticas recomendadas.

- **Precisión**<br/>
Usar filtros. Si confía en expresiones de búsqueda solo sola, fruto podrían provocar un documento que se devolverá el valor de aspecto precisas que no tiene en cualquiera de sus campos. 

- **Campos de destino**<br/>
En facetas desglosar, normalmente desea incluir solo los documentos que tienen el valor de aspecto de un campo específico (facetas), no en cualquier lugar a través de todos los campos de búsqueda. Agregar un filtro refuerza el campo de destino dirigiendo el servicio de búsqueda en el campo facetas para un valor coincidente.

- **Eficiencia de índice**<br/>
Si su aplicación utiliza navegación facetas exclusivamente (es decir, sin cuadro de búsqueda), puede marcar el campo como `searchable=false`, `facetable=true` para generar un índice más compacto. Además, la indización sólo se produce en los valores de faceta completo, con sin salto de word o la indización de los componentes de un valor de varias palabra.

- **Rendimiento**<br/>
Filtros de restringir el conjunto de documentos de candidatos para la búsqueda y les excluirán de la clasificación. Si tiene un amplio conjunto de documentos, utilizando un ejercicio de faceta muy selectivo hacia abajo a menudo le proporcionará significativamente mejor rendimiento.


<a name="tips"></a> 
##<a name="tips-on-how-to-control-faceted-navigation"></a>Obtener sugerencias sobre cómo controlar la navegación por facetas

A continuación, encontrará una hoja de sugerencia con información sobre problemas específicos.

**Agregar etiquetas de cada campo de navegación de faceta**

Las etiquetas se definen normalmente en el código HTML o formulario (**index.cshtml** en la aplicación de ejemplo). No hay ninguna API en Azure buscar etiquetas de navegación de faceta o cualquier otro tipo de metadatos.

**Definir qué campos se pueden utilizar como faceta**

Recuerde que el esquema del índice determina qué campos están disponibles para usar como un aspecto. Suponiendo que un campo es facetable, la consulta especifica qué campos faceta por. El campo por el que se encuentra faceting proporciona los valores que aparecen debajo de la etiqueta. 

Se recuperan los valores que aparecen en cada etiqueta del índice. Por ejemplo, si el campo de faceta es *Color*, los valores disponibles para filtrar adicionales serán los valores de ese campo (rojo, negro y así sucesivamente).

Para numéricos y fecha y hora solo los valores, puede establecer explícitamente valores en el campo de faceta (por ejemplo, `facet=Rating,values:1|2|3|4|5`). Se permite una lista de valores de esos tipos de campo simplificar la separación de resultados de faceta en intervalos contiguos (es decir, ambos intervalos según valores numéricos o períodos de tiempo). 

**Recortar los resultados de faceta**

Resultados de faceta son documentos que se encuentra en los resultados de búsqueda que coincidan con un término de faceta. En el ejemplo siguiente, en los resultados de búsqueda para la *informática en nube*, 254 elementos también tienen *especificación interno* como un tipo de contenido. Elementos no están necesariamente mutuamente. Si un elemento cumple los criterios de ambos filtros, se cuenta en cada uno de ellos. Esto es posible cuando faceting en `Collection(Edm.String)` campos, que a menudo se usan para implementar el etiquetado de documento.

        Search term: "cloud computing"
        Content type
           Internal specification (254)
           Video (10) 

En general, si encuentra que los resultados de faceta continuamente son demasiado grandes, se recomienda agregue más filtros, como se explica en las secciones anteriores, para dar a los usuarios de la aplicación más opciones para limitar la búsqueda.

**Limitar los elementos de la navegación de faceta**

Para cada campo facetas en el árbol de navegación, hay un límite predeterminado de 10 valores. Este valor predeterminado tenga sentido para estructuras de navegación porque mantiene la lista de valores a un tamaño manejable. Para reemplazar el valor predeterminado, asigne un valor para contar.

- `&facet=city,count:5`Especifica que solo las primeras 5 ciudades que se encuentra en la parte superior resultados de la clasificación se devuelven como un resultado de faceta. Dado un término de búsqueda de "aeropuerto" y 32 coincidencias, si la consulta especifica `&facet=city,count:5`, se incluyen las cinco ciudades únicas con el máximo partido a los documentos en los resultados de búsqueda en los resultados de faceta.

Observe la distinción entre los resultados de aspecto y los resultados de búsqueda. Resultados de búsqueda son todos los documentos que coinciden con la consulta. Resultados de faceta son las coincidencias de cada valor de aspecto. En el ejemplo, los resultados de búsqueda incluye los nombres de ciudad que no están en la lista de clasificación de faceta (5 en nuestro ejemplo). Resultados que se filtran a través de la navegación facetas que se hacen visible al usuario cuando borra facetas, o elige otros aspectos además de ciudad. 

> [AZURE.NOTE] Va a hablar sobre `count` cuando hay más de un tipo puede ser confuso. En la tabla siguiente se ofrece un breve resumen de cómo se utiliza el término en API de búsqueda de Azure, el código de ejemplo y la documentación. 

- `@colorFacet.count`<br/>
En el código de presentación, debería ver un parámetro de recuento en el aspecto, que se usa para mostrar el número de resultados de faceta. En los resultados de faceta contar indica el número de documentos que coincidan con en el término de faceta o un rango.

- `&facet=City,count:12`<br/>
En una consulta de faceta, puede establecer recuento en un valor.  El valor predeterminado es 10, pero puede configurarlo superior o inferior. Establecer `count:12` obtiene coincide con la parte superior de 12 en los resultados de faceta por número de documento.

- "`@odata.count`"<br/>
En la respuesta de la consulta, este valor indica el número de elementos coincidentes en los resultados de búsqueda. En promedio, es mayor que la suma de todos los resultados de faceta combinada, debido a la presencia de los elementos que coincidan con el término de búsqueda, pero no tengan faceta valor coincidencias.


**Niveles de navegación facetas** 

Como se indicó, no hay ninguna compatibilidad directa para anidar facetas en una jerarquía. En el cuadro navegación facetas solo es compatible con un nivel de filtros. Sin embargo, existen alternativas. Puede codificar una estructura jerárquica de faceta en un `Collection(Edm.String)` con una entrada, seleccione por jerarquía. Esta solución está fuera del ámbito de este artículo, pero puede leer acerca de las colecciones de [OData, por ejemplo](http://msdn.microsoft.com/library/ff478141.aspx). 

**Validar campos**

Si genera la lista de facetas dinámicamente en función de la entrada del usuario no es de confianza, debe validar que los nombres de los campos facetas son válidos, o los nombres de escape al generar direcciones URL mediante `Uri.EscapeDataString()` en .NET o el equivalente en la plataforma de elección.

**Cuenta en los resultados de faceta**

Al agregar un filtro a una consulta facetas, desea conservar la instrucción de faceta (por ejemplo, `facet=Rating&$filter=Rating ge 4`). Técnicamente, faceta = clasificación no es necesario, pero mantenerlo devuelve el recuento de los valores de faceta para la clasificación de 4 o superior. Por ejemplo, si un usuario hace clic en "4" y la consulta incluye un filtro para mayor o igual a "4", se devuelven recuentos para cada clasificación de 4 y hacia arriba.  

**Implicaciones sharding en recuentos de faceta**

En determinadas circunstancias, es posible que los recuentos de faceta no coinciden con los conjuntos de resultados (consulte [navegación facetas en Azure búsqueda (post foro)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Recuentos de faceta pueden ser incorrectos debido a la arquitectura de sharding. Cada índice de búsqueda tiene varias shards y cada uno de los informes de los aspectos top N por número de documento, que se combina en un único resultado. Si algunos shards tienen una gran cantidad de coincidencia de valores, mientras que otros usuarios tienen menor, es posible que algunos valores de faceta faltan o en cuentan en los resultados.

Aunque este comportamiento podría cambiar en cualquier momento, si se produce este comportamiento hoy, puede trabajar a su alrededor infle el recuento de forma artificial:<number> a un gran número de exigir informes completa de cada fragmentar. Si el valor de count: es mayor o igual que el número de valores únicos en el campo, se garantiza que los resultados. Sin embargo, cuando recuentos de documento están muy altos, que hay una reducción del rendimiento, así que usa esta opción con cuidado.

<a name="rangefacets"></a>
##<a name="facet-navigation-based-on-a-range-values"></a>Navegación de faceta basado en los valores de rango

Faceting sobre rangos es un requisito de la aplicación de búsqueda comunes. Se admiten rangos de datos numéricos y valores de fecha y hora. Puede obtener más información acerca de cada enfoque en [Buscar documentos (API de búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Búsqueda de Azure simplifica construcción de rango proporcionando dos métodos para calcular un rango. Para ambos métodos, búsqueda de Azure crea los rangos adecuados dado las entradas que ha proporcionado. Por ejemplo, si se especifican los valores de rango de 10 | 20 | 30, creará automáticamente rangos de 0 -10, 10 a 20, 20-30. La aplicación de ejemplo quita los intervalos que están vacíos. 

**Método 1: Usar el parámetro de intervalo**<br/>
Para establecer facetas precio en incrementos de 10 $, se especifica:`&facet=price,interval:10`


**Método 2: Utilizar una lista de valores**<br/>
Para datos numéricos, puede usar una lista de valores.  Tenga en cuenta el intervalo de faceta para listPrice, representado como sigue:

  ![][5]

El rango especificado en el archivo **CatalogSearch.cs** con una lista de valores:

    facet=listPrice,values:10|25|100|500|1000|2500

Cada rango integrado con 0 como punto de partida, un valor de la lista como un extremo y después se recorta del rango anterior para crear intervalos específicos. Búsqueda de Azure hará como parte de la navegación facetas. No tiene que escribir código para estructurar cada intervalo.

### <a name="build-a-filter-for-facet-ranges"></a>Crear un filtro para rangos de faceta ###

Para filtrar los documentos basados en un rango seleccionado por el usuario, puede usar el `"ge"` y `"lt"` operadores en una expresión de dos partes que define los extremos del intervalo de filtro. Por ejemplo, si el usuario elige el rango 10-25, el filtro sería `$filter=listPrice ge 10 and listPrice lt 25`.

En la aplicación de ejemplo, la expresión de filtro utiliza parámetros **priceFrom** y **priceTo** para establecer los extremos. El método de **Crear filtro** de **CatalogSearch.cs** contiene la expresión de filtro que proporciona los documentos dentro del rango.

  ![][6]

<a name="geofacets"></a> 
##<a name="filtered-navigation-based-on-geopoints"></a>Navegación filtrada basada en GeoPoints

Es común para ver los filtros que le ayudan a elegir una tienda, restaurante o destino en función de su proximidad a su ubicación actual. Aunque este tipo de filtro puede ser similar a navegación facetas, es en realidad simplemente un filtro. Aquí se mencione para aquellos que buscan específicamente consejos de implementación para ese problema de diseño en concreto.

Hay dos funciones geoespaciales en búsqueda de Azure, **geo.distance** y **geo.intersects**.

- La función **geo.distance** devuelve la distancia en kilómetros entre dos puntos, uno un campo y uno es una constante pasados como parte del filtro. 

- La función **geo.intersects** devuelve true si un punto determinado se encuentra dentro de un polígono determinado, donde el punto es un campo y polígono se especifica como una lista de constante de coordenadas pasada como parte del filtro.

Puede encontrar ejemplos de filtro en la [sintaxis de la expresión de OData (búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798921.aspx).

<a name="tryitout"></a>
##<a name="try-it-out"></a>Pruébelo

Azure búsqueda Adventure Works demostración en Codeplex contiene los ejemplos que se hace referencia en este artículo. Al trabajar con los resultados de búsqueda, vea la dirección URL para los cambios de construcción de la consulta. Esta aplicación pasa anexar facetas al URI al seleccionar cada una de ellas.

1.  Configurar la aplicación de ejemplo para usar la dirección URL del servicio y la clave de la api. 

    Observe el esquema que se define en el archivo Program.cs del proyecto CatalogIndexer. Especifica los campos facetable de color, listPrice, tamaño, grosor, categoryName y modelName.  Navegación facetas implementados actualmente solo algunos de estos (color, listPrice, categoryName).

3.  Ejecute la aplicación. 

    En primer lugar, aparece el cuadro de búsqueda. Puede haga clic en el botón de búsqueda para obtener todos los resultados o escriba un término de búsqueda.

    ![][7]
 
4.  Escriba un término de búsqueda, como bicicleta y haga clic en **Buscar**. La consulta se ejecuta rápidamente.
 
    También se devuelve una estructura de exploración facetas con los resultados de búsqueda.  En la dirección URL, facetas de colores, categorías y los precios son nulos. En la página de resultados de búsqueda, la estructura de exploración facetas incluye recuentos para cada resultado de faceta.

     ![][8]
 
5.  Haga clic en un color, la categoría y el intervalo de precios. Aspectos son nulos en una búsqueda inicial, pero que ocupan en valores, se eliminan los resultados de búsqueda de elementos que ya no coinciden con. Observe que el URI toma los cambios a la consulta.

    ![][9]
 
6.  Para borrar la consulta facetas para que puede probar los comportamientos de consulta diferente, haga clic en **Catálogo de AdventureWorks** en la parte superior de la página.

    ![][10]
 
<a name="nextstep"></a>
##<a name="next-step"></a>Siguiente paso

Para probar sus conocimientos, puede agregar un campo de faceta *modelName*. El índice ya está configurado para esta faceta, por lo que se requiere ningún cambio en el índice. Pero, debe modificar el código HTML para incluir una nueva faceta para modelos y agregue el campo faceta al constructor consulta.

Para más información sobre principios de diseño para la navegación facetas, le recomendamos los siguientes vínculos:

- [Diseño de la búsqueda facetas](http://www.uie.com/articles/faceted_search/)
- [Patrones de diseño: Navegación facetas](http://alistapart.com/article/design-patterns-faceted-navigation)

También puede ver el [Análisis detallado de búsqueda de Azure](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410). En 45:25, hay una demostración sobre cómo implementar facetas.

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 