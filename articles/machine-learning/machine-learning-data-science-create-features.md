<properties
    pageTitle="Característica de ingeniería en el proceso de análisis de Cortana | Microsoft Azure" 
    description="Explica los efectos de ingeniería de la característica y se proporcionan ejemplos de su rol en el proceso de mejora de datos de aprendizaje."
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
    ms.date="09/19/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-engineering-in-the-cortana-analytics-process"></a>Ingeniería de la característica en el proceso de análisis de Cortana 

Este tema explica los efectos de ingeniería de la característica y proporciona ejemplos de su rol en el proceso de mejora de datos de aprendizaje del equipo. Los ejemplos que se utilizan para ilustrar este proceso se dibujan Studio de aprendizaje del equipo de Azure. 

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

Vínculos de este **menú** a temas que describen cómo crear características para los datos en distintos entornos. Esta tarea es un paso en el [Proceso de ciencias de datos de equipo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Característica intentos de ingeniería para aumentar la capacidad predictiva de aprendizaje algoritmos mediante la creación de características de los datos sin formato que facilitan el proceso de aprendizaje. La ingeniería y selección de características es una parte de la TDSP descrito en la [¿Qué es el proceso de ciencias de datos de grupo?](data-science-process-overview.md) Ingeniería de la característica y selección son partes del paso **desarrollar características** de la TDSP. 

* **ingeniería de la característica**: este proceso intenta crear características adicionales de relevantes de las características existentes sin formato de los datos y para aumentar la capacidad predictiva del algoritmo de aprendizaje.

* **selección de características**: este proceso selecciona el subconjunto de clave de las características de datos original de un intento de reducir las dimensiones del problema de aprendizaje.

Normalmente se aplica primero **ingeniería de la característica** para generar características adicionales y, a continuación, se realiza el paso de **selección de características** para eliminar características relevante, redundantes o muy relacionadas.

A menudo se pueden mejorar los datos de entrenamiento utilizados en aprendizaje de máquina por extracción de características de los datos recopilados. Un ejemplo de una función de ingeniería en el contexto de aprender a clasificar las imágenes de caracteres manuscritas es la creación de un poco mapa de densidad construida a partir de los datos de la distribución de bits sin formato. Esta asignación puede ayudar a encontrar los bordes de los caracteres de forma más eficaz que simplemente utilizando la distribución sin formato directamente.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="creating-features-from-your-data---feature-engineering"></a>Creación de funciones de ingeniería de características de los datos:

Los datos de entrenamiento constan de una matriz que se compone de ejemplos (registros u observaciones almacenadas en filas), cada una de las cuales tiene un conjunto de características (variables o campos almacenados en las columnas). Las características especificadas en el diseño de experimentación deben caracterizan los patrones de los datos. Aunque muchos de los campos de datos sin formato se pueden incluir directamente en el conjunto de características seleccionadas utilizado para entrenar un modelo, suele ser el caso de que deben crearse a partir de las características de los datos sin formato para generar un conjunto de datos mejorado formación características adicionales de (ingeniería).

¿Qué tipo de características debe crearse para mejorar el conjunto de datos cuando un modelo de aprendizaje? Funciones de ingeniería que mejoran la formación proporcionan información que diferencia mejor los patrones de los datos. Esperamos que las nuevas características para proporcionar información adicional que no sea claramente capturada o fácilmente aparente en el conjunto de características original o existente. Pero este proceso es algo de una imagen. Decisiones de sonido y aumentar la productividad gracias a menudo requieren algunos conocimientos de dominios.

Al iniciar con el aprendizaje Azure, es más fácil de entender este proceso concretamente con ejemplos proporcionados en Studio. Aquí se muestran dos ejemplos:

* Un ejemplo de regresión [predicción del número de alquileres de bicicletas](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) en un ensayo controlado donde se conocen los valores de destino
* Un ejemplo de clasificación de minería de datos de texto con la [Característica de hash](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

### <a name="example-1-adding-temporal-features-for-regression-model"></a>Ejemplo 1: Agregar características Temporal para el modelo de regresión. ###

Vamos a usar el ensayo "petición previsión de bicicletas" en Azure máquina aprendizaje Studio para demostrar cómo diseñar características para una tarea de regresión. El objetivo de este ensayo es predecir la demanda de las bicicletas, es decir, el número de alquileres de bicicletas dentro de un mes, día y hora específica. El conjunto de datos "alquiler de bicicleta UCI dataset" se usa como la entrada de datos sin formato. Este conjunto de datos se basa en los datos reales de la compañía Bikeshare Capital que mantiene una red de alquiler bicicletas en Washington DC en los Estados Unidos. El conjunto de datos representa el número de alquileres de bicicletas en una hora específica del día en los años 2011 y año 2012 y contiene 17379 filas y columnas de 17. El conjunto de características sin formato contiene meteorológica (temperatura/humedad/viento) y el tipo del día (día festivo o día de la semana). El campo predecir es "cnt", un recuento que representa el alquileres de bicicletas en una hora específica y que va de 1 a 977 de rangos.

Con el objetivo de construcción características eficaces en los datos de entrenamiento cuatro regresión modelos se crean utilizando el mismo algoritmo, pero con cuatro conjuntos de datos de aprendizaje diferentes. Los cuatro conjuntos de datos representan los mismos datos de entrada sin formato, pero con un gran número de características de configuración. Estas características se agrupan en cuatro categorías:

1. A = meteorología, vacaciones + DIASEM + fin_de_semana características para el día previsto
2. B = número de bicicletas que estaban arrendadas en cada una de las 12 horas anterior
3. C = número de bicicletas que estaban arrendadas en cada uno de los días de 12 anterior a la misma hora
4. D. = número de bicicletas que estaban arrendadas en cada una de las 12 semanas anterior a la misma hora y el mismo día

Además de la característica set A que ya existen en los datos sin formato originales, los tres conjuntos de características se crean mediante la característica de proceso de ingeniería. Conjunto de características B capturas muy reciente demanda de las bicicletas. Conjunto de características C capturas la demanda de bicicletas en una hora determinada. Conjunto de características D capturas petición bicicletas horas en particular y el día de la semana. La cuatro formación conjuntos de datos cada incluyen un conjunto de características, A + B, A + B + C y A + B + C + D, respectivamente.

En la misma aprendizaje del equipo de Azure, se crean estos cuatro conjuntos de datos de aprendizaje a través de cuatro ramas del conjunto de datos de entrada previamente tratado. Excepto la izquierda que contiene la mayoría rama, cada una de estas ramas un módulo de [secuencia de comandos de Execute R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) , en la que un conjunto de derivado características (función establece B, C y D) se construye y anexa al conjunto de datos importado respectivamente. La figura siguiente muestra la secuencia de comandos de R utilizado para crear el conjunto de características B en la segunda bifurcación izquierda.

![crear funciones](./media/machine-learning-data-science-create-features/addFeature-Rscripts.png)

En la tabla siguiente se resumen la comparación de los resultados de rendimiento de los cuatro modelos. Obtener los mejores resultados se muestran por características A + B + C. Nota que la tasa de error disminuye al conjunto de características adicionales se incluyen en los datos de entrenamiento. Comprueba nuestro presunción que el conjunto de características B, C proporciona información adicional relevante para la tarea de regresión. Pero agregar la característica D parece proporcionar cualquier reducción adicional de la tasa de error.

![comparación de resultado](./media/machine-learning-data-science-create-features/result1.png)

### <a name="example2"></a>Ejemplo 2: Creación de funciones de minería de datos de texto  

Característica ingeniería ampliamente se aplica en tareas relacionadas con la minería de datos de texto, como el análisis de opinión y clasificación de documento. Por ejemplo, cuando desea clasificar los documentos en varias categorías, una suposición típica es que el incluido en una categoría de documento de word o frases reduce la probabilidad de que se produzca en otra categoría de documento. Dicho de otro modo, la frecuencia de la distribución de palabras o frases es posible caracterizan categorías de documento diferente. En las aplicaciones de minería de datos de texto, porque partes individuales de contenido de texto suele servir de los datos de entrada, la característica de proceso de ingeniería es necesaria para crear las características de frecuencias de la palabra o frase.

Para realizar esta tarea, se aplica una técnica denominada **función hash** para convertir eficazmente características de texto arbitrario en índices. En lugar de asociar cada característica de texto (palabras o frases) a un índice en particular, este método funciona aplicando una función de hash a las características y utilizando sus valores de hash como índices directamente.

En el aprendizaje Azure, hay un módulo de [Característica hash](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) que crea estos palabra o frase características cómodamente. En la figura siguiente muestra un ejemplo del uso de este módulo. El conjunto de datos de entrada contiene dos columnas: la clasificación de libro comprendido entre 1 y 5 y el contenido de revisión real. El objetivo de este módulo [Característica hash](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) es para recuperar un conjunto de nuevas características que muestran la frecuencia de repetición de las palabras correspondientes / revisión de frases en el libro en particular. Para utilizar este módulo, es necesario realizar los siguientes pasos:

* En primer lugar, seleccione la columna que contiene el texto de entrada ("Col2" en este ejemplo).
* En segundo término, establecer "bitsize de Hashing" 8, lo que significa que 2 ^ 8 = 256 características se creará. La fase de word o en todo el texto se le hash a 256 índices. El parámetro "Hashing bitsize" comprendido entre 1 y 31. Las palabras o frases están poco probable que se obtiene en el mismo índice si establecer que sea un número mayor.
* Por último, establezca el parámetro "N-g" a 2. Este modo Obtiene la frecuencia de repetición de unigrams (una función para cada palabra) y bigrams (una función para cada pareja de palabras adyacentes) del texto de entrada. El parámetro "N-g" comprendido entre 0 y 10, que indica el número máximo de palabras secuenciales que desea incluir en una función.  

![Módulo "Característica Hashing"](./media/machine-learning-data-science-create-features/feature-Hashing1.png)

La figura siguiente muestra lo que estas características nueva apariencia similar.

![Ejemplo de la "función Hashing"](./media/machine-learning-data-science-create-features/feature-Hashing2.png)


## <a name="conclusion"></a>Conclusión

Funciones de ingeniería y seleccionados aumentan la eficiencia del proceso de aprendizaje que intenta extraer la información clave contenida en los datos. También mejoran la potencia de estos modelos para clasificar los datos de entrada con precisión y predecir resultados de interés de manera más sólida. También pueden combinar característica ingeniería y selección para que el aprendizaje matemáticamente más manejable. Para ello, mejorar y, a continuación, reducir el número de características necesitado para calibre o formar a un modelo. Matemáticamente hablando, las características seleccionadas para entrenar el modelo son un conjunto mínimo de variables independientes que explican los patrones de los datos y, a continuación, predecir resultados correctamente.

Tenga en cuenta que no siempre es necesariamente realizar la selección de características de ingeniería o característica. Si es necesario o no depende de los datos se han o recopilar, el algoritmo que elija y el objetivo de la ensayo.
 
