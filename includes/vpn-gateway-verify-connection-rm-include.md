### <a name="to-verify-your-connection-by-using-powershell"></a>Para comprobar la conexión con PowerShell

Puede comprobar que la conexión se realizó correctamente mediante el uso de la `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet, con o sin `-Debug`. 

1. Use el siguiente ejemplo de cmdlet, configurar los valores para que coincida con su propio. Si se le solicita, seleccione 'A' para poder ejecutar 'All'. En el ejemplo, `-Name` hace referencia al nombre de la conexión que ha creado y desea probar.

        Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. Cuando haya finalizado el cmdlet, ver los valores. En el ejemplo siguiente muestra el estado de conexión 'Conectado' y puede ver la entrada y salida de bytes.

        Body:
        {
          "name": "MyGWConnection",
          "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
          "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
            "virtualNetworkGateway1": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
        teways/vnetgw1"
            },
            "localNetworkGateway2": {
              "id":
        "/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
        ways/LocalSite"
            },
            "connectionType": "IPsec",
            "routingWeight": 10,
            "sharedKey": "abc123",
            "connectionStatus": "Connected",
            "ingressBytesTransferred": 33509044,
            "egressBytesTransferred": 4142431
          }

### <a name="to-verify-your-connection-by-using-the-azure-portal"></a>Para comprobar la conexión a través del portal de Azure

En el portal de Azure, puede ver el estado de conexión, vaya a la conexión. Hay varias formas de hacerlo. Los pasos siguientes muestran una forma para desplazarse a la conexión y comprobar.

1. En el [portal de Azure](http://portal.azure.com), haga clic en **todos los recursos** y vaya a la puerta de enlace de red virtual.
2. En el módulo de la puerta de enlace de red virtual, haga clic en **conexiones**. Puede ver el estado de cada conexión.
3. Haga clic en el nombre de la conexión que desea comprobar para abrir **Essentials**. En Essentials, puede ver más información acerca de la conexión. El **estado** es 'Correcto' y 'Conectado' cuando se ha realizado una conexión correcta.

    ![Compruebe la conexión](./media/vpn-gateway-verify-connection-rm-include/connectionsucceeded.png)