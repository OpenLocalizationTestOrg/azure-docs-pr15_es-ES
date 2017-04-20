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