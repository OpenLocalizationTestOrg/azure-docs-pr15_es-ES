<properties
    pageTitle="Crear VM desde un disco duro virtual generalized | Microsoft Azure"
    description="Obtenga información sobre cómo crear una máquina virtual de Windows desde una imagen de disco duro virtual generalizada con PowerShell de Azure, en el modelo de implementación de administrador de recursos."
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
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-generalized-vhd-image"></a>Crear una máquina virtual de una imagen de disco duro virtual generalizada

Una imagen de disco duro virtual generalized ha tenido toda la información de cuenta personal quitada mediante [Sysprep](virtual-machines-windows-generalize-vhd.md). Puede crear un disco duro virtual generalizado, ejecute Sysprep en una máquina virtual de local, a continuación, [cargar el disco duro virtual a Azure](virtual-machines-windows-upload-image.md), o ejecutar Sysprep en una máquina virtual de Azure existente y [copiar el disco duro virtual](virtual-machines-windows-vhd-copy.md).

Si desea crear una máquina virtual desde un disco duro virtual especializado, consulte [crear una máquina virtual desde un disco duro virtual especializada](virtual-machines-windows-create-vm-specialized.md).

La forma más rápida de crear una máquina virtual desde un disco duro virtual generalized es usar [plantilla de inicio rápido] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image). 


## <a name="prerequisites"></a>Requisitos previos

Si va a usar un disco duro virtual cargado desde una máquina virtual local, como creados con Hyper-V, debe asegurarse de que sigue las instrucciones de [preparar un disco duro virtual de Windows para cargar en Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 

VHD cargados y VHD de máquina virtual de Azure existente necesitan ser generalizado para poder crear una máquina virtual con este método. Para obtener más información, vea [Generalize una máquina virtual de Windows con Sysprep](virtual-machines-windows-generalize-vhd.md). 


## <a name="set-the-uri-of-the-vhd"></a>Establecer el URI del disco duro virtual

El URI para el disco duro virtual usar tiene el formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. En este ejemplo, el disco duro virtual denominado **myVHD** se encuentra en la cuenta de almacenamiento **mystorageaccount** en el contenedor **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Crear una red virtual

Crear la vNet y la subred de la [red virtual](../virtual-network/virtual-networks-overview.md).


1. Crear la subred. En el ejemplo siguiente se crea una subred denominada **mySubnet** en el grupo de recursos **myResourceGroup** con el prefijo de dirección **10.0.0.0/24**.  

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
      
2. Crear la red virtual. En el ejemplo siguiente se crea una red virtual denominada **myVnet** en la ubicación de **Estados Unidos oeste** con el prefijo de dirección de **10.0.0.0/16**.  

    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-network-interface"></a>Crear una interfaz de red y la dirección IP pública

Para habilitar la comunicación con la máquina virtual en la red virtual, necesita una [dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

1. Crear una dirección IP pública. En este ejemplo se crea una dirección IP pública denominada **myPip**. 

    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Crear la NIC. Este ejemplo crea un NIC denominado **myNic**. 

    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Crear una regla RDP y el grupo de seguridad de red

Para poder iniciar sesión en la máquina virtual mediante RDP, debe tener una regla de seguridad que permite el acceso RDP en el puerto 3389. 

Este ejemplo crea un GSN denominado **myNsg** que contiene una regla llamada **myRdpRule** que permite tráfico RDP sobre el puerto 3389. Para obtener más información sobre NSGs, consulte [Abrir puertos a una máquina virtual en Azure con PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Crear una variable de la red virtual

Cree una variable de la red virtual completada. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Crear la máquina virtual

El siguiente script de PowerShell muestra cómo configurar las configuraciones de máquina virtual y usar la imagen de VM cargada como origen de la instalación de nuevo.

</br>


```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential
    
    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"
    
    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"
    
    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"
    
    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"
    
    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"
    
    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage, Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"
    
    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName
    
    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    
    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    
    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
    
    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows
    
    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Compruebe que se creó la máquina virtual 

Cuando haya terminado, debería ver la VM recién creada en el [portal de Azure](https://portal.azure.com) en **Examinar** > **máquinas virtuales de Windows**, o mediante los siguientes comandos de PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Pasos siguientes

Para administrar su nuevo equipo virtual con Azure PowerShell, consulte [máquinas virtuales de administrar con el Administrador de recursos de Azure y PowerShell](virtual-machines-windows-ps-manage.md).


