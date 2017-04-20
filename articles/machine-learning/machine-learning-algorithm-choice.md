<properties
    pageTitle="Cómo elegir algoritmos de aprendizaje de equipo | Microsoft Azure"
    description="Cómo elegir algoritmos de aprendizaje de la máquina de Azure para aprendizaje supervisado y no supervisado en los experimentos de regresión, clasificación o agrupamiento."
    services="machine-learning"
    documentationCenter=""
    authors="brohrer"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>
    
<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="08/09/2016"
    ms.author="brohrer;garye" />

# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Cómo elegir los algoritmos para la máquina de Azure de Microsoft Learning

La respuesta a la pregunta "¿qué equipo algoritmo de aprendizaje debo usar?" siempre es "Depende". Depende del tamaño, calidad y naturaleza de los datos. Depende de lo que desea hacer con la respuesta. Depende de cómo se tradujo la expresión matemática del algoritmo en instrucciones para el equipo que está utilizando. Y depende de cuánto tiempo tiene. Incluso los científicos más experimentados de datos no pueden saber qué algoritmo realizará mejor antes de tratar de ellos.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Hoja de referencia sobre el algoritmo de aprendizaje automático

La **Microsoft Azure máquina de aprendizaje algoritmo Cheat Sheet** le ayuda a elegir la máquina algoritmo para las soluciones de análisis predictivo de la biblioteca de aprendizaje de Microsoft Azure máquina de algoritmos de aprendizaje.
En este artículo se explica cómo utilizarlo.

> [AZURE.NOTE] Para descargar hoja de trampas y siga con este artículo, vaya a la [hoja de referencia sobre algoritmo para Microsoft Azure máquina de aprendizaje Studio de aprendizaje automático](machine-learning-algorithm-cheat-sheet.md).

Esta hoja de referencia sobre tiene un público muy específico en mente: principio datos científico con el aprendizaje del equipo de nivel de títulos universitarios, intentando elegir un algoritmo inicial en Azure máquina de aprendizaje Studio. Eso significa que hace algunas generalizaciones y simplificaciones, pero apunta en una dirección segura. También significa que hay un montón de algoritmos que no se muestran aquí. A medida que crece el aprendizaje Azure para abarcar un conjunto más completo de los métodos disponibles, vamos a añadir.

Estas recomendaciones son sugerencias y comentarios compilado de un montón de científicos de datos y expertos de aprendizaje de la máquina. No estamos de acuerdo en todo, pero he intentado armonizar nuestras opiniones en un consenso aproximado. La mayoría de las instrucciones de desacuerdo comienzan con "Depende..."

### <a name="how-to-use-the-cheat-sheet"></a>Cómo utilizar la hoja de referencia sobre

Leer las etiquetas de ruta de acceso y el algoritmo en el gráfico como "de * &lt;etiqueta de ruta de acceso&gt; * utilizar * &lt;algoritmo de&gt;*." Por ejemplo, "para *velocidad* utilice *dos regresión logística de clase*." Se aplicará en ocasiones más de una rama.
A veces, ninguno de ellos será un ajuste perfecto. Se dirigen a ser recomendaciones de la regla, por lo que no se preocupe siendo exacta.
Varios científicos de datos que hablé con dicho que la única forma para encontrar el mejor algoritmo es tratar de todos ellos.

