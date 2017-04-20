<properties
    pageTitle="¿Qué aplicaciones móviles"
    description="Obtenga información sobre qué ventajas aporta aplicación de servicio a sus aplicaciones móviles de empresa."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>¿Qué es aplicaciones Mobile?

Servicio de aplicaciones de Azure es una totalmente administrado [plataforma como servicio](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) para desarrolladores profesionales que recupera un amplio conjunto de funciones en la web, móvil y escenarios de integración. *Aplicaciones móviles* en *Servicio de la aplicación de Azure* ofrecen una plataforma de desarrollo de la aplicación móvil de alta escalabilidad, globalmente disponible para los desarrolladores empresariales e integradores que recupera un amplio conjunto de capacidades a los desarrolladores de móviles.

![Aplicaciones móviles](./media/app-service-mobile-value-prop/overview.png)

##<a name="why-mobile-apps"></a>¿Por qué aplicaciones móviles?
*Aplicaciones móviles* en *Servicio de la aplicación de Azure* ofrece una plataforma de desarrollo de aplicaciones móviles de alta escalabilidad, disponibles globalmente para programadores de empresa e integradores que recupera un amplio conjunto de capacidades a los desarrolladores de móviles. Con aplicaciones móviles, puede:

- **Generar nativa y cruzada plataforma aplicaciones** - si desea crear nativa iOS, Android y Windows aplicaciones o Xamarin o Cordova (Phonegap) entre plataformas aplicaciones, puede aprovechar las ventajas de aplicación de servicio con SDK nativa.
- **Conectar a sus sistemas empresariales** - con aplicaciones de Mobile puede agregar corporativa de inicio de sesión en minutos y conectarse a su empresa local o recursos en la nube.
- **Crear aplicaciones de la lista sin conexión con la sincronización de datos** - realizar su plantilla móvil productivo mediante la creación de aplicaciones que funcionan sin conexión y usar aplicaciones móviles para sincronizar los datos en segundo plano cuando hay conectividad con cualquiera de los orígenes de datos de la empresa o SaaS APIs.
- **Notificaciones de inserción a millones en segundos** : comuníquese con sus clientes con las notificaciones de inserción de instantánea en cualquier dispositivo, personalizada para sus necesidades, se envía cuando es el momento.

## <a name="mobile-app-features"></a>Características de la aplicación móvil
Las siguientes características son importantes para el desarrollo móvil habilitado para la nube:

- **Autenticación y la autorización** - seleccionar en una lista creciente de proveedores de identidades, incluyendo Azure Active Directory para la autenticación de empresa, además de los proveedores sociales como Facebook, Google, Twitter y Account de Microsoft.  Aplicaciones de Azure Mobile proporciona un servicio de OAuth 2.0 para cada proveedor.  También puede integrar el SDK para el proveedor de identidades para funcionalidades específicas del proveedor.

  Descubra más sobre nuestras [características de autenticación].

- **Acceso a datos** - aplicaciones móviles de Azure proporciona un origen de datos de mobile descriptivo OData v3 vinculado a SQL Azure o en un servidor de SQL de local.  Este servicio se puede basar en el marco de entidades, lo que permite integrar fácilmente con otro NoSQL y proveedores de datos SQL, incluidos los proveedores de [Almacenamiento de tablas de Azure], MongoDB, [DocumentDB] y SaaS API como Office 365 y Salesforce.com.
- **Sincronización sin conexión** - hacer de nuestro SDK de cliente le facilita generar sólidas y responde aplicaciones móviles que funcionan con una conexión de datos de conjunto que se pueden sincronizar automáticamente con los datos de back-end, incluido el soporte de resolución de conflictos.

  Descubra más sobre nuestras [características de datos].

- **Notificaciones de inserción** - nuestro SDK de cliente se integre con las capacidades de registro de Hubs de notificación de Azure, lo que le permite enviar notificaciones de inserción a millones de usuarios al mismo tiempo.

  Descubra más sobre nuestras [características de notificación de inserción].

