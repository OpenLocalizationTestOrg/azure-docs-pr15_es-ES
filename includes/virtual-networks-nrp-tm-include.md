## <a name="traffic-manager-profile"></a>Perfil de administrador de tráfico

Administrador de tráfico y su recurso de extremo secundarios habilitar el enrutamiento de DNS a extremos en Azure y fuera de Azure. Esta distribución de tráfico se rige por los métodos de enrutamiento de directiva. Administrador de tráfico también permite el estado del extremo para supervisar y tráfico desviado adecuadamente según el estado de un extremo. 

| (Propiedad) | Descripción |
|---|---|
|**trafficRoutingMethod**| los valores posibles son el *rendimiento*, *Weighted*y *prioridad* | 
| **dnsConfig** | Nombre de dominio completo para el perfil | 
| **Protocolo** | Protocolo de supervisión, los valores posibles son *HTTP* y *HTTPS*|
| **Puerto** | puerto de supervisión |  
| **Ruta de acceso** | ruta de acceso de supervisión |
| **Extremos** |  contenedor para recursos de punto final | 

### <a name="endpoint"></a>Punto final 

Un extremo es un recurso secundario de un perfil de administrador de tráfico. Representa un servicio o extremo de web para que el usuario se distribuye el tráfico en función de la directiva configurada en el recurso de perfil de administrador de tráfico. 

| (Propiedad) | Descripción | 
|---|---| 
| **Tipo** |  el tipo de extremo, los valores posibles son *punto final de Azure*, *Extremo externo*y *Extremo anidados* | 
| **targetResourceId** |  dirección IP pública de un extremo de servicio o la web. Esto puede ser un extremo de Azure o externo. | 
| **Peso** | peso de extremo utilizado en la administración del tráfico. | 
| **Prioridad** | prioridad del extremo, que se utiliza para definir una acción de migración tras error |

Ejemplo de administrador de tráfico en formato Json: 


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## <a name="additional-resources"></a>Recursos adicionales

Para obtener más información, lea [API de REST de documentación para el Administrador de tráfico](https://msdn.microsoft.com/library/azure/mt163664.aspx) .
