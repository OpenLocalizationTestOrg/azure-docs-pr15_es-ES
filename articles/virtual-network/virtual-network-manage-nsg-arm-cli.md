<properties 
   pageTitle="Administrar mediante la CLI de Azure en el Administrador de recursos de NSGs | Microsoft Azure"
   description="Obtenga información sobre cómo administrar NSGs existentes mediante la CLI de Azure en el Administrador de recursos"
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

# <a name="manage-nsgs-using-the-azure-cli"></a>Administrar NSGs mediante la CLI de Azure

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="retrieve-information"></a>Recuperar información

Puede ver su NSGs existentes, recuperar reglas para un GSN existente y averigüe qué recursos un GSN está asociado a.

### <a name="view-existing-nsgs"></a>Ver NSGs existentes

Para ver la lista de NSGs en un grupo de recursos específico, ejecute el `azure network nsg list` comando tal como se muestra a continuación. 

    azure network nsg list --resource-group RG-NSG

Resultados esperados:

    info:    Executing command network nsg list
    + Getting the network security groups
    data:    Name          Location
    data:    ------------  --------
    data:    NSG-BackEnd   westus
    data:    NSG-FrontEnd  westus
    info:    network nsg list command OK
         
### <a name="list-all-rules-for-an-nsg"></a>Lista de todas las reglas para un GSN

Para ver las reglas de un GSN denominado **GSN cliente**, ejecute el `azure network nsg show` comando tal como se muestra a continuación. 

    azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd

Resultados esperados:
    
    info:    Executing command network nsg show
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Name                            : NSG-FrontEnd
    data:    Type                            : Microsoft.Network/networkSecurityGroups
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    Tags                            : displayName=NSG - Front End
    data:    Security group rules:
    data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
    data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
    data:    rdp-rule                       Internet           *            *               3389              Tcp       Inbound    Allow   100
    data:    web-rule                       Internet           *            *               80                Tcp       Inbound    Allow   101
    data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
    data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
    data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
    data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
    data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
    data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
    info:    network nsg show command OK

>[AZURE.NOTE] También puede usar `azure network nsg rule list --resource-group RG-NSG --nsg-name NSG-FrontEnd` para obtener una lista de las reglas de **Cliente GSN** GSN.

### <a name="view-nsg-associations"></a>Ver las asociaciones de GSN

Para ver qué asociar recursos es la GSN **GSN cliente** , ejecute el `azure network nsg show` comando tal como se muestra a continuación. Observe que la única diferencia es el uso del parámetro **--json** .

    azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json

Busque las propiedades **networkInterfaces** y **subredes** tal como se muestra a continuación:

    "networkInterfaces": [],
    ...
    "subnets": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
        }
    ],
    ...

En el ejemplo anterior, la GSN no está asociada a las interfaces de red (NIC) y está asociada a una subred denominada **front-end**.

## <a name="manage-rules"></a>Administrar reglas

Puede agregar reglas a un GSN existente, editar las reglas existentes y quitar las reglas.

### <a name="add-a-rule"></a>Agregar una regla

Para agregar una regla que permita **el tráfico** al puerto **443** desde cualquier equipo para el **Cliente GSN** GSN, ejecute el `azure network nsg rule create` comando tal como se muestra a continuación.

    azure network nsg rule create --resource-group RG-NSG \
        --nsg-name NSG-FrontEnd \
        --name allow-https \
        --description "Allow access to port 443 for HTTPS" \
        --protocol Tcp \
        --source-address-prefix * \
        --source-port-range * \
        --destination-address-prefix * \
        --destination-port-range 443 \
        --access Allow \
        --priority 102 \
        --direction Inbound     

Resultados esperados:

    info:    Executing command network nsg rule create
    + Looking up the network security rule "allow-https"
    + Creating a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : *
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule create command OK

### <a name="change-a-rule"></a>Cambiar una regla

Para cambiar la regla que creó anteriormente para permitir el tráfico de **Internet** , ejecute el `azure network nsg rule set` comando tal como se muestra a continuación.

    azure network nsg rule set --resource-group RG-NSG \
        --nsg-name NSG-FrontEnd \
        --name allow-https \
        --source-address-prefix Internet

Resultados esperados:

    info:    Executing command network nsg rule set
    + Looking up the network security group "NSG-FrontEnd"
    + Setting a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : Internet
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule set command OK

### <a name="delete-a-rule"></a>Eliminar una regla

Para eliminar la regla que creó anteriormente, ejecute el `azure network nsg rule delete` comando tal como se muestra a continuación.

    azure network nsg rule delete --resource-group RG-NSG \
        --nsg-name NSG-FrontEnd \
        --name allow-https \
        --quiet

>[AZURE.NOTE] La **--silencioso** parámetro garantiza que no es necesario que confirme la eliminación.

