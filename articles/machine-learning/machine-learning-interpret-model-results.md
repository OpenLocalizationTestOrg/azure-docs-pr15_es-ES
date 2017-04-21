<properties
    pageTitle="Interpretar los resultados del modelo de aprendizaje de equipo | Microsoft Azure"
    description="Cómo elegir el parámetro óptima para un algoritmo de uso y visualizar los resultados del modelo de puntuación."
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
    ms.author="bradsev" />


# <a name="interpret-model-results-in-azure-machine-learning"></a>Interpretar los resultados del modelo de aprendizaje del equipo de Azure

En este tema se explica cómo visualizar e interpretar los resultados de la predicción de Azure máquina aprendizaje Studio. Después de haber capacitado un modelo y realizar predicciones encima de ellas ("puntuación del modelo"), debe comprender e interpretar el resultado de predicción.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Existen cuatro tipos principales de modelos en aprendizaje de Azure máquina de aprendizaje:

* Clasificación
* Clústeres
* Regresión.
* Sistemas de recomendación

Los módulos utilizados predicción sobre estos modelos son:

* [Puntuación modelo] [ score-model] módulo de clasificación y regresión.
* [Asignar a clústeres] [ assign-to-clusters] módulo para clústeres
* [Puntuación recomendación de caja de cerillas] [ score-matchbox-recommender] para sistemas de recomendación

Este documento explica cómo interpretar los resultados de predicción para cada uno de estos módulos. Para obtener información general de estos módulos, vea [cómo elegir parámetros para optimizar los algoritmos en Azure el aprendizaje](machine-learning-algorithm-parameters-optimize.md).

Este tema trata interpretación predicción pero no la evaluación de modelo. Para obtener más información acerca de cómo evaluar el modelo, consulte [cómo evaluar el rendimiento del modelo de aprendizaje del equipo de Azure](machine-learning-evaluate-model-performance.md).

Si está familiarizado con el aprendizaje Azure y necesita ayuda para crear un ensayo simple para empezar, vea [crear un ensayo simple en Azure máquina aprendizaje Studio](machine-learning-create-experiment.md) en Azure máquina aprendizaje Studio.

## <a name="classification"></a>Clasificación ##
Hay dos subcategorías de problemas de clasificación:

* Problemas con solo dos clases (clasificación de clase de dos o binario)
* Problemas con más de dos clases (clase múltiples clasificación)

Aprendizaje de máquina Azure tiene distintos módulos tratar cada uno de estos tipos de clasificación, pero los métodos para interpretar los resultados de la predicción son similares.

### <a name="two-class-classification"></a>Clasificación de clase de dos###
**Ensayo de ejemplo**

Un ejemplo de un problema de clasificación de clase de dos es la clasificación de flores iris. La tarea es clasificar flores iris en función de sus características. El conjunto de datos de Iris proporcionado en Azure el aprendizaje es un subconjunto del [conjunto de datos de Iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) populares que contiene instancias de sólo dos especies de flores (clases 0 y 1). Existen cuatro características para cada flor (longitud sepal, sepal, longitud de pétalo, ancho y de ancho pétalo).

![Captura de pantalla de ensayo iris](./media/machine-learning-interpret-model-results/1.png)

Figura 1. Ensayo de problema de clasificación de clase de dos iris

Se ha realizado un ensayo para solucionar este problema, tal como se muestra en la figura 1. Un modelo de árbol de decisión aumentada de clase de dos se ha capacitado y puntuación. Ahora puede visualizar los resultados de predicción del [Modelo de puntuación] [ score-model] módulo haciendo clic en el puerto de salida del [Modelo de puntuación] [ score-model] módulo y, a continuación, haciendo clic en **Ver**.

![Módulo de modelo de puntuación](./media/machine-learning-interpret-model-results/1_1.png)

Esto incorpora los resultados de la puntuación tal como se muestra en la figura 2.

![Resultados de ensayo de clasificación de clase de dos iris](./media/machine-learning-interpret-model-results/2.png)

