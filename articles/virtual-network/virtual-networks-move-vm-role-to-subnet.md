<properties 
   pageTitle="Cómo mover una instancia VM o rol a una subred diferente"
   description="Aprenda a mover máquinas virtuales y las instancias de función a una subred diferente"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>Cómo mover una instancia VM o rol a una subred diferente

Puede usar PowerShell para mover sus máquinas virtuales de una subred a otra en la misma red virtual (VNet). Instancias de la función se pueden mover el CSCFG de edición, en lugar de usar PowerShell.

>[AZURE.NOTE] Este artículo contiene información relativa a Azure solo implementaciones clásicas.

¿Por qué mover máquinas virtuales a otra subred? Migración de subred es útil cuando la subred anterior es demasiado pequeña y no se puede expandir debido a máquinas virtuales en ejecución existentes en esa subred. En ese caso, puede crear una subred nuevos, más grande y migrar las máquinas virtuales a la subred nueva y luego una vez completada la migración, puede eliminar el antigua subred vacía.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Cómo mover una máquina virtual a otra subred

Para mover una máquina virtual, ejecute el cmdlet de PowerShell de conjunto AzureSubnet, usando el ejemplo siguiente como plantilla. En el ejemplo siguiente, nos desplazamos TestVM desde su subred presentar, a la subred 2. Asegúrese de editar el ejemplo para reflejar su entorno. Tenga en cuenta que, siempre que ejecute el cmdlet de actualización AzureVM como parte de un procedimiento, se reinicie la máquina virtual como parte del proceso de actualización.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
  	| Set-AzureSubnet –SubnetNames Subnet-2 `
  	| Update-AzureVM

Si especifica una dirección IP estática de privado interna para su máquina virtual, debe desactivar esta configuración para poder mover la máquina virtual en una nueva subred. En ese caso, utilice la siguiente:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Set-AzureSubnet -SubnetNames Subnet-2 `
  	| Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Para mover una instancia de rol a otra subred

Para mover una instancia de funciones, editar el archivo CSCFG. En el ejemplo siguiente, nos cambiaremos "Role0" en una red virtual *VNETName* desde su subred presentar a *subred 2*. Porque ya se ha implementado la instancia de rol, solo podrá cambiar el nombre de subred = subred 2. Asegúrese de editar el ejemplo para reflejar su entorno.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
