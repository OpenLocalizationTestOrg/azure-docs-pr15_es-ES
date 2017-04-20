<properties
    pageTitle="Mover recursos de aplicación Web a otro grupo de recursos"
    description="Describe los escenarios donde puede mover aplicaciones Web y servicios de aplicación de un grupo de recursos a otro."
    services="app-service"
    documentationCenter=""
    authors="ZainRizvi"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/04/2016"
    ms.author="zarizvi"/>
    
# <a name="supported-move-configurations"></a>Configuraciones de mover compatibles

Puede mover recursos de Azure Web App con la [Api de ARM mover recursos](../resource-group-move-resources.md).

Aplicaciones Web de Azure admite actualmente los siguientes escenarios de desplazamiento:

* Mover todo el contenido de un grupo de recursos (aplicaciones web, planes de servicio de la aplicación y certificados) a otro grupo de recursos 
    * Nota: El grupo de recursos de destino no puede contener los recursos Microsoft.Web en este escenario
* Mover aplicaciones web individuales a un grupo de recursos diferente, mientras sigue los host en su plan de servicio de aplicación actual (el plan de servicio de la aplicación permanece en el grupo de recursos antiguo)
