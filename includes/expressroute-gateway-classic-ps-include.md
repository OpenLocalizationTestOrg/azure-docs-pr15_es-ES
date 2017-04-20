Debe crear una VNet y una subred de puerta de enlace en primer lugar, antes de trabajar en las siguientes tareas. Vea el artículo [Configurar una red Virtual con el portal clásico](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) para obtener más información.   

## <a name="add-a-gateway"></a>Agregar una puerta de enlace

Use el comando siguiente para crear una puerta de enlace. Asegúrese de sustituir los valores para sus propios.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Comprobar que la puerta de enlace se ha creado

Utilice el comando siguiente para comprobar que la puerta de enlace se ha creado. Este comando también recupera el identificador de la puerta de enlace, que necesita para otras operaciones.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Cambiar el tamaño de una puerta de enlace

Hay un número de [SKU de puerta de enlace](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Puede usar el comando siguiente para cambiar el SKU de puerta de enlace en cualquier momento.

>[AZURE.IMPORTANT] Este comando no funciona para UltraPerformance puerta de enlace. Para cambiar la puerta de enlace a una puerta de enlace de UltraPerformance, quitar la puerta de enlace ExpressRoute existente y, a continuación, crear una nueva puerta de enlace de UltraPerformance. Para reducir la puerta de enlace de una puerta de enlace de UltraPerformance, quitar la puerta de enlace de UltraPerformance y, a continuación, cree una nueva puerta de enlace. 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Quitar una puerta de enlace

Use el comando siguiente para quitar una puerta de enlace

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>