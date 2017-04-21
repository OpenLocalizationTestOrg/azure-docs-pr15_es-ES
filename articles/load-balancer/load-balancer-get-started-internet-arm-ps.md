<properties
   pageTitle="Crear un equilibrador de carga a través de Internet en el Administrador de recursos con PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo crear un equilibrador de carga a través de Internet en el Administrador de recursos con PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
   ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="get-started"></a>Creación de un equilibrador de carga a través de Internet en el Administrador de recursos con PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación de administrador de recursos. También puede [obtener información sobre cómo crear un equilibrador de carga a través de Internet mediante el modelo de implementación clásico](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Implementar la solución con PowerShell de Azure

Los siguientes procedimientos explican cómo crear un equilibrador de carga a través de Internet mediante el Administrador de recursos de Azure con PowerShell. Con el Administrador de recursos de Azure, cada recurso es crear y configurar de forma individual y después colocar juntos para crear un equilibrador de carga.

Debe crear y configurar los siguientes objetos para implementar un equilibrador de carga:

- Configuración de IP front-end: contiene direcciones IP (puntos) públicas para el tráfico de red entrante.
- Grupo de direcciones de back-end: contiene interfaces de red (NIC) para que los equipos virtuales recibir el tráfico de red de equilibrador de carga.
- Reglas de equilibrio de carga: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto en el grupo de direcciones de back-end.
- NAT reglas de entrada: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.
- Sondeos: contiene sondeos de estado que usa para comprobar la disponibilidad de instancias de máquina virtual en el grupo de direcciones de back-end.

Para obtener más información, consulte [Administrador de Azure recursos de soporte técnico para equilibrador de carga](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar PowerShell para usar el Administrador de recursos

Asegúrese de que tiene la última versión de producción del Administrador de recursos de Azure módulo de PowerShell:

1. Inicie sesión en Azure.

        Login-AzureRmAccount

    Escriba sus credenciales cuando se le solicite.

2. Compruebe las suscripciones para la cuenta.

        Get-AzureRmSubscription

3. Elija cuál de las suscripciones de Azure usar.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Crear un grupo de recursos. (Omitir este paso si está utilizando un grupo de recursos existente).

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Crear una red virtual y una dirección IP pública para el grupo IP front-end

1. Crear una subred y una red virtual.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Crear un Azure público recurso Dirección IP, denominado **PublicIP**para usarlo con un grupo IP front-end con el nombre DNS **loadbalancernrp.westus.cloudapp.azure.com**. El comando siguiente utiliza el tipo de asignación estática.

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

    >[AZURE.IMPORTANT]El equilibrador de carga utiliza la etiqueta de dominio de la dirección IP pública como prefijo para su nombre completo. Esto es diferente del modelo de implementación clásico, que usa el servicio de nube como el FQDN del equilibrador de carga.
    >En este ejemplo, el FQDN es **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Crear un grupo de IP front-end y una dirección de back-end

1. Crear un grupo IP front-end denominado **Libras cliente** que utiliza el recurso **PublicIp** .

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP

2. Crear un grupo de dirección de back-end denominado **kg-back-end**.

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Crear reglas NAT, una regla de equilibrador de carga, un sondeo y un equilibrador de carga

Este ejemplo crea los siguientes elementos:

- Una regla NAT para traducir todo el tráfico entrante en el puerto 3441 al puerto 3389
- Una regla NAT para traducir todo el tráfico entrante en el puerto 3442 al puerto 3389
- Una regla de sondeo para comprobar el estado de mantenimiento en una página denominada **HealthProbe.aspx**
- Una regla de equilibrador de carga para equilibrar todo el tráfico entrante en el puerto 80 al puerto 80 en las direcciones en el grupo de back-end
- Un equilibrador de carga que usa todos estos objetos

Siga estos pasos:

1. Crear las reglas NAT.

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

2. Crear un sondeo de estado. Hay dos formas de configurar un sondeo:

    Sondeo HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    Sondeo TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

3. Crear una regla de equilibrador de carga.

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

4. Crear el equilibrador de carga con los objetos creados previamente.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## <a name="create-nics"></a>Crear NIC

Crear interfaces de red (o modificar los existentes) y, a continuación, asociarlos reglas NAT, reglas de equilibrador de carga y sondeos:

1. Obtener la red virtual y una subred de una red virtual, donde es necesario crear las NIC.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Crear una NIC denominada **nic1 de libras pueden**y asociarla con la primera regla NAT y el grupo de direcciones de back-end de primer (y único).

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

3. Crear una NIC denominada **NIC 2 de libras pueden**y asociarla con la segunda regla NAT y el grupo de direcciones de back-end de primer (y único).

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

4. Active las NIC.

        $backendnic1

    Resultados esperados:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Usar el `Add-AzureRmVMNetworkInterface` cmdlet para asignar las NIC a máquinas virtuales diferentes.

## <a name="create-a-virtual-machine"></a>Crear una máquina virtual

Para obtener instrucciones sobre cómo crear una máquina virtual y asignar una NIC, consulte [crear una máquina virtual de Azure utilizando PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Agregar la interfaz de red al equilibrador de carga

1. Recuperar el equilibrador de carga de Azure.

    Cargar el recurso de equilibrador de carga en una variable (si todavía no lo ha hecho todavía). La variable se denomina **$lb**. Use los mismos nombres de los recursos de equilibrador de carga que creó anteriormente.

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

2. Cargar la configuración de fondo a una variable.

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

3. Cargar la interfaz de red ya se ha creado en una variable. El nombre de variable es **$nic**. El nombre de la interfaz de red es el mismo del ejemplo anterior.

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

4. Cambiar la configuración de back-end de la interfaz de red.

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

5. Guardar el objeto de interfaz de red.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Después de agrega una interfaz de red al grupo de back-end del equilibrador de carga, se inicia recibir tráfico de red según las reglas de equilibrio de carga para ese recurso del equilibrador de carga.

## <a name="update-an-existing-load-balancer"></a>Actualizar un equilibrador de carga existente

1. Usando el equilibrador de carga del ejemplo anterior, asigne un objeto de equilibrador de carga a la variable **$slb** usando `Get-AzureLoadBalancer`.

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

2. En el ejemplo siguiente, agregar una regla de entrada NAT--usando los puertos 81 en el grupo de servidores front-end y 8181 para el grupo de back-end--un equilibrador de carga existente.

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP

3. Guarde la nueva configuración mediante `Set-AzureLoadBalancer`.

        $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Quitar un equilibrador de carga

Use el comando `Remove-AzureLoadBalancer` para eliminar un equilibrador de carga creado previamente denominado **NRP Libras** en un grupo de recursos denominado **NRP-c**.

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Puede usar el modificador opcional **-Forzar** para evitar la pregunta para eliminación.

## <a name="next-steps"></a>Pasos siguientes

[Empezar a configurar un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
