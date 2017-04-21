<properties 
    pageTitle="Información general sobre las API de Hubs evento Azure | Microsoft Azure"
    description="Un resumen de algunas de la API de cliente de evento Hubs .NET clave."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm" />

# <a name="event-hubs-api-overview"></a>Introducción a la API de Hubs evento

Este artículo resume algunas de la API de cliente de evento Hubs .NET claves. Existen dos categorías: administración y las API de tiempo de ejecución. API de tiempo de ejecución constan de todas las operaciones necesarias para enviar y recibir un mensaje. Operaciones de administración le permiten administrar un estado de la entidad de evento Hubs por crear, actualizar y eliminar entidades.

Escenarios de supervisión abarcan administración y tiempo de ejecución. Documentación de referencia detallada sobre las API de .NET, vea las referencias [.NET de Bus de servicio](https://msdn.microsoft.com/library/azure/mt419900.aspx) y [Las API de EventProcessorHost](https://msdn.microsoft.com/library/azure/mt445521.aspx) .

## <a name="management-apis"></a>API de administración

Para realizar las siguientes operaciones de administración, debe tener permisos de **administración** en el espacio de nombres de evento Hubs:

### <a name="create"></a>Crear

```
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### <a name="update"></a>Actualización

```
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### <a name="delete"></a>Eliminar

```
namespaceManager.DeleteEventHubAsync("Event Hub name").Wait();
```

## <a name="run-time-apis"></a>API de tiempo de ejecución

### <a name="create-publisher"></a>Crear publicación

```
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("Event Hub name");
```

### <a name="publish-message"></a>Publicar el mensaje

```
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Crear consumidor

```
// Create the Event Hubs client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
EventHubConsumerGroup defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));
                        
// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Utilizar mensaje

```
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)
                                    
// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Host de procesador API de eventos

Estas API ofrecen la resistencia de procesos de trabajo que no esté disponible, mediante la distribución shards entre los trabajadores disponibles.

```
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

La interfaz de [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) se define como sigue:

```
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }
        
        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de los escenarios de evento Hubs, visite estos vínculos:

- [¿Qué es Hubs de evento de Azure?](event-hubs-what-is-event-hubs.md)
- [Información general de Hubs de evento](event-hubs-overview.md)
- [Guía de programación de Hubs de evento](event-hubs-programming-guide.md)
- [Ejemplos de código de evento Hubs](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hubs&f[0].Type=SearchText&ac=5)

Las referencias de la API de .NET son aquí:

- [Referencias de Bus de servicio y API de evento Hubs .NET](https://msdn.microsoft.com/library/azure/mt419900.aspx)
- [Host de procesador referencia de la API de eventos](https://msdn.microsoft.com/library/azure/mt445521.aspx)
