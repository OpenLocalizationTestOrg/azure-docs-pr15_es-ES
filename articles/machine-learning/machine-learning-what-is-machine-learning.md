<properties
    pageTitle="¿Qué es el aprendizaje en Azure? | Microsoft Azure"
    description="Explica los conceptos básicos de aprendizaje de la máquina en la nube, describe lo que puede utilizarlo para y define términos de aprendizaje de la máquina."
    keywords="¿Qué es aprendizaje de máquinas, equipo aprendiendo términos, predictivas, análisis predictivo, operativa"
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="cgronlun;tedway;olgali"/>


# <a name="introduction-to-machine-learning-in-the-cloud"></a>Introducción a la máquina de aprendizaje en la nube

## <a name="what-is-machine-learning"></a>¿Qué es el aprendizaje?

Aprendizaje automático es una técnica de la ciencia de datos que ayuda a los equipos a aprender de los datos existentes con el fin de prever tendencias, resultados y comportamientos futuros.  

Estas previsiones o predicciones de aprendizaje automático pueden hacer aplicaciones y dispositivos inteligentes. Cuando compra en línea, aprendizaje automático ayuda a recomendar otros productos que pueden gustarte basados en lo que haya adquirido. Cuando su tarjeta de crédito es pasada, aprendizaje automático compara la transacción a una base de datos de transacciones y ayuda a detecta el fraude. Cuando su aspiradora robot aspira a una sala, el aprendizaje ayuda a decidir si se realiza el trabajo.

Para obtener una introducción breve, pruebe la serie de vídeos [Ciencia de datos para principiantes](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md). Sin utilizar tecnicismos ni matemáticas, ciencia de datos para principiantes presenta el aprendizaje y le guía a través de un sencillo modelo de predicción.

## <a name="what-is-machine-learning-in-the-microsoft-azure-cloud"></a>¿Qué es el aprendizaje en la nube de Microsoft Azure?

Aprendizaje automático Azure es un servicio de poderosos análisis predictivo basado en la nube que hace posible crear e implementar modelos predictivos como soluciones de analítica rápidamente. Cuando se utiliza la nube Azure para ejecutar experimentos de aprendizaje de la máquina o crear soluciones, no tienes que comprar hardware costoso o infraestructura.

