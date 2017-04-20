<properties 
    pageTitle="Detalles de la configuración de red para trabajar con la ruta de Express" 
    description="Detalles de la configuración de red para ejecutar entornos de servicio de aplicación en las redes virtuales conexión a un circuito de ExpressRoute." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="stefsch"/>   

# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Detalles de la configuración de red para entornos de servicio de la aplicación con ExpressRoute 

## <a name="overview"></a>Información general ##
Los clientes pueden conectar un [Azure ExpressRoute] [ ExpressRoute] circuito a su infraestructura de red virtual, por tanto, ampliar su red local a Azure.  Puede crear un entorno de servicio de la aplicación en una subred de esta [red virtual] [ virtualnetwork] infraestructura.  Aplicaciones que se ejecutan en el entorno de aplicación de servicio, a continuación, pueden establecer conexiones seguras a recursos de back-end accesibles sólo a través de la conexión de ExpressRoute.  

Se puede crear un entorno de servicio de la aplicación en **ambos** una red virtual del Administrador de recursos de Azure, **o** una implementación clásica modelo de red virtual.  Con un cambio reciente realizado en junio de 2016, ahora también se puede implementar ASEs en redes virtuales que utilizan intervalos de direcciones pública o espacios de direcciones de RFC1918 (es decir direcciones privadas). 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="required-network-connectivity"></a>Conectividad de red ##
Hay requisitos de conectividad de red para entornos de servicio de aplicación que no se cumplen inicialmente en una red virtual conectada a una ExpressRoute.  Los entornos de servicio de aplicación requieren todas las opciones siguientes para que funcione correctamente:


