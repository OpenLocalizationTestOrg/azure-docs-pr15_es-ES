<properties
   pageTitle="Crear VNet interconexión usar cmdlets de Powershell | Microsoft Azure"
   description="Aprenda a crear una red virtual con el portal de Azure en el Administrador de recursos."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai; annahar"/>

# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Crear VNet interconexión usar cmdlets de Powershell

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para crear un VNet interconexión con PowerShell, siga los pasos siguientes:

1. Si nunca ha usado Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccione la suscripción.

> [AZURE.NOTE] Cmdlet de PowerShell para la administración VNet interconexión se incluye con [Azure PowerShell 1,6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Leer los objetos de una red virtual:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

3. Para establecer VNet interconexión, debe crear dos vínculos, uno para cada dirección. El siguiente paso crear primero un vínculo de interconexión VNet para VNet1 a VNet2:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id

    Muestra el resultado:

        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Initiated
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

4. Este paso creará un vínculo de interconexión VNet para VNet2 a VNet1:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id

    Muestra el resultado:

        Name            : LinkToVNet1
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet2
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

5. Una vez creado el vínculo de interconexión VNet, puede ver el estado de los vínculos siguiente:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

    Muestra el resultado:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Hay algunas propiedades configurables de interconexión VNet:

  	|Opción|Descripción|Predeterminado|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Si el espacio del punto VNet se incluye como parte de la etiqueta Virtual_network de direcciones|Sí|
  	|AllowForwardedTraffic|Si se aceptan o se descarta el tráfico no origina una VNet peered|No|
  	|AllowGatewayTransit|Permite que el interlocutor VNet utilizar la puerta de enlace VNet|No|
  	|UseRemoteGateways|Use la puerta de enlace de su punto VNet. El punto VNet debe tener una puerta de enlace configurado y AllowGatewayTransit seleccionado. No puede usar esta opción si ha configurado una puerta de enlace|No|

    Cada vínculo de interconexión VNet tiene el conjunto de propiedades anteriores. Por ejemplo, puede establecer AllowVirtualNetworkAccess en True para el vínculo de interconexión VNet VNet1 a VNet2 y establecer en False para el vínculo de interconexión VNet en la otra dirección.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

    Puede ejecutar Get-AzureRmVirtualNetworkPeering doble comprobar el valor de propiedad después del cambio. Desde el resultado, puede ver que allowforwardedtraffic cambia conjunto verdadero después de ejecutar los cmdlets anteriores.

        Name            : LinkToVNet2
        Id          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic       : True
        AllowGatewayTransit     : False
        UseRemoteGateways       : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

    Una vez establecida la interconexión en este escenario, debería poder iniciar las conexiones de cualquier máquina virtual a cualquier máquina virtual de dos VNets. De forma predeterminada, AllowVirtualNetworkAccess es verdadero y VNet interconexión se aprovisionar las ACL adecuadas para permitir la comunicación entre VNets. Puede aplicar reglas de grupo (GSN) de seguridad de red para bloquear la conectividad entre subredes específicas o máquinas virtuales para tener un control preciso de acceso entre dos redes virtuales.  Para obtener más información sobre cómo crear reglas del GSN, consulte este [artículo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para crear VNet interconexión entre suscripciones con PowerShell, siga los pasos siguientes:

1. Inicie sesión en Azure con privilegios A usuario de cuenta para una suscripción y ejecute el siguiente cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5

    Esto no es necesario, se puede establecer interconexión incluso si los usuarios individualmente elevar interconexión solicitudes para sus respectivos VNets como que coincida con las solicitudes. Agregar un usuario con privilegios de lo otro VNet como un usuario en el VNet local facilita la instalación.

2. Inicie sesión en Azure con la cuenta con privilegios-del usuario B para suscripción B y ejecute el siguiente cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3

3. En el usuario A está inicio de sesión, ejecute el cmdlet siguiente:

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. En la sesión de inicio de sesión del usuario-B, ejecute el cmdlet siguiente:

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. Una vez establecida la interconexión, cualquier máquina virtual en VNet3 debería poder comunicarse con cualquier máquina virtual VNet5.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. En este escenario, puede ejecutar los cmdlets de PowerShell para establecer la interconexión VNet.  Debe establecer la propiedad AllowForwardedTraffic en True y vincular VNET1 HubVNet, que permite el tráfico entrante desde fuera del espacio de dirección VNet interconexión.

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id

2. Después de establece la interconexión, puede hacer referencia a este [artículo](virtual-network-create-udr-arm-ps.md) y definir una ruta definida por el usuario (UDR) para redirigir el tráfico de VNet1 a través de un equipo virtual para utilizar su capacidad. Especifique la dirección de salto siguiente en la ruta, puede configurarlo para la dirección IP del dispositivo virtual en el punto VNet HubVNet. A continuación, encontrará una muestra:

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para crear un VNet interconexión entre una red virtual clásica y una red virtual del Administrador de recursos de Azure en PowerShell, siga los pasos siguientes:

1. Leer el objeto de una red virtual para **VNET1**, el Administrador de recursos de Azure una red virtual como sigue:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

2. Para establecer VNet interconexión en este escenario, es necesario un único vínculo, específicamente un vínculo de **VNET1** a **VNET2**. Este paso requiere conocer el identificador de recurso. de su VNet clásico El formato de Id. de grupo de recursos tiene el siguiente aspecto:

        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Asegúrese de reemplazar SubscriptionID, ResourceGroupName y VirtualNetworkName con los nombres apropiados.

    Esto se puede conseguir haciendo lo siguiente:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2

3. Una vez VNet se crea el vínculo de interconexión, puede ver el estado de los vínculos tal como se muestra en el siguiente resultado:

        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Quitar VNet interconexión

1.  Para quitar el VNet interconexión, debe ejecutar el siguiente cmdlet:

        Remove-AzureRmVirtualNetworkPeering  

        Remove both links, using the following commands:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2

2. Una vez quitar un vínculo en un VNET interconexión, el estado de los vínculos del mismo nivel se vaya a desconectado. En este estado, volver a no puede crear el vínculo hasta que el estado del vínculo punto cambia a iniciado. Se recomienda que quitar ambos vínculos antes de volver a crear la VNet interconexión.
