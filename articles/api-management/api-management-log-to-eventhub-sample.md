<properties
    pageTitle="Supervisar la API con la administración de la API de Azure, Hubs de evento y Runscope"
    description="Aplicación de ejemplo que muestra la directiva de registro para eventhub conexión administración de la API de Azure, Hubs de evento de Azure y Runscope de registro y la supervisión de HTTP"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Supervisar la API con la administración de la API de Azure, Hubs de evento y Runscope

El [servicio de administración de la API](api-management-key-concepts.md) ofrece muchas funciones para mejorar el procesamiento de solicitudes HTTP enviadas a la API de HTTP. Sin embargo, la existencia de las convocatorias y respuestas son temporales. Se realiza la solicitud y pase a través del servicio de administración de la API a la API de back-end. La API procesa la solicitud y una respuesta devuelta a través de al consumidor API. El servicio de administración de la API mantiene algunas estadísticas importantes acerca de las API para que se muestre en el panel de portal de Publisher, pero algo más que, desaparecen los detalles.

Mediante el uso de la [Directiva](api-management-howto-policies.md) de [registro para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) en el servicio de administración de la API puede enviar los detalles de la solicitud y la respuesta a un [Concentrador de evento de Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Existen varios motivos por qué desea generar eventos de los mensajes HTTP enviados a la API. Algunos ejemplos de auditoría de actualizaciones, análisis de uso, excepción alertas y 3er integraciones de terceros.   

En este artículo se muestra cómo capturar el mensaje de convocatoria y respuesta HTTP completo, enviar a un concentrador de evento y, a continuación, transmitir ese mensaje a un servicio que proporciona HTTP registro y la supervisión de servicios de terceros.

## <a name="why-send-from-api-management-service"></a>¿Por qué se envía desde servicio de administración de la API?
Es posible escribir software intermedio HTTP que se puede conectar a marcos de HTTP API para capturar solicitudes y respuestas HTTP y fuente de en el registro y la supervisión de sistemas. La desventaja de este enfoque es el software intermedio HTTP debe estar integrado en el servidor de la API y debe coincidir con la plataforma de la API. Si hay varias API cada uno de ellos debe implementar el software intermedio. A menudo hay motivos por qué no se pueden actualizar las API de back-end.

Mediante el servicio de administración de la API de Azure integrar con la infraestructura de registro proporciona una solución centralizada e independiente de la plataforma. También es scalable en parte debido a las capacidades de [replicación geo](api-management-howto-deploy-multi-region.md) de administración de la API de Azure.

## <a name="why-send-to-an-azure-event-hub"></a>¿Por qué se envía a un concentrador de evento de Azure?
Es razonable pregunte, ¿por qué crear una directiva específica de Azure evento Hubs? Hay muchos orígenes distintos, donde puede ser conveniente mi solicitudes de registro. ¿Por qué no enviar solo las solicitudes directamente al destino final?  Es una opción. Sin embargo, cuando se realizan las solicitudes de registro de un servicio de administración de la API, es necesario tener en cuenta cómo afectará al rendimiento de la API de registro de mensajes. Aumento gradual de carga puede corregirse aumentando instancias disponibles de componentes del sistema o aprovechando geo replicación. Sin embargo, breves picos de tráfico pueden provocar solicitudes de retrasarse considerablemente si las solicitudes de infraestructura de registro iniciar lentas bajo carga.

Los centros de evento de Azure está diseñado para grandes volúmenes de entrada de datos, con capacidad para tratar con un número de eventos mucho mayor que el número de solicitudes HTTP proceso la mayoría de las API. El concentrador de evento actúa como un tipo de búfer sofisticado entre el servicio de administración de la API y la infraestructura que almacenará y procesar los mensajes. Así se garantiza que no se verá afectado el rendimiento de la API debido a la infraestructura de registro.  

Una vez que se ha pasado los datos a un concentrador de eventos que se conserva y esperará los consumidores de concentrador de evento para que lo procese. El concentrador de eventos no importa cómo se procesará, solo se preocupa que asegurarse de que el mensaje se entregarán correctamente.     

Evento Hubs tienen la capacidad de eventos de secuencia a varios grupos de consumidores. Esto permite eventos procese sistemas completamente diferentes. Esto permite compatible con muchos de los escenarios de integración sin colocar retrasos de suma en el procesamiento de la solicitud de API dentro del servicio de administración de la API como un único evento es necesario generar.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Una directiva para enviar mensajes de la aplicación/http
Un concentrador de evento acepta los datos del evento como una cadena simple. El contenido de esa cadena es completamente suya. Para poder empaquetar una solicitud HTTP y envíelo a evento Hubs debemos dar formato a la cadena con la información de solicitud o respuesta. En situaciones como esta, si hay un formato existente nos podemos reutilizar, tenemos podemos escribir nuestro propio código de análisis. Inicialmente considera con la [HAR](http://www.softwareishard.com/blog/har-12-spec/) para enviar solicitudes HTTP y las respuestas. Sin embargo, este formato está optimizado para almacenar una secuencia de solicitudes HTTP en un formato JSON según. Contenía un número de elementos obligatorio que agrega complejidad innecesaria para el escenario de pasar el mensaje HTTP durante la conexión.  

Una opción alternativa era utilizar la `application/http` tipo de medio, como se describe en la especificación de HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Este tipo de medio utiliza el mismo formato que se utiliza para enviar mensajes HTTP durante la conexión, pero el mensaje completo se pueden colocar en el cuerpo de otra solicitud HTTP. En nuestro caso sólo vamos a usar el cuerpo como nuestro mensaje para enviar a Hubs de evento. Afortunadamente, hay un analizador que existe en las bibliotecas de [Cliente de Microsoft ASP.NET Web API 2.2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) que pueden analizar este formato y conviértala en nativo `HttpRequestMessage` y `HttpResponseMessage` objetos.

Para poder crear este mensaje que debemos aprovechar las ventajas de C# según [las expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx) de administración de la API de Azure. Aquí es la directiva que envía un mensaje de solicitud HTTP a Azure evento Hubs.

       <log-to-eventhub logger-id="conferencelogger" partition-id="0">
       @{
           var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                       context.Request.Method,
                                                       context.Request.Url.Path + context.Request.Url.QueryString);

           var body = context.Request.Body?.As<string>(true);
           if (body != null && body.Length > 1024)
           {
               body = body.Substring(0, 1024);
           }

           var headers = context.Request.Headers
                                  .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                  .ToArray<string>();

           var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

           return "request:"   + context.Variables["message-id"] + "\n"
                               + requestLine + headerString + "\r\n" + body;
       }
       </log-to-eventhub>

### <a name="policy-declaration"></a>Declaración de directiva
Hay algunas cosas determinados mencionar acerca de esta expresión de la directiva. La directiva de registro para eventhub tiene un atributo denominado identificador de registrador que hace referencia el nombre del registrador que se ha creado en el servicio de administración de la API. Los detalles de cómo configurar un registrador de concentrador de evento en el servicio de administración de la API pueden encontrarse en el documento [cómo registrar eventos a Azure evento Hubs en administración de la API de Azure](api-management-howto-log-event-hubs.md). El segundo atributo es un parámetro opcional que indica que Hubs de evento que particiones para almacenar el mensaje en. Evento Hubs utilizan particiones para habilitar escalabilidad y requiere un mínimo de dos. Sólo se garantiza la entrega solicitada de mensajes dentro de una partición. Si no se indica en qué partición para colocar el mensaje, utiliza un algoritmo de turnos para distribuir la carga de concentrador de evento. Sin embargo, pueden producirse algunos de los mensajes que deben procesarse en el orden correcto.  

### <a name="partitions"></a>Particiones
Para garantizar que los mensajes se envíen a los consumidores en orden y aprovechar las ventajas de la función de distribución de carga de particiones, que elegí enviar mensajes de solicitud HTTP a una partición y mensajes de respuesta HTTP en una segunda partición. Esto asegurará de una distribución de carga y nos podemos garantizar que todas las solicitudes se utilizará en orden y todas las respuestas se utilizarán en orden. Es posible que una respuesta a consumir antes de la solicitud correspondiente, pero como que no es un problema ya tenemos un mecanismo diferente para correlación solicita a las respuestas y sabemos que solicitudes siempre están antes que las respuestas.

### <a name="http-payloads"></a>Cargas HTTP
Después de crear el `requestLine` podemos comprobar si debe ser trunca el cuerpo de la solicitud. El cuerpo de la solicitud se trunca a 1024 solo. Esto podría aumentar, sin embargo, los mensajes de evento concentrador individuales están limitados a 256KB, por lo que es probable que algunos mensajes HTTP organismos will no cabe en un único mensaje. Cuando se realiza el registro y el análisis de una gran cantidad de información puede derivarse simplemente la línea de solicitud HTTP y los encabezados. Además, muchas solicitudes de API solo vuelva organismos pequeños y por la pérdida de valor de la información por truncar grandes organismos es bastante mínima en comparación con la reducción de transferencia, procesamiento y los costos de almacenamiento para mantener todo el contenido de cuerpo. Una nota final sobre el procesamiento del cuerpo es que necesitamos pasar `true` a la que<string>método () porque estamos leyendo el contenido del cuerpo, pero era también desee la API para que pueda leer el cuerpo de back-end. Pasando true para este método se producen el cuerpo para almacenar en búfer para que se pueda leer una segunda vez. Esto es importante tener en cuenta si tiene una API que no carga de archivos grandes o utiliza el sondeo larga. En estos casos sería mejor evitar la lectura del cuerpo en absoluto.   

### <a name="http-headers"></a>Encabezados HTTP
Encabezados HTTP pueden transferirse simplemente por en el formato del mensaje en un formato de par clave/valor. Hemos optado por franja de determinados campos confidencial de seguridad, para evitar la pérdida de innecesariamente información de credenciales. No es probable que se usaría claves API y otras credenciales para fines de análisis. Si se desea hacer el análisis en el usuario y el producto concreto están usando, a continuación, que podríamos hacerlo desde la `context` objeto y agregar el mensaje.     
### <a name="message-metadata"></a>Metadatos de mensaje
Para generar el mensaje completo para enviar al concentrador de evento, la primera línea no es realmente en parte de la `application/http` mensaje. La primera línea es metadatos adicionales que consta de si el mensaje es una solicitud o mensaje de respuesta y un identificador de mensaje que se utiliza para relacionar solicitudes a las respuestas. El identificador del mensaje se crea mediante otra directiva es similar a esta:

    <set-variable name="message-id" value="@(Guid.NewGuid())" />

Nos podemos que creó el mensaje de solicitud, almacenado en una variable hasta que se devuelve la respuesta y se envían a continuación, simplemente la solicitud y la respuesta como un solo mensaje. Sin embargo, enviar la solicitud y la respuesta de forma independiente y usando un identificador de mensaje para relacionar los dos, obtenemos un poco más flexibilidad en el tamaño del mensaje, la capacidad de aprovechar varias particiones mientras mantiene el orden de los mensajes y la solicitud aparecerá en nuestro panel de información de registro antes. También puede haber algunos escenarios donde nunca se envía una respuesta válida en el hub de evento, posiblemente debido a un error de solicitud grave en el servicio de administración de la API, pero todavía tendrá un registro de la solicitud.

La directiva para enviar el mensaje de respuesta HTTP es muy similar a la solicitud y por tanto la configuración de directiva completa es similar a esta:

      <policies>
        <inbound>
            <set-variable name="message-id" value="@(Guid.NewGuid())" />
            <log-to-eventhub logger-id="conferencelogger" partition-id="0">
              @{
                  var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                              context.Request.Method,
                                                              context.Request.Url.Path + context.Request.Url.QueryString);

                  var body = context.Request.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Request.Headers
                                       .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                       .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                       .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "request:"   + context.Variables["message-id"] + "\n"
                                      + requestLine + headerString + "\r\n" + body;
              }
          </log-to-eventhub>
        </inbound>
        <backend>
            <forward-request follow-redirects="true" />
        </backend>
        <outbound>
            <log-to-eventhub logger-id="conferencelogger" partition-id="1">
              @{
                  var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                                      context.Response.StatusCode,
                                                      context.Response.StatusReason);

                  var body = context.Response.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Response.Headers
                                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                                  .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "response:"  + context.Variables["message-id"] + "\n"
                                      + statusLine + headerString + "\r\n" + body;
             }
          </log-to-eventhub>
        </outbound>
      </policies>

