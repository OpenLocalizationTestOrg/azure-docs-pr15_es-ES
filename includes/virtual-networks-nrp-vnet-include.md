## <a name="virtual-network"></a>Red virtual
Recursos de redes (VNET) y subredes virtuales ayudan a definir un límite de seguridad para cargas de trabajo que se ejecuta en Azure. Una VNet se caracteriza por una colección de espacios de direcciones, definido como bloques CIDR. 

>[AZURE.NOTE] Los administradores de red están familiarizados con la notación CIDR. Si no está familiarizado con CIDR, [obtener más información acerca de él](http://whatismyipaddress.com/cidr).

![VNet con varias subredes](./media/resource-groups-networking/Figure4.png)

VNets contener las siguientes propiedades.

|(Propiedad)|Descripción|Valores de ejemplo|
|---|---|---|
|**addressSpace**|Colección de prefijos de direcciones que conforman la VNet en notación CIDR|192.168.0.0/16|
|**subredes**|Colección de subredes que conforman la VNet|consulte [subredes](#Subnets) .|
|**dirección IP**|Dirección IP asignada al objeto. Esta es una propiedad de solo lectura.|104.42.233.77|

### <a name="subnets"></a>Subredes
Una subred es un recurso secundario de una VNet y ayuda a define segmentos de espacios de direcciones dentro de un bloque CIDR con prefijos de direcciones IP. NIC se pueden agregar a subredes y conectadas a máquinas virtuales, que permite la conectividad de varias cargas de trabajo.

Subredes contengan las siguientes propiedades. 

|(Propiedad)|Descripción|Valores de ejemplo|
|---|---|---|
|**addressPrefix**|Prefijo de dirección único que conforman la subred en notación CIDR|192.168.1.0/24|
|**networkSecurityGroup**|GSN aplicado a la subred|consulte [NSGs](#Network-Security-Group)|
|**routeTable**|Tabla de ruta que se aplican a la subred|consulte [UDR](#Route-table)|
|**ipConfigurations**|Colección de objetos de configuración IP utilizado NIC conectadas a la subred|consulte [UDR](#Route-table)|


Ejemplo VNet en formato JSON:

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Recursos adicionales

- Obtenga más información sobre [VNet](../articles/virtual-network/virtual-networks-overview.md).
- Lea la [documentación de referencia de la API de REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) para VNets.
- Lea la [documentación de referencia de la API de REST](https://msdn.microsoft.com/library/azure/mt163618.aspx) de subredes.