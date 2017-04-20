| Recursos                                    | Límite                                  |
|---------------------------------------------|----------------------------------------|
| Tamaño de caché                                  | 530 GB ([póngase en contacto con nosotros](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase) para obtener más información)                                  |
| Bases de datos                                   | 64                                     |
| Max clientes conectados                       | 40.000                                 |
| Redis réplicas de caché (para alta disponibilidad) | 1 |
| Shards en una caché premium con clústeres    | 10 |

Límites de caché Redis Azure y tamaños son diferentes para cada nivel de precios. Para ver los niveles de precios y sus tamaños asociados, vea [Precios de Azure Redis caché](https://azure.microsoft.com/pricing/details/cache/).

Para obtener más información sobre los límites de configuración de caché de Azure Redis, consulte [Configuración del servidor predeterminado Redis](redis-cache/cache-configure.md#default-redis-server-configuration).

Dado que es realizar la configuración y administración de instancias de Azure Redis caché por Microsoft, no todos los comandos Redis son compatibles con Azure Redis caché. Para obtener más información, consulte [Redis comandos no se admite en Cache]((redis-cache/cache-configure.md#redis-commands-not-supported-in-azure-redis-cache) Azure Redis.