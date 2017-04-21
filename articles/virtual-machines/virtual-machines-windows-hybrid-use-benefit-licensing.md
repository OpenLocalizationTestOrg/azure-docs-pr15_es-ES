<properties
   pageTitle="Ventaja de usar Azure híbrido para servidor de ventana | Microsoft Azure"
   description="Aprenda a maximizar los beneficios de la garantía de Software de Windows Server para traer licencias locales con Azure"
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
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Beneficios de uso de Azure híbrido para Windows Server

Para los clientes que usan Windows Server con Software Assurance, puede poner su local licencias de Windows Server en Azure y ejecutar máquinas virtuales de Windows Server en Azure a un coste reducido. La ventaja de usar híbrido de Azure le permite ejecutar máquinas virtuales de Windows Server en Azure y obtener facturado solo para el tipo de cálculo base. Para obtener más información, vea la [ventaja de usar híbrido de Azure licencias por página](https://azure.microsoft.com/pricing/hybrid-use-benefit/). En este artículo se explica cómo implementar las máquinas virtuales de Windows Server en Azure usar este beneficio licencias.

> [AZURE.NOTE] No puede usar imágenes de Azure Marketplace para implementar máquinas virtuales de servidor de Windows mediante la ventaja de usar híbrido de Azure. Debe implementar sus máquinas virtuales con plantillas de PowerShell o administrador de recursos para registrar correctamente sus máquinas virtuales como elegible para el descuento de tasa de cálculo base.

## <a name="pre-requisites"></a>Requisitos previos
Hay un par de requisitos previos para usar Azure híbrido usar beneficio de Windows Server máquinas virtuales en Azure:

- Tener instalado el módulo de PowerShell de Azure
- Ha cargado de su disco duro virtual de Windows Server con el almacenamiento de Azure

### <a name="install-azure-powershell"></a>Instale PowerShell Azure
Asegúrese de que tiene [instalado y configurado la última PowerShell de Azure](../powershell-install-configure.md). Incluso si se van a implementar sus máquinas virtuales con plantillas de administrador de recursos, todavía necesita Azure PowerShell instalado para cargar su disco duro virtual de Windows Server (vea el paso siguiente).

### <a name="upload-a-windows-server-vhd"></a>Cargar un disco duro virtual de Windows Server

Para implementar una máquina virtual de servidor de Windows en Azure, debe crear un disco duro virtual que contenga la generación de Windows Server base. Este disco duro virtual debe estar preparado adecuadamente a través de Sysprep antes de cargarlo en Azure. Puede [obtener más información acerca de los requisitos de disco duro virtual y el proceso de Sysprep](./virtual-machines-windows-upload-image.md) y la [Compatibilidad con Sysprep Roles del servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Copia de la máquina virtual antes de ejecutar Sysprep. Una vez que ha preparado su disco duro virtual, cargue el disco duro virtual con su cuenta de almacenamiento de Azure mediante la `Add-AzureRmVhd` cmdlet como sigue:

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] Microsoft SQL Server, SharePoint Server y Dynamics también pueden utilizar la licencia de Software Assurance. Necesita preparar la imagen de Windows Server por instalar componentes de la aplicación y proporcionar las claves de licencia según corresponda y, a continuación, cargar la imagen de disco en Azure. Revise la documentación adecuada para ejecutar Sysprep con la aplicación, como [Consideraciones para instalar SQL Server mediante Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) o [crear una imagen de referencia de SharePoint Server 2016 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).

También puede obtener más información sobre cómo [cargar el disco duro virtual al proceso de Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] En este artículo se centra en implementar máquinas virtuales de Windows Server. También puede implementar máquinas virtuales de cliente de Windows de la misma manera. En los ejemplos siguientes, se reemplaza `Server` con `Client` correctamente.

## <a name="deploy-a-vm-via-powershell-quick-start"></a>Implementar una máquina virtual a través de inicio de rápido de PowerShell
Al implementar la máquina virtual de servidor de Windows a través de PowerShell, tiene un parámetro adicional para `-LicenseType`. Una vez que el disco duro virtual cargado en Azure, crear una nueva máquina virtual usando `New-AzureRmVM` y especifique el tipo de licencia como sigue:

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

Puede [leer un tutorial más detallado sobre cómo implementar una máquina virtual en Azure a través de PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) siguiente o leer a una guía más descriptiva en los distintos pasos para [crear una máquina virtual de Windows con el Administrador de recursos y PowerShell](./virtual-machines-windows-ps-create.md).

## <a name="deploy-a-vm-via-resource-manager"></a>Implementar una máquina virtual mediante el Administrador de recursos
En las plantillas de administrador de recursos, un parámetro adicional para `licenseType` puede especificarse. Puede obtener más información sobre la [creación de plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md). Una vez que el disco duro virtual cargado en Azure, Editar plantilla de administrador de recursos para incluir el tipo de licencia como parte del cálculo proveedor e implementar la plantilla Normal:

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Compruebe que su máquina virtual está utilizando el beneficio de licencias
Una vez que ha implementado la máquina virtual a través del método de implementación PowerShell o administrador de recursos, compruebe el tipo de licencia con `Get-AzureRmVM` como sigue:
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

El resultado es similar a la siguiente:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Esto se compara con implementado sin licencias ventaja de usar híbrido de Azure, por ejemplo, una máquina virtual implementada directamente desde la Galería de Azure VM siguiente:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## <a name="detailed-powershell-walkthrough"></a>Tutorial detallado de PowerShell

Los siguientes pasos detallados de PowerShell mostrar una implementación completa de una máquina virtual. Puede leer más contexto de cmdlets de real y de las diferentes componentes que se creó en [crear una máquina virtual de Windows con el Administrador de recursos y PowerShell](./virtual-machines-windows-ps-create.md). Paso a través de crear el grupo de recursos, cuenta de almacenamiento y una red virtual, a continuación, definir la máquina virtual y por último, cree la máquina virtual.
 
En primer lugar, bien obtener credenciales, establecer una ubicación y el nombre del grupo de recursos:

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Crear una dirección IP pública:

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Definir la subred, NIC y VNET:

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Nombre de la máquina virtual y cree una configuración de:

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Definir su sistema operativo:

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Agregue su NIC VM:

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Definir la cuenta de almacenamiento para usar:

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Cargar su disco duro virtual, preparado adecuadamente y adjuntar a su máquina virtual para su uso:

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Por último, cree la máquina virtual y definir el tipo de licencia para utilizar la ventaja de usar híbrido de Azure:

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## <a name="next-steps"></a>Pasos siguientes

Obtenga más acerca de las [licencias de la ventaja de usar híbrido de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Más información sobre el [uso de plantillas de administrador de recursos](../azure-resource-manager/resource-group-overview.md).
