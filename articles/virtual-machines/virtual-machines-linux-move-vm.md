<properties
    pageTitle="Mover una máquina virtual de Linux | Microsoft Azure"
    description="Mover una VM Linux a otro Azure suscripción o grupo de recursos en el modelo de implementación de administrador de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Mover una VM Linux a otra suscripción o grupo de recursos

Este artículo describe cómo mover una VM Linux entre suscripciones o grupos de recursos. Mover una máquina virtual entre suscripciones puede ser útil si ha creado una máquina virtual en una suscripción de personal y ahora desea mover a la suscripción de su empresa.

> [AZURE.NOTE] Nuevos identificadores de recursos se crean como parte de la. Una vez que se ha movido la máquina virtual, deberá actualizar las herramientas y secuencias de comandos para usar los nuevos identificadores de recursos. 


## <a name="use-the-azure-cli-to-move-a-vm"></a>Utilizar la CLI de Azure para mover una máquina virtual 

Para mover correctamente una máquina virtual, deberá mover la máquina virtual y los recursos de apoyo. Use el comando **Mostrar grupo azure** para todos los recursos en un grupo de recursos y sus identificadores de la lista. Le ayuda a dirigir el resultado de este comando a un archivo para que pueda copiar y pegar los identificadores en comandos.

    azure group show <resourceGroupName>

Para mover una máquina virtual y los recursos a otro grupo de recursos, utilice el comando CLI **mover recursos azure** . En el ejemplo siguiente se muestra cómo mover una máquina virtual y los recursos más comunes que requiere. Se emplean el parámetro **i -** y pasar de una lista de valores separados por comas (sin espacios) de identificadores para los recursos para mover.

    
    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      
    
    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"
    
Si desea mover la máquina virtual y los recursos a una suscripción diferente, agregue el **--destino subscriptionId & #60; destinationSubscriptionID & #62;** parámetro para especificar la suscripción de destino.

Si está trabajando desde el símbolo del sistema en un equipo Windows, debe agregar una **$** delante de los nombres de variable cuando los declara. Esto no es necesario en Linux.

Le pedirá que confirme que desea mover el recurso especificado. Escriba **Y** para confirmar que desea mover los recursos.
    

[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Pasos siguientes

Puede mover varios tipos diferentes de recursos entre las suscripciones y grupos de recursos. Para obtener más información, vea [mover recursos al nuevo grupo de recursos o suscripción](../resource-group-move-resources.md).    