Figura 2. Visualizar un resultado de puntuación modelo de clasificación de clase de dos

**Interpretación del resultado**

Hay seis columnas en la tabla de resultados. Las cuatro columnas izquierdas son las cuatro características. Las columnas de derecha dos etiquetas de puntuación y probabilidades de puntuación, son los resultados de predicción. La columna de probabilidades de puntuación muestra la probabilidad de que una flor pertenece a la clase positiva (clase 1). Por ejemplo, el primer número de la columna (0.028571) significa que hay es 0.028571 probabilidad de que la primera flor pertenece a la clase 1. La columna de etiquetas de puntuación muestra la clase prevista para cada flor. Esto se basa en la columna de probabilidades de puntuación. Si la probabilidad de una flor puntuada es mayor que 0,5, se prevé como clase 1. En caso contrario, se prevé como clase 0.

**Publicación de servicios Web**

Tras han comprender los resultados de la predicción y consideren sonido, el ensayo se puede publicar como un servicio web para que pueda implementar en varias aplicaciones y llamar para obtener las predicciones de clase en cualquier flor iris nuevo. Para obtener información sobre cómo cambiar un ensayo formación en un ensayo puntuación y publicar como un servicio web, consulte [publicar el servicio web de aprendizaje del equipo de Azure](machine-learning-walkthrough-5-publish-web-service.md). Este procedimiento le ofrece un ensayo puntuación tal como se muestra en la figura 3.

![Captura de pantalla de puntuación ensayo](./media/machine-learning-interpret-model-results/3.png)

Figura 3. Puntuación el ensayo de problema de clasificación de clase de dos iris

Ahora debe configurar la entrada y salida para el servicio web. La entrada es el puerto de entrada correcto del [Modelo de puntuación][score-model], que es la flor Iris características de entrada. La elección de los resultados depende de si está interesado en la clase prevista (etiqueta puntuado), la probabilidad puntuada o ambos. En este ejemplo, se supone que está interesado en ambos. Para seleccionar las columnas de salida deseado, use una [Seleccionar columnas de conjunto de datos] [ select-columns] módulo. Haga clic en [Seleccionar columnas de conjunto de datos][select-columns], haga clic en **iniciar el selector de columna**y seleccione **Las etiquetas de puntuación** y **Las probabilidades de puntuación**. Después de configurar el puerto de salida de [Seleccionar columnas de conjunto de datos] [ select-columns] y volverlo a ejecutar, estará listo para publicar el ensayo puntuación como un servicio web haciendo clic en **Publicar el servicio de WEB**. El ensayo final es similar a la figura 4.

![El ensayo de clasificación de clase de dos iris](./media/machine-learning-interpret-model-results/4.png)

Figura 4. Ensayo puntuación final de un problema de clasificación de clase de dos iris

Después de ejecutar el servicio web y escriba algunos valores de la característica de instancia de prueba, el resultado devuelve dos números. El primer número es la etiqueta puntuada y la segunda es la probabilidad califiquen. Este flor es pronosticado como clase 1 con probabilidad 0.9655.

![Probar interpretar modelo de puntuación](./media/machine-learning-interpret-model-results/4_1.png)

![Resultados de la prueba de puntuación](./media/machine-learning-interpret-model-results/5.png)

Figura 5. Resultado del servicio Web de clasificación de clase de dos iris

### <a name="multi-class-classification"></a>Clasificación de clase múltiples
**Ensayo de ejemplo**

En esta prueba, realizar una tarea de reconocimiento de letra como un ejemplo de clasificación multiclass. Clasificador intenta predecir una letra determinada (clase) en función de algunos valores de atributo escritos a mano extraídos de las imágenes escritos a mano.

![Ejemplo de reconocimiento de letra](./media/machine-learning-interpret-model-results/5_1.png)

