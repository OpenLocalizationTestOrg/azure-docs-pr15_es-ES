<properties 
    pageTitle="Cómo configurar la compatibilidad de red Virtual para una caché de Redis Premium Azure | Microsoft Azure" 
    description="Obtenga información sobre cómo crear y administrar la compatibilidad de red Virtual para las instancias de Azure Redis caché de nivel Premium" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Cómo configurar la compatibilidad de red Virtual para una caché de Redis Premium Azure
Caché de Azure Redis tiene ofertas de caché diferente que proporcionan flexibilidad en la opción de tamaño de caché y características, como el nuevo nivel de Premium.

Las características de nivel de caché de Azure Redis premium incluyen clústeres, persistencia y compatibilidad de red virtual (VNet). Una VNet es una red privada en la nube. Cuando una instancia de Azure Redis caché está configurada con un VNet, no es accesible públicamente y solo se puede acceder desde máquinas virtuales y aplicaciones dentro de la VNet. En este artículo se describe cómo configurar la compatibilidad de red virtual de una instancia de Azure Redis caché premium.

>[AZURE.NOTE] Caché de Azure Redis admite ambos clásica y BRAZO VNets.

Para obtener información sobre otras características premium de caché, vea [Introducción al nivel Premium caché Redis de Azure](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>¿Por qué VNet?
Implementación de [Azure una red Virtual (VNet)](https://azure.microsoft.com/services/virtual-network/) proporciona aislamiento de la memoria caché Redis de Azure, así como subredes, las directivas de control de acceso y otras características restringir el acceso a Azure Redis caché y seguridad mejorada.

## <a name="virtual-network-support"></a>Soporte técnico de red virtual
Soporte de red (VNet) virtual está configurado en el módulo de **Caché Redis nueva** durante la creación de la memoria caché. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Una vez que haya seleccionado un nivel de precios de premium, puede configurar la integración de Azure VNet de caché Redis seleccionando un VNet que se encuentra en la misma suscripción y ubicación de la memoria caché. Para usar una nueva VNet, crear primero los pasos de [crear una red virtual con el portal de Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o [crear una red virtual (clásico) mediante el portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) y, a continuación, volver al módulo de **Caché Redis nuevo** para crear y configurar la memoria caché de premium.

Para configurar la VNet para la caché de nueva, haga clic en **Una red Virtual** en el módulo de **Caché Redis nuevo** y seleccione la VNet deseada en la lista desplegable.

![Red virtual][redis-cache-vnet]

Seleccione la subred deseada en la lista desplegable de **subred** y especifique la **dirección IP estática**de deseada. Si está utilizando un clásico VNet el campo **dirección IP estática** es opcional, y si no se especifica, se elige uno de la subred seleccionada.

>[AZURE.IMPORTANT] Al implementar una caché de Azure Redis en un VNet ARM, la caché debe estar en una subred dedicada que no contiene otros recursos excepto instancias de Azure Redis caché. Si intenta implementar una caché de Azure Redis en un VNet ARM a una subred que contiene otros recursos, se produce un error en la implementación.

![Red virtual][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] Las primeras cuatro direcciones en una subred están reservadas y no pueden usarse. Para obtener más información, vea [¿hay algunas restricciones sobre el uso de direcciones IP dentro de estas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Después de crea la caché, puede ver la configuración de la VNet haciendo clic en **Una red Virtual** desde el módulo de **configuración** .

![Red virtual][redis-cache-vnet-info]


Para conectarse a la instancia de caché Azure Redis cuando se usa un VNet, especifique el nombre de host de la memoria caché de la cadena de conexión, tal como se muestra en el ejemplo siguiente.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Redis Azure caché VNet preguntas más frecuentes

La siguiente lista contiene las respuestas a las preguntas más frecuentes acerca de la escala de Azure Redis caché.

-   [¿Cuáles son algunos problemas comunes de configuración incorrecta con Azure Redis caché y VNets?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-   [¿Puedo usar VNets con una caché estándar o básica?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-   [¿Por qué no la creación de una caché Redis en algunas subredes pero no otros?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-   [¿Funcionan todas las características de la caché al hospedar una caché en un VNET?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>¿Cuáles son algunos problemas comunes de configuración incorrecta con Azure Redis caché y VNets?

Cuando la caché de Azure Redis está alojado en un VNet, se usan los puertos en la tabla siguiente. Si se bloquean estos puertos, la caché podría no funcionar correctamente. Tiene uno o varios de estos puertos bloqueados es el problema de configuración más comunes cuando se usa la memoria caché Redis de Azure en un VNet.

| Puertos     | Dirección        | Protocolo de transporte | Propósito                                                                           | Dirección IP remota                           |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443     | Saliente         | TCP                | Redis dependencias en Azure almacenamiento/PKI (Internet)                                | *                                   |
| 53          | Saliente         | TCP/UDP            | Redis dependencias en DNS (Internet/VNet)                                         | *                                   |
| 6379, 6380  | Entrada          | TCP                | Comunicación de cliente a Redis, equilibrio de carga de Azure                               | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 8443        | Entrada o salida | TCP                | Detalle de implementación para Redis                                                   | VIRTUAL_NETWORK                     |
| 8500        | Entrada          | TCP/UDP            | Equilibrio de carga de Azure                                                              | AZURE_LOADBALANCER                  |
| 10221 10231 | Entrada o salida | TCP                | Detalle de implementación para Redis (puede restringir extremo remoto a VIRTUAL_NETWORK) | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 13000 a 13999 | Entrada          | TCP                | Comunicación de cliente a Redis clústeres de equilibrio de carga de Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 15000 a 15999 | Entrada          | TCP                | Comunicación de cliente a Redis clústeres de equilibrio de carga de Azure                      | VIRTUAL_NETWORK, AZURE_LOADBALANCER |
| 16001       | Entrada          | TCP/UDP            | Equilibrio de carga de Azure                                                              | AZURE_LOADBALANCER                  |
| 20226       | Entrada + saliente | TCP                | Detalle de implementación para clústeres Redis                                          | VIRTUAL_NETWORK                     |


Hay requisitos de conectividad de red para Azure Redis caché que no se cumplen inicialmente en una red virtual. Caché de Azure Redis requiere todos los elementos siguientes para que funcione correctamente cuando se usa dentro de una red virtual.

-  Conectividad de red saliente a extremos de almacenamiento de Azure en todo el mundo. Esto incluye extremos ubicados en la misma región como la instancia de Azure Redis caché, así como puntos finales de almacenamiento que encuentran en **otras** áreas de Azure. Resolver los extremos de almacenamiento de Azure en los dominios DNS siguientes: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*y *file.core.windows.net*. 
-  Conectividad de red saliente *ocsp.msocsp.com*, *mscrl.microsoft.com* y *crl.microsoft.com*. Esta conectividad es necesaria para admitir la funcionalidad SSL.
-  La configuración de DNS de la red virtual debe ser capaces de resolver todos los extremos y dominios mencionadas en los puntos anteriores. Garantizar una infraestructura DNS válida se configuran y mantienen de la red virtual pueden cumplir estos requisitos de DNS.



### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>¿Puedo usar VNets con una caché estándar o básica?

VNets sólo puede utilizarse con caché premium.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>¿Por qué no la creación de una caché Redis en algunas subredes pero no otros?

Si va a implementar una caché de Azure Redis a un VNet ARM, la caché debe estar en una subred dedicada que no contenga ningún otro tipo de recurso. Si intenta implementar una caché de Azure Redis a una subred ARM VNet que contiene otros recursos, se produce un error en la implementación. Debe eliminar los recursos existentes dentro de la subred antes de poder crear una nueva caché Redis.

Puede implementar varios tipos de recursos a un VNet clásica siempre y cuando tenga suficientes direcciones IP disponibles.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>¿Funcionan todas las características de la caché al hospedar una caché en un VNET?

Cuando la memoria caché es parte de un VNET, solo los clientes de la VNET pueden acceder a la memoria caché. Como resultado, las siguientes características de administración de la memoria caché no funcionan en este momento.

-   Redis consola - como consola Redis utiliza al cliente de cli.exe redis hospedado en máquinas virtuales que no forman parte de su VNET, no puede conectarse a la memoria caché.


## <a name="use-expressroute-with-azure-redis-cache"></a>Usar ExpressRoute con caché Redis Azure

Los clientes pueden conectar un circuito de [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) a su infraestructura de red virtual, por tanto, ampliar su red local a Azure. 

De manera predeterminada, un circuito ExpressRoute recién creado anuncia una ruta predeterminada que permite la conectividad saliente de Internet. Con esta configuración, las aplicaciones cliente son puedas conectarse a otros extremos Azure, incluida la memoria caché Redis de Azure.

Sin embargo, una configuración de cliente común es definir su propia ruta predeterminada (0.0.0.0/0) que fuerza el tráfico saliente de Internet en su lugar flujo local. Este flujo de tráfico siempre saltos conectividad con Azure Redis caché porque el tráfico saliente está bloqueado en modo local o NAT haría con un conjunto de direcciones que ya no funcionan con distintos extremos de Azure no reconocible.

La solución consiste en definir uno (o más) definidas por el usuario rutas (UDRs) en la subred que contiene la caché de Azure Redis. Un UDR define rutas de subred específica que se aceptará en lugar de la ruta predeterminada.

Si es posible, se recomienda usar la siguiente configuración:

- La configuración de ExpressRoute anuncia 0.0.0.0/0 y predeterminada forzar establece un túnel todo el tráfico saliente local.
- La UDR aplicado a la subred que contiene la caché de Azure Redis define 0.0.0.0/0 con un tipo de salto siguiente de Internet (un ejemplo de esto es más abajo en este artículo).

El efecto combinado de estos pasos es que el nivel de subred UDR tiene prioridad sobre el ExpressRoute forzado túnel, lo que garantiza el acceso a Internet de la memoria caché Redis de Azure.

Aunque se conecta a una instancia de Azure Redis caché desde en local aplicación mediante ExpressRoute no es un uso típico escenario debido a problemas de rendimiento (para mejor rendimiento caché Redis de Azure clientes deben estar en la misma región como la caché de Azure Redis), en este escenario que la ruta de acceso de red saliente no puede viajar a través de los servidores proxy corporativos internos ni puede forzar túnel a local. Hacerlo cambia la dirección NAT eficaz de tráfico de red saliente de la memoria caché Redis de Azure. Cambiar la dirección NAT de una caché de Azure Redis tráfico de red saliente de la instancia provoca errores de conectividad a muchos de los extremos enumerados anteriormente. El resultado es intentos de creación de Azure Redis caché.

**Importante:**  Las rutas definidas en una UDR **debe** ser específicas tienen prioridad sobre las rutas anunciadas por la configuración de ExpressRoute. En el ejemplo siguiente se usa el intervalo de direcciones 0.0.0.0/0 amplia y así puede accidentalmente reemplazar por los anuncios de ruta con intervalos de direcciones más específicas.

**Muy importante:**  Caché de Azure Redis no es compatible con las configuraciones de ExpressRoute que **incorrectamente entre-anunciar rutas de la ruta de interconexión pública a la ruta de acceso de interconexión privada**. Configuraciones de ExpressRoute que tienen interconexión público configurado, recibirá anuncios de ruta de Microsoft para un amplio conjunto de intervalos de direcciones IP de Microsoft Azure. Si estos intervalos de direcciones se anuncia entre incorrectamente en la ruta de acceso de interconexión privada, el resultado final es que todos los paquetes de red salientes de subred de la instancia de caché de Azure Redis túnel forzar incorrectamente a infraestructura de red local del cliente. Este flujo de red saltos de Azure Redis caché. La solución para este problema es detener rutas de publicidad cruzado desde la ruta de acceso de interconexión público a la ruta de acceso de interconexión privada.

Información general acerca de las rutas definidas por el usuario está disponible en esta [información general](../virtual-network/virtual-networks-udr-overview.md). 

Para obtener más información sobre ExpressRoute, vea [información general técnica de ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo usar las características premium de caché más.

-   [Introducción al nivel Premium caché Redis de Azure](cache-premium-tier-intro.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

