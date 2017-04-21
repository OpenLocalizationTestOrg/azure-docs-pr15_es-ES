<properties 
   pageTitle="Controlar el enrutamiento y usar equipos virtuales con PowerShell en el modelo de implementación clásica | Microsoft Azure"
   description="Obtenga información sobre cómo controlar el enrutamiento en VNets con PowerShell en el modelo de implementación clásica"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Controlar el enrutamiento y usar dispositivos virtuales (clásico) con PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación clásico.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

El ejemplo Azure PowerShell comandos que esperan un entorno simple ya ha creado se basa en el escenario anterior. Si desea ejecutar los comandos que se muestran en este documento, cree el entorno que se muestra en [crear un VNet con PowerShell (clásico)](virtual-networks-create-vnet-classic-netcfg-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Crear la UDR para la subred front-end
Para crear la tabla de rutas y la ruta es necesario para la subred front-end basada en el ejemplo anterior, siga los pasos siguientes.

3. Ejecutar la **`New-AzureRouteTable`** cmdlet para crear una tabla de ruta para la subred front-end.

        New-AzureRouteTable -Name UDR-FrontEnd `
            -Location uswest `
            -Label "Route table for front end subnet"

    Resultado:

        Name         Location   Label                          
        ----         --------   -----                          
        UDR-FrontEnd West US    Route table for front end subnet

4. Ejecutar la **`Set-AzureRoute`** cmdlet para crear una ruta en la tabla de ruta creado anteriormente para enviar todo el tráfico destinado a la subred de back-end (192.168.2.0/24) para **FW1** VM (192.168.0.4).
    
        Get-AzureRouteTable UDR-FrontEnd `
            |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

    Resultado:

        Name     : UDR-FrontEnd
        Location : West US
        Label    : Route table for frontend subnet
        Routes   : 
                   Name                 Address Prefix    Next hop type        Next hop IP address
                   ----                 --------------    -------------        -------------------
                   RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Ejecutar la **`Set-AzureSubnetRouteTable`** cmdlet para asociar la tabla de ruta creado anteriormente con la subred **front-end** .

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName FrontEnd `
            -RouteTableName UDR-FrontEnd
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Crear la UDR para la subred back-end
Para crear la tabla de rutas y la ruta es necesario para la subred de back-end basada en el ejemplo anterior, siga los pasos siguientes.

3. Ejecutar la **`New-AzureRouteTable`** cmdlet para crear una tabla de ruta para la subred back-end.

        New-AzureRouteTable -Name UDR-BackEnd `
            -Location uswest `
            -Label "Route table for back end subnet"

4. Ejecutar la **`Set-AzureRoute`** cmdlet para crear una ruta en la tabla de ruta creado anteriormente para enviar todo el tráfico destinado a la subred de front-end (192.168.1.0/24) para **FW1** VM (192.168.0.4).

        Get-AzureRouteTable UDR-BackEnd `
            |Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

5. Ejecutar la **`Set-AzureSubnetRouteTable`** cmdlet para asociar la tabla de ruta creado anteriormente con la subred de **back-end** .

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName BackEnd `
            -RouteTableName UDR-BackEnd

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Habilitar el reenvío de IP en la máquina virtual de FW1
Para habilitar el reenvío de la máquina virtual de FW1 IP, siga los pasos siguientes.

1. Ejecutar la **`Get-AzureIPForwarding`** cmdlet para comprobar el estado de reenvío IP

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Get-AzureIPForwarding

    Resultado:

        Disabled

2. Ejecutar la **`Set-AzureIPForwarding`** comando para habilitar el reenvío de IP de la máquina virtual *FW1* .

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Set-AzureIPForwarding -Enable
