<properties
    pageTitle="¿Qué son escala VM establece? | Microsoft Azure"
    description="Obtenga información acerca de los conjuntos de escala de máquina virtual."
    keywords="conjuntos de máquina virtual Linux, escala de máquina virtual" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/24/2016"
    ms.author="gatneil"/>

# <a name="what-are-virtual-machine-scale-sets"></a>¿Qué máquina virtual escala establece?

Conjuntos de escala de máquina virtual le permiten administrar varios VM como un conjunto. En un nivel alto, conjuntos de escala tienen las siguientes ventajas y desventajas:

Los profesionales de TI:

1. Alta disponibilidad. Cada conjunto de escala coloca sus máquinas virtuales en un conjunto de disponibilidad con los dominios de error 5 (FDs) y 5 actualización de dominios (UDs) para garantizar la disponibilidad (para obtener más información sobre FDs y UDs, ver la [disponibilidad de la máquina virtual](./virtual-machines-linux-manage-availability.md)). 
2. Fácil integración con equilibrador de carga de Azure y puerta de enlace de aplicación.
3. Fácil integración con Autoescala de Azure.
4. Simplificado de implementación, administración y limpiar de máquinas virtuales.
5. Compatible con las versiones de Windows y Linux comunes, así como imágenes personalizadas.

Desventajas:

1. No se puede adjuntar discos de datos a las instancias de la máquina virtual de un conjunto de escala. En su lugar, debe usar el almacenamiento de blobs, archivos de Azure, tablas de Azure u otras soluciones de almacenamiento.

## <a name="quick-create-using-azure-cli"></a>Crear rápido mediante CLI de Azure

[AZURE.INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Para obtener información general, consulte la [página de inicio principal para los conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Para obtener más documentación, consulte la [página principal de documentación de escala establece](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Por ejemplo plantillas de administrador de recursos con conjuntos de escala, busque "vmss" en las [plantillas de Azure tutorial github repo](https://github.com/Azure/azure-quickstart-templates).

