<properties 
    pageTitle="Filtrado y preprocesamiento en el SDK de perspectivas aplicación | Microsoft Azure" 
    description="Escribir procesadores de telemetría e inicializadores de telemetría para el SDK de para filtrar o agregar propiedades a los datos antes de enviar la telemetría en el portal de información de la aplicación." 
    services="application-insights"
    documentationCenter="" 
    authors="beckylino" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="borooji"/>

# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrado y preprocesamiento de telemetría en el SDK de perspectivas de aplicación

*Información de la aplicación está en vista previa.*

Puede escribir y configurar plug-ins para el SDK de perspectivas de aplicación personalizar cómo se captura y procesan antes de enviar al servicio de aplicación perspectivas telemetría. 

Actualmente, estas características están disponibles para el SDK de ASP.NET.

* [Muestreo](app-insights-sampling.md) reduce el volumen de telemetría sin afectar a las estadísticas. Mantiene juntos relacionadas con los puntos de datos para que pueda desplazarse entre ellos al diagnosticar un problema. En el portal, se multiplica el recuento total para compensa la para el muestreo.
* [Filtrado con procesadores de telemetría](#filtering) le permite seleccionar o modificar telemetría en el SDK antes de enviarlo al servidor. Por ejemplo, puede reducir el volumen de telemetría excluyendo las solicitudes de robots. Pero filtrar es un enfoque más básico para reducir el tráfico de muestreo. Le permite tener más control sobre lo que se transmite, pero debe tener en cuenta que afecta a las estadísticas: por ejemplo, si filtra todas las solicitudes correcta.
* [Inicializadores de telemetría agregar propiedades](#add-properties) a cualquier telemetría enviado desde la aplicación, incluidos telemetría desde los módulos estándar. Por ejemplo, puede agregar valores calculados; o números de versión por la que se va a filtrar los datos en el portal.
* [La API de SDK](app-insights-api-custom-events-metrics.md) se usa para enviar métricas y eventos personalizados.


Antes de empezar:

* Instale la [aplicación perspectivas SDK para ASP.NET v2](app-insights-asp-net.md) en la aplicación. 


<a name="filtering"></a>
## <a name="filtering-itelemetryprocessor"></a>Filtrado: ITelemetryProcessor

Esta técnica proporciona un control más directo sobre qué es incluir o excluir de la secuencia de telemetría. Puede usar junto con muestreo, o por separado.

Para filtrar telemetría, escribir un procesador de telemetría y registrar con el SDK. Telemetría todos los pasa a través de su procesador y, a continuación, puede quitar de la secuencia o agregar propiedades. Esto incluye telemetría desde los módulos estándar, como el selector de solicitud HTTP y el selector de dependencia, así como de telemetría que haya escrito usted mismo. Por ejemplo, puede filtrar telemetría sobre solicitudes de robots o llamadas de dependencia correcta. 

> [AZURE.WARNING] Filtrar la telemetría enviada desde el SDK usando procesadores puede inclinar las estadísticas que se observan en el portal y dificultar la siga elementos relacionados.
> 
> Considere la posibilidad de usar [muestreo](app-insights-sampling.md).

### <a name="create-a-telemetry-processor"></a>Crear un procesador de telemetría

1. Compruebe que el SDK de perspectivas de aplicación en su proyecto es versión 2.0.0 o posterior. Haga clic en el proyecto en el Explorador de soluciones de Visual Studio y elija Administrar paquetes de NuGet. En el Administrador de paquetes de NuGet, active Microsoft.ApplicationInsights.Web.

1. Para crear un filtro, implementar ITelemetryProcessor. Esto es otro punto de capacidad de ampliación como el módulo de telemetría, inicializador de telemetría y canal de telemetría. 

    Observe que los procesadores de telemetría construir una cadena de procesamiento. Al crear una instancia de un procesador de telemetría, pasar un vínculo al siguiente procesador en la cadena. Cuando se pasa un punto de datos de telemetría para el método de proceso, funciona y, a continuación, llama el procesador de telemetría siguiente de la cadena.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Insertar esto en ApplicationInsights.config: 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Esta es la misma sección donde inicializa un filtro de muestreo).

Puede pasar valores de cadena desde el archivo config proporcionando públicas propiedades con nombre en la clase. 

> [AZURE.WARNING] Tenga cuidado para que coincida con el nombre del tipo y los nombres de propiedad en el archivo config a los nombres de clase y propiedad en el código. Si el archivo config hace referencia a un tipo de inexistentes o propiedad, el SDK silenciosamente no enviar ninguna telemetría.

 
**Como alternativa,** puede inicializar el filtro en el código. En una clase de inicialización adecuado - por ejemplo AppStart en Global.asax.cs - inserte el procesador en la cadena:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients creadas después de este momento usará los procesadores.

### <a name="example-filters"></a>Filtros de ejemplo

#### <a name="synthetic-requests"></a>Solicitudes de síntesis

Filtrar pruebas robots y web. Aunque métricas Explorer ofrece la opción para filtrar los orígenes de síntesis, esta opción reduce el tráfico filtrando en el SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Error de autenticación

Filtrar solicitudes con una respuesta "401". 

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar las llamadas de dependencia rápida y remota

Si solo desea diagnosticar llamadas lentas, descartar los fast. 

> [AZURE.NOTE] Esto inclinar las estadísticas que ve en el portal. El gráfico de dependencia tendrá un aspecto como si las llamadas de dependencia son todos los errores.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnosticar problemas de dependencia

[Este blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) describe un proyecto para diagnosticar problemas de dependencia enviando automáticamente ping normales a las dependencias.


<a name="add-properties"></a>
## <a name="add-properties-itelemetryinitializer"></a>Agregar propiedades: ITelemetryInitializer

Utilizar a inicializadores de telemetría para definir propiedades globales que se envían con todos los telemetría; y reemplazar seleccionado comportamiento de los módulos de telemetría estándar. 

Por ejemplo, la información de aplicación para el paquete Web recopila telemetría acerca de las solicitudes HTTP. De forma predeterminada, lo marca como error cualquier solicitud con un código de respuesta > = 400. Pero si desea tratar 400 como un éxito, puede proporcionar a un inicializador de telemetría que establece la propiedad de éxito.

Si desea proporcionar un inicializador de telemetría, se llama siempre que se llama a cualquiera de los métodos de Track*(). Esto incluye métodos llamados por los módulos de telemetría estándar. Por convención, estos módulos no establecen cualquier propiedad que ya ha establecido un inicializador. 

**Definir su inicializador**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Cargar su inicializador**

En ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Como alternativa,* puede crear una instancia del inicializador en código, por ejemplo en Global.aspx.cs:


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Obtenga más información de este ejemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetría de JavaScript

*JavaScript*

Insertar a un inicializador de telemetría inmediatamente detrás del código de inicialización que obtuvo desde el portal: 

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Puede ver un resumen de las propiedades personalizadas no disponibles en la telemetryItem, en el [modelo de datos](app-insights-export-data-model.md#lttelemetrytypegt).

Puede agregar a tantas inicializadores como desee. 


## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor y ITelemetryInitializer

¿Cuál es la diferencia entre los procesadores de telemetría e inicializadores de telemetría?

* Hay algunos problemas en lo que puede hacer con ellos: ambos pueden usarse para agregar propiedades a telemetría.
* TelemetryInitializers siempre se ejecutan antes de TelemetryProcessors.
* TelemetryProcessors permiten completamente reemplazar o descartar un elemento de telemetría.
* TelemetryProcessors no procesar telemetría de contador de rendimiento.



## <a name="persistence-channel"></a>Canal de persistencia 

Si la aplicación se ejecuta cuando la conexión a internet no está siempre disponible o lenta, considere usar el canal de persistencia en lugar de canal en memoria predeterminada. 

El canal de en memoria predeterminada pierde cualquier telemetría que no se ha enviado en el momento en que se cierra la aplicación. Aunque puede utilizar `Flush()` para intentar enviar los datos permanecen en el búfer, aún pierde datos si no hay ninguna conexión a internet, o si la aplicación se cierra antes de transmisión se ha completado.

Por el contrario, el canal de persistencia búferes de telemetría en un archivo, antes de enviar el portal. `Flush()`garantiza que los datos se almacenan en el archivo. Si no se envían los datos en el momento en que la aplicación se cierra, permanece en el archivo. Cuando se reinicia la aplicación, los datos se enviarán a continuación, si hay una conexión a internet. Datos se acumulen en el archivo para siempre que sea necesario hasta que haya una conexión disponible. 

### <a name="to-use-the-persistence-channel"></a>Usar el canal de persistencia

1. Importar el paquete de NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Incluir este código en la aplicación, en una ubicación de inicialización adecuado:
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Usar `telemetryClient.Flush()` antes de su cierra la aplicación, para asegurarse de que se envían al portal o guardados en el archivo de datos.

    Tenga en cuenta que Flush() sincrónico para el canal de persistencia, pero asincrónico para otros canales.

 
El canal de persistencia está optimizado para escenarios de dispositivos, donde el número de eventos creados mediante una aplicación es pequeño y la conexión a menudo es poco confiable. Este canal escribirá eventos en el disco en almacenamiento confiable en primer lugar y, a continuación, intente enviarlo. 

#### <a name="example"></a>Ejemplo

Supongamos que desea controlar las excepciones no controladas. Suscribirse a la `UnhandledException` evento. En la devolución de llamada, incluye una llamada a vaciado para asegurarse de que la telemetría se conserva.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Cuando la aplicación se cierra, verá un archivo en `%LocalAppData%\Microsoft\ApplicationInsights\`, que contiene los eventos comprimidos. 
 
Próxima vez que inicie esta aplicación, el canal recoger este archivo y ofrecer telemetría a la información de la aplicación si es posible.

#### <a name="test-example"></a>Ejemplo de prueba

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


El código del canal de persistencia es en [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel). 


## <a name="reference-docs"></a>Documentos de referencia

* [Introducción a la API](app-insights-api-custom-events-metrics.md)

* [Referencia ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## <a name="sdk-code"></a>Código SDK

* [SDK principal de ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [5 DE ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Pasos siguientes


* [Buscar eventos y registros][diagnostic]
* [Muestreo](app-insights-sampling.md)
* [Solución de problemas][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 
