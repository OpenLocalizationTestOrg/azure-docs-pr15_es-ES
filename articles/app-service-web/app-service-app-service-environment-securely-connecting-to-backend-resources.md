<properties 
    pageTitle="Conectar forma segura a recursos de back-end desde un entorno de servicio de aplicaciones" 
    description="Obtenga información sobre cómo conectar de forma segura a recursos de back-end de un entorno de servicio de aplicación." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="stefsch"/>   

# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Conectar forma segura a recursos de back-end desde un entorno de servicio de aplicaciones #

## <a name="overview"></a>Información general ##
Dado que siempre se crea un entorno de servicio de aplicación en **ambos** una red virtual del Administrador de recursos de Azure, **o** una implementación clásica modelo [red virtual][virtualnetwork], conexiones salientes desde un entorno de servicio de aplicación a otros recursos de back-end pueden flujo exclusivamente a través de la red virtual.  Con un cambio reciente realizado en junio de 2016, también se puede implementar ASEs en redes virtuales que utilizan intervalos de direcciones pública o espacios de direcciones de RFC1918 (es decir direcciones privadas).  

Por ejemplo, puede haber un SQL Server en un clúster de máquinas virtuales con puerto 1433 bloqueado.  El punto final pueden encuentra solo permitir acceso desde otros recursos en la misma red virtual.  

Otro ejemplo extremos confidenciales, puede ejecutar local y estar conectados a Azure a través de cualquier [Sitio a otro] [ SiteToSite] o [Azure ExpressRoute] [ ExpressRoute] conexiones.  Como resultado, solo los recursos en redes virtuales conectados al sitio a otro o ExpressRoute túneles podrán obtener acceso a los extremos local.

Para todos estos escenarios, aplicaciones que se ejecutan en un entorno de servicio de aplicación podrán conectarse a los distintos servidores y recursos de forma segura.  El tráfico saliente de aplicaciones que se ejecutan en un entorno de servicio de aplicación a extremos privados en la misma red virtual (o conectado a la misma red virtual) le flujo sólo a través de la red virtual.  El tráfico saliente extremos privados no fluirá sobre Internet público.

Una advertencia se aplica al tráfico saliente desde un entorno de servicio de aplicación a los extremos dentro de una red virtual.  Entornos de servicio de aplicación no pueden llegar a extremos de máquinas virtuales ubicados en la **misma** subred que el entorno de servicios de aplicación.  Esto normalmente no debería ser un problema siempre y cuando se implementan entornos de aplicación de servicio en una subred reservada para uso exclusivo de solo el entorno de servicios de aplicación.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS y conectividad saliente ##
En un entorno de servicio de aplicación para que funcione correctamente, se requiere acceso de salida a varios extremos. Una lista completa de los extremos externos utilizado un ASE se encuentra en la sección "Requiere conectividad de red" de este artículo de la [Configuración de red para ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Entornos de servicio de aplicación requieren una infraestructura DNS válida configurada para la red virtual.  Si por cualquier motivo se cambia la configuración de DNS después de haber creado un entorno de servicio de aplicación, los desarrolladores pueden forzar un entorno de servicio de la aplicación para reanudar la nueva configuración de DNS.  Desencadenar un reinicio de entorno graduales mediante el icono de "Reiniciar" situado en la parte superior del módulo de administración de entorno de aplicación de servicio en el portal de hará que el entorno de recoger la nueva configuración de DNS.

También se recomienda que los servidores DNS personalizados en el vnet ser configuración antelación antes de crear un entorno de servicio de aplicación.  Si se cambia la configuración de DNS de la red virtual mientras se crea un entorno de servicio de aplicación, que da al error del proceso de creación de entorno de servicio de aplicación.  En un modo similar, si existe un servidor DNS personalizado en el otro extremo de una puerta de enlace VPN y el servidor DNS es accesible o no está disponible, el proceso de creación del entorno de servicio de aplicación también funcionará.

## <a name="connecting-to-a-sql-server"></a>Conectarse a un servidor SQL
Una configuración de SQL Server comunes tiene un extremo que escucha en el puerto 1433:

![Extremo SQL Server][SqlServerEndpoint]

Existen dos métodos para restringir el tráfico a este extremo:


- [Las listas de Control de acceso de red] [ NetworkAccessControlLists] (ACL de red)

- [Grupos de seguridad de red][NetworkSecurityGroups]


## <a name="restricting-access-with-a-network-acl"></a>Restringir el acceso con una red ACL

Puerto 1433 puede protegerse con una lista de control de acceso de red.  El ejemplo siguiente cliente blancas las direcciones que provienen de dentro de una red virtual y bloquea el acceso a todos los demás clientes.

![Ejemplo de lista de Control de acceso de red][NetworkAccessControlListExample]

Las aplicaciones que se ejecuta en el entorno de aplicación de servicio en la misma red virtual como SQL Server podrán conectarse a la instancia de SQL Server con la dirección IP de **VNet interno** para la máquina virtual de SQL Server.  

La cadena de conexión de ejemplo siguiente hace referencia a SQL Server mediante su dirección IP privada.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Aunque la máquina virtual tiene también un extremo público, intentos de conexión con la dirección IP pública serán rechazados debido a la red ACL. 

## <a name="restricting-access-with-a-network-security-group"></a>Restringir el acceso con un grupo de seguridad de red
Es una alternativa para proteger el acceso a un grupo de seguridad de la red.  Grupos de seguridad de red pueden aplicarse a máquinas virtuales individuales o a una subred que contiene máquinas virtuales de Windows.

En primer lugar debe crear un grupo de seguridad de red:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir el acceso a únicamente el tráfico interno VNet es muy sencillo con un grupo de seguridad de la red.  Las reglas de forma predeterminada en un grupo de seguridad de red solo permiten el acceso desde otros clientes de red en la misma red virtual.

Bloquear el acceso a SQL Server como resultado es tan sencillo como aplicar un grupo de seguridad de la red con sus reglas de forma predeterminada en los equipos de virtual que ejecuta SQL Server o la subred que contiene los equipos virtuales.

En el ejemplo siguiente se aplica a un grupo de seguridad de la red a la subred que contengan:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
El resultado final es un conjunto de reglas de seguridad que bloquea el acceso externo, al permitir el acceso de VNet interno:

![Reglas de seguridad de red de forma predeterminada][DefaultNetworkSecurityRules]


## <a name="getting-started"></a>Introducción
Todos los artículos y cómo-de para entornos de aplicación de servicio están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Para empezar con entornos de servicio de aplicación, vea [Introducción a entorno de aplicación de servicio][IntroToAppServiceEnvironment]

Para obtener más detalles para controlar el tráfico de su entorno de servicio de aplicación, vea [controlar el tráfico a un entorno de servicio de aplicación][ControlInboundASE]

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, vea [Servicio de aplicación de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ControlInboundASE]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/ 

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
