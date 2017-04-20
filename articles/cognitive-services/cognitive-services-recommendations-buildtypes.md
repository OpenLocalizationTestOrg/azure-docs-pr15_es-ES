<properties
    pageTitle="Tipos de generación y la calidad de modelo: recomendaciones API | Microsoft Azure"
    description="Azure máquina recomendaciones de aprendizaje: Guía de inicio rápido"
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
    ms.date="09/20/2016"
    ms.author="luisca"/>

#  <a name="build-types-and-model-quality"></a>Tipos de generación y la calidad de modelo #

<a name="TypeofBuilds"></a>
## <a name="supported-build-types"></a>Tipos de generación compatibles ##

La API de recomendaciones actualmente admite dos tipos de compilación: *recomendación* y *FBT*. Cada uno de ellos se ha creado con diferentes algoritmos y cada una tiene desventajas. Este documento describe cada una de estas versiones y técnicas para comparar la calidad de los modelos generados.

Si no lo ha hecho ya, se recomienda que complete la [Guía de inicio rápido](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### <a name="recommendation-build-type"></a>Tipo de generación de recomendación ###

El tipo de generación de recomendación usa descomposición en factores matriz para proporcionar recomendaciones. Genera vectores [característica latencia](https://en.wikipedia.org/wiki/Latent_variable) en función de las transacciones para describir cada elemento y, a continuación, utiliza esos vectores latencia para comparar los elementos que son similares.

Si entrenar el modelo en función de las compras realizadas en la tienda de electrónica y proporcionar un teléfono 650 Lumia como entrada para el modelo, el modelo devolverá un conjunto de elementos que suelen comprar por personas que están probable que un teléfono 650 Lumia de compra. Los elementos no pueden ser complementarios. En este ejemplo, es posible que el modelo devolverá otros teléfonos porque personas que le gusta el 650 Lumia les gusta otros teléfonos.

La generación de recomendación tiene dos funciones que hacen que sea atractivo:

* *Es compatible con la compilación de recomendación *fría elemento* colocación **

Elementos que no tienen uso significativa se denominan elementos frías. Por ejemplo, si recibe un envío de un teléfono que nunca ha vendido antes, el sistema no puede inferir recomendaciones para este producto en transacciones únicamente. Esto significa que el sistema debe aprender de información sobre el producto.

Si desea usar el elemento fría colocación, debe proporcionar información de características para cada uno de los elementos del catálogo. A continuación se muestra las primeras líneas del catálogo podrán aspecto (formato de nota clave = valor de las características).

>Pro2 6CX 00001, superficie, superficie, escriba = Hardware, almacenamiento = memoria de 128 GB = 4G, fabricante = Microsoft

>H 73-00013, reactivación Xbox 360, juegos,, tipo = Software, idioma = inglés, valoración = para adultos

>WAH 0F05, Minecraft Xbox 360, juegos,, * tipo = Software, idioma = español, valoración = juventud

También debe configurar los siguientes parámetros de compilación:

| Crear parámetro         | Notas
|------------------     |-----------
|*useFeaturesInModel*     | Se establece en **true**.  Indica si se pueden usar características para mejorar el modelo de recomendación.
|*allowColdItemPlacement*   | Se establece en **true**. Indica si la recomendación también debe push frías elementos a través de semejanza de la característica.
| *modelingFeatureList*   | Lista de valores separados por comas de nombres de características que se utilizará en la generación de recomendación para mejorar la recomendación. Por ejemplo, "idioma, almacenamiento" en el ejemplo anterior.

**La generación de recomendación admite recomendaciones de usuario**

Una compilación de recomendación es compatible con [recomendaciones de usuario](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Esto significa que puede proporcionar recomendaciones personalizadas para los usuarios según sus historiales de transacciones. Para obtener recomendaciones de usuario, puede proporcionar el identificador de usuario o el historial reciente de transacciones para ese usuario.

Uno es un ejemplo clásico de donde desea aplicar recomendaciones de usuario al iniciar sesión en la página principal. Se puede aumentar el nivel de contenido que se aplica al usuario específico.

También puede aplicar un tipo de generación de recomendaciones cuando el usuario va a desproteger. En este momento, tiene la lista de elementos que el usuario está a punto de compra y puede proporcionar recomendaciones basadas en la cesta actual.

#### <a name="recommendations-build-parameters"></a>Parámetros de compilación de recomendaciones

| Nombre  |   Descripción |    Tipo, <br>  valores válidos <br> (valor predeterminado)
|-------|-------------------|------------------
| *NumberOfModelIterations* |   El número de iteraciones que realiza el modelo se refleja en el tiempo de cálculo total y la precisión del modelo. Cuanto mayor sea el número, más precisa del modelo, pero el tiempo de cálculo tarda más.  |   Número entero, <br>  10 a 50 <br>(40)
| *NumberOfModelDimensions* |   El número de dimensiones está relacionado con el número de características que el modelo intentará buscar dentro de los datos. Aumentar el número de dimensiones le permitirá mejor ajuste de los resultados en clústeres más pequeños. Sin embargo, demasiadas dimensiones impedirá el modelo de búsqueda de correlación entre los elementos. |  Número entero, <br> 10 a 40 <br>(20) |
| *ItemCutOffLowerBound* |  Define el número mínimo de puntos de uso que debe ser un elemento en para que se considere parte del modelo. |        Número entero, <br> 2 o más <br> (2) |
| *ItemCutOffUpperBound* |  Define el número máximo de puntos de uso que debe ser un elemento en para que se considere parte del modelo. |  Número entero, <br>2 o más<br> (2147483647) |
|*UserCutOffLowerBound* |   Define el número mínimo de las transacciones de que un usuario debe haber realizado para considerarse parte del modelo. | Número entero, <br> 2 o más <br> (2)
| *UserCutOffUpperBound* |  Define el número máximo de transacciones de que un usuario debe haber realizado para considerarse parte del modelo. | Número entero, <br>2 o más <br> (2147483647)|
| *UseFeaturesInModel* |    Indica si se pueden usar características para mejorar el modelo de recomendación. |     Valor booleano<br> Valor predeterminado: True
|*ModelingFeatureList* |    Lista de valores separados por comas de nombres de características que se utilizará en la generación de recomendación para mejorar la recomendación. Depende de las características que son importantes. |    Cadena hasta 512 caracteres
| *AllowColdItemPlacement* |    Indica si la recomendación también debe push frías elementos a través de semejanza de la característica. | Valor booleano <br> Valor predeterminado: False
| *EnableFeatureCorrelation*    | Indica si se pueden usar características de razonamiento. | Valor booleano <br> Valor predeterminado: False
| *ReasoningFeatureList* |  Lista de valores separados por comas de nombres de características que se usará para razonamiento oraciones, como explicaciones de recomendación. Depende de las características que son importantes para los clientes. | Cadena hasta 512 caracteres
| *EnableU2I* | Habilitar recomendaciones personalizadas, también denominadas usuario para elemento recomendaciones (U2I). | Valor booleano <br>Valor predeterminado: True
|*EnableModelingInsights* | Define si se debe realizar la evaluación sin conexión para recopilar modelado perspectivas (es decir, precisión y diversidad métricas). Si no se utilizará para formación porque se necesita estar reservada para realizar pruebas del modelo de valor es true, un subconjunto de los datos. Más información sobre [evaluaciones sin conexión](#OfflineEvaluation). | Valor booleano <br> Valor predeterminado: False
| *SplitterStrategy* | Si Habilitar modelado perspectivas está establecida en *true*, es cómo se deben dividir datos fines de evaluación.  | Cadena, *RandomSplitter* o *LastEventSplitter* <br>Predeterminado: RandomSplitter


<a name="FBTBuild"></a>
### <a name="fbt-build-type"></a>Tipo de generación FBT ###

La generación de (FBT) junta con frecuencia comprada hace un análisis que cuenta el número de veces dos o tres productos diferentes conjunta aparecen juntas. A continuación, ordena los conjuntos basados en una función de semejanza (**con repeticiones**, **Jaccard**, **levantar**).

Piense **Jaccard** y **levantar** como formas que desea normalizar las apariciones de compañeros.  Esto significa que los elementos se devolverán solo si se donde compró junto con el elemento de valor de inicialización.

En nuestro ejemplo de teléfono 650 Lumia, teléfono X se devolverá solo si se compró teléfono X en la misma sesión como el teléfono 650 Lumia. Ya sea poco probable, se esperaría elementos complementaria a la 650 Lumia va a devolver; Por ejemplo, un protector de pantalla o un adaptador de alimentación para el 650 Lumia.

Actualmente, se supone que dos elementos se compró en la misma sesión si se producen en una transacción con el mismo identificador de usuario y la marca de tiempo.

Versiones FBT no admiten elementos fríos, porque por definición que esperan dos elementos comprar en la misma transacción. Mientras FBT versiones pueden devolver conjuntos de elementos (tres dígitos), no admiten recomendaciones personalizadas porque aceptan un elemento de un valor de inicialización único como entrada.


#### <a name="fbt-build-parameters"></a>Parámetros de compilación FBT

| Nombre  |   Descripción |       Tipo,  <br> valores válidos <br> (valor predeterminado)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | ¿Cómo conservador el modelo es. Número de apariciones de compañeros de elementos para tener en cuenta para un modelado. |  Número entero, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Limita el número de elementos en un conjunto de frecuencia.| Entero  <br> 2-3 <br> (2)
| *FbtMinimalScore* | Puntuación mínima que debe tener un conjunto frecuentes que se incluirán en los resultados devueltos. Cuanto mayor sea el mejor. | Doble <br> 0 y anteriores <br> (0)
| *FbtSimilarityFunction* | Define la función semejanza para usarlo con la compilación. **Levantar** elige serendipity, **la repetición** elige predicción y **Jaccard** es un compromiso entre los dos. | Cadena,  <br>  <i>cooccurrence, elevación, jaccard</i><br> Valor predeterminado: <i>jaccard</i>

<a name="SelectBuild"></a>
## <a name="build-evaluation-and-selection"></a>Evaluación de generación y selección ##

Esta guía puede ayudarle a determinar si debe usar un recomendaciones o una compilación FBT, pero no proporciona una respuesta definitiva en casos donde puede utilizar cualquiera de ellas. Además, incluso si sabe que va a usar un tipo de generación FBT, podría aún desea elegir **Jaccard** o **levantar** como la función semejanza.

Es la mejor manera de elegir entre dos versiones diferentes probarlas en el mundo real (evaluación en línea) y realizar el seguimiento de una tasa de conversión para las distintas versiones. El tipo de conversión podría se mide en función de clics de recomendación, el número real compras de recomendaciones que se muestra, o incluso de los importes de compra real cuando se muestran las recomendaciones diferentes. Puede seleccionar la métrica de tasa de conversión en función de su objetivo empresarial.

En algunos casos, desea evaluar el modelo sin conexión antes de poner en producción. Mientras evaluación sin conexión no es un reemplazo de evaluación en línea, puede servir como una métrica.

<a name="OfflineEvaluation"></a>
## <a name="offline-evaluation"></a>Evaluación sin conexión  ##

El objetivo de una evaluación sin conexión es predecir la diversidad de recomendaciones (el número de elementos que se recomienda) y la precisión (el número de usuarios que va a adquirir uno de los elementos recomendados).
Como parte de la evaluación de métricas precisión y diversidad, el sistema busca una muestra de los usuarios y divide las transacciones para esos usuarios en dos grupos: el conjunto de datos de aprendizaje y el conjunto de datos de prueba.

> [AZURE.NOTE]Para usar métricas sin conexión, debe tener las marcas de tiempo en los datos de uso.
> Datos de tiempo son necesarios dividir uso correctamente entre conjuntos de datos de entrenamiento y la prueba.

> Además, evaluación sin conexión no puede producir resultados para los archivos de uso pequeña. Para que la evaluación se completa, debe ser un mínimo de 1.000 puntos de uso en el conjunto de datos de prueba.

<a name="Precision"></a>
### <a name="precision-at-k"></a>Precisión en k ###
En la tabla siguiente representa la salida de la evaluación de precisión en k sin conexión.

| K | 1 | 2 | 3 |   4 |     5
|---|---|---|---|---|---|
|Porcentaje |   13,75 | 18.04   | 21 |  24.31 | 26.61
|Usuarios de prueba |    10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Usuarios que se consideran | 10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Usuarios no tienen en cuenta | 0 | 0 | 0 | 0 | 0

#### <a name="k"></a>K
En la tabla anterior, *k* representa el número de recomendaciones que se muestra al cliente. La tabla se lee como sigue: "Si durante el período de prueba, solo recomendación se muestra a los clientes, 13,75 solo los usuarios habría comprado esa recomendación". Esta declaración se basa en el supuesto de que el modelo se formación con datos de compra. Otra forma de decir esto es la precisión en 1 13,75.

Observe que mientras más elementos se muestran al cliente, la probabilidad de que el cliente comprar un artículo recomendado sube. Para la misma anterior, la probabilidad casi se duplica a 26.61 porcentaje cuando se recomiendan 5 elementos.

#### <a name="percentage"></a>Porcentaje
Se muestra el porcentaje de usuarios que interactúe con al menos una de las recomendaciones *k* . El porcentaje se calcula dividiendo el número de usuarios que interactúe con al menos una recomendación por el número total de usuarios que se considera. Consulte los usuarios que se consideran para obtener más información.

#### <a name="users-in-test"></a>Usuarios de prueba
Datos de esta fila representan el número total de usuarios en el conjunto de datos de prueba.

#### <a name="users-considered"></a>Usuarios que se consideran
Solo se considera un usuario si el sistema se recomienda al menos elementos *k* basan en el modelo generado con el conjunto de datos de aprendizaje.

#### <a name="users-not-considered"></a>Usuarios no tienen en cuenta
Datos de esta fila representan todos los usuarios no tienen en cuenta. Los usuarios que no se ha recibido al menos *k* elementos recomendados.

Usuario que no tienen en cuenta = los usuarios de prueba: se consideran que los usuarios

<a name="Diversity"></a>
### <a name="diversity"></a>Diversidad ###
Métricas diversidad medir el tipo de elementos que se recomienda. En la tabla siguiente representa la salida de la evaluación de la diversidad sin conexión.

|Cubo de percentil |    0-90|  90-99| 99-100
|------------------|--------|-------|---------
|Porcentaje        | 34.258 | 55.127| 10.615


Total de elementos que se recomienda: 100,000

Elementos únicos recomendados: 954

#### <a name="percentile-buckets"></a>Percentil depósitos
Cada depósito de percentil está representado por un intervalo (valores mínimos y máximos comprendidos entre 0 y 100). Los elementos superiores a 100 son los elementos más populares y los elementos próximo a 0 son menos populares. Por ejemplo, si el valor de porcentaje para el período de percentil 99-100 es 10.6, significa que 10.6 porcentaje de las recomendaciones devuelve solo el uno por ciento elementos más populares. El valor mínimo de cubo de percentil es ambos inclusive, y el valor máximo es exclusivo, excepto 100.
#### <a name="unique-items-recommended"></a>Elementos únicos recomendados
Los elementos únicos recomendados métrica muestra el número de elementos que se han devuelto de evaluación.
#### <a name="total-items-recommended"></a>Elementos totales recomendados
Los elementos de totales, se recomienda métrica muestra el número de elementos que se recomienda. Algunas pueden ser duplicados.

<a name="ImplementingEvaluation"></a>
### <a name="offline-evaluation-metrics"></a>Métrica de evaluación sin conexión ###
Las mediciones precisión y diversidad sin conexión pueden ser útiles cuando se selecciona la compilación de usar. En tiempo de compilación, como parte de las respectivas FBT o recomendación parámetros de compilación:

-   Establezca el parámetro de compilación *enableModelingInsights* en **true**.
-   Opcionalmente, seleccione la *splitterStrategy* (ambos *RandomSplitter* o *LastEventSplitter*).
*RandomSplitter* divide los datos de uso en tren y prueba establece basada en el porcentaje de prueba determinado *randomSplitterParameters* y aleatorio inicialización de valores.
*LastEventSplitter* divide los datos de uso en tren y probar conjuntos basados en la última transacción para cada usuario.

Esto activará una compilación que utiliza solo un subconjunto de los datos de formación y el resto de los datos para calcular métricas de evaluación.  Una vez completada la compilación, para obtener el resultado de la evaluación, debe llamar a la [API de obtener generar métricas](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f), pasar respectivos *modelId* y *buildId*.

 Éste es el resultado JSON para la evaluación de ejemplo.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }
