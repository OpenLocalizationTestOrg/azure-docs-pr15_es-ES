<properties
   pageTitle="Crear un equilibrador de carga interno mediante la CLI de Azure en el Administrador de recursos | Microsoft Azure"
   description="Aprenda a crear un equilibrador de carga interno mediante la CLI de Azure en el Administrador de recursos"
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

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Crear un equilibrador de carga interno mediante la CLI de Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementación clásico](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Implementar la solución mediante la CLI de Azure

Los pasos siguientes muestran cómo crear un equilibrador de carga a través de Internet mediante el Administrador de recursos de Azure con CLI. Con el Administrador de recursos de Azure, cada recurso es crear y configurar de forma individual y después colocar juntos para crear un recurso.

Debe crear y configurar los objetos siguientes para implementar un equilibrador de carga:

- **Configuración de IP de front-end**: contiene las direcciones IP públicas para el tráfico de red entrante
- **Grupo de direcciones de back-end**: contiene interfaces de red (NIC) que permiten a los equipos virtuales recibir el tráfico de red de equilibrador de carga
- **Reglas de equilibrio de carga**: contiene reglas que asignan un puerto público en el equilibrador de carga al puerto en el grupo de direcciones de back-end
- **Las reglas de entrada NAT**: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end
- **Sondeos**: contiene sondeos de estado que se usan para comprobar la disponibilidad de instancias de máquinas virtuales en el grupo de direcciones de back-end

Para obtener más información, consulte [Administrador de Azure recursos de soporte técnico para equilibrador de carga](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurar CLI para usar el Administrador de recursos

1. Si nunca ha usado CLI de Azure, consulte [instalar y configurar la CLI Azure](../../articles/xplat-cli-install.md). Siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.

2. Ejecute el comando de **modo de azure config** para cambiar a modo de administrador de recursos, como sigue:

        azure config mode arm

    Resultados esperados:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Crear un equilibrador de carga interno paso a paso

1. Inicie sesión en Azure.

        azure login

    Cuando se le solicite, escriba sus credenciales de Azure.

2. Cambiar las herramientas de comandos en modo de administrador de recursos de Azure.

        azure config mode arm

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Todos los recursos en el Administrador de recursos de Azure están asociados a un grupo de recursos. Si todavía no lo ha hecho todavía, cree un grupo de recursos.

    azure group create <resource group name> <location>

## <a name="create-an-internal-load-balancer-set"></a>Crear un conjunto de equilibrador de carga interno

1. Crear un equilibrador de carga interno

    En el siguiente escenario, se crea un grupo de recursos denominado nrprg en región oriental US.

        azure network lb create --name nrprg --location eastus

    >[AZURE.NOTE] Todos los recursos para una equilibradores de carga internos, como redes virtuales y subredes virtual, deben estar en el mismo grupo de recursos y de la misma región.

2. Crear una dirección IP front-end para el equilibrador de carga interno.

    Debe ser la dirección IP que usa en el intervalo de subred de su red virtual.

        azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet

3. Crear el grupo de direcciones de back-end.

        azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb

    Después de definir una dirección IP front-end y un conjunto de direcciones de back-end, puede crear reglas de equilibrador de carga, las reglas de entrada NAT, y sondeos de salud personalizada.

4. Crear una regla de equilibrador de carga para el equilibrador de carga interno.

    Si sigue los pasos anteriores, el comando crea una regla de equilibrador de carga para escuchar el puerto 1433 en el grupo de servidores front-end y enviar tráfico de red de equilibrio de carga al grupo de direcciones de back-end, además, usa el puerto 1433.

        azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb

5. Crear reglas NAT entrante.

    Las reglas de entrada NAT se usan para crear los extremos de un equilibrador de carga que vaya a una instancia de máquina virtual específica. Los pasos anteriores, crean dos reglas NAT para escritorio remoto.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389

6. Crear sondeos de estado para el equilibrador de carga.

    Un sondeo de mantenimiento comprueba todas las instancias de máquina virtual para asegurarse de que pueden enviar tráfico de red. La instancia de máquina virtual con comprobaciones de sondeo error se quitará el equilibrador de carga hasta que va volver a conectarse a una comprobación de sondeo determina que es correcto.

        azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4

    >[AZURE.NOTE]La plataforma de Microsoft Azure, utiliza una dirección de IPv4 estática, públicamente enrutable para una gran variedad de escenarios de administración. La dirección IP es 168.63.129.16. Esta dirección IP no debería ser bloqueada por los servidores de seguridad, ya que esto puede provocar un comportamiento inesperado.
    >Con respecto a Equilibrio de carga interna Azure, se usa esta dirección IP mediante la supervisión de sondeos desde el equilibrador de carga para determinar el estado de mantenimiento de máquinas virtuales de un conjunto de equilibrio de carga. Si un grupo de seguridad de red se utiliza para restringir el tráfico a Azure máquinas virtuales de Windows en un conjunto de equilibrio de carga internamente o se aplica a una subred de una red virtual, asegúrese de que se agrega una regla de seguridad de la red para permitir el tráfico de 168.63.129.16.

## <a name="create-nics"></a>Crear NIC

Debe crear NIC (o modificar los existentes) y asociarlos reglas NAT, reglas de equilibrador de carga y sondeos.

1. Crear una NIC denominada *nic1 de libras pueden*y, a continuación, asociarla con la regla NAT *rdp1* y el grupo de direcciones de back-end de *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus

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

2. Crear una NIC denominada *ser kg-NIC 2*y, a continuación, asociarla con la regla NAT *rdp2* y el grupo de direcciones de back-end de *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus

3. Crear una máquina virtual denominada *DB1*y asociarlo la NIC denominada *nic1 de libras ser*. Crear una cuenta de almacenamiento denominada *web1nrp* antes de ejecuta el siguiente comando:

        azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Máquinas virtuales en un equilibrador de carga deben estar en el mismo conjunto de disponibilidad. Usar `azure availset create` para crear la disponibilidad de una conjunto.

4. Crear una máquina virtual (VM) denominada *DB2*y asociarlo la NIC denominada *NIC 2 de libras ser*. Una cuenta de almacenamiento denominada *web1nrp* se creó antes de ejecutar el siguiente comando.

        azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="delete-a-load-balancer"></a>Eliminar un equilibrador de carga

Para quitar un equilibrador de carga, utilice el siguiente comando:

    azure network lb delete --resource-group nrprg --name ilbset

## <a name="next-steps"></a>Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga con afinidad IP de origen](load-balancer-distribution-mode.md)

[Configurar los valores de tiempo de espera TCP inactivas para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
