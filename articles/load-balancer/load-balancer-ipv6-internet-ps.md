<properties
    pageTitle="Crear un opuestas equilibrador de carga con IPv6 con PowerShell para el Administrador de recursos de Internet | Microsoft Azure"
    description="Aprenda a crear un opuestas equilibrador de carga con IPv6 con PowerShell para el Administrador de recursos de Internet"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="IPv6, equilibrador de carga de azure, pila dual, ip pública, ipv6 nativa, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Empezar a crear un opuestas equilibrador de carga con IPv6 con PowerShell para el Administrador de recursos de Internet

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [CLI de Azure](./load-balancer-ipv6-internet-cli.md)
- [Plantilla](./load-balancer-ipv6-internet-template.md)

Un equilibrador de carga de Azure es un equilibrador de carga de nivel 4 (TCP, UDP). El equilibrador de carga proporciona alta disponibilidad al distribuir el tráfico entrante entre instancias del servicio correcto en servicios de nube o máquinas virtuales en un conjunto de equilibrador de carga. Azure equilibrador de carga también puede presentar estos servicios en varios puertos, varias direcciones IP o ambos.

## <a name="example-deployment-scenario"></a>Escenario de implementación de ejemplo

El diagrama siguiente muestra la solución implementada en este artículo de equilibrio de carga.

![Escenario de equilibrador de carga](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

En este escenario creará los siguientes recursos de Azure:

- un equilibrador de carga a través de Internet con un IPv4 y la dirección IP pública de IPv6
- dos cargar Equilibrio reglas para asignar al públicas VIP a los extremos privados
- un conjunto de disponibilidad a la contiene las dos VM
- dos máquinas virtuales (VM)
- una interfaz de red virtual para cada VM con direcciones IPv4 y IPv6 asignadas

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Implementar la solución con PowerShell de Azure

Los pasos siguientes muestran cómo crear un opuestas equilibrador de carga con el Administrador de recursos de Azure con PowerShell de Internet. Con el Administrador de recursos de Azure, cada recurso se crea y configura de forma individual, luego superponer entre sí para crear un recurso.

Para implementar un equilibrador de carga, crear y configurar los siguientes objetos:

- Configuración de IP front-end - contiene las direcciones IP públicas para el tráfico de red entrante.
- Grupo de direcciones de back-end - contiene interfaces de red (NIC) para que los equipos virtuales recibir el tráfico de red de equilibrador de carga.
- Reglas de equilibrio de carga - contiene reglas de asignar un puerto público en el equilibrador de carga al puerto en el grupo de direcciones de back-end.
- NAT reglas de entrada: contiene reglas de la asignación de un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.
- Sondeos: contiene sondeos de estado que usa para comprobar la disponibilidad de instancias de máquinas virtuales en el grupo de direcciones de back-end.

Para obtener más información, consulte [Administrador de Azure recursos de soporte técnico para equilibrador de carga](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar PowerShell para usar el Administrador de recursos

Asegúrese de que tiene la última versión de producción del Administrador de recursos de Azure módulo de PowerShell.

1. Inicie sesión en Azure

        Login-AzureRmAccount

    Escriba sus credenciales cuando se le solicite.

2. Comprobar las suscripciones para la cuenta

        Get-AzureRmSubscription

3. Elija cuál de las suscripciones de Azure usar.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Crear un grupo de recursos (omitir este paso si usa un grupo existente de recursos)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Crear una red virtual y una dirección IP pública para el grupo IP front-end

1. Crear una red virtual con una subred.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Crear dirección IP pública de Azure recursos (puntos) para el grupo de direcciones IP front-end.

        $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
        $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6

    >[AZURE.IMPORTANT] El equilibrador de carga, utiliza la etiqueta de dominio de la dirección IP pública como prefijo para su FQDN. En este ejemplo, el FQDN son *lbnrpipv4.westus.cloudapp.azure.com* y *lbnrpipv6.westus.cloudapp.azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Crear las configuraciones de IP de front-end y un conjunto de direcciones de Back-End

1. Crear la configuración de dirección front-end que usa la dirección IP pública que creó.

        $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
        $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6

2. Crear grupos de direcciones de back-end.

        $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
        $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"


## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Crear reglas Libras, reglas NAT, un sondeo y un equilibrador de carga

Este ejemplo crea los siguientes elementos:

- una regla NAT para traducir todo el tráfico entrante en el puerto 443 al puerto 4443
- una regla de equilibrador de carga para equilibrar todo el tráfico entrante en el puerto 80 al puerto 80 en las direcciones en el grupo de back-end.
- una regla de equilibrador de carga para permitir conexión RDP a las VM en el puerto 3389.
- una regla de sondeo para comprobar el estado de mantenimiento en una página con el nombre *HealthProbe.aspx* o un servicio en el puerto 8080
- un equilibrador de carga que usa todos estos objetos

1. Crear las reglas NAT.

        $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
        $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443

2. Crear un sondeo de estado. Hay dos formas de configurar un sondeo:

    Sondeo HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    o sondeo TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
        $RDPprobe = New-AzureRmLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2


    En este ejemplo, vamos a usar los sondeos TCP.

3. Crear una regla de equilibrador de carga.

        $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $RDPrule = New-AzureRmLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389

4. Crear el equilibrador de carga con los objetos creados previamente.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule

## <a name="create-nics-for-the-back-end-vms"></a>Crear NIC para las máquinas virtuales de back-end

1. Obtenga la red Virtual y Virtual subred, donde es necesario crear las NIC.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Crear configuraciones IP y NIC para las máquinas virtuales.

        $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
        $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
        $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

        $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
        $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
        $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Crear equipos virtuales y asigne las NIC recién creadas

Para obtener más información sobre la creación de una máquina virtual, consulte [crear y configurar previamente una máquina Virtual de Windows con el Administrador de recursos y Azure PowerShell](..\virtual-machines\virtual-machines-windows-ps-create.md)

1. Crear una cuenta de almacenamiento y configurar la disponibilidad

        New-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
        $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
        New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName $LRS
        $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'

2. Crear cada VM y asignar la anterior creado NIC

        $mySecureCredentials= Get-Credential -Message “Type the username and password of the local administrator account.”

        $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
        $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
        $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

        $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
        $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
        $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2

## <a name="next-steps"></a>Pasos siguientes

[Empezar a configurar un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
