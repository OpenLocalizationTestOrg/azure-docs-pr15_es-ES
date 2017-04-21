<properties
    pageTitle="Crear una escala de máquina Virtual que establezca con PowerShell | Microsoft Azure"
    description="Crear una escala de máquina Virtual que establezca con PowerShell"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Crear una escala de máquina virtual de Windows que establezca con PowerShell de Azure

Estos pasos siguen un enfoque rellenar en blanco para crear un conjunto de escala de Azure máquina virtual. Vea [Información general conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-overview.md) para obtener más información acerca de los conjuntos de escala.

Tardará unos 30 minutos para realizar los pasos de este artículo.

## <a name="step-1-install-azure-powershell"></a>Paso 1: Instale PowerShell Azure

Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener información sobre cómo instalar la última versión de PowerShell de Azure, seleccionar la suscripción e iniciar sesión con su cuenta.

## <a name="step-2-create-resources"></a>Paso 2: Creación de recursos

Crear los recursos que son necesarios para el nuevo conjunto de escala.

### <a name="resource-group"></a>Grupo de recursos

Un conjunto de escala de la máquina virtual debe estar incluido en un grupo de recursos.

1. Obtener una lista de ubicaciones disponibles donde puede colocar recursos:

        Get-AzureLocation | Sort Name | Select Name

2. Seleccione una ubicación que mejor le convenga, reemplace el valor de **$locName** con ese nombre de ubicación y, a continuación, cree la variable:

        $locName = "location name from the list, such as Central US"

3. Reemplace el valor de **$rgName** con el nombre que desea usar para el nuevo grupo de recursos y, a continuación, cree la variable: 

        $rgName = "resource group name"
        
4. Crear el grupo de recursos:
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Verá algo parecido a este ejemplo:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Cuenta de almacenamiento

Se utiliza una cuenta de almacenamiento por una máquina virtual para almacenar el disco de sistema operativo y el diagnósticos datos utilizados para la escala. Cuando sea posible, es recomendable tener una cuenta de almacenamiento para cada máquina virtual creado en un conjunto de escala. Si no es posible, planificación de no más de 20 máquinas virtuales por cuenta de almacenamiento. El ejemplo en este artículo muestra tres cuentas de almacenamiento que se ha creado tres equipos virtuales.

1. Reemplace el valor de **$saName** con un nombre para la cuenta de almacenamiento. Probar el nombre de exclusividad. 

        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName

    Si la respuesta es **True**, el nombre de la propuesta es único.

3. Reemplace el valor de **$saType** con el tipo de la cuenta de almacenamiento y, a continuación, cree la variable:  

        $saType = "storage account type"
        
    Los valores posibles son: Standard_LRS, Standard_GRS, Standard_RAGRS o Premium_LRS.
        
4. Crear la cuenta:
    
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Verá algo parecido a este ejemplo:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

5. Repita los pasos 1 a 4 para crear tres cuentas de almacenamiento, por ejemplo myst1, myst2 y myst3.

### <a name="virtual-network"></a>Red virtual

Una red virtual se requiere para los equipos virtuales en el conjunto de escala.

1. Reemplace el valor de **$subnetName** con el nombre que desea usar para la subred en la red virtual y, a continuación, cree la variable: 

        $subnetName = "subnet name"
        
2. Crear la configuración de subred:
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
    El prefijo de dirección puede ser diferente en su red virtual.

3. Reemplace el valor de **$netName** con el nombre que desea usar para la red virtual y, a continuación, cree la variable: 

        $netName = "virtual network name"
        
4. Crear una red virtual:
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Configuración del conjunto de escala

Tiene todos los recursos que necesita para la escala de conjunto de configuración, vamos a crearlo.  

1. Reemplace el valor de **$ipName** con el nombre que desea usar para la configuración de IP y, a continuación, cree la variable: 

        $ipName = "IP configuration name"
        
2. Crear la configuración de IP:

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Reemplace el valor de **$vmssConfig** con el nombre que desea usar para la configuración del conjunto de escala y, a continuación, cree la variable:   

        $vmssConfig = "Scale set configuration name"
        
3. Crear la configuración para el conjunto de escala:

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    Este ejemplo muestra que una escala de establece que se creó con tres equipos virtuales. Vea [Información general conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-overview.md) para obtener más información sobre la capacidad de conjuntos de escala. Este paso también incluye establecer el tamaño (denominado SkuName) de los equipos virtuales en el conjunto. Para buscar un tamaño que satisfaga sus necesidades, mire [tamaños para máquinas virtuales](../virtual-machines/virtual-machines-windows-sizes.md).
    
4. Agregue la configuración de la interfaz de red a la configuración del conjunto de escala:
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    Verá algo parecido a este ejemplo:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Perfil de sistema operativo

1. Reemplace el valor de **$computerName** con el prefijo de nombre del equipo que desea usar y, a continuación, cree la variable: 

        $computerName = "computer name prefix"
        
2. Reemplace el valor de **$adminName** el nombre de la cuenta de administrador en los equipos virtuales y, a continuación, cree la variable:

        $adminName = "administrator account name"
        
3. Reemplace el valor de **$adminPassword** con la contraseña de la cuenta y, a continuación, cree la variable:

        $adminPassword = "password for administrator accounts"
        
4. Cree el perfil de sistema operativo:

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Perfil de almacenamiento

1. Reemplace el valor de **$storageProfile** con el nombre que desea usar para el perfil de almacenamiento y, a continuación, cree la variable:  

        $storageProfile = "storage profile name"
        
2. Crear las variables que definen la imagen para usar:  
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    Para buscar la información sobre otras imágenes a utilizar, mire [navegar y seleccionar máquina virtual Azure imágenes con Windows PowerShell y CLI Azure](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).
        
3. Reemplace el valor de **$vhdContainers** con una lista que contiene las rutas de acceso donde se almacenan los discos duro virtuales, como "https://mystorage.blob.core.windows.net/vhds" y, a continuación, cree la variable:
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. Cree el perfil de almacenamiento:

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Conjunto de escala de máquina virtual

Por último, puede crear el conjunto de escala.

1. Reemplace el valor de **$vmssName** con el nombre del conjunto de escala de máquina virtual y, a continuación, cree la variable:

        $vmssName = "scale set name"
        
2. Crear el conjunto de escala:

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Verá algo parecido a este ejemplo que muestra una implementación correcta:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>Paso 3: Explorar recursos

Use estos recursos para explorar el conjunto de escala de máquina virtual que ha creado:

- Azure portal - una cantidad limitada de información está disponible con el portal.
- [Explorador de recursos de azure](https://resources.azure.com/) : esta herramienta es la mejor para explorar el estado actual de su conjunto de escala.
- Azure PowerShell: Utilice este comando para obtener información:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## <a name="next-steps"></a>Pasos siguientes

- Administrar el conjunto de escala que acaba de crear con la información de [administrar máquinas virtuales de un conjunto de escala de máquina Virtual](virtual-machine-scale-sets-windows-manage.md)
- Considere la posibilidad de configurar la escala automática de la escala establecida con información de [conjuntos de escala automático de escala y máquina virtual](virtual-machine-scale-sets-autoscale-overview.md)
- Más información sobre la escala vertical revisando [Autoescala Vertical con conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md)
