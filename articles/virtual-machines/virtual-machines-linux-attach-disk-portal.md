<properties
    pageTitle="Adjuntar un disco de datos a una VM Linux | Microsoft Azure"
    description="Cómo adjuntar el disco de datos nueva o existente a una VM Linux en el portal de Azure utiliza el modelo de implementación de administrador de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cynthn"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Cómo adjuntar un disco de datos a una VM Linux en el portal de Azure

Este artículo le muestra cómo adjuntar discos nuevos y existentes a una máquina virtual de Linux a través del portal de Azure. También puede [adjuntar un disco de datos a una máquina virtual de Windows en el portal de Azure](virtual-machines-windows-attach-disk-portal.md). Antes de hacerlo, revisar estas sugerencias:

- El tamaño de la máquina virtual controla cuántos discos de datos que puede adjuntar. Para obtener más información, vea [tamaños para máquinas virtuales](virtual-machines-linux-sizes.md).
- Para utilizar el almacenamiento de Premium, tendrá una máquina virtual DS series o GS-series. Puede utilizar discos de cuentas de almacenamiento estándar y Premium con estas máquinas virtuales. Almacenamiento de Premium está disponible en determinadas regiones. Para obtener información detallada, consulte [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](../storage/storage-premium-storage.md).
- Discos conectados a máquinas virtuales son en realidad .vhd archivos en una cuenta de almacenamiento de Azure. Para obtener más información, consulte [acerca de discos y VHD para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md).
- Para un disco nuevo, no es necesario crear primero porque Azure lo crea cuando lo acople.
- Para un disco existente, el archivo .vhd debe estar disponible en una cuenta de almacenamiento de Azure. Puede usar un .vhd que ya está allí, si no está asociada a otra máquina virtual o cargar sus propios .vhd del archivo a la cuenta de almacenamiento.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Pasos siguientes

Después de agrega el disco, debe prepararlos para su uso. Ver en el sistema operativo del equipo virtual: "Cómo: inicializar un nuevo disco de datos en Linux" en este [artículo](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux).
