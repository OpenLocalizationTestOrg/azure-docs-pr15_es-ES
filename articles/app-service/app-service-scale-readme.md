<properties
    pageTitle="Azure de aplicación de servicio: Aplicaciones de servicio de aplicación de escala"
    description="Obtenga información sobre los pormenores de escalado de aplicación de servicio de aplicación."
    keywords="aplicación de servicio, azure aplicación servicio, plan de servicios de aplicación scalable, escala, costo de servicio de aplicación"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-scaling-app-service-applications"></a>Azure de aplicación de servicio: Aplicaciones de servicio de aplicación de escala

Aplicaciones que se encuentran en el servicio de aplicación de Azure pueden [lograr escala masiva](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
Sin embargo, una aplicación de ajuste de escala es un problema complejo que no tiene una solución "para todos". En una escala correctamente su aplicación son 3 áreas clave que se contribuyen a su éxito de aplicaciones:

1. Descripción de la arquitectura de la aplicación y sus puntos débiles.
    * ¿Es el estado de la aplicación? ¿Sin estado?
    * ¿Cuáles son todos los componentes de la aplicación?
        * ¿Dónde están lo botella en la aplicación?
    * Cuando se carga se aplica a la aplicación, ¿qué romperá primera?
2. Descripción de los requisitos de carga y el rendimiento esperados.
    * ¿Necesita la aplicación de servicio mil usuarios? ¿o un millón?
    * ¿Tráfico vaya desde una única ubicación geográfica o global?
    * ¿Hay variaciones estacionales? ¿picos de tráfico?
    * ¿Con qué rapidez debe responder la aplicación? ¿1 segundo? ¿1 milisegundos?
3. Descripción y correctamente aprovechar la plataforma de la aplicación de host.
    * ¿Qué características debo aprovechar para alcanzar los objetivos de escala?

Esta sección le ayudará a comprender los factores y diseñar una estrategia que aprovecha las características de servicio de aplicación necesarias para alcanzar los objetivos de escalabilidad de ayuda.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]
