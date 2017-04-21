<properties 
   pageTitle="Cómo configurar una dirección IP privada estática en modo ARM mediante CLI | Microsoft Azure"
   description="Descripción de direcciones IP estática (DIP) y cómo se administran en modo ARM mediante CLI"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-in-azure-cli"></a>Cómo configurar una dirección IP privada estática en CLI de Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]En este artículo trata sobre el modelo de implementación de administrador de recursos. También puede [Administrar direcciones IP privado en el modelo de implementación clásico](virtual-networks-static-private-ip-classic-cli.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Los comandos de CLI de Azure de ejemplo siguientes esperan un entorno simple ya ha creado. Si desea ejecutar los comandos que se muestran en este documento, cree primero el entorno de prueba que se describe en [crear un vnet](virtual-networks-create-vnet-arm-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Cómo especificar una dirección IP privada estática al crear una máquina virtual
Para crear una máquina virtual denominada *DNS01* en la subred *front-end* de un VNet denominado *TestVNet* con una dirección IP privada estática de *192.168.1.101*, siga los pasos siguientes:

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.

2. Ejecutar el comando de **azure config modo** para cambiar al modo de administrador de recursos, como se muestra a continuación.

        azure config mode arm

    Resultados esperados:

        info:    New mode is arm

3. Ejecute la **red azure público ip crear** para crear una dirección IP pública para la máquina virtual. La lista que aparece después de la salida explica los parámetros que se utiliza.

        azure network public-ip create -g TestRG -n TestPIP -l centralus
    
    Resultados esperados:

        info:    Executing command network public-ip create
        + Looking up the public ip "TestPIP"
        + Creating public ip address "TestPIP"
        + Looking up the public ip "TestPIP"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
        data:    Name                            : TestPIP
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        info:    network public-ip create command OK

    - **-g (o--grupo de recursos)**. Nombre de la dirección IP pública se creará en el grupo de recursos.
    - **-n (o--nombre)**. Nombre de la dirección IP pública.
    - **-l (o--ubicación)**. Región de Azure donde se creará la dirección IP pública. En el escenario, *centralus*.

3. Ejecute el comando **crear red azure nic** para crear una NIC con una dirección IP privada estática. La lista que aparece después de la salida explica los parámetros que se utiliza.

        azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd

    Resultados esperados:

        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

    - **-a (o--dirección de ip privada)**. Dirección IP privada estática de la NIC.
    - **-m (o--nombre de vnet de subred)**. Nombre de la VNet donde se creará el NIC.
    - **-k (o--nombre de subred)**. Nombre de la subred donde se creará el NIC.

4. Ejecute el comando **crear azure vm** para crear la máquina virtual con la dirección IP pública y NIC creado anteriormente. La lista que aparece después de la salida explica los parámetros que se utiliza.

        azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd

    Resultados esperados:

        info:    Executing command vm create
        + Looking up the VM "DNS01"
        info:    Using the VM Size "Standard_A1"
        warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account vnetstorage
        + Looking up the NIC "TestNIC"
        info:    Found an existing NIC "TestNIC"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
        info:    Found public ip parameters, trying to setup PublicIP profile
        + Looking up the public ip "TestPIP"
        info:    Found an existing PublicIP "TestPIP"
        info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
        + Updating NIC "TestNIC"
        + Looking up the NIC "TestNIC"
        + Creating VM "DNS01"
        info:    vm create command OK

    - **-y (o tipo de sistema operativo:)**. Tipo de sistema operativo de la máquina virtual, *Windows* o *Linux*.
    - **-f (o nombre de nic--)**. Nombre de la NIC usará la máquina virtual.
    - **-i (o--nombre de ip pública)**. Nombre de la dirección IP pública usará la máquina virtual.
    - **-F (o nombre--vnet)**. Nombre de la VNet donde se creará la máquina virtual.
    - **-j (o nombre de subred: vnet)**. Nombre de la subred donde se creará la máquina virtual.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Cómo recuperar información de dirección IP privada estática de una máquina virtual

Para ver la información de dirección IP privada estática de la máquina virtual creada con esta secuencia de comandos, ejecute el siguiente comando CLI de Azure y observar los valores para el *método de asignación de IP privadas* y *la dirección IP privada*:

    azure vm show -g TestRG -n DNS01

Resultados esperados:

    info:    Executing command vm show
    + Looking up the VM "DNS01"
    + Looking up the NIC "TestNIC"
    + Looking up the public ip "TestPIP
    data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    ProvisioningState               :Succeeded
    data:    Name                            :DNS01
    data:    Location                        :centralus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Source image:
    data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :DNS01
    data:      User Name                     :adminuser
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-90-1A-A8
    data:          Provisioning State        :Succeeded
    data:          Name                      :TestNIC
    data:          Location                  :centralus
    data:            Private IP alloc-method :Static
    data:            Private IP address      :192.168.1.101
    data:            Public IP address       :40.122.213.159
    info:    vm show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Cómo quitar una dirección IP privada estática de una máquina virtual
No se puede quitar una dirección IP privada estática de una NIC en Azure CLI para el Administrador de recursos. Debe crear una nueva NIC que usa una dirección IP dinámica, quitar la NIC anterior de la máquina virtual y, a continuación, agregar la nueva VM. Para cambiar la NIC para la máquina virtual utiliza int eh comandos anteriores, siga los pasos siguientes.
    
1. Ejecute el comando **crear red azure nic** para crear una nueva NIC con asignación dinámica de IP. Observe cómo no debe especificar la dirección IP en este momento.

        azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd

    Resultados esperados:

        info:    Executing command network nic create
        + Looking up the network interface "TestNIC2"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC2"
        + Looking up the network interface "TestNIC2"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
        data:    Name                            : TestNIC2
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.6
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

2. Ejecute el comando **Configurar azure vm** para cambiar la NIC usada por la máquina virtual.

        azure vm set -g TestRG -n DNS01 -N TestNIC2

    Resultados esperados:

        info:    Executing command vm set
        + Looking up the VM "DNS01"
        + Looking up the NIC "TestNIC2"
        + Updating VM "DNS01"
        info:    vm set command OK

3. Si desea, ejecute el comando **de red azure nic eliminar** para eliminar la NIC antiguo.

        azure network nic delete -g TestRG -n TestNIC --quiet

    Resultados esperados:

        info:    Executing command network nic delete
        + Looking up the network interface "TestNIC"
        + Deleting network interface "TestNIC"
        info:    network nic delete command OK

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Cómo agregar una dirección IP privada estática a una máquina virtual existente
Para agregar una dirección IP privada estática a la NIC usada por la máquina virtual creada con la secuencia de comandos anterior, ejecute el siguiente comando:

    azure network nic set -g TestRG -n TestNIC2 -a 192.168.1.101

Resultados esperados:

    info:    Executing command network nic set
    + Looking up the network interface "TestNIC2"
    + Updating network interface "TestNIC2"
    + Looking up the network interface "TestNIC2"
    data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
    data:    Name                            : TestNIC2
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : centralus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-90-29-25
    data:    Enable IP forwarding            : false
    data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    IP configurations:
    data:      Name                          : NIC-config
    data:      Provisioning state            : Succeeded
    data:      Private IP address            : 192.168.1.101
    data:      Private IP Allocation Method  : Static
    data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las direcciones [IP públicas reservada](virtual-networks-reserved-public-ip.md) .
- Obtenga información sobre las direcciones [IP públicas (ILPIP) de nivel de instancia](virtual-networks-instance-level-public-ip.md) .
- Consulte las [API REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).
