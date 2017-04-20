<properties 
    pageTitle="Referencia de análisis en la aplicación perspectivas | Microsoft Azure" 
    description="Referencia de instrucciones de análisis de la herramienta de búsqueda eficaces de perspectivas de aplicación. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="awills"/>

# <a name="reference-for-analytics"></a>Referencia para análisis

[El análisis](app-insights-analytics.md) es la característica de búsqueda eficaz de [Perspectivas de aplicación](app-insights-overview.md). Estas páginas describen el lenguaje de consulta de análisis.

> [AZURE.NOTE] [Versión de prueba de análisis en nuestros datos simuladas](https://analytics.applicationinsights.io/demo) si su aplicación no enviar datos de aplicación impresiones todavía.

## <a name="index"></a>Índice


**Permitir** [Permitir](#let-clause)


**Operadores y consultas** [recuento](#count-operator)  |  [evaluar](#evaluate-operator) | [Extender](#extend-operator) | [unirse](#join-operator) | [límite](#limit-operator) | [mvexpand](#mvexpand-operator) | [analizar](#parse-operator) | [proyecto](#project-operator) | [proyecto ausente](#project-away-operator) | [rango](#range-operator) | [reducir](#reduce-operator) | [representar directiva](#render-directive) | [Restringir Cláusula](#restrict-clause) | [Ordenar](#sort-operator) | [resumir](#summarize-operator) | [tomar](#take-operator) | [principio](#top-operator) | [anidados a la parte superior](#top-nested-operator) | [unión](#union-operator) | [donde](#where-operator) | [dónde en](#where-in-operator)

**Agregaciones** [cualquier](#any)  |  [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [recuento](#count) | [contar.Si](#countif) | [BDCONTAR](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [percentil](#percentile) | [percentiles](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [DesvEst](#stdev) | [suma](#sum) | [variación](#variance)

**Escalares** [Literales booleanos](#boolean-literals)  |  [Operadores booleanos](#boolean-operators) | [conversiones](#casts) | [comparaciones escalares](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**Números** [Operadores aritméticos](#arithmetic-operators)  |  [Literales numéricos](#numeric-literals) | [abs](#abs) | [Papelera](#bin) | [exp](#exp) | [piso](#floor) | [gamma](#gamma) | [registro](#log) | [aleatorio](#rand) | [RAIZ](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Fecha y hora** [Fecha y hora expresiones](#date-and-time-expressions)  |  [Fecha y hora](#date-and-time-literals) | [hace](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [ahora](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Cadena** [GUID](#guids)  |  [Protegerlos literales de cadena](#obfuscated-string-literals) | [Literales de cadena](#string-literals) | [comparaciones de cadenas](#string-comparisons) | [countof](#countof) | [extraer](#extract) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [Reemplazar](#replace) | [dividir](#split) | [strcat](#strcat) | [strlen](#strlen) | [subcadena](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Matrices, objetos y dinámicas** [Matriz y objeto literales](#array-and-object-literals)  |  [Funciones de objeto dinámico](#dynamic-object-functions) | [objetos dinámicos en cláusulas permiten](#dynamic-objects-in-let-clauses) | [expresiones de ruta de acceso de JSON](#json-path-expressions) | [nombres](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [rango](#range) | [todynamic](#todynamic) | [treepath](#treepath)



## <a name="let"></a>Permitir

### <a name="let-clause"></a>permitir que la cláusula

**Tabular permiten: una tabla de nombres**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Valor escalar permiten - un valor de nomenclatura**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda permiten - una función de nomenclatura**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime) { strcat(getmonth(t),'/',dayofmonth(t),'/',getyear(t)) }; 
    requests | summarize count() by bin(timestamp, 1d) | project count_, day=us_date(timestamp)

Una cláusula permiten enlaza un [nombre](#names) a un resultado tabular, el valor escalar o la función. La cláusula es un prefijo a una consulta y el ámbito del enlace es esa consulta. (Dejar que no proporciona una manera a los elementos de nombre que usa más adelante en la sesión.)

**Sintaxis**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *tipo:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`,[`dynamic`](#dynamic-type)
* *plain_query:* Una consulta no precedida de una cláusula permitir.

**Ejemplos**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...


Autocombinación:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
  	| project start = timestamp, session_id
  	| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
  	| extend duration = stop - start 


## <a name="queries-and-operators"></a>Operadores y consultas

Una consulta sobre la telemetría se compone de una referencia a una secuencia de origen, seguida de una canalización de filtros. Por ejemplo:


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
Cada filtro precedido del carácter de canalización `|` es una instancia de un *operador*, con algunos parámetros. La entrada para el operador es la tabla que es el resultado de la canalización de anterior. En la mayoría de los casos, los parámetros son [expresiones escalares](#scalars) sobre las columnas de la entrada. En algunos casos, los parámetros son los nombres de las columnas de entrada y, en algunos casos, el parámetro es una segunda tabla. El resultado de una consulta siempre es una tabla, incluso si solo tiene una columna y una fila.

Las consultas pueden contener saltos de línea, pero son terminadas con una línea en blanco. Pueden contener comentarios entre `//` y al final de la línea.

Una consulta puede ir precedida uno o más [permiten cláusulas](#let-clause), que definir escalares, tablas o funciones que se pueden usar en la consulta.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T`se usa en los siguientes ejemplos de consulta para indicar la tabla de origen o de canalización anterior.
> 

### <a name="count-operator"></a>Operador Count

La `count` operador devuelve el número de registros (filas) en el conjunto de registros de entrada.

**Sintaxis**

    T | count

**Argumentos**

* *T*: los datos tabulares cuyos registros son para que se van a contar.

**Devuelve**

Esta función devuelve una tabla con un único registro y una columna de tipo `long`. El valor de la celda solo es el número de registros de *T*. 

**Ejemplo**

```AIQL
requests | count
```

### <a name="evaluate-operator"></a>evaluar el operador

`evaluate`es un mecanismo de extensión que permite algoritmos especializados que se anexa a las consultas.

`evaluate`debe ser el último operador en la canalización de la consulta (excepto para un posible `render`). No debe aparecer en el cuerpo de una función.

[evaluar autocluster](#evaluate-autocluster) | [evaluar cesta](#evaluate-basket) | [evaluar diffpatterns](#evaluate-diffpatterns) | [evaluar extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>evaluar autocluster

     T | evaluate autocluster()

AutoCluster busca patrones comunes de atributos discretas (dimensiones) de los datos y reducirá los resultados de la consulta original (si está 100 o 100 filas k) a un pequeño número de patrones. AutoCluster se ha desarrollado para ayudar a analizar errores (por ejemplo, excepciones, se bloquea), pero posiblemente puede trabajar en cualquier conjunto filtrado de datos. 

**Sintaxis**

    T | evaluate autocluster( arguments )

**Devuelve**

AutoCluster devuelve un conjunto de tramas que capturar partes de los datos con valores comunes compartidos entre varios atributos discretas (normalmente pequeño). Cada patrón está representado por una fila en los resultados. 

Las dos primeras columnas son el recuento y el porcentaje de filas de la consulta original que se capturan en el modelo. Las columnas restantes son de la consulta original y su valor es un valor específico de la columna o ' *' es decir, valores de variables. 

Observe que los patrones no son discontinuas: que pueden superponerse y normalmente no cubren todas las filas originales. Algunas filas no pueden ser en cualquier patrón.

**Sugerencias**

* Usar `where` y `project` en la canalización de entrada para reducir los datos a solo lo que le interesa.
* Cuando encuentre una fila interesante, desea explorar en profundidad aún más agregando sus valores específicos para su `where` filtro.

**Argumentos (opcionales)**

* `output=all | values | minimal` 

    El formato de los resultados. Las columnas de recuento y porcentaje siempre aparecen en los resultados. 

 * `all`-salen de todas las columnas de la entrada
 * `values`-filtros de columnas con solo ' *' en los resultados
 * `minimal`-También filtra las columnas que son idénticas para todas las filas de la consulta original. 


* `min_percent=`*doble* (predeterminado: 1)

    Cobertura de porcentaje mínimo de las filas generadas.

    Ejemplo:`T | evaluate autocluster("min_percent=5.5")`


* `num_seeds=`*int* (predeterminado: 25) 

    El número de semillas determina el número de puntos de búsqueda local inicial del algoritmo. En algunos casos, dependiendo de la estructura de los datos, aumenta el número de semillas aumenta el número (o calidad) de los resultados a través del espacio de búsqueda aumentado en más lento equilibrio de consulta. El argumento num_seeds tiene disminución resultados en ambas direcciones para disminuir 5 se logran mejoras de rendimiento mínima y crecer por encima de 50 casi nunca generará patrones adicionales.

    Ejemplo:`T | evaluate autocluster("num_seeds=50")`


* `size_weight=`*0 < doble < 1*+ (predeterminado: 0,5)

    Proporciona control sobre el equilibrio entre genérico (cobertura alto) e informativos (muchos valores compartidos). Size_weight creciente normalmente reduce el número de tramas y cada patrón suele cubrir un porcentaje mayor. Disminuir size_weight normalmente genera patrones más específicos con más valores compartidos y cobertura de porcentaje más pequeño. El mostrar opciones avanzadas fórmula es la media geométrica ponderada entre el resultado genérico normalizado y puntuación informativo con size_weight y size_weight 1 como los pesos. 

    Ejemplo:`T | evaluate autocluster("size_weight=0.8")`


* `weight_column=`*columna*

    Considere cada fila de la entrada según el peso especificado (de forma predeterminada cada fila tiene un grosor de '1'), uso común de una columna de peso es tengan en muestreos de cuenta o la creación de depósitos/agregación de los datos que ya está incrustadas en cada fila.

    Ejemplo:`T | evaluate autocluster("weight_column=sample_Count")` 



#### <a name="evaluate-basket"></a>evaluar la cesta

     T | evaluate basket()

Cesta busca todos los patrones frecuentes de atributos discretas (dimensiones) de los datos y devolverá todas las tramas frecuentes que pasa el umbral de frecuencia en la consulta original. Cesta se garantiza que buscar todos los patrones de frecuencia en los datos, pero no se garantiza polinómica tiempo de ejecución. El tiempo de ejecución de la consulta es lineal en el número de filas, pero en algunos casos, podría ser exponencial del número de columnas (dimensiones). Cesta se basa en el algoritmo de Apriori desarrollado originalmente para minería de datos de análisis de cesta. 

**Devuelve**

Todos los patrones que aparecen en más de una fracción especificada (el valor predeterminado es 0,05) de los eventos.

**Argumentos (opcionales)**


* `threshold=`*0,015 < doble < 1* (predeterminado: 0,05) 

    Establece la proporción de las filas se considera frecuentes mínima (no se devolverán tramas con relación menor).

    Ejemplo:`T | evaluate basket("threshold=0.02")`


* `weight_column=`*columna*

    Considere cada fila de la entrada según el peso especificado (de forma predeterminada cada fila tiene un grosor de '1'), uso común de una columna de peso es tengan en muestreos de cuenta o la creación de depósitos/agregación de los datos que ya está incrustadas en cada fila.

    Ejemplo: T | evaluar basket("weight_column=sample_Count")


* `max_dims=`*1 < int* (predeterminado: 5)

    Establece el número máximo de dimensiones y por cesta, limitada de forma predeterminada para reducir el tiempo de ejecución de consulta.


* `output=minimize` | `all` 

    El formato de los resultados. Las columnas de recuento y porcentaje siempre aparecen en los resultados.

 * `minimize`-filtros de columnas con solo ' *' en los resultados.
 * `all`-salen de todas las columnas de la entrada.




#### <a name="evaluate-diffpatterns"></a>evaluar diffpatterns

     requests | evaluate diffpatterns("split=success")

Diffpatterns compara dos conjuntos de datos de la misma estructura y busca patrones de atributos discretas (dimensiones) que caracterizan las diferencias entre los dos conjuntos de datos. Diffpatterns se ha desarrollado para ayudar a analizar los errores (por ejemplo, errores de comparación no errores en un período de tiempo determinado) pero potencialmente puede encontrar las diferencias entre los dos conjuntos de datos de la misma estructura. 

**Sintaxis**

`T | evaluate diffpatterns("split=`*BinaryColumn*`" [, arguments] )`

**Devuelve**

Diffpatterns devuelve un conjunto (normalmente pequeño) de tramas que capturar distintas partes de los datos en dos conjuntos (es decir, una trama captura un gran porcentaje de las filas en el primer conjunto de datos y bajo porcentaje de las filas en el segundo conjunto). Cada patrón está representado por una fila en los resultados.

Las primeras cuatro columnas son el recuento y el porcentaje de filas de la consulta original que se capturan en el modelo de cada conjunto, en la quinta columna es la diferencia (en puntos de porcentaje absoluta) entre los dos conjuntos. Las columnas restantes son de la consulta original y su valor es un valor específico de la columna o * lo que significa que los valores de variables. 

Tenga en cuenta que los patrones no son distintos: que pueden superponerse y normalmente no cubren todas las filas originales. Algunas filas no pueden ser en cualquier patrón.

**Sugerencias**

* Usar dónde y del proyecto en la canalización de entrada para reducir los datos a solo lo que le interesa.

* Cuando encuentre una fila interesante, desea explorar en profundidad aún más agregando sus valores específicos para filtrar su dónde.

**Argumentos**

* `split=`*nombre de columna* (obligatorio)

    La columna debe tener exactamente con dos valores. Si es necesario, cree una columna de este tipo:

    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`

* `target=`*cadena*

    Indica el algoritmo para buscar solo los patrones que tiene un porcentaje superior en el conjunto de datos de destino, el destino debe ser uno de los dos valores de la división de columna.

    `requests | evaluate diffpatterns("split=success", "target=false")`

* `threshold=`*0,015 < doble < 1* (predeterminado: 0,05) 

    Establece la diferencia de trama mínimas (proporción) entre los dos conjuntos.

    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`

* `output=minimize | all`

    El formato de los resultados. Las columnas de recuento y porcentaje siempre aparecen en los resultados. 

 * `minimize`-filtros de columnas con solo ' *' en los resultados
 * `all`-salen de todas las columnas de la entrada

* `weight_column=`*columna*

    Considere cada fila de la entrada según el peso especificado (de forma predeterminada, cada fila tiene un grosor de '1'). Un uso común de una columna de peso es tengan en muestreos de cuenta o la creación de depósitos/agregación de los datos que ya está incrustadas en cada fila.

    `requests | evaluate autocluster("weight_column=itemCount")`






#### <a name="evaluate-extractcolumns"></a>evaluar extractcolumns

     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns se usa para enriquecer una tabla con varias columnas simples dinámicamente extraídas fuera (semitransparente de títulos) columnas estructuradas según su tipo. Actualmente es compatible con json columnas solo, ambos dinámicos y serialización de jsons de cadena.


* `max_columns=`*int* (predeterminado: 10) 

    El número de columnas agregados nuevas es dinámico y puede ser muy grande (realmente es el número de claves distintas en todos los registros de json) para que no se debemos limitar. Las nuevas columnas se ordenan en orden descendente basándose en su frecuencia y hasta max_columns se agregan a la tabla.

    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`


* `min_percent=`*doble* (predeterminado: 10.0) 

    Otra manera de limitar las nuevas columnas y omitir columnas cuya frecuencia es menor que min_percent.

    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`


* `add_prefix=`*bool* (predeterminado: verdadero) 

    Si es true se agrega el nombre de la columna compleja como un prefijo a los nombres de columnas extraída.


* `prefix_delimiter=`*cadena* (predeterminado: "_") 

    Si add_prefix = true este parámetro define el delimitador que se usará para concatenar los nombres de las nuevas columnas.

    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`


* `keep_original=`*bool* (predeterminado: falso) 

    Si es true las columnas (json) originales se guardarán en la tabla de resultados.


* `output=query | table` 

    El formato de los resultados. 

 * `table`-El resultado es la misma tabla recibida menos las columnas de entrada especificadas más columnas nuevas que se han extraído de las columnas de entrada.
 * `query`-El resultado es una cadena que representa la consulta que haría para obtener el resultado como tabla. 




### <a name="extend-operator"></a>extender el operador

     T | extend duration = stopTime - startTime

Anexar una o más columnas calculadas a una tabla. 


**Sintaxis**

    T | extend ColumnName = Expression [, ...]

**Argumentos**

* *T:* La tabla de entrada.
* *ColumnName:* El nombre de las columnas que desea agregar. [Nombres](#names) distinguen mayúsculas de minúsculas y pueden contener alfabético, numérico o '_' caracteres. Usar `['...']` o `["..."]` a presupuesto nombres con otros caracteres o palabras clave.
* *Expresión:* Un cálculo de las columnas existentes.

**Devuelve**

Una copia de la tabla de entrada con las columnas especificadas adicionales.

**Sugerencias**

* Usar [`project`](#project-operator) en su lugar, si también desea quitar o cambiar el nombre de algunas columnas.
* No use `extend` dar un nombre más corto para usar en una expresión de tipo long. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    Se han indizado las columnas de la tabla nativas; el nuevo nombre define una columna adicional que no está indizada, por lo que es probable que se ejecuten más despacio la consulta.

**Ejemplo**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### <a name="join-operator"></a>operador de unión

    Table1 | join (Table2) on CommonColumn

Combina las filas de dos tablas valores coincidentes de la columna especificada.


**Sintaxis**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argumentos**

* *Tabla1* - la 'izquierda' de la combinación
* *Tabla2* - el lado derecho de la combinación. Puede ser una expresión de consulta anidada que genera una tabla.
* *CommonColumn* - una columna que tiene el mismo nombre en las dos tablas.
* *Tipo* - especifica cómo se asociarán filas de ambas tablas.

**Devuelve**

Una tabla con:

* Una columna para cada columna en cada una de las dos tablas, incluyendo las claves coincidentes. Las columnas de la derecha se cambiará automáticamente si hay conflictos de nombres.
* Una fila para cada coincidencia entre las tablas de entrada. Una coincidencia es una fila seleccionada de una tabla que tiene el mismo valor para todos los `on` campos como una fila de la otra tabla. 

* `Kind`no se especifica

    Solo una fila de la izquierda se hace coincidir cada valor de la `on` clave. El resultado contiene una fila por cada coincidencia de esta fila con filas de la derecha.

* `Kind=inner`
 
     Hay una fila de la salida de todas las combinaciones de filas coincidentes de la izquierda y derecha.

* `kind=leftouter`(or `kind=rightouter` or `kind=fullouter`)

     Además de las coincidencias internas, hay una fila para cada fila en el lado izquierdo (o derecha), incluso si no tiene ninguna coincidencia. En ese caso, las celdas de resultado no coincidentes contienen valores nulos.

* `kind=leftanti`

     Devuelve todos los registros del lado izquierdo que no tienen coincidencias desde la derecha. La tabla de resultados solo tiene las columnas de la izquierda. 
 
Si hay varias filas con los mismos valores de los campos, recibirá filas para todas las combinaciones.

**Sugerencias**

Para mejorar el rendimiento:

* Usar `where` y `project` para reducir el número de filas y columnas en las tablas de entrada, antes del `join`. 
* Si una tabla siempre es menor que la otra, puede usarlo como el lado izquierdo (canalizado) de la combinación.
* Las columnas para la coincidencia de combinación deben tener el mismo nombre. Use el operador de proyecto si es necesario cambiar el nombre de una columna en una de las tablas.

**Ejemplo**

Obtener extendido actividades de un registro en el que algunos movimientos marca el inicio y final de una actividad. 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
  	| where Name == "Start"
  	| project Name, City, ActivityId, StartTime=timestamp
  	| join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
  	| project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>operador de límite

     T | limit 5

Devuelve hasta el número especificado de filas de la tabla de entrada. No hay ninguna garantía los registros devueltos. (Para obtener registros específicos, use [`top`](#top-operator).)

**Alias**`take`

**Sintaxis**

    T | limit NumberOfRows


**Sugerencias**

`Take`es una forma sencilla y eficaz para ver una muestra de los resultados cuando se trabaja de forma interactiva. Tenga en cuenta que no garantiza para generar las filas concretas o para que se producen en un orden determinado.

Hay un límite implícito del número de filas devueltas al cliente, incluso si no usa `take`. Para levantar este límite, use la `notruncation` opción de solicitud de cliente.



### <a name="mvexpand-operator"></a>operador de mvexpand

    T | mvexpand listColumn 

Expande una lista de una celda (JSON) escribió dinámicos de forma que cada entrada tiene una fila distinta. Se duplican todas las celdas en una fila expandida. 

(Vea también [`summarize makelist`](#summarize-operator) que realiza la función opuesta.)

**Ejemplo**

Supongamos que la tabla de entrada es:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Hola"|{"clave": "valor"}|
|2|"todos"|[0,1, "k", "v"]|

    mvexpand D

El resultado es:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Hola"|{"clave": "valor"}|
|2|"todos"|0|
|2|"todos"|1|
|2|"todos"|"k"|
|2|"todos"|"v"|


**Sintaxis**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Argumentos**

* *ColumnName:* En el resultado, matrices en la columna con nombre se expanden a varias filas. 
* *ArrayExpression:* Una expresión que revela una matriz. Si se usa este formulario, se agrega una nueva columna y se conserva uno existente.
* *Nombre:* Un nombre para la nueva columna.
* *Typename:* La expresión expandida a un tipo determinado
* *RowLimit:* El número máximo de filas generado a partir de cada fila original. El valor predeterminado es 128.

**Devuelve**

Varias filas para cada uno de los valores de una matriz en la columna con nombre o en la expresión de matriz.

La columna expandida siempre tiene tipo dinámico. Utilizar una conversión como `todatetime()` o `toint()` si desea calcular o agregar valores.

Se admiten dos modos de expansiones bolsa de propiedades:

* `bagexpansion=bag`: Los contenedores de propiedades se expanden en los contenedores de propiedades de una entrada. Esta es la expansión de forma predeterminada.
* `bagexpansion=array`: Los contenedores de propiedades se expanden en dos elementos `[` *clave*`,`*valor* `]` matriz estructuras, lo que permite acceso universal a claves y valores (así como, por ejemplo, ejecute una agregación Recuento distinto sobre los nombres de propiedad). 

**Ejemplos**


    exceptions | take 1 
  	| mvexpand details[0]

Un registro se divide en filas para cada elemento en el campo detalles.



### <a name="parse-operator"></a>operador de análisis

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

Extrae los valores de una cadena. Puede usar la coincidencia de expresión regular o simple.

**Sintaxis**

    T | parse [kind=regex|relaxed] SourceText 
        with [Match | Column [: Type [*]] ]  ...

**Argumentos**

* `T`La tabla de entrada.
* `kind`: 
 * `simple`(predeterminado): la `Match` cadenas son cadenas sin formato.
 * `relaxed`: si el texto no analiza como el tipo de una columna, la columna se establece en null y el análisis continúa 
 * `regex`: la `Match` cadenas son expresiones regulares.
* `Text`: Una columna u otra expresión que se evalúa como o se puede convertir en una cadena.
* *Coincidir:* Coincidir con el elemento siguiente de la cadena y descartarla.
* *Columna:* Asignar el elemento siguiente de la cadena a esta columna. Si no existe, se crea la columna.
* *Tipo:* Analizar el elemento siguiente de la cadena como el tipo especificado, como int, fecha, doble. 


**Devuelve**

La tabla entrada, extendida según la lista de columnas.

Los elementos de la `with` cláusula se comparan con el texto de origen por separado. Cada elemento chews desactivar un fragmento de texto de origen: 

* Una cadena literal o expresión regular mueve el cursor coincidente por la longitud de la coincidencia.
* En un análisis de regex, una expresión regular puede usar el operador de reducción '?' tan pronto como sea posible mover a la siguiente coincidencia.
* El nombre de una columna con un tipo analiza el texto como el tipo especificado. A menos que tipo = Media, un análisis incorrecta invalida que coincidan con el patrón todo.
* Un nombre de columna sin un tipo o con el tipo 'cadena' copia el número mínimo de caracteres para ir a la coincidencia siguiente.
* ' *' Se omite el número mínimo de caracteres para ir a la coincidencia siguiente. Puede usar '*' al principio y al final de la trama, o después de un tipo distinto de cadena o entre coincidencias de cadena.

Todos los elementos de un modelo de análisis deben coincidir correctamente; en caso contrario, no se producirá ningún resultado. La excepción a esta regla es que, cuando amable = Media, si no puede analizar una variable escrita, el resto del análisis continúa.

**Ejemplos**

*Simple:*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

x | contador | presentar | Año
---|---|---|---
1 | 2 | SOCKS | 63

*Más flexible:*

Cuando la entrada contiene una coincidencia correcta para cada columna escrito, un análisis flexible genera el mismo resultado que un análisis simple. Pero si una de las columnas con tipo no analiza correctamente, sigue un análisis flexible procesar el resto del modelo, mientras que un análisis simple se detiene y no genera ningún resultado.


```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


x  | presentar | Año
---|---|---
1 |  SOCKS | 63


*Regex:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\\)"
| project-away x, s
```

recursos | segmentación de datos | bloquear | versión | anterior
---|---|---|---|---
Programador | 16 | 17/02/2016 08:41:00 | 17/02/2016 08:41 | 2016-02-17T08:40:00Z

### <a name="project-operator"></a>operador de proyecto

    T | project cost=price*quantity, price

Seleccione las columnas para incluir, cambiar el nombre o quitar e insertar nuevas columnas calculadas. Se especifica el orden de las columnas en el resultado por el orden de los argumentos. Solo las columnas especificadas en los argumentos se incluyen en el resultado: las otras personas de la entrada se eliminan.  (Vea también `extend`.)


**Sintaxis**

    T | project ColumnName [= Expression] [, ...]

**Argumentos**

* *T:* La tabla de entrada.
* *ColumnName:* El nombre de una columna para que aparezcan en los resultados. Si no hay ninguna *expresión*, debe aparecer una columna de ese nombre en la entrada. [Nombres](#names) distinguen mayúsculas de minúsculas y pueden contener alfabético, numérico o '_' caracteres. Usar `['...']` o `["..."]` a presupuesto nombres con otros caracteres o palabras clave.
* *Expresión:* Expresión escalar opcional que hace referencia a las columnas de entrada. 

    Es válido para devolver una nueva columna calculada con el mismo nombre que una columna existente en la entrada.

**Devuelve**

Una tabla que tiene las columnas denominadas como argumentos y tantas filas como la tabla de entrada.

**Ejemplo**

En el ejemplo siguiente se muestra varios tipos de manipulación que puede llevar a cabo utilizando la `project` operador. La tabla entrada `T` tiene tres columnas de tipo `int`: `A`, `B`, y `C`. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>operador de proyecto

    T | project-away column1, column2, ...

Excluir columnas especificadas. El resultado contiene todas las columnas entradas excepto los que asigne un nombre.

### <a name="range-operator"></a>operador de rango

    range LastWeek from ago(7d) to now() step 1d

Genera una tabla de una columna de valores. Observe que no dispone de una canalización de entrada. 

|SemanaPasada|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|09:10:04.627 2015-12-12|



**Sintaxis**

    range ColumnName from Start to Stop step Step

**Argumentos**

* *ColumnName:* El nombre de la columna única en la tabla de resultados.
* *Inicio:* El menor valor en el resultado.
* *Detener:* El valor más alto que se genera el resultado (o un límite del valor más alto, si *paso* pasos sobre este valor).
* *Paso:* La diferencia entre dos valores consecutivos. 

Los argumentos deben ser valores numéricos, una fecha o un intervalo de tiempo. Que no se hacen referencia a las columnas de una tabla. (Si desea calcular el intervalo basado en una tabla de entrada, use la [ *función*de rango](#range), tal vez con el [operador mvexpand](#mvexpand-operator).) 

**Devuelve**

Una tabla con una única columna denominada *ColumnName*, cuyos valores son *Inicio*, *Iniciar* + *paso*,... hasta e incluyendo *Detener*.

**Ejemplo**  

```AIQL
range Steps from 1 to 8 step 3
```

Una tabla con una única columna denominada `Steps` cuyo tipo es `long` y cuyos valores son `1`, `4`, y `7`.

**Ejemplo**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Una tabla de la medianoche en los siete últimos días. La función de la Papelera (planta) reduce cada vez al inicio del día.

**Ejemplo**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Muestra cómo el `range` operador puede utilizarse para crear un pequeño, ad-hoc, tabla de dimensión que se utiliza para presentar ceros donde el origen de datos no tiene valores.

### <a name="reduce-operator"></a>reducir el operador

    exceptions | reduce by outerMessage

Intenta agrupar registros similares. Para cada grupo, el operador envía el `Pattern` piensa que mejor describe ese grupo y la `Count` de registros de ese grupo.


![](./media/app-insights-analytics-reference/reduce.png)

**Sintaxis**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argumentos**

* *ColumnName:* La columna para examinar. Debe ser de tipo cadena.
* *Umbral:* Un valor en el intervalo {de 0 a 1}. El valor predeterminado es 0.001. Para grandes entradas umbral debería pequeña. 

**Devuelve**

Dos columnas, `Pattern` y `Count`. En muchos casos, trama será un valor de la columna completo. En algunos casos, puede identificar términos comunes y reemplazar las partes variables con ' *'.

Por ejemplo, el resultado de `reduce by city` pueden incluir: 

|Trama | Recuento |
|---|---|
| SAN * | 5182 |
| SAN * | 2846 |
| Moscú | 3726 |
| \*-en:\* | 2730 |
| París | 27163 |


### <a name="render-directive"></a>representar directiva

    T | render [ table | timechart  | barchart | piechart ]

Representación dirige a la capa de presentación cómo mostrar la tabla. Debe ser el último elemento de la canalización. Es una alternativa cómoda a utilizar los controles en la pantalla, lo que le permite guardar una consulta con un método de presentación determinado.

### <a name="restrict-clause"></a>restringir la cláusula 

Especifica el conjunto de nombres de tabla disponibles a los operadores que siguen. Por ejemplo:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sort-operator"></a>operador de ordenación 

    T | sort by country asc, price desc

Ordenar las filas de la tabla de entrada en orden por una o más columnas.

**Alias**`order`

**Sintaxis**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argumentos**

* *T:* La tabla de entrada para ordenar.
* *Columna:* Columna de *T* de ordenación. El tipo de los valores debe ser numérico, fecha, hora o cadena.
* `asc`Ordenar por en orden ascendente, bajo a alto. El valor predeterminado es `desc`, descendente de alto a bajo.

**Ejemplo**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Todas las filas de seguimientos de tabla que tienen un determinado `ActivityId`, ordenados por su marca de tiempo.

### <a name="summarize-operator"></a>resumir operador

Genera una tabla que agrega el contenido de la tabla de entrada.
 
    requests
  	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Una tabla que muestra el número de duración de solicitud promedio y conjunto de ciudades de cada país. Hay una fila en los resultados de los distintos países. Las columnas de salida muestran el recuento, duración promedio, ciudades y país. Se pasan por alto todas las columnas de entrada.


    T | summarize count() by price_range=bin(price, 10.0)

Una tabla que muestra cuántos elementos tienen los precios de cada intervalo [0,10.0], [10.0,20.0], y así sucesivamente. Este ejemplo tiene una columna para la cuenta y otro para el intervalo de precios. Se pasan por alto todas las columnas de entrada.


**Sintaxis**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argumentos**

* *Columna:* Nombre opcional para una columna de resultados. Valores predeterminados de un nombre que se derivan de la expresión. [Nombres](#names) distinguen mayúsculas de minúsculas y pueden contener alfabético, numérico o '_' caracteres. Usar `['...']` o `["..."]` a presupuesto nombres con otros caracteres o palabras clave.
* *Agregación:* Una llamada a una función de agregación como `count()` o `avg()`, con nombres de columna como argumentos. Consulte [agregaciones](#aggregations).
* *GroupExpression:* Una expresión de las columnas, proporciona un conjunto de valores distintos. Normalmente es un nombre de columna que ya proporciona un conjunto de valores, restringido o `bin()` con una columna numérica o de tiempo como argumento. 

Si desea proporcionar una expresión numérica o de tiempo sin usar `bin()`, análisis aplica automáticamente con un intervalo de `1h` para ocasiones, o `1.0` para los números.

Si no proporciona una *GroupExpression,* toda la tabla se resume en una fila de resultado único.



**Devuelve**

Las filas de entrada se organizan en grupos que tengan los mismos valores de la `by` expresiones. A continuación, se calculan las funciones de agregación especificada sobre cada grupo, que genera una fila para cada grupo. El resultado contiene el `by` también al menos una columna para cada uno y columnas calculan agregado. (Algunas funciones de agregación devuelven varias columnas).

El resultado tiene tantas filas como hay distintas combinaciones de `by` valores. Si desea resumir los rangos de valores numéricos, use `bin()` reducir rangos de valores independientes.

**Nota**

Aunque puede proporcionar expresiones arbitrarias para la agregación y expresiones de agrupamiento, es más eficaz usar nombres de columna simple o aplicar `bin()` a una columna numérica.



### <a name="take-operator"></a>operador Take

Alias de [límite](#limit-operator)


### <a name="top-operator"></a>operador superior

    T | top 5 by Name desc nulls first

Devuelve los primeros registros *N* ordenados por las columnas especificadas.


**Sintaxis**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [`nulls first`|`nulls last`] [, ... ]

**Argumentos**

* *NumberOfRows:* El número de filas de *T* para devolver.
* *Sort_expression:* Una expresión de ordenación de las filas. Suele ser un nombre de columna. Puede especificar más de un sort_expression.
* `asc`o `desc` (predeterminado) puede aparecer al control si la selección está realmente desde la "inferior" o "superior" del intervalo.
* `nulls first`o `nulls last` controles donde aparecen los valores nulos. `First`es el valor predeterminado para `asc`, `last` es el valor predeterminado para `desc`.


**Sugerencias**

`top 5 by name`primera vista equivale a `sort by name | take 5`. Sin embargo, se ejecuta más rápidamente y siempre devuelve ordena los resultados, mientras que `take` ofrece ninguna garantía de este tipo.

### <a name="top-nested-operator"></a>operador anidados a la parte superior

    requests 
  	| top-nested 5 of name by count()  
    , top-nested 3 of performanceBucket by count() 
    , top-nested 3 of client_CountryOrRegion by count()
  	| render barchart 

Genera resultados jerárquicos, donde cada nivel es un detalle desde el nivel anterior. Es útil para responder a preguntas que suenen como "¿Qué son las solicitudes de 5 superiores y para cada uno de ellos, ¿cuáles son los depósitos superior 3 rendimiento y para cada uno de ellos, que son los países de la parte superior 3 las solicitudes proceden?"

**Sintaxis**

   T | anidar principio N de columna por la agregación [,...]

**Argumentos**

* N:int - número de filas para devolver o pasar al siguiente nivel. En una consulta con tres niveles donde N es 5, 3 y 3, el número total de filas será 45.
* COLUMNA: una columna para agrupar por para la agregación. 
* AGREGACIÓN - una [función de agregación](#aggregations) para aplicar a cada grupo de filas. Los resultados de estos agregaciones determinará los grupos superiores para que se muestre.


### <a name="union-operator"></a>operador de unión

     Table1 | union Table2, Table3

Toma dos o más tablas y devuelve las filas de todos ellos. 

**Sintaxis**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argumentos**

* *Tabla1*, *tabla2* ...
 *  El nombre de una tabla, como por ejemplo `requests`, o una tabla definida en un [permiten cláusula](#let-clause); o
 *  Una expresión de consulta como`(requests | where success=="True")`
 *  Un conjunto de tablas especificadas con un carácter comodín. Por ejemplo, `e*` ¿forma la unión de todas las tablas que se definen en cláusulas permiten anteriores cuyo nombre comienza con 'e', junto con la tabla 'excepciones'.
* `kind`: 
 * `inner`-El resultado tiene el subconjunto de columnas que sean comunes a todas las tablas de entrada.
 * `outer`-El resultado tiene todas las columnas que se producen en cualquiera de las entradas. Las celdas que no se han definido por una fila de entrada se establecen en `null`.
* `withsource=`*ColumnName:* Si se especifica, el resultado incluye una columna denominada *ColumnName* cuyo valor indica qué tabla de origen ha contribuido en cada fila.

**Devuelve**

Una tabla con tantas filas como está en todas las tablas de entrada y tantas columnas como son los nombres de columna única en las entradas.

**Ejemplo**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
Unión de todas las tablas cuyos nombres empiezan "tt".


**Ejemplo**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
El número de usuarios diferentes que se haya producido ya sea un `exceptions` evento o una `traces` evento sobre el día anterior. En el resultado, la columna 'SourceTable' indicará "Consulta" o "Comando".

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Esta versión más eficaz genera el mismo resultado. Filtra cada tabla antes de crear la unión.

### <a name="where-operator"></a>donde operador

     requests | where resultCode==200

Filtra una tabla al subconjunto de filas que cumplen un predicado.

**Alias**`filter`

**Sintaxis**

    T | where Predicate

**Argumentos**

* *T:* Entrada tabular cuyos registros son va a filtrar.
* *Predicado:* A `boolean` [expresión](#boolean) sobre las columnas de *T*. Se evalúa para cada fila de *T*.

**Devuelve**

Filas en *T* cuya *predicado* es `true`.

**Sugerencias**

Para obtener el mejor rendimiento:

* **Comparaciones de uso simples** entre los nombres de columna y constantes. (Significa 'Constante' constante sobre la tabla - para `now()` y `ago()` son correctas, y así que son valores escalares asignados mediante un [ `let` cláusula](#let-clause).)

    Por ejemplo, prefiere `where Timestamp >= ago(1d)` a `where floor(Timestamp, 1d) == ago(1d)`.

* **Simplest términos primero**: si tiene varias cláusulas conjunta con `and`, primero coloque las cláusulas que implican una sola columna. Así que `Timestamp > ago(1d) and OpId == EventId` es mejor que al revés.


**Ejemplo**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Registros no anteriores al 1 hora y provengan del origen denominado "Kuskus" y tienen dos columnas del mismo valor. 

Observe que incluimos la comparación entre dos columnas por último, ya que no puede utilizar el índice y exige un recorrido.


### <a name="where-in-operator"></a>operador de dónde

    requests | where resultCode !in (200, 201)

    requests | where resultCode in (403, 404)

**Sintaxis**

    T | where col in (expr1, expr2, ...)
    T | where col !in (expr1, expr2, ...)

**Argumentos**

* `col`: Una columna de la tabla.
* `expr1`...: Una lista de expresiones escalares.

Usar `in` se usa para incluir solo las filas en las que `col` es igual a una de las expresiones `expr1...`.

Usar `!in` para incluir sólo las filas que `col` no es igual a cualquiera de las expresiones `expr1...`.  


## <a name="aggregations"></a>Agregaciones

Las agregaciones son funciones que se usan para combinar valores de los grupos creados en la [operación de resumir](#summarize-operator). Por ejemplo, en esta consulta DCont() es una función de agregación:

    requests | summarize dcount(name) by success

### <a name="any"></a>cualquier 

    any(Expression)

Aleatoriamente selecciona una fila del grupo y devuelve el valor de la expresión especificada.

Es útil, por ejemplo, si alguna columna tiene un gran número de valores similares (por ejemplo, una columna de "texto de error") y desea que dicha columna contiene una vez por un valor único de la clave compuesta de grupo de ejemplo. 

**Ejemplo**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>
### <a name="argmin-argmax"></a>argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Encuentra una fila en el grupo que minimiza o maximiza *ExprToMaximize*y a continuación, devuelve el valor de *ExprToReturn* (o `*` para devolver toda la fila).

**Sugerencia**: las columnas pasan automáticamente se cambia el nombre. Para asegurarse de que está usando los nombres derecho, inspeccionar los resultados mediante `take 5` antes de que los resultados de canalización en otro operador.

**Ejemplos**

Para cada nombre de la solicitud, mostrar cuándo se produjo la solicitud más larga:

    requests | summarize argmax(duration, timestamp) by name

Mostrar todos los detalles de la solicitud más larga, no solo la marca de tiempo:

    requests | summarize argmax(duration, *) by name


Buscar el valor más bajo de cada métrica, junto con su marca de tiempo y otros datos:

    metrics 
  	| summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)
 


### <a name="avg"></a>AVG

    avg(Expression)

Calcula el promedio de *expresión* en el grupo.

### <a name="buildschema"></a>buildschema

    buildschema(DynamicExpression)

Devuelve el esquema mínimo que admite todos los valores de *DynamicExpression*. 

El tipo de columna del parámetro debe ser `dynamic` -una bolsa de propiedad o matriz. 

**Ejemplo**

    exceptions | summarize buildschema(details)

Resultado:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Observe que `indexer` se usa para marcar dónde debe usar un índice numérico. Para este esquema, algunas rutas de acceso válidas podría ser (suponiendo que estos índices de ejemplo se encuentran en el intervalo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Ejemplo**

Supongamos que la columna de entrada tiene tres valores dinámicos:

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


El esquema resultante sería:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

El esquema nos indica:

* El objeto raíz es un contenedor con cuatro propiedades denominado x, y, z y t.
* La propiedad denominada "x" que podría ser de tipo "int" o de tipo "cadena".
* La propiedad denomina "y" podría de cualquiera de tipo "double" u otro contenedor con una propiedad denominada "w" del tipo "cadena".
* La ``indexer`` palabra clave indica que "z" y "t" son matrices.
* Cada elemento de la matriz "z" es un entero o una cadena.
* "t" es una matriz de cadenas.
* Todas las propiedades es implícitamente opcional, y cualquier matriz puede estar vacía.

##### <a name="schema-model"></a>Modelo de esquema

La sintaxis del esquema devuelto es:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Son equivalentes a un subconjunto de las anotaciones de tipo de máquina codificadas como un valor dinámico. En la máquina, el esquema de ejemplo sería:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### <a name="count"></a>recuento

    count([ Predicate ])

Devuelve un recuento de filas para el *predicado* se evalúa como `true`. Si se especifica ningún *predicado* , devuelve el número total de registros en el grupo. 

**Sugerencia de rendimiento**: usar `summarize count(filter)` en lugar de`where filter | summarize count()`

> [AZURE.NOTE] Evite usar count() para encontrar el número de solicitudes, excepciones y otros eventos que se han producido. Cuando se [muestra](app-insights-sampling.md) en operación, el número de puntos de datos que se conservan en la aplicación perspectivas será menor que el número de eventos originales. En su lugar, use `summarize sum(itemCount)...`. La propiedad itemCount refleja el número de eventos originales que están representados por cada punto de datos que se conservan.

### <a name="countif"></a>contar.Si

    countif(Predicate)

Devuelve un recuento de filas para el *predicado* se evalúa como `true`.

**Sugerencia de rendimiento**: usar `summarize countif(filter)` en lugar de`where filter | summarize count()`

> [AZURE.NOTE] Evite usar contar.Si para encontrar el número de solicitudes, excepciones y otros eventos que se han producido. Cuando se [muestra](app-insights-sampling.md) en operación, el número de puntos de datos será menor que el número de eventos reales. En su lugar, use `summarize sum(itemCount)...`. La propiedad itemCount refleja el número de eventos originales que están representados por cada punto de datos que se conservan.

### <a name="dcount"></a>BDCONTAR

    dcount( Expression [ ,  Accuracy ])

Devuelve una estimación del número de valores distintos de *Expr* en el grupo. (Para mostrar los valores distintos, utilice [`makeset`](#makeset).)

*Precisión*, si se especifica, controla el equilibrio entre velocidad y precisión.

 * `0`= el cálculo menos preciso y más rápido.
 * `1`el valor predeterminado, que equilibra el tiempo de cálculo y precisión; información sobre errores de 0,8%.
 * `2`= cálculo más preciso y más lento; información sobre errores de 0,4%.

**Ejemplo**

    pageViews 
  	| summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### <a name="dcountif"></a>dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

Devuelve una estimación del número de valores distintos de *Expr* de filas en el grupo cuya *predicado* es true. (Para mostrar los valores distintos, utilice [`makeset`](#makeset).)

*Precisión*, si se especifica, controla el equilibrio entre velocidad y precisión.

 * `0`= el cálculo menos preciso y más rápido.
 * `1`el valor predeterminado, que equilibra el tiempo de cálculo y precisión; información sobre errores de 0,8%.
 * `2`= cálculo más preciso y más lento; información sobre errores de 0,4%.

**Ejemplo**

    pageViews 
  	| summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>MakeList

    makelist(Expr [ ,  MaxListSize ] )

Devuelve un `dynamic` matriz (JSON) de todos los valores de *expresión* en el grupo. 

* *MaxListSize* es un límite de entero opcional en el número máximo de elementos devueltos (el valor predeterminado es *128*).

### <a name="makeset"></a>makeset

    makeset(Expression [ , MaxSetSize ] )

Devuelve un `dynamic` matriz (JSON) del conjunto de valores distintos que *Expr* toma en el grupo. (Sugerencia: para contar solo los valores distintos, utilice [`dcount`](#dcount).)
  
*  *MaxSetSize* es un límite de entero opcional en el número máximo de elementos devueltos (el valor predeterminado es *128*).

**Ejemplo**

    pageViews 
  	| summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Vea también el [ `mvexpand` operador](#mvexpand-operator) para la función opuesta.


### <a name="max-min"></a>MAX, min

    max(Expr)

Calcula el número máximo de *Expr*.
    
    min(Expr)

Calcula el valor mínimo de *Expr*.

**Sugerencia**: Esto le proporciona la Mín o Máx en su propia - por ejemplo, la mayor o menor precio. Pero si desea que otras columnas de la fila - por ejemplo, el nombre del proveedor con el menor precio: usar [argmin o argmax](#argmin-argmax).


<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>
### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentil, percentiles, percentilew, percentilesw

    percentile(Expression, Percentile)

Devuelve una estimación para la *expresión* del percentil especificado en el grupo. La precisión depende de la densidad de población de la región del percentil.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Como `percentile()`, pero se calcula un número de valores de percentil (que es más rápido que calcular de forma individual cada percentil).

    percentilew(Expression, WeightExpression, Percentile)

Percentil ponderada. Use esta opción para datos agregados previamente.  `WeightExpression`es un entero que indica el número de filas original está representado por cada fila agregada.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Como `percentilew()`, pero se calcula un número de valores de percentil.

**Ejemplos**


El valor de `duration` que sea mayor de 95% de la muestra menor que 5% del conjunto de ejemplo, calculado para cada nombre de la solicitud y establecer:

    request 
  	| summarize percentile(duration, 95)
      by name

Omitir "por..." para calcular para toda la tabla.

Calcular simultáneamente varios valores de percentiles para nombres de solicitud diferente:

    
    requests 
  	| summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

Los resultados muestran que para la solicitud /Events/Index, 5% de las solicitudes se han respondido a en 2.44s menor que la mitad de ellos en 3.52s, y 5% son más lentas que 6.85s.

Calcular estadísticas varios:

    requests 
  	| summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>Percentiles ponderadas

Use las funciones de percentil ponderada en casos donde los datos se han agregado previamente. 

Por ejemplo, supongamos que su aplicación realiza muchos miles de operaciones por segundo y desea saber su latencia. La solución simple sería generar una solicitud de aplicación perspectivas o eventos personalizados para cada operación. Esto crearía una gran cantidad de tráfico, aunque muestreo adaptación tardarían efecto reducir. Pero decide implementar una solución aún mejor: escribirá código en su aplicación para agregar los datos antes de enviar a la información de la aplicación. Resumen agregado se enviarán a intervalos regulares, reducir la velocidad de datos quizás a algunos puntos por minuto.

El código toma una secuencia de medidas de latencia en milisegundos. Por ejemplo:
    
     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Cuenta las mediciones en las siguientes ubicaciones:`{ 10, 20, 30, 40, 50, 100 }`

Periódicamente, hace una serie de llamadas TrackEvent, uno para cada depósito con medidas personalizadas en cada llamada: 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

En análisis, verá un grupo de este tipo de eventos similar a esta:

`opCount` | `latency`| significado
---|---|---
8 | 10 | = 8 operaciones en la Papelera de 10 ms
6 | 20 | = 6 operaciones de la Papelera de 20 ms
3 | 30 | = las operaciones 3 en la Papelera de 30ms
1 | 40 | = las operaciones 1 en la Papelera de 40ms

Para obtener una imagen precisa de la distribución original de latencia de evento, usamos `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

Los resultados son los mismos usamos sin formato `percentiles` en el conjunto de medidas original.

> [AZURE.NOTE] Percentiles ponderadas no son aplicables a [muestrear datos](app-insights-sampling.md), donde cada fila muestra representa una muestra aleatoria de filas originales, en lugar de una Papelera. Las funciones de percentil sencilla son adecuadas para los datos de muestras.

#### <a name="estimation-error-in-percentiles"></a>Error de estimación en percentiles

El agregado de percentiles proporciona un valor aproximado mediante [T implícita](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

Algunos puntos importantes: 

* Los límites del error de estimación varían según el valor del percentil solicitado. La mejor precisión es en los extremos de 0 a [100] escalar, percentiles 0 y 100 son la exacta valores mínimos y máximos de la distribución. La precisión disminuye gradualmente hacia el centro de la escala. Es peor en la mediana y restringe al 1%. 
* Límites de error se observan en el rango en lugar del valor. Suponga percentil (X, 50) devuelve el valor de Xm. La estimación garantiza que al menos 49 y máximo 51% de los valores de X son inferiores a Xm. No hay ningún límite teórico en la diferencia entre Xm y real valor medio de X.

### <a name="stdev"></a>DESVEST

     stdev(Expr)

Devuelve la desviación estándar de *Expr* sobre el grupo.

### <a name="variance"></a>varianza

    variance(Expr)

Devuelve la varianza de *Expr* sobre el grupo.

### <a name="sum"></a>suma

    sum(Expr)

Devuelve la suma de la *expresión* en el grupo.                      


## <a name="scalars"></a>Escalares

[conversiones](#casts) | [comparaciones](#scalar-comparisons)
<br/>
[GetType](#gettype) | [hash](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

Los tipos compatibles son:

| Tipo      | Nombres adicionales   | Tipo de .NET equivalente |
| --------- | -------------------- | -------------------- |
| `bool`    | `boolean`            | `System.Boolean`     |
| `datetime`| `date`               | `System.DateTime`    |
| `dynamic` |                      | `System.Object`      |
| `guid`    | `uuid`, `uniqueid`   | `System.Guid`        |
| `int`     |                      | `System.Int32`       |
| `long`    |                      | `System.Int64`       |
| `double`  | `real`               | `System.Double`      |
| `string`  |                      | `System.String`      |
| `timespan`| `time`               | `System.TimeSpan`    |

### <a name="casts"></a>Conversiones

Puede convertir de un tipo a otro. En general, si tiene sentido en la conversión, funcionará:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Comprobar si una cadena se puede convertir a un tipo específico:

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>Comparaciones escalares

||
---|---
`<` |Menos
`<=`|Menor o igual a
`>` |Mayor
`>=`|Mayor o igual a
`<>`|No es igual a
`!=`|No es igual a 
`in`| Operando derecho es una matriz (dinámica) y operando izquierdo es igual a uno de sus elementos.
`!in`| Operando derecho es una matriz (dinámica) y operando izquierdo no es igual a cualquiera de sus elementos.




### <a name="gettype"></a>GetType

**Devuelve**

Una cadena que representa el tipo de almacenamiento subyacente del argumento único. Esto es especialmente útil cuando tiene valores de tipo `dynamic`: en este caso `gettype()` revelarán cómo se codifica un valor.

**Ejemplos**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

### <a name="hash"></a>hash

**Sintaxis**

    hash(source [, mod])

**Argumentos**

* *fuente*: el origen escalar se calcula el valor hash.
* *Mod*: el valor que se aplicará en el resultado de hash de módulo.

**Devuelve**

El valor de xxhash (long) de la escalar determinado, módulo del valor dado mod (si se especifica).

**Ejemplos**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>IFF

La `iff()` función evalúa el primer argumento (el predicado) y devuelve el valor de la segunda o la tercera argumentos dependiendo de si es el predicado `true` o `false`. Los argumentos segundo y terceros deben ser del mismo tipo.

**Sintaxis**

    iff(predicate, ifTrue, ifFalse)


**Argumentos**

* *predicado:* Una expresión que se evalúa como un `boolean` valor.
* *ifTrue:* Una expresión que se evalúa y su valor devuelven de la función si *predicado* se evalúa como `true`.
* *ifFalse:* Una expresión que se evalúa y su valor devuelven de la función si *predicado* se evalúa como `false`.

**Devuelve**

Esta función devuelve el valor de *ifTrue* si el *predicado* se evalúa como `true`, o bien el valor de *ifFalse* en caso contrario.

**Ejemplo**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### <a name="isnull-isnotnull-notnull"></a>IsNull, isnotnull, notnull

    isnull(parsejson("")) == true

Un argumento y le indica si es null.

**Sintaxis**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Devuelve**

Verdadero o falso dependiendo de si el valor es null o no es nulo.


|x|IsNull(x)
|---|---
| "" | falso
|"x" | falso
|parseJSON("")|Verdadero
|parseJSON("[]")|falso
|parseJSON("{}")|falso

**Ejemplo**

    T | where isnotnull(PossiblyNull) | count

Observe que existen otras formas de lograr este efecto:

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar

Evalúa una consulta o una expresión y devuelve el resultado como un valor único. Esta función es útil para los cálculos preconfigurados; Por ejemplo, para calcular un recuento total de eventos y eligiendo como una línea base.

**Sintaxis**

    toscalar(query)
    toscalar(scalar)

**Devuelve**

El argumento evaluado. Si el argumento es una tabla, devuelve la primera columna de la primera fila. (Buena práctica es organizar que el argumento tiene solo una columna y fila).

**Ejemplo**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
  	| extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>Literales booleanos

    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>Operadores booleanos

    and 
    or 

    

## <a name="numbers"></a>Números

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>Literales numéricos

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### <a name="arithmetic-operators"></a>Operadores aritméticos

|| |
|---|-------------|
| + | Agregar         |
| - | Restar    |
| * | Multiplicar    |
| / | Dividir      |
| % | Módulo      |
||
|`<` |Menos
|`<=`|Menor o igual a
|`>` |Mayor
|`>=`|Mayor o igual a
|`<>`|No es igual a
|`!=`|No es igual a 


### <a name="abs"></a>ABS

**Sintaxis**

    abs(x)

**Argumentos**

* x - un entero, real o intervalo de tiempo

**Devuelve**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### <a name="bin-floor"></a>Papelera, piso

Redondea valores hacia abajo hasta el entero múltiplo del tamaño de una ubicación determinada. Utilizar mucho en el [`summarize by`](#summarize-operator) consulta. Si tiene un conjunto de valores por, se agruparán en un conjunto de valores específicos menor.

Alias `floor`.

**Sintaxis**

     bin(value, roundTo)
     floor(value, roundTo)

**Argumentos**

* *valor:* Un número, una fecha o un intervalo de tiempo. 
* *roundTo:* "Tamaño de ubicación". Número, fecha o intervalo de tiempo que divide el *valor*. 

**Devuelve**

Al múltiplo más próximo de *roundTo* por debajo de *valor*.  
 
    (toint((value/roundTo)-0.5)) * roundTo

**Ejemplos**

Expresión | Resultado
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`|  `datetime(1953-04-15)`


La siguiente expresión calcula un histograma de duraciones, con un tamaño de 1 segundo período:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>EXP

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>piso

Un alias para [`bin()`](#bin).

### <a name="gamma"></a>gamma

La [función gamma](https://en.wikipedia.org/wiki/Gamma_function)

**Sintaxis**

    gamma(x)

**Argumentos**

* *x:* Un número real

Para enteros positivos, `gamma(x) == (x-1)!` por ejemplo, `gamma(5) == 4 * 3 * 2 * 1`.

Vea también [loggamma](#loggamma).


### <a name="log"></a>registro

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v`debe ser un número real > 0. En caso contrario, se devuelve null.

### <a name="loggamma"></a>loggamma


El logaritmo natural del valor absoluto de la [función gamma](#gamma).

**Sintaxis**

    loggamma(x)

**Argumentos**

* *x:* Un número real


### <a name="rand"></a>aleatorio

Generador de números aleatorios.

* `rand()`-un número real entre 0.0 y 1.0
* `rand(n)`-un número entero entre 0 y n-1




### <a name="sqrt"></a>SQRT

La función raíz cuadrada.  

**Sintaxis**

    sqrt(x)

**Argumentos**

* *x:* Un número real > = 0.

**Devuelve**

* Un número positivo que`sqrt(x) * sqrt(x) == x`
* `null`Si el argumento es negativo, o no se puede convertir a un `real` valor. 




### <a name="toint"></a>ToInt

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### <a name="tolong"></a>tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### <a name="todouble"></a>ToDouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## <a name="date-and-time"></a>Fecha y hora


[hace](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [ahora](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>Literales de fecha y hora

|||
---|---
**fecha y hora**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Horas siempre están en UTC. Si se omite la fecha ofrece un tiempo hoy.
`now()`|La hora actual.
`now(`-*intervalo de tiempo*`)`|`now()-`*intervalo de tiempo*
`ago(`*intervalo de tiempo*`)`|`now()-`*intervalo de tiempo*
**intervalo de tiempo**|
`2d`|2 días
`1.5h`|1,5 horas 
`30m`|30 minutos
`10s`|10 segundos
`0.1s`|0,1 segundos
`100ms`| 100 milisegundos
`10microsecond`|
`1tick`|100ns
`time("15 seconds")`|
`time("2")`| 2 días
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### <a name="date-and-time-expressions"></a>Expresiones de fecha y hora

Expresión |Resultado
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Menos
`<=`|Menor o igual a
`>` |Mayor
`>=`|Mayor o igual a
`<>`|No es igual a
`!=`|No es igual a 




### <a name="ago"></a>hace

Resta el intervalo de tiempo determinado de la hora UTC actual. Como `now()`, esta función se puede usar varias veces en una instrucción y la hora del reloj de UTC que se hace referencia será el mismo para todas las instancias.

**Sintaxis**

    ago(a_timespan)

**Argumentos**

* *a_timespan*: intervalo para restar la hora UTC actual (`now()`).

**Devuelve**

    now() - a_timespan

**Ejemplo**

Todas las filas con una marca de tiempo en la última hora:

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>DatePart

    datepart("Day", datetime(2015-12-14)) == 14

Extrae una parte especificada de una fecha como un número entero.

**Sintaxis**

    datepart(part, datetime)

**Argumentos**

* `part:String`-{"Año", "Mes", "Día", "Hora", "Minutos", "Segundo", "Milisegundos", "Microsegundo", "Nanosegundos"}
* `datetime`

**Devuelve**

Entero largo que representa el elemento especificado.


### <a name="dayofmonth"></a>DayOfMonth

    dayofmonth(datetime("2016-05-15")) == 15 

El número ordinal del día del mes.

**Sintaxis**

    dayofmonth(a_date)

**Argumentos**

* `a_date`: A `datetime`.


### <a name="dayofweek"></a>DayOfWeek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

El número entero de días desde la anterior el domingo, como un `timespan`.

**Sintaxis**

    dayofweek(a_date)

**Argumentos**

* `a_date`: A `datetime`.

**Devuelve**

La `timespan` desde medianoche al principio de la anterior el domingo, redondea un número entero de días.

**Ejemplos**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>DAYOFYEAR

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

El número ordinal del día del año.

**Sintaxis**

    dayofyear(a_date)

**Argumentos**

* `a_date`: A `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getMonth

Obtener el número de mes (1-12) de un valor datetime.

**Ejemplo**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getYear

Obtener el año de una fecha y hora.

**Ejemplo**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>Ahora

    now()
    now(-2d)

Opcionalmente, la hora UTC actual, desplazamiento por un intervalo de tiempo determinado. Esta función se puede usar varias veces en una instrucción y la hora que se hace referencia será el mismo para todas las instancias.

**Sintaxis**

    now([offset])

**Argumentos**

* *offset:* A `timespan`, agregada a la hora UTC actual. Valor predeterminado: 0.

**Devuelve**

La hora actual del reloj UTC como un `datetime`.

    now() + offset

**Ejemplo**

Determina el intervalo desde el evento identificado por el predicado:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>
### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>ToDateTime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Comprobar si una cadena es una fecha válida:

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>ToTimeSpan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>WeekOfYear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

El resultado entero representa el número de semana por el estándar ISO 8601. El primer día de la semana es el domingo y la primera semana del año es la semana que contiene el primer jueves del mismo año. (Los últimos días de un año, por tanto, pueden contener algunos de los días de la semana 1 del año siguiente, o pueden contener los días de la primera parte de la semana 52 o 53 del año anterior).


## <a name="string"></a>Cadena

[countof](#countof) | [extraer](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [Reemplazar](#replace) | [dividir](#split) | [strcat](#strcat) | [strlen](#strlen) | [subcadena](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### <a name="string-literals"></a>Literales de cadena

Las reglas son los mismos que JavaScript.

Pueden estar entre cadenas en único o caracteres de comillas dobles. 

Barra diagonal inversa (`\`) se usa para caracteres de escape como `\t` (ficha) `\n` (nueva línea) y las instancias de la oferta caracteres de cierre.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>Literales de cadena confusa

Literales de cadena confusa son cadenas que se oculte el análisis al generar la cadena (por ejemplo, cuando el seguimiento). El proceso de ocultación reemplaza confusas todos los caracteres a iniciar (`*`) caracteres.

Para formar una cadena confusa literal, anteponga `h` o 'H'. Por ejemplo:

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>Comparaciones de cadenas

Operador|Descripción|Distingue mayúsculas de minúsculas|Ejemplo de true
---|---|---|---
`==`|Es igual a |Sí| `"aBc" == "aBc"`
`<>` `!=`|No es igual a|Sí| `"abc" <> "ABC"`
`=~`|Es igual a |No| `"abc" =~ "ABC"`
`!~`|No es igual a |No| `"aBc" !~ "xyz"`
`has`|Right lado (RHS) es un término todo en izquierda lado (LHS)|No| `"North America" has "america"`
`!has`|RHS no es un término completo en LHS|No|`"North America" !has "amer"` 
`hasprefix`|RHS es un prefijo de un término en LHS|No|`"North America" hasprefix "ame"`
`!hasprefix`|RHS no es un prefijo de cualquier término en LHS|No|`"North America" !hasprefix "mer"`
`hassuffix`|RHS es un sufijo de un término en LHS|No|`"North America" hassuffix "rth"`
`!hassuffix`|RHS no es un sufijo de cualquier término en LHS|No|`"North America" !hassuffix "mer"`
`contains` | RHS se produce como una subcadena de LHS|No| `"FabriKam" contains "BRik"`
`!contains`| RHS no se produce en LHS|No| `"Fabrikam" !contains "xyz"`
`containscs` | RHS se produce como una subcadena de LHS|Sí| `"FabriKam" contains "Kam"`
`!containscs`| RHS no se produce en LHS|Sí| `"Fabrikam" !contains "Kam"`
`startswith`|RHS es una subcadena inicial de LHS.|No|`"Fabrikam" startswith "fab"`
`!startswith`|RHS no es una subcadena inicial de LHS.|No|`"Fabrikam" !startswith "abr"`
`endswith`|RHS es una subcadena terminal de LHS.|No|`"Fabrikam" endswith "kam"`
`!endswith`|RHS no es una subcadena terminal de LHS.|No|`"Fabrikam" !endswith "ka"`
`matches regex`|LHS contiene a una coincidencia con RHS|Sí| `"Fabrikam" matches regex "b.*k"`
`in`|Es igual a cualquiera de los elementos|Sí|`"abc" in ("123", "345", "abc")`
`!in`|No es igual a cualquiera de los elementos|Sí|`"bc" !in ("123", "345", "abc")`

Usar `has` o `in` si está probando la presencia de un término completamente léxico - es decir, un símbolo o una palabra alfanumérica delimitado por caracteres no alfanuméricos o inicio o final del campo. `has`realiza más rápido que `contains`, `startswith` o `endswith`. La primera de estas consultas se ejecuta más rápidamente:

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Cuenta las apariciones de una subcadena en una cadena. Coincidencias de cadena sin formato que se superponen; coincidencias Regex no.

**Sintaxis**

    countof(text, search [, kind])

**Argumentos**

* *texto:* Una cadena.
* *búsqueda:* La cadena sin formato o expresión regular para que coincida con dentro de *texto*.
* *tipo:* `"normal"|"regex"` Predeterminado `normal`. 

**Devuelve**

El número de veces que puede coincidir la cadena de búsqueda en el contenedor. Coincidencias de cadena sin formato que se superponen; coincidencias Regex no.

**Ejemplos**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (no 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### <a name="extract"></a>extraer

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Obtener a una coincidencia con una [expresión regular](#regular-expressions) de una cadena de texto. Si lo desea, a continuación, convierte la subcadena extraída al tipo indicado.

**Sintaxis**

    extract(regex, captureGroup, text [, typeLiteral])

**Argumentos**

* *regex:* Una [expresión regular](#regular-expressions).
* *captureGroup:* Un positivo `int` constante que indica el grupo de captura para extraer. 0 significa que toda la coincidencia, 1 para el valor coincide con la primera ('paréntesis')' en la expresión regular, 2 o más de las siguientes paréntesis.
* *texto:* A `string` para buscar.
* *typeLiteral:* Un literal de tipo opcional (por ejemplo, `typeof(long)`). Si se proporciona, la subcadena extraída se convierte a este tipo. 

**Devuelve**

Si *regex* encuentra una coincidencia de *texto*: la subcadena que coincide con el grupo de captura indicada *captureGroup*, de manera opcional se convierten en *typeLiteral*.

Si no hay ninguna coincidencia o se produce un error en la conversión de tipo: `null`. 

**Ejemplos**

La cadena de ejemplo `Trace` se busca una definición para `Duration`. La coincidencia se convierte en `real`, luego multiplicado por una constante de tiempo (`1s`) para que `Duration` es de tipo `timespan`. En este ejemplo, es igual a 123.45 segundos:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

En este ejemplo es equivalente a `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>
### <a name="isempty-isnotempty-notempty"></a>EsVacío, isnotempty, notempty

    isempty("") == true

True si el argumento es una cadena vacía o es nulo.
Vea también [isnull](#isnull).


**Sintaxis**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Devuelve**

Indica si el argumento es una cadena vacía o isnull.

|x|IsEmpty(x)
|---|---
| "" | Verdadero
|"x" | falso
|parseJSON("")|Verdadero
|parseJSON("[]")|falso
|parseJSON("{}")|falso


**Ejemplo**


    T | where isempty(fieldName) | count


### <a name="parseurl"></a>ParseURL

Dividir una dirección URL en sus partes.

**Sintaxis**

    parseurl(urlstring)

**Argumentos**

* *urlstring:* UNA DIRECCIÓN URL.

**Devuelve**

Un objeto que contiene los elementos como cadenas.

**Ejemplo**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>reemplazar

Reemplazar todas las coincidencias de regex con otra cadena.

**Sintaxis**

    replace(regex, rewrite, text)

**Argumentos**

* *regex:* La [expresión regular](https://github.com/google/re2/wiki/Syntax) para buscar *texto*. Puede contener grupos de captura paréntesis' (' ')'. 
* *reescritura:* Regex de reemplazo para cualquier coincidencia realizado por *matchingRegex*. Usar `\0` para hacer referencia a toda la coincidencia, `\1` para el primer grupo de captura, `\2` y así sucesivamente para los grupos de captura subsiguientes.
* *texto:* Una cadena.

**Devuelve**

*texto* después de reemplazar a todas las coincidencias de *regex* con evaluaciones de *reescritura*. No se superpone a coincidencias.

**Ejemplo**

Esta declaración:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Tiene los siguientes resultados:

| x    | str | reemplazar|
|---|---|---|
| 1    | Número es y 1.000000  | El número de era: y 1.000000|
| 2    | Número es 2.000000  | El número de era: 2.000000|
| 3    | Número es 3.000000  | El número de era: 3.000000|
| 4    | Número es 4.000000  | El número de era: 4.000000|
| 5    | Número es 5.000000  | El número de era: 5.000000|
 



### <a name="split"></a>dividir

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Divide una cadena dada según un delimitador determinado y devuelve una matriz de cadena con las subcadenas contenidas. Si lo desea, puede ser una subcadena concreta devuelve si existe.

**Sintaxis**

    split(source, delimiter [, requestedIndex])

**Argumentos**

* *origen*: la cadena de origen que se va a dividir según el delimitador determinado.
* *delimitador*: delimitador que se usará para dividir la cadena de origen.
* *requestedIndex*: un índice de base cero opcional `int`. Si ha indicado, la matriz de la cadena devuelta contendrá la subcadena solicitada si existe. 

**Devuelve**

Una matriz de cadena que contiene las subcadenas de la cadena de origen dado que están delimitadas por el delimitador especificado.

**Ejemplos**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat

    strcat("hello", " ", "world")

Concatena entre 1 y 16 argumentos, que deben ser cadenas.

### <a name="strlen"></a>strlen

    strlen("hello") == 5

Longitud de una cadena.

### <a name="substring"></a>subcadena

    substring("abcdefg", 1, 2) == "bc"

Extraer una subcadena de una cadena de origen determinado a partir de un índice determinado. Si lo desea, puede especificar la longitud de la subcadena solicitada.

**Sintaxis**

    substring(source, startingIndex [, length])

**Argumentos**

* *origen:* La cadena de origen que se obtiene la subcadena del.
* *startingIndex:* La base cero posición del carácter inicial de la subcadena solicitada.
* *longitud:* Un parámetro opcional que se pueden usar para especificar el número de caracteres solicitado en la subcadena. 

**Devuelve**

Una subcadena de la cadena especificada. La subcadena comienza en la posición del carácter startingIndex (basado en cero) y continúa hasta el final de la cadena o longitud caracteres si especificado.

**Ejemplos**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>ToLower

    tolower("HELLO") == "hello"

Convierte una cadena a minúsculas.

### <a name="toupper"></a>ToUpper

    toupper("hello") == "HELLO"

Convierte una cadena en mayúsculas.



### <a name="guids"></a>GUID

    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>Matrices, objetos y dinámicas

[literales](#dynamic-literals) | [conversión](#casting-dynamic-objects) | [operadores](#operators) | [le permiten cláusulas](#dynamic-objects-in-let-clauses)
<br/>
[ArrayLength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [rango](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)


Este es el resultado de una consulta en una excepción de aplicación perspectivas. El valor de `details` es una matriz.

![](./media/app-insights-analytics-reference/310.png)

**Indización:** Índice de matrices y objetos al igual que en JavaScript:

    exceptions | take 1
  	| extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Pero usar `arraylength` y otras funciones de análisis (no ".length"!)

**Conversión** En algunos casos es necesario convertir un elemento que extraer un objeto, como su tipo podría variar. Por ejemplo, `summarize...to` necesita un tipo específico:

    exceptions 
  	| summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
  	| summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Literales** Para crear una matriz explícita o un objeto de bolsa de propiedades, escribir como una cadena JSON y conversión:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** Para desarmar las propiedades de un objeto en filas separadas, use mvexpand:

    exceptions | take 1 
  	| mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)


**treepath:** Para buscar todas las rutas de acceso en un objeto complejo:

    exceptions | take 1 | project timestamp, details 
  	| extend path = treepath(details) 
  	| mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema:** Para buscar el esquema mínimo que admite todos los valores de la expresión en la tabla:

    exceptions | summarize buildschema(details)

Resultado:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Observe que `indexer` se usa para marcar dónde debe usar un índice numérico. Para este esquema, algunas rutas de acceso válidas podría ser (suponiendo que estos índices de ejemplo se encuentran en el intervalo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>Matriz y objeto literales

Para crear un literal dinámico, use `parsejson` (alias `todynamic`) con un JSON cadena argumento:

* `parsejson('[43, 21, 65]')`-una matriz de números
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`-un único valor del tipo dinámico que contiene un número
* `parsejson('"21"')`-un único valor del tipo dinámico que contiene una cadena

Nota que, a diferencia de JavaScript, JSON exige el uso de comillas dobles (`"`) alrededor de cadenas. Por lo tanto, normalmente es más fácil presupuesto una cadena JSON codificada literales con comillas (`'`).

Este ejemplo crea un valor dinámico y, a continuación, usa sus campos:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>Funciones de objeto dinámico

|||
|---|---|
| *valor* `in` *matriz*| True si hay un elemento de *matriz* que == *valor*<br/>`where City in ('London', 'Paris', 'Rome')`
| *valor* `!in` *matriz*| True si no hay ningún elemento de *matriz* que == *valor*
|[`arraylength(`matriz`)`](#arraylength)| Null si no es una matriz.
|[`extractjson(`ruta de acceso, objeto`)`](#extractjson)|Ruta de acceso se utiliza para navegar en objeto.
|[`parsejson(`origen`)`](#parsejson)| Convierte una cadena JSON en un objeto dinámico.
|[`range(`desde, paso`)`](#range)| Matriz de valores
|[`mvexpand`listColumn](#mvexpand-operator) | Aplica una fila por cada valor en una lista en una celda especificada.
|[`summarize buildschema(`columna`)`](#buildschema) |Deduce el esquema de tipo de contenido de columna
|[`summarize makelist(`columna`)`](#makelist)| Acopla grupos de filas y coloca los valores de la columna de una matriz.
|[`summarize makeset(`columna`)`](#makeset) | Acopla grupos de filas y coloca los valores de la columna en una matriz, sin duplicados.

### <a name="dynamic-objects-in-let-clauses"></a>Objetos dinámicos en cláusulas permiten


[Permitir que las cláusulas](#let-clause) almacén dinámicos valores como cadenas, así que estos dos cláusulas son equivalentes y ambas necesitan la `parsejson` (o `todynamic`) antes de ser usada:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### <a name="arraylength"></a>ArrayLength

El número de elementos de una matriz dinámica.

**Sintaxis**

    arraylength(array)

**Argumentos**

* *matriz:* A `dynamic` valor.

**Devuelve**

El número de elementos de *matriz*, o `null` si *la matriz* no es una matriz.

**Ejemplos**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Obtener un elemento especificado fuera de un texto JSON mediante una expresión de ruta de acceso. Si lo desea convertir la cadena extraída a un tipo específico.


**Sintaxis**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**Devuelve**

Esta función realiza una consulta de JsonPath en origen de datos que contiene una cadena JSON válida, opcionalmente, convertir ese valor en otro tipo según el tercer argumento.



**Ejemplo**

La notación de [corchetes] y notación de puntos son equivalentes:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Sugerencias de rendimiento**

* Aplicar cláusulas where antes de utilizar`extractjson()`
* Considere la posibilidad de usar una coincidencia de expresión regular con [extraer](#extract) en su lugar. Esto se ejecuta mucho más rápidamente y entra en vigor si se produce la JSON desde una plantilla.
* Usar `parsejson()` si necesita extraer más de un valor de la JSON.
* Considere la posibilidad de tener la JSON analizado en recopilación al declarar el tipo de la columna sean dinámicos.

### <a name="json-path-expressions"></a>Expresiones de ruta de acceso de JSON

|||
|---|---|
|`$`|Objeto raíz|
|`@`|Objeto actual|
|`[0]`|Subíndice de matriz|
|`.`o`[0]` | Elemento secundario|

*(Se actualmente no implementan caracteres comodín, recursividad, unión o sectores.)*




### <a name="parsejson"></a>parseJSON

Interpreta un `string` como un [valor JSON](http://json.org/)) y devuelve el valor como `dynamic`. Es superior a usar `extractjson()` cuando necesita extraer más de un elemento de un objeto compuesto de JSON.

**Sintaxis**

    parsejson(json)

**Argumentos**

* *json:* Un documento JSON.

**Devuelve**

Un objeto de tipo `dynamic` especificado por *json*.

**Ejemplo**

En el ejemplo siguiente, cuando `context_custom_metrics` es una `string` que tiene este aspecto: 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

a continuación, el siguiente fragmento recupera el valor de la `duration` franja en el objeto y de que TI recupera dos ranuras `duration.value` y  `duration.min` (`118.0` y `110.0`, respectivamente).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>rango

La `range()` función (no a confundirse con la `range` operador) genera una matriz dinámica contiene una serie de valores espaciados igualmente.

**Sintaxis**

    range(start, stop, step)

**Argumentos**

* *Inicio:* El valor del primer elemento de la matriz resultante. 
* *Detener:* El valor del último elemento de la matriz resultante o el menor valor mayor que el último elemento de la matriz resultante y dentro de un número entero múltiplo de *paso* de *Inicio*.
* *paso:* La diferencia entre dos elementos consecutivos de la matriz.

**Ejemplos**

En el ejemplo siguiente se devuelve `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

En el ejemplo siguiente se devuelve una matriz que contiene todos los días en el año 2015:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Convierte una cadena en un valor dinámico.

### <a name="treepath"></a>TreePath

    treepath(dynamic_object)

Enumera todas las expresiones de ruta de acceso para identificar un objeto dinámico de hojas. 

**Devuelve**

Matriz de expresiones de ruta de acceso.

**Ejemplos**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Nota que "[0]" indica la presencia de una matriz, pero no especifica el índice de la ruta de acceso específica.

### <a name="zip"></a>ZIP

    zip(list1, list2, ...)

Combina un conjunto de listas en una lista de tuplas.

* `list1...`: Una lista de valores

**Ejemplos**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]

    
    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>Nombres

Nombres pueden tener hasta 1024 caracteres. Distinguen mayúsculas de minúsculas y pueden contener letras, dígitos y caracteres de subrayado (`_`). 

Presupuesto un nombre mediante ['... '] or [" ... "] para incluir otros caracteres o usar una palabra clave como un nombre. Por ejemplo:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|[' ruta\\archivo\'x\''] | Usar \ caracteres de escape|
|["d-e.=/f#\n"] | |
|[@'path\file'] | Sin caracteres de escape - \ es literal|
|[@"\now& luego\"] | |
|[donde] | Uso de una palabra clave como un nombre|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


