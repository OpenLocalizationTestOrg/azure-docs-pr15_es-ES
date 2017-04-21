<properties
    pageTitle="Conectar máquinas virtuales de Windows en un servicio de nube | Microsoft Azure"
    description="Conectar máquinas virtuales de Windows creadas con el modelo de implementación clásico a un servicio de nube Azure o una red virtual."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Conectar máquinas virtuales de Windows creadas con el modelo de implementación clásica con un servicio de red o en la nube virtual

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Máquinas virtuales de Windows creadas con el modelo de implementación clásica siempre se ubican en un servicio de nube. El servicio de nube actúa como un contenedor y proporciona un nombre único de DNS público, una dirección IP pública y un conjunto de puntos finales para tener acceso a la máquina virtual a través de Internet. El servicio de nube puede estar en una red virtual, pero no es necesario. También puede [conectar máquinas virtuales de Linux con un servicio de nube o de red virtual](virtual-machines-linux-classic-connect-vms.md).

Si no es un servicio de nube en una red virtual, se denomina un servicio de nube *independiente* . Las máquinas virtuales de un servicio de nube independiente sólo puede comunicarse con otros equipos virtuales con nombres DNS públicos de las otras máquinas virtuales y, a continuación, el tráfico viaja por Internet. Si es un servicio de nube en una red virtual, las máquinas virtuales de ese servicio de nube puede comunicarse con todos los otros equipos virtuales en la red virtual sin enviar todo el tráfico a través de Internet.

Si coloca sus máquinas virtuales en el mismo servicio de nube independiente, aún puede usar Equilibrio de carga y conjuntos de disponibilidad. Para obtener información detallada, vea [máquinas virtuales de equilibrio de carga](virtual-machines-windows-load-balance.md) y [Administrar la disponibilidad de máquinas virtuales de Windows](virtual-machines-windows-manage-availability.md). Sin embargo, no puede organizar las máquinas virtuales en subredes o conectarse a un servicio de nube independiente de su red local. Aquí tenemos un ejemplo:

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Pasos siguientes

Después de crear una máquina virtual, es una buena idea agregar [un disco de datos](virtual-machines-windows-classic-attach-disk.md) para que los servicios y las cargas de trabajo tengan una ubicación para almacenar los datos. 