<properties
   pageTitle="Crear un equilibrador de carga interno con PowerShell en el Administrador de recursos | Microsoft Azure"
   description="Aprenda a crear un equilibrador de carga interno con PowerShell en el Administrador de recursos"
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

# <a name="create-an-internal-load-balancer-using-powershell"></a>Crear un equilibrador de carga interno con PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementación clásico](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


Los siguientes pasos explican cómo crear un equilibrador de carga interno utilizando el Administrador de recursos de Azure con PowerShell. Con el Administrador de recursos de Azure, los elementos para crear un equilibrador de carga interno están configurados de forma individual y, a continuación, se combinan para crear un equilibrador de carga.

Debe crear y configurar los objetos siguientes para implementar un equilibrador de carga:

- Delantera configuración de IP de fondo: configurar la dirección IP privada para el tráfico de red entrante
- Grupo de direcciones de back-end - configurará las interfaces de red que recibirán el tráfico de equilibrio de carga procedentes de conjunto IP de front-end
- Reglas - origen y configuración de puerto local para el equilibrador de carga de equilibrio de carga.
- Sondeos - configura el sondeo de estado de mantenimiento de las instancias de máquina Virtual.
- Entrada reglas NAT - configura las reglas de puerto para tener acceso directo a una de las instancias de máquina Virtual.

Puede obtener más información acerca de carga de componentes de equilibrador con el Administrador de recursos de Azure en [Azure Administrador de recursos de soporte técnico para equilibrador de carga](load-balancer-arm.md).

Los siguientes pasos explican cómo configurar un equilibrador de carga entre dos máquinas virtuales.


## <a name="setup-powershell-to-use-resource-manager"></a>Configuración de PowerShell para usar el Administrador de recursos

Asegúrese de que tiene la última versión de producción del módulo Azure de PowerShell y configuración de PowerShell correctamente para tener acceso a su suscripción de Azure.

### <a name="step-1"></a>Paso 1

        Login-AzureRmAccount

### <a name="step-2"></a>Paso 2

Comprobar las suscripciones para la cuenta

        Get-AzureRmSubscription

Se le pedirá para autenticar con sus credenciales.<BR>

### <a name="step-3"></a>Paso 3

Elija cuál de las suscripciones de Azure usar. <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="create-resource-group-for-load-balancer"></a>Crear el grupo de recursos para equilibrador de carga

### <a name="step-4"></a>Paso 4

Crear un nuevo grupo de recursos (omitir este paso si usa un grupo existente de recursos)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Administrador de recursos de Azure requiere que todos los grupos de recursos especifican una ubicación. Se usa como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear un equilibrador de carga utilizará el mismo grupo de recursos.

En el ejemplo anterior que hemos creado un grupo de recursos denominado "NRP-c" y la ubicación "Oeste nos".

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Crear una red Virtual y una dirección IP privada para grupo IP de front-end


### <a name="step-1"></a>Paso 1

Crea una subred para la red virtual y la asigna a variable $backendSubnet

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Crear una red virtual:

    $vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Crea una red virtual y agrega la subred libras subred ser a la red virtual NRPVNet y la asigna a variable $vnet



## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>Crear grupo de IP de frente end y back-end dirección

Configurar un grupo IP de front-end para que entrantes carga equilibrador tráfico y back-end grupo direcciones de red recibir la carga equilibrada tráfico.

### <a name="step-1"></a>Paso 1

Crear un grupo IP de front-end con la dirección IP privada 10.0.2.5 para el 10.0.2.0/24 subred que será el extremo de tráfico de red entrante.

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### <a name="step-2"></a>paso 2

Configurar un grupo de direcciones de back-end utilizado para recibir el tráfico entrante de grupo de servidores front-end IP:

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>Crear reglas de Libras, las reglas NAT, sondeo y equilibrador de carga

Después de crear el grupo IP de front-end y back-end de direcciones, debe crear las reglas que pertenecen el recurso de equilibrador de carga:

### <a name="step-1"></a>Paso 1

    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

     $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


El ejemplo anterior crea los siguientes elementos:

