La siguiente tabla enumera los requisitos para las puertas de enlace VPN de RouteBased y PolicyBased. Esta tabla se aplica al administrador de recursos y modelos de implementación clásico. El modelo clásico, puertas de enlace VPN PolicyBased son los mismos que las puertas de enlace estáticos y puertas de enlace de ruta son los mismos que las puertas de enlace dinámicos.


|   | **Puerta de enlace VPN PolicyBased Basic** | **Puerta de enlace VPN RouteBased Basic** | **Puerta de enlace VPN RouteBased estándar**   | **Puerta de enlace VPN RouteBased alto rendimiento** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Conectividad de sitio a sitio (S2S)**  | Configuración de la VPN PolicyBased        | Configuración de la VPN RouteBased  | Configuración de la VPN RouteBased     | Configuración de la VPN RouteBased    |
| Conectividad de punto al sitio de **(P2S**)      | No compatible   | Compatible (puede coexistencia con S2S)  | Compatible (puede coexistencia con S2S)  | Compatible (puede coexistencia con S2S) |
| **Método de autenticación**                 |    Clave previamente compartida  | Clave previamente compartida para S2S conectividad, certificados para conectividad P2S | Clave previamente compartida para S2S conectividad, certificados para conectividad P2S | Clave previamente compartida para S2S conectividad, certificados para conectividad P2S |
| **Número máximo de conexiones de S2S**       | 1                              | 10                                                                    | 10                                | 30                               |
| **Número máximo de conexiones de P2S**       | No compatible                  | 128                                                                   | 128                               | 128                              |
|**Compatibilidad con enrutamiento activa (BGP)**           | No compatible                  | No compatible                                                         | Compatibles                     | Compatibles                   |
 
