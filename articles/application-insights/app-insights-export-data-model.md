<properties 
    pageTitle="Modelo de datos de aplicación perspectivas" 
    description="Describe las propiedades exportado desde exportar continua en JSON y usar como filtros." 
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
    ms.date="03/21/2016" 
    ms.author="awills"/>

# <a name="application-insights-export-data-model"></a>Modelo de datos de aplicación perspectivas exportar

Esta tabla enumeran las propiedades de telemetría enviados desde el SDK de [Perspectivas de aplicación](app-insights-overview.md) en el portal. Verá estas propiedades en los resultados de datos de [Exportar continuo](app-insights-export-telemetry.md).
También aparecerán en filtros de propiedades en el [Explorador de métrica](app-insights-metrics-explorer.md) y [Diagnóstico de búsqueda](app-insights-diagnostic-search.md).

Puntos que tenga en cuenta:

* `[0]`en estas tablas denota un punto en la ruta de acceso donde se debe insertar un índice; pero no siempre es 0.
* Duraciones de tiempo que se encuentran en décimas de un microsegundo, así que 10000000 == 1 segundo.
* Fechas y horas son UTC y que se indican en el formato ISO`yyyy-MM-DDThh:mm:ss.sssZ`

Existen varios [ejemplos](app-insights-export-telemetry.md#code-samples) que muestran cómo usarlas.



## <a name="example"></a>Ejemplo

    // A server report about an HTTP request
    {
    "request": [ 
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": "" 
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events 
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0", 
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0, 
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }




## <a name="context"></a>Contexto

Todos los tipos de telemetría se acompañan una sección de contexto. Todos estos campos no se transmiten con cada punto de datos.



|Ruta de acceso|Tipo|Notas|
|---|---|---|
| Context.Custom.Dimensions [0]  | [] de objeto  | Pares de cadenas de clave y valor establecidos por el parámetro de propiedades personalizadas. Longitud máxima de clave 100, los valores de longitud máxima de 1024. Más de 100 valores únicos, la propiedad se puede realizar búsqueda, pero no se pueden usar para la segmentación de. Teclas de máximo de 200 por ikey.  |
| Context.Custom.Metrics [0]  | [] de objeto  | Pares de valor de clave se establece mediante el parámetro de medidas personalizadas y TrackMetrics. Clave longitud máxima de 100, puede valores numéricos. |
| context.data.eventTime | cadena | UTC |
| context.data.isSynthetic | valor booleano | Solicitud parece que proviene de una prueba de robot o web. |
| context.data.samplingRate | número | Porcentaje de telemetría generado por el SDK que se envía al portal. Intervalo 0,0 100,0.|
| Context.Device | objeto | Dispositivo cliente |
| Context.Device.Browser | cadena | Internet Explorer, Chrome... |
| context.device.browserVersion | cadena | Chrome 48.0... |
| context.device.deviceModel | cadena | |
| context.device.deviceName | cadena | |
| Context.Device.Id | cadena | |
| Context.Device.Locale | cadena | en GB, de-DE... |
| Context.Device.Network | cadena | |
| context.device.oemName | cadena | |
| context.device.osVersion | cadena | Sistema operativo host |
| context.device.roleInstance | cadena | Identificador de host del servidor |
| context.device.roleName | cadena | |
| Context.Device.Type | cadena | PC, explorador... |
| Context.Location | objeto | Deriva IPCliente. |
| Context.Location.City | cadena | Derivado de IPCliente, si se conoce  |
| Context.Location.ClientIP | cadena | Última octágono es anonymized en 0. |
| Context.Location.Continent | cadena | |
| Context.Location.Country | cadena | |
| Context.Location.Province | cadena | Estado o provincia |
| Context.Operation.Id | cadena | Elementos que tienen el mismo identificador de operación se muestran como elementos relacionados en el portal. Normalmente, el identificador de solicitud. |
| Context.Operation.Name | cadena | nombre de dirección URL o la solicitud |
| context.operation.parentId | cadena | Permite anidadas elementos relacionados. |
| Context.Session.Id | cadena | Identificador de un grupo de operaciones de la misma fuente. Un plazo de 30 minutos sin una operación indica el final de una sesión. |
| context.session.isFirst | valor booleano | |
| context.user.accountAcquisitionDate | cadena | |
| context.user.anonAcquisitionDate | cadena | |
| context.user.anonId | cadena | |
| context.user.authAcquisitionDate | cadena | [Usuario autenticado](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated | valor booleano | |
| internal.data.documentVersion | cadena | |
| internal.Data.Id | cadena | |



## <a name="events"></a>Eventos

Eventos personalizados generados por [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event). 


|Ruta de acceso|Tipo|Notas|
|---|---|---|
| número de eventos [0] | entero | 100 /[(muestreo)](app-insights-sampling.md) . Por ejemplo, 4 =&gt; 25%. |
| nombre de evento [0] | cadena | Nombre del evento.  Longitud máxima 250. |
| dirección url de evento [0] | cadena | |
| evento [0] urlData.base | cadena | |
| evento [0] urlData.host | cadena | |

## <a name="exceptions"></a>Excepciones

Informes de [excepciones](app-insights-asp-net-exceptions.md) en el servidor y en el explorador. 


|Ruta de acceso|Tipo|Notas|
|---|---|---|
| ensamblado basicException [0] | cadena | |
| recuento de basicException [0] | entero | 100 /[(muestreo)](app-insights-sampling.md) . Por ejemplo, 4 =&gt; 25%. |
| exceptionGroup basicException [0] | cadena | |
| exceptionType basicException [0] | cadena | |cadena | |
| failedUserCodeMethod basicException [0] | cadena | |
| failedUserCodeAssembly basicException [0] | cadena | |
| handledAt basicException [0] | cadena | |
| hasFullStack basicException [0] | valor booleano | |
| Id. de basicException [0] | cadena | |
| método de basicException [0] | cadena | |
| mensaje de basicException [0] | cadena | Mensaje de excepción. Longitud máxima 10k.|
| outerExceptionMessage basicException [0] | cadena | |
| outerExceptionThrownAtAssembly basicException [0] | cadena | |
| outerExceptionThrownAtMethod basicException [0] | cadena | |
| outerExceptionType basicException [0] | cadena | |
| outerId basicException [0] | cadena | |
| basicException [0] parsedStack [0] ensamblado | cadena | |
| nombre de archivo de basicException [0] parsedStack [0] | cadena | |
| nivel de parsedStack [0] basicException [0] | entero | |
| línea de parsedStack [0] basicException [0] | entero | |
| método de parsedStack [0] basicException [0] | cadena | |
| pila de basicException [0] | cadena | Longitud máxima 10k|
| basicException [0] typeName | cadena | |



## <a name="trace-messages"></a>Seguimiento de mensajes

Enviado por [TrackTrace](app-insights-api-custom-events-metrics.md#track-trace)y el [registro de adaptadores](app-insights-asp-net-trace-logs.md).


|Ruta de acceso|Tipo|Notas|
|---|---|---|
| nombreDeRegistrador mensaje [0] | cadena ||
| parámetros de mensaje [0] | cadena ||
| mensaje [0] sin formato | cadena | El mensaje de registro, la longitud máxima de 10k. |
| mensaje [0] nivelDeGravedad | cadena | |



## <a name="remote-dependency"></a>Dependencia remoto

Enviar por TrackDependency. Usado para el rendimiento de informes y el uso de las [llamadas a las dependencias](app-insights-asp-net-dependencies.md) en el servidor y llamadas de AJAX en el explorador.

|Ruta de acceso|Tipo|Notas|
|---|---|---|
| remoteDependency [0] asincrónica | valor booleano | |
| nombre de base de remoteDependency [0] | cadena |  |
| commandName remoteDependency [0] | cadena | Por ejemplo "inicio o índice" |
| recuento de remoteDependency [0] | entero | 100 /[(muestreo)](app-insights-sampling.md) . Por ejemplo, 4 =&gt; 25%. |
| dependencyTypeName remoteDependency [0] | cadena | HTTP, SQL... |
| durationMetric.value remoteDependency [0] | número | Tiempo de llamada de finalización de una respuesta de dependencia |
| Id. de remoteDependency [0] | cadena | |
| nombre de remoteDependency [0] | cadena | Dirección URL. Longitud máxima 250.|
| resultCode remoteDependency [0] | cadena | de dependencia HTTP |
| éxito remoteDependency [0] | valor booleano | |
| tipo de remoteDependency [0] | cadena | HTTP, Sql... |
| dirección url de remoteDependency [0] | cadena |  Longitud máxima 2000 |
| urlData.base remoteDependency [0] | cadena | Longitud máxima 2000 |
| urlData.hashTag remoteDependency [0] | cadena | |
| urlData.host remoteDependency [0] | cadena | Longitud máxima 200 |


## <a name="requests"></a>Solicitudes

Enviar por [TrackRequest](app-insights-api-custom-events-metrics.md#track-request). Los módulos estándar usa para el tiempo de respuesta del servidor de informes, medida en el servidor. 


|Ruta de acceso|Tipo|Notas|
|---|---|---|
| recuento de solicitud [0] | entero | 100 /[(muestreo)](app-insights-sampling.md) . Por ejemplo: 4 =&gt; 25%. |
| solicitar durationMetric.value [0] | número | Hora de la solicitud llega a la respuesta. 1e7 == 1s |
| Id. de solicitud [0] | cadena | Id. de operación |
| nombre de la solicitud [0] | cadena | OBTENER/entrada + dirección url base.  Longitud máxima 250 |
| solicitar responseCode [0] | entero | Respuesta HTTP enviado al cliente |
| solicitar éxito [0] | valor booleano | Predeterminado == (responseCode &lt; 400) |
| url de la solicitud [0] | cadena | Sin incluir el host |
| solicitar urlData.base [0] | cadena | |
| solicitar urlData.hashTag [0] | cadena |  |
| solicitar urlData.host [0] | cadena | |


## <a name="page-view-performance"></a>Rendimiento de la vista de página

Enviado por el explorador. Mide el tiempo para procesar una página de usuario que inicia la solicitud para mostrar completa (excluidas las llamadas de AJAX asincrónico).

Valores de contexto muestran SO cliente y versión del explorador. 


|Ruta de acceso|Tipo|Notas|
|---|---|---|
| clientProcess.value clientPerformance [0] | entero | Hora de finalización de recibir el código HTML para mostrar la página. |
| nombre de clientPerformance [0] | cadena | |
| networkConnection.value clientPerformance [0] | entero | Tiempo necesario para establecer una conexión de red. |
| receiveRequest.value clientPerformance [0] | entero | Hora de finalización de enviar la solicitud al recibir el código HTML en la respuesta. |
| sendRequest.value clientPerformance [0] | entero | Tiempo de necesario para enviar la solicitud HTTP. |
| total.value clientPerformance [0] | entero | Hora de inicio enviar la solicitud para mostrar la página. |
| dirección url de clientPerformance [0] | cadena | Dirección URL de esta solicitud |
| urlData.base clientPerformance [0] | cadena | |
| urlData.hashTag clientPerformance [0] | cadena | |
| urlData.host clientPerformance [0] | cadena | |
| urlData.protocol clientPerformance [0] | cadena | |

## <a name="page-views"></a>Vistas de página

Enviar por trackPageView() o [stopTrackPage](app-insights-api-custom-events-metrics.md#page-view)

|Ruta de acceso|Tipo|Notas|
|---|---|---|
| recuento de vista [0] | entero | 100 /[(muestreo)](app-insights-sampling.md) . Por ejemplo, 4 =&gt; 25%. |
| Ver durationMetric.value [0] | entero | Valor opcionalmente establecido en trackPageView() o startTrackPage() - stopTrackPage(). No es lo mismo como valores de clientPerformance. |
| nombre de la vista [0] | cadena | Título de la página.  Longitud máxima 250 |
| dirección url de la vista [0] | cadena | |
| Ver urlData.base [0] | cadena | |
| Ver urlData.hashTag [0] | cadena | |
| Ver urlData.host [0] | cadena | |



## <a name="availability"></a>Disponibilidad

Informes de [pruebas de disponibilidad web](app-insights-monitor-web-app-availability.md).

|Ruta de acceso|Tipo|Notas|
|---|---|---|
| disponibilidad [0] availabilityMetric.name | cadena | disponibilidad |
| disponibilidad [0] availabilityMetric.value | número |1.0 o 0,0 |
| recuento de disponibilidad [0] | entero | 100 /[(muestreo)](app-insights-sampling.md) . Por ejemplo, 4 =&gt; 25%. |
| disponibilidad [0] dataSizeMetric.name | cadena | |
| disponibilidad [0] dataSizeMetric.value | entero | |
| disponibilidad [0] durationMetric.name | cadena | |
| disponibilidad [0] durationMetric.value | número | Duración de prueba. 1e7 == 1s |
| mensaje de disponibilidad [0] | cadena | Error de diagnóstico |
| resultado de disponibilidad [0] | cadena | Superar |
| disponibilidad [0] runLocation | cadena | Origen geográfico de solicitud de http |
| disponibilidad [0] nombrePrueba | cadena | |
| disponibilidad [0] testRunId | cadena | |
| disponibilidad [0] testTimestamp | cadena | |




## <a name="metrics"></a>Métricas

Generado por TrackMetric().

El valor de métrica se encuentra en context.custom.metrics[0]

Por ejemplo:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Acerca de los valores de métrica

Métricos valores, tanto en los informes de métrica y en otro sitio, se indican con una estructura de objeto estándar. Por ejemplo:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Actualmente: aunque esto podría cambiar en el futuro, en todos los valores informado de los módulos SDK estándar, `count==1` y solo la `name` y `value` campos son útiles. El único caso donde podría ser diferentes sería si escribir sus propias llamadas TrackMetric en que se establecen los otros parámetros. 

La finalidad de los demás campos es permitir métricas agregarse en el SDK, para reducir el tráfico en el portal. Por ejemplo, podría promedio varias lecturas sucesivas antes de enviar cada informe métrica. A continuación, calcular el min, max, desviación estándar y un valor agregado (suma o promedio) y establecer recuento en el número de lecturas representada por el informe. 

En las tablas anteriores, hemos omitido el recuento de campos poco usado, min, max, DesvEst y sampledValue.

En lugar de la agregación previa métricas, puede usar [muestreo](app-insights-sampling.md) si necesita reducir el volumen de telemetría.


### <a name="durations"></a>Duraciones

Excepto donde se indique lo contrario, las duraciones se representan en décimas de un microsegundo, para que 10000000.0 significa 1 segundo.



## <a name="see-also"></a>Vea también

* [Información de la aplicación](app-insights-overview.md) 
* [Exportar continuo](app-insights-export-telemetry.md)
* [Ejemplos de código](app-insights-export-telemetry.md#code-samples)


