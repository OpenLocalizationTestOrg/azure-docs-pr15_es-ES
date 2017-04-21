<properties
    pageTitle="Crear una copia de la máquina virtual de Windows | Microsoft Azure"
    description="Obtenga información sobre cómo crear una copia de la máquina virtual de Azure especializadas con Windows, en el modelo de implementación de administrador de recursos."
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
    ms.date="09/21/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-specialized-vhd"></a>Crear una máquina virtual desde un disco duro virtual especializado

Crear una nueva máquina virtual adjuntando un disco duro virtual especializado como el disco de sistema operativo con Powershell. Un disco duro virtual especializado mantiene las cuentas de usuario, aplicaciones y otros datos del estado de la máquina virtual original. 

Si desea crear una máquina virtual desde un disco duro virtual generalizado, consulte [crear una máquina virtual de una imagen de disco duro virtual generalizada](virtual-machines-windows-create-vm-generalized.md).

## <a name="create-the-subnet-and-vnet"></a>Crear la subred y vNet

Crear la vNet y la subred de la [red virtual](../virtual-network/virtual-networks-overview.md).

1. Crear la subred. En este ejemplo se crea una subred denominada **mySubNet**, en el grupo de recursos **myResourceGroup**y se establece el prefijo de dirección de subred en **10.0.0.0/24**.

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```

2. Crear la vNet. En este ejemplo se establece el nombre de red virtual **myVnetName**, la ubicación donde desea **Oeste de Estados Unidos**y el prefijo de dirección de la red virtual para **10.0.0.0/16**. 

    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-nic"></a>Crear una dirección IP pública y NIC

Para habilitar la comunicación con la máquina virtual en la red virtual, necesita una [dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

1. Crear la dirección IP pública. En este ejemplo, el nombre de dirección IP público se establece en **myIP**.

    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Crear la NIC. En este ejemplo, el nombre NIC se establece en **myNicName**.

    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Crear una regla RDP y el grupo de seguridad de red

Para poder iniciar sesión en la máquina virtual mediante RDP, debe tener una regla de seguridad que permite el acceso RDP en el puerto 3389. Porque el disco duro virtual para la nueva máquina virtual creado a partir de una existente VM especializada, una vez creada la máquina virtual puede usar una cuenta existente de la máquina virtual de origen que tenían permiso para iniciar sesión con RDP.

En este ejemplo se establece el nombre de GSN **myNsg** y el nombre de la regla RDP a **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

Para obtener más información acerca de los extremos y reglas GSN, vea [Abrir puertos a una máquina virtual en Azure con PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

## <a name="create-the-vm-configuration"></a>Crear la configuración de la máquina virtual

Establecer la configuración de la máquina virtual para adjuntar el disco duro virtual copiado como el sistema operativo de disco duro virtual.


```powershell
    # Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept in a storage account named "myStorageAccount" in a container named "myContainer".
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    
    #Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
    $vmName = "myVM"
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

    #Add the NIC
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

    #Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this Windows-based VHD should be attached to the VM as the OS disk.
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


Si tiene discos de datos que necesitan conectar a la máquina virtual, también deberá agregar lo siguiente: 

```powershell
    # Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun).
    $dataDiskName = $vmName + "dataDisk"
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

Los datos y las direcciones URL de disco del sistema operativo este aspecto: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Puede encontrar en el portal examinando el contenedor de almacenamiento de destino, haga clic en el sistema operativo o el disco duro virtual que se ha copiado de datos y, a continuación, copiar el contenido de la dirección URL.


## <a name="create-the-vm"></a>Crear la máquina virtual

Crear la máquina virtual con las configuraciones que acaba de crear.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Si este comando se realizó correctamente, verá un resultado similar a esta:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
 
```
 
## <a name="verify-that-the-vm-was-created"></a>Compruebe que se creó la máquina virtual 
 
Debe ver la VM recién creada en el [portal de Azure](https://portal.azure.com), en **Examinar** > **máquinas virtuales de Windows**, o mediante los siguientes comandos de PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Pasos siguientes

Para iniciar sesión su nuevo equipo virtual, vaya a la máquina virtual en el [portal](https://portal.azure.com), haga clic en **Conectar**y abra el archivo de escritorio remoto RDP. Use las credenciales de cuenta de la máquina virtual original para iniciar sesión en su nuevo equipo virtual. Para obtener más información, consulte [cómo conectar e iniciar sesión en una máquina virtual Azure con Windows](virtual-machines-windows-connect-logon.md).







