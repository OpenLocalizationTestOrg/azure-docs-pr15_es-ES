<properties 
    pageTitle="¿Qué es el SDK de WebJobs de Azure" 
    description="Una introducción sobre el SDK de WebJobs de Azure. Explica el SDK novedades, escenarios típicos es útil para y ejemplos de código." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="what-is-the-azure-webjobs-sdk"></a>¿Qué es el SDK de WebJobs de Azure

## <a id="overview"></a>Información general

En este artículo se explica qué es el SDK WebJobs, revisa algunos escenarios comunes es útil para y proporciona información general sobre cómo usar en el código.

[WebJobs](websites-webjobs-resources.md) es una característica del servicio de aplicación de Azure que le permite ejecutar un programa o script en el mismo contexto como una aplicación web, la aplicación de API o la aplicación móvil. El propósito del [SDK WebJobs](websites-webjobs-resources.md) es simplificar el código que escriba para las tareas comunes que un WebJob puede llevar a cabo, como el procesamiento de imagen, la cola de procesamiento, RSS agregación, mantenimiento del archivo y enviar correos electrónicos. El SDK WebJobs tiene características integradas para trabajar con el almacenamiento de Azure y Bus de servicio, para programar tareas y tratamiento de errores y muchos otros escenarios comunes. Además, se ha diseñado para que sea extensible. [WebJobs SDK es Abrir origen](https://github.com/Azure/azure-webjobs-sdk/), y no hay un [repositorio de origen para las extensiones de abrir](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

El SDK de WebJobs incluye los siguientes componentes:

* **Paquetes de NuGet**. Paquetes de NuGet que agregue a un proyecto de aplicación de consola de Visual Studio proporcionan un marco que usa el código mediante la decoración de los métodos de atributos WebJobs SDK.
  
* **Panel**. Parte del SDK WebJobs se incluye en el servicio de aplicación de Azure y proporciona supervisión enriquecidos y diagnóstico para programas que usan los paquetes de NuGet. No tienes que escribir el código para usar estas características de supervisión y diagnóstico.

## <a id="scenarios"></a>Escenarios

Estos son algunos escenarios típicos que puede controlar más fácilmente con el SDK de WebJobs de Azure:

* Imagen del procesamiento u otro trabajo intensivo de la CPU. Una característica común de los sitios Web es la capacidad de cargar imágenes o vídeos. Con qué frecuencia desea manipular el contenido una vez que se carga, pero no desea hacer que el usuario espere mientras lo haga.

* Procesamiento de cola. Una forma común de un front-end de web para comunicarse con un servicio de back-end es usar colas. Cuando el sitio Web necesita trabajar, inserta un mensaje en una cola. Un servicio de back-end extrae los mensajes de la cola y realiza el trabajo. Podría usar colas para el procesamiento de imagen: por ejemplo, cuando el usuario carga un número de archivos, coloque los nombres de archivo en un mensaje de la cola y recoger el back-end para el procesamiento. O bien, puede usar colas para mejorar la capacidad de respuesta del sitio. Por ejemplo, en lugar de escribir directamente en una base de datos SQL, escribir una cola, decir al usuario que haya finalizado y permitir que las bases de datos de latencia alta de back-end servicio controlador a trabajar. Para obtener un ejemplo de cola de procesamiento de proceso de imágenes, consulte el [tutorial WebJobs SDK Introducción](websites-dotnet-webjobs-sdk-get-started.md).

* Agregación de RSS. Si tiene un sitio que mantiene una lista de fuentes RSS, puede extraer en todos los artículos de las fuentes de un proceso de fondo.

* Archivo de mantenimiento, como agregar o limpiar los archivos de registro.  Es posible que tenga los archivos de registro se crea por varios sitios o independiente períodos de tiempo que desea combinar para ejecutar trabajos de análisis en ellas. O bien, desea programar una tarea para ejecutar semanal para limpiar los archivos de registro antiguo.

* Entrada en tablas de Azure. Podría tener los archivos almacenados y BLOB y su análisis y almacenar los datos en tablas. La función de entrada puede escribir una gran cantidad de filas (millones en algunos casos) y el SDK WebJobs hace posible implementar esta funcionalidad fácilmente. El SDK también proporciona la supervisión en tiempo real de los indicadores de progreso, como el número de filas que se escriben en la tabla.

* Otras tareas de ejecución larga que desea ejecutar en un subproceso en segundo plano, como [Enviar correos electrónicos](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure). 

* Las tareas que desea ejecutar en una programación, por ejemplo, realizar una operación de copia de seguridad cada noche.

En muchos de estos escenarios desea ajustar el tamaño de una aplicación web para que se ejecute en varias VM, que podrían usar varios WebJobs simultáneamente. En algunos casos el resultado podría ser los mismos datos que se procesan varias veces, pero esto no es un problema cuando se usa la cola integrado, blob y desencadenadores de Bus de servicio de SDK WebJobs. El SDK garantiza que las funciones se procesará solo una vez para cada mensaje o blob.

El SDK WebJobs también facilita manejar escenarios de control de errores comunes. Puede configurar alertas para enviar notificaciones cuando se produce un error en una función, y puede establecer tiempos de espera para que una función se cancela automáticamente si no se completa dentro de un límite de tiempo especificado.

## <a id="code"></a>Ejemplos de código

El código para controlar las tareas típicas que funcionan con el almacenamiento de Azure es sencillo. En la aplicación de consola `Main` método crea un `JobHost` objeto que las llamadas a métodos que escriba las coordenadas. El marco de trabajo WebJobs SDK sabe cuándo llamar a los métodos y qué valores de parámetro para utilizar basan en los atributos de SDK WebJobs que usar en ellos. El SDK proporciona *desencadenadores* que especifique qué condiciones hacen que la función llamar y *carpetas* que especifique cómo obtener información dentro y fuera de los parámetros de método.

Por ejemplo, el atributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) hace que una función que se llama cuando se recibe un mensaje de una cola y el formato de mensaje es JSON para un tipo personalizado o de una matriz de bytes, el mensaje se deserializa automáticamente. El atributo [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) activa un proceso cuando se cree un nuevo blob en una cuenta de almacenamiento de Azure.

