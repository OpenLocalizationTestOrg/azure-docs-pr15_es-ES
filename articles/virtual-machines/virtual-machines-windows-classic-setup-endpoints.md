<properties
    pageTitle="Configurar extremos en una máquina virtual de Windows clásico | Microsoft Azure"
    description="Aprenda a configurar los extremos de una máquina virtual de Windows en el portal de clásico Azure para permitir la comunicación con una máquina virtual de Windows Azure."
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

# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Cómo configurar extremos en una máquina virtual de Windows clásica en Azure


Todas las ventanas de máquinas virtuales que cree en Azure utiliza el modelo de implementación clásica automáticamente puede comunican a través de una privada canal de red con otros equipos virtuales en el mismo servicio de nube o una red virtual. Sin embargo, equipos de Internet u otras redes virtuales requieren extremos para dirigir el tráfico de red entrante a una máquina virtual. En este artículo también está disponible para [máquinas virtuales de Linux](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]En el modelo de implementación de **Administrador de recursos** , extremos están configurados con **Grupos de seguridad de red (NSGs)**. Para obtener más información, consulte el [permitir el acceso externo a su máquina virtual con el Portal de Azure] (virtual-machines-windows-nsg-quickstart-portal.md).

Cuando se crea una máquina virtual de Windows en el portal de clásico Azure, extremos comunes como las de escritorio remoto y Windows PowerShell remoto son normalmente creará automáticamente. Puede configurar extremos adicionales al crear la máquina virtual o posteriormente según sea necesario.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Pasos siguientes

* Para usar un cmdlet de PowerShell de Azure para configurar un extremo VM, vea [Agregar AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* Para usar un cmdlet de PowerShell de Azure para administrar una ACL en un extremo, vea [control de acceso de administrar listas (ACL) para extremos con PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Si ha creado una máquina virtual en el modelo de implementación de administrador de recursos, puede usar PowerShell Azure para [crear grupos de seguridad de red](../virtual-network/virtual-networks-create-nsg-arm-ps.md) para controlar el tráfico de la máquina virtual.
