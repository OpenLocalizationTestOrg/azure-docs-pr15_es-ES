<properties 
    pageTitle="Información general de arquitectura de red de entornos de servicio de aplicaciones" 
    description="Introducción a la arquitectura de red topología ofApp entornos de servicio." 
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

# <a name="network-architecture-overview-of-app-service-environments"></a>Información general de arquitectura de red de entornos de servicio de aplicaciones

## <a name="introduction"></a>Introducción ##
Entornos de servicio de aplicación siempre se crean en una subred de una [red virtual] [ virtualnetwork] -aplicaciones que se ejecute en un entorno de servicio de la aplicación puedan comunicarse con extremos privadas ubicados en la misma topología de red virtual.  Puesto que los clientes pueden bloquear partes de su infraestructura de red virtual, es importante comprender los tipos de flujos de comunicación de red que se producen con un entorno de servicio de aplicación.

## <a name="general-network-flow"></a>Flujo de red general ##
 
Cuando un entorno de servicio de la aplicación (ASE) utiliza una dirección IP virtual (VIP) pública de aplicaciones, todo el tráfico entrante llega en ese VIP público.  Esto incluye el tráfico HTTP y HTTPS de aplicaciones, así como otra tráfico para FTP, la funcionalidad de depuración remota y las operaciones de administración de Azure.  Para una lista completa de los puertos específicos (obligatorios y opcionales) que están disponibles en la dirección VIP pública, vea el artículo sobre [cómo controlar el tráfico de] [ controllinginboundtraffic] en un entorno de servicio de aplicación. 

Entornos de servicio de aplicación también admiten la ejecución de aplicaciones que se enlazan solo a una dirección de una red virtual interna, también conocida como una dirección ILB (equilibrador de carga interno).  En un ILB llegan a tráfico de ASE, HTTP y HTTPS habilitado para aplicaciones, así como llamadas de depuración remotas, en la dirección ILB.  Configuraciones de ILB ASE más comunes, el tráfico FTP o FTPS llegarán también en la dirección ILB.  Sin embargo las operaciones de administración de Azure aún fluirá a puertos 454/455 en la dirección VIP pública de una ILB habilitado ASE.

El diagrama siguiente muestra una visión general de los distintos flujos de red entrantes y salientes para un entorno de servicio de la aplicación donde las aplicaciones se enlazan a una dirección IP virtual pública:

![Flujos de red general][GeneralNetworkFlows]

Un entorno de servicio de la aplicación puedan comunicarse con una amplia variedad de extremos de cliente privado.  Por ejemplo, aplicaciones que se ejecutan en el entorno de servicio de aplicación pueden conectarse a los servidores de base de datos que se ejecutan en máquinas virtuales de IaaS en la misma topología de red virtual.

>[AZURE.IMPORTANT] Mirar el diagrama de red, se implementan los "otros calcular recursos" en una subred diferente desde el entorno de servicios de aplicación. Implementar recursos en la misma subred con la ASE bloqueará conectividad de ASE con los recursos (excepto el enrutamiento ASE de dentro de un específico). Implementar en una subred diferente en su lugar (en la misma VNET). El entorno de aplicación de servicio, a continuación, podrá conectar. No es necesaria ninguna configuración adicional.

Entornos de servicio de aplicación también comunican con DB de Sql y el almacenamiento de Azure recursos necesarios para administrar y trabajar en un entorno de servicio de aplicación.  Algunos de los recursos de Sql y el almacenamiento de un entorno de servicio de la aplicación se comunica con se encuentran en la misma región como el entorno de servicios de aplicación, mientras que otras personas se encuentran en las regiones de Azure remotas.  Como resultado, conectividad saliente a Internet siempre se requiere para un entorno de servicio de la aplicación para que funcione correctamente. 

Desde un entorno de servicio de la aplicación se implementa en una subred, grupos de seguridad de red pueden usarse para controlar el tráfico a la subred.  Para obtener más información sobre cómo controlar el tráfico a un entorno de servicio de aplicación, vea el siguiente [artículo][controllinginboundtraffic].

Para obtener más información sobre cómo permitir conectividad saliente de Internet desde un entorno de servicio de aplicación, consulte el siguiente artículo sobre cómo trabajar con [Ruta Express][ExpressRoute].  El mismo método que se describe en el artículo se aplica cuando se trabaja con conectividad de sitio a sitio y usar fuerza túnel.

## <a name="outbound-network-addresses"></a>Direcciones de red saliente ##
Cuando un entorno de servicio de la aplicación hace llamadas salientes, una dirección IP siempre está asociada a las llamadas salientes.  La dirección IP específica que se utiliza depende de si el extremo que se llama se encuentra dentro de la topología de red virtual o fuera de la topología de red virtual.

