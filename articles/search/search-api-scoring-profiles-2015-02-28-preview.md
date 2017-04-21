<properties
    pageTitle="Puntuación perfiles (Azure REST API versión 2015-02-28-vista previa de búsqueda) | Microsoft Azure | Vista previa de la búsqueda de Azure API"
    description="Búsqueda de Azure es un servicio de búsqueda de nube hospedado que admite la optimización de resultados clasificados basados en perfiles de puntuación definidas por el usuario."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.author="heidist"
    ms.date="08/29/2016" />

# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>Perfiles de puntuación (búsqueda Azure REST API versión 2015 02-28-vista previa)

> [AZURE.NOTE] En este artículo se describe los perfiles puntuación en la [Vista previa de 2015-02-28](search-api-2015-02-28-preview.md). Actualmente no hay ninguna diferencia entre la `2015-02-28` versión documentado en [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) y la `2015-02-28-Preview` versión se describe aquí, pero le ofrecemos este documento de todos modos para proporcionar cobertura del documento a través de la API completa.

## <a name="overview"></a>Información general

Puntuación hace referencia a los cálculos de un resultado de búsqueda para cada elemento devuelto en los resultados de búsqueda. El resultado es un indicador de importancia de un elemento en el contexto de la operación de búsqueda actual. Cuanto mayor sea el resultado, más relevantes el elemento. En resultados de búsqueda, los elementos son jerarquía ordenada de alto a bajo, en función de la puntuación de búsqueda calculada para cada elemento.

Búsqueda de Azure utiliza puntuación para calcular un resultado inicial de forma predeterminada, pero puede personalizar el cálculo mediante un perfil de puntuación. Perfiles de puntuación a mayor control sobre la clasificación de elementos en los resultados de búsqueda. Por ejemplo, desea aumentar elementos en función de su potencial de ingresos, promover elementos más recientes o tal vez mejore elementos que se han de inventario demasiado larga.

Un perfil de puntuación es parte de la definición de índice compuesta de campos, funciones y parámetros.

Para darle una idea de qué aspecto tiene un perfil de puntuación, en el ejemplo siguiente se muestra un perfil simple denominado 'geo'. Este aumenta elementos que tengan el término de búsqueda en el `hotelName` campo. También usa el `distance` función que dé prioridad a los elementos que están dentro de diez kilómetros de la ubicación actual. Si alguien busca el término 'DCI' y 'DCI' pasa a ser parte del nombre del hotel, documentos que incluyan hoteles con 'DCI' aparecerán superiores en los resultados de búsqueda.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

Para usar este perfil puntuación, la consulta está diseñada para especificar el perfil en la cadena de consulta. En la siguiente consulta, observe el parámetro de consulta, `scoringProfile=geo` en la solicitud.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