La `set-variable` directiva crea un valor que sea accesible por ambos el `log-to-eventhub` directiva en la `<inbound>` sección y la `<outbound>` sección.  

## <a name="receiving-events-from-event-hubs"></a>Recibir eventos de Hubs de evento
Se reciben eventos de Azure evento concentrador mediante el [protocolo AMQP](http://www.amqp.org/). El equipo de Bus de servicio de Microsoft ha realizado a cliente bibliotecas disponibles para que los eventos mucho más fácil. Existen dos métodos diferentes compatibles, uno que se *Consumidor directa* y la otra está utilizando la `EventProcessorHost` clase. Ejemplos de estos dos enfoques pueden encontrarse en la [Guía de programación de Hubs de evento](../event-hubs/event-hubs-programming-guide.md). Es la versión abreviada de las diferencias, `Direct Consumer` ofrece un control total y el `EventProcessorHost` qué parte del trabajo de fontanería para que pero determinados supuestos acerca de cómo se procesar esos eventos.  

### <a name="eventprocessorhost"></a>EventProcessorHost
En este ejemplo, usaremos el `EventProcessorHost` para simplificar, pero es posible que no es la mejor opción para esta situación particular. `EventProcessorHost`realiza el trabajo de asegurarse de que no tiene que preocuparse por subprocesos dentro de una clase de procesador de evento concreto. Sin embargo, en nuestro ejemplo, estamos simplemente convertir el mensaje a otro formato y lo pase a otro servicio usando un método asincrónico. No es necesario para actualizar el estado compartido y, por tanto, sin riesgo de problemas de subprocesos. Para la mayoría de los escenarios, `EventProcessorHost` es, probablemente, la mejor opción y que resulta ser la opción más fácil.     

### <a name="ieventprocessor"></a>IEventProcessor
El concepto central cuando se usa `EventProcessorHost` es crear una implementación de la `IEventProcessor` interfaz que contiene el método `ProcessEventAsync`. A continuación se muestra la esencia de ese método:

  asincrónico {IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages) tareas

           foreach (EventData eventData in messages)
           {
               _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

               try
               {
                   var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
                   await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
               }
               catch (Exception ex)
               {
                   _Logger.LogError(ex.Message);
               }
           }
            ... checkpointing code snipped ...
        }

