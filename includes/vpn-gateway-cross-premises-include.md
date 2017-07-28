|                              | **Sitio de punto**                                                                            | **Sitio de sitio**                                                                                        | **ExpressRoute**                                                                                                                     |
|------------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Azure compatibles con servicios** | Servicios de nube y máquinas virtuales                                                          | Servicios de nube y máquinas virtuales                                                                     | [Lista de servicios](../expressroute/expressroute-faqs.md#supported-services)                                                       |
| **Anchos típica**       | Normalmente < 100 Mbps agregado                                                               | Normalmente < 100 Mbps agregado                                                                          | 50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 GB/s, 2 GB/s, 5 GB/s, 10 GB/s                                                               |
| **Protocolos admitidos**      | Sockets seguros (SSTP) de protocolo de túnel                                                     | IPsec                                                | Conexión directa por VLAN, tecnologías de red VPN (MPLS, VPLS,...)                                                                                                    |
| **Enrutamiento**                  | RouteBased (dinámico)                                                                        | Se admiten PolicyBased (enrutamiento estático) y RouteBased (enrutamiento dinámico VPN)                 | BGP                                                                                                                                  |
| **Resistencia de conexión**    | activo y pasivo                                                                               | activo y pasivo                                                                                          | activo activo                                                                                                                        |
| **Caso de uso típico**         | Creación de prototipos, desarrollo / prueba / escenarios de laboratorio para servicios en la nube y máquinas virtuales              | Desarrollo / prueba / escenarios de laboratorio y pequeña escala cargas de trabajo de producción para servicios en la nube y máquinas virtuales | Acceso a todos los servicios de Azure (lista validada), empresariales y objetivos crítica cargas de trabajo, copia de seguridad, Big Data, Azure como un sitio de DR |
| **SLA**                      | [SLA](https://azure.microsoft.com/support/legal/sla/)                                        | [SLA](https://azure.microsoft.com/support/legal/sla/)                                                   | [SLA](https://azure.microsoft.com/support/legal/sla/)                                                                                |
| **Precios**                  | [Precios](https://azure.microsoft.com/pricing/details/vpn-gateway/)                           | [Precios](https://azure.microsoft.com/pricing/details/vpn-gateway/)                                      | [Precios](https://azure.microsoft.com/pricing/details/expressroute/)                                                                   |
| **Documentación técnica**  | [Documentación de la puerta de enlace VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Documentación de la puerta de enlace VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/)            | [ExpressRoute documentación](https://azure.microsoft.com/documentation/services/expressroute/)                                        |
| **Preguntas más frecuentes**                     | [Preguntas más frecuentes de puerta de enlace VPN](vpn-gateway-vpn-faq.md)                                                    | [Preguntas más frecuentes de puerta de enlace VPN](vpn-gateway-vpn-faq.md)                                                               | [ExpressRoute preguntas más frecuentes](../expressroute/expressroute-faqs.md)                                                                             |