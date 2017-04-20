## <a name="receive-messages-with-eventprocessorhost"></a>Recibir mensajes con EventProcessorHost

[EventProcessorHost][] es una clase .NET que simplifica reciba los eventos de evento Hubs por administrar los controles persistentes y paralelo recibe de esos Hubs de evento. Usa [EventProcessorHost][], puede dividir eventos entre varios receptores, incluso cuando está alojado en distintos nodos. Este ejemplo muestra cómo usar [EventProcessorHost][] para un receptor único. En el ejemplo de [procesamiento de escala el evento][] muestra cómo usar [EventProcessorHost][] con varios receptores.

Para utilizar [EventProcessorHost][], debe tener una [cuenta de almacenamiento de Azure][]:

1. Inicie sesión en el [portal de Azure][]y a continuación, haga clic en **nuevo** en la parte superior izquierda de la pantalla.

2. Haga clic en **datos + almacenamiento**y luego haga clic en la **cuenta de almacenamiento**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage1.png)

3. En el módulo **crear cuenta de almacenamiento** , escriba un nombre para la cuenta de almacenamiento. Elija una suscripción Azure, el grupo de recursos y la ubicación en la que desea crear el recurso. A continuación, haga clic en **crear**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage2.png)

4. En la lista de cuentas de almacenamiento, haga clic en la cuenta de almacenamiento recién creado.

5. En el módulo de cuenta de almacenamiento, haga clic en **las teclas de acceso**. Copie el valor de **clave1** usar más adelante en este tutorial.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage3.png)

4. En Visual Studio, cree un nuevo proyecto de aplicación de escritorio de Visual C# mediante la plantilla de proyecto de **Aplicación de consola** . Nombre del **destinatario**del proyecto.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp1.png)

5. En el Explorador de soluciones, haga clic en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet para la solución**.

6. Haga clic en la ficha **Examinar** , busque `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Asegúrese de que el nombre del proyecto (**receptor**) especificado en el cuadro de **versiones** . Haga clic en **instalar**y acepte los términos de uso.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-eph-csharp1.png)

    Visual Studio se descarga, se instala y agrega una referencia al [Concentrador de eventos de Bus de servicio de Azure - paquete EventProcessorHost NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), con todas sus dependencias.

7. Haga clic en el proyecto **receptor** , haga clic en **Agregar**y, a continuación, haga clic en **clase**. Asigne un nombre a la nueva clase **SimpleEventProcessor**y, a continuación, haga clic en **Agregar** para crear la clase.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp2.png)

8. Agregue las siguientes instrucciones en la parte superior del archivo SimpleEventProcessor.cs:

    ```
    using Microsoft.ServiceBus.Messaging;
    using System.Diagnostics;
    ```

    A continuación, sustituya el siguiente código del cuerpo de la clase:

    ```
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());

                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }
    ```

    Esta clase se llamará por la **EventProcessorHost** para procesar los eventos recibidos desde el Hub de evento. Tenga en cuenta que el `SimpleEventProcessor` clase utiliza un cronómetro para llamar periódicamente al método de control en el contexto de **EventProcessorHost** . Así se garantiza que, si se reinicia el receptor, perderá no más de cinco minutos de trabajo.

9. En la clase de **programa** , agregue la siguiente `using` instrucción en la parte superior del archivo:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

    A continuación, sustituya la `Main` método en la `Program` clase con el siguiente código, sustituya el nombre del concentrador de evento y la cadena de conexión en el nivel de espacio de nombres que haya guardado anteriormente, y la cuenta de almacenamiento y clave que copió en las secciones anteriores. 

    ```
    static void Main(string[] args)
    {
      string eventHubConnectionString = "{Event Hub connection string}";
      string eventHubName = "{Event Hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      var options = new EventProcessorOptions();
      options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

> [AZURE.NOTE] En este tutorial se usa una instancia de [EventProcessorHost][]. Para aumentar el rendimiento, se recomienda que ejecute varias instancias de [EventProcessorHost][], como se muestra en el ejemplo de [escala el procesamiento de eventos][] . En estos casos, varias instancias automáticamente coordinan entre sí para equilibrar la carga de los eventos recibidos. Si desea varios receptores cada proceso de *todos* los eventos, debe utilizar el concepto de **ConsumerGroup** . Al recibir eventos de equipos diferentes, puede ser útil para especificar nombres de instancias de [EventProcessorHost][] basándose en los equipos (o funciones) que se implementan. Para obtener más información acerca de estos temas, vea los temas de [Información general de Hubs de evento][] y [Evento Hubs programación manual][] .

<!-- Links -->
[Información general de Hubs de evento]: ../articles/event-hubs/event-hubs-overview.md
[Guía de programación de Hubs de evento]: ../articles/event-hubs/event-hubs-programming-guide.md
[Escalado de procesamiento de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Cuenta de almacenamiento de Azure]: ../articles/storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Portal de Azure]: https://portal.azure.com