<properties
   pageTitle="Cómo ampliar las funciones de Azure | Microsoft Azure"
   description="Comprender cómo las funciones de Azure escalar para satisfacer las necesidades de las cargas de trabajo condicionados por eventos."
   services="functions"
   documentationCenter="na"
   authors="dariagrigoriu"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, cálculo dinámica, sin servidor arquitectura"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="dariagrigoriu"/>

# <a name="how-to-scale-azure-functions"></a>Cómo ampliar las funciones de Azure

## <a name="introduction"></a>Introducción

Una ventaja de funciones de Azure es que solo se consumen recursos de cálculo cuando sea necesario. Esto significa que no pagar VM inactivas o tiene reservar capacidad para cuando es posible que tenga. En su lugar, la plataforma asigna potencia informática cuando el código se está ejecutando, escala hasta según sea necesario para controlar la carga y a continuación, se ajusta hacia abajo cuando no se está ejecutando el código.

El mecanismo de esta nueva funcionalidad es el plan de servicio dinámico.  

Este artículo proporciona una descripción general de cómo funciona el plan de servicio dinámicos y cómo se ajusta la plataforma a petición para ejecutar el código.

Si no todavía está familiarizado con las funciones de Azure, asegúrese de comprobar el artículo [Introducción a las funciones de Azure](functions-overview.md) para entender mejor sus funciones.

## <a name="configure-azure-functions"></a>Configurar las funciones de Azure

Dos opciones principales relacionadas con ajuste de escala:

* Plan de servicio dinámico o [plan de servicios de aplicación de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)
* Tamaño de la memoria para el entorno de ejecución

El costo de una función cambia según el plan de servicio que seleccione. Con un plan de servicio dinámico costo es una función de tiempo de ejecución, el tamaño de la memoria y el número de ejecuciones. Cargos acumulan solo cuando el código se está ejecutando.

Un plan de servicio de aplicación aloja sus funciones en máquinas virtuales existentes, que también pueden utilizarse para ejecutar otro código. Después de pagar para estas máquinas virtuales de cada mes, no hay costo adicional para las funciones de ejecución en ellas.

## <a name="choose-a-service-plan"></a>Elija un plan de servicio

Al crear funciones, puede seleccionar para ejecutarlos en un plan de servicio dinámico o un [plan de servicios de aplicación](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
En el plan de servicios de aplicación, las funciones se ejecutarán en una máquina virtual dedicada, igual que el trabajo de aplicaciones web hoy para Basic, estándar o Premium SKU.
Este VM dedicado se asigna a las aplicaciones y funciones y siempre está disponible si se ejecuta código de forma activa o no. Esto es una buena opción si tiene máquinas virtuales de existentes, bajo utilizados que ya se están ejecutando otro código o si desea ejecutar funciones continuamente o casi continuamente. Una máquina virtual separa coste de tamaño en tiempo de ejecución y la memoria. Como resultado, puede limitar el coste de muchas funciones de larga duración y el costo de las máquinas virtuales de uno o más que se ejecutan en.

[AZURE.INCLUDE [Dynamic Service plan](../../includes/functions-dynamic-service-plan.md)]
