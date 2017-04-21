<properties
    pageTitle="Cambiar el tamaño de una máquina virtual de Windows | Microsoft Azure"
    description="Cambiar el tamaño de una máquina virtual de Windows creada en el modelo de implementación de administrador de recursos, uso de Powershell de Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>

    
# <a name="resize-a-windows-vm"></a>Cambiar el tamaño de una máquina virtual de Windows

Este artículo le muestra cómo cambiar el tamaño de una máquina virtual de Windows, creado en el modelo de implementación de administrador de recursos con Powershell de Azure.

Después de crear una máquina virtual (VM), puede ajustar la máquina virtual hacia arriba o hacia abajo cambiando el tamaño de la máquina virtual. En algunos casos, es necesario desasignar la máquina virtual. Esto puede suceder si el nuevo tamaño no está disponible en el clúster de hardware que actualmente se hospeda la máquina virtual.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Cambiar el tamaño de una máquina virtual de Windows no está en un conjunto de disponibilidad

1. Lista de los tamaños VM que están disponibles en el clúster de hardware donde se hospeda la máquina virtual. 

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```

2. Si el tamaño deseado en la lista, ejecute los comandos siguientes para cambiar el tamaño de la máquina virtual. Si el tamaño deseado no aparece, vaya al paso 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Si el tamaño deseado no aparece, ejecute los comandos siguientes para desasignar la máquina virtual, cambiar su tamaño y reinicie la máquina virtual.

    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [AZURE.WARNING] Cancelar la asignación de la máquina virtual libera las direcciones IP dinámicas asignadas a la máquina virtual. Los discos de sistema operativo y los datos no se ven afectados. 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Cambiar el tamaño de una máquina virtual de Windows en un conjunto de disponibilidad

Si el nuevo tamaño de una máquina virtual en un conjunto de disponibilidad no está disponible en el clúster de hardware hospedando la máquina virtual, todas las máquinas virtuales en el conjunto de disponibilidad tendrá cancelar la asignación para cambiar el tamaño de la máquina virtual. También debe actualizar el tamaño de las demás VM en la disponibilidad establecer después una VM que se ha cambiado. Para cambiar el tamaño de una máquina virtual en un conjunto de disponibilidad, realice los pasos siguientes.

1. Lista de los tamaños VM que están disponibles en el clúster de hardware donde se hospeda la máquina virtual.

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```

2. Si el tamaño deseado en la lista, ejecute los comandos siguientes para cambiar el tamaño de la máquina virtual. Si no aparece, vaya al paso 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Si el tamaño deseado no aparece, continúe con los pasos siguientes para desasignar máquinas virtuales de todos en el conjunto de disponibilidad, cambiar el tamaño de las máquinas virtuales y reiniciar ellos.

4.  Detener todas las máquinas virtuales en el conjunto de disponibilidad.

    ```powershell
    $rg = "<resourceGroupName>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
    } 
    ```
              
5.  Cambiar el tamaño y reinicie las máquinas virtuales en el conjunto de disponibilidad.

    ```powershell
    $rg = "<resourceGroupName>"
    $newSize = "<newVmSize>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
        $vm.HardwareProfile.VmSize = $newSize
        Update-AzureRmVM -ResourceGroupName $rg -VM $vm
        Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
    }
    ```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más escalabilidad ejecutar varias instancias VM y escalar. Para obtener más información, vea [cambiar automáticamente la escala de equipos de Windows en un conjunto de escala de máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).