Resultados esperados:

    info:    Executing command network nsg rule delete
    + Looking up the network security group "NSG-FrontEnd"
    + Deleting network security rule "allow-https"
    info:    network nsg rule delete command OK

## <a name="manage-associations"></a>Administrar las asociaciones

Puede asociar una GSN a subredes y NIC. También puede desasociar una GSN de cualquier recurso a que está asociado.

### <a name="associate-an-nsg-to-a-nic"></a>Asociar un GSN a una NIC

Para asociar el **Front-end de GSN** GSN a **TestNICWeb1** NIC, ejecute el `azure network nic set` comando tal como se muestra a continuación.

    azure network nic set --resource-group RG-NSG \
        --name TestNICWeb1 \
        --network-security-group-name NSG-FrontEnd

Resultados esperados:

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Looking up the network security group "NSG-FrontEnd"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-nic"></a>Desvincular un GSN desde una NIC

Para eliminar la asociación de **Front-end de GSN** GSN de **TestNICWeb1** NIC, ejecute el `azure network nic set` comando tal como se muestra a continuación.

    azure network nic set --resource-group RG-NSG --name TestNICWeb1 --network-security-group-id ""

>[AZURE.NOTE] Aviso de la "" (vacío) valor para el parámetro de **red-seguridad-Id. de grupo** . Es cómo quitar una asociación a un GSN. No se puede hacer lo mismo con el parámetro de **red-seguridad-en nombre de grupo** .

Resultado esperado:

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-subnet"></a>Desvincular un GSN desde una subred

Para desvincular el **Front-end de GSN** GSN desde la subred **front-end** , ejecute el `azure network vnet subnet set` comando tal como se muestra a continuación.

    azure network vnet subnet set --resource-group RG-NSG \
        --vnet-name TestVNet \
        --name FrontEnd \
        --network-security-group-id ""

Resultados esperados:

    info:    Executing command network vnet subnet set
    + Looking up the subnet "FrontEnd"
    + Setting subnet "FrontEnd"
    + Looking up the subnet "FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:    Type                            : Microsoft.Network/virtualNetworks/subnets
    data:    ProvisioningState               : Succeeded
    data:    Name                            : FrontEnd
    data:    Address prefix                  : 192.168.1.0/24
    data:    IP configurations:
    data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
    data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
    data:
    info:    network vnet subnet set command OK

### <a name="associate-an-nsg-to-a-subnet"></a>Asociar un GSN a una subred

Para asociar el **Front-end de GSN** GSN a la subred **FronEnd** nuevamente, ejecute el `azure network vnet subnet set` comando tal como se muestra a continuación.

    azure network vnet subnet set --resource-group RG-NSG \
        --vnet-name TestVNet \
        --name FrontEnd \
        --network-security-group-name NSG-FronEnd

>[AZURE.NOTE] El comando encima solo funciona porque la GSN **GSN cliente** no está en el mismo grupo de recursos como la red virtual **TestVNet**. Si la GSN está en otro grupo de recursos, debe usar la **--grupo identificador red de seguridad** parámetro en su lugar y proporcionar el identificador completo para el GSN. Puede recuperar el identificador ejecutando **red azure GSN mostrar--c-GSN--front-end de GSN--json de nombre del grupo de recursos** y buscando la propiedad **id** . 

Resultados esperados:

        info:    Executing command network vnet subnet set
        + Looking up the subnet "FrontEnd"
        + Looking up the network security group "NSG-FrontEnd"
        + Setting subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    IP configurations:
        data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
        data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
        data:
        info:    network vnet subnet set command OK

## <a name="delete-an-nsg"></a>Eliminar un GSN

Solo puede eliminar un GSN si no está asociada a ningún recurso. Para eliminar un GSN, siga los pasos siguientes.

1. Para comprobar los recursos asociados a una GSN, ejecute el `azure network nsg show` tal como se muestra en [la vista NSGs asociaciones](#View-NSGs-associations).
2. Si la GSN está asociado a cualquier NIC, ejecute el `azure network nic set` como se muestra en [Dissociate un GSN desde una NIC](#Dissociate-an-NSG-from-a-NIC) para cada NIC. 
3. Si la GSN está asociado a cualquier subred, ejecute el `azure network vnet subnet set` como se muestra en [Dissociate un GSN desde una subred](#Dissociate-an-NSG-from-a-subnet) para cada subred.
4. Para eliminar la GSN, ejecute el `azure network nsg delete` comando tal como se muestra a continuación.

        azure network nsg delete --resource-group RG-NSG --name NSG-FrontEnd --quiet

    Resultados esperados:

        info:    Executing command network nsg delete
        + Looking up the network security group "NSG-FrontEnd"
        + Deleting network security group "NSG-FrontEnd"
        info:    network nsg delete command OK

## <a name="next-steps"></a>Pasos siguientes

- [Habilitar el registro](virtual-network-nsg-manage-log.md) para NSGs.