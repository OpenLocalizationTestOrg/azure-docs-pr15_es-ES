<properties
    pageTitle="Crear varias máquinas virtuales | Microsoft Azure"
    description="Opciones para crear varias máquinas virtuales de Windows"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="guybo"/>

# <a name="create-multiple-azure-virtual-machines"></a>Crear varias máquinas virtuales de Azure

Hay muchos escenarios donde se necesita para crear una gran cantidad de similar máquinas virtuales de Windows (VM). Algunos ejemplos incluyen la informática de alto rendimiento (HPC), análisis de datos a gran escala, servidores de nivel medio o back-end scalable y con qué frecuencia sin estado (como servidores Web) y bases de datos distribuidas.

En este artículo se describe las opciones disponibles para crear varias máquinas virtuales en Azure. Estas opciones ir más allá de los casos simples donde crear manualmente una serie de máquinas virtuales. Para crear máquinas virtuales muchos, los procesos que suele utilizar no escalan bien si necesita crear más de un conjunto de máquinas virtuales.

Una forma de crear muchas máquinas virtuales similares es utilizar la construcción de administrador de recursos de Azure de _bucles de recursos_.

## <a name="resource-loops"></a>Bucles de recursos

Bucles de recursos son una abreviatura sintáctica en las plantillas de administrador de recursos de Azure. Recursos bucles pueden crear un conjunto de recursos configurados de forma similar en un bucle. Puede usar recursos bucles para crear varias cuentas de almacenamiento, interfaces de red o máquinas virtuales de Windows. Para obtener más información acerca de los recursos bucles, consulte [crear máquinas virtuales de disponibilidad establece mediante bucles de recursos](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## <a name="challenges-of-scale"></a>Retos de escala

Aunque bucles de recursos que sea más fácil construir una infraestructura de nube en escala y generar plantillas más concisas, permanecen ciertos desafíos. Por ejemplo, si utiliza un bucle de recursos para crear 100 máquinas virtuales, debe relacionar controladores de interfaz de red (NIC) con cuentas de almacenamiento y VM correspondientes. Dado que el número de máquinas virtuales es probable que sea distinto del número de cuentas de almacenamiento, debe tratar con tamaños de bucle del recurso diferente, demasiado. Se trata de solucionar problemas, pero la complejidad aumenta significativamente con escala.

Otro desafío se produce cuando se necesita una infraestructura que escala elástica. Por ejemplo, imaginemos una infraestructura Autoescala que automáticamente incrementa o disminuye el número de máquinas virtuales en respuesta a la carga de trabajo. Máquinas virtuales no proporcionan un mecanismo integrado que varían en número (escalado y escala en). Si escala en eliminando máquinas virtuales, es difícil garantizar alta disponibilidad por asegurarse de que están equilibradas máquinas virtuales entre dominios de actualización y errores.

Por último, cuando se utiliza un bucle de recursos, varias llamadas para crear recursos vayan a la estructura subyacente. Cuando varias llamadas crean recursos similares, Azure tiene una oportunidad implícita para mejorar este diseño y optimizar el rendimiento y la confiabilidad de implementación. Aquí es donde se _establece la escala de la máquina virtual_ entran.

## <a name="virtual-machine-scale-sets"></a>Conjuntos de escala de máquina virtual

Conjuntos de escala de máquina virtual son un recurso de Azure calcular a implementar y administrar un conjunto de máquinas virtuales idénticos. Con todas máquinas virtuales configuran la misma, escala VM conjuntos son fáciles de ajustar en y escalar. Simplemente cambiar el número de máquinas virtuales en el conjunto. También puede configurar conjuntos de escala VM para ajustar automáticamente en función de las necesidades de la carga de trabajo.

Para las aplicaciones que tenga que ajustar el tamaño de los recursos de cálculo y en, operaciones de escala están equilibradas implícitamente en errores y actualización de dominios.

En lugar de correlación de varios recursos como NIC y máquinas virtuales, un conjunto de escala de la máquina virtual tiene red, almacenamiento, máquina virtual y propiedades de extensión que se pueden configurar de forma centralizada.

Para obtener una introducción a los conjuntos de escala de máquina virtual, consulte la [página de producto de conjuntos de escala de la máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Para obtener más información, vaya a la [escala de máquinas virtuales establece documentación](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).
