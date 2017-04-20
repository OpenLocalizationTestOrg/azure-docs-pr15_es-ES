|    | **Rendimiento de puerta de enlace VPN (1)** | **Establece un túnel IPsec de puerta de enlace VPN max (2)** | **Rendimiento de puerta de enlace ExpressRoute** | **Puerta de enlace VPN y ExpressRoute coexistencia**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU básica (3)(5)**              |  100 Mbps | 10                         |  500 Mbps                           | No   |
| **SKU estándar (4)(5)**           |  100 Mbps | 10                         | 1000 Mbps                           | Sí  |
| **Alto rendimiento SKU (4)**   | 200 Mbps  | 30                         | 2000 Mbps                           | Sí  |

- (1) el rendimiento de la VPN es una estimación aproximada según las medidas entre VNets en la misma región de Azure. No es un rendimiento garantizado para conexiones entre local a través de Internet. Es la medida de rendimiento máximo posible.
- (2) consulte RouteBased VPN el número de túneles. Una VPN PolicyBased solo admite un túnel VPN de sitio a otro.
- (3) BGP no se admite para la SKU básica.
- (4) VPN PolicyBased no son compatibles para esta SKU. Se admiten para la SKU básica.
- (5) conexiones de puerta de enlace de VPN activo S2S no son compatibles para esta SKU. Activo activo es compatible con la SKU HighPerformance.