- **SDK de cliente** - le proporcionamos un conjunto completo de SDK de cliente que abarcan desarrollo nativo ([iOS], [Android] y [Windows]), desarrollo de varias plataformas ([Xamarin para iOS y Android], [Xamarin formularios]) y desarrollo de aplicaciones de híbrido ([Apache Cordova]).  Cada cliente SDK está disponible con una licencia MIT y Abrir origen.

## <a name="azure-app-service-features"></a>Características de Azure de aplicación de servicio.
Las siguientes características de la plataforma son suele ser útiles para los sitios de producción móvil.

- **Autoescala** - servicio de la aplicación le permite rápidamente escalado de entrada o salida para controlar cualquier carga de cliente entrantes. Seleccione el número y el tamaño de máquinas virtuales o Configurar escala automática para ajustar el tamaño de la aplicación móvil back-end, en función de la carga o programación manualmente.

  Descubra más sobre el [ajuste de escala automático].

- **Entornos de ensayo** : aplicación de servicio se pueden ejecutar varias versiones de su sitio, lo que permite realizar A / B probar, probar en producción como parte de un plan de DevOps más grande y local ensayo de nuevo back-end.

  Descubra más información sobre los [entornos de ensayo].

- **Implementación continua** : aplicación de servicio puede integrar con sistemas SCM común, lo que le permite implementar automáticamente una nueva versión de su servidor presionando una rama de su sistema SCM.

  Descubra más información sobre [las opciones de implementación].

- **Red virtual** : aplicación de servicio puede conectarse a recursos locales con conexiones de red, ExpressRoute o híbrido virtuales.

  Descubra más información sobre [las conexiones de híbridos], [redes virtuales]y [ExpressRoute].

- **Aislado / dedicado entornos** -servicio de la aplicación se puede ejecutar en un entorno completamente aislado y dedicado para ejecutarse de forma segura aplicaciones de servicio de la aplicación de Azure en alta escala.  Esto es ideal para cargas de trabajo que requieren muy alta escala, aislamiento o acceso seguro a redes.

  Descubra más información sobre los [Entornos de aplicación de servicio].

## <a name="getting-started"></a>Introducción ##
Para empezar con aplicaciones móviles, siga el tutorial de [Introducción] .  Esto tratan los aspectos básicos de producir un móvil back-end y el cliente de su elección, a continuación, integrar autenticación, sincronización sin conexión y las notificaciones de inserción.  Puede seguir el tutorial [Introducción] varias veces: una vez para cada aplicación de cliente.

Para obtener más información sobre las aplicaciones de Azure Mobile, revise nuestra [mapa de aprendizaje].
Para obtener más información sobre la plataforma de servicio de la aplicación de Azure, vea [Servicio de aplicación de Azure].

>[AZURE.NOTE]Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](https://tryappservice.azure.com/?appServiceName=mobile), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Servicio de aplicaciones de Azure]: ../app-service/app-service-value-prop-what-is.md
[Introducción]: app-service-mobile-ios-get-started.md
[Almacenamiento de tablas de Azure]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[características de autenticación]: ./app-service-mobile-auth.md
[características de datos]: ./app-service-mobile-offline-data-sync.md
[características de notificación de inserción]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin para iOS y Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Formularios de Xamarin]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[ajuste de escala automático]: ../app-service-web/web-sites-scale.md
[entornos de ensayo]: ../app-service-web/web-sites-staged-publishing.md
[Opciones de implementación]: ../app-service-web/web-sites-deploy.md
[conexiones de híbrido]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[redes virtuales]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service-web/app-service-app-service-environment-network-configuration-expressroute.md
[Entornos de servicio de aplicaciones]: ../app-service-web/app-service-app-service-environment-intro.md
[mapa de aprendizaje]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/
