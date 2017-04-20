<properties 
    pageTitle="Uso de la supervisión y el rendimiento para aplicaciones de escritorio de Windows" 
    description="Analizar el rendimiento de la aplicación de escritorio de Windows con HockeyApp y perspectivas de aplicaciones y uso." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="awills"/>

# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Uso de la supervisión y el rendimiento en las aplicaciones de escritorio de Windows

*Información de la aplicación está en vista previa.*

[Perspectivas de aplicación de Visual Studio](app-insights-overview.md) y [HockeyApp](https://hockeyapp.net) le permiten supervisar la aplicación implementada para uso y rendimiento.

> [AZURE.IMPORTANT] Se recomienda [HockeyApp](https://hockeyapp.net) a distribuir y supervisar las aplicaciones de escritorio y dispositivos. Con HockeyApp, se puede administrar distribución, pruebas directo y comentarios de los usuarios, así como supervisar los informes de uso y bloqueo. También puede [exportar y su telemetría con el análisis de la consulta](app-insights-hockeyapp-bridge-app.md).

> Aunque telemetría puede enviarse a información de la aplicación desde una aplicación de escritorio, esto es principalmente útil para depuración y experimentación.


## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Para enviar telemetría de aplicación impresiones desde una aplicación de Windows

1. En el [portal de Azure](https://portal.azure.com), [Cree un recurso de información de la aplicación](app-insights-create-new-resource.md). Tipo de aplicación, elija la aplicación ASP.NET.
2. Realizar una copia de la clave de instrumentación. Encontrar la clave en el menú desplegable Essentials del nuevo recurso que acaba de crear. 
3. En Visual Studio, edite los paquetes de NuGet de su proyecto de la aplicación y agregue Microsoft.ApplicationInsights.WindowsServer. (O elija Microsoft.ApplicationInsights si desea que el hardware API sin los módulos de la colección de telemetría estándar.)
4. Establecer la clave de instrumentación en el código:

    `TelemetryConfiguration.Active.InstrumentationKey = "`*la clave*`";` 

    o en ApplicationInsights.config (si ha instalado uno de los paquetes de telemetría estándar):
 
    `<InstrumentationKey>`*la clave*`</InstrumentationKey>` 

    Si usa ApplicationInsights.config, asegúrese de que sus propiedades en el Explorador de soluciones están establecidos **Generar acción = contenido, copiar en el directorio de salida = copia**.
5. [Usar la API](app-insights-api-custom-events-metrics.md) para enviar telemetría.
6. Ejecute la aplicación y vea la telemetría en el recurso que creó en el Portal de Azure.

## <a name="telemetry"></a>Código de ejemplo

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Pasos siguientes

* [Crear un panel](app-insights-dashboards.md)
* [Búsqueda de diagnóstico](app-insights-diagnostic-search.md)
* [Explorar métricas](app-insights-metrics-explorer.md)
* [Escribir consultas de análisis](app-insights-analytics.md)
 
