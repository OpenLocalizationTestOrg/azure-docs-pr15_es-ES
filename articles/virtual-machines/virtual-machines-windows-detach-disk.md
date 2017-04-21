<properties
    pageTitle="Desvincular un disco de datos de una máquina virtual de Windows | Microsoft Azure"
    description="Aprenda a desvincular un disco de datos de una máquina virtual en Azure utiliza el modelo de implementación de administrador de recursos."
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



# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Cómo desvincular un disco de datos de una máquina virtual de Windows


Cuando ya no necesite un disco de datos que está conectado a una máquina virtual, puede separar fácilmente. Quita el disco de la máquina virtual, pero no eliminarlo de almacenamiento. 

> [AZURE.WARNING] Si se desconecta un disco no se eliminan automáticamente. Si se ha suscrito a almacenamiento Premium, seguirá incurre en gastos de almacenamiento para el disco. Para obtener más información, consulte [precios y facturación al usar almacenamiento Premium](../storage/storage-premium-storage.md#pricing-and-billing). 

Si desea volver a usar los datos existentes en el disco, puede volver a asociar a la misma máquina virtual u otro.  


## <a name="detach-a-data-disk-using-the-portal"></a>Desvincular un disco de datos con el portal

1. En el hub de portal, seleccione **máquinas virtuales de Windows**.

2. Seleccione la máquina virtual que tiene el disco de datos que desea desasociar y, a continuación, haga clic en **todas las opciones**.

3. En el módulo de **configuración** , seleccione **discos**.

4. En el módulo de **discos** , seleccione el disco de datos que desea desasociar.

5. En el módulo para el disco de datos, haga clic en **Desasociar**.


    ![Captura de pantalla que muestra el botón Desasociar.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

El disco de almacenamiento, pero ya no está conectado a una máquina virtual.


## <a name="detach-a-data-disk-using-powershell"></a>Desvincular un disco de datos con PowerShell

En este ejemplo, el primer comando obtiene la máquina virtual denominada **MyVM07** en el grupo de recursos de **RG11** mediante el cmdlet Get-AzureRmVM. El comando almacena la máquina virtual en la variable **$VirtualMachine** . 

El segundo comando quita el disco de datos denominado DataDisk3 de la máquina virtual. 

El comando final actualiza el estado de la máquina virtual para completar el proceso de quitar el disco de datos.

    $VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
    Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
    Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


Para obtener más información, vea [Quitar AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## <a name="next-steps"></a>Pasos siguientes

Si desea volver a utilizar el disco de datos, puede simplemente [adjuntarlo a otra VM](virtual-machines-windows-attach-disk-portal.md)
