Para modificar la dirección IP de puerta de enlace, utilice la `New-AzureRmVirtualNetworkGatewayConnection` cmdlet. Mientras se mantiene el nombre de la puerta de enlace de red local exactamente igual que el nombre existente, sobrescribirá la configuración. En este momento, el cmdlet "Establecer" no admite la modificación de la dirección IP de puerta de enlace.

### <a name="gwipnoconnection"></a>Cómo modificar la dirección IP de puerta de enlace - sin conexión de la puerta de enlace

Para actualizar la dirección IP de puerta de enlace de la puerta de enlace de red local que todavía no tiene una conexión, utilice el ejemplo siguiente. También puede actualizar los prefijos de dirección al mismo tiempo. La configuración que especifique sobrescribirá los valores existentes. Asegúrese de usar el nombre de la puerta de enlace de red local existente. Si no, que va a crear una nueva puerta de enlace de red local, no sobrescribir uno existente.

Use el siguiente ejemplo, reemplazar los valores de sus propios.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Cómo modificar la dirección IP de puerta de enlace - conexión de puerta de enlace existente

Si existe ya una conexión de la puerta de enlace, primero deberá quitar la conexión. A continuación, puede modificar la dirección IP de puerta de enlace y vuelva a crear una nueva conexión. Esto se traducirán en el tiempo de inactividad para la conexión VPN.


>[AZURE.IMPORTANT] No elimine la puerta de enlace VPN. Si hace esto, debe volver a través de los pasos para volver a crearlo, así como para volver a configurar el enrutador local con la dirección IP que se asignará a la puerta de enlace recién creado.
 

1. Quitar la conexión. Puede buscar el nombre de la conexión mediante la `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName

2. Modifique el valor de GatewayIpAddress. También puede modificar sus prefijos de direcciones en este momento, si es necesario. Tenga en cuenta que esto sobrescribirá la configuración de puerta de enlace de red local existente. Use el nombre de la puerta de enlace de red local existente al modificar para que se sobrescriba la configuración. En caso contrario, se creará una nueva puerta de enlace de red local, no modificar uno existente.

        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. Crear la conexión. En este ejemplo, hemos estamos configurando un tipo de conexión IPsec. Cuando vuelva a crear la conexión, use el tipo de conexión que se especifica para su configuración. Tipos de conexión adicionales, consulte la página de [cmdlet de PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Para obtener el nombre de VirtualNetworkGateway, puede ejecutar la `Get-AzureRmVirtualNetworkGateway` cmdlet.

    Establecer las variables:

        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

    Crear la conexión:
    
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