En los datos de entrenamiento, hay 16 características extraídas de imágenes de letras escritas a mano. Las 26 letras del formulario nuestras 26 clases. Figura 6 muestra un ensayo que entrenar a un modelo de clasificación multiclass para el reconocimiento de letra y predecir en el mismo conjunto en un conjunto de datos de prueba de funciones.

![Ensayo de multiclass clasificación de reconocimiento de letra](./media/machine-learning-interpret-model-results/6.png)

Figura 6. Ensayo de problema de clasificación multiclass de reconocimiento de letra

Visualizar los resultados del [Modelo de puntuación] [ score-model] módulo haciendo clic en el puerto de salida de [Modelo de puntuación] [ score-model] módulo y, a continuación, haga clic en **Ver**, debería ver contenido tal como se muestra en la figura 7.

![Resultados del modelo de puntuación](./media/machine-learning-interpret-model-results/7.png)

Figura 7. Visualizar los resultados del modelo de puntuación en una clasificación de clase múltiples

**Interpretación del resultado**

Las columnas de 16 izquierdas representan los valores de la característica del conjunto de prueba. Las columnas con nombres como puntuación probabilidades de clase "XX" solo como la columna de probabilidades de puntuación en el caso de clase de dos. Se muestra la probabilidad de que se encuentre la entrada correspondiente a una determinada categoría. Por ejemplo, para la primera entrada, es 0.003571 probabilidad de que sea una "A" 0.000451 probabilidad de que sea una "B" y así sucesivamente. La última columna (rótulos de puntuación) es igual a etiquetas de puntuación en el caso de clase de dos. Selecciona la clase con la mayor probabilidad califiquen como la clase prevista de la entrada correspondiente. Por ejemplo, para la primera entrada, la etiqueta puntuada es "F", ya que tiene la mayor probabilidad sea un "F" (0.916995).

**Publicación de servicios Web**

También puede obtener la etiqueta puntuada para cada entrada y la probabilidad de la etiqueta califiquen. La lógica básica es encontrar la probabilidad mayor entre todas las probabilidades califiquen. Para ello, debe usar la [Secuencia de comandos de ejecutar R] [ execute-r-script] módulo. El código de R se muestra en la figura 8 y el resultado de la ensayo se muestra en la figura 9.

![Ejemplo de código de R](./media/machine-learning-interpret-model-results/8.png)

Figura 8. Código de R para extraer las etiquetas de puntuación y las probabilidades asociadas de las etiquetas

![Resultado de ensayo](./media/machine-learning-interpret-model-results/9.png)

Figura 9. Ensayo puntuación final del problema de clasificación multiclass reconocimiento de letra

Después de publicar y ejecutar el servicio web y escriba algunos valores de la característica de entrada, el resultado devuelto aspecto figura 10. Esta carta escritos a mano, con sus características de 16 extraídos, se pronosticados sea "T" con probabilidad 0.9715.

![Módulo de puntuación interpretación de prueba](./media/machine-learning-interpret-model-results/9_1.png)

![Resultado de prueba](./media/machine-learning-interpret-model-results/10.png)

Figura 10. Resultado del servicio Web de clasificación multiclass

## <a name="regression"></a>Regresión.

Problemas de regresión son diferentes de los problemas de clasificación. En un problema de clasificación, que intenta predecir clases separadas, como qué clase una flor iris pertenece a. Pero, como puede ver en el siguiente ejemplo de un problema de regresión, que intenta predecir una variable continua, como el precio de un coche.

**Ensayo de ejemplo**

Usar la predicción de precio automóviles como el ejemplo de regresión. Está intentando predecir el precio de un coche en función de sus características, incluidos la creación, tipo de combustible, tipo de cuerpo y rueda de unidad. El ensayo se muestra en la figura 11.

![Ensayo de regresión precio automóviles](./media/machine-learning-interpret-model-results/11.png)

Figura 11. Ensayo de precio automóvil regresión problema

Visualizar el [Modelo de puntuación] [ score-model] módulo, el resultado es similar a la figura 12.

