<properties 
    pageTitle="API de aplicación perspectivas para eventos personalizados y métrica | Microsoft Azure" 
    description="Insertar líneas de código en su aplicación de escritorio o de dispositivo, la página web o el servicio para realizar un seguimiento de uso y diagnóstico de problemas." 
    services="application-insights"
    documentationCenter="" 
    authors="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/19/2016" 
    ms.author="awills"/>

# <a name="application-insights-api-for-custom-events-and-metrics"></a>API de perspectivas de aplicación para métricas y eventos personalizados 

*Información de la aplicación está en vista previa.*

Insertar líneas de código en la aplicación para averiguar qué hacen los usuarios con él, o para diagnosticar problemas. Puede enviar telemetría de dispositivo y aplicaciones de escritorio, los clientes web y servidores web. La telemetría de core API de [Perspectivas de aplicación de Visual Studio](app-insights-overview.md) permite enviar eventos personalizados y métricas y sus propias versiones de telemetría estándar. Esta API es la misma API usado por los selectores de datos de aplicación perspectivas estándares.

## <a name="api-summary"></a>Resumen de la API

La API es uniforme en todas las plataformas, además de algunas pequeñas variaciones.

Método | Utilizado para
---|---
[`TrackPageView`](#page-views) | Páginas, pantallas, módulos o formularios
[`TrackEvent`](#track-event) | Acciones de usuario y otros eventos. Usar para realizar un seguimiento de comportamiento de los usuarios o para supervisar el rendimiento.
[`TrackMetric`](#track-metric) | Medidas de rendimiento como longitudes de cola no relacionadas con eventos específicos
[`TrackException`](#track-exception)|Excepciones de registro de diagnóstico. Trazar donde se producen en relación con otros eventos y examinar seguimientos de la pila.
[`TrackRequest`](#track-request)| Inicie la frecuencia y la duración de solicitudes de servidor para el análisis del rendimiento.
[`TrackTrace`](#track-trace)|Mensajes del registro de diagnóstico. También puede capturar registros de fiesta de 3 º.
[`TrackDependency`](#track-dependency)|Inicie la duración y la frecuencia de llamadas a componentes externos que depende la aplicación.

Puede [adjuntar propiedades y mediciones](#properties) a la mayoría de estas llamadas de telemetría. 


## <a name="prep"></a>Antes de empezar

Si todavía no lo ha hecho estos aún:

* Agregue el SDK de perspectivas de aplicación a su proyecto:
 * [Proyecto ASP.NET][greenbrown]
 * [Proyecto de Java][java] 
 * [JavaScript en cada página web][client]   

* En el código de servidor web o dispositivo, incluyen:

    *C#:*`using Microsoft.ApplicationInsights;`

    *VB:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

## <a name="construct-a-telemetryclient"></a>Crear un TelemetryClient

Crear una instancia de TelemetryClient (excepto en JavaScript en páginas web):

*C#:* 

    private TelemetryClient telemetry = new TelemetryClient();

*VB:* 

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient es segura.

Le recomendamos que use una instancia de `TelemetryClient` para cada módulo de la aplicación. Por ejemplo, es posible que tenga una `TelemetryClient` en el servicio web para informar entrantes solicitudes http y otra en una clase de software intermedio a eventos de lógica de negocios de informe. Puede establecer las propiedades como `TelemetryClient.Context.User.Id` para realizar un seguimiento de los usuarios y sesiones, o `TelemetryClient.Context.Device.Id` para identificar el equipo. Esta información se vincula a todos los eventos enviados por la instancia.


## <a name="track-event"></a>Eventos de control

En información de la aplicación, un *evento personalizado* es una datos punto puede mostrar tanto en como en el [Explorador de métricas] [ metrics] como un recuento de agregado así como apariciones individuales en [Búsqueda de diagnóstico][diagnostic]. (Que no está relacionado con MVC u otro framework "eventos.") 

Insertar llamadas TrackEvent en el código para contar con qué frecuencia los usuarios, elija una característica determinada, ¿con qué frecuencia lograr objetivos concretos, o puede realizar determinados tipos de error. 

Por ejemplo, en una aplicación de juego, enviar un evento siempre que un usuario gana el juego: 

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*
    
    telemetry.TrackEvent("WinGame");

*VB*


    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


### <a name="view-your-events-in-the-azure-portal"></a>Ver los eventos en el portal de Azure

Para ver un recuento de sus eventos, abra un módulo de [Explorador métrica](app-insights-metrics-explorer.md) , agregar un nuevo gráfico y seleccione eventos.  

![](./media/app-insights-api-custom-events-metrics/01-custom.png)

Para comparar los recuentos de eventos diferentes, establezca el tipo de gráfico a la cuadrícula y a continuación, grupo por nombre de evento:

![](./media/app-insights-api-custom-events-metrics/07-grid.png)


En la cuadrícula, haga clic en un nombre de evento para ver las apariciones individuales del evento.

![Los eventos de obtención de detalles](./media/app-insights-api-custom-events-metrics/03-instances.png)

Haga clic en cualquier repetición para ver más detalles.

Para centrarse en eventos específicos de búsqueda o Explorer métrica, establecer filtro del módulo en los nombres de evento que está interesado en:

![Abrir filtros, expanda el nombre del evento y seleccione uno o varios valores](./media/app-insights-api-custom-events-metrics/06-filter.png)

## <a name="track-metric"></a>Métrica de control

Use TrackMetric para enviar métricas que no están vinculadas a determinados eventos. Por ejemplo, puede supervisar una longitud de cola a intervalos regulares. 

Métricas se muestran como gráficos estadísticos en el Explorador de métrica, pero a diferencia de los eventos, no puede buscar apariciones individuales en búsqueda de diagnóstico.

Valores de métrica deben ser > = 0 para que se muestre correctamente.


*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

De hecho, puede hacerlo en un subproceso de fondo:

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


Para ver los resultados, abra el Explorador de métricas y agregar un nuevo gráfico. Establecer para mostrar su métrica.

![Agregar un gráfico nuevo o seleccione un gráfico y en personalizado, seleccione la métrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

Existen algunos [límites del número de métricas](#limits) que puede usar.

## <a name="page-views"></a>Vistas de página

En un dispositivo o aplicación de la página web, telemetría de vista de página se envía de forma predeterminada cuando se carga cada pantalla o la página. Pero se puede cambiar para realizar el seguimiento de vistas de página en momentos adicionales o diferentes. Por ejemplo, en una aplicación que muestra las pestañas o módulos, que podría querer realizar un seguimiento de una "página" cuando el usuario abra un nuevo módulo. 

![Lente de uso en el módulo de introducción](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Datos de usuario y sesión se envían como propiedades junto con vistas de página, para que los gráficos de usuario y sesión vaya activas cuando hay telemetría de vista de página.

#### <a name="custom-page-views"></a>Vistas de página personalizado

*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


Si tiene varias pestañas en diferentes páginas HTML, puede especificar la dirección URL demasiado:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### <a name="timing-page-views"></a>Vistas de la página de intervalos

De forma predeterminada, las horas notifican como "Tiempo de carga de la vista de página" se mide desde cuando el explorador envía la solicitud, hasta que se llama el evento de carga de página del explorador.

En su lugar, puede:

* Establecer una duración explícita en la llamada [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) .
 * `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`
* Usar la vista de página registra los intervalos de llamadas `startTrackPage` y `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

... 

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

El nombre que usar como el primer parámetro asocia las llamadas de inicio y detención. El valor predeterminado es el nombre de la página actual. 

Las duraciones de carga página resultante que se muestra en el Explorador de métrica derivan del intervalo entre las llamadas de inicio y detención. Es que el intervalo de tiempo realmente.

## <a name="track-request"></a>Solicitud de control

Utilizar el SDK de servidor para iniciar las solicitudes HTTP. 

Puede también llamarlo usted mismo si desea simular solicitudes en un contexto que no tenga el módulo de servicio web ejecutando.

*C#*

    // At start of processing this request:

    // Operation Id and Name are attached to all telemetry and help you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    telemetry.Context.Operation.Name = requestName;
    
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetry.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success



## <a name="operation-context"></a>Contexto de la operación

Elementos de telemetría se pueden asociar juntos adjuntando a ellos un ID de operación comunes. El módulo de seguimiento de solicitud estándar para ello de excepciones y otros eventos enviados al procesamiento de una solicitud HTTP. En [búsqueda](app-insights-diagnostic-search.md) y [análisis](app-insights-analytics.md), puede utilizar el identificador para encontrar fácilmente eventos asociados a la solicitud. 

Establecer el identificador de la manera más sencilla es establecer un contexto de operación mediante este patrón:

    // Establish an operation context and associated telemetry item:
    using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
    {
        // Telemetry sent in here will use the same operation ID.
        ...
        telemetry.TrackEvent(...); // or other Track* calls
        ...
        // Set properties of containing telemetry item - for example:
        operation.Telemetry.ResponseCode = "200";
        
        // Optional: explicitly send telemetry item:
        telemetry.StopOperation(operation);

    } // When operation is disposed, telemetry item is sent.

Además de establecer un contexto de operación `StartOperation` crea un elemento de telemetría del tipo que especifique y lo envía cuando desechar la operación, o si se llama explícitamente `StopOperation`. Si usa `RequestTelemetry` como el tipo de telemetría, su duración está establecida para el intervalo de tiempo entre iniciar y detener.

Contextos de operación no pueden anidarse. Si ya existe un contexto de la operación, su ID está asociado con todos los elementos contenidos, incluido el elemento creado con StartOperation.

En la búsqueda, el contexto de la operación se utiliza para crear la lista de elementos relacionados:

![Elementos relacionados](./media/app-insights-api-custom-events-metrics/21.png)


## <a name="track-exception"></a>Realizar un seguimiento de excepción

Enviar excepciones a la aplicación perspectivas: contarles [][metrics], como una indicación de la frecuencia de un problema; y [Examinar las repeticiones individuales][diagnostic]. Los informes incluyen el seguimiento de la pila.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

Los SDK detectarlos muchos automáticamente, para que no tenga siempre llamar explícitamente a TrackException.

* ASP.NET: [escribir código para detectar excepciones](app-insights-asp-net-exceptions.md)
* J2EE: [se detectan automáticamente las excepciones](app-insights-java-get-started.md#exceptions-and-request-failures)
* JavaScript: Detecta automáticamente. Si desea deshabilitar recopilación automática, agregue una línea en el fragmento de código insertar en sus páginas web:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```


## <a name="track-trace"></a>Realizar un seguimiento de seguimiento 

Use esta opción para ayudar a diagnosticar problemas mediante el envío de un rastro de ruta de navegación' ' de impresiones de aplicación. Puede enviar grandes volúmenes de datos de diagnóstico y les inspeccionar en [búsqueda de diagnóstico][diagnostic]. 

 

[Inicie sesión adaptadores] [ trace] usar esta API para enviar registros de terceros en el portal.


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


Puede buscar en el contenido del mensaje, pero no puede filtrar en ella (a diferencia de los valores de propiedad).

El límite de tamaño en `message` es mucho mayor que el límite de las propiedades.
Una ventaja de TrackTrace es que puede colocar datos relativamente largos en el mensaje. Por ejemplo, se podrían codificar datos del envío.  


Además, puede agregar un nivel de gravedad a su mensaje. Y, como otro telemetría, puede agregar valores de propiedad que puede usar para filtrar o buscar diferentes conjuntos de trazas de ayuda. Por ejemplo:


    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Esto le permitirá, en [búsqueda][diagnostic], para filtrar fácilmente todos los mensajes de un determinado nivel de gravedad relativos a una base de datos determinado.

## <a name="track-dependency"></a>Realizar un seguimiento de dependencia

Use esta llamada para realizar un seguimiento de los tiempos de respuesta y tasas de éxito de las llamadas a un fragmento de código externo. Los resultados aparecen en los gráficos de dependencia en el portal. 

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Recuerde que el servidor que SDK incluyen un [módulo de dependencia](app-insights-dependencies.md) que descubre y realiza un seguimiento de determinadas dependencia automáticamente - llama por ejemplo las bases de datos y las API de REST. Tiene que instalar a un agente en el servidor para que el módulo de funcionar. Si desea realizar un seguimiento de las llamadas que no se detectan el seguimiento automatizado, o si no desea instalar al agente debería usar esta llamada.

Para desactivar el módulo de seguimiento de dependencia estándar, editar [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) y eliminar la referencia a `DependencyCollector.DependencyTrackingTelemetryModule`.



## <a name="flushing-data"></a>Datos de baja

Normalmente el SDK envía los datos a veces elegidos minimizar el impacto en el usuario. Sin embargo, en algunos casos podría querer vaciar el búfer: por ejemplo, si está utilizando el SDK en una aplicación que se cierra.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

Observe que la función es asincrónico para el [canal de telemetría de servidor](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/), pero sincrónico si decide usar el canal de memoria o [persistente](app-insights-api-filtering-sampling.md#persistence-channel).


## <a name="authenticated-users"></a>Usuarios autenticados

En una aplicación web, los usuarios están identificados por cookie de manera predeterminada. Un usuario podría van a contar más de una vez si obtener acceso a la aplicación desde un equipo distinto o el explorador o eliminar las cookies. 

Pero, si los usuarios iniciar sesión en la aplicación, puede obtener un recuento más preciso estableciendo el identificador de usuario autenticado en el código del explorador:

*JavaScript*

```JS
    // Called when my app has identified the user.
    function Authenticated(signInId) {
      var validatedId = signInId.replace(/[,;=| ]+/g, "_");
      appInsights.setAuthenticatedUserContext(validatedId);
      ...
    }
```

En una aplicación de MVC, por ejemplo de sitio web ASP.NET:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

No es necesario utilizar el nombre de inicio de sesión de la real del usuario. Solo tiene que ser un identificador único para ese usuario. No debe incluir espacios o ninguno de los caracteres `,;=|`. 

El identificador de usuario también se establece en una cookie de sesión y envía al servidor. Si está instalado el SDK de servidor, el identificador de usuario autenticado se enviará como parte de las propiedades de contexto de telemetría de cliente y servidor, para que pueda filtrar y buscar en él.

Si su aplicación de grupos de usuarios en cuentas, también puede pasar un identificador de la cuenta (con las mismas restricciones de caracteres).


      appInsights.setAuthenticatedUserContext(validatedId, accountId);

En el [Explorador de métricas](app-insights-metrics-explorer.md), puede crear un gráfico que cuenta los **usuarios autenticados** y **cuentas de usuario**. 

También puede [Buscar] [ diagnostic] para puntos de datos de cliente con nombres de usuario específicos y cuentas.

## <a name="properties"></a>Filtrar, buscar y segmentar los datos con las propiedades

Puede adjuntar propiedades y medidas a sus eventos (y también a métricas, página vistas, excepciones y otros datos de telemetría).

**Propiedades** son valores de cadena que puede usar para filtrar la telemetría en los informes de uso. Por ejemplo si la aplicación proporciona varios juegos, que desee adjuntar el nombre del juego para cada evento, para que pueda ver qué juegos son más populares. 

Hay un límite de aproximadamente 1k en la longitud de cadena. (Si desea enviar grandes cantidades de datos, use el parámetro de mensaje de [TrackTrace](#track-trace)).

**Métricas** son valores numéricos que se pueden presentar gráficamente. Por ejemplo, que desea comprobar si hay un aumento gradual de las calificaciones que lograr los jugadores. Los gráficos se pueden segmentar por las propiedades que se envía con el evento, por lo que podría obtener independiente o apiladas los gráficos de juegos diferentes.

Valores de métrica deben ser > = 0 para que se muestre correctamente.


Existen algunos [límites en el número de valores de propiedad y métricas, las propiedades](#limits) que puede usar.


*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );
          

*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*VB*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*
    
    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());
    
    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());
    
    telemetry.trackEvent("WinGame", properties, metrics);


> [AZURE.NOTE] Procure no para registrar información de identificación personal en las propiedades.

**Si ha usado métricas**, abra el Explorador de métrica y seleccione la métrica del grupo personalizado:

![Abra el Explorador de métrica, seleccione el gráfico y seleccione la métrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*Si no aparece la métrica, o si el encabezado personalizado no existe, cierre el módulo de selección e inténtelo más tarde. A veces puede tardar una hora para métricas agregarse a través de la canalización.*

**Si utiliza propiedades y mediciones**, segmento la métrica por la propiedad:


![Establecer la agrupación, a continuación, seleccione la propiedad en Agrupar por](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)



**En diagnóstico búsqueda**, puede ver las propiedades y métricas de repeticiones individuales de un evento.


![Seleccione una instancia y luego seleccione '...'](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)


Use el campo de búsqueda para ver las apariciones de evento con un valor de propiedad concreto.


![Escriba un término en la búsqueda](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Más información sobre las expresiones de búsqueda][diagnostic].

#### <a name="alternative-way-to-set-properties-and-metrics"></a>Forma alternativa para establecer propiedades y mediciones

Si es más cómodo, puede recopilar los parámetros de un evento en un objeto independiente:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [AZURE.WARNING] No volver a usar la misma instancia del elemento de telemetría (`event` en este ejemplo) para llamar a Track*() varias veces. Esto puede ocasionar telemetría se envíen con una configuración incorrecta.



## <a name="timed"></a>Eventos de intervalos

Que le gustaría cuánto tiempo tarda para realizar alguna acción del gráfico. Por ejemplo, tal vez desee saber cuánto usuarios tomar para tener en cuenta las opciones en un juego. Este es un ejemplo útil de usos de los parámetros de medición.


*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="defaults"></a>Propiedades predeterminadas de telemetría personalizado

Si desea establecer los valores de propiedad predeterminados para algunos de los eventos personalizados que se escribe, puede establecerlos en un TelemetryClient. Se adjuntan a cada elemento de telemetría enviado desde el cliente. 

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");
    
*VB*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);
    
    gameTelemetry.TrackEvent("WinGame");


    
Llamadas de telemetría individuales pueden reemplazar los valores predeterminados de sus diccionarios de propiedad.

**Los clientes web para JavaScript**, [utilizar a inicializadores de telemetría de JavaScript](#js-initializer)

**Para agregar propiedades de telemetría todos los** incluidos los datos de módulos de la colección estándar, [implementar `ITelemetryInitializer` ](app-insights-api-filtering-sampling.md#add-properties).


## <a name="sampling-filtering-and-processing-telemetry"></a>Muestreo, filtrado y procesamiento de telemetría 

Puede escribir código para procesar la telemetría antes de que se envía desde el SDK. El proceso incluye los datos enviados desde los módulos de telemetría estándar como colección de solicitud HTTP y colección de dependencia.

* [Agregar propiedades](app-insights-api-filtering-sampling.md#add-properties) a telemetría implementando `ITelemetryInitializer` : por ejemplo agregar versión números o valores calculan de otras propiedades. 
* Puede modificar o descartar telemetría antes de se envía desde el SDK implementando [filtrado](app-insights-api-filtering-sampling.md#filtering) `ITelemetryProcesor`. Controlar lo que se envían o se descartan, pero debe tener en cuenta el efecto en las métricas. Dependiendo de cómo descartar elementos, puede perder la capacidad de desplazarse entre los elementos relacionados.
* [El muestreo](app-insights-api-filtering-sampling.md#sampling) es una solución para reducir el volumen de los datos enviados desde la aplicación en el portal. Lo hace sin que ello afecte la métrica mostrada y sin afectar a su capacidad para diagnosticar problemas por navegar entre los elementos relacionados como excepciones, las convocatorias y las vistas de página.

[Aprende más](app-insights-api-filtering-sampling.md)


## <a name="disabling-telemetry"></a>Deshabilitación de telemetría

Para **iniciar y detener dinámicamente** la colección y transmisión de telemetría:

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Para **Deshabilitar seleccionados selectores estándares** - por ejemplo, contadores de rendimiento, las solicitudes HTTP o dependencias - eliminar o comentar las líneas correspondientes en [ApplicationInsights.config][config]. Puede hacerlo, por ejemplo, si desea enviar sus propios datos TrackRequest.

## <a name="debug"></a>Modo para programadores

Durante la depuración, es útil tener la telemetría acelerado a través de la canalización de modo que puede ver los resultados inmediatamente. Puede obtener también mensajes adicionales que le ayudarán a hacer un seguimiento cualquier problema con la telemetría. Desactivarlo en producción, tal como puede ralentizar la aplicación.


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a>Establecer la clave de instrumentación de telemetría personalizado seleccionado

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a>Tecla de instrumentación dinámica

Para evitar la mezcla de telemetría en entornos de desarrollo, prueba y producción, puede [crear distintos de los recursos de aplicación perspectivas] [ create] y cambiar sus claves según el entorno.

En lugar de obtener la clave de instrumentación del archivo de configuración, puede establecer en el código. Establecer la clave en un método de inicialización, como global.aspx.cs en un servicio ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey; 



En páginas web, que podría querer establecer desde el servidor web estado, en lugar de codificación de literalmente en la secuencia de comandos. Por ejemplo, en una página web generados en una aplicación de ASP.NET:

*JavaScript en Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient tiene una propiedad de contexto, que contiene un número de valores que se envían junto con todos los datos de telemetría. Normalmente se establecen los módulos de telemetría estándar, pero también puede establecer usted mismo. Por ejemplo:

    telemetry.Context.Operation.Name = "MyOperationName";

Si establece cualquiera de estos valores usted mismo, considere la posibilidad de quitar la línea correspondiente de [ApplicationInsights.config][config], de modo que no confundir a los valores y los valores estándares.

* **Componente** Identifica la aplicación y su versión
* **Dispositivo** Datos acerca del dispositivo en el que se está ejecutando la aplicación (en las aplicaciones web, éste es el dispositivo de servidor o cliente desde el que se envía la telemetría)
* **InstrumentationKey** Identifica el recurso de información de la aplicación en Azure dónde aparecerá la telemetría. Recoger normalmente ApplicationInsights.config
* **Ubicación** Identifica la ubicación geográfica del dispositivo.
* **Operación** En aplicaciones web, la solicitud HTTP actual. En otros tipos de aplicación, puede establecer esto a eventos de grupo juntos.
 * **Identificador**: un valor generado que se corresponda eventos diferentes, por lo que inspeccionar cualquier evento de diagnóstico de búsqueda, puede encontrar "elementos relacionados"
 * **Nombre**: un identificador, normalmente la dirección URL de la solicitud HTTP. 
 * **SyntheticSource**: si no nulo o está vacío, esta cadena indica que el origen de la solicitud se ha identificado como una prueba de robot o web. De forma predeterminada se excluyen de los cálculos en el Explorador de métricas.
* **Propiedades** Propiedades que se envían con todos los datos de telemetría. Se puede reemplazar en control * llamadas individuales.
* **Sesión** Identifica la sesión del usuario. El identificador se establece en un valor generado, que se cambia cuando el usuario no ha estado activo durante un tiempo.
* **Usuario** Información de usuario. 

## <a name="limits"></a>Límites


[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

*¿Cómo puedo evitar alcanzar el límite de velocidad de datos?*

* Utilizar [muestreo](app-insights-sampling.md).

*¿Cuánto tiempo mantiene datos?*

* Consulte la [declaración de privacidad y retención de datos][data].


## <a name="reference-docs"></a>Documentos de referencia

* [Referencia ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referencia de Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Referencia de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [SDK Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)


## <a name="sdk-code"></a>Código SDK

* [SDK principal de ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [5 DE ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Paquetes de Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Todas las plataformas](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Preguntas

* *¿Qué excepciones podrían producir llamadas Track_()?*
    
    Ninguno. No es necesario ajustarlos en cláusulas try capturas. Si el SDK detecta problemas, registrará mensajes que se muestra en el resultado de la consola de depuración, y - si los mensajes se obtiene a través de-en búsqueda de diagnóstico.



* *¿Hay una API de REST obtener datos desde el portal?*

    Sí, próximamente. Mientras tanto, use [Exportar continuo](app-insights-export-telemetry.md).

## <a name="next"></a>Pasos siguientes


[Buscar eventos y registros][diagnostic]

[Ejemplos y tutoriales](app-insights-code-samples.md)

[Solución de problemas][qna]


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

 
