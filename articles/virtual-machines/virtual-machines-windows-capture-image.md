<properties
    pageTitle="Capturar una imagen de máquina virtual de VM generalizado de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo capturar una imagen de máquina virtual de una máquina virtual de Azure generalized creados en el modelo de implementación de administrador de recursos"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>

# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Cómo capturar una imagen de VM desde una VM generalizado de Azure


En este artículo se muestra cómo usar PowerShell Azure para crear una imagen de una VM generalizado de Azure. A continuación, puede usar la imagen para crear otra VM. La imagen incluye el disco de sistema operativo y los discos de datos que se han adjuntado a la máquina virtual. La imagen no incluye los recursos de una red virtual, por lo que necesitará configurar los recursos cuando se crea la nueva máquina virtual. 


## <a name="prerequisites"></a>Requisitos previos

- Debe haber [generalizado de la máquina virtual](virtual-machines-windows-generalize-vhd.md). Generalización una máquina virtual quita toda la información de cuenta personal, entre otras cosas y prepara el equipo que se usará como una imagen.

- Debe tener Azure PowerShell versión 1.0. x o posterior instalado. Si ya no ha instalado PowerShell, lea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para los pasos de instalación.


## <a name="log-in-to-azure-powershell"></a>Inicie sesión en Azure PowerShell

1. Abra Azure PowerShell e inicie sesión su cuenta de Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Se abrirá una ventana emergente para que escribir sus credenciales de cuenta de Azure.

2. Obtener la identificadores de suscripción para las suscripciones disponibles.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Establecer la suscripción correcta utilizando el identificador de suscripción.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Cancelar la máquina virtual y establecer el estado de generalizado       

1. Cancelar la asignación de los recursos de máquina virtual.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```

    Cambia el *estado* de la máquina virtual en el portal de Azure de **Stopped** a **Stopped (asignación)**.

2. Establecer el estado de la máquina virtual en **Generalized**. 

    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```

3. Comprobar el estado de la máquina virtual. La sección **OSState/generalizado** de la máquina virtual debería tener la **DisplayStatus** establecida en **VM generalizado**.  

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Crear la imagen 

1. Copiar la imagen de la máquina virtual en el contenedor de almacenamiento de destino con este comando. La imagen se crea en la misma cuenta de almacenamiento que la máquina virtual original. La `-Path` parámetro guarda una copia de la plantilla JSON localmente. La `-DestinationContainerName` parámetro es el nombre del contenedor que desee colocar las imágenes. Si no existe el contenedor, se crea por usted.

    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```

    Puede obtener la dirección URL de la imagen de la plantilla de archivo JSON. Vaya a los **recursos** > **storageProfile** > **osDisk** > **imagen** > sección**uri** para la ruta de acceso completa de la imagen. La dirección URL de la imagen es similar a: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
    
    También puede comprobar el URI en el portal. Copiar la imagen a un contenedor denominado **sistema** en su cuenta de almacenamiento. 


## <a name="next-steps"></a>Pasos siguientes

- Ahora puede [crear una máquina virtual de la imagen](virtual-machines-windows-create-vm-generalized.md).

