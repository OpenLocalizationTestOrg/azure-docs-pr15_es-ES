<properties
    pageTitle="Crear una máquina virtual en el portal de clásico | Microsoft Azure"
    description="Crear una máquina virtual de Windows en el portal de clásico de Azure."
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
    ms.date="10/18/2016"
    ms.author="cynthn"/>

# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Crear una máquina virtual ejecuta Windows en el portal de clásico de Azure

> [AZURE.SELECTOR]
- [Portal de clásico de Azure](virtual-machines-windows-classic-tutorial.md)
- [PowerShell: Implementación de clásico](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo [realizar estos pasos con el modelo de implementación de administrador de recursos](virtual-machines-windows-hero-tutorial.md) con el **nuevo portal de Azure**. 

En este tutorial se muestra cómo crear una máquina virtual (VM) Azure ejecuta Windows en el portal de clásico de Azure. Usaremos una imagen de Windows Server como ejemplo, pero que es una de las imágenes de muchos que Azure ofrece. Tenga en cuenta que las opciones de imagen dependen de su suscripción. Por ejemplo, imágenes de escritorio de Windows pueden estar disponibles para los suscriptores MSDN.

Esta sección muestra cómo usar la opción **De la Galería** en el portal de clásico Azure para crear la máquina virtual. Esta opción ofrece más opciones de configuración que la opción **Crear rápido** . Por ejemplo, si quiere unirse a una máquina virtual a una red virtual, debe usar la opción **De la Galería** .

También puede crear máquinas virtuales con [sus propias imágenes](virtual-machines-windows-classic-createupload-vhd.md). Para obtener información sobre este y otros métodos, vea [maneras de crear una máquina virtual de Windows](virtual-machines-windows-creation-choices.md).



## <a name="video-walkthrough"></a>Tutorial de vídeo

Esto es un tutorial acerca de este tutorial.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Crear la máquina virtual

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [crear una máquina virtual que utiliza el modelo de implementación de administrador de recursos](virtual-machines-windows-hero-tutorial.md) en el nuevo portal de Azure. 

- Inicie sesión en la máquina virtual. Para obtener instrucciones, vea [iniciar sesión en una máquina virtual ejecuta Windows Server](virtual-machines-windows-classic-connect-logon.md).

- Adjuntar un disco para almacenar datos. Puede adjuntar discos vacíos y discos que contienen datos. Para obtener instrucciones, consulte [adjuntar un disco de datos a una máquina virtual de Windows creada con el modelo de implementación clásico](virtual-machines-windows-classic-attach-disk.md).
