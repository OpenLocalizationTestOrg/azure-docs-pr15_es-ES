<properties
    pageTitle="Usar Powershell para establecer alertas en perspectivas de aplicación"
    description="Automatizar la configuración de aplicación perspectivas para obtener los correos electrónicos de los cambios de métrica."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/19/2016"
    ms.author="awills"/>

# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Usar PowerShell para establecer alertas en perspectivas de aplicación

Puede automatizar la configuración de [alertas](app-insights-alerts.md) en [Perspectivas de aplicación de Visual Studio](app-insights-overview.md).

Además, puede [establecer webhooks automatizar la respuesta a una alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="one-time-setup"></a>Configuración de una sola vez

Si todavía no lo ha usado PowerShell con la suscripción antes de Azure:

Instale el módulo de Powershell de Azure en el equipo donde desea ejecutar las secuencias de comandos.

 * Instalar [Instalador de plataforma Web de Microsoft (v5 o superior)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Para instalar Microsoft Azure Powershell


## <a name="connect-to-azure"></a>Conectarse a Azure

Inicie Azure PowerShell y [conectarse a su suscripción](../powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## <a name="get-alerts"></a>Recibir alertas

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Agregar alerta


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



## <a name="example-1"></a>Ejemplo 1

Enviarme un correo electrónico si la respuesta del servidor para las solicitudes HTTP, calcula el promedio de más de cinco minutos, es menor que 1 segundo. Mis recursos perspectivas de aplicación se denomina IceCreamWebApp y se encuentra en el grupo de recursos Fabrikam. Soy el propietario de la suscripción de Azure.

El GUID es el identificador de suscripción (no la clave de instrumentación de la aplicación).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Ejemplo 2

Tengo una aplicación en la que uso [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) para informar de una métrica denominada "salesPerHour". Enviar que un correo electrónico a Mis compañeros si "salesPerHour" es menor que 100, promedio de más de 24 horas.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

La misma regla puede usarse para la métrica notificada mediante el [parámetro de medida](app-insights-api-custom-events-metrics.md#properties) de llamada de seguimiento otro como TrackEvent o trackPageView.

## <a name="metric-names"></a>Métricos nombres

Nombre de métrica | Nombre de la pantalla | Descripción
---|---|---
`basicExceptionBrowser.count`|Excepciones de explorador|Recuento de las excepciones que se inicia en el explorador.
`basicExceptionServer.count`|Excepciones de servidor|Recuento de excepciones no controladas por la aplicación
`clientPerformance.clientProcess.value`|Hora de procesamiento del cliente|Tiempo entre recibir el último byte de un documento hasta que se carga DOM. Aún pueden procesar peticiones asíncronas.
`clientPerformance.networkConnection.value`|Tiempo de conexión de red de carga de página| Tarda el explorador para conectarse a la red. Puede ser 0 si la caché.
`clientPerformance.receiveRequest.value`|Recibe el tiempo de respuesta| Tiempo entre enviar solicitud para empezar a recibir la respuesta del explorador.
`clientPerformance.sendRequest.value`|Hora de la solicitud de envío| Duración explorador para enviar la solicitud.
`clientPerformance.total.value`|Tiempo de carga de página de explorador|Tiempo de solicitud de usuario hasta que se cargan DOM, hojas de estilo, secuencias de comandos y las imágenes.
`performanceCounter.available_bytes.value`|Memoria disponible|Memoria física inmediatamente disponible para un proceso o para uso del sistema.
`performanceCounter.io_data_bytes_per_sec.value`|Tasa de IO de proceso|Número total de bytes por segundo leer y escribir en archivos, la red y dispositivos.
`performanceCounter.number_of_exceps_thrown_per_sec`|tasa de excepción|Excepciones iniciadas por segundo.
`performanceCounter.percentage_processor_time.value`|Proceso de la CPU|El porcentaje de tiempo transcurrido de todos los subprocesos de proceso utilizada por el procesador para ejecutar instrucciones para el proceso de aplicaciones.
`performanceCounter.percentage_processor_total.value`|Tiempo de procesador|El porcentaje de tiempo que el procesador invierte en subprocesos activos.
`performanceCounter.process_private_bytes.value`|Proceso de bytes privados|Memoria asignada exclusivamente a procesos de aplicación supervisados.
`performanceCounter.request_execution_time.value`|Tiempo de ejecución de solicitud ASP.NET|Tiempo de ejecución de la solicitud más reciente.
`performanceCounter.requests_in_application_queue.value`|Solicitudes en cola de ejecución de ASP.NET|Longitud de la cola de solicitud de aplicación.
`performanceCounter.requests_per_sec`|Tasa de solicitud ASP.NET|Tasa de todas las solicitudes a la aplicación por segundo desde ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Errores de dependencia|Recuento de errores llamadas realizadas por la aplicación de servidor a recursos externos.
`request.duration`|Tiempo de respuesta del servidor|Tiempo entre recibir una solicitud HTTP y finalización de enviar la respuesta.
`request.rate`|Tasa de solicitud|Tasa de todas las solicitudes de la aplicación por segundo.
`requestFailed.count`|Solicitudes de errores|Las solicitudes de recuento de HTTP que ha generado un código de respuesta > = 400
`view.count`|Vistas de página|Recuento de solicitudes de usuario del cliente de una página web. Se filtra el tráfico síntesis.
{su nombre personalizado métrica}|{Su nombre métrica}|El valor de métrica notificado por [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) o en el [parámetro de medidas de una llamada de seguimiento](app-insights-api-custom-events-metrics.md#properties).

Las mediciones se envían por módulos de telemetría diferentes:

Grupo métrica | Módulo de selector
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>vista | [Explorador JavaScript](app-insights-javascript.md)
performanceCounter | [Rendimiento](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dependencia](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
solicitud de<br/>requestFailed|[Solicitud del servidor](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)

## <a name="webhooks"></a>Webhooks

Puede [automatizar la respuesta a una alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure llamará a una dirección web de su elección cuando se produce una alerta.

## <a name="see-also"></a>Vea también


* [Secuencia de comandos para configurar la información de la aplicación](app-insights-powershell-script-create-resource.md)
* [Crear perspectivas de aplicaciones y recursos de prueba de web a partir de plantillas](app-insights-powershell.md)
* [Automatizar acoplamiento diagnósticos de Microsoft Azure de impresiones de aplicación](app-insights-powershell-azure-diagnostics.md)
* [Automatizar la respuesta a una alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
