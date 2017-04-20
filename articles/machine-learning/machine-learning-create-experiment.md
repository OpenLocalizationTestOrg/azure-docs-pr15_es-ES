<properties
    pageTitle="Un experimento simple en máquina de aprendizaje Studio | Microsoft Azure"
    description="Este tutorial de aprendizaje máquina le guiará a través de un experimento científico de datos fácil. Se podrá predecir el precio de un automóvil con un algoritmo de regresión."
    keywords="experimentar, regresión lineal, algoritmos de aprendizaje de la máquina, tutorial de aprendizaje de la máquina, técnicas de modelado predictivo, experimento científico de datos"
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
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="garye"/>

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Tutorial de aprendizaje de la máquina: crear el primer experimento científico de los datos en Azure máquina de aprendizaje Studio

Este tutorial de aprendizaje máquina le guiará a través de un experimento científico de datos fácil. Vamos a crear un modelo de regresión lineal que predice el precio de un automóvil en función de distintas variables tales como realizar y especificaciones técnicas. Para ello, utilizaremos Azure máquina Learning Studio para desarrollar y establecer una iteración en una simple predictiva analytics experimentar.

*Análisis predictivo* es un tipo de datos ciencia que utiliza los datos actuales para predecir resultados futuros. Para un ejemplo muy sencillo de análisis predictivo, vea ciencia de datos para principiantes de vídeo 4: [predecir una respuesta con un modelo simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (en tiempo de ejecución: 7:42).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>¿Cómo ayuda el equipo Studio de aprendizaje?

Máquina Learning Studio facilita configurar un experimento mediante arrastrar y colocar los módulos preprogramados con las técnicas de modelado de predicción. Para ejecutar el experimento y predecir una respuesta, utilizará máquina de aprendizaje Studio para *crear un modelo*, *Entrenar el modelo*y *puntuación y prueba del modelo*.

Introduzca la máquina Learning Studio: [https://studio.azureml.net](https://studio.azureml.net). Si has iniciado sesión en equipo aprendizaje Studio antes, haga clic en **iniciar sesión aquí**. De lo contrario, haga clic en **Registrarse** y elegir entre las opciones gratuitas y de pagadas.

Para obtener información general acerca de la máquina de aprendizaje Studio, consulte [¿Qué es Studio de aprendizaje de máquina?](machine-learning-what-is-ml-studio.md)

## <a name="five-steps-to-create-an-experiment"></a>Cinco pasos para crear un experimento

En este tutorial de aprendizaje de máquina, siga cinco pasos básicos para generar un experimento en la máquina de aprendizaje Studio con el fin de crear, entrenar y anotar su modelo:

- Crear un modelo
    - [Paso 1: Obtener datos]
    - [Paso 2: Preprocesar los datos]
    - [Paso 3: Definir funciones]
- Entrenar el modelo
    - [Paso 4: Elegir y aplicar un algoritmo de aprendizaje]
- Puntuación y probar el modelo
    - [Paso 5: Predecir los nuevos precios de automóviles]

[Paso 1: Obtener datos]: #step-1-get-data
[Paso 2: Preprocesar los datos]: #step-2-preprocess-data
[Paso 3: Definir funciones]: #step-3-define-features
[Paso 4: Elegir y aplicar un algoritmo de aprendizaje]: #step-4-choose-and-apply-a-learning-algorithm
[Paso 5: Predecir los nuevos precios de automóviles]: #step-5-predict-new-automobile-prices


## <a name="step-1-get-data"></a>Paso 1: Obtener datos

Hay una serie de conjuntos de datos de ejemplo incluida con Studio de aprendizaje de la máquina que puede elegir, y puede importar datos de muchos orígenes. Para este ejemplo, utilizaremos el conjunto de datos de ejemplo que se incluye, **datos de precio del automóvil (Raw)**.
Este conjunto de datos incluye entradas para un número de automóviles individuales, incluyendo información como la marca, modelo, especificaciones técnicas y precio.

1. Iniciar un experimento nuevo haciendo clic en **+ nueva** en la parte inferior de la ventana del estudio de aprendizaje de la máquina, seleccione **experimentar**y, a continuación, seleccione **Probar en blanco**. Seleccione el nombre del experimento de forma predeterminada en la parte superior del lienzo y cambiar su nombre por un nombre significativo, por ejemplo, la **predicción de precios del automóvil**.

2. A la izquierda del experimento lienzo es una paleta de conjuntos de datos y módulos. **Automóvil** del tipo en el cuadro de búsqueda en la parte superior de esta paleta para buscar el conjunto de datos etiqueta de **datos de precios de automóvil (Raw)**.

    ![Búsqueda de paleta][screen1a]

3. Arrastre el conjunto de datos al lienzo experimento.

    ![Conjunto de datos][screen1]

Para ver el aspecto de estos datos, haga clic en el puerto de salida en la parte inferior del automóvil dataset y, a continuación, seleccione **Ver**.

![Puerto de salida del módulo][screen1c]

Las variables del conjunto de datos aparecen como columnas y cada instancia de un automóvil aparece como una fila. La columna de la derecha (columna 26 y titulado "precio") es la variable de destino que vamos a tratar de predecir.

![Visualización del conjunto de datos][screen1b]

Cierre la ventana de visualización haciendo clic en la "**x**" en la esquina superior derecha.

## <a name="step-2-preprocess-data"></a>Paso 2: Preprocesar los datos

Un conjunto de datos normalmente requiere preprocesamiento antes de que se puedan analizar. Es posible que haya observado los valores que faltan en las columnas de varias filas. Estos valores que faltan deben limpiarse para que el modelo puede analizar los datos correctamente. En nuestro caso, se suprimirá todas las filas que tienen valores que faltan. Además, la columna **pérdidas normalizado** tiene una gran proporción de valores que faltan, por lo que se podrá excluir esa columna del modelo en conjunto.

> [AZURE.TIP] Los valores de entrada de datos que faltan de limpieza es un requisito previo para utilizar la mayoría de los módulos.

Primero se suprimirá la columna **pérdidas normalizado** y, a continuación, se quitará cualquier fila que faltan datos.

1. Escriba **Seleccionar columnas** en el cuadro de búsqueda en la parte superior de la paleta de módulo para buscar las [Columnas Seleccione conjunto de datos] [ select-columns] módulo y, a continuación, arrastre para el experimento de lienzo y conéctelo al puerto de salida del conjunto de datos de **datos de precios de automóvil (Raw)** . Este módulo nos permite seleccionar qué columnas de datos que queremos incluir o excluir en el modelo.

2. Seleccione las [Columnas Seleccione conjunto de datos] [ select-columns] módulo y haga clic en **iniciar el selector de columna** en el panel **Propiedades** .

    - A la izquierda, haga clic en **reglas**
    - **Comenzar con**, haga clic en **todas las columnas**. Esto indica [Seleccionar columnas en el conjunto de datos] [ select-columns] pase a través de todas las columnas (excepto los que vamos a excluir).
    - Desde los listas desplegables, seleccione **Excluir** y **nombres de columna**y, a continuación, haga clic dentro del cuadro de texto. Se muestra una lista de columnas. Seleccione **pérdidas normalizado**y se agregará al cuadro de texto.
    - Haga clic en el botón marca de verificación (Aceptar) para cerrar el selector de columna.

    ![Seleccionar columnas][screen3]

    El panel de propiedades para **Seleccionar columnas de conjunto de datos** indica que pasará por todas las columnas del conjunto de datos excepto **pérdidas normalizado**.

    ![Seleccionar columnas de propiedades de conjunto de datos][screen4]

    > [AZURE.TIP] Puede agregar un comentario a un módulo haciendo doble clic en el módulo y escribiendo texto. Esto puede ayudarle a ver de un vistazo lo que está haciendo el módulo en el experimento. En este caso, haga doble clic en las [Columnas Seleccione conjunto de datos] [ select-columns] módulo y escriba el comentario "excluyen pérdidas normalizado."

3. Arrastre los [Datos limpios falta] [ clean-missing-data] módulo para el experimento de lienzo y conéctelo a la [Seleccione columnas de Dataset] [ select-columns] módulo. En el panel **Propiedades** , seleccione **quitar toda la fila** en el **modo de limpieza** para limpiar los datos eliminando filas que tienen valores que faltan. Haga doble clic en el módulo y escriba el comentario "Quitar filas de valor falta."

    ![Propiedades de datos falta limpios][screen4a]

4. Ejecutar el experimento haciendo clic en **Ejecutar** en el lienzo del experimento.

Cuando termine el experimento, todos los módulos tienen una marca de verificación verde para indicar que ha finalizado correctamente. Observe también el estado **terminado ejecutando** en la esquina superior derecha.

![Primer experimento ejecutar][screen5]

Todo lo que hemos hecho en el experimento en este punto es limpiar los datos. Si desea ver el conjunto de datos limpio, haga clic en el puerto de salida izquierda de los [Datos limpios falta] [ clean-missing-data] módulo ("dataset limpio") y seleccione **Ver**. Observe que ya no se incluye la columna **pérdidas normalizada** y no hay ningún valor que falta.

Ahora que los datos están limpios, estamos listos para especificar qué características vamos a utilizar en el modelo de predicción.

## <a name="step-3-define-features"></a>Paso 3: Definir funciones

En la máquina de aprendizaje, *características* son propiedades medibles individuales de algo que le interesa. En nuestro conjunto de datos, cada fila representa un automóvil y cada columna es una característica de ese automóvil.

Buscar un buen conjunto de características para la creación de un modelo de predicción requiere experimentación y conocimiento acerca del problema que desea resolver. Algunas características son mejores para predecir el destino que otros. Además, algunas características tienen una fuerte correlación con otras características (por ejemplo, ciudad mpg frente a la carretera mpg), por lo que no agreguen mucha información nueva para el modelo y se pueden quitar.

Vamos a crear un modelo que utiliza un subconjunto de las características de nuestro conjunto de datos. Puede volver y seleccionar distintas funciones, ejecute de nuevo el experimento y ver si obtendrá mejores resultados. Pero, para empezar, vamos a intentar las siguientes características:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Arrastre otro [Seleccionar columnas en el conjunto de datos] [ select-columns] módulo para el experimento de lienzo y conéctelo al puerto de salida izquierda de los [Datos limpios falta] [ clean-missing-data] módulo. Haga doble clic en el módulo y escriba "Seleccionadas características de predicción".

2. En el panel **Propiedades** , haga clic en **iniciar el selector de columna** .

3. Haga clic en **reglas**.

4. Bajo **Comenzar con** haga clic **sin columnas**y, a continuación, seleccione **incluir** y **nombres de columna** en la fila de filtro. Escriba la lista de nombres de columna. Esto indica el módulo atraviese sólo las columnas que especificamos.

    > [AZURE.TIP] Al ejecutar el experimento, nos hemos asegurado de que las definiciones de columna de los datos pasan del conjunto de datos a través de los [Datos limpios falta] [ clean-missing-data] módulo. Esto significa que otros módulos que se conecta también contiene la información del conjunto de datos.

5. Haga clic en el botón marca de verificación (Aceptar).

![Seleccionar columnas][screen6]

Esto genera el conjunto de datos que se utilizará en el algoritmo de aprendizaje en los siguientes pasos. Más adelante, puede volver y vuelva a intentarlo con una selección diferente de características.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Paso 4: Elegir y aplicar un algoritmo de aprendizaje

Ahora que los datos están listos, la construcción de un modelo de predicción consiste en formación y pruebas. Utilizaremos nuestros datos para entrenar el modelo y, a continuación, pruebe el modelo para ver cómo cerrar es capaz de predecir los precios. Por ahora, no se preocupe por qué es necesario entrenar y probar un modelo.

*Clasificación* y *regresión* son dos tipos de máquina supervisada aprender técnicas. Clasificación predice una respuesta desde un conjunto definido de categorías, como un color (rojo, azul o verde). Regresión se utiliza para predecir un número.

Puesto que deseamos predecir el precio, que es un número, vamos a utilizar un modelo de regresión. Para este ejemplo, va Formaremos a un modelo de *regresión lineal* de simple y, en el paso siguiente, realizaremos una prueba.

1. Utilizamos nuestros datos para entrenamiento y pruebas, dividiéndolo en aprendizaje independientes y conjuntos de pruebas. Seleccione y arrastre los [Datos divididos] [ split] módulo para el experimento de lienzo y conéctelo a la salida de la última [Seleccionar columnas en el conjunto de datos] [ select-columns] módulo. Establecer la **fracción de filas en el primer conjunto de datos de salida** a 0,75. De este modo, se utilizará 75 por ciento de los datos para entrenar el modelo y retener el 25 por ciento para la prueba.

    > [AZURE.TIP] Cambiando el parámetro de **inicialización aleatorio** , puede producir diferentes muestras aleatorias para formación y pruebas. Este parámetro controla la propagación del generador de números pseudoaleatorio.

2. Ejecutar el experimento. Esto permite que las [Seleccione columnas de Dataset] [ select-columns] y [Datos divididos] [ split] módulos para pasar las definiciones de columna a los módulos iremos agregando a continuación.  

3. Para seleccionar el algoritmo de aprendizaje, expanda la categoría **Máquina de aprendizaje** en la paleta de módulo a la izquierda del lienzo y, a continuación, expanda **Inicializar el modelo**. Muestra diversas categorías de módulos que pueden utilizarse para inicializar algoritmos de aprendizaje de la máquina.

    Para este experimento, seleccione la [Regresión lineal] [ linear-regression] módulo bajo la categoría de **regresión** (se puede encontrar el módulo escribiendo "regresión lineal" en el cuadro de búsqueda de paleta) y arrástrelo hasta el lienzo del experimento.

4. Busque y arrastre el [Tren modelo] [ train-model] módulo al lienzo experimento. Conecte el puerto de entrada izquierdo a la salida de la [Regresión lineal] [ linear-regression] módulo. Conecte el puerto de entrada derecha a la salida de datos de entrenamiento (puerto izquierdo) de los [Datos divididos] [ split] módulo.

5. Seleccione el [Modelo del tren] [ train-model] módulo, haga clic en **iniciar el selector de columna** en el panel de **Propiedades** y, a continuación, seleccione la columna de **precio** . Este es el valor que nuestro modelo se va a predecir.

    ![Seleccione la columna "precio"][screen7]

6. Ejecutar el experimento.

El resultado es un modelo de regresión capacitados que puede utilizarse para anotar nuevos ejemplos para realizar predicciones.

![Aplica el algoritmo de aprendizaje de la máquina][screen8]

## <a name="step-5-predict-new-automobile-prices"></a>Paso 5: Predecir los nuevos precios de automóviles

Ahora que hemos entrenados el modelo mediante el 75 por ciento de nuestros datos, se puede utilizar para anotar el otro 25% de los datos para ver qué tan bien nuestras funciones de modelo.

1. Busque y arrastre el [Modelo Score] [ score-model] módulo para el experimento de lienzo y conecte el puerto de entrada izquierdo a la salida del [Tren modelo] [ train-model] módulo. Conecte el puerto de entrada derecha a la salida de datos de prueba (puerto derecha) de los [Datos divididos] [ split] módulo.  

    ![Módulo de modelo de puntuación][screen8a]

2. Para ejecutar el experimento y ver el resultado del [Modelo Score] [ score-model] módulo, haga clic en el puerto de salida y, a continuación, seleccione **Ver**. El resultado muestra los valores pronosticados para el precio y los valores conocidos de los datos de prueba.  

3. Por último, para probar la calidad de los resultados, seleccione y arrastre el [Modelo evaluar] [ evaluate-model] módulo para el experimento de lienzo y conecte el puerto de entrada de izquierda a la salida del [Modelo Score] [ score-model] módulo. (Existen dos puertos de entrada porque el [Modelo de evaluación] [ evaluate-model] módulo puede utilizarse para comparar dos modelos.)

4. Ejecutar el experimento.

Para ver el resultado del [Modelo evaluar] [ evaluate-model] módulo, haga clic en el puerto de salida y, a continuación, seleccione **Ver**. Las siguientes estadísticas se muestran para nuestro modelo:

- **Error absoluta Media** (MAE): el promedio de errores absolutos (un *error* es la diferencia entre el valor previsto y el valor real).
- **Error al cuadrado medio de raíz** (RMSE): la raíz cuadrada de la media de errores al cuadrado de predicciones realizadas en el conjunto de datos de prueba.
- **Desviación absoluta relativa**: el promedio de errores absolutos con respecto a la diferencia absoluta entre los valores reales y la media de todos los valores reales.
- **Error al cuadrado relativa**: el promedio de errores al cuadrado en relación con el cuadrado de la diferencia entre los valores reales y la media de todos los valores reales.
- **Coeficiente de determinación**: también conocido como el **valor de R cuadrado**, esta es una métrica estadística que indica cómo un modelo se adapta a los datos.

Para cada uno de lo error estadísticas, menor es mejor. Un valor inferior indica que las predicciones hacerlos coincidir con los valores reales. Para el **Coeficiente de determinación**, cuanto más se aproxime su valor es uno (1.0), mejor las predicciones.

![Resultados de la evaluación][screen9]

El experimento final debería tener este aspecto:

![Tutorial de aprendizaje de la máquina: completar el experimento de regresión lineal que utiliza las técnicas de modelado de predicción.][screen10]

## <a name="next-steps"></a>Próximos pasos

Ahora que ha completado un primer tutorial de aprendizaje de máquina y tener el experimento configurado, puede recorrer en iteración para intentar mejorar el modelo. Por ejemplo, puede cambiar las características que utilice en la predicción. O puede modificar las propiedades de la [Regresión lineal] [ linear-regression] algoritmo o pruebe un algoritmo diferente en conjunto. Incluso puede agregar equipo varios algoritmos para el experimento de aprendizaje a la vez y comparar dos mediante el [Modelo de evaluación] [ evaluate-model] módulo.

> [AZURE.TIP] Utilice el botón **Guardar como** en el lienzo de experimento para copiar cualquier iteración del experimento. Puede ver todas las iteraciones del experimento haciendo clic en **Ver historial de ejecutar** en el lienzo. Consulte [Administrar experimentar iteraciones en Azure máquina Learning Studio] [ runhistory] para obtener más detalles.

[runhistory]: machine-learning-manage-experiment-iterations.md

Cuando esté satisfecho con el modelo, se puede implementar como un servicio web que se utilizará para predecir precios automóviles con nuevos datos. Vea [implementar un servicio web de aprendizaje del equipo de Azure] [ publish] para obtener más detalles.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Para ver un tutorial más amplia y detallado de las técnicas de modelado predictivo para la creación, formación, puntuación e implementación de un modelo, ven [desarrollar una solución utilizando el aprendizaje Azure predictiva][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
