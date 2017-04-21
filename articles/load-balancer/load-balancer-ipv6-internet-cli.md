<properties
    pageTitle="Crear un Internet opuestas equilibrador de carga con IPv6 en Azure Administrador de recursos con la CLI de Azure | Microsoft Azure"
    description="Aprenda a crear un Internet opuestas equilibrador de carga con IPv6 en Azure Administrador de recursos con la CLI de Azure"
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

# <a name="create-an-internet-facing-load-balancer-with-ipv6-in-azure-resource-manager-using-the-azure-cli"></a>Crear un Internet opuestas equilibrador de carga con IPv6 en Azure Administrador de recursos con la CLI de Azure

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [CLI de Azure](./load-balancer-ipv6-internet-cli.md)
- [Plantilla](./load-balancer-ipv6-internet-template.md)

Un equilibrador de carga de Azure es un equilibrador de carga de nivel 4 (TCP, UDP). El equilibrador de carga proporciona alta disponibilidad al distribuir el tráfico entrante entre instancias del servicio correcto en servicios de nube o máquinas virtuales en un conjunto de equilibrador de carga. Azure equilibrador de carga también puede presentar estos servicios en varios puertos, varias direcciones IP o ambos.

## <a name="example-deployment-scenario"></a>Escenario de implementación de ejemplo

El diagrama siguiente muestra la solución de equilibrio de carga se implementa con la plantilla de ejemplo descrita en este artículo.