![Puntuación resultados para el problema de predicción de precio automóviles](./media/machine-learning-interpret-model-results/12.png)

Figura 12. Resultado final para el problema de predicción de precio automóviles

**Interpretación del resultado**

Etiquetas puntuadas es la columna de resultado de este resultado puntuación. Los números son el precio previsto para cada coche.

**Publicación de servicios Web**

Puede publicar el ensayo de regresión en un servicio web y llámelo predicción de precio automóvil del mismo modo que en el caso de uso de la clasificación de clase de dos.

![La puntuación de ensayo para el problema de regresión precio automóviles](./media/machine-learning-interpret-model-results/13.png)

Figura 13. Ensayo de un problema de regresión automóvil precio de puntuación

Ejecuta el servicio web, el resultado devuelto es similar a la figura 14. El precio previsto para este coche es $15,085.52.

![Módulo de puntuación interpretación de prueba](./media/machine-learning-interpret-model-results/13_1.png)

![Resultados de módulo de puntuación](./media/machine-learning-interpret-model-results/14.png)

Figura 14. Resultado del servicio Web de un problema de regresión precio automóviles

## <a name="clustering"></a>Clústeres

**Ensayo de ejemplo**

Vamos a utilizar el conjunto de datos de Iris de nuevo para crear un clúster ensayo. Aquí puede filtrar las etiquetas de clase en el conjunto de datos para que solo tiene características y que puede usar para clústeres. En este iris caso de uso, especifique el número de grupos en dos durante el proceso de aprendizaje, lo que significa que haría clúster las flores en dos clases. El ensayo se muestra en la figura 15.

![Experimentar problemas de agrupación de iris](./media/machine-learning-interpret-model-results/15.png)

Figura 15. Experimentar problemas de agrupación de iris

Clústeres difiere de clasificación en que el conjunto de datos de entrenamiento no tienen etiquetas de verdad tierra por sí mismo. Las instancias de conjunto de datos de formación en clústeres distintos de grupos de clústeres. Durante el proceso de aprendizaje, el modelo de los nombres de las entradas por las diferencias entre las características de aprendizaje. Después de eso, el modelo capacitado puede usarse para clasificar las entradas futuras. Existen dos partes del resultado que nos interesan dentro de un problema de agrupamiento. La primera parte es etiquetar el conjunto de datos de formación y la segunda es la clasificación de un nuevo conjunto de datos con el modelo capacitado.

La primera parte del resultado pueden visualizarse haciendo clic en el puerto de salida izquierda del [Modelo de clústeres tren] [ train-clustering-model] y, a continuación, haga clic en **Ver**. La visualización se muestra en la figura 16.

![Resultado de agrupación](./media/machine-learning-interpret-model-results/16.png)

Figura 16. Visualizar clústeres resultado para el conjunto de datos de aprendizaje

El resultado de la segunda parte, clústeres nuevas entradas con el modelo clúster capacitado, se muestra en la figura 17.

![Visualizar clústeres resultado](./media/machine-learning-interpret-model-results/17.png)

Figura 17. Visualizar clúster resultado en un nuevo conjunto de datos

**Interpretación del resultado**

Aunque los resultados de las dos partes provienen de ensayo diferentes fases, que tienen el mismo aspecto y se interpretan de la misma manera. Las primeras cuatro columnas son características. La última columna, las asignaciones, es el resultado de predicción. Las entradas que se le asignadas al mismo número están diseñadas para ser del mismo clúster, es decir, que comparten similitudes de algún modo (este ensayo utiliza la métrica de distancia euclidiano predeterminada). Dado que especifica el número de clústeres 2, las entradas en las asignaciones están etiquetadas 0 o 1.

**Publicación de servicios Web**

Puede publicar el ensayo agrupación en un servicio web y llamar para clústeres predicciones caso de uso de la misma forma que en la clasificación de clase de dos.

![La puntuación de ensayo para el problema de agrupación de iris](./media/machine-learning-interpret-model-results/18.png)