-  Conectividad de red saliente a extremos de almacenamiento de Azure en todo el mundo en ambos puertos 80 y 443.  Esto incluye extremos ubicados en la misma región como el entorno de aplicación de servicio, así como los extremos de almacenamiento habían ubicado en **otras** áreas de Azure.  Resolver los extremos de almacenamiento de Azure en los dominios DNS siguientes: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* y *file.core.windows.net*.  
-  Conectividad de red de salida para el servicio de archivos de Azure en el puerto 445.
-  Conectividad de red saliente a extremos de base de datos Sql ubicado en la misma región como el entorno de servicios de aplicación.  Resolver los extremos de la base de datos de SQL en el siguiente dominio: *database.windows.net*.  Esto requiere acceso a los puertos 1433, 11000 11999 y 14000 a 14999.  Para obtener más detalles, consulte [este artículo sobre el uso del puerto V12 de base de datos de Sql](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
-  Conectividad de red saliente a los extremos de plano de administración de Azure (ASM y ARM extremos).  Esto incluye conectividad saliente *management.core.windows.net* y *management.azure.com*. 
-  Conectividad de red saliente *ocsp.msocsp.com*, *mscrl.microsoft.com* y *crl.microsoft.com*.  Esto es necesario para admitir la funcionalidad SSL.
-  La configuración de DNS de la red virtual debe ser capaces de resolver todos los extremos y dominios mencionadas en los puntos anteriores.  Si no se resuelve estos extremos, se producirá un error de intentos de creación del entorno de aplicación de servicio y entornos de servicio de aplicación existente se marcará como mal Estados.
-  El acceso de salida en el puerto 53 se requiere para la comunicación con los servidores DNS.
-  Si existe un servidor DNS personalizado en el otro extremo de una puerta de enlace VPN, el servidor DNS debe ser accesible desde la subred que contiene el entorno de servicios de aplicación. 
-  La ruta de acceso de red saliente no puede viajar a través de los servidores proxy corporativos internos ni puede forzar túnel a local.  Si lo hace, cambia la dirección NAT eficaces del tráfico saliente de red desde el entorno de servicios de aplicación.  Cambiar la dirección NAT del tráfico de red saliente de un entorno de servicio de aplicación, se producirán errores de conectividad a muchos de los extremos enumerados anteriormente.  El resultado intentos de creación de entorno de servicio de aplicación, así como previamente correcto entornos de servicio de aplicación se marquen como mal estado.  
-  Entrada de acceso de red a los puertos necesarios para entornos de aplicación de servicio debe tener permiso como se describe en este [artículo][requiredports].

Garantizar una infraestructura DNS válida se configuran y mantienen de la red virtual pueden cumplir los requisitos de DNS.  Si por cualquier motivo se cambia la configuración de DNS después de haber creado un entorno de servicio de aplicación, los desarrolladores pueden forzar un entorno de servicio de la aplicación para reanudar la nueva configuración de DNS.  Desencadenar un reinicio de entorno graduales mediante el icono "Reiniciar" ubicada en la parte superior del módulo de administración de entorno de aplicación de servicio en el [portal de Azure] [ NewPortal] hará que el entorno de recoger la nueva configuración de DNS.

Se pueden cumplir los requisitos de acceso de red entrante mediante la configuración de un [grupo de seguridad de red] [ NetworkSecurityGroups] de subred del entorno de servicio de aplicación para permitir el acceso necesario, como se describe en este [artículo][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Habilitar la conectividad de red saliente para un entorno de servicio de aplicaciones##
De manera predeterminada, un circuito ExpressRoute recién creado anuncia una ruta predeterminada que permite la conectividad saliente de Internet.  Con esta configuración de un entorno de servicio de aplicación podrá conectarse a otros extremos de Azure.

Sin embargo, una configuración de cliente común es definir su propia ruta predeterminada (0.0.0.0/0) que fuerza el tráfico saliente de Internet en su lugar flujo local.  Este flujo de tráfico siempre saltos entornos de servicio de aplicación porque el tráfico saliente está bloqueado en modo local o NAT haría con un conjunto de direcciones que ya no funcionan con distintos extremos de Azure no reconocible.

La solución consiste en definir rutas de definidas por el usuario uno (o más) (UDRs) en la subred que contiene el entorno de aplicación de servicio.  Un UDR define rutas de subred específica que se aceptará en lugar de la ruta predeterminada.

Si es posible, se recomienda usar la siguiente configuración:

- La configuración de ExpressRoute anuncia 0.0.0.0/0 y predeterminada forzar establece un túnel todo el tráfico saliente local.
- La UDR aplicado a la subred que contiene el entorno de servicios de aplicación define 0.0.0.0/0 con un tipo de salto siguiente de Internet (un ejemplo de esto es más abajo en este artículo).

El efecto combinado de estos pasos es que el nivel de subred UDR tendrá prioridad sobre el ExpressRoute forzado túnel, lo que garantiza el acceso a Internet desde el entorno de servicios de aplicación.

> [AZURE.IMPORTANT] Las rutas definidas en una UDR **debe** ser específicas tienen prioridad sobre las rutas anunciadas por la configuración de ExpressRoute.  El ejemplo siguiente utiliza el intervalo de direcciones 0.0.0.0/0 amplia y así puede accidentalmente reemplazar por los anuncios de ruta con intervalos de direcciones más específicas.
>
>Los entornos de servicio de aplicación no son compatibles con las configuraciones de ExpressRoute que **entre-anunciar rutas de la ruta de interconexión pública a la ruta de acceso de interconexión privada**.  Configuraciones de ExpressRoute que tienen interconexión público configurado, recibirá anuncios de ruta de Microsoft para un amplio conjunto de intervalos de direcciones IP de Microsoft Azure.  Si estos intervalos de direcciones entre anunciados en la ruta de interconexión privada, el resultado final es que todos los paquetes de red salientes de subred del entorno de servicio de aplicación será túnel forzar a infraestructura de red local del cliente.  Este flujo de red no es compatible actualmente con entornos de aplicación de servicio.  Una solución para este problema es detener rutas de publicidad cruzado desde la ruta de acceso de interconexión público a la ruta de acceso de interconexión privada.

Información general acerca de las rutas definidas por el usuario está disponible en esta [información general][UDROverview].  

Detalles sobre la creación y configuración de rutas definidas por el usuario está disponible en esta [Las guías][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Ejemplo de configuración de UDR para un entorno de servicio de aplicaciones ##

**Requisitos previos**

1. Instalar Azure Powershell desde la [página de descargas de Azure] [ AzureDownloads] (con fecha de junio de 2015 o posterior).  En herramientas de línea de comandos de"" hay un vínculo "Instalar" en "Windows Powershell" que va a instalar la última cmdlets de Powershell.

2. Se recomienda que se crea una única subred para uso exclusivo por un entorno de servicio de aplicación.  Así se garantiza que los UDRs aplicados a la subred solo abrirán el tráfico saliente para el entorno de aplicación de servicio.
3. **Importante**: implementar el entorno de servicios de aplicación hasta que **después** se siguen los siguientes pasos de configuración.  Esto garantiza que la conectividad de red saliente está disponible antes de implementar un entorno de servicio de aplicación.

**Paso 1: Crear una tabla de ruta con nombre**

El fragmento de código siguiente crea una tabla de ruta denominada "DirectInternetRouteTable" en la región Oeste nos Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Paso 2: Crear una o varias rutas en la tabla de rutas**

Deberá agregar una o varias rutas a la tabla de rutas para habilitar el acceso a Internet.  

Es el enfoque recomendado para configurar el acceso de salida a Internet definir una ruta para 0.0.0.0/0 tal como se muestra a continuación.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Recuerde que 0.0.0.0/0 es un rango de dirección amplia y así se reemplazará por intervalos de direcciones más específicas anunciadas por la ExpressRoute.  Para volver a repetir la recomendación anterior, un UDR con una ruta de 0.0.0.0/0 debe usarse junto con una configuración de ExressRoute que sólo anuncia 0.0.0.0/0 también. 

Como alternativa, puede descargar una lista completa y actualizada de intervalos CIDR usando Azure.  El archivo Xml que contiene todos los intervalos de direcciones IP de Azure está disponible desde el [Centro de descarga de Microsoft][DownloadCenterAddressRanges].  

Tenga en cuenta que estos intervalos cambian con el tiempo, así que impliquen periódicos actualización manual de las rutas definidas por el usuario para mantener sincronizados.  Además, dado que hay un límite predeterminado de 100 rutas en un único UDR, tendrá que "Resumen" los intervalos de direcciones IP de Azure para que quepa dentro del límite de 100 ruta, teniendo en cuenta que UDR definido rutas deben ser más específico que las rutas publicado por su ExpressRoute.  


**Paso 3: Asociar la tabla de ruta a la subred que contiene el entorno de servicios de aplicación**

El último paso de la configuración es asociar la tabla de ruta a la subred donde se implementará el entorno de servicios de aplicación.  El comando siguiente asocia el "DirectInternetRouteTable" a la "ASESubnet" que incluye un entorno de servicio de aplicación.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Paso 4: Últimos pasos**

Una vez que la tabla de rutas enlazada a la subred, se recomienda probar primero y confirme el efecto previsto.  Por ejemplo, implementar una máquina virtual en la subred y confirme:


- El tráfico saliente a los extremos de Azure y Azure no se mencionó anteriormente en este artículo **no** fluye hacia abajo el circuito ExpressRoute.  Es muy importante comprobar este comportamiento, ya que si el tráfico saliente de la subred es todavía está forzado túnel local, entorno de servicio de aplicación creación siempre se producirá un error. 
- Las búsquedas DNS para los extremos mencionan anteriormente son todos resolver correctamente. 

Una vez que se confirman los pasos anteriores, debe eliminar la máquina virtual porque la subred debe estar "vacía" en el momento de que crear el entorno de servicios de aplicación.
 
Continúe con la creación de un entorno de servicio de aplicación!

## <a name="getting-started"></a>Introducción
Todos los artículos y cómo-de para entornos de aplicación de servicio están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Para empezar con entornos de servicio de aplicación, vea [Introducción a entorno de aplicación de servicio][IntroToAppServiceEnvironment]

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, vea [Servicio de aplicación de Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]:  https://portal.azure.com
 

<!-- IMAGES -->
