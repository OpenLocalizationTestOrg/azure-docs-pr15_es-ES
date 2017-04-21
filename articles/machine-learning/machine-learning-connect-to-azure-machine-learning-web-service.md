<properties
    pageTitle="Conectarse a un servicio Web de aprendizaje de equipo | Microsoft Azure"
    description="Con C# o Python, conectarse a un servicio Web de aprendizaje del equipo de Azure mediante una clave de autorización."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016" 
    ms.author="garye" />


# <a name="connect-to-an-azure-machine-learning-web-service"></a>Conectarse a un servicio Web de aprendizaje de máquina Azure

La experiencia del desarrollador de aprendizaje del equipo de Azure es una API del servicio Web para realizar predicciones de entrada de datos en tiempo real o en modo por lotes. Use Azure máquina aprendizaje Studio para crear predicciones e implementar un servicio Web de aprendizaje del equipo de Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para obtener información sobre cómo crear e implementar un servicio Web de aprendizaje de la máquina con Machine Studio de aprendizaje:

- Para obtener un tutorial acerca de cómo crear un ensayo en máquina aprendizaje Studio, vea [crear su primer ensayo](machine-learning-create-experiment.md).
- Para obtener más información sobre cómo implementar un servicio Web, vea [implementar un servicio Web de aprendizaje del equipo](machine-learning-publish-a-machine-learning-web-service.md).
- Para obtener más información sobre el aprendizaje en general, visite el [Centro de documentación de aprendizaje de equipo](https://azure.microsoft.com/documentation/services/machine-learning/).

## <a name="azure-machine-learning-web-service"></a>Azure servicio Web de aprendizaje de equipo ##

Con el servicio Web de aprendizaje del equipo de Azure, una aplicación externa se comunica con un modelo de evaluación de flujo de trabajo de aprendizaje en tiempo real. Una llamada de servicio Web de aprendizaje del equipo devuelve resultados de predicción a una aplicación externa. Para realizar un servicio Web de aprendizaje de máquina llamada, se pasa una clave de API que se crea al implementar una predicción. El servicio Web de aprendizaje de equipo se basa en el resto, una opción de arquitectura populares para proyectos de programación web.

Aprendizaje de máquina Azure tiene dos tipos de servicios:

- Respuesta de solicitud de servicio (RR): una latencia baja, el servicio de gran escalabilidad que proporciona una interfaz para crear e implementar los modelos de estado de equipo aprendizaje Studio.
- Servicio de ejecución de proceso por lotes (BES) – asincrónica de servicio que la puntuación de un lote de registros de datos.

Para obtener más información acerca de los servicios Web de aprendizaje de la máquina, vea [implementar un servicio Web de aprendizaje del equipo](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Obtener una clave de autorización de aprendizaje del equipo de Azure ##

Cuando se implementa el ensayo, se generan claves API del servicio Web. Puede recuperar las claves de varias ubicaciones.

## <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Desde el portal de servicios Web de Microsoft Azure máquina aprendizaje

Inicie sesión en el portal de [Servicios Web de Microsoft Azure máquina aprendizaje](https://services.azureml.net) .

Para recuperar la clave de la API de un servicio Web de aprendizaje de nuevo equipo:

1. En el portal de servicios Web de Azure máquina aprendizaje, haga clic en **Los servicios Web** en el menú superior.
2. Haga clic en el servicio Web para el que desea recuperar la clave.
3. En el menú superior, haga clic en **Consume**.
4. Copie y guarde la **Clave principal**.


Para recuperar la clave de la API de un servicio Web clásico de aprendizaje de equipo:

1. En el portal de servicios Web de Azure máquina aprendizaje, haga clic en **Los servicios Web clásica** del menú superior.
2. Haga clic en el servicio Web con el que está trabajando.
3. Haga clic en el punto final para el que desea recuperar la clave.
3. En el menú superior, haga clic en **Consume**.
4. Copie y guarde la **Clave principal**.

## <a name="classic-web-service"></a>Servicio Web de clásico ##

 También puede recuperar una clave de un servicio Web clásico de máquina aprendizaje Studio o el portal de Azure.

### <a name="machine-learning-studio"></a>Máquina aprendizaje Studio ###

1. Studio de aprendizaje de máquina, haga clic en **Servicios WEB** a la izquierda.
2. Haga clic en un servicio Web. La **clave API** se encuentra en la pestaña **panel** .

### <a name="azure-portal"></a>Portal de Azure ###

1. Haga clic en **El aprendizaje** de la izquierda.
2. Haga clic en el área de trabajo en el que se encuentra el servicio Web.
3. Haga clic en **Servicios WEB**.
4. Haga clic en un servicio Web.
5. Haga clic en un punto final. "API" está presionada la tecla a la inferior derecha.

## <a id="connect"></a>Conectarse a un servicio Web de aprendizaje de equipo

Puede conectarse a un servicio Web de aprendizaje de equipo mediante cualquier lenguaje de programación que admite la respuesta y solicitud HTTP. Puede ver ejemplos en C#, Python y R desde una página de Ayuda del servicio Web de aprendizaje del equipo.

**Ayuda de la API de aprendizaje de equipo** Ayuda de la API de aprendizaje de equipo se crea al implementar un servicio Web. Consulte [el aprendizaje Azure Tutorial: implementar el servicio Web](machine-learning-walkthrough-5-publish-web-service.md).
La Ayuda de la API de aprendizaje de máquina contiene detalles sobre una servicio Web de predicción.

2. Haga clic en el servicio Web con el que está trabajando.
3. Haga clic en el punto final para el que desea ver la página de Ayuda de API.
3. En el menú superior, haga clic en **Consume**.
3. Haga clic en **página de Ayuda de la API** en la respuesta de la solicitud o extremos de ejecución por lotes.

**A la vista el aprendizaje API ayuda para un servicio Web nueva**

En el equipo de Azure Portal de servicios Web de aprendizaje:

1. En el menú superior, haga clic en **Servicios WEB** .
2. Haga clic en el servicio Web para el que desea recuperar la clave.

Haga clic en **Consume** para obtener al URI de la solicitud de Reposonse y servicios de ejecución del lote y código de ejemplo de C#, R y Python.

Haga clic en **Swagger API** para obtener la documentación de Swagger en función de las API llamadas desde los URI suministrado.

### <a name="c-sample"></a>Ejemplo de C# ###

Para conectarse a un servicio Web de aprendizaje del equipo, use un **HttpClient** pasando ScoreData. ScoreData contiene un FeatureVector, un vector n dimensiones de características numéricos que representa la ScoreData. Autenticar con el servicio de aprendizaje del equipo con una clave de API.

Para conectarse a un servicio Web de aprendizaje del equipo, debe estar instalado el paquete de NuGet **Microsoft.AspNet.WebApi.Client** .

**Instalar Microsoft.AspNet.WebApi.Client NuGet en Visual Studio**

1. Publicar el conjunto de datos de descarga de UCI: conjunto de datos de clase de 2 para adultos servicio Web.
2. Haga clic en **Herramientas** > **Administrador de paquetes de NuGet** > **Consola del Administrador de paquetes**.
2. Elija el **paquete de instalación Microsoft.AspNet.WebApi.Client**.

**Para ejecutar el código de ejemplo**

1. Publicar "ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de contenido para adultos 2 clase" ensayo, parte de la colección de ejemplo de aprendizaje del equipo.
2. Asignar apiKey con la clave de un servicio Web. Consulte **obtener una clave de autorización de aprendizaje del equipo de Azure** anteriores.
3. Asignar serviceUri con el URI de solicitud.


### <a name="python-sample"></a>Ejemplo de Python ###

Para conectarse a un servicio Web de aprendizaje del equipo, utilice la biblioteca de **urllib2** pasando ScoreData. ScoreData contiene un FeatureVector, un vector n dimensiones de características numéricos que representa la ScoreData. Autenticar con el servicio de aprendizaje del equipo con una clave de API.


**Para ejecutar el código de ejemplo**

1. Implementar "ejemplo 1: descargar el conjunto de datos de UCI: conjunto de datos de contenido para adultos 2 clase" ensayo, parte de la colección de ejemplo de aprendizaje del equipo.
2. Asignar apiKey con la clave de un servicio Web. Vea la sección **obtener una clave de autorización de aprendizaje del equipo de Azure** cerca del principio de este artículo.
3. Asignar serviceUri con el URI de solicitud.
