<properties
    pageTitle="Comandos de PowerShell de red comunes para máquinas virtuales | Microsoft Azure"
    description="Comandos de PowerShell comunes para ayudarle a crear una red virtual y los recursos asociados para máquinas virtuales."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="common-network-azure-powershell-commands-for-vms"></a>Comandos de PowerShell de Azure comunes de red para máquinas virtuales

Si desea crear una máquina virtual, debe crear una [red virtual](../virtual-network/virtual-networks-overview.md) o saber sobre una red virtual existente en el que se puede agregar la máquina virtual. Normalmente, cuando se crea una máquina virtual, también debe considerar la creación de los recursos descritos en este artículo.

Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener información sobre cómo instalar la última versión de PowerShell de Azure, seleccionar la suscripción e iniciar sesión con su cuenta.

## <a name="create-network-resources"></a>Crear recursos de red

Tarea | Comando 
-------------- | -------------------------
Crear configuraciones de subred | $subred1 = [Nuevo AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -subnet_name"nombre" AddressPrefix - XX. X.X.X/XX<BR>$subred2 = nuevo AzureRmVirtualNetworkSubnetConfig-subnet_name"nombre" AddressPrefix - XX. X.X.X/XX<BR><BR>Una red típica podría tener una subred de una [internet opuestas equilibrador de carga](../load-balancer/load-balancer-internet-overview.md) y una subred independiente para un [equilibrador de carga interno](../load-balancer/load-balancer-internal-overview.md). |
Crear una red virtual | $vnet = [AzureRmVirtualNetwork New](https://msdn.microsoft.com/library/mt603657.aspx) -nombre "virtual_network_name" - ResourceGroupName "resource_group_name"-ubicación "location_name" AddressPrefix - XX. X.X.X/XX-subred $subred1, subred2 $
Prueba de un nombre de dominio único | [Prueba AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) - DomainQualifiedName "nombre_dominio"-ubicación "location_name"<BR><BR>Puede especificar un nombre de dominio DNS de un [recurso IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md), que se crea una asignación para domainname.location.cloudapp.azure.com a la dirección IP pública de los servidores DNS administrados de Azure. El nombre puede contener sólo letras, números y guiones. El primer y último carácter debe ser una letra o número y el nombre de dominio deben ser únicos dentro de su ubicación de Azure. Si se devuelve **True** , el nombre de la propuesta es único globalmente.
Crear una dirección IP pública | $pip = [AzureRmPublicIpAddress New](https://msdn.microsoft.com/library/mt603620.aspx) -nombre "ip_address_name" - ResourceGroupName "resource_group_name" - DomainNameLabel "nombre_dominio"-ubicación "location_name" - AllocationMethod dinámicos<BR><BR>La dirección IP pública utiliza el nombre de dominio que anteriormente probado y se usa la configuración de front-end del equilibrador de carga.
Crear una configuración de IP de front-end | $frontendIP = [Nuevo AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -frontend_ip_name"nombre" - PublicIpAddress $pip<BR><BR>La configuración de cliente incluye la dirección IP pública que creó anteriormente para el tráfico de red entrante.
Crear un grupo de direcciones de back-end | $beAddressPool = [Nuevo AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -backend_pool_name"nombre"<BR><BR>Proporciona direcciones internas para el servidor del equilibrador de carga que se tiene acceso a través de una interfaz de red.
Crear un sondeo | $healthProbe = [Nuevo AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -probe_name"nombre" - RequestPath 'HealthProbe.aspx'-el protocolo http-puertos 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Contiene sondeos de estado que se usa para comprobar la disponibilidad de instancias de máquinas virtuales en el grupo de direcciones de back-end.
Crear una regla de equilibrio de carga | $lbRule = [AzureRmLoadBalancerRuleConfig New](https://msdn.microsoft.com/library/mt619391.aspx) -nombre HTTP - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-sondeo $healthProbe-protocolo Tcp - FrontendPort 80 - BackendPort 80<BR><BR>Contiene reglas que asignación un puerto público en el equilibrador de carga a un puerto en el grupo de direcciones de back-end.
Crear una regla NAT entrante | $inboundNATRule = [Nuevo AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -rule_name"nombre" - FrontendIpConfiguration $frontendIP-3441 protocolo TCP - FrontendPort - BackendPort 3389<BR><BR>Contiene reglas de la asignación de un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.
Crear un equilibrador de carga | $loadBalancer = "resource_group_name" [Nuevo AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) - ResourceGroupName-load_balancer_name"nombre"-ubicación "location_name" - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-sondeo $healthProbe
Crear una interfaz de red | $nic1 = "resource_group_name" [Nuevo AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) - ResourceGroupName-network_interface_name"nombre"-ubicación "location_name" PrivateIpAddress - XX. X.X.X-subred subred2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crear una interfaz de red con la dirección IP pública y subred de una red virtual que creó anteriormente.
    
## <a name="get-information-about-network-resources"></a>Obtener información acerca de los recursos de red

Tarea | Comando 
-------------- | -------------------------
Lista de redes virtuales | [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Enumera todas las redes virtuales en el grupo de recursos.
Obtener información acerca de una red virtual | AzureRmVirtualNetwork Get-nombre "virtual_network_name" - ResourceGroupName "resource_group_name"
Lista de subredes en una red virtual | AzureRmVirtualNetwork Get-nombre "virtual_network_name" - ResourceGroupName "resource_group_name" & #124; Seleccione subredes
Obtener información sobre una subred | [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -subnet_name"nombre" - VirtualNetwork $vnet<BR><BR>Obtiene información sobre la subred en la red virtual especificada. El valor $vnet representa el objeto devuelto por Get-AzureRmVirtualNetwork.
Enumerar las direcciones IP | [Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Muestra las direcciones IP públicas en el grupo de recursos.
Equilibradores de carga de la lista | [Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Enumera todos los equilibradores de carga en el grupo de recursos.
Interfaces de red de la lista | [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Enumera todas las interfaces de red en el grupo de recursos.
Obtener información sobre una interfaz de red | AzureRmNetworkInterface Get-nombre "network_interface_name" - ResourceGroupName "resource_group_name"<BR><BR>Obtiene información sobre una interfaz de red específica.
Obtener la configuración de IP de una interfaz de red | [Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -ipconfiguration_name"nombre" - NetworkInterface $nic<BR><BR>Obtiene información sobre la configuración de IP de la interfaz de red especificada. El valor $nic representa el objeto devuelto por Get-AzureRmNetworkInterface.

## <a name="manage-network-resources"></a>Administrar recursos de red

Tarea | Comando 
-------------- | -------------------------
Agregar una subred a una red virtual | [Agregar-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) - AddressPrefix XX. X.X.X/XX-subnet_name"nombre" - VirtualNetwork $vnet<BR><BR>Agrega una subred a una red virtual existente. El valor $vnet representa el objeto devuelto por Get-AzureRmVirtualNetwork.
Eliminar una red virtual | [Quitar AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -nombre "virtual_network_name" - ResourceGroupName "resource_group_name"<BR><BR>Quita la red virtual especificada del grupo de recursos.
Eliminar una interfaz de red | [Quitar AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -nombre "network_interface_name" - ResourceGroupName "resource_group_name"<BR><BR>Quita la interfaz de red especificada del grupo de recursos.
Eliminar un equilibrador de carga | [Quitar AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -nombre "load_balancer_name" - ResourceGroupName "resource_group_name"<BR><BR>Quita el equilibrador de carga especificada del grupo de recursos.
Eliminar una dirección IP pública | [Quitar AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-nombre "ip_address_name" - ResourceGroupName "resource_group_name"<BR><BR>Quita la dirección IP pública del grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes

- Usar la interfaz de red que acaba de crear al [crear una máquina virtual](virtual-machines-windows-ps-create.md).
- Obtenga información sobre cómo puede [crear una máquina virtual con varias interfaces de red](../virtual-network/virtual-networks-multiple-nics.md).
