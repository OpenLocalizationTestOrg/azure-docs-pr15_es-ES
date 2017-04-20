<properties
    pageTitle="Implementación de aplicaciones de servicio de aplicaciones de Azure"
    description="Obtenga información sobre cómo implementar aplicaciones para el trabajo de servicio de aplicaciones"
    keywords="servicio de aplicaciones de servicio, azure de aplicación, implementar, implementación"
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="dariagrigoriu"/>

# <a name="azure-app-service-deployment-overview"></a>Información general de implementación de Azure de aplicación de servicio

Servicio de aplicaciones de Azure proporciona una característica enriquecida e integrada configurada para admitir la creación de flujos de trabajo de implementación eficaz y flexible. Implementación de la aplicación puede aprovechar opciones, que incluyen la integración continua o control de origen local de publicación, WebDeploy y FTP. El método recomendado para la implementación de la aplicación de producción es intercambio de franja de implementación. Ranuras de implementación representan los entornos de ensayo y la integración de asociados con las aplicaciones de producción. Pueden configurar ranuras de implementación y destino de tráfico de web de validación y tráfico puede intercambiar a petición para su implementación en producción con ningún tiempo de inactividad y automatizado preparación. Los pasos de un flujo de trabajo de implementación se pueden automatizar fácilmente a través de los productos de administración de lanzamiento como administración de lanzamiento de Visual Studio. Esto es útil para junto con otros recursos de solución (por ejemplo, almacenan datos), periodicidad y replicación a través de varias unidades de implementación. 

[AZURE.INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]
