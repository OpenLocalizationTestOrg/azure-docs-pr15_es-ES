<properties
    pageTitle="Configurar los extremos de una VM Linux clásica | Microsoft Azure"
    description="Aprenda a configurar extremos para una VM Linux en el portal de clásico Azure para permitir la comunicación con un equipo virtual Linux en Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/13/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Cómo configurar extremos en una máquina virtual clásica de Linux en Azure

Todas las máquinas virtuales de Linux que cree en Azure utiliza el modelo de implementación clásica automáticamente puede comunicarse a través de un canal de una red privada con otros equipos virtuales en el mismo servicio de nube o una red virtual. Sin embargo, equipos de Internet u otras redes virtuales requieren extremos para dirigir el tráfico de red entrante a una máquina virtual. En este artículo también está disponible para [máquinas virtuales de Windows](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]En el modelo de implementación de **Administrador de recursos** , extremos están configurados con **Grupos de seguridad de red (NSGs)**. Para obtener más información, consulte [abrir puertos y extremos] (virtual-máquinas-linux-GSN-quickstart.md).

Cuando se crea una máquina virtual Linux en el portal de clásico Azure, un extremo de Shell seguro (SSH) es normalmente creará automáticamente. Puede configurar extremos adicionales al crear la máquina virtual o posteriormente según sea necesario.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Pasos siguientes

* También puede crear un extremo de la máquina virtual mediante la [interfaz de línea de comandos de Azure](../virtual-machines-command-line-tools.md). Ejecute el comando **crear azure vm extremo** .

* Si ha creado una máquina virtual en el modelo de implementación de administrador de recursos, puede utilizar la CLI de Azure en modo de administrador de recursos para [crear grupos de seguridad de red](../virtual-network/virtual-networks-create-nsg-arm-cli.md) para controlar el tráfico de la máquina virtual.