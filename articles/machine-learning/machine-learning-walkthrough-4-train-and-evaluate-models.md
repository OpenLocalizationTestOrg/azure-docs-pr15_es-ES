<properties
    pageTitle="Paso 4: Aprendizaje y evaluar los modelos de analíticos predictivos | Microsoft Azure"
    description="Paso 4 de la desarrollar un tutorial solución predictiva: tren, puntuación y evaluar varios modelos en Azure máquina aprendizaje Studio."
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
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Tutorial paso 4: Aprendizaje y evaluar los modelos de analíticos predictivos

Este tema contiene el cuarto paso del tutorial, [desarrollar una solución de analítica predictiva en aprendizaje de máquina de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Crear un área de trabajo de aprendizaje de equipo](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Cargar datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Crear un nuevo ensayo](machine-learning-walkthrough-3-create-new-experiment.md)
4.  **Entrenar y evaluar los modelos**
5.  [Implementar el servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Una de las ventajas de usar Azure máquina aprendizaje Studio para crear modelos de aprendizaje del equipo es la capacidad de probar más de un tipo de modelo a la vez en un ensayo y comparar los resultados. Este tipo de experimentación le ayuda a encontrar la mejor solución para su problema.

En la misma que nos estamos desarrollar en este tutorial, se creará dos tipos de modelos y, a continuación, comparar los resultados de puntuación para decidir qué algoritmo queremos usar en nuestra prueba final.  

Existen varios modelos de que disponemos. Para ver los modelos disponibles, expanda el nodo de **Aprendizaje** en la paleta de módulo y, a continuación, expanda **Inicializar modelo** y los nodos debajo de él. Para el propósito de este ensayo, seleccione el equipo de Vector de soporte técnico (SVM) y los módulos de clase de dos árboles de decisión aumenta.    

> [AZURE.TIP] Para obtener ayuda para decidir qué algoritmo de aprendizaje de máquina que mejor adapte el problema concreto que intenta solucionar, consulte [cómo elegir algoritmos de aprendizaje de Microsoft Azure máquina](machine-learning-algorithm-choice.md).

##<a name="train-the-models"></a>Entrenar a los modelos
En primer lugar, vamos a configurar el modelo de árbol de decisión incremento:  

1.  Buscar el [Árbol de decisión de clase de dos aumenta] [ two-class-boosted-decision-tree] módulo en la paleta de módulo y arrástrelo hasta el lienzo.
2.  Buscar el [Modelo de tren] [ train-model] módulo, arrástrelo hasta el lienzo y, a continuación, conecte el resultado del módulo de árbol de decisión aumentada al puerto de entrada izquierdo ("no capacitado modelo") del [Modelo de tren] [ train-model] módulo.
    
    El [Árbol de decisión de clase de dos aumenta] [ two-class-boosted-decision-tree] módulo inicializa el modelo genérico y el [Modelo de entrenar] [ train-model] usa datos de formación para entrenar el modelo. 
     
3.  Conecte la salida izquierda ("conjunto de resultados") de la izquierda [Ejecutar Script de R] [ execute-r-script] módulo a la derecha de entrada puerto ("Dataset") del [Modelo de tren] [ train-model] módulo.

    > [AZURE.TIP] No necesitamos dos de las entradas y uno de los resultados de la [Secuencia de comandos de ejecutar R] [ execute-r-script] módulo para este ensayo, por lo que dejar no adjunta. 

4.  Seleccione el [Modelo de tren] [ train-model] módulo. En el panel **Propiedades** , haga clic en **el selector de columna de inicio**, seleccione **Todos los tipos** en la lista desplegable hacia abajo en **Columnas disponibles** y escriba "Riesgo de crédito" en el campo de texto. Seleccione **Todos los tipos** en la lista desplegable en las **Columnas seleccionadas**. Seleccione "Riesgo de crédito" y haga clic en el botón de flecha resaltado para desplazarse a las **Columnas seleccionadas**. 
5.  Haga clic en **Guardar**.


Esta parte de la ensayo ahora tiene un aspecto similar a este:  

![Un modelo de aprendizaje][1]

A continuación, se configure el modelo SVM.  

En primer lugar, una breve explicación sobre SVM. Árboles de decisión aumentada funcionan mejor con características de cualquier tipo. Sin embargo, dado que el módulo SVM genera un clasificador lineal, el modelo que genera tiene el error de prueba mejor cuando todas las características numéricas tienen la misma escala. Para convertir todas las características numéricas en la misma escala, usamos una transformación "Tanh" (con los [Datos normalizar] [ normalize-data] módulo.) Esto transforma los números en el intervalo [0,1]. Funciones de cadena se convierten por el módulo SVM características por categorías y, a continuación, las características de 0 o 1 binario, así que no necesitamos transformar manualmente las funciones de cadena. Además, no queremos transformar el riesgo de crédito columna (21) - es numérico, pero es el valor que estamos formación el modelo de predecir, por lo que necesitamos dejarla tal cual.  

Para configurar el modelo SVM, haga lo siguiente:

1.  Buscar la [Clase dos soporte Vector máquina] [ two-class-support-vector-machine] módulo en la paleta de módulo y arrástrelo hasta el lienzo.
2.  Haga clic en el [Modelo de tren] [ train-model] módulo, haga clic en **Copiar**y, a continuación, haga clic en el lienzo y seleccione **Pegar**. La copia del [Modelo de tren] [ train-model] módulo tiene la misma selección de columna como el original.
3.  Conecte el resultado del módulo SVM al puerto entrado izquierdo ("no capacitado modelo") de la segunda [Tren modelo] [ train-model] módulo.
4.  Buscar los [Datos normalizar] [ normalize-data] módulo y arrástrelo hasta el lienzo.
5.  Conectar la entrada de este módulo con los resultados de la izquierda de la izquierda [Ejecutar Script de R] [ execute-r-script] módulo (tenga en cuenta que el puerto de salida de un módulo que esté conectado a más de un módulo de otro).
6.  Conecte el puerto de salida izquierda ("transformación de conjunto de datos") de los [Datos de normalizar] [ normalize-data] módulo a la derecha de entrada puerto ("Dataset") de la segunda [Tren modelo] [ train-model] módulo.
7.  En el panel de **Propiedades** para los [Datos normalizar] [ normalize-data] módulo, seleccione **Tanh** para el parámetro de **método de transformación** .
8.  Haga clic en **iniciar el selector de columna**, no seleccione "columnas" para **Empezar con**, seleccione **incluir** en la lista desplegable de la primera, seleccione el **tipo de columna** en la lista desplegable de la segunda y seleccione **numérico** en la lista desplegable de terceros. Especifica que todas las columnas numéricas (y numérico sólo) se transforman.
9.  Haga clic en el signo más (+) a la derecha de esta fila: esta forma crea una fila de listas desplegables. Seleccione **Excluir** en la lista desplegable de la primera, seleccione los **nombres de columna** en la segundo lista desplegable y haga clic en el campo de texto y seleccione "Riesgo de crédito" de la lista de columnas. Especifica que se debe omitir la columna de riesgo de crédito (debemos hacerlo porque esta columna es numérica y en caso contrario, se podría transformar).
10. Haga clic en **Aceptar**.  


Los [Datos de normalizar] [ normalize-data] módulo ahora está establecido para realizar una transformación Tanh en todas las columnas numéricas excepto la columna de riesgo de crédito.  

Esta parte de nuestra ensayo ahora debe ser similar al siguiente:  

![El segundo modelo de formación][2]  

##<a name="score-and-evaluate-the-models"></a>Puntuación y evaluar los modelos

Usamos los datos de pruebas que se dividen los [Datos divididos] [ split] módulo de puntuación nuestros modelos capacitados. A continuación, podemos comparar los resultados de los dos modelos para ver que generó mejores resultados.  

1.  Buscar el [Modelo de puntuación] [ score-model] módulo y arrástrelo hasta el lienzo.
2.  Conectar el [Tren modelo] [ train-model] módulo que está conectado al [Árbol de decisión de clase de dos aumenta] [ two-class-boosted-decision-tree] módulo a la izquierda de entrada puerto del [Modelo de puntuación] [ score-model] módulo.
3.  Conecte el puerto de entrada correcto del [Modelo de puntuación] [ score-model] módulo a los resultados de la izquierda de la derecha [Ejecutar Script de R] [ execute-r-script] módulo.

    El [Modelo de puntuación] [ score-model] módulo puede tomar la información de crédito de los datos de pruebas, ejecute a través del modelo y comparar las predicciones genera el modelo con la columna de riesgo de crédito real de los datos de pruebas.

4.  Copiar y pegar el [Modelo de puntuación] [ score-model] módulo para crear una segunda copia o arrastre un nuevo módulo en el lienzo.
5.  Conecte el puerto de entrada izquierdo de este módulo en el modelo de SVM (es decir, conectar al puerto de salida del [Modelo de tren] [ train-model] módulo que está conectado a la [Clase dos soporte Vector máquina] [ two-class-support-vector-machine] módulo).
6.  Para el modelo SVM, hay que es igual a los datos de entrenamiento la misma transformación a los datos de prueba. Por lo tanto, copie y pegue los [Datos normalizar] [ normalize-data] módulo para crear una segunda copia y conectarse a los resultados de la izquierda de la derecha [Ejecutar Script de R] [ execute-r-script] módulo.
7.  Conecte el puerto de entrada correcto del [Modelo de puntuación] [ score-model] módulo a los resultados de la izquierda de los [Datos de normalizar] [ normalize-data] módulo.  

Para evaluar los resultados de puntuación dos, usamos un [Modelo de evaluar] [ evaluate-model] módulo.  

1.  Buscar el [Modelo de evaluar] [ evaluate-model] módulo y arrástrelo hasta el lienzo.
2.  Conecte el puerto de entrada izquierdo al puerto de salida del [Modelo de puntuación] [ score-model] módulo asociado con el modelo de árbol de decisión aumentada.
3.  Conecte el puerto de entrada de derecha a otro [Modelo de puntuación] [ score-model] módulo.  

Para ejecutar el ensayo, haga clic en el botón **Ejecutar** debajo del lienzo. Puede tardar unos minutos. Un indicador de giro en cada módulo de muestra que se está ejecutando y, a continuación, una marca de verificación verde cuando haya finalizado el módulo. Cuando todos los módulos tienen una marca de verificación, el ensayo ha terminado de ejecutarse.

El ensayo debe ahora el siguiente aspecto:  

![Evaluar ambos modelos][3]

Para comprobar los resultados, haga clic en el puerto de salida del [Modelo evaluar] [ evaluate-model] módulo y seleccione **Ver**.  

El [Modelo de evaluar] [ evaluate-model] módulo genera un par de curvas y mediciones que permiten comparar los resultados de los dos modelos califiquen. Puede ver los resultados como curvas receptor operador característica (rc), precisión y recuperación curvas o curvas de elevación. Datos adicionales que se muestran incluyen una matriz de confusiones acumulados valores para el área bajo la curva (AUC) y otras métricas. Puede cambiar el valor de umbral moviendo el control deslizante izquierdo o derecho y ver cómo afecta el conjunto de métricas.  

A la derecha del gráfico, haga clic en **el conjunto de datos de Scored** o **Scored conjunto de datos para comparar** para resaltar la curva asociada y mostrar las medidas asociadas a continuación. En la leyenda de las curvas, "Puntuación dataset" corresponde al puerto entrado izquierdo del [Modelo evaluar] [ evaluate-model] módulo: en este caso, éste es el modelo de árbol de decisión aumentada. "Conjunto de datos para comparar una puntuación" corresponde al puerto entrado derecha - el modelo SVM en nuestro caso. Al hacer clic en una de estas etiquetas, la curva para ese modelo se resalta y mostrar las mediciones correspondientes, tal como se muestra en la ilustración siguiente.  

![Curvas ROC para modelos][4]

Examinando estos valores, puede decidir qué modelo es más cercana a la que le da los resultados que está buscando. Puede volver atrás y trabajemos en su ensayo cambiando los valores en los distintos modelos. 

> [AZURE.TIP] Cada vez que ejecute el ensayo un registro de esa iteración se conserva en el historial de ejecutar. Puede ver estos iteraciones y vuelva a cualquiera de ellos, haciendo clic en **Ver el historial de ejecutar** debajo del lienzo. También puede hacer clic **Antes de ejecutar** en el panel de **Propiedades** para volver a la iteración precedente el que tiene abierto.
> 
Puede hacer una copia de cualquier iteración de su ensayo haciendo clic en **Guardar como** debajo del lienzo. Usar propiedades de **Resumen** y la **Descripción** del ensayo para mantener un registro de lo que ha intentado en su iteraciones de ensayo.

>  Para obtener más detalles, vea [Administrar experimentar iteraciones en Azure máquina aprendizaje Studio](machine-learning-manage-experiment-iterations.md).  


----------

**Siguiente: [implementar el servicio web](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
