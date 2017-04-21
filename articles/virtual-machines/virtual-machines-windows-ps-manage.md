<properties
    pageTitle="Administración de máquinas virtuales con el Administrador de recursos y PowerShell | Microsoft Azure"
    description="Administrar máquinas virtuales con el Administrador de recursos de Azure y PowerShell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>Administrar máquinas virtuales de Azure con el Administrador de recursos y PowerShell

## <a name="install-azure-powershell"></a>Instale PowerShell Azure
 
Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener información sobre cómo instalar la última versión de PowerShell de Azure, seleccionar la suscripción e iniciar sesión con su cuenta.

## <a name="set-variables"></a>Conjunto de variables

Todos los comandos en el artículo requieren el nombre del grupo de recursos que se encuentra la máquina virtual y el nombre de la máquina virtual para administrar. Reemplace el valor de **$rgName** con el nombre del grupo de recursos que contiene la máquina virtual. Reemplace el valor de **$vmName** con el nombre de la máquina virtual. Crear las variables.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>Mostrar información sobre una máquina virtual

Obtener la información de la máquina virtual.
  
    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Devuelve algo parecido a este ejemplo:

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## <a name="stop-a-virtual-machine"></a>Detener una máquina virtual

Detener la máquina virtual.

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Se le solicita confirmación:

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
        
Escriba **Y** para detener la máquina virtual.

Después de unos minutos, devuelve algo parecido a este ejemplo:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>Iniciar una máquina virtual

Inicie la máquina virtual si se ha detenido.

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Después de unos minutos, devuelve algo parecido a este ejemplo:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

Si desea volver a iniciar una máquina virtual que ya se está ejecutando, describe en uso **AzureRmVM reiniciar** siguiente.

## <a name="restart-a-virtual-machine"></a>Reiniciar una máquina virtual

Reinicie la máquina virtual.

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Devuelve algo parecido a este ejemplo:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>Eliminar una máquina virtual

Eliminar la máquina virtual.  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [AZURE.NOTE] Puede usar el **-Forzar** parámetro para omitir el mensaje de confirmación.

Si no usa el - parámetro forzar, se le solicita confirmación:

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Devuelve algo parecido a este ejemplo:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>Actualizar una máquina virtual

Este ejemplo muestra cómo actualizar el tamaño de la máquina virtual.
        
    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
Devuelve algo parecido a este ejemplo:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK
                              
Para obtener una lista de tamaños disponibles para una máquina virtual, vea [tamaños para máquinas virtuales de Azure](virtual-machines-windows-sizes.md) .

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Agregar un disco de datos a una máquina virtual

Este ejemplo muestra cómo agregar un disco de datos a una máquina virtual existente.

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

El disco que agregue no está inicializado. Para inicializar el disco, puede iniciar sesión en él y usar la administración de disco. Si instaló WinRM y un certificado en ella cuando lo creó, puede usar PowerShell remoto iniciar el disco. También puede usar una extensión de script personalizado: 

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

El archivo de script puede contener algo parecido a este código de inicialización los discos:

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## <a name="next-steps"></a>Pasos siguientes

Si hay problemas con una implementación, puede buscar en [implementaciones de grupo de recursos de solución de problemas con el portal de Azure](../resource-manager-troubleshoot-deployments-portal.md)
