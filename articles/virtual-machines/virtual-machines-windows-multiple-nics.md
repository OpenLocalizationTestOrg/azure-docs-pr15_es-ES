<properties
   pageTitle="Crear una máquina virtual de Windows con varias NIC | Microsoft Azure"
   description="Obtenga información sobre cómo crear una máquina virtual de Windows con varias NIC conectadas a través de plantillas de Azure PowerShell o administrador de recursos."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-windows-vm-with-multiple-nics"></a>Crear una máquina virtual de Windows con varias NIC
Puede crear una máquina virtual (VM) en Azure que tiene varias interfaces de red virtual (NIC) adjuntas. Un escenario común sería tener diferentes subredes para conectividad front-end y back-end o una red dedicado a una solución de copia de seguridad o supervisión. Este artículo proporciona comandos rápidos para crear una máquina virtual con varias NIC adjuntadas. Para obtener información detallada, incluido cómo crear varias NIC dentro de sus propios scripts de PowerShell, obtener más información sobre cómo [implementar máquinas virtuales de múltiples NIC](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Diferentes [tamaños VM](virtual-machines-windows-sizes.md) admite un número distinto de NIC, así que cambiar el tamaño de la máquina virtual según corresponda.

>[AZURE.WARNING] Debe adjuntar varias NIC cuando cree una máquina virtual: no se puede agregar NIC a una máquina virtual existente. Puede [crear una máquina virtual en función de los discos virtuales originales](virtual-machines-windows-vhd-copy.md) y crear varias NIC como implementar la máquina virtual.

## <a name="create-core-resources"></a>Crear recursos principales
Asegúrese de que tiene la [última Azure PowerShell instalado y configurado](../powershell-install-configure.md). Inicie sesión su cuenta de Azure:

```powershell
Login-AzureRmAccount
```

En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores. Nombres de parámetro de ejemplo incluidos `myResourceGroup`, `mystorageaccount`, y `myVM`.

Primero, cree un grupo de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la `WestUs` ubicación:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Crear una cuenta de almacenamiento para almacenar sus máquinas virtuales. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Crear subredes y una red virtual
Definir dos subredes de red virtual: uno para el tráfico front-end y otro para el tráfico de back-end. En el ejemplo siguiente se define dos subredes, denominados `mySubnetFrontEnd` y `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Crear la red virtual y subredes. En el ejemplo siguiente se crea una red virtual denominada `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Crear varias NIC
Cree dos NIC, adjuntando una NIC a la subred front-end y una NIC a la subred back-end. En el ejemplo siguiente se crea dos NIC, denominadas `myNic1` y `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

Normalmente se crea un [grupo de seguridad de la red](../virtual-network/virtual-networks-nsg.md) o [equilibrador de carga](../load-balancer/load-balancer-overview.md) para ayudar a administrar y distribuir el tráfico entre sus máquinas virtuales. El artículo de la [máquina virtual de múltiples NIC más detallada](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) le guía a través de la creación de un grupo de seguridad de la red y asignar NIC.


## <a name="create-the-virtual-machine"></a>Crear la máquina virtual
Ahora empezar a crear la configuración de la máquina virtual. El tamaño de cada máquina virtual tiene un límite para el número total de NIC que puede agregar a una máquina virtual. Obtenga más información acerca de los [tamaños de la máquina virtual de Windows](virtual-machines-windows-sizes.md). 

En primer lugar, establezca sus credenciales VM en el `$cred` variable como sigue:

```powershell
$cred = Get-Credential
```

En el ejemplo siguiente se define una máquina virtual denominada `myVM` y utiliza un tamaño de memoria virtual que admite hasta dos NIC (`Standard_DS2_v2`):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Crear el resto de la configuración de la máquina virtual. En el ejemplo siguiente se crea una máquina virtual de Windows Server 2012 R2:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName Te"MyVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Adjuntar las dos NIC que creó anteriormente:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Configurar el almacenamiento y el disco virtual para su nueva máquina virtual:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Por último, cree una máquina virtual:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Crear varias NIC con plantillas de administrador de recursos
Plantillas de administrador de recursos de Azure use descriptiva archivos JSON para definir su entorno. Obtenga una [Visión general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md). Plantillas de administrador de recursos proporcionan una manera de crear varias instancias de un recurso durante la implementación, como la creación de varias NIC. Utilice *Copiar* para especificar el número de instancias para crear:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Obtenga más información sobre cómo [crear varias instancias con la función *Copiar*](../resource-group-create-multiple.md). 

También puede usar un `copyIndex()` , a continuación, anexar un número a un nombre de recurso, que le permite crear `myNic1`, `MyNic2`, etcetera. A continuación muestra un ejemplo de anexar el valor de índice:

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Puede obtener un ejemplo completo de [creación de varias NIC con plantillas de administrador de recursos](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Pasos siguientes
Asegúrese de revisar los [tamaños de la máquina virtual de Windows](virtual-machines-windows-sizes.md) cuando intenta crear una máquina virtual con varias NIC. Preste atención a la cantidad máxima de NIC es compatible con el tamaño de cada máquina virtual. 

Recuerde que no puede agregar NIC adicionales a una máquina virtual existente, debe crear todas las NIC cuando se implementa la máquina virtual. Tenga cuidado al planear las implementaciones para asegurarse de que tiene todos los la conectividad de red necesarios desde el principio.