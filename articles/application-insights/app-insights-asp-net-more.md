<properties 
    pageTitle="Sacar más partido a aplicación perspectivas | Microsoft Azure" 
    description="Después de la introducción a la información de la aplicación, le presentamos un resumen de las características que puede explorar." 
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
    ms.date="10/27/2016" 
    ms.author="awills"/>

# <a name="more-telemetry-from-application-insights"></a>Más de telemetría de perspectivas de aplicación

Una vez haya [había agregado perspectivas de aplicación en el código ASP.NET](app-insights-asp-net.md), hay algunas cosas que puede hacer para obtener telemetría aún más. 

## <a name="if-your-app-runs-on-your-iis-server-"></a>Si la aplicación se ejecuta en el servidor IIS...

Si la aplicación está alojada en los servidores IIS en el control, instale al Monitor de estado de aplicación perspectivas en los servidores. Si ya está instalado, no es necesario hacer nada.

1. En cada servidor web IIS, inicie sesión con credenciales de administrador.
2. Descargar y ejecutar el [Monitor de estado del instalador](http://go.microsoft.com/fwlink/?LinkId=506648).
3. En el Asistente de instalación, inicie sesión en Microsoft Azure.

No es necesario hacer nada, pero puede confirmar que la supervisión está habilitada para la aplicación.

![Extender en Azure](./media/app-insights-asp-net-more/025.png)

(También puede usar al Monitor de estado para [Habilitar la supervisión en tiempo de ejecución](app-insights-monitor-performance-live-website-now.md), incluso si no ha instrumentación sus aplicaciones en Visual Studio.)

### <a name="what-do-you-get"></a>¿Qué obtener?

Si está instalado el Monitor de estado de los equipos servidor, obtendrá algunas telemetría adicional:

* Telemetría de dependencia (SQL llamadas y resto realizados por la aplicación) para las aplicaciones de .NET 4.5. (Para las versiones posteriores de .NET, Monitor de estado no es necesario para telemetría dependencia.) 
* Seguimiento de la pila de excepción mostrar más detalles.
* Contadores de rendimiento. En información de la aplicación, estos contadores aparecen en el módulo de servidores. 

![Extender en Azure](./media/app-insights-asp-net-more/070.png)

Para ver más o menos contadores, [Edite los gráficos](app-insights-metrics-explorer.md). Si el contador de rendimiento que desea no está en el conjunto disponible, puede [agregarla al conjunto recopilado por el módulo de contador de rendimiento](app-insights-performance-counters.md).

## <a name="if-its-an-azure-web-app-"></a>Si se trata de una aplicación web de Azure...

Si la aplicación se ejecuta como una aplicación web de Azure, vaya a panel de control de Azure de la aplicación o VM y abra el módulo de aplicación perspectivas. 

### <a name="what-do-you-get"></a>¿Qué obtener?

* Seguimiento de la pila de excepción mostrar más detalles.
* Telemetría de dependencia (SQL llamadas y resto realizados por la aplicación) para las aplicaciones de .NET 4.5. (Para las versiones posteriores de. NET, la extensión no es necesaria para telemetría dependencia.) 

![Extender en Azure](./media/app-insights-asp-net-more/080.png)

(También puede usar este método para [habilitar en tiempo de ejecución de supervisión de rendimiento](app-insights-monitor-performance-live-website-now.md), incluso si no ha instrumentación su aplicación en Visual Studio.)

## <a name="client-side-monitoring"></a>Supervisión de cliente

Ha instalado el SDK que envía los datos de telemetría desde el servidor (back-end) de la aplicación. Ahora puede agregar la supervisión de cliente. Esto le ofrece datos en los usuarios, sesiones, vistas de la página y cualquier excepción o bloqueos que se producen en el explorador. También podrá escribir su propio código para realizar un seguimiento de cómo funcionan los usuarios con la aplicación, hasta el nivel detallado de clics y pulsaciones de teclas.

Agregar el fragmento de código de JavaScript perspectivas de aplicación a cada página web, obtener telemetría de los exploradores de cliente.

1. En Azure, abra el recurso de información de la aplicación de la aplicación.
2. Abra introducción, Monitor cliente y copie el fragmento de código.
3. Pegue la consulta para que aparezca en el encabezado de cada página web, normalmente que puede hacerlo por pegarla en el patrón de diseño de página.

![Extender en Azure](./media/app-insights-asp-net-more/100.png)

Observe que el código contiene la clave de instrumentación que identifica el recurso de aplicación.

### <a name="what-do-you-get"></a>¿Qué obtener?

* Puede escribir JavaScript para enviar [telemetría personalizada de las páginas web](app-insights-api-custom-events-metrics.md), por ejemplo, para realizar un seguimiento de clics.
* En [análisis](app-insights-analytics.md), datos de `pageViews` y datos de AJAX en `dependencies`. 
* [Rendimiento del cliente y datos de uso](app-insights-javascript.md) en el módulo de los exploradores.

![Extender en Azure](./media/app-insights-asp-net-more/090.png)


[Más información sobre la página web de seguimiento.](app-insights-web-track-usage.md)



## <a name="track-application-version"></a>Versión de la aplicación de control

Asegúrese de que `buildinfo.config` generados por el proceso de MSBuild. En el archivo, agregue:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Cuando tiene la información de compilación, el módulo de información de la aplicación web agrega **versión de la aplicación** como una propiedad para cada elemento de telemetría. Que le permite filtrar por versión al realizar [búsquedas de diagnósticos](app-insights-diagnostic-search.md) o al [Explorar métricas](app-insights-metrics-explorer.md). 

Sin embargo, observe que el número de versión de compilación genera solo MS Build, no por la compilación de developer en Visual Studio.


## <a name="availability-web-tests"></a>Pruebas de web de disponibilidad

Enviar a la aplicación web de solicitudes de HTTP a intervalos regulares de todo el mundo. Le avisará si la respuesta es lenta o poco confiables.

En el recurso de información de la aplicación de la aplicación, haga clic en el mosaico de disponibilidad para agregar, editar y ver las pruebas web.

Puede agregar varias pruebas que se ejecutan en varias ubicaciones.

![Extender en Azure](./media/app-insights-asp-net-more/110.png)

[Aprende más](app-insights-monitor-web-app-availability.md)

## <a name="custom-telemetry-and-logging"></a>Registro y telemetría personalizado

Los paquetes de aplicación perspectivas que haya agregado a su código proporcionan una API que puede llamar desde la aplicación.

* [Generar sus propios eventos y mediciones](app-insights-api-custom-events-metrics.md), por ejemplo, para contar eventos de negocio o supervisar el rendimiento.
* [Captura de seguimientos de registro](app-insights-asp-net-trace-logs.md) de Log4Net, NLog o System.Diagnostics.Trace.
* [Filtro, modificar, o aumentar](app-insights-api-filtering-sampling.md) la telemetría estándar enviada desde su aplicación escribiendo procesadores de telemetría. 


## <a name="powerful-analysis-and-presentation"></a>Presentación y análisis eficaces

Hay muchas formas de explorar los datos. Si ha iniciado recientemente con recomendaciones de aplicación, consulte estos artículos:

||
|---|---
|[**Búsqueda de diagnóstico datos de ejemplo**](app-insights-visual-studio.md)<br/>Buscar y filtrar eventos como solicitudes, excepciones, llamadas de dependencia, seguimientos de registro y vistas de la página. En Visual Studio, ir al código de seguimiento de la pila.|![Visual studio](./media/app-insights-asp-net-more/61.png)
|[**Explorador de métricas para datos agregados**](app-insights-metrics-explorer.md)<br/>Explorar, filtro y segmento agregado datos como tasas de solicitudes de errores y excepciones; tiempos de respuesta, tiempos de carga.|![Visual studio](./media/app-insights-asp-net-more/060.png)
|[**Paneles**](app-insights-dashboards.md#dashboards)<br/>Mezclar datos de varios recursos y compartir con otros usuarios. Ideal para aplicaciones de varios componentes y para mostrar continuo en la sala de reuniones.  |![Ejemplo de paneles](./media/app-insights-asp-net-more/62.png)
|[**Secuencia de métricas directo**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>Cuando se implementa una nueva compilación, vea estos indicadores de rendimiento casi en tiempo real para asegurarse de que todo funciona según lo esperado.|![Ejemplo de análisis](./media/app-insights-asp-net-more/050.png)
|[**Análisis**](app-insights-analytics.md)<br/>Responder a preguntas difíciles sobre el rendimiento y el uso de la aplicación usando este lenguaje de consulta eficaces.|![Ejemplo de análisis](./media/app-insights-asp-net-more/010.png)
|[**Alertas automáticas y manuales**](app-insights-alerts.md)<br/>Alertas automáticas adaptan a patrones normal de la aplicación de telemetría y desencadenador cuando hay algo fuera de la trama habitual. También puede establecer alertas en particulares niveles de métricas personalizados o estándares.|![Ejemplo de alerta](./media/app-insights-asp-net-more/020.png)

## <a name="data-management"></a>Administración de datos

|||
|---|---|
|[**Exportar continuo**](app-insights-export-telemetry.md)<br/>Copie todas su telemetría en almacenamiento para que pueda analizar su propia manera.|
|**API de acceso a datos**<br/>Próximamente.|
|[**Muestreo**](app-insights-sampling.md)<br/>Reduce la velocidad de datos y puede mantenerse dentro del límite de su nivel de precios.|![Mosaico de muestreo](./media/app-insights-asp-net-more/030.png)