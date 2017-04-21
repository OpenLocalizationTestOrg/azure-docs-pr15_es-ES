<properties
    pageTitle="Administrar Bus de servicio de Azure mediante la automatización de Azure | Microsoft Azure"
    description="Aprenda a usar el servicio de automatización de Azure para administrar Bus de servicio de Azure."
    services="service-bus, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

# <a name="managing-azure-service-bus-using-azure-automation"></a>Administración de Bus de servicio de Azure mediante la automatización de Azure

Esta guía presentan el servicio de automatización de Azure y cómo puede utilizarse para simplificar la administración de Bus de servicio de Azure.

## <a name="what-is-azure-automation"></a>¿Qué es la automatización de Azure?

[Automatización de Azure](../automation/automation-intro.md) es un servicio de Azure para simplificar la administración de la nube mediante la automatización de procesos y la configuración de estado que desee. Mediante la automatización de Azure, manual, repetido, tareas de ejecución larga y provocar errores se pueden automatizar para aumentar la confiabilidad, eficiencia y tiempo de valor para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo es muy confiable de alta disponibilidad escala para satisfacer sus necesidades. En automatización de Azure, procesos pueden ser dio manualmente, parte 3 º sistemas o intervalos programados para que las tareas que se producen exactamente cuando sea necesario.

Reducir la sobrecarga operativa y liberar TI y el personal de DevOps para centrarse en el trabajo que agrega el valor para el negocio al mover las tareas de administración de la nube se ejecute automáticamente mediante la automatización de Azure.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>¿Cómo puede ayudar automatización de Azure a administrar Bus de servicio de Azure?

Puede administrar Bus de servicio con la automatización de Azure mediante la [API de REST de Bus de servicio](https://msdn.microsoft.com/library/azure/mt639375.aspx). En la automatización de Azure puede ejecutar secuencias de comandos de PowerShell para realizar muchas de las tareas de Bus de servicio con la API de REST. También puede emparejar estas llamadas API de REST de automatización de Azure con los cmdlets de otros servicios de Azure, para automatizar tareas complejas en servicios de Azure y sistemas de terceros 3ª.

Estos son algunos ejemplos de uso de PowerShell para administrar Bus de servicio de Azure:

* [Personalizado cmdlets de PowerShell para administrar colas de Bus de servicio de Azure](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Cómo crear colas, temas y suscripciones utilizando una secuencia de comandos de PowerShell de Bus de servicio](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Crear espacios de Bus de servicio de Azure con PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

El módulo de PowerShell para trabajar con bus de servicio de Azure en automatización runbooks puede descargarse desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).


## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de automatización de Azure y cómo puede utilizarse para administrar Bus de servicio de Azure, siga estos vínculos para obtener más información acerca de la automatización de Azure.

* Vea la automatización Azure [Introducción Tutorial](../automation/automation-first-runbook-graphical.md)
* Consulte cómo [Administrar Bus de servicio con PowerShell](service-bus-powershell-how-to-provision.md)
