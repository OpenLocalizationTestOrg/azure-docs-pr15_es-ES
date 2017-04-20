<properties 
    pageTitle="Entorno de servicio de la aplicación | Microsoft Azure" 
    description="¿Qué es un entorno de servicio de la aplicación de Azure? Introducción a la aplicación de servicio de entorno." 
    keywords="entorno de servicio de aplicación de Azure, una red virtual, proteger redes"
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

# <a name="app-service-environment-documentation"></a>Documentación del entorno de servicio de aplicación

Un entorno de servicio de aplicación es un [Premium] [ PremiumTier] opción de plan de servicio de la aplicación de Azure que proporciona un entorno completamente aislado y dedicado de ejecución de forma segura aplicaciones de servicio de la aplicación de Azure en alta escala, incluidas las [Aplicaciones Web]de servicio[WebApps], [Aplicaciones Mobile][MobileApps]y las [Aplicaciones de la API][APIApps].  

Entornos de servicio de aplicación son ideales para cargas de trabajo que requieren:

- Muy alta escala
- Aislamiento y acceso seguro a redes

Los clientes pueden crear varios entornos de servicio de aplicación dentro de una única región de Azure, así como a través de varias áreas de Azure.  De esta forma ideal para escalar horizontalmente niveles de la aplicación de menos de estado en apoyo altas cargas de trabajo RPS entornos de aplicación de servicio.

Entornos de servicio de aplicaciones están aislados solo las aplicaciones de un cliente en ejecución y siempre se implementan en una red virtual.  Los clientes tienen control más preciso sobre ambos tráfico de red entrante y saliente aplicación mediante [grupos de seguridad de red][NetworkSecurityGroups].  Aplicaciones también pueden establecer conexiones seguras alta velocidad redes virtual a los recursos corporativos local.

Aplicaciones a menudo necesitan tener acceso a recursos de la empresa como bases de datos internos y servicios web.  Aplicaciones que se ejecutan en entornos de servicio de aplicación pueden tener acceso a los recursos accesibles a través de [Sitio a sitio] [ SiteToSite] VPN y [Azure ExpressRoute] [ ExpressRoute] conexiones.

* [¿Qué es un entorno de servicio de la aplicación?](../app-service-web/app-service-app-service-environment-intro.md)
* [Crear un entorno de servicio de aplicaciones](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Creación de aplicaciones en un entorno de servicio de aplicaciones](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Crear y usar un equilibrador de carga interno con entornos de servicio de aplicaciones](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configurar un entorno de servicio de aplicaciones](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Escala de aplicaciones en un entorno de servicio de aplicaciones](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Seguridad de redes y arquitectura](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Cómo de

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## <a name="videos"></a>Vídeos
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
