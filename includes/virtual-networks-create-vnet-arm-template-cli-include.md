## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Implementar la plantilla ARM mediante la CLI de Azure

Para implementar la plantilla ARM que ha descargado mediante CLI de Azure, siga los pasos siguientes.

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../articles/xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.
2. Ejecutar la **`azure config mode`** comando para cambiar al modo de administrador de recursos, como se muestra a continuación.

        azure config mode arm

    Éste es el resultado esperado para el comando anterior:

        info:    New mode is arm

3. Si es necesario, ejecute el **`azure group create`** para crear un nuevo grupo de recursos, como se muestra a continuación. Observe que el resultado del comando. La lista que aparece después de la salida explica los parámetros que se utiliza. Para obtener más información acerca de los grupos de recursos, visite [Información general del Administrador de recursos de Azure](../articles/resource-group-overview.md).

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

4. Ejecutar la **`azure group deployment create`** cmdlet para implementar el nuevo VNet mediante la plantilla y el parámetro de archivos que ha descargado y modificado por encima. La lista que aparece después de la salida explica los parámetros que se utiliza.

        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

    Éste es el resultado esperado para el comando anterior:

        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK

    - **-g (o--grupo de recursos)**. Nombre del grupo de recursos la VNet nuevo se creará en.
    - **-f (o--archivo de plantilla)**. Ruta de acceso al archivo de plantilla ARM.
    - **-e (o--parámetros archivo)**. Ruta de acceso al archivo de parámetros ARM.

5. Ejecutar la **`azure network vnet show`** comando para ver las propiedades de la nueva vnet, tal como se muestra a continuación.

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
