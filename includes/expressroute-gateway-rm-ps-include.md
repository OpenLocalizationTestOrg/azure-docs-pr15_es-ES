Los pasos de esta tarea usan un VNet en función de los siguientes valores. Nombres y configuraciones adicionales también se clasifican en esta lista. No utilizamos esta lista directamente en cualquiera de los pasos, aunque le sumamos variables basándose en los valores de esta lista. Puede copiar la lista para usar como referencia, reemplazando los valores por la suya propia.

Lista de referencia de configuración:
    
- Nombre de red virtual = "TestVNet"
- Espacio de direcciones de red virtual = 192.168.0.0/16
- Grupo de recursos = "TestRG"
- Nombre de subred1 = "Cliente" 
- Espacio de direcciones subred1 = "192.168.0.0/16"
- Nombre de puerta de enlace subred: "GatewaySubnet" siempre debe dar nombre a una subred *GatewaySubnet*de puerta de enlace.
- Espacio de direcciones de puerta de enlace subred = "192.168.200.0/26"
- Región = "US oriental"
- Nombre de puerta de enlace = "GW"
- Nombre de puerta de enlace IP = "GWIP"
- Configuración de la puerta de enlace IP nombre = "gwipconf"
-  Tipo = "ExpressRoute" es necesario para una configuración de ExpressRoute este tipo.
- Nombre de puerta de enlace público IP = "gwpip"


## <a name="add-a-gateway"></a>Agregar una puerta de enlace

1. Conectarse a su suscripción de Azure. 

        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. Declare las variables para este ejercicio. En este ejemplo se utilice el las variables en el ejemplo siguiente. Asegúrese de editar para reflejar la configuración que desea usar. 
        
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"

3. Almacene el objeto de red virtual como una variable.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. Agregar una subred de puerta de enlace a la red Virtual. La subred de puerta de enlace debe tener el nombre "GatewaySubnet". Desea crear una puerta de enlace es /27 o mayor (/ 26/25, etcetera.).
            
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. Establecer la configuración.

            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. Almacenar la subred de puerta de enlace como una variable.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. Solicitar una dirección IP pública. La dirección IP solicitada antes de crear la puerta de enlace. No se puede especificar la dirección IP que desea utilizar; se asigna dinámicamente. Deberá usar esta dirección IP en la siguiente sección de configuración. El AllocationMethod debe ser dinámicos.

        $pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic

8. Crear la configuración de la puerta de enlace. La configuración de puerta de enlace define la subred y la dirección IP pública. En este paso, se especifica la configuración que se usará al crear la puerta de enlace. Este paso no crea realmente el objeto de puerta de enlace. Use el ejemplo siguiente para crear la configuración de la puerta de enlace. 

        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

9. Crear la puerta de enlace. En este paso, el **-GatewayType** es especialmente importante. Debe usar el valor **ExpressRoute**. Tenga en cuenta que después de ejecutar estos cmdlets, la puerta de enlace puede tardar 20 minutos o más para crear.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>Comprobar que la puerta de enlace se ha creado

Utilice el comando siguiente para comprobar que la puerta de enlace se ha creado.

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>Cambiar el tamaño de una puerta de enlace

Hay un número de [SKU de puerta de enlace](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Puede usar el comando siguiente para cambiar el SKU de puerta de enlace en cualquier momento.

>[AZURE.IMPORTANT] Este comando no funciona para UltraPerformance puerta de enlace. Para cambiar la puerta de enlace a una puerta de enlace de UltraPerformance, quitar la puerta de enlace ExpressRoute existente y, a continuación, crear una nueva puerta de enlace de UltraPerformance. Para reducir la puerta de enlace de una puerta de enlace de UltraPerformance, quitar la puerta de enlace de UltraPerformance y, a continuación, cree una nueva puerta de enlace.

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>Quitar una puerta de enlace

Use el comando siguiente para quitar una puerta de enlace

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  
