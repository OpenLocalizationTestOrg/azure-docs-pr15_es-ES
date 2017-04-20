<properties 
    pageTitle="Aplicación perspectivas para principales de ASP.NET" 
    description="Supervisar las aplicaciones web de disponibilidad, rendimiento y uso." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="awills"/>

# <a name="application-insights-for-aspnet-core"></a>Aplicación perspectivas para principales de ASP.NET

[Perspectivas de aplicación de Visual Studio](app-insights-overview.md) permite supervisar la aplicación web de disponibilidad, rendimiento y uso. Con comentarios que obtener información sobre el rendimiento y la eficacia de la aplicación en la naturaleza, puede elegir opciones informadas acerca de la dirección del diseño de cada ciclo de vida del desarrollo de.

![Ejemplo](./media/app-insights-asp-net-core/sample.png)

Necesitará una suscripción con [Microsoft Azure](http://azure.com). Inicie sesión con una cuenta de Microsoft, es posible que tenga para Windows, XBox Live u otros servicios de nube de Microsoft. El equipo puede tener una suscripción a Azure organizativa: pida al propietario que lo agregue a través de su cuenta de Microsoft.


## <a name="getting-started"></a>Introducción

Siga la [Guía de introducción](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

## <a name="using-application-insights"></a>Usar la aplicación perspectivas

Inicie sesión en el [portal de Microsoft Azure](https://portal.azure.com) y busque el recurso creó para supervisar la aplicación.

En una ventana independiente del explorador, use la aplicación durante un tiempo. Verá los datos que aparecen en los gráficos de la información de la aplicación. (Es posible que tenga que haga clic en actualizar.) Habrá solo una cantidad pequeña de datos mientras desarrolla, pero estos gráficos realmente vienen activos al publicar su aplicación y tiene muchos usuarios. 

La página información general muestra los gráficos de rendimiento, es probable que le interese: tiempo de respuesta del servidor, tiempo de carga de página y recuentos de solicitudes de errores. Haga clic en cualquier gráfico para ver más datos y gráficos.

Vistas en el portal se dividen en dos categorías principales:

* [Explorador de métricas](app-insights-metrics-explorer.md) muestra gráficos y tablas de métricas y recuentos, como los tiempos de respuesta, tasas de error o métricas cree usted mismo con la [API](app-insights-api-custom-events-metrics.md). Filtrar y segmentar los datos por valores de propiedad para obtener una mejor descripción de la aplicación y sus usuarios.
* [Explorador de búsqueda de](app-insights-diagnostic-search.md) listas de eventos individuales, como solicitudes específicas, excepciones, registro trazas o eventos que creado con la [API](app-insights-api-custom-events-metrics.md). Filtrar y buscar en los eventos y desplazarse entre los eventos relacionados para investigar problemas.
* [Análisis](app-insights-analytics.md) le permite ejecutar consultas de tipo SQL sobre la telemetría y es una herramienta de diagnóstico y analítica eficaz.

## <a name="alerts"></a>Alertas

* Obtendrá automáticamente [las alertas de diagnósticos proactivas](app-insights-proactive-diagnostics.md) que saber sobre cambios irregulares en tasas de error y otras métricas.
* Configurar [pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) para probar su sitio Web continuamente desde ubicaciones en todo el mundo y obtener correos electrónicos tan pronto como se produce un error en las pruebas.
* Configurar [alertas métricas](app-insights-monitor-web-app-availability.md) saber si métricas como tiempos de respuesta o los tipos de excepción vaya límites aceptables fuera.

## <a name="get-more-telemetry"></a>Obtener más telemetría

* [Agregar telemetría a las páginas web](app-insights-javascript.md) para supervisar el uso de página y el rendimiento.
* [Dependencias del monitor](app-insights-dependencies.md) para ver si resto, SQL u otros recursos externos se ralenticen.
* [Usar la API](app-insights-api-custom-events-metrics.md) para enviar sus propios eventos y métricas para obtener una vista más detallada de rendimiento y el uso de la aplicación.
* [Pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) comprobar la aplicación constantemente de todo el mundo. 


## <a name="open-source"></a>Abrir origen

[Leer y contribuir al código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


