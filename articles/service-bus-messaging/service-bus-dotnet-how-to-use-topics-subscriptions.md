<properties
    pageTitle="Usar temas de Bus de servicio con .NET | Microsoft Azure"
    description="Obtenga información sobre cómo usar temas de Bus de servicios y suscripciones con .NET en Azure. Ejemplos de código se escriben para aplicaciones .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Cómo utilizar las suscripciones y temas de Bus de servicio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artículo describe cómo utilizar las suscripciones y temas de Bus de servicio. Los ejemplos se escriben en C# y usan las API de .NET. Los escenarios cubiertos incluyen la creación de temas y suscripciones, crear filtros de suscripción, enviar mensajes a un tema, recibir mensajes de una suscripción y eliminar temas y suscripciones. Para obtener más información sobre temas y suscripciones, vea la sección [pasos siguientes](#next-steps) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configurar la aplicación para utilizar Bus de servicio

Cuando se crea una aplicación que utiliza Bus de servicio, deberá agregar una referencia al ensamblado Bus de servicio e incluir los espacios de nombres correspondiente. La manera más sencilla de hacerlo es descargar el paquete de [NuGet](https://www.nuget.org) adecuado.

## <a name="get-the-service-bus-nuget-package"></a>Obtener el paquete NuGet Bus de servicio

El [paquete de servicio Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) es la manera más sencilla de obtener la API de Bus de servicio y configurar la aplicación con todas las dependencias de la Bus de servicio. Para instalar el paquete de servicio Bus NuGet en su proyecto, haga lo siguiente:

1.  En el Explorador de soluciones, haga clic en **referencias**, haga clic en **Administrar paquetes de NuGet**.
2.  Buscar "Bus de servicio" y seleccione el elemento de **Bus de servicio de Microsoft Azure** . Haga clic en **instalar** para completar la instalación, cierre el cuadro de diálogo siguiente:

    ![][7]

Ya está listo para escribir código para Bus de servicio.

## <a name="create-a-service-bus-connection-string"></a>Crear una cadena de conexión de Bus de servicio

Bus de servicio utiliza una cadena de conexión para almacenar credenciales y extremos. Puede colocar la cadena de conexión en un archivo de configuración, en lugar de codificar:

- Cuando se usa servicios de Azure, se recomienda que almacene la cadena de conexión con el sistema de configuración de servicio de Azure (archivos .csdef y .cscfg).
- Al utilizar sitios Web Azure o máquinas virtuales de Azure, se recomienda que almacene la cadena de conexión con el sistema de configuración de .NET (por ejemplo, el archivo Web.config).

En ambos casos, puede recuperar la cadena de conexión mediante la `CloudConfigurationManager.GetSetting` método, tal como se muestra más adelante en este artículo.

### <a name="configure-your-connection-string"></a>Configurar la cadena de conexión

El mecanismo de configuración de servicio le permite cambiar dinámicamente los parámetros de configuración desde el [portal de Azure][] sin implementar la aplicación. Por ejemplo, agregue un `Setting` etiqueta a su archivo de definición (**.csdef**) de servicio, tal como se muestra en el ejemplo siguiente.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

A continuación, especifique los valores en el archivo de configuración (.cscfg) de servicio.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Use el nombre de clave de firma de acceso compartido (SA) y los valores de clave recuperados desde el portal como se describió anteriormente.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configurar la cadena de conexión al utilizar sitios Web Azure o máquinas virtuales de Azure

Al utilizar sitios Web o máquinas virtuales de Windows, se recomienda que use el sistema de configuración de .NET (por ejemplo, Web.config). Almacenar la cadena de conexión mediante la `<appSettings>` elemento.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Use el nombre de SA y los valores de clave que ha recuperado desde el [portal de Azure][], como se describió anteriormente.

## <a name="create-a-topic"></a>Crear un tema

Puede realizar operaciones de administración para temas de Bus de servicios y suscripciones mediante la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Esta clase proporciona métodos para crear, enumerar y eliminar temas.

El ejemplo siguiente se crea un `NamespaceManager` objeto con la Azure `CloudConfigurationManager` con permisos para administrar las credenciales de clase con una cadena de conexión que consta de la dirección base de un espacio de nombres de Bus de servicio y las SA adecuadas. Esta cadena de conexión tiene el siguiente formato:

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Use el siguiente ejemplo, dados los valores de configuración en la sección anterior.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Hay sobrecargas del método [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) que le permiten establecer las propiedades del tema; Por ejemplo, para establecer el valor predeterminado time to live (TTL) valor que se aplicará a los mensajes enviados al tema. Esta configuración se aplica mediante la clase [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . En el ejemplo siguiente se muestra cómo crear un tema denominado **TestTopic** con un tamaño máximo de 5 GB y un mensaje de forma predeterminada TTL de 1 minuto.

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] Puede usar el método [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) en [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) objetos para comprobar si ya existe un tema con un nombre especificado dentro de un espacio de nombres.

## <a name="create-a-subscription"></a>Crear una suscripción

También puede crear suscripciones a temas mediante la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Las suscripciones se denominan y pueden tener un filtro opcional que restringe el conjunto de mensajes que se pasan a cola virtual de la suscripción.

> [AZURE.IMPORTANT] En el orden de los mensajes que se reciben en una suscripción, debe crear suscripción antes de enviar los mensajes al tema. Si no hay ninguna suscripción a un tema, el tema descarta los mensajes.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Crear una suscripción con el filtro predeterminado (MatchAll)

Si no se especifica ningún filtro cuando se crea una nueva suscripción, el filtro de **MatchAll** es el filtro predeterminado que se utiliza. Al usar el filtro **MatchAll** , todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción denominada "AllMessages" y se usa el filtro de **MatchAll** predeterminado.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Crear suscripciones con los filtros

También puede configurar filtros que le permite especificar qué mensajes enviados a un tema deben aparecer dentro de una suscripción de un tema específico.

El tipo de filtro admitido por suscripciones más flexible es la clase [SqlFilter][] , que implementa un subconjunto de SQL92. Filtros SQL funcionan en las propiedades de los mensajes que se publican en el tema. Para obtener más información sobre las expresiones que se pueden usar con un filtro SQL, vea la sintaxis de la [SqlFilter.SqlExpression][] .

En el ejemplo siguiente se crea una suscripción denominada **HighMessages** con un objeto [SqlFilter][] que solo selecciona los mensajes que tienen una propiedad personalizada del **MessageNumber** superior a 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Asimismo, en el ejemplo siguiente se crea una suscripción denominada **LowMessages** con [SqlFilter][] que solo selecciona mensajes que tienen una propiedad **MessageNumber** o igual a 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Ahora cuando se envía un mensaje a `TestTopic`, siempre se entrega a receptores suscrito a la suscripción de tema **AllMessages** y entregar selectivamente a receptores suscritos a las suscripciones de tema **HighMessages** y **LowMessages** (según el contenido del mensaje).

## <a name="send-messages-to-a-topic"></a>Enviar mensajes a un tema

Para enviar un mensaje a un tema de Bus de servicio, la aplicación crea un objeto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) con la cadena de conexión.

