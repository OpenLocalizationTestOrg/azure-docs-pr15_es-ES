<properties
    pageTitle="Crear modelos de análisis de texto en Azure máquina aprendizaje Studio | Microsoft Azure"
    description="Cómo crear modelos de análisis de texto en Azure máquina aprendizaje Studio con módulos para texto de preprocesamiento, g N o característica hash"
    services="machine-learning"
    documentationCenter=""
    authors="rastala"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="roastala" />


#<a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Crear modelos de análisis de texto en Azure máquina aprendizaje Studio

Puede usar el aprendizaje Azure para generar y operativa modelos de análisis de texto. Estos modelos pueden ayudar a solucionar, por ejemplo, los problemas de análisis de opinión o de clasificación de documentos.

En un ensayo de análisis de texto, lo haría normalmente:

 1. Limpiar y preprocesamiento de conjunto de datos de texto
 2. Extraer vectores numéricos característica de texto previamente procesado
 3. Modelo de clasificación o regresión tren
 4. Puntuación y validar el modelo
 5. Implementa el modelo en producción

En este tutorial, aprenderá estos pasos como le guiaremos a través de un modelo de análisis de opinión con conjunto de datos de revisiones de la libreta de Amazon (consulte este artículo de investigación "biografías, Bollywood,-radiocasetes y batidoras: adaptación de dominio para la clasificación de opinión" John Blitzer, Mark Dredze y Fernando Pereira; Asociación de cálculo lingüístico (ACL) 2007.) Este conjunto de datos consta de las puntuaciones de revisión (1-2 o 4 y 5) y el texto de forma libre. El objetivo es predecir la puntuación de revisión: baja (1 - 2) o alto (4-5).

Puede encontrar experimentación cubierto en este tutorial en la Galería de inteligencia de Cortana:

