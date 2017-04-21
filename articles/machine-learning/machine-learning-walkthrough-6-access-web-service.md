<properties
    pageTitle="Paso 6: Obtener acceso al servicio web de aprendizaje de equipo | Microsoft Azure"
    description="Paso 6 de la desarrollar soluciones predictiva Tutorial: obtener acceso a un servicio web de aprendizaje del equipo de Azure active."
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


# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Tutorial paso 6: Obtener acceso al servicio web de aprendizaje del equipo de Azure

Este es el último paso del tutorial, [desarrollar una solución de analítica predictiva en aprendizaje de máquina de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Crear un área de trabajo de aprendizaje de equipo](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Cargar datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Crear un nuevo ensayo](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Entrenar y evaluar los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implementar el servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.  **Acceso al servicio web**

----------

En el paso anterior en este tutorial hemos implementado un servicio web que usa nuestro modelo de predicción del riesgo de crédito. Ahora los usuarios necesitan poder enviar datos y recibir resultados. 

El servicio web es un servicio web de Azure que puede recibir y devolver datos mediante las API de REST de dos maneras:  

-   **Solicitud/respuesta** : el usuario envía una o varias filas de datos de crédito al servicio con un protocolo HTTP y el servicio responde con uno o varios conjuntos de resultados.
-   **Ejecución por lotes** : el usuario guarda una o varias filas de datos de crédito en un blobs de Windows Azure y envía la ubicación de blob al servicio. El servicio de puntuación todas las filas de datos en la entrada blob, almacena los resultados en otro blob y devuelve la dirección URL de ese contenedor.  

La manera más rápida y sencilla de obtener acceso al servicio web es a través de las plantillas de aplicación Web disponibles en la [Aplicación de Web de Azure Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/).
Estas plantillas de aplicación web pueden crear una aplicación web personalizada que conozca los datos de entrada y qué devolverá el servicio web. Todo lo que debe hacer es proporcionar acceso al servicio web y los datos y hace el resto de la plantilla.

Para obtener más información sobre cómo usar las plantillas de aplicación web, vea [Consume un servicio web de aprendizaje del equipo de Azure con una plantilla de aplicación web](machine-learning-consume-web-service-with-web-app-template.md).

También puede desarrollar una aplicación personalizada para obtener acceso al servicio web usando código starter proporcionado en R, C# y lenguajes de programación Python.
Puede encontrar detalles completos en [cómo consumir un servicio web de aprendizaje del equipo de Azure que se ha publicado desde un equipo de ensayo de aprendizaje](machine-learning-consume-web-services.md).