El código siguiente muestra cómo crear un objeto de [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) para el tema de **TestTopic** creado anteriormente con la [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) llamada API.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Los mensajes enviados a temas de Bus de servicio son instancias de la clase [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Objetos de [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) tienen un conjunto de propiedades estándar (como [etiqueta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) y [período de vida](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un diccionario que se utiliza para contener propiedades personalizadas de específicos de la aplicación y un cuerpo de datos de aplicación arbitrario. Una aplicación puede establecer el cuerpo del mensaje pasando cualquier objeto serializable al constructor del objeto [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) y **DataContractSerializer** correspondiente a continuación, se utiliza para serializar el objeto. Como alternativa, puede proporcionar **System.IO.Stream** .

En el ejemplo siguiente se muestra cómo enviar mensajes de prueba cinco al objeto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) **TestTopic** obtenido en el ejemplo anterior. Tenga en cuenta que el valor de propiedad [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) de cada mensaje varía según la iteración del bucle (determina qué suscripciones reciban).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Temas de Bus de servicio compatible con el tamaño máximo de mensaje de 256 KB en el [nivel estándar](service-bus-premium-messaging.md) y 1 MB en el [nivel de Premium](service-bus-premium-messaging.md). El encabezado, que incluye las propiedades de la aplicación estándar y personalizados, puede tener un tamaño máximo de 64 KB. No hay ningún límite del número de mensajes en un tema, pero hay un límite en el tamaño total de los mensajes que llevan a cabo por un tema. El tamaño de este tema se define en la hora de creación, con un límite máximo de 5 GB. Si está habilitada la partición, el límite es superior. Para obtener más información, vea [con particiones mensajería entidades](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Cómo recibir mensajes de una suscripción

La manera recomendada para recibir mensajes de una suscripción es utilizar un objeto [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) . Objetos de [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) pueden trabajar en dos modos: [ *ReceiveAndDelete* y *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Al usar el modo de **ReceiveAndDelete** recibir es una operación de captura solo; es decir, cuando Bus de servicio recibe una solicitud de lectura para un mensaje en una suscripción, marca el mensaje como consumida y devuelve a la aplicación. Modo de **ReceiveAndDelete** es el modelo más sencillo y funciona mejor para escenarios en los que puede tolerar una aplicación no procesar un mensaje si se produce un error. Para comprender esto, considere la posibilidad de un escenario en el que el consumidor envía la solicitud de recepción y, a continuación, se bloquea antes de procesamiento. Porque Bus de servicio ha marcado el mensaje como consumido, cuando la aplicación se reinicia y empieza a utilizar mensajes nuevamente, habrá perdido el mensaje que se ha utilizado antes el bloqueo.

En el modo de **PeekLock** (que es el modo predeterminado), el proceso de recepción se convierte en una operación de dos fases que permite a las aplicaciones de soporte técnico que no pueden tolerar mensajes que falta. Cuando Bus de servicio recibe una solicitud, encuentra el mensaje siguiente consumir, bloquea para evitar que otros consumidores recibirlo y, a continuación, se devuelve a la aplicación. Después de la aplicación termine de procesar el mensaje (o almacena confiable para procesamiento futuro), complete la segunda fase del proceso de recepción llamando [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) en el mensaje recibido. Cuando Bus de servicio detecta el **completado** llamar, marca el mensaje como consumida y se quita de la suscripción.

En el ejemplo siguiente se muestra cómo los mensajes se pueden recibir y procesar utilizando el modo de **PeekLock** predeterminado. Para especificar un valor diferente de [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , puede utilizar otra sobrecarga de [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Este ejemplo usa la devolución de llamada [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) para procesar los mensajes cuando llegan a la suscripción **HighMessages** .

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

En este ejemplo se configura la devolución de llamada [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) con un objeto [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) . [Autocompletar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) se establece en **false** para habilitar el control manual de cuándo llamar [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) en el mensaje recibido. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) está establecido en 1 minuto, lo que hace que el cliente que esperar hasta un minuto antes de terminar la característica de renovación automática y el cliente realiza una llamada nueva para comprobar si hay mensajes. Este valor de propiedad reduce el número de veces que el cliente realiza llamadas facturables que no recuperar mensajes.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Cómo controlar los mensajes no se puede leer y bloqueos de la aplicación

Bus de servicio proporciona funcionalidad para ayudarle a recuperarse correctamente de errores en la aplicación o dificultades para procesar un mensaje. Si una aplicación de recepción no puede procesar el mensaje por algún motivo, puede llamar al método [abandonar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) en el mensaje recibido (en lugar de al método [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ). Esto hace Bus de servicio desbloquear el mensaje dentro de la suscripción y que esté disponible para recibir nuevamente, por la misma aplicación consumir o por otra aplicación consume.

También es un tiempo de espera asociado a un mensaje bloqueado dentro de la suscripción, y si se produce un error en la aplicación procesar el mensaje antes el tiempo de espera de bloqueo caduca (por ejemplo, si se bloquea la aplicación), entonces Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para volver a recibir.

En caso de que la aplicación se bloquea después de procesar el mensaje, pero antes de que haya emitido la solicitud [completado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) , el mensaje se se volverá a entregar a la aplicación cuando se reinicia. A menudo se denomina *procesamiento al menos una vez*; es decir, cada mensaje se procesa al menos una vez, pero en algunos casos es posible volverá a entregar el mismo mensaje. Si el escenario no puede tolerar procesamiento duplicado, los desarrolladores de aplicaciones deben agregar lógica adicional a su aplicación para controlar la entrega de mensajes duplicados. A menudo, esto se logra mediante la propiedad de [Id. de mensaje](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) del mensaje, que se mantiene constante en todos los intentos de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar temas y suscripciones

En el ejemplo siguiente se muestra cómo eliminar el tema **TestTopic** del espacio de nombres de servicio de **HowToSample** .

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Eliminación de un tema, también elimina cualquier suscripciones que están registradas con el tema. Suscripciones también se pueden eliminar de forma independiente. El código siguiente muestra cómo eliminar una suscripción denominada **HighMessages** desde el tema de **TestTopic** .

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de temas de Bus de servicios y suscripciones, siga estos vínculos para obtener más información.

-   [Colas, temas y las suscripciones][].
-   [Ejemplo de filtros de tema][]
-   Referencia de la API para [SqlFilter][].
-   Crear una aplicación de trabajo que envía y recibe mensajes de una cola de Bus de servicio: [Bus de servicio negociada mensajería tutorial .NET][].
-   Ejemplos de Bus de servicio: descargar de [Azure muestras][] o vea la [información general](service-bus-samples.md).

  [Portal de Azure]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
  [Ejemplo de filtros de tema]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Tutorial de Bus de servicio con mensajería .NET]: service-bus-brokered-tutorial-dotnet.md
  [Ejemplos de Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