Figura 18. Puntuación ensayo de un problema clúster de iris

Después de ejecutar el servicio web, el resultado devuelto es similar a la figura 19. Este flor es pronosticado en clúster 0.

![Prueba interpretar módulo puntuación](./media/machine-learning-interpret-model-results/18_1.png)

![Resultado del módulo de puntuación](./media/machine-learning-interpret-model-results/19.png)

Figura 19. Resultado del servicio Web de clasificación de clase de dos iris

## <a name="recommender-system"></a>Sistema de recomendación
**Ensayo de ejemplo**

Para sistemas de recomendación, puede usar el problema de recomendación restaurante como ejemplo: puede recomendar restaurantes en función de su historial de clasificación de los clientes. Los datos de entrada está compuesto por tres partes:

* Clasificaciones de restaurante de los clientes
* Datos de la característica de cliente
* Datos de la característica de restaurante

Hay varias cosas que podemos hacer con la [Recomendación de caja de cerillas tren] [ train-matchbox-recommender] módulo en el aprendizaje Azure:

- Predecir clasificaciones para un usuario determinado y un elemento
- Recomendamos elementos a un usuario determinado
- Buscar usuarios de un usuario determinado
- Buscar elementos relacionados con un elemento determinado

Puede elegir qué desea hacer mediante la selección de las cuatro opciones en el menú **tipo de predicción de recomendación** . Aquí puede recorrer los cuatro escenarios.

![Recomendación de caja de cerillas](./media/machine-learning-interpret-model-results/19_1.png)

Un ensayo de aprendizaje de Azure equipo típico para un sistema de recomendación es similar a la figura 20. Para obtener información sobre cómo usar los módulos de sistema de recomendación, consulte [recomendación de caja de cerillas tren] [ train-matchbox-recommender] y [recomendación de caja de cerillas puntuación][score-matchbox-recommender].

![Ensayo del sistema de recomendación](./media/machine-learning-interpret-model-results/20.png)

Figura 20. Ensayo del sistema de recomendación

**Interpretación del resultado**

**Predecir clasificaciones para un usuario determinado y un elemento**

Seleccionando **La predicción de clasificación** en **tipo de predicción de recomendación**, se plantea el sistema de recomendación predecir la clasificación de un usuario determinado y un elemento. La visualización de la [Recomendación de caja de cerillas puntuación] [ score-matchbox-recommender] salida es similar a la figura 21.

![Resultados del sistema recomendación--predicción de clasificación de puntuación](./media/machine-learning-interpret-model-results/21.png)

Figura 21. Visualizar el resultado de la puntuación del sistema recomendación--predicción de clasificación

Las dos primeras columnas son los pares de elemento de usuario proporcionados por los datos de entrada. La tercera columna es la clasificación prevista de un usuario para un elemento determinado. Por ejemplo, en la primera fila, el cliente U1048 se prevé que restaurante tasa 135026 como 2.

**Recomendamos elementos a un usuario determinado**

Seleccionando **Recomendación del elemento** en el **tipo de predicción de recomendación**, está solicitando al sistema de recomendación que recomienda elementos a un usuario determinado. El último parámetro para elegir en este escenario es *recomendado artículo selección*. La opción **De la clasificación de elementos (para la evaluación de modelo)** está principalmente para la evaluación de modelo durante el proceso de aprendizaje. Para esta fase predicción, elegimos **De todos los elementos**. La visualización de la [Recomendación de caja de cerillas puntuación] [ score-matchbox-recommender] salida es similar a la figura 22.

![Resultado de la puntuación de sistema de recomendación: recomendación de elemento](./media/machine-learning-interpret-model-results/22.png)

Figura 22. Visualizar los resultados de la puntuación del sistema de recomendación: recomendación del elemento

La primera de seis columnas representa el usuario determinado identificadores recomendar elementos, como proporcionadas por los datos de entrada. Las cinco columnas representan los elementos que se recomienda para el usuario en orden descendente de relevancia. Por ejemplo, en la primera fila, el restaurante más recomendado para cliente U1048 es 134986, seguido de 135018, 134975, 135021 y 132862.

