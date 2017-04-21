<properties
    pageTitle="Característica de ingeniería y selección de aprendizaje del equipo de Azure | Microsoft Azure"
    description="Explica los efectos de la selección de características y de ingeniería de la característica y se proporcionan ejemplos de su rol en el proceso de mejora de datos de aprendizaje del equipo."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-engineering-and-selection-in-azure-machine-learning"></a>Ingeniería de la característica y selección de aprendizaje del equipo de Azure

Este tema explica los fines de ingeniería de la característica y selección de características en el proceso de mejora de datos de aprendizaje del equipo. Se muestra qué implican estos procesos usando ejemplos de Azure máquina aprendizaje Studio.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Los datos de entrenamiento utilizados en aprendizaje de máquina a menudo pueden mejorarse mediante la selección o la extracción de características de los datos recopilados. Un ejemplo de una función de ingeniería en el contexto de aprender a clasificar las imágenes de caracteres a mano es un mapa de bits densidad construido a partir de los datos de la distribución de bits sin formato. Esta asignación puede ayudar a encontrar los bordes de los caracteres de forma más eficaz que la distribución sin formato.

Funciones de ingeniería y seleccionados aumentan la eficiencia del proceso de aprendizaje, que intenta extraer la información clave contenida en los datos. También mejoran la potencia de estos modelos para clasificar los datos de entrada con precisión y predecir resultados de interés de manera más sólida. También pueden combinar característica ingeniería y selección para que el aprendizaje matemáticamente más manejable. Para ello, mejorar y, a continuación, reducir el número de características necesitado para calibre o formar a un modelo. Matemáticamente hablando, las características seleccionadas para entrenar el modelo son un conjunto mínimo de variables independientes que explican los patrones de los datos y, a continuación, predecir resultados correctamente.

La ingeniería y selección de características es una parte de un proceso más grande, que normalmente consta de cuatro pasos:

* Recopilación de datos
* Mejora de datos
* Construcción de modelo
* Procesamiento posterior

Ingeniería y selección conforman el paso de mejora de datos de aprendizaje del equipo. Pueden distinguir tres aspectos de este proceso para nuestros propósitos:

* **Preprocesamiento de datos**: este proceso intenta asegurarse de que los datos recopilados están limpio y coherentes. Incluye tareas como la integración de varios conjuntos de datos, tratamiento de datos que faltan, tratamiento de datos incoherentes y conversión de tipos de datos.
* **Ingeniería de características**: este proceso intenta crear características adicionales de relevantes de las características existentes sin formato de los datos y para aumentar la capacidad predictiva el algoritmo de aprendizaje.
* **Selección de características**: este proceso selecciona el subconjunto clave de las características de datos original para reducir las dimensiones del problema de aprendizaje.

Este tema sólo trata los aspectos de la selección de característica del proceso de mejora de datos y de ingeniería de la característica. Para obtener más información sobre el paso de preprocesamiento de datos, vea [preprocesamiento datos en Azure máquina aprendizaje Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/).


## <a name="creating-features-from-your-data--feature-engineering"></a>Creación de funciones de ingeniería de características de sus datos:

Los datos de entrenamiento constan de una matriz que se compone de ejemplos (registros u observaciones almacenadas en filas), cada una de las cuales tiene un conjunto de características (variables o campos almacenados en las columnas). Las características especificadas en el diseño de experimentación deben caracterizan los patrones de los datos. Aunque muchos de los campos de datos sin formato se pueden incluir directamente en el conjunto de características seleccionadas utilizado para entrenar un modelo, funciones de ingeniería adicionales a menudo necesitan crearse a partir de las características de los datos sin formato para generar un conjunto de datos mejorada de aprendizaje.

¿Qué tipo de características debe crearse para mejorar el conjunto de datos cuando un modelo de aprendizaje? Funciones de ingeniería que mejoran la formación proporcionan información que diferencia mejor los patrones de los datos. Cree que las nuevas características para proporcionar información adicional que no se captura claramente o fácilmente aparente en el conjunto de características original o existente, pero este proceso es algo de una imagen. Decisiones de sonido y aumentar la productividad gracias a menudo requieren algunos conocimientos de dominios.

Al iniciar con el aprendizaje Azure, es más fácil de entender este proceso concretamente usando ejemplos proporcionados en Studio de aprendizaje del equipo. Aquí se muestran dos ejemplos:

* Un ejemplo de regresión ([predicción del número de alquileres de bicicletas](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)) en un ensayo controlado donde se conocen los valores de destino
* Un ejemplo de clasificación de minería de texto mediante la [Característica de hash][feature-hashing]

### <a name="example-1-adding-temporal-features-for-a-regression-model"></a>Ejemplo 1: Agregar características temporales para un modelo de regresión. ###

Para mostrar cómo diseñar características para una tarea de regresión, vamos a usar el ensayo "petición previsión de bicicletas" en Azure máquina aprendizaje Studio. El objetivo de este ensayo es predecir la demanda de las bicicletas, es decir, el número de alquileres de bicicletas dentro de un mes específico, día o la hora. El **conjunto de datos de bicicleta alquiler UCI** del conjunto de datos se utiliza como los datos de entrada sin formato.

