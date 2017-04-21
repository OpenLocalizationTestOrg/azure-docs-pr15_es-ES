<properties 
    pageTitle="Ejemplos integrados con R de los servicios web de aprendizaje de equipo | Microsoft Azure" 
    description="Buscar un conjunto de web útil ejemplos de servicios creados con código de R y aprendizaje de equipo y a continuación, publicado en Azure Marketplace." 
    keywords="CSharp, código r, ejemplos de servicios web"
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="web-services-examples-using-r-code-on-azure-machine-learning-and-published-to-microsoft-azure-marketplace"></a>Web services ejemplos con código de R en Azure el aprendizaje y publicado en Microsoft Azure Marketplace

En este artículo son web de ejemplo servicios se han creado con el aprendizaje Azure y publicar en Azure Marketplace. Cada ejemplo de servicio web tiene un documento completo adjunto, incrustación de conjuntos de datos de ejemplo para probar los servicios y explica cómo el usuario puede crear un servicio similar a sí mismos. 

En Azure máquina aprendizaje Studio, los usuarios pueden escribir código R y con unos pocos clics, publicar como un servicio web para aplicaciones y dispositivos para consumir todo el mundo. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Genere calculadoras simples que proporcionan funciones estadísticas con la creación de una predicción de análisis de opinión de minería de texto personalizado, nuevos y experimentados usuarios R pueden beneficiarse de la facilidad con la que los usuarios de aprendizaje del equipo de Azure pueden operativa código R. Mientras está estos web services podrían haberse consumidos por usuarios potencialmente a través de una aplicación móvil o un sitio Web, el propósito de estos ejemplos de servicios web mostrar cómo puede controle el aprendizaje R secuencias de comandos con fines analíticos y utilizarse para crear servicios web en la parte superior código R.

Cada ejemplo incluye un ejemplo de C# para el consumo de servicio web.


![Diagrama de código R en Azure aprendizaje: soluciones de R para propietarios usar o publicado en Azure Marketplace.][1]

Tenga en cuenta los siguientes escenarios.

##<a name="scenario-1-generic-model"></a>Escenario 1: Modelo de genérico 
Un usuario trabaja con un modelo genérico que se puede aplicar a los datos de un usuario nuevo, como una previsión básica de datos de la serie de tiempo o un método de R personalizado con funciones analíticas avanzadas. Este usuario publica el modelo como un servicio web para que otros usuarios consumir con sus datos.



* [Clasificador binario](machine-learning-r-csharp-binary-classifier.md)
* [Modelo de clúster](machine-learning-r-csharp-cluster-model.md)
* [Multivariate regresión lineal.](machine-learning-r-csharp-multivariate-linear-regression.md)
* [Realizar una previsión - Suavización exponencial](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [ETS + STL previsión](machine-learning-r-csharp-retail-demand-forecasting.md)
* [Realizar una previsión - Autoregressive integrado Media móvil (ARIMA)](machine-learning-r-csharp-arima.md)
* [Análisis de supervivencia](machine-learning-r-csharp-survival-analysis.md)


##<a name="scenario-2-trained-model--specific-data"></a>Escenario 2: Capacitado modelo: datos específicos 
Un usuario tiene datos que proporciona útiles predicciones a través de código de R, como una muestra de cuestionarios personalidad agrupados mediante un algoritmo de k emparejadas predecir tipo personalidad del usuario o los datos de la encuesta de estado que se pueden usar para predecir el riesgo de una persona para cáncer pulmonar con un paquete de análisis R de supervivencia grande. El usuario publica los datos a través de un servicio web que prevé el resultado de un usuario nuevo.

##<a name="scenario-3-trained-model--generic-data"></a>Escenario 3: Capacitado modelo: datos genéricos 
Un usuario tiene datos genéricos (por ejemplo, un cuerpo de texto) que permite a un servicio web generarse y aplicar de forma general en diferentes tipos de casos de uso y escenarios.

* [Diccionario en función de análisis de opinión](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

##<a name="scenario-4-advanced-calculator"></a>Escenario 4: Calculadora avanzada 
Un usuario proporciona cálculos avanzados o simulaciones que no requieren cualquier modelo capacitado o la conexión de un modelo de datos del usuario.

* [Diferencia de prueba de proporciones](machine-learning-r-csharp-difference-in-two-proportions.md)
* [Conjunto de distribución normal](machine-learning-r-csharp-normal-distribution.md)
* [Conjunto de distribución binomial.](machine-learning-r-csharp-binomial-distribution.md)

##<a name="faq"></a>Preguntas más frecuentes
Para las preguntas más frecuentes sobre consumo del servicio web o publicar en el catálogo de soluciones, vea [aquí](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png


 
