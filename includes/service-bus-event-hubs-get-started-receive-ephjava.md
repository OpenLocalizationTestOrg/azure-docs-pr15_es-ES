## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Recibir mensajes con EventProcessorHost en Java

EventProcessorHost es una clase de Java que simplifica reciba los eventos de evento Hubs por administrar los controles persistentes y paralelo recibe de esos Hubs de evento. Con EventProcessorHost puede dividir eventos entre varios receptores, incluso cuando está alojado en distintos nodos. Este ejemplo muestra cómo usar EventProcessorHost para un receptor único.

###<a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Para utilizar EventProcessorHost, debe tener una [cuenta de almacenamiento de Azure][]:

1. Inicie sesión en el [portal de clásico de Azure][]y haga clic en **nuevo** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de datos**, **almacenamiento**, a continuación, **Crear rápido**y, a continuación, escriba un nombre para su cuenta de almacenamiento. Seleccione la región que desee y, a continuación, haga clic en **Crear cuenta de almacenamiento**.

    ![][11]

3. Haga clic en la cuenta de almacenamiento recién creado y, a continuación, haga clic en **Administrar las teclas de acceso**:

    ![][12]

    Copie la clave de acceso principal para usar más adelante en este tutorial.

###<a name="create-a-java-project-using-the-eventprocessor-host"></a>Crear un proyecto de Java con el EventProcessor Host

La biblioteca de cliente de Java para Hubs de evento está disponible para su uso en proyectos de experto en el [Repositorio Central de experto en][Maven Package]y se puede hacer referencia mediante la siguiente declaración de dependencia dentro de su archivo de proyecto experto:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Para diferentes tipos de entornos de compilación, puede obtener explícitamente los archivos de JAR más recientes publicados del [Repositorio Central de experto] [ Maven Package] o desde [el punto de distribución de la versión en GitHub](https://github.com/Azure/azure-event-hubs/releases).  

1. Para el ejemplo siguiente, primero cree un nuevo proyecto de experto para una aplicación de consola/shell en su entorno de desarrollo de Java favorito. Se llamará a la clase ```ErrorNotificationHandler```.     

    ``` Java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```

2. Usar el siguiente código para crear una nueva clase denominada ```EventProcessor```.

    ```Java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;

    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
                
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```

3. Crear una clase final llamada ```EventProcessorSample```, utilizando el código siguiente.

    ```Java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;

    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";

            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
            
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
            
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
            
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }

            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
                
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
            
            System.out.println("End of sample");
        }
    }
    ```

4. Reemplazar los campos siguientes con los valores que utilizó cuando creó el concentrador de evento y una cuenta de almacenamiento.

    ``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";

    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";

    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [AZURE.NOTE] En este tutorial se usa una instancia de EventProcessorHost. Para aumentar el rendimiento, se recomienda que ejecute varias instancias de EventProcessorHost. En estos casos, varias instancias automáticamente coordinan entre sí con el fin de equilibrio de carga los eventos recibidos. Si desea varios receptores cada proceso de *todos* los eventos, debe utilizar el concepto de **ConsumerGroup** . Al recibir eventos de equipos diferentes, puede ser útil para especificar nombres de instancias de EventProcessorHost basándose en los equipos (o funciones) que se implementan.

<!-- Links -->
[Event Hubs overview]: ../articles/event-hubs/event-hubs-overview.md
[Cuenta de almacenamiento de Azure]: ../articles/storage/storage-create-storage-account.md
[Portal de clásico de Azure]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

