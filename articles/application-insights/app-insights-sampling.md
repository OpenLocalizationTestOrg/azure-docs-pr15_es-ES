<properties 
    pageTitle="Muestras de telemetría en aplicación perspectivas | Microsoft Azure" 
    description="Cómo mantener el volumen de telemetría bajo control." 
    services="application-insights" 
    documentationCenter="windows"
    authors="vgorbenko" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="awills"/>

#  <a name="sampling-in-application-insights"></a>Muestreo en perspectivas de aplicación

*Información de la aplicación está en vista previa.*


Muestreo es que una característica de [Perspectivas de aplicación de Visual Studio](app-insights-overview.md) es la manera recomendada para reducir el tráfico de telemetría y almacenamiento sin perder un análisis estadística correcto de datos de aplicación. El filtro selecciona elementos relacionados para que pueda desplazarse entre los elementos cuando se realiza investigaciones de diagnósticos.
Cuando los recuentos métricos se presentan al usuario en el portal, están normalizarse para tener en cuenta el muestreo para minimizar cualquier efecto en las estadísticas.

Muestreo reduce el tráfico, le ayuda a mantener dentro de cuotas mensuales de datos y le ayuda a evitar la limitación.

## <a name="in-brief"></a>En resumen:

* Muestreo conserva 1 en *n* registros y descarta el resto. Por ejemplo, puede mantener el eventos de 1 a 5, un muestreo de 20%. 
* Muestreo se realiza automáticamente si la aplicación envía una gran cantidad de telemetría, en las aplicaciones de servidor web ASP.NET.
* También puede establecer muestreo manualmente, ya sea en el portal de la página de precio. o bien, en el SDK de ASP.NET en el archivo config, también reducir el tráfico de red.
* Si iniciar eventos personalizados y desea asegurarse de que un conjunto de eventos se conservan o se descartan juntas, asegúrese de que tienen el mismo valor OperationId.
* El divisor de muestreo *n* se notifica cada registro en la propiedad `itemCount`, que en la búsqueda aparece debajo del nombre descriptivo "solicitud recuento" o "número de eventos". Cuando no se muestra en operación, `itemCount==1`.
* Si escribe análisis de consultas, debe [tener en cuenta muestreo](app-insights-analytics-tour.md#counting-sampled-data). En particular, en lugar de simplemente el recuento de registros, debe usar `summarize sum(itemCount)`.


## <a name="types-of-sampling"></a>Tipos de muestreo


Existen tres métodos de muestreo alternativo:

* **Muestreo adaptación** se ajusta automáticamente el volumen de telemetría enviado desde el SDK en su aplicación ASP.NET. Valor predeterminado de SDK v 2.0.0-beta3. Están disponibles para ASP.NET solo telemetría de servidor. 
* **Tasa fija muestreo** reduce el volumen de telemetría enviado desde su servidor de ASP.NET y los exploradores de los usuarios. Establecer la velocidad. El cliente y el servidor sincronizarán su muestreo para esa, en búsqueda, puedan navegar entre las vistas de la página relacionada y solicitudes.
* **Muestras de recopilación** reduce el volumen de telemetría conservado por el servicio de información de la aplicación, en una tasa que ha configurado. No reducir el tráfico de telemetría, pero le ayuda a mantener dentro de la cuota mensual. 

Si muestras de tasa adaptación o fijo están en operación, muestreo recopilación está deshabilitado.

## <a name="ingestion-sampling"></a>Muestras de recopilación

Este formulario de muestreo funciona en el punto donde la telemetría desde el servidor web, exploradores y dispositivos alcanza el extremo de servicio de información de la aplicación. Aunque no reduce el tráfico de telemetría enviado desde la aplicación, reduce la cantidad procesada y se conservan (y cobrar por) por aplicación perspectivas.

Use este tipo de muestreo si la aplicación a menudo trata sobre la cuota mensual y no tiene la opción de usar cualquiera de los tipos basados en el SDK de muestreo. 

Establecer la velocidad de muestreo en las cuotas y precios de módulo:

![Desde el módulo de información general de la aplicación, haga clic en configuración, cuota, ejemplos, a continuación, seleccione una velocidad de muestreo y haga clic en actualizar.](./media/app-insights-sampling/04.png)

Al igual que otros tipos de muestreo, el algoritmo conserva los elementos de telemetría relacionados. Por ejemplo, cuando está inspeccionar la telemetría en búsqueda, podrá encontrar la solicitud relacionada con una excepción determinada. Métrica cuenta como la tasa de solicitud y tasa de excepción se conservan correctamente.

Puntos de datos que se descartan por muestreo no están disponibles en función de cualquier aplicación perspectivas como [Exportar continuo](app-insights-export-telemetry.md).

Muestras de recopilación no funcionan mientras está basado en el SDK adaptación o tasa fija muestreo en funcionamiento. Si la tasa de muestreo en el SDK es menor que 100%, se omite la tasa de muestreo de recopilación que ha configurado.

> [AZURE.WARNING] El valor que aparece en el mosaico indica el valor que establezca para muestreo de recopilación. No representa la tasa de muestreo real si muestreo SDK esté en funcionamiento.


## <a name="adaptive-sampling-at-your-web-server"></a>Muestreo adaptación en el servidor web

Muestras adaptación está disponible para el SDK de perspectivas de aplicación ASP.NET v 2.0.0-beta3 y versiones posteriores y está habilitado de forma predeterminada. 


Muestreo adaptación afecta al volumen de telemetría enviado desde la aplicación web de servidor del servicio de información de la aplicación. El volumen se ajusta automáticamente para mantener dentro de un tipo de tráfico máximo especificado.

No funciona en bajos volúmenes de telemetría, así que una aplicación de depuración o un sitio Web con poco uso no se verán afectado.

Para lograr el volumen de destino, algunos de la telemetría generada se descarta. Pero como otros tipos de muestreo, el algoritmo mantiene elementos de telemetría relacionados. Por ejemplo, cuando está inspeccionar la telemetría en búsqueda, podrá encontrar la solicitud relacionada con una excepción determinada. 

Métrica cuenta como la tasa de solicitud y tasa de excepción se ajustan para compensa la para la velocidad de muestreo, para que los que muestren en el Explorador de métrica aproximadamente los valores correctos.

Los paquetes de **actualizaciones NuGet de su proyecto** a la última versión de la *versión preliminar* de aplicación perspectivas: haga clic en el proyecto en el Explorador de soluciones, elija Administrar paquetes de NuGet, active **incluir la versión preliminar** y busque Microsoft.ApplicationInsights.Web. 

En [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), puede ajustar varios parámetros en el `AdaptiveSamplingTelemetryProcessor` nodo. Las cifras que se muestran son los valores predeterminados:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    El tipo de destino que el objetivo es el algoritmo de adaptación para **en cada host del servidor**. Si la aplicación web se ejecuta en muchos hosts, reduzca este valor tal modo que permanezca dentro de su velocidad de destino del tráfico en el portal de información de la aplicación.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 

    El intervalo en el que se vuelve a evaluar la tasa actual de telemetría. Evaluación se realiza como una media móvil. Desea acortar este intervalo si su telemetría está sujeto a ráfagas rápidas.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    Cuando muestreo cambios de valor de porcentaje, ¿con qué frecuencia después estamos nos permite reducir el porcentaje de muestreo nuevamente para capturar menos datos.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`

    Cuando muestreo cambios de valor de porcentaje, cómo pronto después estamos se puede aumentar el porcentaje de muestreo nuevamente para capturar más datos.

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`

    Como porcentaje de muestreo varía, ¿qué es el valor mínimo que estamos pueden establecer.

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`

    Como porcentaje de muestreo varía, ¿qué es el valor máximo que estamos pueden establecer.

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 

    En el cálculo de la media móvil, el grosor asigna el valor más reciente. Use un valor igual o inferior a 1. Los valores más pequeños modifique el algoritmo menos reactiva a rápida.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`

    Valor asignado cuando ha comenzado a la aplicación. No reducir esto mientras está depuración. 

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: configurar muestreo adaptación de código

En lugar de ajustar muestreo en el archivo de configuración, puede usar el código. Esto le permite especificar una función de devolución de llamada se invoca siempre que se vuelve a evaluar la velocidad de muestreo. Puede utilizar esto, por ejemplo, para averiguar qué muestreo se está usando.

Quitar el `AdaptiveSamplingTelemetryProcessor` nodo desde el archivo config.



*C#*

```C#

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    
    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Obtenga más información acerca de los procesadores de telemetría](app-insights-api-filtering-sampling.md#filtering)).


<a name="other-web-pages"></a>
## <a name="sampling-for-web-pages-with-javascript"></a>Muestreo para páginas web con JavaScript

Puede configurar páginas web de muestras de tipo fijo desde cualquier servidor. 

Cuando [configure las páginas web de información de la aplicación](app-insights-javascript.md), modifique el fragmento que recibe desde el portal de información de la aplicación. (En las aplicaciones ASP.NET, el fragmento de código normalmente trata en _Layout.cshtml.)  Insertar una línea como `samplingPercentage: 10,` antes de la clave de instrumentación:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 
    
    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Para el porcentaje de muestras, elija un porcentaje que se parezca 100/N, donde N es un entero.  Muestreo actualmente no admite otros valores.

Si también se habilita el muestreo tasa fija en el servidor, los clientes y el servidor sincronizará para esa, en búsqueda, puedan navegar entre las vistas de la página relacionada y solicitudes.


## <a name="fixed-rate-sampling-for-aspnet-web-sites"></a>Muestras de tipo fijo para sitios web ASP.NET

Tasa fija muestreo reduce el tráfico enviado desde el servidor web y exploradores web. A diferencia de muestreo adaptación, reduce telemetría en una tasa fija decidida por el usuario. También sincroniza el cliente y muestreo de servidor para que se conservan elementos relacionados: por ejemplo, para que si mira una vista de página de búsqueda, puede encontrar su solicitud relacionado.

El algoritmo de muestreo conserva los elementos relacionados. Para cada solicitud HTTP eventos junto con sus eventos relacionados se descartan o transmitidas. 

En el Explorador de métricas, tasas como recuentos de convocatoria y excepción se multiplican por un factor a compensa la para la velocidad de muestreo, para que sean aproximadamente correctos.

1. **Actualizar los paquetes de NuGet de su proyecto** a la última versión de la *versión preliminar* de la información de la aplicación. Haga clic en el proyecto en el Explorador de soluciones, elija Administrar paquetes de NuGet, active **incluir la versión preliminar** y busque Microsoft.ApplicationInsights.Web. 

2. **Deshabilitar muestreo adaptación**: en [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), quitar o comentar el `AdaptiveSamplingTelemetryProcessor` nodo.

    ```xml

    <TelemetryProcessors>
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    

    ```

2. **Habilitar el módulo de muestras de tipo fijo.** Agregar este fragmento de código a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```XML

    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>

    ```

> [AZURE.NOTE] Para el porcentaje de muestras, elija un porcentaje que se parezca 100/N, donde N es un entero.  Muestreo actualmente no admite otros valores.



### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativa: habilitar muestreo tasa fija en el código de servidor


En lugar de establecer el parámetro de muestreo en el archivo de configuración, puede usar el código. 

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Obtenga más información acerca de los procesadores de telemetría](app-insights-api-filtering-sampling.md#filtering)).


## <a name="when-to-use-sampling"></a>¿Cuándo usar muestreo?

Muestreo adaptación se habilita automáticamente si utiliza la 2.0.0-beta3 de la versión de ASP.NET SDK o posterior. No importa qué versión SDK que utilice, puede usar muestras de recopilación (en nuestro servidor).

No es necesario muestreo para la mayoría de las aplicaciones pequeñas y medianas. La información de diagnóstico más útil y estadísticas más precisas se obtienen mediante la recopilación de datos en todas las actividades de usuario. 

 
Las ventajas principales de muestreo son:

* Intervalo de tiempo de aplicación perspectivas servicio colocaciones ("límites") de puntos de datos cuando la aplicación envía una tasa muy alta de telemetría en resumen. 
* Para mantener dentro de la [cuota](app-insights-pricing.md) de puntos de datos para el nivel de precios. 
* Para reducir el tráfico de red de la colección de telemetría. 

### <a name="which-type-of-sampling-should-i-use"></a>¿Qué tipo de muestreo debo usar?


**Use la recopilación de muestreo si:**

* A menudo, vaya a través de la cuota mensual de telemetría.
* Está usando una versión del SDK que no admite muestreo - por ejemplo, el SDK de Java o las versiones de ASP.NET anteriores a 2.
* Recibe una gran cantidad de telemetría de exploradores web de los usuarios.

**Utilizar tasa fija muestreo si:**

* Usa el SDK de perspectivas de aplicación para la versión de servicios web ASP.NET 2.0.0 o posterior, y
* Desea muestreo sincronizados entre cliente y servidor, de modo que, cuando se están investigando los eventos de [búsqueda](app-insights-diagnostic-search.md), puede desplazarse entre los eventos relacionados en el cliente y el servidor, como las vistas de página y las solicitudes de http.
* Está seguro del porcentaje muestreo adecuado para la aplicación. Debe ser suficientemente alto para obtener indicadores precisos, pero por debajo de la tasa que supera la cuota de precio y los límites de limitación. 


**Utilizar muestreo adaptación:**

En caso contrario, se recomienda muestreo adaptación. Esta opción está habilitada de forma predeterminada en el servidor ASP.NET SDK, versión 2.0.0-beta3 o posterior. No reduce el tráfico hasta un determinado tipo mínimo, por lo que no afectará a un sitio de poco uso.


## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>¿Cómo sé si muestreo está en operación?

Para conocer la velocidad de muestreo real independientemente de dónde se ha aplicado, use una [consulta de análisis](app-insights-analytics.md) como esta:

    requests | where timestamp > ago(1d)
  	| summarize 100/avg(itemCount) by bin(timestamp, 1h) 
  	| render areachart 

En cada conserva el registro, `itemCount` indica el número de registros originales que representa, igual a 1 + el número de registros descartados anterior. 


## <a name="how-does-sampling-work"></a>¿Cómo funciona muestreo?

Tasa fija y muestreo adaptación son una característica de SDK en versiones ASP.NET desde 2.0.0 en adelante. Muestras de recopilación son una característica del servicio de información de la aplicación y pueden estar en funcionamiento si el SDK no está realizando muestreo. 

El algoritmo de muestreo decide qué elementos de telemetría que ocupa y cuáles desea conservar (ya sea en el SDK o en el servicio de información de la aplicación). La decisión de muestreo se basa en varias reglas que pretende conservar todos los puntos de datos interrelacionados intacta, mantener una experiencia de diagnóstico en perspectivas de aplicación que puede utilizar y fiable incluso con un conjunto de datos reducido. Por ejemplo, si una solicitud errónea su aplicación envía elementos de telemetría adicionales (como excepción y trazas la sesión de esta solicitud), muestreo no dividirá esta solicitud y otro telemetría. Se mantiene o coloca todo. Como resultado, cuando examine los detalles de la solicitud de aplicación perspectivas, siempre puede ver la solicitud junto con sus elementos de telemetría asociado. 

Para las aplicaciones que definen "usuario" (es decir, las aplicaciones web más típicas), la decisión de muestreo se basa en el hash del identificador de usuario, lo que significa que todos los telemetría para cualquier usuario concreto se mantienen o se descarta. Para los tipos de aplicaciones que no se definen los usuarios (por ejemplo, los servicios web) la decisión de muestreo se basa en el identificador de operación de la solicitud. Por último, para los elementos de telemetría que no tienen id de usuario ni operación establecer (por ejemplo artículos de telemetría notificados desde subprocesos asincrónicos sin contexto http) muestreo simplemente captura un porcentaje de elementos de telemetría de cada tipo. 

La presentación de telemetría a usted, el servicio de aplicación perspectivas ajusta las métricas el mismo porcentaje de muestreo que usó en el momento de la colección de compensación para los puntos de datos que faltan. Por lo tanto, al mirar la telemetría en perspectivas de aplicación, los usuarios experimentan aproximaciones estadística correctas que están muy cerca de los números reales.

La precisión de la aproximación depende en gran medida el porcentaje de muestreo configurado. Además, la precisión aumenta para aplicaciones que controlen un gran volumen de solicitudes de generalmente similares desde una gran cantidad de usuarios. Por otro lado, para las aplicaciones que no funcionan con una carga importante, muestreo no es necesario estas aplicaciones normalmente pueden enviar todas su telemetría mientras mantenerse dentro de la cuota, sin que se produzca una pérdida de datos de limitación. 

Tenga en cuenta que perspectivas de aplicación no muestra tipos de telemetría métricas y sesiones, desde para estos tipos de reducción de la precisión puede ser muy no deseado. 

### <a name="adaptive-sampling"></a>Adaptación muestreo

Muestreo adaptación agrega un componente que supervisa la velocidad de transmisión de SDK actual y a continuación, se ajusta el porcentaje de muestreo para intentar mantenerse dentro de la velocidad máxima de destino. Ajuste se vuelve a calcular a intervalos regulares y se basa en una media móvil de la tasa de transmisión saliente.

## <a name="sampling-and-the-javascript-sdk"></a>Muestras y el SDK de JavaScript

El cliente (JavaScript) SDK participa en muestreos tasa fija junto con el SDK de servidor. Las páginas instrumentadas sólo enviará telemetría de cliente de los mismos usuarios para la que el servidor realiza su decisión a "muestra en". Esta lógica está diseñada para mantener la integridad de sesión de usuario a través de los lados cliente y servidor. Como resultado, desde cualquier elemento de telemetría determinado en aplicación perspectivas puede encontrar todos los demás elementos de telemetría para este usuario o sesión. 

*Mi cliente y del servidor telemetría no mostrar muestras coordinadas tal y como se describió anteriormente.*

* Compruebe que habilitado tasa fija muestreo en servidor y cliente.
* Asegúrese de que la versión SDK es 2.0 o posterior.
* Compruebe que establece el mismo porcentaje de muestreo en el cliente y el servidor.


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 

*¿Por qué no muestreo un simple "recopilar X porcentaje de cada tipo de telemetría"?*

 *  Mientras este enfoque muestreo proporciona con una precisión de muy alta en aproximaciones métricas, funcionarían capacidad de correlación de datos de diagnósticos por usuario, la sesión y la solicitud, que es fundamental diagnósticos de. Por lo tanto, muestreo funciona mejor con "recopile todos telemetría elementos para X porcentaje de usuarios de la aplicación", o "telemetría todos para X porcentaje de solicitudes de aplicación" lógica. Para los elementos de telemetría no asociados a las solicitudes (por ejemplo, procesamiento asincrónica en segundo plano), es el otoño volver "recopilar X % de todos los elementos de cada tipo de telemetría." 

*¿Se puede cambiar el porcentaje de muestreo más adelante?*

 * Sí, muestreo adaptación cambia gradualmente el porcentaje de muestras, basado en el volumen de la telemetría observado actualmente.

 

*Si uso muestreo de tipo fijo, ¿cómo sé qué muestreo porcentaje funcionará la mejor para mi aplicación?*

* Una forma de muestreo inicialmente adaptación, averigüe qué puntuar liquida en (consulte la pregunta anterior) y, a continuación, cambie a tipo fijo muestreo utilizando la tasa. 

    En caso contrario, tendrá que adivinar. Analizar el uso de telemetría actual en AI, observar límite que está teniendo y calcular el volumen de la telemetría recopilado. Estas tres entradas, junto con el nivel de precios seleccionado, recomendamos cuánto desea reducir el volumen de la telemetría recopilado. Sin embargo, un aumento en el número de usuarios o algunos otro cambio en el volumen de telemetría podría invalidar la estimación.

*¿Qué ocurre si se configura el porcentaje de muestreo demasiado bajo?*

* Porcentaje de muestreo demasiado bajo (muestreo demasiado minuciosa) reduce la precisión de las aproximaciones cuando perspectivas de aplicación intenta compensa la la visualización de los datos para la reducción de volumen de datos. Además, experiencia de diagnóstico puede negativo afectar, como algunas de las solicitudes con poca frecuencia errores o lentas pueden muestrear fuera.

*¿Qué ocurre si se configura el porcentaje de muestreo demasiado alto?*

* Configurar el porcentaje de muestreo demasiado alto (no tan agresiva suficientemente) el resultado de una reducción del volumen de la telemetría recopilada insuficiente. Aún puede experimentar pérdida de datos de telemetría relacionada con el límite y el costo de utilizar la aplicación perspectivas podría ser mayor que planeó debido a exceso cargos.

*¿En qué plataformas puedo usar muestreo?*

* Muestras de recopilación pueden ocurrir automáticamente para cualquier telemetría encima de un cierto volumen, si el SDK no está realizando muestreo. ¿Trabaja, por ejemplo, si su aplicación usa un servidor de Java, o si está utilizando una versión anterior de SDK ASP.NET.

* Si está utilizando las versiones de ASP.NET SDK 2.0.0 y por encima (alojado en Azure o en su propio servidor), obtendrá adaptación muestras de forma predeterminada, pero puede cambiar tipo fijo como se describió anteriormente. Con muestras de tipo fijo, el SDK de explorador se sincroniza automáticamente con los eventos relacionados de ejemplo. 

*Hay ciertos eventos poco comunes que siempre desea ver. ¿Cómo puedo conseguirlas más allá del módulo de muestreo?*

 * Iniciar una instancia independiente de TelemetryClient con un nuevo TelemetryConfiguration (no predeterminado activa). Use enviar sus eventos poco comunes.



## <a name="next-steps"></a>Pasos siguientes

* [El filtrado](app-insights-api-filtering-sampling.md) puede proporcionar más control estricto de lo que envía el SDK.
