<properties 
    pageTitle="Explorar los registros de seguimiento de .NET en perspectivas de aplicación" 
    description="Buscar registros generados con seguimiento, NLog o Log4Net." 
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
    ms.date="07/21/2016" 
    ms.author="awills"/>
 
# <a name="explore-net-trace-logs-in-application-insights"></a>Explorar los registros de seguimiento de .NET en perspectivas de aplicación  

Si usa NLog, log4Net o System.Diagnostics.Trace para el seguimiento de diagnóstico en su aplicación ASP.NET, puede hacer que los registros que se envían a [Perspectivas de aplicación de Visual Studio][start], donde puede explorar y búsquedas en ellas. Los registros se combinarán con la demás telemetría procedentes de la aplicación, para que puedan identificar los seguimientos asociados con los servicios de cada solicitud del usuario y les relacionar con otros eventos y los informes de excepción.




> [AZURE.NOTE] ¿Necesita el módulo de captura de registro? Es un adaptador útil para los registradores de fiesta de 3 º, pero si no está usando NLog, log4Net o System.Diagnostics.Trace, considere la posibilidad de llamar a [Aplicación perspectivas TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) directamente.


## <a name="install-logging-on-your-app"></a>Instalar la aplicación de inicio de sesión

Instale el marco del registro seleccionado en el proyecto. Esto debe dar como resultado una entrada de app.config o web.config.

Si está usando System.Diagnostics.Trace, debe agregar una entrada a web.config:

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener" 
             type="System.Diagnostics.TextWriterTraceListener" 
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configurar la información de la aplicación para recopilar registros

**[Agregar perspectivas de aplicación al proyecto](app-insights-asp-net.md)** si todavía no lo ha hecho todavía. Verá una opción para incluir el selector de registro.

O **Configurar perspectivas de aplicación** haciendo clic en el proyecto en el Explorador de soluciones. Seleccione la opción para **Configurar colección de seguimiento**.

*¿No hay ninguna opción de selector aplicación perspectivas menú o registro?* Pruebe la [solución de problemas](#troubleshooting).


## <a name="manual-installation"></a>Instalación manual

Use este método si el tipo de proyecto no es compatible con el instalador de información de la aplicación (por ejemplo un proyecto escritorio de Windows). 

1. Si planea usar log4Net o NLog, instálelo en su proyecto. 
2. En el Explorador de soluciones, haga clic en su proyecto y elija **Administrar paquetes de NuGet**.
3. Busque "Perspectivas de aplicación"

    ![Obtener la versión preliminar del adaptador adecuado](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)

4. Seleccione el paquete adecuado - uno de:
  + Microsoft.ApplicationInsights.TraceListener (para capturar System.Diagnostics.Trace llamadas)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

El paquete de NuGet instala a los ensamblados necesarios y también modifica web.config o app.config.

## <a name="insert-diagnostic-log-calls"></a>Insertar llamadas de registro de diagnóstico

Si usa System.Diagnostics.Trace, sería una llamada típica:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si prefiere log4net o NLog:

    logger.Warn("Slow response - database01");


## <a name="using-the-trace-api-directly"></a>Usar el seguimiento de la API directamente

El seguimiento de la información de la aplicación API puede llamar directamente. Los adaptadores registro use esta API. 

Por ejemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Una ventaja de TrackTrace es que puede colocar datos relativamente largos en el mensaje. Por ejemplo, se podrían codificar datos del envío. 

Además, puede agregar un nivel de gravedad a su mensaje. Y, como otro telemetría, puede agregar valores de propiedad que puede usar para filtrar o buscar diferentes conjuntos de trazas de ayuda. Por ejemplo:


    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Esto le permitirá, en [búsqueda][diagnostic], para filtrar fácilmente todos los mensajes de un determinado nivel de gravedad relativos a una base de datos determinado.

## <a name="explore-your-logs"></a>Explorar los registros

Ejecute la aplicación, ya sea en modo de depuración o implementar directo.

En el módulo de información general de la aplicación en [el portal de aplicación perspectivas][portal], haga clic en [búsqueda][diagnostic].

![En información de la aplicación, haga clic en búsqueda](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Búsqueda](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Puede, por ejemplo:

* Filtrar seguimientos de registro, o en los elementos con propiedades específicas
* Inspeccionar un elemento específico en detalle.
* Buscar otro telemetría relacionados con la misma solicitud de usuario (es decir, con la misma OperationId) 
* Guardar la configuración de esta página como favorito

> [AZURE.NOTE] **Muestreo.** Si la aplicación envía una gran cantidad de datos y que está usando el SDK de perspectivas de aplicación para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptación puede operar y enviar sólo un porcentaje de su telemetría. [Más información sobre muestreo.](app-insights-sampling.md)

## <a name="next-steps"></a>Pasos siguientes

[Diagnosticar errores y excepciones de ASP.NET][exceptions]

[Obtener más información acerca de la búsqueda][diagnostic].



## <a name="troubleshooting"></a>Solución de problemas

### <a name="how-do-i-do-this-for-java"></a>¿Cómo hacer esto para Java

Usar los [adaptadores de registro de Java](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>No hay ninguna opción perspectivas de aplicación en el menú contextual del proyecto

* Herramientas de aplicación perspectivas está instalado en el equipo de desarrollo. En el menú Herramientas de Visual Studio, extensiones y actualizaciones, busque herramientas de perspectivas de aplicación. Si no está en la ficha instalado, abra la pestaña en línea e instalarlo.
* Esto podría ser un tipo de proyecto no admitido herramientas de perspectivas de aplicación. Use [la instalación manual](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Opción sin adaptador de registro en la herramienta de configuración

* Debe instalar el marco de trabajo de registro en primer lugar.
* Si está usando System.Diagnostics.Trace, asegúrese de [lo ha configurado en `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* ¿Tengo la última versión de herramientas de la información de la aplicación? En el menú **Herramientas** de Visual Studio, elija **extensiones y actualizaciones**y abra la ficha **actualizaciones** . Si hay herramientas perspectivas de aplicación, haga clic en para actualizarla.


### <a name="emptykey"></a>Recibo un error "clave de instrumentación no puede estar vacía"

Parece instalar el paquete de Nuget adaptador registro sin perspectivas de aplicación.

En el Explorador de soluciones, haga clic con el botón `ApplicationInsights.config` y elija **Actualizar aplicación perspectivas**. Obtendrá un cuadro de diálogo que le invita a iniciar sesión en Azure y cree un recurso de información de la aplicación, o volver a utilizar uno existente. Que debe repararlo.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Puedo ver seguimientos de búsqueda de diagnóstico, pero no los otros eventos

A veces puede tardar un rato para todos los eventos y las solicitudes para obtener a través de la canalización.

### <a name="limits"></a>¿Se conserva la cantidad de datos?

Hasta 500 eventos por segundo de cada aplicación. Eventos se conservan siete días.

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>No puedo ver algunas de las entradas de registro que se esperaban

Si la aplicación envía una gran cantidad de datos y que está usando el SDK de perspectivas de aplicación para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptación puede operar y enviar sólo un porcentaje de su telemetría. [Más información sobre muestreo.](app-insights-sampling.md)

## <a name="add"></a>Pasos siguientes

* [Configurar disponibilidad y pruebas de capacidad de respuesta][availability]
* [Solución de problemas][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

 
