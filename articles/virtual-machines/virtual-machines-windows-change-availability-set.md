<properties
    pageTitle="Cambiar un conjunto de disponibilidad de VM | Microsoft Azure"
    description="Obtenga información sobre cómo cambiar la disponibilidad para sus máquinas virtuales usando PowerShell de Azure y el modelo de implementación de administrador de recursos."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="drewm"/>



# <a name="change-the-availability-set-for-a-windows-vm"></a>Cambiar la disponibilidad para una máquina virtual de Windows

Los pasos siguientes describen cómo cambiar el conjunto de disponibilidad de una máquina virtual con PowerShell de Azure. Solo pueden agregarse a una disponibilidad establecer cuando se crea una máquina virtual. Para cambiar la disponibilidad de conjunto, debe eliminar y volver a crear la máquina virtual. 

## <a name="change-the-availability-set-using-powershell"></a>Cambiar la disponibilidad establezca con PowerShell

1. Capturar los siguientes detalles de la máquina virtual modificarse.

    Nombre de la máquina virtual
    
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <Name-of-resource-group> -Name <name-of-VM>
    $vm.Name
    ```
 
    Tamaño de memoria virtual
    
    ```powershell
    $vm.HardwareProfile.VmSize
    ```

    Interfaz de red principal de la red y las interfaces de red opcional si existen en la máquina virtual
    
    ```powershell
    $vm.NetworkProfile.NetworkInterfaces[0].Id
    ```

    Perfil de sistema operativo disco

    ```powershell
    $vm.StorageProfile.OsDisk.OsType
    $vm.StorageProfile.OsDisk.Name
    $vm.StorageProfile.OsDisk.Vhd.Uri
    ```

    Perfiles de disco para cada disco de datos 
    
    ```powershell
    $vm.StorageProfile.DataDisks[<index>].Lun
    $vm.StorageProfile.DataDisks[<index>].Vhd.Uri
    ```

    Extensiones VM instaladas 
    
    ```powershell
    $vm.Extensions
    ```

2. Eliminar la máquina virtual sin eliminar cualquiera de los discos o las interfaces de red.

    ```powershell
    Remove-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName> 
    ```

3. Crear la disponibilidad establecer si aún no existe

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName <resourceGroupName> -Name <availabilitySetName> -Location "<location>" 
    ```

4. Vuelva a crear la máquina virtual con el nuevo conjunto de disponibilidad

    ```powershell
    $vm2 = New-AzureRmVMConfig -VMName <VM-name> -VMSize <vm-size> -AvailabilitySetId <availability-set-id>

    Set-AzureRmVMOSDisk -CreateOption "Attach" -VM <vmConfig> -VhdUri <osDiskURI> -Name <osDiskName> [-Windows | -Linux]

    Add-AzureRmVMNetworkInterface -VM <vmConfig> -Id  <nicId> 

    New-AzureRmVM -ResourceGroupName <resourceGroupName> -Location <location> -VM <vmConfig>
    ``` 

5. Agregar discos de datos y extensiones. Para obtener más información, consulte [Adjuntar datos disco VM](virtual-machines-windows-attach-disk-portal.md) y [Ejemplos de configuración de extensión](virtual-machines-windows-extensions-configuration-samples.md). Extensiones y discos de datos pueden agregarse a la máquina virtual con PowerShell o CLI de Azure.

## <a name="example-script"></a>Secuencia de comandos de ejemplo

La siguiente secuencia de comandos proporciona un ejemplo de recopilar la información necesaria, la eliminación de la máquina virtual original y, a continuación, volver a crear en un nuevo conjunto de disponibilidad.

```powershell
    #set variables
    $rg = "demo-resource-group"
    $vmName = "demo-vm"
    $newAvailSetName = "demo-as"
    $outFile = "C:\temp\outfile.txt"

    #Get VM Details
    $OriginalVM = get-azurermvm -ResourceGroupName $rg -Name $vmName

    #Output VM details to file
    "VM Name: " | Out-File -FilePath $outFile 
    $OriginalVM.Name | Out-File -FilePath $outFile -Append

    "Extensions: " | Out-File -FilePath $outFile -Append
    $OriginalVM.Extensions | Out-File -FilePath $outFile -Append

    "VMSize: " | Out-File -FilePath $outFile -Append
    $OriginalVM.HardwareProfile.VmSize | Out-File -FilePath $outFile -Append

    "NIC: " | Out-File -FilePath $outFile -Append
    $OriginalVM.NetworkProfile.NetworkInterfaces[0].Id | Out-File -FilePath $outFile -Append

    "OSType: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.OsType | Out-File -FilePath $outFile -Append

    "OS Disk: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.Vhd.Uri | Out-File -FilePath $outFile -Append

    if ($OriginalVM.StorageProfile.DataDisks) {
    "Data Disk(s): " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.DataDisks | Out-File -FilePath $outFile -Append
    }

    #Remove the original VM
    Remove-AzureRmVM -ResourceGroupName $rg -Name $vmName

    #Create new availability set if it does not exist
    $availSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -ErrorAction Ignore
    if (-Not $availSet) {
    $availset = New-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -Location $OriginalVM.Location
    }

    #Create the basic configuration for the replacement VM
    $newVM = New-AzureRmVMConfig -VMName $OriginalVM.Name -VMSize $OriginalVM.HardwareProfile.VmSize -AvailabilitySetId $availSet.Id
    Set-AzureRmVMOSDisk -VM $NewVM -VhdUri $OriginalVM.StorageProfile.OsDisk.Vhd.Uri  -Name $OriginalVM.Name -CreateOption Attach -Windows

    #Add Data Disks
    foreach ($disk in $OriginalVM.StorageProfile.DataDisks ) { 
    Add-AzureRmVMDataDisk -VM $newVM -Name $disk.Name -VhdUri $disk.Vhd.Uri -Caching $disk.Caching -Lun $disk.Lun -CreateOption Attach -DiskSizeInGB $disk.DiskSizeGB
    }

    #Add NIC(s)
    foreach ($nic in $OriginalVM.NetworkInterfaceIDs) {
        Add-AzureRmVMNetworkInterface -VM $NewVM -Id $nic
    }

    #Create the VM
    New-AzureRmVM -ResourceGroupName $rg -Location $OriginalVM.Location -VM $NewVM -DisableBginfoExtension
```

## <a name="next-steps"></a>Pasos siguientes

Agregar almacenamiento adicional para su máquina virtual agregando un [disco de datos](virtual-machines-windows-attach-disk-portal.md).