![¿Qué es el aprendizaje? Flujo de trabajo básico que controle el análisis predictivo en el aprendizaje de la máquina de Azure.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

Aprendizaje automático Azure no sólo proporciona herramientas para modelar el análisis predictivo, sino que también proporciona un servicio totalmente gestionado que se puede utilizar para implementar los modelos predictivos como servicios web listos para consumir. Aprendizaje de máquinas Azure proporciona herramientas para crear soluciones de análisis predictivo completo en la nube: crear, probar, hacer operativa y administrar modelos de predicción.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="what-is-predictive-analytics"></a>¿Qué es el análisis predictivo?

Análisis predictivo utiliza varias fórmulas matemáticas denominadas algoritmos que analizan datos históricos o actuales para los patrones o tendencias para prever futuros eventos.

Aprendizaje automático Azure es una forma especialmente eficaz realizar análisis predictivo: puede trabajar desde una biblioteca listos para el uso de algoritmos, utilizar los algoritmos para crear modelos en un PC conectado a internet e implementar rápidamente la solución predictiva. Para obtener resultados rápidamente, comience con ejemplos listos para usar y soluciones de la [Galería de inteligencia Cortana](http://gallery.cortanaintelligence.com/).

## <a name="build-complete-machine-learning-solutions-in-the-cloud"></a>Generar soluciones en la nube para el aprendizaje de máquina completa

Aprendizaje de máquinas Azure tiene todo lo que necesita para crear soluciones de análisis predictivo en la nube, desde una biblioteca grande de algoritmo, a un estudio para la creación de modelos para una manera fácil de implementar el modelo como un servicio web.

### <a name="machine-learning-studio-create-predictive-models"></a>Estudio de aprendizaje de la máquina: Crear modelos de predicción

En la [Máquina de aprendizaje Studio](machine-learning-what-is-ml-studio.md), puede crear rápidamente modelos predictivos arrastrando, colocando y conexión de módulos. Es fácil de experimentar con diferentes combinaciones y [Pruébelo gratuitamente](https://studio.azureml.net/?selectAccess=true&o=2).

* En la [Galería de inteligencia Cortana](machine-learning-gallery-how-to-use-contribute-publish.md), puede probar soluciones de análisis creadas por otros usuarios o contribuir con su propio. Publicar preguntas o comentarios sobre experimentos en la Comunidad o compartir vínculos a experimentos a través de redes sociales, como LinkedIn, Twitter.

  ![Pruebe los experimentos predictivos o contribuir sus propios en Azure Cortana inteligencia Galería](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)

* Utilice una amplia biblioteca de [módulos y algoritmos de aprendizaje automático](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) en máquina de aprendizaje Studio para reactivar sus modelos de predicción. Elija de los experimentos muestra, R y Python paquetes y algoritmos mejor en su clase de negocios de Microsoft como Xbox y Bing. Extender los módulos de estudio con sus propios scripts personalizados de [R](machine-learning-r-quickstart.md) y [Python](machine-learning-execute-python-scripts.md) .

  ![¿Qué es el análisis predictivo: ejemplo de un experimento de análisis predictivo en Azure máquina Learning Studio](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)


### <a name="operationalize-predictive-analytics-solutions-purchase-web-services-or-publish-your-own"></a>Operativa la soluciones de análisis predictivo: compra de servicios web o publicar sus propias

* Servicios web listos para consumir de compra de [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning), tales como recomendaciones, análisis de texto y detección de anomalías.

* Controle sus modelos de análisis predictivo:
    * [Implementar servicios web](machine-learning-publish-a-machine-learning-web-service.md)
    * [Formar y capacitar modelos a través de API](machine-learning-retrain-models-programmatically.md)
    * [Administrar los extremos del servicio web](machine-learning-create-endpoint.md)
    * [Escala de un servicio web](machine-learning-scaling-webservice.md)
    * [Consumir servicios web](machine-learning-consume-web-services.md)

## <a name="key-machine-learning-terms-and-concepts"></a>Máquina clave términos y conceptos de aprendizaje

Términos de aprendizaje de la máquina pueden resultar confusos. Aquí están las definiciones de términos clave para ayudarle. Utilice los comentarios siguientes para decirnos acerca de cualquier otro término que le gustaría definido.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Exploración de los datos, análisis descriptivo y análisis predictivo

**Exploración de los datos** es el proceso de recopilación de información acerca de un conjunto de datos grande y a menudo no estructurado con el fin de encontrar las características para un análisis enfocado. **Minería de datos** hace referencia a la exploración automatizada de los datos.

**Análisis descriptivo** es el proceso de análisis de un conjunto de datos con el fin de resumir lo que sucedió. La gran mayoría de business analytics, como informes de ventas, métricas web y análisis de redes sociales: son descriptivos.

**Análisis predictivo** es el proceso de creación de modelos de datos históricos o actuales para prever los resultados futuros.


### <a name="supervised-and-unsupervised-learning"></a>Aprendizaje supervisado y no supervisado
 Algoritmos **de aprendizaje supervisada** están capacitados con datos etiquetados: en otras palabras, los datos consta de ejemplos de las respuestas que desea. Por ejemplo, podría formar un modelo que identifica el uso fraudulento de tarjeta de crédito de un conjunto de datos con puntos de datos etiquetados de cargos fraudulentos y válidos conocidos. El aprendizaje mayoría está supervisado.

 **Supervisión de aprendizaje** se utiliza en datos con ninguna etiqueta, y el objetivo es buscar las relaciones en los datos. Por ejemplo, que desea encontrar agrupaciones de datos demográficos de los clientes con los hábitos de compra similares.

### <a name="model-training-and-evaluation"></a>Evaluación y formación de modelo
Un modelo de aprendizaje de la máquina es una abstracción de la pregunta que intenta contestar o el resultado que desea predecir. Modelos son entrenados y evaluados a partir de datos existentes.

#### <a name="training-data"></a>Datos de entrenamiento
Al entrenar un modelo de datos, utilice un conjunto conocido de datos y realizar ajustes en el modelo basado en las características de datos para obtener la respuesta más precisa. En el aprendizaje de la máquina de Azure, se genera un modelo desde un módulo de algoritmo que procesa los datos de entrenamiento y módulos funcionales, por ejemplo, un módulo de puntuación.

En el aprendizaje supervisado, si está formación un modelo de detección de fraudes, utilice un conjunto de transacciones que se etiquetan como fraudulento o es válido. Dividir el conjunto de datos al azar y utilice parte para entrenar el modelo y la parte de prueba o evaluar el modelo.

#### <a name="evaluation-data"></a>Datos de la evaluación
Una vez que tenga un modelo capacitado, evaluar el modelo utilizando los datos de prueba restantes. Utilice los datos que se conocen los resultados, que puede saber si el modelo predice con exactitud.

## <a name="other-common-machine-learning-terms"></a>Otros términos comunes de aprendizaje de la máquina

* **algoritmo**: un conjunto independiente de reglas utilizadas para resolver problemas a través de procesamiento de datos, matemáticas o razonamiento automatizado.
* **detección de anomalías**: un modelo de indicadores de sucesos inusuales o valores y le ayuda a descubrir problemas. Por ejemplo, detección de fraudes de tarjetas de crédito busca compras inusuales.
* **datos categóricos**: datos que se organizan por categorías y que pueden dividirse en grupos. Por ejemplo, un conjunto de datos categórico para autos podría especificar año, marca, modelo y precio.
* **clasificación**: un modelo para organizar los puntos de datos en categorías basándose en un conjunto de datos para la categoría a la que ya se conocen las agrupaciones.
* **ingeniería de la característica**: el proceso de extracción o seleccionar características relacionadas con un conjunto de datos con el fin de mejorar el conjunto de datos y mejorar los resultados. Por ejemplo, podrían mejorar datos de tarifa aérea por días de la semana y días festivos. Vea la [característica selección e ingeniería en aprendizaje de máquinas de Azure](machine-learning-feature-selection-and-engineering.md).
* **módulo**: una parte funcional en un modelo de estudio de aprendizaje de la máquina, como el módulo de introducir datos que permite especificar y editar los conjuntos de datos pequeños. Un algoritmo también es un tipo de módulo de estudio de aprendizaje de la máquina.
* **modelo**: un modelo de aprendizaje supervisado es el producto de un equipo formado por datos de entrenamiento, un módulo de algoritmo y módulos funcionales, como un módulo de modelo de puntuación del experimento de aprendizaje.
* **datos numéricos**: los datos que tienen significado como medidas (datos continuos) o cuentan (datos discretos). También se denomina *datos cuantitativos*.
* **partición**: el método por el que dividir los datos en muestras. Para obtener más información, consulte [ejemplo y partición](https://msdn.microsoft.com/library/azure/dn905960.aspx) .
* **predicción**: una predicción es una previsión de un valor o valores de un modelo de aprendizaje de la máquina. También puede ver el término "predicción puntuación." Sin embargo, los resultados previstos no son el resultado final de un modelo. Una evaluación del modelo sigue la puntuación.
* **regresión**: un modelo para predecir un valor basado en las variables independientes, como el precio de un automóvil de la predicción según su año y marca.
* **puntuación**: un valor de predicción generado a partir de un modelo capacitado de clasificación o regresión, utilizando el [módulo de modelo de puntuación](https://msdn.microsoft.com/library/azure/dn905995.aspx) en el estudio de aprendizaje de la máquina. Modelos de clasificación también devuelven una calificación para la probabilidad del valor previsto. Una vez que ha generado las puntuaciones de un modelo, puede evaluar la exactitud del modelo utilizando el [módulo de modelo de evaluación](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **ejemplo**: una parte de un conjunto de datos que se pretenda que sea representativo del conjunto. Ejemplos pueden ser seleccionados de forma aleatoria o basándose en características específicas del conjunto de datos.



## <a name="next-steps"></a>Próximos pasos
Puede aprender los conceptos básicos de análisis predictivo y aprendizaje de máquinas usando un [tutorial paso a paso](machine-learning-create-experiment.md) y mediante la [creación de las muestras](machine-learning-sample-experiments.md).  


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/
