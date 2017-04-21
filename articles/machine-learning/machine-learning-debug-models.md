<properties 
    pageTitle="Depurar el modelo en el aprendizaje Azure | Microsoft Azure" 
    description="Se explica cómo depurar su modelo de aprendizaje del equipo de Azure." 
    services="machine-learning"
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/09/2016" 
    ms.author="bradsev;garye" />

# <a name="debug-your-model-in-azure-machine-learning"></a>Depurar el modelo en el aprendizaje Azure

En este artículo se explica cómo depurar los modelos de aprendizaje de Microsoft Azure máquina. Más concretamente, cubre los posibles motivos por qué cualquiera de los siguientes dos escenarios de error puede producirse cuando se ejecuta un modelo de:

* el [Modelo de tren] [ train-model] módulo produce un error 
* el [Modelo de puntuación] [ score-model] módulo produce resultados incorrectos 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-throws-an-error"></a>Módulo de modelo de tren produce un error

![Image1](./media/machine-learning-debug-models/train_model-1.png)

El [Modelo de tren] [ train-model] módulo espera las siguientes entradas de 2:

1. El tipo de modelo de clasificación o regresión de la colección de modelos de aprendizaje del equipo de Azure
2. Los datos de entrenamiento con una columna de la etiqueta especificada. La columna etiqueta Especifica la variable de predecir. Se supone que el resto de las columnas que se incluyen son características.

Este módulo, produce un error en los siguientes casos:

1. La columna de la etiqueta se especifica incorrectamente porque más de una columna está seleccionada como etiqueta o se selecciona un índice de columna incorrecta. Por ejemplo, el segundo caso se aplicará si se utiliza un índice de columna de 30 con un conjunto de datos de entrada que tenía 25 solo columnas.

2. El conjunto de datos no contiene todas las columnas de la característica. Por ejemplo, si el conjunto de datos de entrada tiene sólo 1 columna, que está marcada como la columna de etiqueta, no sería características con la que se va a crear el modelo. En este caso, el [Modelo de tren] [ train-model] módulo, producirá un error.

3. El conjunto de datos de entrada (características o etiqueta) contienen infinito como un valor.


## <a name="score-model-module-does-not-produce-correct-results"></a>Módulo de modelo de puntuación no producir resultados correctos

![imagen2](./media/machine-learning-debug-models/train_test-2.png)

En un gráfico de formación y pruebas típico para supervisados de aprendizaje, los [Datos divididos] [ split] módulo divide el conjunto de datos original en dos partes: el elemento que se usa para entrenar el modelo y el elemento que se ha reservado para puntuación bien el modelo capacitado realiza en los datos no se han entrenamiento en. El modelo de formación, a continuación, se usa para los datos de prueba después de que los resultados se evalúan para determinar la precisión del modelo de la puntuación.

El [Modelo de puntuación] [ score-model] módulo requiere dos entradas:

1. Un resultado modelo capacitado [Tren modelo] [ train-model] módulo
2. Un conjunto de datos puntuación no que no se ha preparado el modelo en

Puede ocurrir que aunque el ensayo se realiza correctamente, el [Modelo de puntuación] [ score-model] módulo produce resultados incorrectos. Varios escenarios pueden hacer que esto suceda:

1. Si la etiqueta especificada es por categorías y un modelo de regresión está capacitado en los datos, un resultado incorrecto podría presentarse por el [Modelo de puntuación] [ score-model] módulo. Esto es porque regresión requiere una variable de respuesta continuo. En este caso debería ser más adecuado para usar un modelo de clasificación. 
2. Del mismo modo, si un modelo de clasificación está capacitado en un conjunto de datos con números de punto flotante en la columna etiqueta, se pueden producir resultados no deseados. Esto es porque clasificación requiere una variable independiente de respuesta que solo permite valores comprendidos sobre un conjunto finito y normalmente algo pequeño de clases.
3. Si el conjunto de datos puntuación no contiene todas las características que se usa para entrenar el modelo, el [Modelo de puntuación] [ score-model] producirá un error.
4. El [Modelo de puntuación] [ score-model] se produce ningún resultado correspondiente a una fila en el conjunto de datos de resultados que contiene un valor que falta o un valor infinito para cualquiera de sus características.
5. El [Modelo de puntuación] [ score-model] pueden producir resultados idénticos para todas las filas en el conjunto de datos de puntuación. Esto puede ocurrir, por ejemplo, cuando trate de clasificación mediante decisión bosques si se elige el número mínimo de muestras por nodo hoja sea más que el número de ejemplos de formación disponibles.


<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 
