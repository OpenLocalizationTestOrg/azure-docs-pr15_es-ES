<properties
    pageTitle="Una máquina de modelo de aprendizaje de reciclaje | Microsoft Azure"
    description="Aprenda a un modelo de reciclaje y actualizar el servicio Web para usar el modelo recién capacitado en Azure el aprendizaje."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-machine-learning-model"></a>Una máquina de modelo de aprendizaje de reciclaje

Como parte del proceso de operationalization de modelos de aprendizaje de equipo en el equipo de aprendizaje de Azure, el modelo capacitado y guardado. A continuación, se usa para crear un servicio Web predicative. A continuación, se pueden consumir el servicio Web en sitios web, aplicaciones móviles y paneles. 

Modelos que se crean con el aprendizaje no suelen estáticos. El modelo debe aprender a utilizarla como nuevos datos vuelve a estar disponibles o cuando el usuario de la API tiene sus propios datos. 

Formación puede ocurrir con frecuencia. Con la característica de la API de formación de programación, puede el modelo mediante las API de formación de reciclaje mediante programación y actualizar el servicio Web con el modelo recién capacitado. 

Este documento describe el proceso de reconversión y muestra cómo usar las API de formación.

## <a name="why-retrain-defining-the-problem"></a>¿Por qué de reciclaje: definir el problema  

Como parte del proceso de cursos de aprendizaje de equipo, un modelo está capacitado con un conjunto de datos. Modelos que se crean con el aprendizaje no suelen estáticos. El modelo debe aprender a utilizarla como nuevos datos vuelve a estar disponibles o cuando el usuario de la API tiene sus propios datos.

En estos casos, una API de programación proporciona una forma conveniente para permitir o el consumidor de la API para crear a un cliente que puede, de forma regular o puntual de reciclaje el modelo con sus propios datos. A continuación, puede evaluar los resultados de formación y actualizar la API del servicio Web para usar el modelo recién capacitado.

>[AZURE.NOTE] Si tiene una existente de formación de ensayo y el servicio Web nuevo, desea desproteger reciclaje de un servicio Web predictiva existente en lugar de seguir el tutorial mencionado en la sección siguiente.

## <a name="end-to-end-workflow"></a>Flujo de trabajo to-end 

El proceso implica los siguientes componentes: A formación ensayo y un ensayo predictiva publican como un servicio Web. Para habilitar la formación de un modelo de formación, se debe publicar el ensayo formación como un servicio Web con el resultado de un modelo de capacitado. Esto permite el acceso de API al modelo de reciclaje. 

Los siguientes pasos se aplican a los servicios de nuevo y clásico de Web:

Crear el servicio Web predictiva inicial:

* Crear un ensayo de formación
* Crear un ensayo web predictiva
* Implementar un servicio web predictiva

El servicio Web de reciclaje:

* Ensayo de formación de actualización para permitir la formación
* Implementar el servicio web reconversión
* Usar el código de servicio de ejecución de proceso por lotes para el modelo de reciclaje

Para obtener un tutorial de los pasos anteriores, vea [modelos de aprendizaje de reciclaje mediante programación](machine-learning-retrain-models-programmatically.md).

Si implementa un servicio Web clásico:

* Crear un nuevo punto final en el servicio Web predictiva
* Obtener la dirección URL de revisión y el código
* Usar la dirección URL de revisión para indicar el nuevo extremo en el modelo de retrained 

Para obtener un tutorial de los pasos anteriores, vea [un servicio Web clásico de reciclaje](machine-learning-retrain-a-classic-web-service.md).

Si tiene dificultades de reciclaje de un servicio Web clásico, vea [solución de problemas de reciclaje de un servicio Web clásico de aprendizaje de máquina de Azure](machine-learning-troubleshooting-retraining-models.md).

Si implementa un servicio Web nuevo:

* Inicie sesión en su cuenta de administrador de recursos de Azure
* Obtener la definición del servicio Web
* Exportar la definición del servicio Web como JSON
* Actualizar la referencia a la `ilearner` blob en el JSON
* Importar el JSON en una definición de servicio Web
* Actualizar el servicio Web con la nueva definición del servicio Web

Para obtener un tutorial de los pasos anteriores, vea [un servicio Web nueva mediante los cmdlets de PowerShell de administración de aprendizaje de máquina de reciclaje](machine-learning-retrain-new-web-service-using-powershell.md).

El proceso de configuración de reciclaje de un servicio Web clásico implica los siguientes pasos:

![Información general del proceso de reciclaje][1]

El proceso de configuración de reciclaje de un servicio Web nuevo implica los siguientes pasos:

![Información general del proceso de reciclaje][7]

## <a name="other-resources"></a>Otros recursos

- [Modelos de formación y aprendizaje de máquina de Azure de actualización con el generador de datos de Azure](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
- [Crear muchos modelos de aprendizaje y web extremos de servicio de un ensayo con PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md)
- [AML formación modelos usando las API de](https://www.youtube.com/watch?v=wwjglA8xllg) vídeo muestra cómo modelos de aprendizaje creados en Azure el aprendizaje de reciclaje utilizando las API de formación y PowerShell.

<!--image links-->
[1]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/machine-learning-retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