Este conjunto de datos se basa en los datos reales de la compañía Bikeshare Capital que mantiene una red de alquiler bicicletas en Washington DC en los Estados Unidos. El conjunto de datos representa el número de alquileres de bicicletas en una hora específica de un día de 2011 a 2012 y contiene 17379 filas y columnas de 17. El conjunto de características sin formato contiene meteorológica (temperatura, humedad, viento) y el tipo del día (día festivo o día de la semana). El campo predecir es **cnt**, un número que representa la alquileres de bicicletas en una hora específica y que va de 1 a 977.

Para construir eficaces características de los datos de entrenamiento, se crean cuatro modelos de regresión utilizando el mismo algoritmo, pero con cuatro conjuntos de datos de aprendizaje diferentes. Los cuatro conjuntos de datos representan los mismos datos de entrada sin formato, pero con un gran número de características de configuración. Estas características se agrupan en cuatro categorías:

1. A = meteorología, vacaciones + DIASEM + fin_de_semana características para el día previsto
2. B = número de bicicletas que estaban arrendadas en cada una de las 12 horas anterior
3. C = número de bicicletas que estaban arrendadas en cada uno de los días de 12 anterior a la misma hora
4. D. = número de bicicletas que estaban arrendadas en cada una de las 12 semanas anterior a la misma hora y el mismo día

Además de la característica set A que ya existe en los datos sin formato originales, los tres conjuntos de características se crean mediante la característica de proceso de ingeniería. Conjunto de características B capturas la demanda reciente de las bicicletas. Conjunto de características C capturas la demanda de bicicletas en una hora determinada. Conjunto de características D capturas petición bicicletas horas en particular y el día de la semana. Cada uno de los cuatro conjuntos de datos de aprendizaje incluye conjuntos de características A, A + B, A + B + C y A + B + C + D, respectivamente.

En la misma aprendizaje del equipo de Azure, se crean estos cuatro conjuntos de datos de aprendizaje a través de cuatro ramas del conjunto de datos de entrada previamente tratado. Excepto la rama izquierda, cada una de estas ramas contiene un [Script de R ejecutar] [ execute-r-script] módulo en el que un conjunto de derivado características (función establece B, C y D) se crea y se anexa al conjunto de datos importado respectivamente. La figura siguiente muestra la secuencia de comandos de R utilizado para crear el conjunto de características B en la segunda bifurcación izquierda.