Una lista de objetos EventData se pasan al método y se iteración en esa lista. Se analizan los bytes de cada método en un objeto HttpMessage y ese objeto se pasa a una instancia de IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
La `HttpMessage` instancia contiene tres tipos de datos:

      public class HttpMessage
       {
           public Guid MessageId { get; set; }
           public bool IsRequest { get; set; }
           public HttpRequestMessage HttpRequestMessage { get; set; }
           public HttpResponseMessage HttpResponseMessage { get; set; }

        ... parsing code snipped ...

      }

La `HttpMessage` instancia contiene un `MessageId` GUID que nos permite conectarse la solicitud HTTP a la respuesta HTTP correspondiente y un valor booleano que identifica si el objeto contiene una instancia de un HttpRequestMessage y HttpResponseMessage. Usando integrada en clases HTTP de `System.Net.Http`, puede aprovechar las ventajas de la `application/http` analizar código que se incluye en `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
La `HttpMessage` instancia se reenvía a la implementación de `IHttpMessageProcessor` que es una interfaz que he creado para desacoplar la recepción y la interpretación del evento de concentrador de evento de Azure y el procesamiento real de la misma.


## <a name="forwarding-the-http-message"></a>Reenviar el mensaje HTTP
En este ejemplo, decidido sería interesante insertar la solicitud HTTP sobre en [Runscope](http://www.runscope.com). Runscope es un servicio de basada en la nube que está especializado en HTTP de depuración, registro y la supervisión. Tienen un nivel gratuito, por lo que es fácil probar y nos permite ver las solicitudes HTTP en fluye en tiempo real a través de nuestro servicio de administración de la API.

La `IHttpMessageProcessor` implementación este aspecto:

      public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
       {
           private HttpClient _HttpClient;
           private ILogger _Logger;
           private string _BucketKey;
           public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
           {
               _HttpClient = httpClient;
               var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
               _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
               _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
               _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
               _Logger = logger;
           }

           public async Task ProcessHttpMessage(HttpMessage message)
           {
               var runscopeMessage = new RunscopeMessage()
               {
                   UniqueIdentifier = message.MessageId
               };

               if (message.IsRequest)
               {
                   _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
                   runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
               }
               else
               {
                   _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
                   runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
               }

               var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
               messagesLink.BucketKey = _BucketKey;
               messagesLink.RunscopeMessage = runscopeMessage;
               var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
               _Logger.LogDebug("Request sent to Runscope");
           }
       }

Puede aprovechar las ventajas de una [biblioteca de cliente existente para Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) que facilita la inserción `HttpRequestMessage` y `HttpResponseMessage` instancias hacia arriba en el servicio. Para obtener acceso a la API Runscope necesitará una cuenta y una tecla de la API. Encontrará instrucciones para obtener una clave API en la demostración de la [Creación de aplicaciones de la API de acceso a Runscope](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) .

## <a name="complete-sample"></a>Ejemplo completo
El [código fuente](https://github.com/darrelmiller/ApimEventProcessor) y pruebas para los datos de ejemplo se encuentran en Github. Necesitará un [Servicio de administración de la API](api-management-get-started.md), [Un concentrador conectado de evento](api-management-howto-log-event-hubs.md)y una [Cuenta de almacenamiento](../storage/storage-create-storage-account.md) para ejecutar el ejemplo para usted.   

El ejemplo es solo una aplicación de consola simple que escucha los eventos procedentes de concentrador de evento, las convierte en una `HttpRequestMessage` y `HttpResponseMessage` objetos y, a continuación, los reenvía a la API Runscope.

En la siguiente imagen animada, puede ver una solicitud de que se realizan en una API en el Portal de programadores, la aplicación de consola que muestra el mensaje que se recibe, procesa y reenvía y, a continuación, la convocatoria y respuesta mostrarse en el inspector de tráfico Runscope.

![Demostración de solicitud se envíen a Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Resumen
Servicio de administración de la API de Azure ofrece un lugar ideal para capturar el tráfico HTTP a y desde la API de viaje. Azure Hubs de evento es una solución altamente scalable, bajo costo para capturar el tráfico y la alimentación en los sistemas de procesamiento secundario para el registro, supervisión y otros análisis sofisticados. Conectarse a 3ª tráfico de fiesta supervisar sistemas como Runscope es tan simple como unas docenas líneas de código.

## <a name="next-steps"></a>Pasos siguientes
-   Obtenga más información sobre Hubs de evento de Azure
    -   [Introducción a Azure evento Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Recibir mensajes con EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Guía de programación de Hubs de evento](../event-hubs/event-hubs-programming-guide.md)
-   Obtenga más información sobre la integración de administración de la API y Hubs de evento
    -   [Cómo iniciar eventos en Azure evento Hubs en administración de la API de Azure](api-management-howto-log-event-hubs.md)
    -   [Referencia de la entidad de registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [referencia de la directiva de registro para eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    