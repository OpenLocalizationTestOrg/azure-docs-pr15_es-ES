<properties
   pageTitle="Optimizar el código de Visual Studio Azure | Microsoft Azure"
   description="Más información sobre cómo Azure código herramientas de optimización de Visual Studio ayudan hacer su código más sólida y mejor rendimiento."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="optimizing-your-azure-code"></a>Optimizar el código de Azure

Cuando está programando aplicaciones que utilicen Microsoft Azure, existen algunas prácticas de codificación que debe seguir para ayudar a evitar problemas con escalabilidad de la aplicación, el comportamiento y el rendimiento en un entorno de nube. Microsoft proporciona una herramienta de análisis de código de Azure que reconoce y algunos de estos problemas habituales identifica y le ayuda a resolverlos. Puede descargar la herramienta de Visual Studio mediante NuGet.

## <a name="azure-code-analysis-rules"></a>Reglas de análisis de código de Azure

La herramienta de análisis de código de Azure usa las siguientes reglas para marcar automáticamente el código de Azure cuando encuentre problemas conocidos afectar al rendimiento. Detecta problemas aparecen como un advertencias o errores del compilador. Correcciones de código o sugerencias para resolver el error o advertencia con qué frecuencia se proporcionan a través de un icono de bombilla.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Evitar el uso de modo de estado de sesión (en proceso) predeterminado

### <a name="id"></a>ID.

AP0000

### <a name="description"></a>Descripción

Si utiliza el modo de estado de sesión (en proceso) de forma predeterminada para las aplicaciones de la nube, puede perder el estado de la sesión.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

De forma predeterminada, el modo de estado de sesión especificado en el archivo web.config está en proceso. Además, si ninguna entrada especificada en el archivo de configuración, el modo de estado de la sesión de forma predeterminada en el proceso. El modo en proceso almacena el estado de sesión en memoria en el servidor web. Cuando se reinicia una instancia o una nueva instancia se usa para equilibrio de carga o la capacidad de recuperación, no se guardó el estado de sesión almacenado en la memoria en el servidor web. Esta situación impide que la aplicación está scalable en la nube.

