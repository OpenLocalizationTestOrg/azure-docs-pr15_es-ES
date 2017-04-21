<properties
    pageTitle="Mover una máquina virtual de Windows | Microsoft Azure"
    description="Mover una máquina virtual de Windows a otro Azure suscripción o grupo de recursos en el modelo de implementación de administrador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Mover una máquina virtual de Windows a otro grupo de recursos o de suscripción de Azure 

Este artículo describe cómo mover una máquina virtual de Windows entre las suscripciones o grupos de recursos. Mover entre suscripciones puede ser útil si creó una máquina virtual en una suscripción de personal y ahora desea mover a la suscripción de su empresa para continuar su trabajo.

> [AZURE.NOTE] Nuevos identificadores de recursos se creará como parte del cambio. Una vez que se ha movido la máquina virtual, debe actualizar las herramientas y secuencias de comandos para usar los nuevos identificadores de recursos. 


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## <a name="use-powershell-to-move-a-vm"></a>Usar Powershell para mover una máquina virtual

Para mover una máquina virtual a otro grupo de recursos, debe asegurarse de que mover todos los recursos dependientes. Para usar el cmdlet mover AzureRMResource, necesita el nombre del recurso y el tipo de recurso. Puede obtener ambas cosas desde el cmdlet AzureRMResource buscar.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
    

Para mover una máquina virtual es necesario mover varios recursos. Podemos crear variables independientes para cada recurso y, a continuación, escríbalas. Este ejemplo incluye la mayoría de los recursos básicas para una máquina virtual, pero puede agregar más según sea necesario.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"
    
    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
    
    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Para mover los recursos a otra suscripción, incluya el parámetro **- DestinationSubscriptionId** . 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Le pedirá que confirme que desea mover los recursos especificados. Escriba **Y** para confirmar que desea mover los recursos.

  
## <a name="next-steps"></a>Pasos siguientes

Puede mover varios tipos diferentes de recursos entre las suscripciones y grupos de recursos. Para obtener más información, vea [mover recursos al nuevo grupo de recursos o suscripción](../resource-group-move-resources.md).    