**Buscar usuarios de un usuario determinado**

Seleccionando **Usuarios relacionados** en **tipo de predicción de recomendación**, está solicitando al sistema de recomendación para buscar usuarios relacionados a un usuario determinado. Usuarios relacionados son los usuarios que tienen preferencias similares. El último parámetro para elegir en este escenario es la *selección de usuario relacionado*. La opción **De los usuarios que clasificación elementos (para la evaluación de modelo)** está principalmente para la evaluación de modelo durante el proceso de aprendizaje. Elija **De todos los usuarios** de esta fase de predicción. La visualización de la [Recomendación de caja de cerillas puntuación] [ score-matchbox-recommender] resultado aspecto figura 23.

![Resultado de la puntuación de sistema de recomendación: usuarios relacionados](./media/machine-learning-interpret-model-results/23.png)

Figura 23. Visualizar los resultados de la puntuación del sistema de recomendación: usuarios relacionados

La primera de seis columnas se muestra al usuario determinado que identificadores necesarias para encontrar usuarios relacionados, proporcionados por los datos de entrada. Las cinco columnas almacenan los usuarios previstos relacionados del usuario en orden descendente de relevancia. Por ejemplo, en la primera fila, el cliente más relevante para cliente U1048 es U1051, seguido de U1066, U1044, U1017 y U1072.

**Buscar elementos relacionados con un elemento determinado**

Seleccionando **Elementos relacionados** en **tipo de predicción de recomendación**, se plantea el sistema de recomendación para buscar elementos relacionados a un elemento determinado. Elementos relacionados son los elementos más probable que se le ha gustado por el mismo usuario. El último parámetro para elegir en este escenario es la *selección del elemento relacionado*. La opción **De la clasificación de elementos (para la evaluación de modelo)** está principalmente para la evaluación de modelo durante el proceso de aprendizaje. Elegimos **De todos los elementos** de esta fase de predicción. La visualización de la [Recomendación de caja de cerillas puntuación] [ score-matchbox-recommender] salida es similar a la figura 24.

![Resultado de la puntuación de sistema de recomendación: elementos relacionados](./media/machine-learning-interpret-model-results/24.png)

Figura 24. Visualizar los resultados de la puntuación del sistema de recomendación--elementos relacionados

La primera de seis columnas representa el elemento determinado identificadores necesarias para buscar elementos relacionados, como proporcionadas por los datos de entrada. Las cinco columnas almacenan los elementos relacionados previstos del elemento en orden descendente en términos de relevancia. Por ejemplo, en la primera fila, el elemento más relevante para el elemento 135026 es 135074, seguido de 135035, 132875, 135055 y 134992.

**Publicación de servicios Web**

El proceso de publicación estos experimentación como servicios web obtener predicciones es similar para cada uno de los cuatro escenarios. Aquí se toman el segundo escenario (recomienda elementos a un usuario determinado) como un ejemplo. Puede seguir el mismo procedimiento con los otros tres.

Guardar el sistema de recomendación capacitado como modelo capacitado y filtrar los datos de entrada a una columna de identificador de usuario único como se ha solicitado, puede conectar el ensayo como en la figura 25 y publicar como un servicio web.

![Ensayo del problema de recomendación restaurante de puntuación](./media/machine-learning-interpret-model-results/25.png)

Figura 25. Ensayo del problema de recomendación restaurante de puntuación

Ejecuta el servicio web, el resultado devuelto es similar a la figura 26. Los cinco restaurantes recomendados para usuario U1048 son 134986, 135018, 134975, 135021 y 132862.

![Ejemplo de servicio del sistema de recomendación](./media/machine-learning-interpret-model-results/25_1.png)

![Resultados de ejemplo ensayo](./media/machine-learning-interpret-model-results/26.png)

Figura 26. Resultado del servicio Web de problema de recomendación restaurante


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
