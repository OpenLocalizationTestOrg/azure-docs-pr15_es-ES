<properties
    pageTitle="Supervisar el rendimiento para aplicaciones web móviles con análisis programador | Microsoft Azure"
    description="Rendimiento de la aplicación y la supervisión de uso para desarrolladores de la aplicación móvil. , escritorio, servicio web y aplicaciones de back-end con HockeyApp y perspectivas de aplicación."
    authors="alancameronwills"
    services="application-insights"
    documentationCenter=""
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="awills"/>

# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Análisis de dispositivos móvil con HockeyApp y perspectivas de aplicación

Supervisar el rendimiento y el uso de su prueba beta e implementar aplicaciones móviles y de escritorio con [HockeyApp](https://hockeyapp.net/). Supervisar las aplicaciones de servicio y back-end de web auxiliares con [Recomendaciones de aplicación de Visual Studio](app-insights-overview.md). Analizar datos desde el cliente y el servidor de aplicaciones en el análisis y mostrar los gráficos junto con los demás en un panel de Azure.

Microsoft Developer Analytics ofrece dos soluciones para la supervisión de rendimiento de aplicación:

* **HockeyApp para móviles** y aplicaciones de escritorio.
 * Distribuir versiones de prueba a los usuarios seleccionados.
 * Bloquee el análisis.
 * Telemetría personalizado para el análisis de uso.
* Aplicaciones de **Perspectivas de aplicación para sitios web** y servicios de back-end.
 * Métricas de rendimiento y el uso y alertas.
 * Creación de informes de excepción y el seguimiento de diagnóstico.
 * Búsqueda de diagnóstico con vínculos de telemetría de filtrado y relacionados.

Ambas soluciones ofrecen:

 * **[Lenguaje de consulta analítica](app-insights-analytics.md)** eficaz de diagnóstico y análisis.
 * **[Exportar datos](app-insights-export-telemetry.md)** a su propio almacenamiento.
 * Mostrar **panel integrada** de tablas y gráficos analíticos.

## <a name="monitor-your-app-components"></a>Supervisar los componentes de aplicación

Siga las instrucciones de estas páginas para instalar el SDK en el código e iniciar la aplicación de seguimiento.

### <a name="web-apps---application-insights"></a>Aplicaciones Web - perspectivas de aplicación

* [Aplicación web ASP.NET](app-insights-asp-net.md) 
* [Aplicación web de Java](app-insights-java-get-started.md)
* [Aplicación web de Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Servicios de nube de Azure](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>Aplicaciones móviles - HockeyApp

* [aplicación iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Aplicación de Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Aplicación Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Aplicación de Windows universal](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Aplicación de Windows Phone 8 y 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation aplicación](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Para aplicaciones de escritorio de Windows, se recomienda HockeyApp. Pero también puede [Enviar telemetría desde una aplicación de Windows de impresiones de aplicación](app-insights-windows-desktop.md). Desea hacerlo para experimentar con las perspectivas de aplicación.


## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Análisis y exportación de telemetría HockeyApp

Puede investigar HockeyApp personalizados e inicie sesión con las características de análisis y exportar continuo de perspectivas de aplicación mediante la [configuración de un puente](app-insights-hockeyapp-bridge-app.md)de telemetría.