- Regla de NAT que todo el tráfico entrante al puerto 3441 irán al puerto 3389.
- segunda regla NAT que todo el tráfico entrante al puerto 3442 irán al puerto 3389.
- una regla de equilibrador de carga que se cargará saldo tráfico entrante todo en público puerto 80 al puerto local 80 en el grupo de direcciones de back-end.
- una regla de sondeo que deberá comprobar el estado de salud de ruta de acceso "HealthProbe.aspx"



### <a name="step-2"></a>Paso 2

Crear el equilibrador de carga sumar todos los objetos (reglas NAT, reglas de equilibrador de carga, las configuraciones de sondeo):

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## <a name="create-network-interfaces"></a>Crear interfaces de red

Después de crear el equilibrador de carga interno, debe definir qué interfaces de red va a recibir el tráfico de red de equilibrio de carga, las reglas NAT y sondeo entrante. En este caso, la interfaz de red está configurada de forma individual y puede asignarse a una máquina virtual más adelante.


### <a name="step-1"></a>Paso 1


Obtener la red virtual de recursos y subred para crear interfaces de red:

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


Este paso crea una interfaz de red que pertenecen al grupo de back-end del equilibrador de carga y asociar la primera regla NAT para RDP para esta interfaz de red:

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### <a name="step-2"></a>Paso 2

Crear una segunda interfaz de red denominada NIC 2 de Libras pueden:

Este paso crea una segunda interfaz de red, asignar a la misma agrupación de back-end del equilibrador de carga y asociar la segunda regla NAT crean para RDP:

     $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

El resultado final mostrará lo siguiente:

    $backendnic1

Resultados esperados:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
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
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>Paso 3

Use el comando Agregar AzureRmVMNetworkInterface para asignar la NIC a una máquina virtual.

Puede encontrar las instrucciones paso a paso para crear una máquina virtual y asignar a una NIC siguiendo la documentación: [crear una máquina virtual de Azure utilizando PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

Si ya tiene una máquina virtual creada, puede agregar la interfaz de red con los siguientes pasos:

#### <a name="step-1"></a>Paso 1

Cargar el recurso de equilibrador de carga en una variable (si todavía no lo ha hecho todavía). La variable usada se denomina $lb y utilizar los mismos nombres desde el recurso del equilibrador de carga creado anteriormente.

    $lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### <a name="step-2"></a>Paso 2

Cargar la configuración de back-end a una variable.

    $backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### <a name="step-3"></a>Paso 3

Cargar la interfaz de red ya se ha creado en una variable. el nombre de variable utilizado es NIC de $. El nombre de la interfaz de red utilizado es el mismo del ejemplo anterior.

    $nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### <a name="step-4"></a>Paso 4

Cambiar la configuración de back-end de la interfaz de red.

    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### <a name="step-5"></a>Paso 5

Guardar el objeto de interfaz de red.

    Set-AzureRmNetworkInterface -NetworkInterface $nic

Después de agrega una interfaz de red al grupo de back-end del equilibrador de carga, se inicia recibe el tráfico de red en función de las reglas para ese recurso del equilibrador de carga de equilibrio de carga.

## <a name="update-an-existing-load-balancer"></a>Actualizar un equilibrador de carga existente


### <a name="step-1"></a>Paso 1

Con el equilibrador de carga del ejemplo anterior, asigne objeto del equilibrador de carga a variable $slb con Get-AzureRmLoadBalancer

    $slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### <a name="step-2"></a>Paso 2

En el ejemplo siguiente, agregará una regla NAT entrantes con los puertos 81 en el front-end y 8181 para el grupo de back-end a un equilibrador de carga existente

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### <a name="step-3"></a>Paso 3

Guardar la nueva configuración de uso conjunto AzureLoadBalancer

    $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Quitar un equilibrador de carga

Use el comando AzureRmLoadBalancer quitar para eliminar un equilibrador de carga creado previamente con el nombre "NRP-kg" en un grupo de recursos denominado "NRP-c"

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Puede usar la opcional cambiar - forzar para evitar la pregunta para eliminación.



## <a name="next-steps"></a>Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)