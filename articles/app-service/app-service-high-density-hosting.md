<properties
    pageTitle="Alta densidad de hospedaje en la aplicación de servicio de Azure | Microsoft Azure"
    description="Alta densidad de hospedaje en la aplicación de servicio de Azure"
    authors="btardif"
    manager="wpickett"
    editor=""
    services="app-service\web"
    documentationCenter=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"/>

# <a name="high-density-hosting-on-azure-app-service"></a>Alta densidad de hospedaje en la aplicación de servicio de Azure

Cuando se usa el servicio de aplicación, la aplicación está desconectada de la capacidad asignada a él por dos conceptos:

- **La aplicación:** Representa la aplicación y su configuración de tiempo de ejecución. Por ejemplo, incluye la versión de .NET que debe cargarse el tiempo de ejecución, la configuración de la aplicación, etcetera.

- **El Plan de servicio de aplicaciones:** Define las características de la capacidad, el conjunto de características disponibles y la ubicación de la aplicación. Por ejemplo, podrían ser características grande máquina (cuatro núcleos), cuatro instancias, las características Premium en Estados Unidos oriental.

Una aplicación siempre está vinculada a un plan de servicio de aplicaciones, pero un plan de servicio de aplicación puede proporcionar capacidad a uno o más aplicaciones.

Como resultado, la plataforma proporciona la flexibilidad para aislar una sola aplicación o tener varias aplicaciones compartir recursos mediante el uso compartido de un plan de servicio de aplicación.

Sin embargo, cuando varias aplicaciones comparten un plan de servicio de aplicación, una instancia de esa aplicación se ejecuta en todas las instancias de ese plan de servicios de aplicación.

## <a name="per-app-scaling"></a>Por escalado de la aplicación
*Por escalado de la aplicación* es una característica que puede habilitada en el nivel del plan de servicio de aplicaciones y, a continuación, se utiliza por la aplicación.

Por aplicación escala escala una aplicación independiente desde el plan de servicios de aplicación que lo hospeda. De este modo, se puede configurar un plan de servicio de aplicación para proporcionar instancias de 10, pero se puede establecer una aplicación para escalar a solo 5 de ellas.

La plantilla de administrador de recursos de Azure siguiente crea un plan de servicio de la aplicación que se ha escalado a 10 instancias y una aplicación que está configurada para usar por escalado de la aplicación y escalar a solo 5 instancias.

El plan de servicio de la aplicación es establecer la propiedad **escala por sitio** True ( `"perSiteScaling": true`). La aplicación es establecer el **número de trabajadores** usar para 5 (`"properties": { "numberOfWorkers": "5" }`).

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }


## <a name="recommended-configuration-for-high-density-hosting"></a>Configuración recomendada para el hospedaje de alta densidad

Por escalado de la aplicación es una característica que está habilitada en regiones de Azure públicas y entornos de aplicación de servicio. Sin embargo, la estrategia recomendada es usar entornos de aplicación de servicio para aprovechar sus características avanzadas y los grupos más grandes de capacidad.  

Siga estos pasos para configurar alta densidad para las aplicaciones de hospedaje:

1. Configurar el entorno de servicio de la aplicación y elija un grupo de trabajo está dedicado al escenario de hospedaje de alta densidad.

1. Crear un plan de servicios de aplicación única y escalar para usar toda la capacidad disponible en el grupo de trabajo.

1. Establezca el indicador de escalado de cada sitio true en el plan de servicio de aplicación.

1. Nuevos sitios se crean y se asignan a ese plan de servicio de la aplicación con la propiedad **numberOfWorkers** establecida en **1**. Con esta configuración, produce la mayor densidad posible en este grupo de trabajo.

1. El número de trabajadores se puede configurar de forma independiente por sitio para conceder a recursos adicionales según sea necesario. Por ejemplo, un sitio de uso de alta puede establecer un **numberOfWorkers** a **3** para tener más capacidad de procesamiento de esa aplicación mientras poco uso sitios configure **numberOfWorkers** a **1**.
