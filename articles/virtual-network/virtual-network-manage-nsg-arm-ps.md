<properties 
   pageTitle="Administrar con PowerShell en el Administrador de recursos de NSGs | Microsoft Azure"
   description="Obtenga información sobre cómo administrar existente NSGs con PowerShell en el Administrador de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-powershell"></a>Administrar NSGs con PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Recuperar información

Puede ver su NSGs existentes, recuperar reglas para un GSN existente y averigüe qué recursos un GSN está asociado a.

### <a name="view-existing-nsgs"></a>Ver NSGs existentes
Para ver todas las NSGs existentes en una suscripción, ejecute el `Get-AzureRmNetworkSecurityGroup` cmdlet tal como se muestra a continuación.

    Get-AzureRmNetworkSecurityGroup

Resultado esperado:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
                            
    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Para ver la lista de NSGs en un grupo de recursos específico, ejecute el `Get-AzureRmNetworkSecurityGroup` cmdlet tal como se muestra a continuación. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Resultados esperados:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
         
### <a name="list-all-rules-for-an-nsg"></a>Lista de todas las reglas para un GSN

Para ver las reglas de un GSN denominado **GSN cliente**, ejecute el `Get-AzureRmNetworkSecurityGroup` cmdlet tal como se muestra a continuación. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Resultados esperados:
    
    Name                     : rdp-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound
    
    Name                     : web-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

>[AZURE.NOTE] También puede usar `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` a las reglas predeterminadas de **Front-end de GSN** GSN de la lista.

### <a name="view-nsgs-associations"></a>Ver las asociaciones de NSGs

Para ver qué asociar recursos es la GSN **GSN cliente** , ejecute el `Get-AzureRmNetworkSecurityGroup` cmdlet tal como se muestra a continuación.

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Busque las propiedades **NetworkInterfaces** y **subredes** tal como se muestra a continuación:

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

En el ejemplo anterior, la GSN no está asociada a las interfaces de red (NIC) y está asociada a una subred denominada **front-end**.

## <a name="manage-rules"></a>Administrar reglas

Puede agregar reglas a un GSN existente, editar las reglas existentes y quitar las reglas.

### <a name="add-a-rule"></a>Agregar una regla

Para agregar una regla que permita **el tráfico** al puerto **443** desde cualquier equipo para el **Cliente GSN** GSN, siga los pasos siguientes.

1. Ejecutar la `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar la GSN existente y guardarlo en una variable, como se muestra a continuación.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Ejecutar la `Add-AzureRmNetworkSecurityRuleConfig` cmdlet, tal como se muestra a continuación.

        Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange * `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Para guardar los cambios realizados en la GSN, ejecute el `Set-AzureRmNetworkSecurityGroup` cmdlet tal como se muestra a continuación.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Resultado esperado que muestra las reglas de la seguridad:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Cambiar una regla

Para cambiar la regla que creó anteriormente para permitir el tráfico de **Internet** , siga los pasos siguientes.

1. Ejecutar la `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar la GSN existente y guardarlo en una variable, como se muestra a continuación.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Ejecutar la `Set-AzureRmNetworkSecurityRuleConfig` cmdlet, tal como se muestra a continuación.

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange Internet `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Para guardar los cambios realizados en la GSN, ejecute el `Set-AzureRmNetworkSecurityGroup` cmdlet tal como se muestra a continuación.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Resultado esperado que muestra las reglas de la seguridad:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Eliminar una regla

1. Ejecutar la `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar la GSN existente y guardarlo en una variable, como se muestra a continuación.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Ejecutar la `Remove-AzureRmNetworkSecurityRuleConfig` cmdlet, tal como se muestra a continuación.

        Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule

3. Para guardar los cambios realizados en la GSN, ejecute el `Set-AzureRmNetworkSecurityGroup` cmdlet tal como se muestra a continuación.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Resultado esperado que muestra las reglas de seguridad, observe que ya no aparece la **regla https** :

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Administrar las asociaciones

Puede asociar una GSN a subredes y NIC. También puede desasociar una GSN de cualquier recurso a que está asociado.

### <a name="associate-an-nsg-to-a-nic"></a>Asociar un GSN a una NIC

Para asociar el **Front-end de GSN** GSN a **TestNICWeb1** NIC, siga los pasos siguientes.

1. Ejecutar la `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar la GSN existente y guardarlo en una variable, como se muestra a continuación.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Ejecutar la `Get-AzureRmNetworkInterface` cmdlet para recuperar la NIC existente y guardarlo en una variable, como se muestra a continuación.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Establezca la propiedad de **NetworkSecurityGroup** de la variable **NIC** para el valor de la variable **GSN** , tal como se muestra a continuación.

        $nic.NetworkSecurityGroup = $nsg