![Escenario de equilibrador de carga](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

En este escenario creará los siguientes recursos de Azure:

- dos máquinas virtuales (VM)
- una interfaz de red virtual para cada VM con direcciones IPv4 y IPv6 asignadas
- un equilibrador de carga a través de Internet con un IPv4 y la dirección IP pública de IPv6
- un conjunto de disponibilidad a la contiene las dos VM
- dos cargar Equilibrio reglas para asignar al públicas VIP a los extremos privados

## <a name="deploying-the-solution-using-the-azure-cli"></a>Implementar la solución mediante la CLI de Azure

Los pasos siguientes muestran cómo crear un opuestas equilibrador de carga con el Administrador de recursos de Azure CLI de Internet. Con el Administrador de recursos de Azure, cada recurso es crear y configurar de forma individual y después colocar juntos para crear un recurso.

Para implementar un equilibrador de carga, crear y configurar los siguientes objetos:

- Configuración de IP front-end - contiene las direcciones IP públicas para el tráfico de red entrante.
- Grupo de direcciones de back-end - contiene interfaces de red (NIC) para que los equipos virtuales recibir el tráfico de red de equilibrador de carga.
- Reglas de equilibrio de carga - contiene reglas de asignar un puerto público en el equilibrador de carga al puerto en el grupo de direcciones de back-end.
- NAT reglas de entrada: contiene reglas de la asignación de un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.
- Sondeos: contiene sondeos de estado que usa para comprobar la disponibilidad de instancias de máquinas virtuales en el grupo de direcciones de back-end.

Para obtener más información, consulte [Administrador de Azure recursos de soporte técnico para equilibrador de carga](load-balancer-arm.md).

## <a name="set-up-your-cli-environment-to-use-azure-resource-manager"></a>Configurar su entorno de CLI para usar el Administrador de recursos de Azure

En este ejemplo, se está ejecutando las herramientas CLI en una ventana de comandos de PowerShell. No utilizamos los cmdlets de PowerShell de Azure pero usamos capacidades de secuencias de comandos de PowerShell para mejorar la legibilidad y volver a usarlo.

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../../articles/xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.

2. Ejecute el comando de **modo de azure config** para cambiar al modo de administrador de recursos.

        azure config mode arm

    Resultados esperados:

        info:    New mode is arm

3. Inicie sesión en Azure y obtener una lista de suscripciones.

        azure login

    Escriba sus credenciales de Azure cuando se le solicite.

        azure account list

    Seleccione la suscripción que desea usar. Anote el identificador de la suscripción para el siguiente paso.

4. Configurar las variables de PowerShell para su uso con los comandos de CLI.

        ```
        $subscriptionid = "########-####-####-####-############"  # enter subscription id
        $location = "southcentralus"
        $rgName = "pscontosorg1southctrlus09152016"
        $vnetName = "contosoIPv4Vnet"
        $vnetPrefix = "10.0.0.0/16"
        $subnet1Name = "clicontosoIPv4Subnet1"
        $subnet1Prefix = "10.0.0.0/24"
        $subnet2Name = "clicontosoIPv4Subnet2"
        $subnet2Prefix = "10.0.1.0/24"
        $dnsLabel = "contoso09152016"
        $lbName = "myIPv4IPv6Lb"
        ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Crear un grupo de recursos, un equilibrador de carga, una red virtual y subredes

1. Crear un grupo de recursos

        azure group create $rgName $location

2. Crear un equilibrador de carga

        $lb = azure network lb create --resource-group $rgname --location $location --name $lbName

3. Crear una red virtual (VNet).

        $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix

    Cree dos subredes en este VNet.

        $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
        $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Crear las direcciones IP públicas para el grupo de servidores front-end

1. Configurar las variables de PowerShell

        $publicIpv4Name = "myIPv4Vip"
        $publicIpv6Name = "myIPv6Vip"

2. Crear una dirección IP pública del grupo IP front-end.

        $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
        $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel

    >[AZURE.IMPORTANT]El equilibrador de carga, utiliza la etiqueta de dominio de la dirección IP pública como su FQDN. Este nombre un cambio de implementación clásico, que usa el servicio de nube como el FQDN del equilibrador de carga.
    >En este ejemplo, el FQDN es *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Crear grupos de front-end y back-end

Este ejemplo crea el grupo IP front-end que recibe el tráfico de red entrante en el equilibrador de carga y el grupo IP de back-end donde envía el tráfico de red de equilibrio de carga en el grupo de servidores front-end.

1. Configurar las variables de PowerShell

        $frontendV4Name = "FrontendVipIPv4"
        $frontendV6Name = "FrontendVipIPv6"
        $backendAddressPoolV4Name = "BackendPoolIPv4"
        $backendAddressPoolV6Name = "BackendPoolIPv6"

2. Crear un grupo IP front-end asociar la dirección IP pública creada en el paso anterior y el equilibrador de carga.

        $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
        $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
        $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
        $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName

## <a name="create-the-probe-nat-rules-and-lb-rules"></a>Crear el sondeo, las reglas NAT y Libras reglas

Este ejemplo crea los siguientes elementos:

- una regla de sondeo para comprobar la conectividad con el puerto TCP 80
- una regla NAT para traducir todo el tráfico entrante en el puerto 3389 al puerto 3389 para RDP<sup>1</sup>
- una regla NAT para traducir todo el tráfico entrante en el puerto 3391 al puerto 3389 para RDP<sup>1</sup>
- una regla de equilibrador de carga para equilibrar todo el tráfico entrante en el puerto 80 al puerto 80 en las direcciones en el grupo de back-end.

<sup>1</sup> reglas NAT están asociadas a una instancia de máquina virtual específica detrás del equilibrador de carga. El tráfico de red que lleguen al puerto 3389 se envía a la máquina virtual específica y el puerto asociado a la regla NAT. Debe especificar un protocolo (UDP o TCP) para una regla NAT. Ambos protocolos no se pueden asignar al mismo puerto.

1. Configurar las variables de PowerShell

        $probeV4V6Name = "ProbeForIPv4AndIPv6"
        $natRule1V4Name = "NatRule-For-Rdp-VM1"
        $natRule2V4Name = "NatRule-For-Rdp-VM2"
        $lbRule1V4Name = "LBRuleForIPv4-Port80"
        $lbRule1V6Name = "LBRuleForIPv6-Port80"

2. Crear el sondeo

    En el ejemplo siguiente se crea un sondeo TCP comprueba conectividad hacia el puerto TCP de back-end 80 cada 15 segundos. Se marcarán el recurso de back-end disponible después de dos errores consecutivos.

        $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName

3. Crear reglas NAT entrantes para permitir conexiones RDP a los recursos de back-end

        $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
        $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName

4. Crear reglas que enviar tráfico a puertos back-end diferentes dependiendo de qué front-end recibió la solicitud de un equilibrador de carga

        $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
        $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName

5. Compruebe la configuración

        azure network lb show --resource-group $rgName --name $lbName

    Resultados esperados:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show


## <a name="create-nics"></a>Crear NIC

Crear NIC y asociarlos reglas NAT, reglas de equilibrador de carga y sondeos.

1. Configurar las variables de PowerShell

        $nic1Name = "myIPv4IPv6Nic1"
        $nic2Name = "myIPv4IPv6Nic2"
        $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
        $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
        $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
        $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
        $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
        $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"

2. Cree una NIC para cada back-end y agregue una configuración de IPv6.

        $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

        $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Crear los recursos de máquina virtual de back-end y adjuntar cada NIC

Para crear máquinas virtuales, debe tener una cuenta de almacenamiento. Equilibrio de carga, las máquinas virtuales deben estar los miembros de un conjunto de disponibilidad. Para obtener más información sobre cómo crear máquinas virtuales, vea [crear una máquina virtual de Azure con PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

1. Configurar las variables de PowerShell

        $storageAccountName = "ps08092016v6sa0"
        $availabilitySetName = "myIPv4IPv6AvailabilitySet"
        $vm1Name = "myIPv4IPv6VM1"
        $vm2Name = "myIPv4IPv6VM2"
        $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
        $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
        $disk1Name = "WindowsVMosDisk1"
        $disk2Name = "WindowsVMosDisk2"
        $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
        $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
        $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
        $vmUserName = "vmUser"
        $mySecurePassword = "PlainTextPassword*1"

    >[AZURE.WARNING] Este ejemplo usa el nombre de usuario y la contraseña para las máquinas virtuales en texto sin cifrar. Correspondiente debe tener cuidado al usar credenciales en texto sin cifrar. Para obtener un método más seguro de administración de credenciales en PowerShell, vea el cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

2. Crear el conjunto de cuenta y la disponibilidad de almacenamiento

    Puede usar una cuenta de almacenamiento existente al crear las máquinas virtuales. El comando siguiente crea una nueva cuenta de almacenamiento.

        $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"

    A continuación, cree el conjunto de disponibilidad.

        $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location

3. Crear máquinas virtuales con las NIC asociadas

        $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

        $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn  --storage-account-name $storageAccountName --disable-bginfo-extension

## <a name="next-steps"></a>Pasos siguientes

[Empezar a configurar un equilibrador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