Aquí es un programa sencillo que explora una cola y crea un blob para cada mensaje de cola recibido:

        public static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

La `JobHost` objeto es un contenedor para un conjunto de funciones en segundo plano. La `JobHost` objeto supervisa las funciones, inspecciones para los eventos que se activan, y ejecuta las funciones cuando se producen eventos de desencadenador. Llamar a un `JobHost` método para indicar si desea que el proceso de contenedor para ejecutar en el subproceso actual o un subproceso en segundo plano. En el ejemplo, el `RunAndBlock` método ejecuta el proceso de forma continua en el subproceso actual.

Dado que la `ProcessQueueMessage` método en este ejemplo tiene un `QueueTrigger` atributo el desencadenador de esa función es la recepción de un nuevo mensaje de cola. La `JobHost` objeto busca nuevos mensajes de cola en la cola especificada ("webjobsqueue" en este ejemplo) y cuando se encuentra una, llama `ProcessQueueMessage`. 

La `QueueTrigger` atributo enlaza la `inputText` parámetro para el valor de la cola de mensajes. Y la `Blob` atributo enlaza un `TextWriter` objeto a un blob denominado "blobname" en un contenedor denominado "nombre del contenedor".  

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)

A continuación, la función utiliza estos parámetros para escribir el valor de la cola de mensajes en el blob:

        writer.WriteLine(inputText);

Las características de desencadenador y cuaderno del SDK WebJobs simplifican en gran medida el código que tiene que escribir. El código de bajo nivel necesario para procesar colas, BLOB o archivos, o para iniciar las tareas programadas está hecho Framework SDK WebJobs. Por ejemplo, el marco de trabajo crea colas que no existen aún, se abre la cola, lecturas cola mensajes, eliminaciones en cola mensajes al procesamiento se haya completado, crea contenedores de blob no existen aún, escribe BLOB y así sucesivamente.

En el ejemplo siguiente se muestra una variedad de desencadenadores en una WebJob: `QueueTrigger`, `FileTrigger`, `WebHookTrigger`, y `ErrorTrigger`. 

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a>Programación

La `TimerTrigger` atributo le permite a las funciones de desencadenador para que se ejecute en una programación. Puede programar un WebJob completo a través de Azure o programación funciones individuales de un WebJob mediante el SDK WebJobs `TimerTrigger`. Este es un ejemplo de código.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Para obtener más código de ejemplo, vea [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) en el repositorio de las extensiones azure-webjobs-sdk en GitHub.com.

## <a name="extensibility"></a>Capacidad de ampliación

No está limitado a la funcionalidad integrada: el SDK WebJobs le permite escribir desencadenadores personalizados y carpetas.  Por ejemplo, puede escribir desencadenadores de eventos de caché y programaciones periódicas. Un [Abra repositorio de origen](https://github.com/Azure/azure-webjobs-sdk-extensions) contiene un [Guía detallada de la capacidad de ampliación de WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) y código de ejemplo para ayudar a empezar a escribir sus propios desencadenadores y carpetas.

## <a id="workerrole"></a>Mediante el SDK WebJobs fuera WebJobs

Un programa que usa el SDK WebJobs es una aplicación de consola estándar y se pueden ejecutar en cualquier lugar: no tiene que se ejecute como un WebJob. Puede probar el programa localmente en el equipo de desarrollo y en producción se puede ejecutar en una función de trabajo del servicio de nube o un servicio de Windows si prefiere uno de esos entornos. 

Sin embargo, los paneles solo está disponible como una extensión de una aplicación web de servicio de aplicaciones de Azure. Si desea ejecutar fuera de un WebJob y seguirá pudiendo usar el panel, puede configurar una aplicación web para usar la misma cuenta de almacenamiento que hace referencia la cadena de conexión WebJobs SDK paneles y WebJobs paneles de la aplicación web se muestre datos sobre la ejecución de la función desde el programa que se está ejecutando en otro lugar. Puede ir a los paneles usando la dirección URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Para obtener más información, vea [Introducción a un panel para el desarrollo local con el SDK de WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), pero tenga en cuenta que la entrada de blog muestra un nombre de cadena de conexión anterior. 

## <a id="nostorage"></a>Características de paneles

El SDK de WebJobs proporciona varias ventajas, incluso si no usa desencadenadores WebJobs SDK o carpetas:

* Puede invocar funciones del panel.
* Puede reproducir las funciones del panel.
* Puede ver registros en el panel, vinculado a la WebJob particular (registros de aplicación, escritos mediante Console.Out, Console.Error, seguimiento, etc.) o vinculado a la llamada de función en particular que las generó (registros escritos mediante un `TextWriter` objeto que el SDK pasa a la función como un parámetro). 

Para obtener más información, consulte [cómo invocar manualmente una función](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) y [cómo escribir registros](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs) 

## <a id="nextsteps"></a>Pasos siguientes

Para obtener más información sobre el SDK WebJobs, vea [Azure WebJobs recomienda recursos](http://go.microsoft.com/fwlink/?linkid=390226).

Para obtener información sobre las últimas mejoras en el SDK de WebJobs, consulte las [Notas de la versión](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).
 