4. Para guardar los cambios realizados en la NIC, ejecute el `Set-AzureRmNetworkInterface` cmdlet tal como se muestra a continuación.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Resultado esperado que muestra únicamente la propiedad **NetworkSecurityGroup** :

        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Desvincular un GSN desde una NIC

Para eliminar la asociación de **Front-end de GSN** GSN de **TestNICWeb1** NIC, siga los pasos siguientes.

1. Ejecutar la `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar la GSN existente y guardarlo en una variable, como se muestra a continuación.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Ejecutar la `Get-AzureRmNetworkInterface` cmdlet para recuperar la NIC existente y guardarlo en una variable, como se muestra a continuación.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Establezca la propiedad **NetworkSecurityGroup** de la variable **NIC** en **$null**, tal como se muestra a continuación.

        $nic.NetworkSecurityGroup = $null

4. Para guardar los cambios realizados en la NIC, ejecute el `Set-AzureRmNetworkInterface` cmdlet tal como se muestra a continuación.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Resultado esperado que muestra únicamente la propiedad **NetworkSecurityGroup** :

        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Desvincular un GSN desde una subred

Para desvincular el **Front-end de GSN** GSN desde la subred **front-end** , siga los pasos siguientes.

1. Ejecutar la `Get-AzureRmVirtualNetwork` cmdlet para recuperar la VNet existente y guardarlo en una variable, como se muestra a continuación.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Ejecutar la `Get-AzureRmVirtualNetworkSubnetConfig` cmdlet para recuperar la subred **front-end** y guardarlo en una variable, como se muestra a continuación.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

3. Establezca la propiedad **NetworkSecurityGroup** de la variable de **subred** en **$null**, tal como se muestra a continuación.

        $subnet.NetworkSecurityGroup = $null

4. Para guardar los cambios realizados a la subred, ejecute el `Set-AzureRmVirtualNetwork` cmdlet tal como se muestra a continuación.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Resultado esperado que muestra únicamente las propiedades de la subred **front-end** . Observe que no hay una propiedad para **NetworkSecurityGroup**:

            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Asociar un GSN a una subred

Para asociar el **Front-end de GSN** GSN a la subred **FronEnd** nuevamente, siga los pasos siguientes.

1. Ejecutar la `Get-AzureRmVirtualNetwork` cmdlet para recuperar la VNet existente y guardarlo en una variable, como se muestra a continuación.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Ejecutar la `Get-AzureRmVirtualNetworkSubnetConfig` cmdlet para recuperar la subred **front-end** y guardarlo en una variable, como se muestra a continuación.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

1. Ejecutar la `Get-AzureRmNetworkSecurityGroup` cmdlet para recuperar la GSN existente y guardarlo en una variable, como se muestra a continuación.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

3. Establezca la propiedad **NetworkSecurityGroup** de la variable de **subred** en **$null**, tal como se muestra a continuación.

        $subnet.NetworkSecurityGroup = $nsg

4. Para guardar los cambios realizados a la subred, ejecute el `Set-AzureRmVirtualNetwork` cmdlet tal como se muestra a continuación.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Mostrar solo la propiedad **NetworkSecurityGroup** de la subred **front-end** del resultado esperado:

        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Eliminar un GSN

Solo puede eliminar un GSN si no está asociada a ningún recurso. Para eliminar un GSN, siga los pasos siguientes.

1. Para comprobar los recursos asociados a una GSN, ejecute el `azure network nsg show` tal como se muestra en [la vista NSGs asociaciones](#View-NSGs-associations).
2. Si la GSN está asociado a cualquier NIC, ejecute el `azure network nic set` como se muestra en [Dissociate un GSN desde una NIC](#Dissociate-an-NSG-from-a-NIC) para cada NIC. 
3. Si la GSN está asociado a cualquier subred, ejecute el `azure network vnet subnet set` como se muestra en [Dissociate un GSN desde una subred](#Dissociate-an-NSG-from-a-subnet) para cada subred.
4. Para eliminar la GSN, ejecute el `Remove-AzureRmNetworkSecurityGroup` cmdlet tal como se muestra a continuación.

        Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

    >[AZURE.NOTE] La **-Forzar** parámetro garantiza que no es necesario que confirme la eliminación.

## <a name="next-steps"></a>Pasos siguientes

- [Habilitar el registro](virtual-network-nsg-manage-log.md) para NSGs.