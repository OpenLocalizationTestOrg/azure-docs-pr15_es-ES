<properties
   pageTitle="Crear un Internet opuestas equilibrador de carga en el Administrador de recursos con la CLI de Azure | Microsoft Azure"
   description="Aprenda a crear un Internet opuestas equilibrador de carga en el Administrador de recursos con la CLI de Azure"
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

# <a name="creating-an-internal-load-balancer-using-the-azure-cli"></a>Crear un equilibrador de carga interno mediante la CLI de Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación de administrador de recursos. También puede [obtener información sobre cómo crear un opuestas equilibrador de carga con la implementación clásica de Internet](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Implementar la solución mediante la CLI de Azure

Los pasos siguientes muestran cómo crear un opuestas equilibrador de carga con el Administrador de recursos de Azure CLI de Internet. Con el Administrador de recursos de Azure cada recurso se crea y configura de forma individual, luego superponer entre sí para crear un recurso.

Debe crear y configurar los siguientes objetos para implementar un equilibrador de carga:

- Configuración de IP front-end - contiene las direcciones IP públicas para el tráfico de red entrante.
- Grupo de direcciones de back-end - contiene interfaces de red (NIC) para que los equipos virtuales recibir el tráfico de red de equilibrador de carga.
- Reglas de equilibrio de carga - contiene reglas de asignar un puerto público en el equilibrador de carga al puerto en el grupo de direcciones de back-end.
- NAT reglas de entrada: contiene reglas de la asignación de un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.
- Sondeos: contiene sondeos de estado que usa para comprobar la disponibilidad de instancias de máquinas virtuales en el grupo de direcciones de back-end.

Para obtener más información, consulte [Administrador de Azure recursos de soporte técnico para equilibrador de carga](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurar CLI para usar el Administrador de recursos

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../../articles/xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.

2. Ejecutar el comando de **azure config modo** para cambiar al modo de administrador de recursos, como se muestra a continuación.

        azure config mode arm

    Resultados esperados:

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Crear una red virtual y una dirección IP pública para el grupo IP front-end

1. Crear una red virtual (VNet) denominada *NRPVnet* en la ubicación de Estados Unidos oriental mediante un grupo de recursos denominado *NRPRG*.

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    Crear una subred denominada *NRPVnetSubnet* con un bloque CIDR 10.0.0.0/24 en *NRPVnet*.

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. Crear una dirección IP pública denominada *NRPPublicIP* para usarlo con un grupo IP front-end con DNS nombre *loadbalancernrp.eastus.cloudapp.azure.com*. El comando siguiente utiliza el tipo de asignación estática y el tiempo de espera de inactividad de 4 minutos.

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT]El equilibrador de carga utilizará la etiqueta de dominio de la dirección IP pública como su FQDN. Este servicio un cambio de implementación clásico, que usa la nube como el nombre de dominio completo (FQDN) del equilibrador de carga.
    >En este ejemplo, el FQDN es *loadbalancernrp.eastus.cloudapp.azure.com*.

## <a name="create-a-load-balancer"></a>Crear un equilibrador de carga

El comando siguiente crea un equilibrador de carga denominado *NRPlb* en el grupo de recursos *NRPRG* de *Estados Unidos oriental* Azure ubicación.

    azure network lb create NRPRG NRPlb eastus

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Crear un grupo IP front-end y back-end de direcciones

En este ejemplo se muestra cómo crear el grupo IP front-end que recibe el tráfico de red entrante en el equilibrador de carga y el grupo IP de back-end donde envía el tráfico de red de equilibrio de carga en el grupo de servidores front-end.

1. Crear un grupo IP front-end asociar la dirección IP pública creada en el paso anterior y el equilibrador de carga.

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. Configurar un grupo de direcciones de back-end utilizado para recibir el tráfico entrante desde el grupo IP front-end.

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## <a name="create-lb-rules-nat-rules-and-probe"></a>Crear reglas de Libras, reglas NAT y sondeo

Este ejemplo crea los siguientes elementos.

- una regla NAT para traducir todo el tráfico entrante en el puerto 21 al puerto 22<sup>1</sup>
- una regla NAT para traducir todo el tráfico entrante en el puerto 23 al puerto 22
- una regla de equilibrador de carga para equilibrar todo el tráfico entrante en el puerto 80 al puerto 80 en las direcciones en el grupo de back-end.
- una regla de sondeo para comprobar el estado de mantenimiento en una página denominada *HealthProbe.aspx*.

<sup>1</sup> reglas NAT están asociadas a una instancia de máquina virtual específica detrás del equilibrador de carga. El tráfico de red que lleguen al puerto 21 se envía a una máquina virtual específica en el puerto 22 asociada a esta regla NAT. Debe especificar un protocolo (UDP o TCP) para una regla NAT. Ambos protocolos no se pueden asignar al mismo puerto.

1. Crear las reglas NAT.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22

2. Crear una regla de equilibrador de carga.

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. Crear un sondeo de estado.

        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4

4. Compruebe la configuración.

        azure network lb show nrprg nrplb

    Resultados esperados:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Crear NIC

Debe crear NIC (o modificar los existentes) y asociarlos reglas NAT, reglas de equilibrador de carga y sondeos.

1. Crear una NIC denominada *nic1 de libras pueden*y asociarla con la regla NAT *rdp1* y el grupo de direcciones de back-end de *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Resultados esperados:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Crear una NIC denominada *NIC 2 de libras pueden*y asociarla con la regla NAT *rdp2* y el grupo de direcciones de back-end de *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Crear una máquina virtual (VM) denominada *web1*y asociar con la NIC denominada *nic1 de libras ser*. Una cuenta de almacenamiento denominada *web1nrp* se creó antes de ejecutar el siguiente comando.

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Máquinas virtuales en un equilibrador de carga deben estar en el mismo conjunto de disponibilidad. Usar `azure availset create` para crear la disponibilidad de una conjunto.

    El resultado debería ser similar al siguiente:

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    >[AZURE.NOTE] El mensaje informativo **se trata de una NIC sin publicIP configurado** se espera desde la NIC creada para la conexión a Internet usando la dirección IP pública del equilibrador de carga de equilibrador de carga.

    Puesto que la *nic1 de libras ser* NIC está asociada con la regla NAT *rdp1* , puede conectarse a *web1* con RDP a través de puerto 3441 equilibrador de carga.

4. Crear una máquina virtual (VM) denominada *web2*y asociar con la NIC denominada *NIC 2 de libras ser*. Una cuenta de almacenamiento denominada *web1nrp* se creó antes de ejecutar el siguiente comando.

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="update-an-existing-load-balancer"></a>Actualizar un equilibrador de carga existente

Puede agregar reglas que hacen referencia a un equilibrador de carga existente. En el ejemplo siguiente, se agrega una nueva regla de equilibrador de carga a un equilibrador de carga existente **NRPlb**

    azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool

## <a name="delete-a-load-balancer"></a>Eliminar un equilibrador de carga

Use el comando siguiente para quitar un equilibrador de carga:

    azure network lb delete --resource-group nrprg --name nrplb

## <a name="next-steps"></a>Pasos siguientes

[Empezar a configurar un equilibrador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurar un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