Éste es un ejemplo de la [Galería de Cortana inteligencia](http://gallery.cortanaintelligence.com/) de un experimento que intenta varios algoritmos frente a los mismos datos y compara los resultados: [comparar clase múltiples clasificadores: reconocimiento de letra](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

>[AZURE.TIP] Para descargar e imprimir un diagrama que ofrece una visión general de las capacidades de estudio de aprendizaje de la máquina, consulte el [diagrama de resumen de capacidades de Azure máquina Learning Studio](machine-learning-studio-overview-diagram.md).

## <a name="flavors-of-machine-learning"></a>Modos de aprendizaje de máquinas

### <a name="supervised"></a>Supervisados

Algoritmos de aprendizaje supervisado realizar predicciones basadas en un conjunto de ejemplos. Por ejemplo, cotizaciones históricas pueden utilizarse para intentos de riesgo en los precios futuros. Cada ejemplo que se utiliza para el entrenamiento se etiqueta con el valor de interés, en este caso, el precio de las acciones. Un algoritmo de aprendizaje supervisado busca patrones en las etiquetas de valor. Puede utilizar cualquier información que podría ser pertinente, el día de la semana, la temporada, datos financieros de la empresa, el tipo de industria, la presencia de eventos disruptivos geopolicitical — y cada algoritmo busca diferentes tipos de modelos. Después de que el algoritmo ha encontrado el patrón mejor que pueda, utiliza ese patrón para realizar predicciones para los datos de pruebas sin etiquetar: precios del mañana.

Esto es un tipo más popular y útil de aprendizaje de la máquina. Con una excepción, todos los módulos de aprendizaje de la máquina de Azure están supervisados algoritmos de aprendizaje. Hay varios tipos específicos de aprendizaje supervisado que están representados en el aprendizaje de la máquina de Azure: detección de anomalías, regresión y clasificación.

* **Clasificación**. Cuando los datos se utilizan para predecir una categoría, aprendizaje supervisado también se llama clasificación. Éste es el caso cuando se asigna una imagen como una imagen de un gato' ' o un 'perro'. Cuando hay sólo dos opciones, esto se denomina **dos clases** o **clasificación binomial**. Cuando hay más categorías, como hora de predecir el ganador del torneo NCAA locura de marzo, este problema se conoce como **clasificación de clase múltiples**.

* **Regresión**. Cuando se que se predice un valor, al igual que con los precios de las acciones, aprendizaje supervisado se denomina regresión.

* **Detección de anomalías**. A veces el objetivo es identificar los puntos de datos son simplemente inusuales. En la detección de fraudes, por ejemplo, los patrones de gasto muy inusual de tarjeta de crédito son sospechosas. Las posibles variaciones son tan numerosas y aspecto de los ejemplos de formación tan pocos, que no es factible para saber qué actividades fraudulentas. El enfoque de detección de anomalías es simplemente aprender qué actividad normal aspecto (con un transacciones no fraudulenta del historial) e identificar todo lo que es significativamente diferente.

### <a name="unsupervised"></a>Sin supervisión

En el aprendizaje no supervisado, puntos de datos carecen de rótulos asociados a ellos. En su lugar, el objetivo de un algoritmo de aprendizaje no supervisado es para organizar los datos de alguna manera o para describir su estructura. Esto puede significar la agrupación en clústeres o buscar diferentes maneras de ver datos complejos para que aparezca más organizada o más sencillo.

### <a name="reinforcement-learning"></a>Aprendizaje de refuerzo

En refuerzo de aprendizaje, el algoritmo obtiene elegir una acción en respuesta a cada punto de datos. El algoritmo de aprendizaje también recibe una señal de recompensa un poco más adelante, indicando que la decisión de qué bueno fue.
Según esto, el algoritmo modifica su estrategia para lograr la mayor recompensa. Actualmente no hay ningún refuerzo aprendizaje módulos de algoritmo de aprendizaje de la máquina de Azure. Aprendizaje de refuerzo es común en robótica, donde el conjunto de lecturas del sensor en un punto en el tiempo es un punto de datos y el algoritmo debe elegir la siguiente acción de la idea del robot. También es que un se adecua perfectamente para Internet de cosas aplicaciones.

## <a name="considerations-when-choosing-an-algorithm"></a>Consideraciones al elegir un algoritmo de

### <a name="accuracy"></a>Precisión

Recibir una respuesta más precisa posible no siempre es necesario.
A veces una aproximación es adecuada, según lo que desee utilizarlo para. Si ese es el caso, puede reducir el tiempo de procesamiento considerablemente en pegarse con los métodos más aproximados. Otra ventaja de los métodos más aproximados es que naturalmente tienden a evitar el [sobreajuste](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Tiempo de formación

El número de minutos u horas necesarias para formar un modelo varía mucho entre algoritmos. Tiempo de entrenamiento es a menudo relacionado con precisión: normalmente uno acompaña al otro. Además, algunos algoritmos son más sensibles a la cantidad de puntos de datos que otros.
Cuando el tiempo es limitado puede impulsar la elección del algoritmo, especialmente cuando el conjunto de datos es grande.

### <a name="linearity"></a>Linealidad

Muchos algoritmos de aprendizaje de máquina de hacen uso de linealidad. Algoritmos de clasificación lineal, se supone que las clases pueden separarse mediante una línea recta (o su análogo de mayores dimensiones). Éstos incluyen regresión logística y admiten máquinas de vectores (tal como está implementado en el aprendizaje de la máquina de Azure).
Algoritmos de regresión lineal, se supone que las tendencias de datos sigan una línea recta. Estas suposiciones no son incorrectos para algunos problemas, pero en otros que ofrecen precisión hacia abajo.

![Bounday clase no lineal][1]

***Límite de clase no lineal*** *-depender de un algoritmo de clasificación lineal daría lugar a precisión baja*

![Datos con una tendencia lineal][2]

***Datos con una tendencia lineal*** *-utilizando un método de regresión lineal generaría mucho errores más grande de lo necesario*

A pesar de sus peligros, algoritmos lineales son muy populares como primera línea de ataque. Tienden a ser algorítmico simple y rápido para entrenar.

### <a name="number-of-parameters"></a>Número de parámetros

Los parámetros son los tiradores que obtiene un científico de datos para activar al configurar un algoritmo. Son números que afectan al comportamiento del algoritmo, como tolerancia de error o el número de iteraciones o de opciones entre las variantes de cómo se comporta el algoritmo. El tiempo de formación y la precisión del algoritmo a veces pueden ser bastante sensibles para obtener la configuración de derechos. Normalmente, los algoritmos con parámetros números grandes requieren la mayoría de prueba y error para encontrar una buena combinación.

Alternativamente, existe un bloque del módulo [parámetro barrido](machine-learning-algorithm-parameters-optimize.md) en aprendizaje de máquinas de Azure que intenta automáticamente todas las combinaciones de parámetros en cualquier detalle elegido. Aunque esto es una gran manera de asegurarse de que ha distribuido el espacio del parámetro, el tiempo necesario para formar un modelo aumenta de forma exponencial con el número de parámetros.

La ventaja es que muchos de los parámetros normalmente indica que un algoritmo tiene mayor flexibilidad. A menudo se puede lograr muy buena precisión. Proporciona que puede encontrar la mejor combinación de valores de parámetros.

### <a name="number-of-features"></a>Número de características

Para ciertos tipos de datos, el número de características puede ser muy grande en comparación con el número de puntos de datos. Suele ser el caso con genética o datos textuales. Algunos algoritmos de aprendizaje, realizar formación inviablemente largo de tiempo puede perjudicar la gran cantidad de características. Máquinas de vectores soporte son idóneas para este caso (vea abajo).

### <a name="special-cases"></a>Casos especiales

Algunos algoritmos de aprendizaje suponen determinado sobre la estructura de los datos o los resultados deseados. Si se encuentra uno que se adapte a sus necesidades, puede proporcionarle resultados más útiles, predicciones más precisas o tiempos de formación.

|**Algoritmo de**|**Precisión**|**Tiempo de formación**|**Linealidad**|**Parámetros**|**Notas**|
|---|:---:|:---:|:---:|:---:|---|
|**Clasificación de dos clases**| | | | | |
|[regresión logística](https://msdn.microsoft.com/library/azure/dn905994.aspx)                    | |●|●|5| |
|[bosque de decisión](https://msdn.microsoft.com/library/azure/dn906008.aspx)|●|○| |6| |
|[Selva de decisión](https://msdn.microsoft.com/library/azure/dn905976.aspx)|●|○| |6|Superficie de memoria baja|
|[árbol de decisión se incrementó](https://msdn.microsoft.com/library/azure/dn906025.aspx)|●|○| |6|Grandes necesidades de memoria|
|[Red neuronal](https://msdn.microsoft.com/library/azure/dn905947.aspx)|●| | |9|[Personalización adicional es posible](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[promedio de perceptrón](https://msdn.microsoft.com/library/azure/dn906036.aspx)|○|○|●|4| |
|[máquina de vectores de soporte](https://msdn.microsoft.com/library/azure/dn905835.aspx)| |○|●|5|Es bueno para los conjuntos de características de gran tamaño|
|[máquina de vectores soporte deep localmente](https://msdn.microsoft.com/library/azure/dn913070.aspx)|○| | |8|Es bueno para los conjuntos de características de gran tamaño|
|[Equipo de punto de Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx)| |○|●|3| |
|**Clasificación de clase múltiples**| | | | | |
|[regresión logística](https://msdn.microsoft.com/library/azure/dn905853.aspx)| |●|●|5| |
|[bosque de decisión](https://msdn.microsoft.com/library/azure/dn906015.aspx)|●|○| |6| |
|[Selva de decisión](https://msdn.microsoft.com/library/azure/dn905963.aspx)|●|○| |6|Superficie de memoria baja|
|[Red neuronal](https://msdn.microsoft.com/library/azure/dn906030.aspx)|●| | |9|[Personalización adicional es posible](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[un v todos](https://msdn.microsoft.com/library/azure/dn905887.aspx)|-|-|-|-|Vea las propiedades del método dos clases seleccionadas|
|**Regresión**| | | | | |
|[lineal](https://msdn.microsoft.com/library/azure/dn905978.aspx)| |●|●|4| |
|[Bayesiano lineal](https://msdn.microsoft.com/library/azure/dn906022.aspx)| |○|●|2| |
|[bosque de decisión](https://msdn.microsoft.com/library/azure/dn905862.aspx)|●|○| |6| |
|[árbol de decisión se incrementó](https://msdn.microsoft.com/library/azure/dn905801.aspx)|●|○| |5|Grandes necesidades de memoria|
|[bosque rápida quantile](https://msdn.microsoft.com/library/azure/dn913093.aspx)|●|○| |9|Distribuciones en lugar de predicciones de punto|
|[Red neuronal](https://msdn.microsoft.com/library/azure/dn905924.aspx)|●| | |9|[Personalización adicional es posible](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[POISSON](https://msdn.microsoft.com/library/azure/dn905988.aspx)| | |●|5|Técnicamente log-lineal. Para la predicción de recuentos|
|[ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx)| | | |0|Para predecir el orden de clasificación|
|**Detección de anomalías**| | | | | |
|[máquina de vectores de soporte](https://msdn.microsoft.com/library/azure/dn913103.aspx)|○|○| |2|Especialmente útil para los conjuntos de características de gran tamaño|
|[Detección de anomalías basado en PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx)| |○|●|3| |
|[K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)| |○|●|4|Un algoritmo de agrupamiento|


**Propiedades de algoritmo:**

**●** - muestra una precisión excelente, períodos de formación rápida y el uso de linealidad

**○** - muestra buena precisión y períodos de formación moderada

## <a name="algorithm-notes"></a>Notas del algoritmo

### <a name="linear-regression"></a>Regresión lineal

Como se mencionó anteriormente, [la regresión lineal](https://msdn.microsoft.com/library/azure/dn905978.aspx) se ajusta a una línea (o plano o hiperplano) al conjunto de datos. Es un potente, sencillo y rápido, pero puede ser demasiado simplista según algunos problemas.
Haga clic aquí para ver un [tutorial de regresión lineal](machine-learning-linear-regression-in-azure.md).

![Datos con una tendencia lineal][3]

***Datos con una tendencia lineal***

### <a name="logistic-regression"></a>Regresión logística

Aunque de forma confusa incluye 'regresión' en el nombre, regresión logística es realmente una poderosa herramienta para la clasificación de [dos clases](https://msdn.microsoft.com/library/azure/dn905994.aspx) y [multiclase](https://msdn.microsoft.com/library/azure/dn905853.aspx) . Es rápido y sencillo. El hecho de que utiliza de una '-facilita una solución natural para dividir los datos en grupos con forma curva en lugar de una línea recta. Límites de regresión logística da clase lineal, por lo que al usarlo, asegúrese de que una aproximación lineal es algo que se puede vivir con.

![Regresión logística para dos clases de datos con una sola función][4]

***Una regresión logística para dos clases de datos con una sola función*** *: el límite de clase es el punto en el que la curva logística está tan cerca ambas clases*

### <a name="trees-forests-and-jungles"></a>Árboles, bosques y selvas

Bosques de decisión ([regresión](https://msdn.microsoft.com/library/azure/dn905862.aspx), [dos clases](https://msdn.microsoft.com/library/azure/dn906008.aspx)y [multiclase](https://msdn.microsoft.com/library/azure/dn906015.aspx)), selvas de decisión ([dos clases](https://msdn.microsoft.com/library/azure/dn905976.aspx) y [multiclase](https://msdn.microsoft.com/library/azure/dn905963.aspx)) y árboles de decisión se incrementó ([regresión](https://msdn.microsoft.com/library/azure/dn905801.aspx) y [dos clases](https://msdn.microsoft.com/library/azure/dn906025.aspx)) son todos basados en árboles de decisión, un equipo básico concepto de aprendizaje. Hay muchas variantes de árboles de decisión, pero hacen lo mismo: subdividir el espacio de la característica en regiones con principalmente la misma etiqueta. Pueden ser regiones de categoría coherente o de valor constante, dependiendo de si realiza clasificación o regresión.

![Árbol de decisión subdivide un espacio de función][5]

***Un árbol de decisión subdivide un espacio característica en regiones de valores más o menos uniformes***

Dado un espacio de función puede subdividirse en regiones arbitrariamente pequeñas, es fácil imaginar dividirlo con la precisión suficiente para tener un punto de datos por región: un ejemplo extremo de desbordamiento. Para evitar esto, un gran conjunto de árboles se construyen con especial cuidado matemático adoptado que los árboles no están correlacionados. El promedio de este "bosque de decisión" es un árbol que evita el desbordamiento. Bosques de decisión pueden utilizar mucha memoria. Selvas de decisión son una variante que consume menos memoria a costa de un tiempo ligeramente superior de formación.

Árboles de decisión se incrementó para evitar el desbordamiento, limitar cuántas veces puede subdividir y cómo pocos puntos de datos se permiten en cada región. El algoritmo construye una secuencia de árboles, cada uno de los cuales aprende a compensar el error el árbol antes de la izquierda. El resultado es un estudiante muy preciso que suele utilizar mucha memoria. Para la descripción técnica completa, desproteger [documento original de Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Regresión del quantile rápida de bosque](https://msdn.microsoft.com/library/azure/dn913093.aspx) es una variación de árboles de decisión para el caso especial donde desea conocer no sólo el valor típico (medio) de los datos dentro de una región, sino también su distribución en forma de cuantiles.

### <a name="neural-networks-and-perceptrons"></a>Perceptrones y redes neurales

Redes neurales son cerebro basado en la inspiración sobre los problemas de [regresión](https://msdn.microsoft.com/library/azure/dn905924.aspx) , [multiclase](https://msdn.microsoft.com/library/azure/dn906030.aspx)y [dos clases](https://msdn.microsoft.com/library/azure/dn905947.aspx)de algoritmos de aprendizaje. Vienen en una variedad infinita, pero las redes neurales en aprendizaje de máquinas de Azure son todos de la forma de gráficos acíclicos dirigidos. Esto significa que las características de entrada se pasan hacia delante (nunca hacia atrás) a través de una secuencia de capas antes de convertirse en salidas. En cada capa, entradas son ponderadas en diversas combinaciones, suman y pasa a la siguiente capa. Esta combinación de cálculos sencillos da como resultado la capacidad para obtener tendencias de límites y datos de clase sofisticadas, aparentemente por arte de magia. Varios niveles de redes de este tipo realizan el "aprendizaje profundo" que alimenta el informe técnico de tanta y ciencia ficción.

Este alto rendimiento no incluye de forma gratuita, sin embargo. Redes neurales pueden tardar mucho tiempo en el entrenamiento, especialmente para grandes conjuntos de datos con muchas características. También tienen más parámetros que la mayoría de los algoritmos, lo que significa que el barrido de parámetro expande el tiempo de formación mucho.
Y para esos mérito de adicional que desean [especificar su propia estructura de red](http://go.microsoft.com/fwlink/?LinkId=402867), las posibilidades son inagotables.

<a name="boundaries-learned-by-neural-networks6"></a>![Límites aprendidos por redes neurales][6]
---------------------------

***Los límites aprendidos por redes neurales pueden ser compleja e irregular***

El [perceptrón un promedio de dos clases](https://msdn.microsoft.com/library/azure/dn906036.aspx) es respuesta redes neurales a períodos de formación creciente. Utiliza una estructura de red que proporciona los límites de clase lineal. Es casi primitivo según los estándares actuales, pero tiene un largo historial de trabajo con solidez y es lo suficientemente pequeño para aprender rápidamente.

### <a name="svms"></a>MVS

Máquinas de vectores soporte (SVM) encuentran el límite que separa las clases por una amplia margen como sea posible. Cuando no pueden separarse claramente las dos clases, los algoritmos de encuentran el mejor límite pueden. A medida que se escriben en el aprendizaje de la máquina de Azure, las [dos clases SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx) hace con sólo una línea recta. (En hablar de SVM, utiliza un núcleo lineal.) Porque hace esta aproximación lineal, es capaz de ejecutar con bastante rapidez. Donde realmente destaca es con datos característica intenso, como texto o genómica. En estos casos SVM es capaces de separar las clases más rápidamente y con menos desbordamiento que mayoría otros algoritmos, además de requerir sólo una pequeña cantidad de memoria.

![Límite de clase de equipo de soporte vectorial][7]

***Un límite de clase de equipo de vector habituales de soporte maximiza el margen que separa dos clases***

Otro producto de Microsoft Research, las [dos clases profundo localmente SVM](https://msdn.microsoft.com/library/azure/dn913070.aspx) es una variante no lineal de SVM que mantiene la mayor parte de la eficacia de la velocidad y la memoria de la versión lineal. Es ideal para los casos donde el enfoque lineal no da respuestas suficientemente precisas. Los desarrolladores mantienen rápido al dividir el problema en un montón de pequeños problemas SVM lineales. Lea la [descripción completa](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf) de los detalles sobre cómo saca de este truco.

Utiliza una extensión inteligente de SVM no lineal, la [clase uno SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx) dibuja un límite que describe estrechamente todo el conjunto de datos. Es útil para la detección de anomalías. Los puntos de datos nuevos que sobresalen mucho ese límite son inusuales ser notables.

### <a name="bayesian-methods"></a>Métodos bayesiana

Métodos bayesiana tienen una calidad muy deseable: evitar el desbordamiento. Esto hace algunas suposiciones con antelación sobre la distribución probabilidad de la respuesta. Otro producto derivado de este enfoque es que tienen muy pocos parámetros. Aprendizaje automático Azure tiene dos algoritmos bayesiana para clasificación ([máquina de punto dos clases Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx)) y regresión ([regresión lineal bayesiana](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Tenga en cuenta que estos se supone que los datos se pueden dividir o ajustar con una línea recta.

En una nota histórica, máquinas de punto Bayes desarrolladas en Microsoft Research. Tienen algunos trabajos teóricos excepcionalmente bella detrás de ellos. El alumno interesado se dirige a un [blog de Chris Bishop perspicaces](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx)y el [artículo original en JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) .

### <a name="specialized-algorithms"></a>Algoritmos especializados

Si tiene un objetivo muy específico es de suerte. Dentro de la colección de aprendizaje de la máquina de Azure hay algoritmos que se especializan en la predicción rank ([regresión ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx)), recuento de predicción ([regresión de Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx)) y detección de anomalías (uno basado en el [análisis de componentes principales](https://msdn.microsoft.com/library/azure/dn913102.aspx) y basada en la [máquina de vectores de soporte](https://msdn.microsoft.com/library/azure/dn913103.aspx)s).
Y hay un solo algoritmo de agrupación así ([K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)).

![Detección de anomalías basado en PCA][8]

***Detección de anomalías basado en PCA*** *-la mayoría de los datos que entra una distribución estereotipada; puntos desvía considerablemente de que distribución son sospechosas*

![Conjunto de datos agrupados utilizando K-means][9]

***Un conjunto de datos se agrupan en 5 clústeres utilizando K-means***

También hay un ensemble [una v todos multiclase clasificador](https://msdn.microsoft.com/library/azure/dn905887.aspx), que divide el problema de la clasificación de clase N en problemas de clasificación de dos clases de N-1. La precisión, el tiempo de formación y propiedades de linealidad se determinan por los clasificadores de dos clases que se utilizan.

![Clase de dos clasificadores se combinan para formar un clasificador de tres clases][10]

***Un par de clase de dos clasificadores se combinan para formar un clasificador de tres clases***

También se aprendizaje de máquina Azure incluye el acceso a un entorno de aprendizaje de máquina poderosa debajo del título del [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW desafía la categorización aquí, ya que puede aprender problemas de clasificación y regresión e incluso puede aprender de datos parcialmente sin etiquetar. Puede configurar para utilizar uno cualquiera de una serie de algoritmos, funciones de pérdida y algoritmos de optimización de aprendizaje. Fue diseñado desde el principio hasta ser paralelo, extremadamente rápida y eficaz. Se encarga de los conjuntos de características ridículo grande con poco esfuerzo aparente.
Iniciado y liderado por John Langford del Microsoft Research propio, VW es una entrada de una fórmula en un campo de algoritmos de coche de stock. No todos los problemas se ajusta a VW, pero si lo hace suyo, puede ser la pena Subir la curva de aprendizaje en su interfaz. También está disponible como [código fuente abierto independiente](https://github.com/JohnLangford/vowpal_wabbit) en varios idiomas.


<!-- Media -->

[1]: ./media/machine-learning-algorithm-choice/image1.png
[2]: ./media/machine-learning-algorithm-choice/image2.png
[3]: ./media/machine-learning-algorithm-choice/image3.png
[4]: ./media/machine-learning-algorithm-choice/image4.png
[5]: ./media/machine-learning-algorithm-choice/image5.png
[6]: ./media/machine-learning-algorithm-choice/image6.png
[7]: ./media/machine-learning-algorithm-choice/image7.png
[8]: ./media/machine-learning-algorithm-choice/image8.png
[9]: ./media/machine-learning-algorithm-choice/image9.png
[10]: ./media/machine-learning-algorithm-choice/image10.png
