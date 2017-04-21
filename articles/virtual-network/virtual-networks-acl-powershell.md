<properties
   pageTitle="Cómo administrar listas de Control de acceso (ACL) para extremos con PowerShell"
   description="Obtenga información sobre cómo administrar las ACL con PowerShell"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-manage-access-control-lists-acls-for-endpoints-by-using-powershell"></a>Cómo administrar listas de Control de acceso (ACL) para extremos con PowerShell

Puede crear y administrar listas de Control de acceso (ACL) de red para extremos con PowerShell Azure o en el Portal de administración. En este tema, encontrará los procedimientos para tareas comunes de ACL que se pueden completar mediante PowerShell. Para ver la lista de los cmdlets de PowerShell de Azure vea [Cmdlets de administración de Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Para obtener más información sobre las ACL, vea [¿Qué es una lista de Control de acceso (ACL) de red?](virtual-networks-acl.md). Si desea administrar su ACL mediante el Portal de administración, vea [cómo establecer extremos a una máquina Virtual](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Administrar las ACL de red con PowerShell de Azure

Puede usar cmdlets de PowerShell de Azure para crear, quitar y configurar (establecido) listas de Control de acceso (ACL) de red. Hemos incluido algunos ejemplos de algunas de las maneras en que puede configurar una ACL usando PowerShell.

Para recuperar una lista completa de los cmdlets de PowerShell de ACL, puede utilizar cualquiera de las siguientes acciones:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Crear una ACL de red con las reglas que permitan el acceso desde una subred remota

El ejemplo siguiente muestra una forma de crear una nueva ACL que contiene reglas. Esta ACL se aplica a un extremo de la máquina virtual. Las reglas ACL en el ejemplo siguiente permiten el acceso desde una subred remota. Para crear una nueva ACL de red con reglas de permiso para una subred remota, abra un Azure PowerShell ISE. Copie y pegue el script a continuación, configuración de la secuencia de comandos con sus propios valores y, a continuación, ejecute el script.

1. Crear el nuevo objeto ACL de red.

        $acl1 = New-AzureAclConfig

1. Configurar una regla que permita el acceso de una subred remota. En el ejemplo siguiente, se establece regla *100* (que tiene prioridad sobre la regla 200 o superior) para permitir el acceso de *10.0.0.0/8* de subred remota al extremo de la máquina virtual. Reemplace los valores con sus propios requisitos de configuración. El nombre "Configuración de SharePoint ACL" debe reemplazarse con el nombre descriptivo que desea llamar a esta regla.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"

1. Más reglas, repita el cmdlet, reemplazando los valores con sus propios requisitos de configuración. Asegúrese de cambiar la regla para reflejar el orden en que desea aplicar las reglas de ordenación de número. El número menor de regla tiene prioridad sobre el número más alto.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"

1. A continuación, puede crear un nuevo punto final (Agregar) o configurar las ACL para un extremo existente (establecido). En este ejemplo, se agregará un nuevo punto final de máquina virtual denominado "web" y actualizar el extremo de la máquina virtual con la configuración de ACL.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM

1. A continuación, combinar los cmdlets y ejecute la secuencia de comandos. En este ejemplo, los cmdlets combinados tendría un aspecto similar a este:

        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Quitar una regla de ACL de red que permite el acceso desde una subred remota

El ejemplo siguiente muestra una manera de quitar una regla de ACL de red.  Para quitar una regla de ACL de red con reglas de permiso para una subred remota, abra un Azure PowerShell ISE. Copie y pegue el script a continuación, configuración de la secuencia de comandos con sus propios valores y, a continuación, ejecute el script.

1. Primer paso es obtener el objeto de ACL de red del extremo de la máquina virtual. A continuación, deberá quitar la regla ACL. En este caso, nos estamos quitando por Id. de regla Esto sólo eliminará el identificador de regla 0 de la ACL. No elimina el objeto ACL desde el extremo de la máquina virtual.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. A continuación, debe aplicar el objeto de red ACL al extremo de máquina virtual y actualizar la máquina virtual.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Quitar una ACL de red de un extremo de máquina virtual

En algunos escenarios que podría querer quitar un objeto de ACL de red de un extremo de la máquina virtual. Para ello, abra un Azure PowerShell ISE. Copie y pegue el script a continuación, configuración de la secuencia de comandos con sus propios valores y, a continuación, ejecute el script.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Pasos siguientes

[¿Qué es una lista de Control de acceso (ACL) de red?](virtual-networks-acl.md)