[Predecir reseñas de libros] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Predecir reseñas de libros - ensayo predictiva] (https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Paso 1: Limpiar y preprocesamiento de conjunto de datos de texto

Comenzar el ensayo dividiendo las puntuaciones de revisión en categorías depósitos altos y bajos formular el problema como clasificación de clase de dos. Usamos [editar metadatos] (https://msdn.microsoft.com/library/azure/dn905986.aspx) y módulos [grupo por categorías Values] (https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Crear etiqueta](./media/machine-learning-text-analytics-module-tutorial/create-label.png)

A continuación, limpiar el texto usando módulo [preprocesamiento texto] (https://msdn.microsoft.com/library/azure/mt762915.aspx). La limpieza, reduce el ruido en el conjunto de datos, ayuda a encontrar las características más importantes y mejorar la precisión del modelo final. Eliminamos las palabras irrelevantes - palabras comunes como "el" o "a" - y números, caracteres especiales, caracteres duplicados, direcciones de correo electrónico y las direcciones URL. También convertir el texto en minúsculas, lemmatize las palabras y detectar los límites de la oración que se indican a continuación, por "|" símbolo en texto procesado previamente.

![Preprocesamiento de texto](./media/machine-learning-text-analytics-module-tutorial/preprocess-text.png)

¿Qué pasa si desea usar una lista personalizada de las palabras irrelevantes? Puede pasar en como entrada opcional. También puede utilizar personalizada C# sintaxis de la expresión regular para reemplazar subcadenas y quitar palabras por parte del discurso: sustantivos, verbos o adjetivos.

Una vez completado el preprocesamiento, se división los datos en el tren y probar conjuntos.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Paso 2: Extraer vectores numéricos característica de texto previamente procesado

Para crear un modelo de datos de texto, normalmente debe convertir el texto de forma libre en vectores característica numérico. En este ejemplo, usamos [extraer N g características de texto] módulo (https://msdn.microsoft.com/library/azure/mt762916.aspx) para transformar los datos de texto a ese formato. Este módulo le lleva a una columna de palabras separadas por espacios en blanco y calcula un diccionario de palabras o N-g de palabras, que aparecen en el conjunto de datos. A continuación, cuenta cuántas veces cada palabra o N g, aparece en cada registro y crea vectores de la característica de los recuentos. En este tutorial, se establezca N g tamaño 2, por lo que nuestros vectores característica incluyen palabras o combinaciones de dos palabras subsiguientes.

![Extraer N g](./media/machine-learning-text-analytics-module-tutorial/extract-ngrams.png)

Aplique TF * IDF (término frecuencia inversa documento frecuencia) ponderación a N g cuenta. Este método agrega grosor de palabras que aparecen con frecuencia en un único registro pero son poco comunes en todo el conjunto de datos. Otras opciones incluyen binario, TF y gráfico de peso.

Estas características de texto a menudo tienen dimensiones alto. Por ejemplo, si el cuerpo contiene palabras únicas 100000, el espacio de la característica tendría 100000 dimensiones o más si se usa N g. El módulo de características de N g extraer proporciona un conjunto de opciones para reducir las dimensiones. Puede elegir excluir palabras que son demasiado frecuentes tener importancia predictiva corto o largo o demasiado raro. En este tutorial, se excluye g N que aparecen en menos de 5 registros o en más de 80% de registros.

Además, puede usar selección de características para seleccionar sólo aquellas características que están más relacionadas con el objetivo de predicción. Usamos selección de características chi cuadrado para seleccionar 1000 características. Puede ver el vocabulario de palabras seleccionadas o N g haciendo clic en los resultados de la derecha del módulo de extraer N-g.

Como alternativa al uso de características de N g extraer, puede usar característica hash módulo. Nota Aunque ese [característica hash] (https://msdn.microsoft.com/library/azure/dn906018.aspx) no tiene funciones de selección de la característica de compilación o TF * peso IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Paso 3: Entrenar el modelo de clasificación o regresión.

Ahora el texto se ha convertido a las columnas de la característica numérico. El conjunto de datos todavía contiene columnas de cadena de las etapas anteriores, por lo que usamos seleccionar columnas de conjunto de datos para excluir ellos.

A continuación, usamos [dos clase regresión logística] (https://msdn.microsoft.com/library/azure/dn905994.aspx) para predecir nuestro destino: calificación de revisión alta o baja. En este momento, el problema de análisis de texto se ha convertido en un problema de clasificación normal. Puede usar las herramientas disponibles en aprendizaje de máquina Azure para mejorar el modelo. Por ejemplo, puede experimentar con diferentes clasificadores para averiguar cómo resultados proporcionan o use hyperparameter optimización para mejorar la precisión.

![Tren y puntuación](./media/machine-learning-text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Paso 4: Puntuación y validar el modelo

¿Cómo se validar el modelo cualificado? Estamos puntuación contra el conjunto de datos de prueba y evaluar la precisión. Sin embargo, el modelo que ha aprendido vocabulario de N g y sus pesos del conjunto de datos de aprendizaje. Por lo tanto, debemos usar ese vocabulario y los pesos al extraer las características de datos de prueba, en lugar de crear el vocabulario nuevamente. Por lo tanto, se agrega módulo extraer N g características a la puntuación rama del ensayo, conecte el vocabulario de salida de bifurcación de formación y establecer el modo de vocabulario como solo lectura. También deshabilitar el filtrado de N g por frecuencia estableciendo el mínimo 1 instancia como máximo al 100% y desactivar la selección de características.

Después de la columna de texto en datos de prueba se ha convertido a las columnas de la característica numéricos, nos excluir las columnas de cadena de etapas anteriores como de bifurcación de aprendizaje. A continuación, usamos módulos de puntuación modelo para realizar predicciones y evaluar modelo para evaluar la precisión.

## <a name="step-5-deploy-the-model-to-production"></a>Paso 5: Implementar el modelo de producción

El modelo es casi está listo para su implementación en producción. Cuando se implementa como servicio web, tiene la cadena de texto de forma libre como entrada y devolver una predicción "alta" o "baja". Utiliza el vocabulario N g aprendido para transformar el texto a las características y modelo de regresión logística cualificados para realizar una predicción de estas características. 

Para configurar el ensayo predictiva, se guarda en primer lugar el vocabulario N g como conjunto de datos y el modelo de regresión logística capacitado desde la rama de formación del ensayo. A continuación, se guarda el ensayo de "Guardar como" para crear un gráfico de ensayo para ensayo predictiva. Se quite el módulo de datos divididos y la rama formación el ensayo. Nos, a continuación, conecte el guardado anteriormente vocabulario N g y modelo para extraer N g características y módulos de modelo de puntuación, respectivamente. También hemos quitar el módulo de evaluar modelo.

Estamos insertar seleccionar columnas en el módulo de conjunto de datos antes de módulo preprocesamiento texto para quitar la etiqueta de columna y anular la selección de opción de "Columna de datos anexados puntuación to dataset" en el módulo de puntuación. De esta forma, el servicio web no solicita la etiqueta que está tratando de predecir y no no eco la entrada a las características de respuesta.

![Ensayo predictiva](./media/machine-learning-text-analytics-module-tutorial/predictive-text.png)

Ahora que tenemos un ensayo que se puede publicar como un servicio web y llamar mediante la ejecución de petición de respuesta o lote API.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los módulos de análisis de texto de [documentación de MSDN] (https://msdn.microsoft.com/library/azure/dn905886.aspx).
