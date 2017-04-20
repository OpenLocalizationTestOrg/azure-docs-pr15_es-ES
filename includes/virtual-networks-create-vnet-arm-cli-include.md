## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>Cómo crear un VNet con la CLI de Azure

Puede utilizar la CLI Azure para administrar los recursos de Azure desde el símbolo del sistema desde cualquier equipo que ejecute Windows, Linux o OSX. Para crear un VNet con Azure CLI, siga los pasos siguientes.

1. Si nunca ha usado la CLI de Azure, vea [instalar y configurar la CLI de Azure](../articles/xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.
2. Ejecutar el comando de **azure config modo** para cambiar al modo de administrador de recursos, como se muestra a continuación.

        azure config mode arm

    Éste es el resultado esperado para el comando anterior:

        info:    New mode is arm

3. Si es necesario, ejecute el **grupo azure crear** para crear un nuevo grupo de recursos, como se muestra a continuación. Observe que el resultado del comando. La lista que aparece después de la salida explica los parámetros que se utiliza. Para obtener más información acerca de los grupos de recursos, visite [Información general del Administrador de recursos de Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

        azure group create -n TestRG -l centralus

    Éste es el resultado esperado para el comando anterior:

        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

    - **-n (o--nombre)**. Nombre del nuevo grupo de recursos. En el escenario, *TestRG*.
    - **-l (o--ubicación)**. Región de Azure donde se creará el nuevo grupo de recursos. En el escenario, *centralus*.

4. Ejecute el comando **crear red azure vnet** para crear una VNet y una subred, como se muestra a continuación. 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    Éste es el resultado esperado para el comando anterior:

        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK

    - **-g (o--grupo de recursos)**. Nombre del grupo de recursos donde se creará el VNet. En el escenario, *TestRG*.
    - **-n (o--nombre)**. Nombre de la VNet que debe crearse. En el escenario, *TestVNet*
    - **-a (o--prefijos de direcciones)**. Lista de bloques CIDR utilizado para el espacio de direcciones VNet. En el escenario, *192.168.0.0/16*
    - **-l (o--ubicación)**. Región de Azure donde se creará el VNet. En el escenario, *centralus*.

5. Ejecute el comando **Crear subred vnet azure** para crear una subred tal como se muestra a continuación. Observe que el resultado del comando. La lista que aparece después de la salida explica los parámetros que se utiliza.

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    Éste es el resultado esperado para el comando anterior:

        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK

    - **-e (o nombre--vnet**. Nombre de la VNet donde se creará la subred. En el escenario, *TestVNet*.
    - **-n (o--nombre)**. Nombre de la nueva subred. En el escenario, *front-end*.
    - **-a (o--prefijo de dirección)**. Bloque CIDR. Cuatro nuestro escenario, *192.168.1.0/24*.

6. Repita el paso 5 anteriores para crear otras subredes, si es necesario. En el escenario, ejecute el comando siguiente para crear la subred *back-end* .

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Ejecute el comando **Mostrar vnet de azure red** para ver las propiedades de la nueva vnet, tal como se muestra a continuación.

        azure network vnet show -g TestRG -n TestVNet

    Éste es el resultado esperado para el comando anterior:

        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK
