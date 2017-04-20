<properties 
    pageTitle="Cómo utilizar Bus de servicio de Azure con el SDK WebJobs" 
    description="Aprenda a utilizar colas de Bus de servicio de Azure y temas en el SDK de WebJobs." 
    services="app-service\web, service-bus" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>Cómo utilizar Bus de servicio de Azure con el SDK WebJobs

## <a name="overview"></a>Información general

Esta guía proporciona ejemplos de código de C# que muestran cómo desencadenar un proceso cuando se recibe un mensaje de Bus de servicio de Azure. Los ejemplos de código utilizan [WebJobs SDK](websites-dotnet-webjobs-sdk.md) versión 1.x.

La guía se supone que sabe [cómo crear un proyecto WebJob en Visual Studio con las cadenas de conexión que apunten a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md).

Los fragmentos de código mostrar solo las funciones, no el código que crea la `JobHost` objeto como en este ejemplo:

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

Un [ejemplo de código completo de Bus de servicio](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) se encuentra en el repositorio de azure webjobs sdk muestras de GitHub.com.

## <a id="prerequisites"></a>Requisitos previos

Para trabajar con Bus de servicio debe instalar el paquete de NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) además de los otros paquetes WebJobs SDK. 

También debe establecer la cadena de conexión AzureWebJobsServiceBus además de las cadenas de conexión de almacenamiento.  Puede hacerlo en la `connectionStrings` sección del archivo App.config, tal como se muestra en el ejemplo siguiente:

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

Para un proyecto de ejemplo que incluye la configuración de cadena de conexión de Bus de servicio en el archivo App.config, vea [ejemplo de Bus de servicio](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus). 

Las cadenas de conexión también se pueden configurar en el entorno de tiempo de ejecución de Azure, que, a continuación, reemplaza la configuración de App.config cuando el WebJob se ejecuta en Azure; Para obtener más información, vea [Empezar a trabajar con el SDK de WebJobs](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account).

## <a id="trigger"></a>Cómo activar una función cuando se recibe un mensaje de cola de Bus de servicio

Para escribir una función que el SDK WebJobs llama cuando se recibe un mensaje de cola, utilice el `ServiceBusTrigger` atributo. El constructor de atributo toma un parámetro que especifica el nombre de la cola de sondeo.

### <a name="how-servicebustrigger-works"></a>Cómo funciona ServiceBusTrigger

El SDK recibe un mensaje de `PeekLock` modo y llamadas `Complete` en el mensaje si la función finaliza correctamente, o llamadas `Abandon` si se produce un error en la función. Si la función se ejecuta más de la `PeekLock` tiempo de espera, el bloqueo se renovará automáticamente.

Bus de servicio hace su propio control de colas de daños que no controla ni configura el SDK WebJobs. 

### <a name="string-queue-message"></a>Mensaje de la cola de cadena

El ejemplo siguiente lee un mensaje de cola que contiene una cadena y escribe la cadena en el panel de WebJobs SDK.

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**Nota:** Si va a crear la cola de mensajes en una aplicación que no utilice el SDK WebJobs, asegúrese de establecer [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) en "texto sin formato".

### <a name="poco-queue-message"></a>Mensaje de cola POCO

El SDK deserializará automáticamente un mensaje de cola que contiene JSON para un POCO [(sin formato objeto CLR antiguo](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) tipo. El ejemplo siguiente lee un mensaje de cola que contiene un `BlobInformation` objeto que tiene un `BlobName` propiedad:

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Para obtener ejemplos de código que muestra cómo usar las propiedades de la POCO para trabajar con BLOB y tablas en la misma función, vea la [versión de colas de almacenamiento de este artículo](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Si el código que crea el mensaje de cola no usa el SDK WebJobs, use código similar al siguiente ejemplo:

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>Tipos ServiceBusTrigger funciona con

Además `string` y tipos POCO, puede usar el `ServiceBusTrigger` atributo con una matriz de bytes o un `BrokeredMessage` objeto.

## <a id="create"></a>Cómo crear mensajes de cola de Bus de servicio

Escribir una función que crea una nueva utilización de mensaje de cola la `ServiceBus` atributo y pase el nombre de cola al constructor del atributo. 


### <a name="create-a-single-queue-message-in-a-non-async-function"></a>Crear un mensaje de cola único en una función no asincrónica

El ejemplo siguiente utiliza un parámetro de salida para crear un nuevo mensaje en la cola denominada "outputqueue" con el mismo contenido que el mensaje recibido en la cola denominada "inputqueue".

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

El parámetro de salida para crear un mensaje de cola solo puede representar cualquiera de los siguientes tipos:

* `string`
* `byte[]`
* `BrokeredMessage`
* Tipo POCO serializable que defina. Serie automáticamente como JSON.

Para los parámetros de tipo POCO, siempre se crea un mensaje de la cola cuando finaliza la función; Si el parámetro es null, el SDK crea un mensaje de cola que devolverá null cuando el mensaje se recibe y deserializa. Para los demás tipos, si el parámetro es nulo no se crea ningún mensaje de cola.

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>Crear varios mensajes de cola o en las funciones asíncronas

Para crear varios mensajes, use la `ServiceBus` atributo con `ICollector<T>` o `IAsyncCollector<T>`, como se muestra en el ejemplo siguiente:

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Cada mensaje de cola se crea inmediatamente cuando el `Add` se denomina método.

## <a id="topics"></a>Cómo trabajar con temas de Bus de servicio

Para escribir una función que el SDK llama cuando se recibe un mensaje sobre un tema de Bus de servicio, utilice la `ServiceBusTrigger` atributo con el constructor que toma el nombre de tema y nombre de la suscripción, como se muestra en el ejemplo siguiente:

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

Para crear un mensaje sobre un tema, utilice la `ServiceBus` atributo con un nombre de tema del mismo modo que use con un nombre de cola.

## <a name="features-added-in-release-11"></a>Funciones de agregado en la versión 1.1

Las siguientes características se agregaron en la versión 1.1:

* Permitir la personalización en profundidad del procesamiento a través de mensajes `ServiceBusConfiguration.MessagingProvider`.
* `MessagingProvider`admite la personalización del servicio Bus `MessagingFactory` y `NamespaceManager`.
* A `MessageProcessor` patrón de estrategia le permite especificar un procesador por cola/tema.
* Simultaneidad de procesamiento de mensaje es compatible de forma predeterminada. 
* Personalización sencilla de `OnMessageOptions` mediante `ServiceBusConfiguration.MessageOptions`.
* Permitir [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) especificarlo en `ServiceBusTriggerAttribute` / `ServiceBusAttribute` (para escenarios donde puede que no tenga derechos de administración). 

## <a id="queues"></a>Temas relacionados cubiertos por el artículo de procedimientos de colas de almacenamiento

Para obtener información acerca de los escenarios de WebJobs SDK específicas de Bus de servicio, vea [cómo usar el almacenamiento de Azure cola con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Temas de este artículo son las siguientes:

* Funciones de asincrónica
* Instancias múltiples
* Se está saliendo
* Usar atributos de WebJobs SDK en el cuerpo de una función
* Establecer las cadenas de conexión de SDK en código
* Establecer los valores para SDK WebJobs parámetros de constructor en el código
* Desencadenar una función manualmente
* Escribir registros

## <a id="nextsteps"></a>Pasos siguientes

Esta guía proporciona ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con Bus de servicio de Azure. Para obtener más información sobre cómo usar WebJobs de Azure y el SDK WebJobs, vea [Azure WebJobs recomienda recursos](http://go.microsoft.com/fwlink/?linkid=390226).
 
