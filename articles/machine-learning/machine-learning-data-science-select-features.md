<properties
    pageTitle="Característica de selección en el proceso de ciencias grupo datos | Microsoft Azure" 
    description="Explica el propósito de selección de características y se proporcionan ejemplos de su rol en el proceso de mejora de datos de aprendizaje del equipo."
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


# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Selección de características en el proceso de ciencias de datos de equipo (TDSP)

Este artículo explica los efectos de la selección de características y proporciona ejemplos de su rol en el proceso de mejora de datos de aprendizaje del equipo. Estos ejemplos se dibujan Studio de aprendizaje del equipo de Azure. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Este tema explica el propósito de selección de características y proporciona ejemplos de su rol en el proceso de mejora de datos de aprendizaje. Estos ejemplos se dibujan Studio de aprendizaje del equipo de Azure. 

La ingeniería y selección de características es una parte de la TDSP descrito en la [¿Qué es el proceso de ciencias de datos de grupo?](data-science-process-overview.md). Ingeniería de la característica y selección son partes del paso **desarrollar características** de la TDSP.

* **ingeniería de la característica**: este proceso intenta crear características adicionales de relevantes de las características existentes sin formato de los datos y para aumentar la capacidad predictiva el algoritmo de aprendizaje.

* **selección de características**: este proceso selecciona el subconjunto de clave de las características de datos original de un intento de reducir las dimensiones del problema de aprendizaje.

Normalmente se aplica primero **ingeniería de la característica** para generar características adicionales y, a continuación, se realiza el paso de **selección de características** para eliminar características relevante, redundantes o muy relacionadas.


## <a name="filtering-features-from-your-data---feature-selection"></a>Características de filtrado de los datos: selección de características 

Selección de funciones es un proceso que normalmente se aplican de construcción de conjuntos de datos de formación para el modelado de predicción tareas como tareas de clasificación o regresión. El objetivo es seleccionar un subconjunto de las características del conjunto de datos original que reducir sus dimensiones mediante un conjunto mínimo de características para representar la cantidad máxima de varianza en los datos. Este subconjunto de características son, a continuación, las características sola incluirse entrenar el modelo. Selección de características tiene dos propósitos principales.

* En primer lugar, selección de características a menudo aumenta la precisión de clasificación eliminando relevante y redundantes o altamente correlación características.
* En segundo término, disminuye el número de características que hace que el proceso de formación de modelo más eficaz. Esto es especialmente importante para los estudiantes que son costosos de entrenar como máquinas vector de soporte técnico.

Aunque la selección de características de búsqueda reducir el número de características en el conjunto de datos utilizados para entrenar el modelo, no se suele conoce por el término "reducción de dimensión". Métodos de selección de la característica extraen un subconjunto de características originales de los datos sin convertirlos.  Métodos de reducción de dimensión utilizan funciones de ingeniería que pueden transformar las características originales y, por tanto, modificarlos. Análisis de componente Principal, análisis de correlación canónico y Singular descomposición valor son ejemplos de métodos de reducción de dimensiones.

Entre otras cosas, una categoría ampliamente aplicada de métodos de selección de la característica en un contexto controlado se denomina "selección de funciones de filtro con". Evaluar la correlación entre cada característica y el atributo de destino, estos métodos aplicarán una medida estadística para asignar una calificación a cada característica. Las características a continuación, se clasifican por la puntuación, que puede utilizarse para ayudarle a establecer el umbral para mantener o eliminar una característica específica. Las medidas estadísticas utilizadas en estos métodos y ejemplos de persona correlación, información mutua, la prueba de Chi cuadrada.

En Azure máquina aprendizaje Studio, existen módulos referente a la selección de características. Como se muestra en la siguiente ilustración, estos módulos incluyen [basada en el filtro de selección de características] [ filter-based-feature-selection] y [Análisis de Fisher lineal: discriminante][fisher-linear-discriminant-analysis].

![Ejemplo de selección de función](./media/machine-learning-data-science-select-features/feature-Selection.png)


Tenga en cuenta, por ejemplo, el uso de la [selección de característica basada en el filtro] [ filter-based-feature-selection] módulo. Con el fin de comodidad, seguiremos utilizando un ejemplo de minería de datos de texto descrito anteriormente. Supongamos que desea crear un modelo de regresión después de haber creado un conjunto de características de 256 a través de la [Función hash] [ feature-hashing] módulo, y que la variable de respuesta es "Col1" y representa un libro revisan clasificaciones comprendido entre 1 y 5. Mediante la configuración de "Método de resultados de la función" sea "Pearson correlación", "columna de destino" sea "Col1" y el "número de características que quiera" a 50. A continuación, el módulo de [selección de característica basada en el filtro] [ filter-based-feature-selection] se devolverá un conjunto de datos que contiene las características de 50 junto con el atributo de destino "Col1". La figura siguiente muestra el flujo de este ensayo y los parámetros de entrada que hemos descrito.

![Ejemplo de selección de función](./media/machine-learning-data-science-select-features/feature-Selection1.png)

La figura siguiente muestra los conjuntos de datos resultante. En cada característica es una puntuación basada en la correlación de Pearson entre sí y el atributo de destino "Col1". Se mantienen las características con la puntuación superior.

![Ejemplo de selección de función](./media/machine-learning-data-science-select-features/feature-Selection2.png)

Los resultados de las características seleccionadas correspondientes se muestran en la siguiente ilustración.

![Ejemplo de selección de función](./media/machine-learning-data-science-select-features/feature-Selection3.png)

Al aplicar esta [selección de característica basada en el filtro] [ filter-based-feature-selection] módulo, 50 fuera de 256 funciones se seleccionan porque tienen las características más relacionadas con la variable de destino "Col1", según el método de puntuación "Pearson correlación".

## <a name="conclusion"></a>Conclusión
Ingeniería de la característica y selección de características son dos comúnmente de ingeniería y características seleccionadas aumentan la eficiencia del proceso de aprendizaje que intenta extraer la información clave contenida en los datos. También mejoran la potencia de estos modelos para clasificar los datos de entrada con precisión y predecir resultados de interés de manera más sólida. También pueden combinar característica ingeniería y selección para que el aprendizaje matemáticamente más manejable. Para ello, mejorar y, a continuación, reducir el número de características necesitado para calibre o formar a un modelo. Matemáticamente hablando, las características seleccionadas para entrenar el modelo son un conjunto mínimo de variables independientes que explican los patrones de los datos y, a continuación, predecir resultados correctamente.

Tenga en cuenta que no siempre es necesariamente realizar la selección de características de ingeniería o característica. Si es necesario o no depende de los datos se han o recopilar, el algoritmo que elija y el objetivo de la ensayo.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 