Esta consulta busca el término 'DCI' y pasa la ubicación actual. Tenga en cuenta que esta consulta incluye otros parámetros, como por ejemplo `scoringParameter`. Parámetros de consulta se describen en [Buscar documentos (API de búsqueda de Azure)](search-api-2015-02-28-preview.md#SearchDocs).

Haga clic en el [ejemplo](#example) para revisar un ejemplo más detallado de un perfil de puntuación.

## <a name="what-is-default-scoring"></a>¿Qué es la puntuación predeterminado

Puntuación calcula un resultado de búsqueda para cada elemento de un conjunto de resultados ordenados JERARQUIA. Cada elemento de un conjunto de resultados de búsqueda se les asigna una calificación de búsqueda y luego la clasificación mayor a menor. Elementos con las puntuaciones mayores se devuelven a la aplicación. De forma predeterminada, se devuelven los 50 superior, pero puede usar el `$top` parámetro para devolver un número mayor o menor de elementos (hasta 1000 en una sola respuesta).

De forma predeterminada, un resultado de búsqueda se calcula en función de las propiedades estadísticas de los datos y la consulta. Búsqueda de Azure busca documentos que incluyen los términos de búsqueda en la cadena de consulta (según algunos o todos los `searchMode`), favorece los documentos que contienen muchas instancias del término de búsqueda. El resultado de búsqueda aumenta aún mayor si el término es poco comunes en el cuerpo de datos, pero común dentro del documento. Base para este enfoque informático relevancia se conoce como TF IDF o (término documento inverso frecuencia frecuencia).

Suponiendo que no hay ninguna ordenación personalizada, resultados son, a continuación, la clasificación por el resultado de búsqueda antes de que se devuelven a la aplicación de llamada. Si `$top` no se especifica, 50 elementos con la búsqueda más alta se devuelve puntuación.

Pueden repetir los valores de resultado de búsqueda a lo largo de un conjunto de resultados. Por ejemplo, es posible que tenga 10 elementos con una calificación de 1,2 20 elementos con una calificación de 1,0 y 20 elementos con una calificación de 0,5. Cuando varios accesos tienen la misma calificación de búsqueda, el orden de los mismos elementos puntuados no está definido y no es estable. Ejecutar la consulta de nuevo y puede ver elementos posición MAYÚS. Dados los dos elementos con una calificación idénticos, no hay ninguna garantía cuál aparece en primer lugar.

## <a name="when-to-use-custom-scoring"></a>Cuándo usar puntuación personalizado

Debe crear uno o varios perfiles puntuación al clasificar el comportamiento predeterminado no vaya ahora suficiente en sus objetivos empresariales de reunión. Por ejemplo, puede decidir que relevancia de la búsqueda debe favorecer elementos recién agregados. Del mismo modo, es posible que tenga un campo que contiene el margen de beneficio o algún otro campo que indica el potencial de ingresos. Aumentar visitas que aportan beneficios a su empresa puede ser un factor importante en la decisión de utilizar perfiles de puntuación.

También se implementa el orden de relevancia a través de perfiles de puntuación. Considere la posibilidad de páginas de resultados de búsqueda que ha usado en el pasado que permiten ordenar por precio, fecha, clasificación o relevancia. En búsqueda de Azure, puntuación perfiles conducir la opción 'relevancia'. Se basa en los objetivos empresariales y el tipo de la experiencia de búsqueda que desea ofrecer controla la definición de relevancia.

<a name="example"></a>
## <a name="example"></a>Ejemplo

Como se indicó, puntuación personalizada se implementa a través de puntuación perfiles definidos en un esquema de índice.

Este ejemplo muestra el esquema de un índice con dos perfiles de puntuación (`boostGenre`, `newAndHighlyRated`). Cualquier consulta en este índice que incluye cualquier perfil como un parámetro de consulta el perfil para el conjunto de resultados de la puntuación.

[Pruebe este ejemplo](search-get-started-scoring-profiles.md).

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>Flujo de trabajo

Para implementar el comportamiento de puntuación personalizado, agregue un perfil de puntuación en el esquema que define el índice. Puede tener varios perfiles de puntuación dentro de un índice, pero solo puede especificar un perfil en el momento en cualquier consulta dada.

Empezar con la [plantilla](#bkmk_template) que se proporciona en este tema.

Proporcione un nombre. Perfiles de puntuación son opcionales, pero si agrega uno, se requiere el nombre. Asegúrese de seguir las convenciones de nomenclatura para los campos (empieza con una letra, evita palabras reservadas y caracteres especiales). Para obtener más información, consulte [Las reglas de nombres](http://msdn.microsoft.com/library/azure/dn857353.aspx) .

El cuerpo del perfil de puntuación se construye desde ponderados campos y funciones.

### <a name="weights"></a>Pesos ###

La `weights` propiedad de perfil de puntuación especifica pares de valor de nombre que asignan una ponderación relativa a un campo. En el [ejemplo](#example), los campos albumTitle, género y artistName son aumentada 1,5, 5 y 2, respectivamente. ¿Por qué género aumenta mucho mayor que los demás? Si se realiza la búsqueda sobre los datos que es algo homogéneos (como ocurre con 'género' en el `musicstoreindex`), es posible que tenga una mayor variación en los pesos relativos. Por ejemplo, en la `musicstoreindex`, 'roca' aparece como ambos un género y en las descripciones de género Formula idénticos. Si desea género a superar la descripción de género, el campo género tendrá un peso relativo mucho mayor.

### <a name="functions"></a>Funciones ###

Funciones se utilizan cuando los cálculos adicionales son necesarios para contextos concretos. Tipos de función válido son `freshness`, `magnitude`, `distance` y `tag`. Cada función tiene parámetros que son únicas.

  - `freshness`se debe usar cuando desea aumentar es cómo nuevo o anterior de un elemento. Esta función sólo puede utilizarse con campos de fecha y hora (`Edm.DataTimeOffset`). Nota la `boostingDuration` atributo sólo se utiliza con la función de actualización.
  - `magnitude`se debe utilizar cuando desea aumentar en función de cómo la alta o baja es de un valor numérico. Escenarios que llamar para esta función incluyen incremento de margen de beneficio, precio máximo, precio más bajo o un recuento de las descargas. Puede invertir el rango, alto a bajo, si desea que el modelo inverso (por ejemplo, a elementos de amplificación precio inferior más artículos de precio superior). Dado un rango de precios de 100 $ a $1, establezca `boostingRangeStart` a 100 y `boostingRangeEnd` en 1 para aumentar los elementos de un precio inferior. Esta función sólo puede usarse con campos doble y entero.
  - `distance`se recomienda cuando desea aumentar la proximidad o ubicación geográfica. Esta función sólo puede utilizarse con `Edm.GeographyPoint` campos.
  - `tag`se recomienda cuando desea aumentar por etiquetas en común entre los documentos y las consultas de búsqueda. Esta función sólo puede utilizarse con `Edm.String` y `Collection(Edm.String)` campos.
  
#### <a name="rules-for-using-functions"></a>Reglas para el uso de funciones ####

  - Tipo de función (actualización, magnitud, distancia, etiqueta) debe ser minúscula.
  - Funciones no pueden incluir valores nulos o vacíos. Específicamente, si incluye fieldname, debe establecer un valor.
  - Funciones solo se pueden aplicar a los campos pueden filtrarse. Para obtener más información acerca de los campos pueden filtrarse, vea [Crear un índice](search-api-2015-02-28-preview.md#CreateIndex) .
  - Funciones solo se aplica a los campos que se definen en la colección de campos de un índice.

Después de define el índice, cargar el esquema de índice, seguido de documentos para generar el índice. Consulte [Crear un índice](search-api-2015-02-28-preview.md#CreateIndex) y [Agregar o actualizar documentos](search-api-2015-02-28-preview.md#AddOrUpdateDocuments) para obtener instrucciones sobre estas operaciones. Una vez creado el índice, debe tener un perfil de puntuación funcional que funciona con los datos de búsqueda.

<a name="bkmk_template"></a>
## <a name="template"></a>Plantilla
Esta sección muestra la sintaxis y la plantilla de recuento de perfiles. Consulte [referencia de atributos de índice](#bkmk_indexref) en la siguiente sección para obtener una descripción de los atributos.

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
            ...
          }
        },
        "functions": (optional) [
          {
            "type": "magnitude | freshness | distance | tag",
            "boost": # (positive number used as multiplier for raw score != 1),
            "fieldName": "...",
            "interpolation": "constant | linear (default) | quadratic | logarithmic",

            "magnitude": {
              "boostingRangeStart": #,
              "boostingRangeEnd": #,
              "constantBoostBeyondRange": true | false (default)
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>
## <a name="scoring-profile-property-reference"></a>Referencia de la propiedad de perfil de puntuación

**Nota** Una función de puntuación solo se pueden aplicar a los campos que se pueden filtrarse.

| (Propiedad) | Descripción |
|----------|-------------|
| `name`   | Obligatorio. Este es el nombre del perfil de puntuación. Sigue las mismas convenciones de nomenclatura de un campo. Debe comenzar con una letra, no pueden contener puntos, coma o @ de símbolos y a continuación, no puede empezar con la frase "azureSearch" (distingue mayúsculas de minúsculas). |
| `text` | Contiene la propiedad grosores. |
| `weights` | Opcional. Un par de valor de nombre que especifica un nombre de campo y el grosor relativo. Ponderación relativa debe ser un entero positivo o el número de punto flotante. Puede especificar el nombre del campo sin un grosor correspondiente. Grosores se usan para indicar la importancia de un campo en relación con otra. |
| `functions` | Opcional. Tenga en cuenta que una función puntuación sólo puede aplicarse a los campos que se pueden filtrarse. |
| `type` | Necesaria para funciones de puntuación. Indica el tipo de función que se usará. Los valores válidos son `magnitude`, `freshness`, `distance` y `tag`. Puede incluir más de una función en cada perfil de puntuación. El nombre de la función debe ser minúscula. |
| `boost` | Necesaria para funciones de puntuación. Número positivo que se utiliza como multiplicación de resultado sin formato. No puede ser igual a 1. |
| `fieldName` | Necesaria para funciones de puntuación. Una función de puntuación solo se pueden aplicar a los campos que forman parte de la colección de campos del índice y que están pueden filtrarse. Además, cada tipo de función presenta restricciones adicionales (actualización se utiliza con campos de fecha y hora, magnitud con campos entero o doble, distancia con campos de ubicación y la etiqueta con la cadena o campos de la colección de cadena). Solo puede especificar un solo campo por la definición de la función. Por ejemplo, para usar la magnitud dos veces en el mismo perfil, necesarias para incluir dos magnitud definiciones, uno para cada campo. |
| `interpolation` | Necesaria para funciones de puntuación. Define la pendiente para que la puntuación incremento aumenta desde el inicio del rango hasta el final del rango. Los valores válidos son `linear` (predeterminado), `constant`, `quadratic`, y `logarithmic`. Para obtener más información, vea [establecer interpolaciones](#bkmk_interpolation) . |
| `magnitude` | La magnitud puntuación función se usa para modificar clasificaciones basándose en el rango de valores de un campo numérico. Algunos de los ejemplos de uso más común de esto son:<ul><li>Clasificación por estrellas: alterar la puntuación en función del valor en el campo "Clasificación de estrella". Cuando dos elementos son relevantes, primero se mostrará el elemento con la clasificación superior.</li><li>Margen: Cuando dos documentos relevantes, un distribuidor puede aumentar los documentos que tienen márgenes superior en primer lugar.</li><li>Haga clic en cuenta: haga clic en aplicaciones que un seguimiento a través de acciones a productos o páginas, podría usar magnitud a elementos de amplificación tiende a obtener el máximo partido de tráfico.</li><li>Descargar recuentos: para las aplicaciones que descargas de control, que mejore la permite a función magnitud elementos que tener más descargas.</li></ul> |
| `magnitude:boostingRangeStart` | Establece el valor de inicio del rango en los que se cuentan magnitud. El valor debe ser un número entero o punto flotante. Para la clasificación por estrellas de 1 a 4, es 1. Para los márgenes en un 50%, sería 50. |
| `magnitude:boostingRangeEnd` | Establece el valor de finalización del intervalo en los que se cuentan magnitud. El valor debe ser un número entero o punto flotante. Para la clasificación por estrellas de 1 a 4, sería 4. |
| `magnitude:constantBoostBeyondRange` | Los valores válidos son true o false (opción predeterminada). Cuando se establece en true, el aumento completo seguirá aplicar a los documentos que tienen un valor para el campo de destino es mayor que el extremo superior del rango. Si es false, el aumento de esta función no se pueden aplicar a los documentos que tiene un valor para el campo de destino que se encuentre fuera del rango. |
| `freshness` | La puntuación de la función de actualización se usa para modificar los resultados de clasificación de elementos en función de los valores de los campos de DateTimeOffset. Por ejemplo, un elemento con una fecha más reciente puede ordenarse superiores a los elementos antiguos. (Tenga en cuenta que también es posible JERARQUIA elementos como eventos del calendario con fechas futuras tal que los elementos más cerca de la presente pueden ordenarse superior a elementos aún más en el futuro). En la versión actual del servicio, un extremo del rango se solucionará a la hora actual. El otro extremo es una vez en el pasado en función de la `boostingDuration`. Para aumentar un intervalo de horas en el futuro usar negativo `boostingDuration`. La tasa a la que el aumento cambia de máximo y mínimo del intervalo depende de la interpolación se aplica al perfil puntuación (consulte la siguiente ilustración). Para invertir el factor aumentar aplicado, elija un factor de amplificación de menos de 1. |
| `freshness:boostingDuration` | Establece un período de caducidad después de incremento se detendrá para un documento concreto. Consulte [conjunto boostingDuration] [bkmk_boostdur #] en la siguiente sección para sintaxis y ejemplos. |
| `distance` | La distancia que se usa la función puntuación afectar a la puntuación de documentos en función de cómo cerrar o extremo están en relación con una ubicación geográfica en referencia. La ubicación de referencia se expresa como parte de la consulta en un parámetro (usando el `scoringParameter` parámetro de consulta) como un lon, lat argumento. |
| `distance:referencePointParameter` | Un parámetro para pasar de consultas para utilizarlo como ubicación de referencia. scoringParameter es un parámetro de consulta. Ver [Documentos de búsqueda](search-api-2015-02-28-preview.md#SearchDocs) para obtener una descripción de los parámetros de consulta. |
| `distance:boostingDistance` | Número que indica la distancia en kilómetros desde la ubicación de referencia donde termina el rango aumentar. |
| `tag` | La etiqueta puntuación función se usa para afectar a la puntuación de documentos basados en las etiquetas en documentos y las consultas de búsqueda. Documentos que tengan etiquetas en común con la consulta de búsqueda se ampliarse. Las etiquetas de la consulta de búsqueda se proporciona como un parámetro de puntuación en cada solicitud de búsqueda (usando el `scoringParameter` parámetro de consulta). |
| `tag:tagsParameter` | Un parámetro para pasar de consultas para especificar etiquetas para una solicitud determinada. `scoringParameter`es un parámetro de consulta. Ver [Documentos de búsqueda](search-api-2015-02-28-preview.md#SearchDocs) para obtener una descripción de los parámetros de consulta. |
| `functionAggregation` | Opcional. Se aplica solo cuando se especifican funciones. Los valores válidos son: `sum` (predeterminado), `average`, `minimum`, `maximum`, y `firstMatching`. Un resultado de búsqueda es un valor único que se calcula desde varias variables, incluyendo varias funciones. Este atributos indica cómo la mejora de todas las funciones se combina en un único amplificación de agregado que se aplica a la puntuación base del documento. La puntuación se basa en el valor de tf idf calculado desde el documento y la consulta de búsqueda. |
| `defaultScoringProfile` | Cuando se ejecuta una solicitud de búsqueda, si no se especifica ningún perfil puntuación, puntuación predeterminado es usado tf-idf (solo). Puntuación nombre del perfil predeterminado puede establecer aquí, causando búsqueda de Azure para usar ese perfil cuando no se proporciona ningún perfil específico de la solicitud de búsqueda. |

<a name="bkmk_interpolation"></a>
## <a name="set-interpolations"></a>Establecer interpolaciones

Interpolaciones le permiten definir la pendiente para que la puntuación incremento aumenta desde el inicio del rango hasta el final del rango. Pueden utilizar el interpolaciones siguientes:

  - `Linear`: Para los elementos que están dentro del intervalo de max y min, se realizará el aumento aplicado al elemento en una cantidad constantemente decreciente. Lineal es la interpolación predeterminada para un perfil de puntuación.
  - `Constant`: Para los elementos que se encuentran en el inicio y final del intervalo, un aumento constante se aplicará a los resultados de la jerarquía.
  - `Quadratic`: En comparación con una interpolación lineal que tiene un aumento constantemente decreciente, cuadrática inicialmente disminuirá a ritmo más pequeño y, a continuación, mientras que se aproxima el final del rango, disminuye en un intervalo mucho mayor. No se permite esta opción interpolación en etiqueta puntuación funciones.
  - `Logarithmic`: En comparación con una interpolación lineal que tiene un aumento constantemente decreciente, logarítmica inicialmente disminuirá a ritmo superior y luego medida que se aproxima el final del rango, disminuye intervalos mucho más pequeño. No se permite esta opción interpolación en etiqueta puntuación funciones.

<a name="Figure1"></a>
 ![][1]

<a name="bkmk_boostdur"></a>
## <a name="set-boostingduration"></a>Establecer boostingDuration

`boostingDuration`es un atributo de la función de actualización. Se usa para establecer una caducidad punto después incremento se detendrá para un documento concreto. Por ejemplo, para aumentar una línea o marca durante un período de promoción de 10 días, especificaría el período de 10 días como "P10D" para dichos documentos. O aumentar especifican próximos eventos de la semana siguiente "-P7D".

`boostingDuration`debe tener el formato como un valor de "dayTimeDuration" XSD (un subconjunto de un valor de duración ISO 8601 restringido). El modelo de esto es: `[-]P[nD][T[nH][nM][nS]]`.

La tabla siguiente proporciona varios ejemplos.

| Duración | boostingDuration |
|----------|------------------|
| 1 día | "P1D" |
| 2 días y 12 horas | "P2DT12H" |
| 15 minutos | "PT15M" |
| 30 días, 5 horas, 10 minutos y 6.334 segundos | "P30DT5H10M6.334S" |

Para obtener más ejemplos, vea [esquema XML: tipos de datos (sitio web de W3.org)](http://www.w3.org/TR/xmlschema11-2/).

**Vea también**
[API de REST de servicio de búsqueda de Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) en MSDN <br/>
[Crear índice (búsqueda Azure API)](http://msdn.microsoft.com/library/azure/dn798941.aspx) en MSDN<br/>
[Agregar un perfil de puntuación a un índice de búsqueda](http://msdn.microsoft.com/library/azure/dn798928.aspx) en MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
