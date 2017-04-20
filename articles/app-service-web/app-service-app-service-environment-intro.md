<properties 
    pageTitle="Introducción al entorno de servicio de aplicaciones" 
    description="Obtenga información sobre la característica de entorno de aplicación de servicio que proporciona las unidades de escala seguro, unido VNet dedicado para ejecutar todas las aplicaciones." 
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

# <a name="introduction-to-app-service-environment"></a>Introducción al entorno de servicio de aplicaciones

## <a name="overview"></a>Información general ##
Un entorno de servicio de aplicación es un [Premium] [ PremiumTier] opción de plan de servicio de la aplicación de Azure que proporciona un entorno completamente aislado y dedicado de ejecución de forma segura aplicaciones de servicio de la aplicación de Azure en alta escala, incluidas las [Aplicaciones Web]de servicio[WebApps], [Aplicaciones Mobile][MobileApps]y las [Aplicaciones de la API][APIApps].  

Entornos de servicio de aplicación son ideales para cargas de trabajo que requieren:

- Muy alta escala
- Aislamiento y acceso seguro a redes

Los clientes pueden crear varios entornos de servicio de aplicación dentro de una única región de Azure, así como a través de varias áreas de Azure.  De esta forma ideal para escalar horizontalmente niveles de la aplicación de menos de estado en apoyo altas cargas de trabajo RPS entornos de aplicación de servicio.

Entornos de servicio de aplicaciones están aislados solo las aplicaciones de un cliente en ejecución y siempre se implementan en una red virtual.  Los clientes tienen control más preciso sobre el tráfico de red de ambos aplicación entrante y saliente y aplicaciones pueden establecer conexiones seguras alta velocidad a través de redes virtuales a los recursos corporativos local.

Todos los artículos y cómo-acerca de para de aplicación de servicio entornos están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Para obtener información general sobre cómo entornos de servicio de aplicación Habilitar alta escala y seguro, acceso de red, vea el [Análisis detallado de AzureCon] [ AzureConDeepDive] en entornos de servicio de aplicación!

Para un análisis más profundo en escala horizontalmente utilizando varios entornos de servicio de aplicación consulte el artículo sobre cómo configurar un [espacio de la aplicación distribuido geo][GeodistributedAppFootprint].

Para ver cómo se haya configurado la arquitectura de seguridad que se muestra en el análisis detallado AzureCon, vea el artículo sobre cómo implementar una [arquitectura de seguridad de capas](app-service-app-service-environment-layered-security.md) con entornos de aplicación de servicio.

Aplicaciones que se ejecutan en entornos de servicio de aplicación pueden acceder sus puertas de enlace ascendente dispositivos como el Firewall de aplicación web (WAFS).  El artículo sobre [cómo configurar un WAFS para entornos de aplicación de servicio](app-service-app-service-environment-web-application-firewall.md) cubre este escenario. 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="dedicated-compute-resources"></a>Recursos de cálculo dedicada ##
Todos los recursos de cálculo en un entorno de servicio de la aplicación están dedicadas exclusivamente a una sola suscripción y un entorno de servicio de la aplicación se pueden configurar con recursos de cálculo hasta cincuenta (50) para uso exclusivo por una aplicación.

Un entorno de servicio de la aplicación se compone de un grupo de recursos de cálculo front-end, así como grupos de recursos de cálculo de trabajo de uno a tres. 

El grupo de servidores front-end contiene recursos de cálculo responsable de terminación SSL como Equilibrio de carga también automática de solicitudes de aplicación dentro de un entorno de servicio de aplicación. 

Cada grupo de trabajo contiene recursos de cálculo asignados a [Planes de servicio de aplicación][AppServicePlan], que a su vez contener uno o más aplicaciones de servicio de aplicaciones de Azure.  Dado que puede haber hasta tres grupos de trabajo diferente en un entorno de servicio de la aplicación, tiene la flexibilidad para elegir los recursos de cálculo diferentes para cada grupo de trabajo.  

Por ejemplo, permite crear un grupo de trabajo con menos potentes recursos de cálculo para planes de servicio de aplicación está pensado para las aplicaciones de desarrollo o de prueba.  Un grupo de trabajo de segundo (o incluso tercer) podría usar más potentes recursos de cálculo está pensados para ejecutar aplicaciones de producción de planes de servicio de aplicación.

Para obtener más detalles sobre la cantidad de recursos de cálculo disponibles para los grupos de front-end y de trabajo, consulte [cómo configurar un entorno de servicio de la aplicación][HowToConfigureanAppServiceEnvironment].  

Para obtener detalles sobre la disponible, calcular tamaños de recursos compatibles en un entorno de servicio de aplicación, consulte la [Aplicación de servicio de precios] [ AppServicePricing] página y revise las opciones disponibles para los entornos de servicio de aplicación en la prima nivel de precios.

## <a name="virtual-network-support"></a>Soporte técnico de red virtual ##
Se puede crear un entorno de servicio de la aplicación en **ambos** una red virtual del Administrador de recursos de Azure, **o** una implementación clásica modelo de red virtual ([más información en redes virtuales][MoreInfoOnVirtualNetworks]).  Dado que un entorno de servicio de aplicación siempre existe en una red virtual y con mayor precisión dentro de una subred de una red virtual, puede aprovechar las características de seguridad de redes virtuales para controlar las comunicaciones de red entrantes y salientes.  

Un entorno de servicio de aplicación pueden ser Internet opuestas con una dirección IP pública, o interno opuestas con solo una dirección del equilibrador de carga interno (ILB) de Azure.

Puede usar [grupos de seguridad de red] [ NetworkSecurityGroups] para restringir las comunicaciones de red entrantes a la subred donde reside un entorno de servicio de aplicación.  Esto le permite ejecutar aplicaciones detrás de los dispositivos ascendentes y servicios como servidores de seguridad de aplicaciones web y los proveedores de SaaS de red.

Aplicaciones también a menudo necesitan tener acceso a recursos de la empresa como bases de datos internos y servicios web.  Un enfoque común es realizar estos extremos disponible solo para el tráfico de red interna que fluye dentro de una red virtual Azure.  Una vez un entorno de servicio de la aplicación se ha unido a la misma red virtual que los servicios internos, aplicaciones que se ejecutan en el entorno pueden tener acceso a ellos, incluidos los extremos accesibles a través de [Sitio a sitio] [ SiteToSite] y [Azure ExpressRoute] [ ExpressRoute] conexiones.

Para obtener más detalles sobre cómo funcionan los entornos de aplicación de servicio con redes virtuales y local con los artículos siguientes en la [Arquitectura de red][NetworkArchitectureOverview], [Controlar el tráfico entrante][ControllingInboundTraffic]y la [Conexión de forma segura a back-ends][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introducción

Para empezar con la aplicación de servicio entornos, consulte [Cómo crear una aplicación de servicio entorno a][HowToCreateAnAppServiceEnvironment]

Todos los artículos y cómo-de para entornos de aplicación de servicio están disponibles en el [archivo Léame para entornos de aplicaciones de servicio](../app-service/app-service-app-service-environments-readme.md).

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, vea [Servicio de aplicación de Azure][AzureAppService].

Para obtener información general de la arquitectura de red del entorno de servicio de aplicación, vea la [Información general sobre la arquitectura de red] [ NetworkArchitectureOverview] artículo.

Para obtener información detallada sobre el uso de un entorno de servicio de la aplicación con ExpressRoute, consulte el siguiente artículo en [entornos de servicio de aplicación y ruta Express][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->

 
