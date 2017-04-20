<properties 
    pageTitle="Configuración de aplicación perspectivas SDK con ApplicationInsights.config o .xml" 
    description="Habilitar o deshabilitar módulos de la colección de datos y agregue contadores de rendimiento y otros parámetros" 
    services="application-insights"
    documentationCenter="" 
    authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/12/2016" 
    ms.author="awills"/>

# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurar la información de la aplicación SDK con ApplicationInsights.config o .xml

Aplicación perspectivas .NET SDK consta de un número de paquetes de NuGet. El [paquete core](http://www.nuget.org/packages/Microsoft.ApplicationInsights) proporciona la API para el envío de telemetría a la información de la aplicación. [Paquetes adicionales](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) proporcionan telemetría _módulos_ e _inicializadores_ para seguimiento automático de telemetría de su aplicación y su contexto. Ajustando el archivo de configuración, puede habilitar o deshabilitar inicializadores y módulos de telemetría y establecer parámetros para algunos de ellos.

El archivo de configuración se denomina `ApplicationInsights.config` o `ApplicationInsights.xml`, en función del tipo de la aplicación. Se agrega automáticamente a su proyecto al [instalar la mayoría de las versiones del SDK][start]. También se agrega a una aplicación web mediante el [Monitor de estado en un servidor IIS][redfield], o cuando se selecciona la [extensión para un sitio Web de Azure o una máquina virtual](app-insights-azure-web-apps.md)de Appplication perspectivas.

No hay un archivo equivalente para controlar el [SDK en una página web][client].

Este documento describe las secciones que vea en la configuración de archivos, cómo controlan los componentes del SDK, y qué paquetes de NuGet cargar los componentes.

## <a name="telemetry-modules-aspnet"></a>Módulos de telemetría (ASP.NET)

Cada módulo de telemetría recopila un tipo específico de datos y utiliza la API principal para enviar los datos. Los módulos se instalan diferentes paquetes de NuGet, que también se agrega las líneas necesarias al archivo config.

Hay un nodo en el archivo de configuración para cada módulo. Para deshabilitar un módulo, elimine el nodo o comentario.



### <a name="dependency-tracking"></a>Dependencia de seguimiento

[Dependencia de seguimiento](app-insights-dependencies.md) recopila telemetría sobre llamadas que su aplicación realiza a las bases de datos y servicios externos y las bases de datos. Para permitir que este módulo para trabajar en un servidor IIS, debe [instalar el Monitor de estado][redfield]. Se utiliza en aplicaciones web de Azure o VM, [Seleccione la extensión de aplicación perspectivas](app-insights-azure-web-apps.md).

También puede escribir su propio código con la [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency)de seguimiento de dependencia.


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Paquete de NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

### <a name="performance-collector"></a>Selector de rendimiento

[Recopila contadores de rendimiento del sistema](app-insights-performance-counters.md) como la carga de CPU, memoria y red de las instalaciones de IIS. Puede especificar qué contadores para recopilar, incluidos los contadores de rendimiento que ha configurado usted mismo.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Paquete de NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .


### <a name="application-insights-diagnostics-telemetry"></a>Aplicación perspectivas diagnósticos telemetría

La `DiagnosticsTelemetryModule` informa de errores en el propio código de instrumentación perspectivas de aplicación. Por ejemplo, si el código no puede tener acceso a contadores de rendimiento o un `ITelemetryInitializer` produce una excepción. Un seguimiento de este módulo de telemetría de seguimiento aparece en la [Búsqueda de diagnóstico][diagnostic]. Envía los datos de diagnóstico a dc.services.vsallin.net.
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* Paquete de NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Si solo se instala este paquete, no se crea automáticamente el archivo ApplicationInsights.config. 

### <a name="developer-mode"></a>Modo para programadores

`DeveloperModeWithDebuggerAttachedTelemetryModule`fuerza la información de la aplicación `TelemetryChannel` para enviar datos inmediatamente, elemento de uno telemetría a la vez, cuando hay un depurador asociado con el proceso de aplicación. Esto reduce la cantidad de tiempo entre el momento en que cuando se realiza un seguimiento de la aplicación de telemetría y aparece en el portal de información de la aplicación. Hace gran sobrecarga de CPU y ancho de banda.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Aplicación perspectivas Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Paquete NuGet

### <a name="web-request-tracking"></a>Seguimiento de solicitud de Web

El [código de tiempo y el resultado de respuesta](app-insights-asp-net.md) de HTTP solicitudes de informes. 

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Paquete de NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### <a name="exception-tracking"></a>Seguimiento de excepción

`ExceptionTrackingTelemetryModule`pistas excepciones no controlada en la aplicación web. Ver [errores y excepciones][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Paquete de NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-realiza un seguimiento de [las excepciones de tarea inadvertida](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-realiza un seguimiento de las excepciones no controladas para funciones de trabajo, servicios de windows y aplicaciones de consola.
* [Aplicación perspectivas Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Paquete de NuGet.

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights

El paquete de Microsoft.ApplicationInsights proporciona la [API principal](https://msdn.microsoft.com/library/mt420197.aspx) del SDK. Use esta opción los demás módulos de telemetría y también puede [usarlo para definir su propios telemetría](app-insights-api-custom-events-metrics.md).

* No hay entrada en ApplicationInsights.config.
* Paquete de NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Si sólo tiene que instalar esta NuGet no se genera ningún archivo config.

## <a name="telemetry-channel"></a>Canal de telemetría

El canal de telemetría administra el búfer y la transmisión de telemetría para el servicio de información de la aplicación. 

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`es el canal predeterminado para los servicios. Coloca los datos en la memoria.
* `Microsoft.ApplicationInsights.PersistenceChannel`es una alternativa para aplicaciones de consola. Pueden guardar los datos unflushed en almacenamiento persistente cuando la aplicación se cierra y le enviará cuando la aplicación se inicia de nuevo.


## <a name="telemetry-initializers-aspnet"></a>Inicializadores de telemetría (ASP.NET)

Inicializadores de telemetría establecen las propiedades de contexto que se envían junto con todos los elementos de telemetría. 

Puede [escribir a sus propios inicializadores](app-insights-api-filtering-sampling.md#add-properties) para establecer las propiedades de contexto.

Los inicializadores estándares están todas establecidos por los paquetes Web o WindowsServer NuGet:


* `AccountIdTelemetryInitializer`establece la propiedad AccountId.
* `AuthenticatedUserIdTelemetryInitializer`establece la propiedad AuthenticatedUserId establecido por el SDK de JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer`actualizaciones de la `RoleName` y `RoleInstance` propiedades de la `Device` contexto para todos los elementos de telemetría con la información que se extraen desde el entorno de tiempo de ejecución de Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer`actualizaciones de la `Version` propiedad de la `Component` contexto para todos los elementos de telemetría con el valor extraídos de la `BuildInfo.config` archivo generado por MS Build.
* `ClientIpHeaderTelemetryInitializer`actualizaciones de `Ip` propiedad de la `Location` contexto de todos los elementos de telemetría basado en la `X-Forwarded-For` encabezado de la solicitud.
* `DeviceTelemetryInitializer`actualiza las siguientes propiedades de la `Device` contexto para todos los elementos de telemetría.
 - `Type`se establece en "PC"
 - `Id`se establece en el nombre de dominio del equipo donde se ejecuta la aplicación web.
 - `OemName`se establece en el valor extraído de la `Win32_ComputerSystem.Manufacturer` WMI de campo.
 - `Model`se establece en el valor extraído de la `Win32_ComputerSystem.Model` WMI de campo.
 - `NetworkType`se establece en el valor extraído de la `NetworkInterface`.
 - `Language`establecer el nombre de la `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer`actualizaciones de la `RoleInstance` propiedad de la `Device` contexto para todos los elementos de telemetría con el nombre del dominio del equipo donde se ejecuta la aplicación web.
* `OperationNameTelemetryInitializer`actualizaciones de la `Name` propiedad de la `RequestTelemetry` y la `Name` propiedad de la `Operation` contexto de todos los elementos de telemetría basado en el método HTTP, así como los nombres de controlador de ASP.NET MVC y acción invoca para procesar la solicitud.
* `OperationIdTelemetryInitializer`o `OperationCorrelationTelemetryInitializer` actualizaciones la `Operation.Id` propiedad de contexto de todos los elementos de telemetría controlan al tratar una solicitud con generada automáticamente `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`actualizaciones de la `Id` propiedad de la `Session` contexto para todos los elementos de telemetría con valor extraídos de la `ai_session` cookie generado por el código de instrumentación ApplicationInsights JavaScript ejecuta en el explorador del usuario. 
* `SyntheticTelemetryInitializer`o `SyntheticUserAgentTelemetryInitializer` actualizaciones el `User`, `Session` y `Operation` propiedades de contextos de todos los elementos de telemetría controlan cuando controla una solicitud de un origen de síntesis, como una disponibilidad probar o robots de motor de búsqueda. De forma predeterminada, [El explorador métricas](app-insights-metrics-explorer.md) no muestra telemetría síntesis. 

    La `<Filters>` establecer propiedades de las solicitudes de identificar.
* `UserAgentTelemetryInitializer`actualizaciones de la `UserAgent` propiedad de la `User` contexto de todos los elementos de telemetría basado en la `User-Agent` encabezado de la solicitud.
* `UserTelemetryInitializer`actualizaciones de la `Id` y `AcquisitionDate` propiedades de `User` contexto para todos los elementos de telemetría con valores extraídos de la `ai_user` cookie generado por el código de instrumentación aplicación perspectivas JavaScript ejecuta en el explorador del usuario.
* `WebTestTelemetryInitializer`establece el identificador de usuario, el identificador de sesión y propiedades del origen de síntesis para las solicitudes HTTP que proceden de [pruebas de disponibilidad](app-insights-monitor-web-app-availability.md).
La `<Filters>` establecer propiedades de las solicitudes de identificar.

## <a name="telemetry-processors-aspnet"></a>Procesadores de telemetría (ASP.NET)

Procesadores de telemetría pueden filtrar y modificar cada elemento de telemetría antes de se envía desde el SDK en el portal.

Puede [escribir sus propios procesadores de telemetría](app-insights-api-filtering-sampling.md#filtering).


#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Procesador de telemetría muestreo adaptación (desde 2.0.0-beta3)

Esto está habilitado de forma predeterminada. Si la aplicación envía una gran cantidad de telemetría, este procesador elimina parte de ella.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

El parámetro proporciona el destino que el algoritmo intenta lograr. Cada instancia del SDK funciona de forma independiente, por lo que si el servidor es un clúster de varios equipos, el volumen real de telemetría se multiplicará según corresponda.

[Más información sobre la muestra](app-insights-sampling.md).



#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Procesador de telemetría de tasa fija muestreo (desde 2.0.0-beta1)

También es un estándar [procesador de telemetría de muestreo](app-insights-api-filtering-sampling.md#sampling) (desde 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parámetros de canal (Java)

Estos parámetros afectan al modo en que el SDK de Java debe almacenar y vaciar los datos de telemetría que recopila.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity

El número de elementos de telemetría que pueden almacenarse en almacenamiento en memoria del SDK. Cuando se alcance este número, se vacía el búfer de telemetría, es decir, los elementos de telemetría se envían al servidor de información de la aplicación.

-   Min: 1
-   Max: 1000
-   Predeterminado: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds 

Determina la frecuencia con los datos que se almacenan en el almacenamiento en memoria, se deben vaciar (enviados a perspectivas de aplicación).

-   Min: 1
-   Max: 300
-   Valor predeterminado: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB

Determina el tamaño máximo en MB asignado para el almacenamiento persistente en el disco local. Este almacenamiento se utiliza para almacenar elementos de telemetría que no se transmite el extremo de la información de la aplicación. Cuando se ha alcanzado el tamaño de almacenamiento, se descartarán los nuevos elementos de telemetría.

-   Min: 1
-   Max: 100
-   Valor predeterminado: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey

Esto determina el recurso de información de la aplicación en la que aparecen los datos. Normalmente se crea un recurso independiente, con una clave independiente, para cada una de las aplicaciones.

Si desea establecer la clave dinámicamente: por ejemplo, si desea enviar los resultados de la aplicación en diferentes recursos - puede omitir la clave del archivo de configuración y establecer en el código.

Para establecer la clave para todas las instancias de TelemetryClient, incluidos los módulos de telemetría estándar, establezca la clave en TelemetryConfiguration.Active. Haga lo siguiente en un método de inicialización, como global.aspx.cs en un servicio ASP.NET:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Si desea enviar un conjunto específico de eventos a un recurso diferente, puede establecer la clave para un TelemetryClient específica:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Más información acerca de la API][api].

Para obtener una nueva [crear un nuevo recurso en el portal de información de la aplicación]de la clave,[new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

