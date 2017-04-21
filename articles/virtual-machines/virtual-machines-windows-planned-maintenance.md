<properties
    pageTitle="Mantenimiento planeado para máquinas virtuales de Windows | Microsoft Azure"
    description="Comprender qué mantenimiento planeado Azure y cómo afecta a las máquinas virtuales de Windows ejecuta en Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="drewm"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="drewm"/>

# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>Mantenimiento planeado para máquinas virtuales de Azure


Comprender qué mantenimiento planeado Azure y cómo puede afectar a la disponibilidad de sus máquinas virtuales de Windows. En este artículo también está disponible para [máquinas virtuales de Linux](virtual-machines-linux-planned-maintenance.md). 

Este artículo proporciona un fondo de proceso de Azure mantenimiento planeado. Si desea solucione reinicia su máquina virtual, puede [leer este blog post que detalla visualización VM reinicie registros](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="why-azure-performs-planned-maintenance"></a>¿Por qué se realiza Azure mantenimiento planeado

Microsoft Azure realiza periódicamente las actualizaciones en todo el mundo para mejorar la confiabilidad, el rendimiento y la seguridad de la infraestructura de host subyacente máquinas virtuales de Windows. Muchas de las actualizaciones se realizan sin ningún impacto a su máquinas virtuales o servicios en la nube, incluidas las actualizaciones de conservación de memoria.

Sin embargo, algunas actualizaciones requieren reiniciar el equipo a los equipos virtuales para aplicar las actualizaciones necesarias en la infraestructura. Las máquinas virtuales se cierran mientras revisión de la infraestructura y, a continuación, se reinicia las máquinas virtuales.

Tenga en cuenta que existen dos tipos de mantenimiento que pueden afectar a la disponibilidad de sus máquinas virtuales: planificado y. Esta página describe cómo Microsoft Azure realiza mantenimiento planeado. Para obtener más información sobre los trabajos de mantenimiento, vea [comprender planeado frente a los trabajos de mantenimiento](virtual-machines-windows-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]
