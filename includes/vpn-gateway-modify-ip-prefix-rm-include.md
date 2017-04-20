### <a name="noconnection"></a>Cómo agregar o quitar prefijos - sin conexión de la puerta de enlace

- **Para agregar** prefijos de direcciones adicionales en un local de red puerta de enlace que ha creado, pero que no todavía tienen una conexión de la puerta de enlace, utilice el ejemplo siguiente. Asegúrese de cambiar los valores a la suya propia.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **Para quitar** un prefijo de dirección de una puerta de enlace de red local que no tiene una conexión VPN, utilice el ejemplo siguiente. Omitir los prefijos que ya no necesite. En este ejemplo, ya no necesitamos prefijo 20.0.0.0/24 (desde el ejemplo anterior), por lo que actualizaremos local puerta de enlace de red y excluir dicho prefijo.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Cómo agregar o quitar prefijos - conexión de puerta de enlace existente

Si ha creado su conexión de puerta de enlace y desea agregar o quitar los prefijos de dirección IP contenidos en la puerta de enlace de red local, debe realizar los siguientes pasos en orden. Esto se traducirán en el tiempo de inactividad para la conexión VPN. Cuando se actualiza su prefijos, deberá quitar la conexión, modifique los prefijos y, a continuación, crear una nueva conexión. En los ejemplos siguientes, asegúrese de cambiar los valores a la suya propia.

>[AZURE.IMPORTANT] No elimine la puerta de enlace VPN. Si hace esto, debe volver a través de los pasos para volver a crearlo, así como para volver a configurar el enrutador local con la nueva configuración.
 
1. Quitar la conexión.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. Modificar los prefijos de dirección de la puerta de enlace de red local.

    Establecer la variable de la LocalNetworkGateway.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

    Modificar los prefijos.

        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Crear la conexión. En este ejemplo, hemos estamos configurando un tipo de conexión IPsec. Cuando vuelva a crear la conexión, use el tipo de conexión que se especifica para su configuración. Tipos de conexión adicionales, consulte la página de [cmdlet de PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .

    Establecer la variable de la VirtualNetworkGateway.

        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

    Crear la conexión. Tenga en cuenta que este ejemplo se utiliza la variable $local que haya configurado en el paso anterior.


        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'
