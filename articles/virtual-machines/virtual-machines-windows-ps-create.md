<properties
    pageTitle="Crear una máquina virtual de Azure con PowerShell | Microsoft Azure"
    description="Usar PowerShell de Azure y el Administrador de recursos de Azure para crear fácilmente una nueva máquina virtual con Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/21/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Crear una máquina virtual de Windows con el Administrador de recursos y PowerShell

Este artículo le muestra cómo crear rápidamente una máquina Virtual de Azure ejecutan Windows Server y los recursos que necesita con [El Administrador de recursos](../azure-resource-manager/resource-group-overview.md) y PowerShell. 

Todos los pasos de este artículo son necesarios para crear una máquina virtual y tardará unos 30 minutos para realizar los pasos. Reemplazar valores de parámetro de ejemplo en los comandos con nombres pertinentes para su entorno.

## <a name="step-1-install-azure-powershell"></a>Paso 1: Instale PowerShell Azure

Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener información sobre cómo instalar la última versión de PowerShell de Azure, seleccionar la suscripción e iniciar sesión con su cuenta.
        
## <a name="step-2-create-a-resource-group"></a>Paso 2: Crear un grupo de recursos

Todos los recursos deben estar contenidos en un grupo de recursos, así que le permite crear en primer lugar.  

1. Obtener una lista de ubicaciones disponibles donde se pueden crear recursos.

    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. Establecer la ubicación de los recursos. Este comando establece la ubicación **centralus**.

    ```powershell
    $location = "centralus"
    ```
    
3. Crear un grupo de recursos. Este comando crea el grupo de recursos denominado **myResourceGroup** en la ubicación que ha configurado.

    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```
    
## <a name="step-3-create-a-storage-account"></a>Paso 3: Crear una cuenta de almacenamiento

Se necesita una [cuenta de almacenamiento](../storage/storage-introduction.md) para almacenar el disco duro virtual que se utiliza la máquina virtual que cree. Nombres de cuenta de almacenamiento deben estar entre 3 y 24 caracteres de longitud y pueden contener números y letras minúsculas solo.

1. Probar el nombre de la cuenta de almacenamiento para exclusividad. Este comando comprueba el nombre **myStorageAccount**.

    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
    
    Si el comando devuelve **True**, el nombre de la propuesta es único en Azure. 
    
2. Ahora, cree la cuenta de almacenamiento.
    
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```
    
## <a name="step-4-create-a-virtual-network"></a>Paso 4: Creación de una red virtual

Todos los equipos virtuales forman parte de una [red virtual](../virtual-network/virtual-networks-overview.md).

1. Crear una subred de la red virtual. Este comando crea una subred denominada **mySubnet** con un prefijo de dirección 10.0.0.0/24.
        
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
    
2. Ahora, cree la red virtual. Este comando crea una red virtual denominada **myVnet** mediante la subred que ha creado y un prefijo de dirección de **10.0.0.0/16**.

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```
        
## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Paso 5: Crear una interfaz de red y la dirección IP pública

Para habilitar la comunicación con la máquina virtual en la red virtual, necesita una [dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md) y una interfaz de red.

1. Crear la dirección IP pública. Este comando crea una dirección IP pública denominada **myPublicIp** con un método de asignación de **dinámica**.
 
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Dynamic
    ```
        
2. Crear la interfaz de red. Este comando crea una interfaz de red denominada **myNIC**.

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```
       
## <a name="step-6-create-a-virtual-machine"></a>Paso 6: Crear una máquina virtual

Ahora que tiene todos los elementos en su lugar, es hora de crear la máquina virtual.

1. Ejecutar este comando para establecer el nombre de la cuenta de administrador y la contraseña para la máquina virtual.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    La contraseña debe estar en 12-123 caracteres y tiene al menos una minúscula, un carácter de mayúsculas, un número y un carácter especial. 
        
2. Crear el objeto de configuración de la máquina virtual. Este comando crea un objeto de configuración denominado **myVmConfig** que define el nombre de la máquina virtual y el tamaño de la máquina virtual.

    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
     
    Para obtener una lista de tamaños disponibles para una máquina virtual, vea [tamaños para máquinas virtuales de Azure](virtual-machines-windows-sizes.md) .
    
3. Establecer configuración de sistema operativo de la máquina virtual. Este comando establece el nombre del equipo, tipo de sistema operativo y las credenciales de cuenta para la máquina virtual.

    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```
    
4. Definir la imagen que use para aprovisionar la máquina virtual. Este comando define la imagen de Windows Server para la máquina virtual. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
        
    Para obtener más información acerca de cómo seleccionar imágenes para usar, consulte [navegar y seleccionadas imágenes de máquina virtual de Windows Azure con PowerShell o CLI](virtual-machines-windows-cli-ps-findimage.md).
        
5. Agregar la interfaz de red que ha creado a la configuración.

    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
        
6. Definir el nombre y la ubicación del disco duro VM. El archivo en el disco duro virtual se almacena en un contenedor. Este comando crea el disco en un contenedor denominado **vhds/WindowsVMosDisk.vhd** en la cuenta de almacenamiento que ha creado.

    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
        
7. Agregar información de disco del sistema operativo a la configuración de la máquina virtual. Reemplace el valor de **$diskName** con un nombre para el disco de sistema operativo. Cree la variable y agregue la información del disco a la configuración.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
        
8. Por último, cree la máquina virtual.

    ```
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```
                                  
## <a name="next-steps"></a>Pasos siguientes

- Si hay problemas con la implementación, sería un paso siguiente buscar en [implementaciones de grupo de recursos de solución de problemas con el portal de Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Obtenga información sobre cómo administrar la máquina virtual que ha creado revisando [máquinas virtuales de administrar con el Administrador de recursos de Azure y PowerShell](virtual-machines-windows-ps-manage.md).
- Aprovechar las ventajas de usar una plantilla para crear una máquina virtual con la información en [crear una máquina virtual de Windows con una plantilla de administrador de recursos](virtual-machines-windows-ps-template.md)