![Crear un conjunto de características](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

La siguiente tabla resume la comparación de los resultados de rendimiento de los cuatro modelos. Obtener los mejores resultados se muestran por características A + B + C. Observe que la tasa de error disminuye cuando conjuntos de características adicionales se incluyen en los datos de entrenamiento. Comprueba nuestro presunción que los conjuntos de características B y C proporcionan información adicional relevante para la tarea de regresión. Agregar el conjunto de características de D no parece proporcionar cualquier reducción adicional de la tasa de error.

![Comparar los resultados de rendimiento](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="example2"></a>Ejemplo 2: Creación de funciones de minería de datos de texto  

Característica ingeniería ampliamente se aplica en tareas relacionadas con la minería de datos de texto, como el análisis de opinión y clasificación de documento. Por ejemplo, si desea clasificar los documentos en varias categorías, una suposición típica es que las palabras o frases que se incluyen en una categoría de documento reduce la probabilidad de que se produzca en otra categoría de documento. En otras palabras, la frecuencia de la distribución de la palabra o frase es capaz de caracterizan categorías de documento diferente. En las aplicaciones de minería de datos de texto, la característica de proceso de ingeniería es necesaria para crear las características que implican frecuencias palabra o frase porque partes individuales de contenido de texto suele servir de los datos de entrada.

Para realizar esta tarea, se aplica una técnica denominada *función hash* para convertir eficazmente características de texto arbitrario en índices. En lugar de asociar cada característica de texto (palabras o frases) a un índice en particular, este método funciona aplicando una función de hash a las características y los valores de hash como índices directamente mediante.

En el aprendizaje Azure, no hay [Función hash] [ feature-hashing] módulo que crea estas características palabra o frase. La siguiente ilustración muestra un ejemplo del uso de este módulo. El conjunto de datos de entrada contiene dos columnas: la clasificación de libro comprendido entre 1 a 5 y el real revisar el contenido. El objetivo de esta [Característica hash] [ feature-hashing] módulo es recuperar nuevas características que muestran la frecuencia de repetición de las correspondientes palabras o frases dentro de la revisión del libro en particular. Para utilizar este módulo, debe completar los pasos siguientes:

1. Seleccione la columna que contiene el texto de entrada (**Col2** en este ejemplo).
2. Establecer *Hashing bitsize* 8, lo que significa 2 ^ 8 = 256 se crean funciones. A continuación, en la palabra o frase en el texto es hash a 256 índices. El parámetro *bitsize de Hashing* comprendido entre 1 y 31. Si el parámetro se establece en un número mayor, las palabras o frases son reduce la probabilidad de hash en el mismo índice.
3. Establezca el parámetro *N g* a 2. Recupera la frecuencia de repetición de unigrams (una función para cada palabra) y bigrams (una función para cada pareja de palabras adyacentes) del texto de entrada. El parámetro *N g* comprendido entre 0 y 10, que indica el número máximo de palabras secuenciales que desea incluir en una función.  

![Módulo de hash característica](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

La figura siguiente muestra el aspecto de estas nuevas características.

![Ejemplo de hash característica](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## <a name="filtering-features-from-your-data--feature-selection"></a>Características de los datos: selección de características de filtrado  ##

*Selección de funciones* es un proceso que normalmente se aplica a la construcción de conjuntos de datos de formación para el modelado de predicción tareas como tareas de clasificación o regresión. El objetivo es seleccionar un subconjunto de las características del conjunto de datos original que reduce sus dimensiones utilizando un conjunto mínimo de características para representar la cantidad máxima de varianza en los datos. Este subconjunto de características contiene las características que se incluirán para entrenar el modelo sola. Selección de características tiene dos propósitos principales:

* Selección de características a menudo aumenta la precisión de clasificación eliminando relevante y redundantes o altamente correlación características.
* Selección de características disminuye el número de características, lo que hace que el proceso de formación de modelo más eficaz. Esto es especialmente importante para los estudiantes que son costosos de entrenar como máquinas vector de soporte técnico.

Aunque la selección de características de búsquedas reducir el número de características en el conjunto de datos utilizados para entrenar el modelo, no se suele conoce por el término *reducción de dimensiones.* Métodos de selección de la característica extraen un subconjunto de características originales de los datos sin convertirlos.  Métodos de reducción de dimensiones utilizan funciones de ingeniería que pueden transformar las características originales y, por tanto, modificarlos. Análisis de componente principal, análisis de correlación canónico y descomposición valor singular son ejemplos de métodos de reducción de dimensiones.

Una categoría ampliamente aplicada de métodos de selección de la característica en un contexto controlado es selección de funciones basadas en el filtro. Evaluar la correlación entre cada característica y el atributo de destino, estos métodos aplicarán una medida estadística para asignar una calificación a cada característica. Las características a continuación, se clasifican por la puntuación, que puede usar para establecer el umbral para mantener o eliminar una característica específica. Algunos ejemplos de las medidas estadísticas utilizadas en estos métodos correlación de Pearson, información mutua y la prueba chi cuadrada.

Azure Studio de aprendizaje de máquina proporciona módulos de selección de características. Como se muestra en la siguiente ilustración, estos módulos incluyen [basada en el filtro de selección de características] [ filter-based-feature-selection] y [Análisis de Fisher lineal: discriminante][fisher-linear-discriminant-analysis].

![Ejemplo de selección de función](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)


Por ejemplo, utilice la [selección de característica basada en el filtro] [ filter-based-feature-selection] módulo con un ejemplo de minería de datos de texto descrito anteriormente. Suponga que desea crear un modelo de regresión después de crea un conjunto de características de 256 a través de la [Función hash] [ feature-hashing] módulo y que la variable de respuesta es **Col1** y representa un libro de revisión de clasificación comprendido entre 1 y 5. Establecer **método de resultados de la característica de** **Correlación de Pearson**, la **columna de destino** a **Col1**y **número de características que quiera** en **50**. El módulo de [selección de característica basada en el filtro] [ filter-based-feature-selection] , a continuación, genera un conjunto de datos que contiene las características de 50 junto con el atributo de destino **Col1**. La figura siguiente muestra el flujo de este ensayo y los parámetros de entrada.

![Ejemplo de selección de función](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

La figura siguiente muestra los conjuntos de datos resultantes. En cada característica es una puntuación basada en la correlación de Pearson entre sí y el atributo de destino **Col1**. Se mantienen las características con la puntuación superior.

![Conjuntos de datos de selección de funciones basadas en filtro](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

La figura siguiente muestra las calificaciones correspondientes de las características seleccionadas.

![Resultados de la función seleccionada](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

Al aplicar esta [selección de característica basada en el filtro] [ filter-based-feature-selection] módulo, 50 fuera de 256 funciones se seleccionan porque tienen más características relacionadas con el objetivo de variable **Col1** según el método de puntuación **Correlación de Pearson**.

## <a name="conclusion"></a>Conclusión
Ingeniería de la característica y selección de características son dos pasos con frecuencia para preparar los datos de entrenamiento al crear un modelo de aprendizaje del equipo. Normalmente, ingeniería de la característica se aplica primero para generar características adicionales y, a continuación, se realiza el paso de selección de la característica para eliminar características relevante, redundantes o muy relacionadas.

No siempre es necesariamente realizar la selección de características de ingeniería o característica. Si es necesario depende de los datos o recopilar, el algoritmo de que elegir y el objetivo de la ensayo.


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
