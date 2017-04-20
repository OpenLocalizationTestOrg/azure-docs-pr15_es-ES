<properties
    pageTitle="Usar servicios móviles, ¿cómo ayuda aplicación de servicio?"
    description="Obtenga información sobre qué ventajas aporta servicio de aplicación a los proyectos de servicios móviles existentes."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>se usa servicios para dispositivos móviles, ¿cómo ayuda aplicación de servicio?

##<a name="overview"></a>Información general
El servicio móvil de existente es seguro y permanecerá compatible. Sin embargo, hay número de ventajas de la *Aplicación de servicio de Azure* plataforma proporciona para su aplicación móvil que no están disponibles en la actualidad con servicios de móvil:

- Oferta más sencilla, más fácil y más eficaces para las aplicaciones que incluyen web y clientes móviles
- Nuevas características de host incluidas las tareas de Web, CNAME personalizado, mejor supervisión
- Listo para usar integración con el Administrador de tráfico
- Conectividad a los recursos locales y VPN con VNet además de conexiones híbrido
- Supervisión, alertas y solución de problemas para la aplicación con NewRelic o AppInsights
- Más rica gama de subyacente calcular recursos y precios
- Autoescala integrados, equilibrio de carga y la supervisión de rendimiento.
- Integrado provisional, capacidades de copia de seguridad, volver y realizar pruebas de producción

## <a name="new-hosting-features"></a>Nuevas características de hospedaje
En el *Servicio de aplicación de Azure* la *Aplicación móvil* de código de back-end se ejecuta en el mismo contenedor como Web App y API App. Como, por ejemplo, puede aprovechar todas las características de este contenedor, incluidos los que no están actualmente en los servicios de Mobile:

- Agregar lógica de back-end ejecución continua a través de trabajos de Web
- Asegúrese de que siempre se ejecuta el código de back-end
- Utilizar CNAME personalizado para proporcionar nombres descriptivos y estables en los extremos de móvil back-end
- Geo escala de la aplicación con el Administrador de tráfico
- Se incluyen las bibliotecas y paquetes que desee.
- (Para .NET) Aproveche cualquier característica de ASP.NET, incluidos MVC
- (Para Node.js) Aproveche cualquier biblioteca de JavaScript puro del ecosistema nodo, incluidas las bibliotecas MVC comunes.

##<a name="access-on-premises-data-using-vnet"></a>Acceso a datos con VNet local
Con los servicios Mobile hoy puede ya usar híbrido conexiones a los recursos de acceso local. Sin embargo, hay situaciones donde se prefiere una solución VPN. Con el *Servicio de aplicación de Azure* puede usar Azure VNet para el código de back-end de la aplicación móvil.

##<a name="use-your-favorite-backend-language"></a>Usar el idioma de favoritos de back-end
*Servicio de aplicación de Azure* ofrece más amplia y más rica compatibilidad con plataformas ASP.NET y Node.js, incluido el acceso a los tiempos de ejecución más reciente.

##<a name="set-up-automatic-scale"></a>Configurar escala automático
Con los servicios de Mobile, todas las instancias de su código de back-end se ejecutan en máquinas virtuales pequeña. *Servicio de aplicación de Azure* le permite seleccionar el tamaño de las máquinas virtuales de un conjunto de opciones mucho más rico. Puede escalar rápidamente vertical u horizontal para controlar cualquier carga de cliente entrantes, en función de varias métricas de rendimiento.

##<a name="be-in-the-know"></a>En la "información"
Ante los problemas en tiempo real con la supervisión y alertas para notificar automáticamente a usted y su equipo. Integrar funciones analíticas avanzadas de aplicación y funcionalidad de supervisión de nuevo Relic y AppInsights para obtener una visión incluso más rica en el rendimiento de la aplicación móvil. Con el *Servicio de aplicación de Azure* ahora puede configurar alertas basadas en gran variedad de indicadores de rendimiento, mediante programación y a través del Portal de Azure.

##<a name="keep-your-assets-safe"></a>Proteger los activos
Automáticamente una copia de su back-end y la base de datos. Los datos y el código es seguros de desastre y restaurar fácilmente, lo que le permite dirigir su negocio con confianza.

##<a name="ready-stage-go"></a>¿Está listo, región, vaya!
Con el *Servicio de aplicación de Azure* ahora se pueden crear varias pruebas de privado y entornos de las aplicaciones móviles de ensayo. Usarlos para realizar pruebas antes de implementar. Intercambiar a producción sin tiempo de inactividad. Aplicaciones Web están precargadas, garantizar la mejor experiencia de cliente.

Puede empezar aprovechar la *Aplicación de servicio* del servicio móvil existente siguiendo este [tutorial](app-service-mobile-migrating-from-mobile-services.md).