Si el extremo que se llama está **fuera** de la topología de red virtual, la dirección de salida (también conocido como la dirección NAT saliente) que se utiliza es la dirección VIP pública del entorno de aplicación de servicio.  Esta dirección se puede encontrar en la interfaz de usuario del portal para el entorno de aplicación de servicio en la hoja de propiedades.
 
![Dirección IP salientes][OutboundIPAddress]

También puede determinar esta dirección para ASEs que solo tienen una pública VIP mediante la creación de una aplicación en el entorno de servicio de la aplicación y, a continuación, realizar una *nslookup* en dirección de la aplicación. La dirección IP resultante es la dirección VIP pública, así como dirección NAT de salida del entorno de servicio de aplicación.

Si es el punto final que se llama **dentro** de la topología de red virtual, la dirección de la aplicación de llamada saliente será la dirección IP interna del recurso de cálculo individuales que utiliza la aplicación.  Sin embargo, no hay una asignación persistente de direcciones IP internas de una red virtual para las aplicaciones.  Aplicaciones pueden desplazarse a través de recursos de cálculo diferente y el grupo de recursos en un entorno de servicio de la aplicación pueden cambiar debido a las operaciones de ajuste de escala de cálculo disponible.

Sin embargo, dado que un entorno de servicio de aplicación siempre se encuentra dentro de una subred, se garantiza que la dirección IP interna de un recurso de cálculo que se ejecuta una aplicación siempre se encuentra dentro del intervalo CIDR de la subred.  Como resultado, cuando ACL poseen o grupos de seguridad de red se utilizan para proteger el acceso a otros extremos de la red virtual, el intervalo de subred que contiene el entorno de aplicación de servicio debe tener concedido acceso.

El siguiente diagrama muestra estos conceptos más detalladamente:

![Direcciones de red saliente][OutboundNetworkAddresses]

En el diagrama anterior:

- Puesto que la dirección VIP pública del entorno de servicio de aplicación es 192.23.1.2, que es la dirección IP saliente utilizada para realizar llamadas a los extremos de "Internet".
- El rango CIDR de la subred que contiene el entorno de servicios de aplicación es 10.0.1.0/26.  Otros extremos dentro de la misma infraestructura de red virtual verán llamadas desde aplicaciones como origina en algún lugar dentro de este intervalo de direcciones.

## <a name="calls-between-app-service-environments"></a>Llamadas entre entornos de servicio de aplicaciones ##
Un escenario más complejo puede ocurrir si implementar múltiples entornos de servicio de aplicación en la misma red virtual y realizar llamadas salientes de un entorno de servicio de aplicación a otro entorno de servicio de aplicación.  Estos tipos de cruzan entorno de servicio de aplicación llamadas también se tratará como llamadas "Internet".

El siguiente diagrama muestra un ejemplo de una arquitectura en capas con aplicaciones en un entorno de servicio de aplicación (por ejemplo, "Casa" web apps) al llamar a aplicaciones en un entorno de servicio de aplicación segundo (por ejemplo, interno back-end API aplicaciones no está pensadas para ser accesible desde Internet). 

![Llamadas entre entornos de servicio de aplicaciones][CallsBetweenAppServiceEnvironments] 

En el ejemplo anterior, el entorno de servicios de aplicación "ASE uno" tiene una dirección IP saliente de 192.23.1.2.  Si una aplicación que se ejecuta en este entorno de servicio de aplicación será hacer una llamada saliente para una aplicación que se ejecuta en un entorno de servicio de aplicación segunda ("ASE dos") que se encuentra en la misma red virtual, la llamada saliente considera una llamada "Internet".  Como resultado el tráfico de red que llegan a la segunda aplicación de servicio de entorno se mostrará como que provienen de 192.23.1.2 (es decir, no la subred intervalo de direcciones del entorno de servicio de aplicación primera).

Aunque las llamadas entre diferentes entornos de servicio de la aplicación se tratan como llamadas "Internet", cuando ambos entornos de servicio de la aplicación se encuentran en la misma región Azure el tráfico de red permanecerá en la red de Azure regional y no fluirá física sobre Internet público.  Como resultado puede usar un grupo de seguridad de red en la subred del entorno de servicio de aplicación segunda solo permitir llamadas entrantes desde el primer entorno de servicio de aplicación (cuya dirección IP saliente es 192.23.1.2), lo que garantiza una comunicación segura entre los entornos de aplicación de servicio.

## <a name="additional-links-and-information"></a>Información y vínculos adicionales ##
Todos los artículos y cómo-de para entornos de aplicación de servicio están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Obtener más información sobre entrante puertos usados por los entornos de aplicación de servicio y usar grupos de seguridad de red para controlar el tráfico entrante está disponible [aquí][controllinginboundtraffic].

Información sobre el uso de usuario definido rutas para conceder el acceso a Internet a entornos de aplicación de servicio está disponible en este [artículo][ExpressRoute]. 


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

