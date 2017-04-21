<properties
    pageTitle="Una solución predictiva para riesgo con Machine aprendizaje | Microsoft Azure"
    description="Obtener un tutorial detallado que muestra cómo crear una solución de análisis predictiva de evaluación de riesgos de crédito en Azure máquina aprendizaje Studio."
    keywords="riesgo de crédito, solución de analítica predictiva, evaluación de riesgos"
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
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Tutorial: Desarrollar una solución de análisis predictiva para evaluación de riesgos de crédito en aprendizaje de máquina de Azure

Supongamos que necesita predecir el riesgo de un individuo según la información que se proporcionan en una aplicación de crédito.  

Evaluación de riesgos de crédito es un problema complejo, por supuesto, pero vamos a un poco a simplificar los parámetros de la pregunta. A continuación, se puede utilizar como un ejemplo de cómo usar Microsoft Azure aprendizaje con Machine aprendizaje Studio y el servicio web de aprendizaje para crear este tipo de solución de analítica predictiva.  

En este tutorial detallado, seguiremos el proceso de desarrollo de un modelo de analítica predictiva en Studio de aprendizaje de equipo y su implementación como un servicio web de aprendizaje del equipo de Azure. Se deberá iniciar con datos de riesgo de crédito disponible públicamente, desarrollar y formar a un modelo de predicción basado en esos datos y, a continuación, se implementa el modelo como un servicio web que se pueden usar otros usuarios de la evaluación de riesgos de crédito.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] Para descargar e imprimir un diagrama que le ofrece una descripción general de las capacidades de máquina aprendizaje Studio, consulte el [diagrama de información general de las capacidades de Azure máquina aprendizaje Studio](machine-learning-studio-overview-diagram.md).

Para crear una solución de evaluación de riesgos de crédito, se deberá seguir estos pasos:  

1.  [Crear un área de trabajo de aprendizaje de equipo](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Cargar datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Crear un nuevo ensayo](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Entrenar y evaluar los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implementar el servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

En este tutorial se basa en una versión simplificada de la [la clasificación binario: predicción de riesgo de crédito](http://go.microsoft.com/fwlink/?LinkID=525270) ensayo de ejemplo en la [Galería de inteligencia de Cortana](http://gallery.cortanaintelligence.com/).