Estado de la sesión ASP.NET es compatible con distintas opciones de almacenamiento para los datos de estado de sesión: proceso, StateServer, SQL Server, personalizado y desactivado. Se recomienda que use en un almacén de estado de sesión externo, como [proveedor de estado de la sesión de Azure para Redis](http://go.microsoft.com/fwlink/?LinkId=401521)modo personalizado a los datos de host.

### <a name="solution"></a>Solución

Es una solución recomendada almacenar el estado de la sesión en un servicio de caché administrada. Aprenda a usar el [proveedor de estado de la sesión de Azure para Redis](http://go.microsoft.com/fwlink/?LinkId=401521) para almacenar el estado de la sesión. También puede almacenar el estado de la sesión en otros sitios para asegurarse de que la aplicación es scalable en la nube. Para obtener más información sobre alternativa soluciones lean [Modos de estado de sesión](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Ejecutar en método no debe ser asincrónica

### <a name="id"></a>ID.

AP1000

### <a name="description"></a>Descripción

Crear métodos asincrónicos (por ejemplo, [espera](https://msdn.microsoft.com/library/hh156528.aspx)) fuera del método [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) y, a continuación, llame a los métodos asincrónico desde [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Declarar el método [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) como asincrónico hace que la función de trabajo escribir un bucle de reinicio.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Llamar a métodos asincrónico dentro del método [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) hace que el tiempo de ejecución del servicio de nube reciclaje de la función de trabajo. Cuando se inicia una función de trabajo, la ejecución del programa todo lleve a cabo dentro del método [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Salir del método [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) hace que la función de trabajo reiniciar. Cuando el tiempo de ejecución de la función trabajador llega del método asincrónico, envía todas las operaciones después del método asincrónico y, a continuación, se devuelve. Hace que la función de trabajo para salir del método [[[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) y reiniciar. En la siguiente iteración de ejecución, la función de trabajo llega del método asincrónico nuevamente y reinicia, provocando la función de trabajo para reciclaje nuevo.

### <a name="solution"></a>Solución

Coloque todas las operaciones asíncronas fuera del método [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . A continuación, llame al método asincrónico refactorizado desde dentro del método [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , como RunAsync () .wait. La herramienta de análisis de código de Azure puede ayudar a solucionar este problema.

Fragmento de código siguiente muestra la corrección del código para este problema:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Usar autenticación de firma de acceso compartido de Bus de servicio

### <a name="id"></a>ID.

AP2000

### <a name="description"></a>Descripción

Uso compartido acceso firma (SA) para la autenticación. Servicio de Control de acceso (ACS) se ha degradado para la autenticación de bus de servicio.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Para mejorar la seguridad, Azure Active Directory va a reemplazar autenticación ACS con autenticación de SA. Para obtener información sobre el plan de transición, consulte [Azure Active Directory es el futuro de ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) .

### <a name="solution"></a>Solución

Utilizar autenticación de SA en las aplicaciones. En el ejemplo siguiente se muestra cómo usar un símbolo de SA existente para tener acceso a un espacio de nombres de bus de servicio o entidad.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Vea los temas siguientes para obtener más información.

- Para obtener información general, vea [Autenticación de firma de acceso compartida con Bus de servicio](https://msdn.microsoft.com/library/dn170477.aspx)

- [Cómo usar autenticación de firma de Access compartida con Bus de servicio](https://msdn.microsoft.com/library/dn205161.aspx)

- Para un proyecto de ejemplo, vea [autenticación de uso compartido acceso firma (SA) con suscripciones de Bus de servicio](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Piense en usar el método OnMessage para evitar "recibir bucle"

### <a name="id"></a>ID.

AP2002

### <a name="description"></a>Descripción

Para evitar que se vayan a "bucle recibir", la llamada al método **OnMessage** es la mejor solución para recibir mensajes de la llamada al método de **recepción** . Sin embargo, si debe utilizar el método de **recepción** y especifique un servidor de tiempo de espera, asegúrese de que el tiempo de espera del servidor es más de un minuto.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Al llamar a **OnMessage**, el cliente inicia un suministro de mensajes interno que explora constantemente la cola o suscripción. Este suministro de mensajes contiene un bucle que emite una llamada a recibir mensajes. Si la llamada agota el tiempo de espera, emite una nueva llamada. El intervalo de tiempo de espera es determinado por el valor de la propiedad [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) de [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)que se utiliza.

La ventaja de usar **OnMessage** con respecto al **recibir** es que los usuarios no tienen sondeos para los mensajes, controlar excepciones, procesar varios mensajes en paralelo y completar los mensajes manualmente.

Si se llama **recepción** sin usar el valor predeterminado, asegúrese de que el valor de *ServerWaitTime* es más de un minuto. Establecer *ServerWaitTime* en más de un minuto impide que el servidor de tiempo de espera antes de que el mensaje completo.

### <a name="solution"></a>Solución

Vea los siguientes ejemplos de código para usos recomendados. Para obtener más detalles, vea QueueClient.OnMessage (Microsoft.ServiceBus.Messaging) y [Método](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx) [QueueClient.Receive (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Para mejorar el rendimiento de la infraestructura de mensajería Azure, consulte el modelo de diseño [Información básica sobre mensajería asincrónica](https://msdn.microsoft.com/library/dn589781.aspx).

A continuación se muestra un ejemplo del uso de **OnMessage** para recibir mensajes.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

A continuación se muestra un ejemplo del uso de **recepción** con el tiempo de espera del servidor de forma predeterminada.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

A continuación se muestra un ejemplo del uso de **recepción** con un tiempo de espera del servidor no predeterminado.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Considere usar métodos de Bus de servicio asincrónicos

### <a name="id"></a>ID.

AP2003

### <a name="description"></a>Descripción

Usar los métodos de Bus de servicio asincrónicos para mejorar el rendimiento con la mensajería con.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

El uso de métodos asincrónicos permite simultaneidad del programa de aplicación porque ejecuta cada llamada no bloquee el subproceso principal. Cuando se usa el Bus de servicios de mensajería métodos, realizar una operación (enviar, recibir, eliminar, etc.) tarda. Este tiempo incluye el procesamiento de la operación por el servicio de Bus de servicio, además de la latencia de la solicitud y la respuesta. Para aumentar el número de operaciones por hora, operaciones deben ejecutar simultáneamente. Para obtener más información, consulte [Prácticas recomendadas para rendimiento mejoras usando Bus negociada mensajería del servicio](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Solución

Para obtener información sobre cómo usar el método asincrónico recomendado, vea [Clase QueueClient (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) .

Para mejorar el rendimiento de la infraestructura de mensajería Azure, consulte el modelo de diseño [Información básica sobre mensajería asincrónica](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Considere la posibilidad de particiones colas de Bus de servicio y temas

### <a name="id"></a>ID.

AP2004

### <a name="description"></a>Descripción

Colas de Bus de servicio de partición y temas para mejorar el rendimiento con la mensajería de Bus de servicio.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Partición colas de Bus de servicio y temas aumenta la disponibilidad de rendimiento y servicios de rendimiento porque el rendimiento general de una cola dividida o tema ya no está limitado por el rendimiento de un agente de mensaje o el almacén de mensajería. Además, un corte temporal de una tienda mensajería no hacer una cola dividida o tema no esté disponible. Para obtener más información, consulte [Particiones entidades de mensajería](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Solución

Fragmento de código siguiente muestra cómo dividir entidades mensajería.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Para obtener más información, vea temas y colas de Bus de servicio particiones [| Blog de Microsoft Azure](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) y desactive la casilla de la muestra de [Microsoft Azure servicio Bus particiones cola](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) .

## <a name="do-not-set-sharedaccessstarttime"></a>No ha establecido SharedAccessStartTime

### <a name="id"></a>ID.

AP3001

### <a name="description"></a>Descripción

Debe evitar el uso de SharedAccessStartTimeset a la hora actual para iniciar inmediatamente la directiva de acceso compartido. Solo debe establecer esta propiedad si desea iniciar la directiva de acceso compartido en un momento posterior.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Sincronización de reloj hace ligeras diferencias entre los centros de datos. Por ejemplo, uno pensaría lógicamente establecer la hora de inicio de una directiva de SA de almacenamiento como la hora actual mediante DateTime.Now o un método similar hará que la directiva de SA surta efecto inmediatamente. Sin embargo, las diferencias de tiempo ligera entre los centros de datos pueden causar problemas con este porque algunas veces del centro de datos pueden ser ligeramente posteriores a la hora de inicio, mientras que otros estén antes. Como resultado, la directiva de SA puede caducar rápidamente (o incluso inmediatamente) si la duración de la directiva se configura demasiado breve.

Para obtener más información sobre el uso compartido de firma de acceso en el almacenamiento de Azure, consulte [Introducción a tabla SA (firma de acceso compartido), SA de cola y actualización Blob SA - Blog del equipo de Microsoft Azure almacenamiento - página de inicio del sitio - Blogs de MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solución

Quite la instrucción que establece la hora de inicio de la directiva de acceso compartido. La herramienta de análisis de código de Azure proporciona una solución para este problema. Para obtener más información sobre la administración de seguridad, consulte el modelo de diseño del [Patrón de clave continuado de ayuda](https://msdn.microsoft.com/library/dn568102.aspx).

Fragmento de código siguiente muestra la corrección del código para este problema.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Compartir la fecha de caducidad debe ser más de cinco minutos de directiva de acceso

### <a name="id"></a>ID.

AP3002

### <a name="description"></a>Descripción

Puede haber como un valor de minuto cinco diferencia de reloj entre los centros de datos en diferentes ubicaciones debido a una condición que se conoce como "reloj." Para evitar que las asociaciones de seguridad token de directiva de expiración antes de lo planeado, establezca la fecha de caducidad sean más de cinco minutos.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Centros de datos en diferentes ubicaciones del mundo sincronizan una señal de reloj. Porque tarda señal de reloj en diferentes ubicaciones de viaje, puede ser una variación de tiempo entre los centros de datos en diferentes ubicaciones geográficas aunque supone todo lo que se sincroniza. Esta diferencia de tiempo puede influir en el intervalo de tiempo y expiración del inicio de directivas de acceso compartido. Por lo tanto, para asegurarse de que la directiva de acceso compartido surte efecto inmediatamente, no especifique la hora de inicio. Además, asegúrese de que la fecha de expiración es más de cinco minutos para evitar que el tiempo de espera inicial.

Para obtener más información sobre el uso compartido de firma de acceso en el almacenamiento de Azure, consulte [Introducción a tabla SA (firma de acceso compartido), SA de cola y actualización Blob SA - Blog del equipo de Microsoft Azure almacenamiento - página de inicio del sitio - Blogs de MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solución

Para obtener más información sobre la administración de seguridad, consulte el modelo de diseño del [Patrón de clave continuado de ayuda](https://msdn.microsoft.com/library/dn568102.aspx).

A continuación se muestra un ejemplo de sin especificar una hora de inicio de la directiva de acceso compartido.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

El siguiente es un ejemplo de especificar una hora de inicio de la directiva de acceso compartido con una duración de expiración de la directiva más de cinco minutos.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Para obtener más información, vea [crear y usar una firma de acceso compartido](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>Usar CloudConfigurationManager

### <a name="id"></a>ID.

AP4000

### <a name="description"></a>Descripción

Uso de la clase [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) para proyectos como sitio Web de Azure y Azure servicios móviles no presente problemas de tiempo de ejecución. Sin embargo, como práctica recomendada, es aconsejable usar nube[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) como una manera unificada de administrar las configuraciones de todas las aplicaciones de nube de Azure.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

CloudConfigurationManager lee el archivo de configuración adecuado para el entorno de aplicación.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Solución

Refactorizar el código para usar la [Clase CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). La herramienta de análisis de código de Azure proporciona una solución de código para este problema.

Fragmento de código siguiente muestra la corrección del código para este problema. Reemplazar

`var settings = ConfigurationManager.AppSettings["mySettings"];`

con

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Aquí es un ejemplo de cómo almacenar el valor de configuración en un archivo App.config o Web.config. Agregue la configuración de la sección appSettings del archivo de configuración. El siguiente es el archivo Web.config de ejemplo de código anterior.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Evitar el uso de cadenas de conexión modificable

### <a name="id"></a>ID.

AP4001

### <a name="description"></a>Descripción

Si usa las cadenas de conexión codificado y debe actualizarlos más adelante, debe realizar cambios en el código fuente y volver a compilar la aplicación. Sin embargo, si almacena las cadenas de conexión en un archivo de configuración, puede cambiarlos más adelante simplemente actualizando el archivo de configuración.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Codificar las cadenas de conexión es una mala práctica porque introduce problemas cuando las cadenas de conexión que necesite cambiar rápidamente. Además, si el proyecto debe revisarse control de código fuente, las cadenas de conexión modificable introducen vulnerabilidad de seguridad ya que las cadenas se pueden visualizar en el código fuente.

### <a name="solution"></a>Solución

Almacenar cadenas de conexión en los archivos de configuración o los entornos de Azure.

- Para las aplicaciones independientes, use app.config para almacenar la configuración de la cadena de conexión.

- Para aplicaciones web alojado en IIS, use web.config para almacenar cadenas de conexión.

- Para las aplicaciones ASP.NET vNext, use configuration.json para almacenar cadenas de conexión.

Para obtener información sobre el uso de archivos de configuraciones como web.config o app.config, vea [Directrices de configuración de Web de ASP.NET](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Para obtener información sobre cómo Azure trabajo de variables de entorno, vea [sitios Web de Azure: cómo las cadenas de la aplicación y cadenas de conexión funciona](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Para obtener información sobre cómo almacenar la cadena de conexión en control de código fuente, vea [evitar colocar información confidencial, como cadenas de conexión en los archivos que se almacenan en el repositorio de código fuente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Archivo de configuración de diagnósticos de uso

### <a name="id"></a>ID.

AP5000

### <a name="description"></a>Descripción

En lugar de configurar la configuración de diagnósticos en el código como con la API de programación de Microsoft.WindowsAzure.Diagnostics, debe configurar la configuración de diagnósticos en el archivo diagnostics.wadcfg. (O diagnostics.wadcfgx si usa Azure SDK 2.5). Haciendo esto, puede cambiar la configuración de diagnósticos sin tener que volver a compilar el código.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Antes de Azure SDK 2,5 (que usa el diagnóstico de Azure 1.3), diagnósticos de Azure (TORUNDA) podría configurarse mediante varios métodos: agregar a blobs de configuración de almacenamiento, utilizando código fundamental, configuración descriptiva o la configuración predeterminada. Sin embargo, la mejor forma de configurar diagnósticos es usar un archivo de configuración de XML (diagnostics.wadcfg o diagnositcs.wadcfgx SDK 2,5 y versiones posteriores) en el proyecto de aplicación. En este método, el archivo diagnostics.wadcfg completamente define la configuración y se puede actualizar y redistribuir como desee. Mezclar el uso del archivo de configuración de diagnostics.wadcfg con los métodos de programación de configuraciones de configuración mediante las clases de [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)o [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)puede provocar confusión. Para obtener más información, vea [inicialización o cambiar configuración de diagnósticos de Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx) .

A partir de 1,3 TORUNDA (incluido con Azure SDK 2,5), ya no es posible utilizar código para configurar diagnósticos. Como resultado, solo puede proporcionar la configuración al aplicar o actualizar la extensión de diagnósticos.

### <a name="solution"></a>Solución

Usar el Diseñador de configuración de diagnósticos para mover la configuración de diagnóstico para el archivo de configuración de diagnósticos (diagnositcs.wadcfg o diagnositcs.wadcfgx SDK 2,5 y versiones posteriores). También se recomienda que instale [Azure SDK 2,5](http://go.microsoft.com/fwlink/?LinkId=513188) y usar la característica de diagnósticos más reciente.

1. En el menú contextual para la función que desee configurar, elija Propiedades y, a continuación, elija la pestaña Configuración.

1. En la sección **diagnóstico** , asegúrese de que está seleccionada la casilla de verificación **Habilitar diagnósticos** .

1. Elija el botón **Configurar** .

  ![Obtener acceso a la opción Habilitar diagnósticos](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Para obtener más información, consulte [Configuración de diagnósticos para servicios de nube de Azure y máquinas virtuales](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) .


## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Evitar declarar objetos DbContext como estático

### <a name="id"></a>ID.

AP6000

### <a name="description"></a>Descripción

Para ahorrar memoria, evitar declarar objetos DBContext como estático.

Comparta sus ideas y comentarios en [comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Motivo

Objetos de DBContext mantenga los resultados de la consulta de cada llamada. No se eliminan los objetos estáticos DBContext hasta que se descarga el dominio de aplicación. Por lo tanto, un objeto de DBContext estático puede consumir grandes cantidades de memoria.

### <a name="solution"></a>Solución

Declarar DBContext como una variable local o un campo de instancia no estático, utilizar para una tarea y, a continuación, deje que se se eliminan después de usar.

La clase de controlador MVC de ejemplo siguiente muestra cómo usar el objeto DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre optimzing y solución de problemas de aplicaciones de Azure, consulte [solucionar problemas de una aplicación web en la aplicación de servicio de Azure con Visual Studio](./app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).
