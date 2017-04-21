<properties 
   pageTitle="Controlar el enrutamiento y utilizar equipos virtuales en el Administrador de recursos con la CLI de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo controlar el enrutamiento y usar accesorios virtuales con la CLI de Azure"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-the-azure-cli"></a>Crear rutas (UDR) definidas por el usuario en Azure CLI

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación de administrador de recursos. También puede [crear UDRs en el modelo de implementación clásico](virtual-network-create-udr-classic-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Los comandos de CLI de Azure de ejemplo siguientes esperan un entorno simple ya ha creado basándose en el ejemplo anterior. Si desea ejecutar los comandos que se muestran en este documento, primero implementar [esta plantilla](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)para crear el entorno de prueba, haga clic en **implementar a Azure**, reemplace los valores de parámetro de forma predeterminada, si es necesario y siga las instrucciones en el portal.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Crear la UDR para la subred front-end
Para crear la tabla de rutas y la ruta es necesario para la subred front-end basada en el ejemplo anterior, siga los pasos siguientes.

3. Ejecutar la **`azure network route-table create`** comando para crear una tabla de ruta para la subred front-end.

        azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

    Resultado:

        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK

    Parámetros:
    - **-g (o--grupo de recursos)**. Nombre del grupo de recursos donde se creará el UDR. En el escenario, *TestRG*.
    - **-l (o--ubicación)**. Región de Azure donde se creará el nuevo UDR. En el escenario, *westus*.
    - **-n (o--nombre)**. Nombre para el nuevo UDR. En el escenario, *UDR-front-end*.

4. Ejecutar la **`azure network route-table route create`** comando para crear una ruta en la tabla de ruta creada anteriormente para enviar todo el tráfico destinado a la subred de back-end (192.168.2.0/24) para **FW1** VM (192.168.0.4).

        azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

    Resultado:

        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK

    Parámetros:
    - **-r (o--nombre de la tabla de ruta)**. Nombre de la tabla de ruta donde se agregará la ruta. En el escenario, *UDR-front-end*.
    - **-a (o--prefijo de dirección)**. Prefijo de dirección de la subred donde paquetes están destinados a. En el escenario, *192.168.2.0/24*.
    - **-y (o--tipo de salto siguiente)**. Tipo de objeto tráfico se enviarán a. Valores posibles son *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*o *Ninguno*.
    - **-p (o--siguiente salto-dirección ip de**). Dirección IP del siguiente salto. En el escenario, *192.168.0.4*.

5. Ejecutar la **`azure network vnet subnet set`** comando para asociar la tabla de ruta creada anteriormente con la subred **front-end** .

        azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

    Resultado:

        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK

    Parámetros:
    - **-e (o nombre--vnet)**. Nombre de la VNet donde se encuentra la subred. En el escenario, *TestVNet*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Crear la UDR para la subred back-end
Para crear la tabla de rutas y la ruta es necesario para la subred de back-end basada en el ejemplo anterior, siga los pasos siguientes.

1. Ejecutar la **`azure network route-table create`** comando para crear una tabla de ruta para la subred back-end.

        azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. Ejecutar la **`azure network route-table route create`** comando para crear una ruta en la tabla de ruta creada anteriormente para enviar todo el tráfico destinado a la subred de front-end (192.168.1.0/24) para **FW1** VM (192.168.0.4).

        azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. Ejecutar la **`azure network vnet subnet set`** comando para asociar la tabla de ruta creada anteriormente con la subred de **back-end** .

        azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## <a name="enable-ip-forwarding-on-fw1"></a>Habilitar el reenvío de IP en FW1
Para habilitar el reenvío en la NIC usada por **FW1**IP, siga los pasos siguientes.

1. Ejecutar la **`azure network nic show`** comando y observe el valor de **reenvío IP habilitar**. Se debe establecer en *false*.

        azure network nic show -g TestRG -n NICFW1

    Resultado:
        
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK

2. Ejecutar la **`azure network nic set`** comando para habilitar el reenvío de IP.

        azure network nic set -g TestRG -n NICFW1 -f true

    Resultado:

        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK

    Parámetros:

    - **-f (o--Habilitar reenvío de ip)**. *true* o *false*.
