<properties 
   pageTitle="Controlar el enrutamiento y usar en el Administrador de recursos de equipos virtuales con PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo controlar el enrutamiento y usar en el Administrador de recursos de equipos virtuales con PowerShell"
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
   ms.date="02/23/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-resource-manager-by-using-powershell"></a>Crear rutas definidas por el usuario (UDR) en el Administrador de recursos con PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación de administrador de recursos. También puede [crear UDRs en el modelo de implementación clásico](virtual-network-create-udr-classic-ps.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

El ejemplo PowerShell comandos que esperan un entorno simple ya ha creado se basa en el ejemplo anterior. Si desea ejecutar los comandos que se muestran en este documento, primero implementar [esta plantilla](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)para crear el entorno de prueba, haga clic en **implementar a Azure**, reemplace los valores de parámetro de forma predeterminada, si es necesario y siga las instrucciones en el portal.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Crear la UDR para la subred front-end
Para crear la tabla de rutas y la ruta es necesario para la subred front-end basada en el ejemplo anterior, siga los pasos siguientes.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Crear una ruta que se utiliza para enviar todo el tráfico destinado a la subred de back-end (192.168.2.0/24) para enrutar al dispositivo virtual **FW1** (192.168.0.4).

        $route = New-AzureRmRouteConfig -Name RouteToBackEnd `
            -AddressPrefix 192.168.2.0/24 -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

4. Crear una tabla de ruta denominada **Front-end UDR** en la región de **westus** que contiene la ruta que creó anteriormente.

        $routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
            -Name UDR-FrontEnd -Route $route

5. Crear una variable que contiene el VNet ¿dónde está la subred. En nuestro ejemplo, el VNet se denomina **TestVNet**.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet

6. Asociar la tabla de ruta creada anteriormente a la subred **front-end** .
        
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            -AddressPrefix 192.168.1.0/24 -RouteTable $routeTable

>[AZURE.WARNING] El resultado del comando anterior muestra el contenido para el objeto de configuración de red virtual, que solo existe en el equipo donde está ejecutando PowerShell. Debe ejecutar el cmdlet **Set-AzureVirtualNetwork** para guardar la configuración en Azure.

7. Guarde la nueva configuración de subred en Azure.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Resultados esperados:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : westus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              : 
                            Name         Value
                            ===========  =====
                            displayName  VNet 
                            
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                                ...,
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConfigurations/ipconfig1"
                                  },
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                                  }
                                ],
                                "NetworkSecurityGroup": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                },
                                "RouteTable": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                                },
                                "ProvisioningState": "Succeeded"
                              },
                                ...
                            ]   

## <a name="create-the-udr-for-the-back-end-subnet"></a>Crear la UDR para la subred back-end
Para crear la tabla de rutas y la ruta es necesario para la subred de back-end basada en el ejemplo anterior, siga los pasos siguientes.

1. Crear una ruta que se utiliza para enviar todo el tráfico destinado a la subred de front-end (192.168.1.0/24) para enrutar al dispositivo virtual **FW1** (192.168.0.4).

        $route = New-AzureRmRouteConfig -Name RouteToFrontEnd `
            -AddressPrefix 192.168.1.0/24 -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

4. Crear una tabla de ruta denominada **Back-end UDR** en la región de **uswest** que contiene la ruta que creó anteriormente.

        $routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
            -Name UDR-BackEnd -Route $route

5. Asociar la tabla de ruta creada anteriormente a la subred **back-end** .

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
            -AddressPrefix 192.168.2.0/24 -RouteTable $routeTable

7. Guarde la nueva configuración de subred en Azure.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Resultados esperados:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : westus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              : 
                            Name         Value
                            ===========  =====
                            displayName  VNet 
                            
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              ...,
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2/ipConfigurations/ipconfig1"
                                  },
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1/ipConfigurations/ipconfig1"
                                  }
                                ],
                                "NetworkSecurityGroup": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BacEnd"
                                },
                                "RouteTable": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd"
                                },
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="enable-ip-forwarding-on-fw1"></a>Habilitar el reenvío de IP en FW1
Para habilitar el reenvío en la NIC usada por **FW1**IP, siga los pasos siguientes.

1. Crear una variable que contiene la configuración de la NIC usado por FW1. En este escenario, la NIC se denomina **NICFW1**.

        $nicfw1 = Get-AzureRmNetworkInterface -ResourceGroupName TestRG -Name NICFW1

2. Habilitar el reenvío de IP y guardar la configuración de NIC.

        $nicfw1.EnableIPForwarding = 1
        Set-AzureRmNetworkInterface -NetworkInterface $nicfw1

    Resultados esperados:

        Name                 : NICFW1
        ResourceGroupName    : TestRG
        Location             : westus
        Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
        Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState    : Succeeded
        Tags                 : 
                               Name         Value                  
                               ===========  =======================
                               displayName  NetworkInterfaces - DMZ
                               
        VirtualMachine       : {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1"
                               }
        IpConfigurations     : [
                                 {
                                   "Name": "ipconfig1",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1/ipConfigurations/ipconfig1",
                                   "PrivateIpAddress": "192.168.0.4",
                                   "PrivateIpAllocationMethod": "Static",
                                   "Subnet": {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/DMZ"
                                   },
                                   "PublicIpAddress": {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1"
                                   },
                                   "LoadBalancerBackendAddressPools": [],
                                   "LoadBalancerInboundNatRules": [],
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
        DnsSettings          : {
                                 "DnsServers": [],
                                 "AppliedDnsServers": [],
                                 "InternalDnsNameLabel": null,
                                 "InternalFqdn": null
                               }
        EnableIPForwarding   : True
        NetworkSecurityGroup : null
        Primary              : True

