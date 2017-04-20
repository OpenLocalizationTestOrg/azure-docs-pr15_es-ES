<properties 
    pageTitle="Solución de problemas y preguntas sobre la información de la aplicación" 
    description="¿Algo en Visual Studio aplicación perspectivas claro o no funciona? Pruebe aquí." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="questions---application-insights-for-aspnet"></a>Preguntas - perspectivas de aplicación de ASP.NET

## <a name="configuration-problems"></a>Problemas de configuración

*Tengo problemas para configurar mi:*

* [Aplicación .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Supervisar una aplicación ya se está ejecutando](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnóstico de Azure](app-insights-azure-diagnostics.md)
* [Aplicación web de Java](app-insights-java-troubleshoot.md)
* [Otras plataformas](app-insights-platforms.md)

*No recibo datos desde el servidor*

* [Establecer excepciones de firewall](app-insights-ip-addresses.md)
* [Configurar un servidor de ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurar un servidor de Java](app-insights-java-agent.md)


## <a name="can-i-use-application-insights-with-"></a>¿Puedo usar aplicación impresiones con...?

[Vea plataformas][platforms]


## <a name="is-it-free"></a>¿Es gratuita?

* Sí, si elige gratuita [precios nivel](app-insights-pricing.md). Obtiene la mayoría de las características y una amplia cuota de datos. 
* Debe proporcionar los datos de la tarjeta de crédito para registrar con Microsoft Azure, pero no se realizará ningún cargo a menos que use otro pagado para Azure servicio o actualizar explícitamente a un nivel de pago.
* Si su aplicación envía más datos que la cuota mensual para el nivel gratuito, deja de iniciar sesión. Si esto sucede, puede elegir iniciar pagar o espere hasta que se restablece la cuota del final del mes.
* Datos de sesión y uso básico no están sujeto a una cuota.
* También es una prueba gratuita de 30 días, durante el que recibe las características de pagadas de forma gratuita.
* Cada recurso de aplicación tiene una cuota independiente y establezca su nivel de precios independientemente de los demás.

#### <a name="what-do-i-get-if-i-pay"></a>¿Qué puedo hacer si tengo que pagar?

* Una mayor [cuota mensual de datos](https://azure.microsoft.com/pricing/details/application-insights/).
* Opción de pago exceso para continuar la recopilación de datos sobre la cuota mensual. Si los datos se trata sobre la cuota, le cobrará por Mb.
* [Exportar continuo](app-insights-export-telemetry.md).


## <a name="q14"></a>¿Qué modificar aplicación perspectivas en mi proyecto?

Los detalles dependen del tipo de proyecto. Para una aplicación web:


+ Estos archivos se agrega a su proyecto:

 + ApplicationInsights.config. 
 + AI.js


+ Estos paquetes de NuGet las instalaciones.

 -  *Aplicación perspectivas API* - la API principal

 -  *API de perspectivas de aplicación para las aplicaciones Web* - usado para enviar telemetría desde el servidor

 -  *API de perspectivas de aplicación para las aplicaciones de JavaScript* - usado para enviar telemetría desde el cliente

    Los paquetes incluyen estos ensamblados:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Inserta elementos en:

 - Web.config

 - Packages.config

+ (Nuevos proyectos solo - si [agregar perspectivas de aplicación a un proyecto existente][start], tendrá que hacerlo manualmente.) Inserta fragmentos de código en el código de cliente y servidor inicializarse el identificador de recurso de aplicación perspectivas. Por ejemplo, en una aplicación MVC, código se inserta en la página maestra Views/Shared/_Layout.cshtml


## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>¿Cómo actualizar desde versiones anteriores de SDK?

Vea las [notas de la versión](app-insights-release-notes.md) para el SDK correspondiente al tipo de aplicación. 



## <a name="update"></a>¿Cómo se puede cambiar qué recursos Azure mi proyecto envía los datos?

En el Explorador de soluciones, haga clic con el botón `ApplicationInsights.config` y elija **Actualizar aplicación perspectivas**. Puede enviar los datos a un recurso nuevo o existente en Azure. El Asistente para la actualización cambia la clave de instrumentación en ApplicationInsights.config, que determina donde el servidor SDK envía los datos. A menos que desactive la opción "Actualizar todo", también cambiará la clave donde aparece en sus páginas web.


#### <a name="data"></a>¿Cuánto tiempo se conservan los datos en el portal? ¿Es segura?

Eche un vistazo a la [declaración de privacidad y retención de datos][data].

## <a name="logging"></a>Registro de información

#### <a name="post"></a>¿Cómo puedo ver los datos de entrada de búsqueda de diagnóstico?

Nos no registrar automáticamente los datos de entrada, pero puede usar una llamada TrackTrace: ubicar los datos en el parámetro de mensaje. Esto tiene un límite de tamaño más largo que los límites de las propiedades de cadena, aunque no puede filtrar en él. 

## <a name="security"></a>Seguridad

#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>¿Es seguro en el portal de Mis datos? ¿Cuánto tiempo se conserva?

Consulte [datos retención y privacidad][data].


## <a name="q17"></a>¿He habilitado todo el contenido de la información de la aplicación?

<table border="1">
<tr><th>¿Qué debería ver</th><th>Cómo obtenerlo</th><th>¿Por qué desea</th></tr>
<tr><td>Gráficos de disponibilidad</td><td><a href="../app-insights-monitor-web-app-availability/">Pruebas Web</a></td><td>Saber que depende de la aplicación web</td></tr>
<tr><td>Rendimiento de la aplicación de servidor: tiempos de respuesta,...
</td><td><a href="../app-insights-asp-net/">Agregar información de la aplicación a su proyecto</a><br/>o <br/><a href="../app-insights-monitor-performance-live-website-now/">Instalar el Monitor de estado de AI en servidor</a> (o escribir su propio código para <a href="../app-insights-api-custom-events-metrics/#track-dependency">realizar un seguimiento de las dependencias</a>)</td><td>Detectar problemas de rendimiento</td></tr>
<tr><td>Telemetría de dependencia</td><td><a href="../app-insights-monitor-performance-live-website-now/">Instalar el Monitor de estado de AI en servidor</a></td><td>Diagnosticar problemas con las bases de datos u otros componentes externos</td></tr>
<tr><td>Obtener seguimientos de la pila de excepciones</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Insertar llamadas TrackException en el código</a> (pero algunos se muestran automáticamente)</td><td>Detectar y diagnosticar excepciones</td></tr>
<tr><td>Seguimientos de registro de búsqueda</td><td><a href="../app-insights-search-diagnostic-logs/">Agregar un adaptador de registro</a></td><td>Diagnosticar excepciones, problemas de rendimiento</td></tr>
<tr><td>Conceptos básicos del uso de cliente: vistas de página, sesiones,...</td><td><a href="../app-insights-javascript/">Inicializador de JavaScript en páginas web</a></td><td>Análisis de uso</td></tr>
<tr><td>Métricas del cliente personalizados</td><td><a href="../app-insights-api-custom-events-metrics/">Llamadas de seguimiento en páginas web</a></td><td>Mejorar la experiencia del usuario</td></tr>
<tr><td>Métricas personalizados de servidor</td><td><a href="../app-insights-api-custom-events-metrics/">Seguimiento de llamadas en el código de servidor</a></td><td>Inteligencia empresarial</td></tr>
</table>


## <a name="automation"></a>Automatización

Puede [escribir secuencias de comandos de PowerShell](app-insights-powershell.md) para crear y actualizar los recursos de información de la aplicación.

## <a name="more-answers"></a>Más respuestas

* [Foro de perspectivas de aplicación](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 