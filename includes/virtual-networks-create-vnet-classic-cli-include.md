## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Cómo crear un VNet clásica con CLI de Azure

Puede utilizar la CLI Azure para administrar los recursos de Azure desde el símbolo del sistema desde cualquier equipo que ejecute Windows, Linux o OSX. Para crear un VNet con Azure CLI, siga los pasos siguientes.

1. Si nunca ha usado CLI de Azure, vea [instalar y configurar la CLI de Azure](../articles/xplat-cli-install.md) y siga las instrucciones hasta el punto donde seleccionar su cuenta de Azure y suscripción.
2. Ejecute el comando **crear red azure vnet** para crear una VNet y una subred, como se muestra a continuación. La lista que aparece después de la salida explica los parámetros que se utiliza.

            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    
    Resultados esperados:

            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    - **--vnet**. Nombre de la VNet que debe crearse. En el escenario, *TestVNet*
    - **-e (o--espacio de direcciones)**. Espacio de direcciones de VNet. En el escenario, *192.168.0.0*
    - **-i (o - cidr)**. Máscara de red en formato CIDR. En el escenario, *16*.
    - **- n (o--nombre de la subred**). Nombre de la primera subred. En el escenario, *front-end*.
    - **-p (o--inicio de subred de ip)**. Dirección IP inicial subred o espacio de direcciones de subred. En el escenario, *192.168.1.0*.
    - **-r (o--subred cidr)**. Máscara de red en formato CIDR para subred. En el escenario, *24*.
    - **-l (o--ubicación)**. Región de Azure donde se creará el VNet. En el escenario, *Central de Estados Unidos*.

3. Ejecute el comando **Crear subred vnet azure** para crear una subred tal como se muestra a continuación. La lista que aparece después de la salida explica los parámetros que se utiliza.

            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    
    Éste es el resultado esperado para el comando anterior:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    - **-t (o nombre--vnet**. Nombre de la VNet donde se creará la subred. En el escenario, *TestVNet*.
    - **-n (o--nombre)**. Nombre de la nueva subred. En el escenario, *back-end*.
    - **-a (o--prefijo de dirección)**. Bloque CIDR subred. Cuatro nuestro escenario, *192.168.2.0/24*.

4. Ejecute el comando **Mostrar vnet de red azure** para ver las propiedades de la nueva vnet, tal como se muestra a continuación.

            azure network vnet show

    Éste es el resultado esperado para el comando anterior:

            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK
