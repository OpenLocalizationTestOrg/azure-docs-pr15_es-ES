<properties
    pageTitle="Procesar los mensajes de dispositivo a la nube Concentrador IoT (. NET) | Microsoft Azure"
    description="Siga este tutorial para conocer los patrones útiles para procesar los mensajes de concentrador IoT dispositivo a la nube."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/05/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-net"></a>Tutorial: Cómo procesar los mensajes de dispositivo a la nube Concentrador IoT mediante .net

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introducción

Azure concentrador IoT es un servicio totalmente gestionado que permite confiable y comunicaciones seguras de bidireccional entre millones de IoT dispositivos y una aplicación back-end. Otros tutoriales ([Introducción a IoT concentrador] y [enviar mensajes de dispositivo de nube con concentrador IoT][lnk-c2d]) muestran cómo utilizar el dispositivo a la nube y dispositivo de nube mensajería funcionalidad básica del concentrador IoT.

Este tutorial se basa en el código que se muestra en el tutorial de [Introducción a IoT concentrador] y, a continuación, se muestran dos patrones scalable que puede usar para procesar los mensajes de la nube de dispositivo:

- Almacenamiento confiable de mensajes de nube de dispositivo de [almacenamiento de blobs de Windows Azure]. Un escenario común es el análisis de *ruta de acceso y* , en la que almacenar los datos de telemetría BLOB usar como entrada en los procesos de análisis. Estos procesos pueden ser controlados por herramientas como [Generador de datos de Azure] o la pila de [HDInsight (Hadoop)] .

- Procesamiento confiable de mensajes *interactivo* de dispositivo a la nube. Mensajes de la nube de dispositivo son interactivos cuando se les inmediatos desencadenadores para un conjunto de acciones en el servidor de aplicaciones. Por ejemplo, un dispositivo puede enviar un mensaje de alarma que activa la inserción de un vale en un sistema CRM. Por el contrario, los mensajes de *punto de datos* fuente simplemente a un motor de análisis. Por ejemplo, telemetría temperatura desde un dispositivo que se pueden almacenar para su análisis posterior es un mensaje de punto de datos.

Porque IoT concentrador expone un [Evento concentrador][lnk-event-hubs]-extremo compatible para recibir mensajes de nube de dispositivo, en este tutorial se utiliza una instancia de [EventProcessorHost] . Esta instancia:

* Sujeto almacena los mensajes de *punto de datos* en el almacenamiento de blobs de Windows Azure.
* Reenvía *interactiva* mensajes de nube de dispositivo a una [cola de Bus de servicio] de Azure para el procesamiento de inmediato.

Bus de servicio ayuda a garantizar procesamiento confiable de mensajes interactivos, ya que proporciona puntos de comprobación de cada mensaje y la deduplicación basada en la ventana de tiempo.

> [AZURE.NOTE] Una instancia de **EventProcessorHost** es sólo una manera de procesar los mensajes interactivos. Otras opciones son [Azure servicio tela] [ lnk-service-fabric] y [Análisis de secuencia de Azure][lnk-stream-analytics].

Al final de este tutorial, ejecute tres aplicaciones de consola de Windows:

* **SimulatedDevice**, una versión modificada de la aplicación había creado en el tutorial de [Introducción a IoT concentrador] , envía datos punto dispositivo a la nube mensajes cada mensajes de nube de dispositivo segundo e interactivas cada 10 segundos. Esta aplicación utiliza el protocolo AMQP para comunicarse con IoT concentrador.
* **ProcessDeviceToCloudMessages** utiliza la clase [EventProcessorHost] para recuperar mensajes desde el extremo de concentrador-compatible con el evento. A continuación, sujeto almacena mensajes de punto de datos en el almacenamiento de blobs de Windows Azure y reenvía mensajes interactivos a una cola de Bus de servicio.
* **ProcessD2CInteractiveMessages** anule la cola los mensajes de la cola de Bus de servicio interactivos.

> [AZURE.NOTE] Concentrador IoT admite SDK muchas plataformas de dispositivos e idiomas, incluidos C, Java y JavaScript. Para obtener información sobre cómo reemplazar el dispositivo simulado en este tutorial con un dispositivo físico y cómo conectar dispositivos a un concentrador IoT, consulte el [Centro para desarrolladores de Azure IoT].

Este tutorial es directamente aplicable a otras formas de consumir mensajes de evento concentrador compatibles, como los proyectos de [HDInsight (Hadoop)] . Para obtener más información, vea [Guía del desarrollador de Azure IoT concentrador - dispositivo a la nube].

Para completar este tutorial, necesita lo siguiente:

+ Microsoft Visual Studio 2015.

+ Una cuenta de Azure active. <br/>Si no tiene una cuenta, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/) de dos minutos.

Debe tener conocimientos básicos de [Almacenamiento de Azure] y [Azure Bus de servicio].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Enviar mensajes interactivos desde un dispositivo simulado

En esta sección, modifique la aplicación de dispositivo simulado que creó en el tutorial de [Introducción a IoT concentrador] para enviar mensajes de nube de dispositivo interactivos al concentrador IoT.

1. En Visual Studio, en el proyecto **SimulatedDevice** , agregue el método siguiente a la clase **Program** .

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Este método es similar al método **SendDeviceToCloudMessagesAsync** en el proyecto **SimulatedDevice** . Las únicas diferencias son que ahora establecer la propiedad del sistema **Id. de mensaje** y **tipo de mensaje**llama a una propiedad de usuario.
    El código asigna un identificador único global (GUID) a la propiedad de **identificador del mensaje** . El Bus de servicios puede utilizar este identificador para anular duplicar los mensajes que recibe. En el ejemplo se utiliza la propiedad de **tipo de mensaje** para distinguir interactiva de los mensajes de punto de datos. La aplicación pasa esta información en las propiedades del mensaje, en lugar de en el cuerpo del mensaje para que el procesador de eventos no es necesario deserializar el mensaje para realizar el enrutamiento del mensaje.

    > [AZURE.NOTE] Es importante crear el **identificador del mensaje** utilizado para anular duplicar mensajes interactivos en el código del dispositivo. Comunicaciones de red intermitentes u otros errores, podrían provocar varias transmisiones del mismo mensaje de ese dispositivo. También puede usar un identificador de mensaje semántico, como un hash de los campos de datos pertinentes del mensaje, en lugar de un GUID.

2. Agregue el método siguiente en el método **Main** , justo antes de la `Console.ReadLine()` línea:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Para simplificar, este tutorial implementar cualquier directiva de reintento. En el código de producción, debe implementar una directiva de reintento como crecimiento exponencial, como se sugiere en el artículo MSDN [Transitorias tratamiento de errores].

## <a name="process-device-to-cloud-messages"></a>Process mensajes de nube de dispositivo

En esta sección, creará una aplicación de consola de Windows que procesa mensajes de nube de dispositivo de concentrador IoT. Concentrador IOT expone un [concentrador de evento]-extremo compatible para habilitar una aplicación leer mensajes de dispositivo a la nube. Este tutorial utiliza la clase [EventProcessorHost] para procesar estos mensajes en una aplicación de consola. Para obtener más información acerca de cómo procesar los mensajes de evento Hubs, consulte el tutorial de [Introducción a Hubs de evento] .

El reto al implementar almacenamiento confiable de mensajes de punto de datos o reenvío de mensajes interactivos es que el procesamiento de eventos se basa en el consumidor del mensaje para proporcionar los controles para su progreso. Además, para lograr un alto rendimiento, cuando se leen de evento Hubs debe proporcionar los controles en lotes de gran tamaño. Este método crea la posibilidad de procesamiento de duplicados para un gran número de mensajes si hay un error y volver al punto de control anterior. En este tutorial, aprenderá a sincronizar el almacenamiento de Azure escribe y windows desduplicación de Bus de servicio con puntos de control de **EventProcessorHost** .

Para escribir mensajes con el almacenamiento de Azure confiable, el ejemplo usa la característica de confirmación de bloque individual de [bloque BLOB][Azure Block Blobs]. El procesador de eventos acumula mensajes en la memoria hasta que es el momento de ofrecer un punto de control. Por ejemplo, después del búfer de mensajes acumulado alcanza el tamaño máximo de bloque de 4 MB o duplicación de Bus de servicio de la ventana de tiempo transcurre. A continuación, antes del punto de control, el código confirma un nuevo bloque de blob.

Procesador de evento usa Hubs evento desplaza el mensaje como bloque de identificadores. Este mecanismo permite al procesador de evento realizar una comprobación de desduplicación antes de que confirma el nuevo bloque de almacenamiento, con cuidado de un bloqueo posible entre confirmar un bloque y el punto de control.

> [AZURE.NOTE] Este tutorial utiliza una sola cuenta de almacenamiento de Azure para escribir todos los mensajes que se recuperen IoT concentrador. Para decidir si debe utilizar varias cuentas de almacenamiento de Azure en la solución, consulte [escalabilidad de almacenamiento de Azure directrices].

La aplicación usa la característica de desduplicación de Bus de servicio para evitar duplicados cuando procesa mensajes interactivos. El dispositivo simulado marca cada mensaje interactiva con un único **identificador del mensaje**. Estos identificadores habilitar Bus de servicio para asegurarse de que, en la ventana de tiempo especificado desduplicación, dos mensajes con el mismo **Id. de mensaje** no se entregarán a los receptores. Este desduplicación, junto con la semántica de finalización de cada mensaje proporcionada por colas de Bus de servicio, hace que sea fácil implementar el procesamiento confiable de mensajes interactivos.

Para asegurarse de que no se vuelve a enviar ningún mensaje de fuera de la ventana de la duplicación, el código sincroniza el mecanismo de control de **EventProcessorHost** con la ventana de la duplicación de cola de Bus de servicio. Se realiza esta sincronización forzar un punto de control al menos una vez cada vez que la ventana de desduplicación transcurre (en este tutorial, la ventana es una hora).

> [AZURE.NOTE] Este tutorial usa una sola cola de Bus de servicio con particiones al proceso de todos los mensajes interactivos recuperados de concentrador IoT. Para obtener más información sobre cómo usar colas de Bus de servicio para satisfacer los requisitos de escalabilidad de su solución, consulte la documentación de [Bus de servicio de Azure] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Aprovisionamiento de una cola de Bus de servicio y una cuenta de almacenamiento de Azure
Para usar la clase [EventProcessorHost] , debe tener una cuenta de almacenamiento de Azure para habilitar la **EventProcessorHost** registrar información de control. Puede usar una cuenta de Azure almacenamiento existente o siga las instrucciones [Sobre el almacenamiento de Azure] para crear uno nuevo. Anote la cadena de conexión de la cuenta de almacenamiento de Azure.

> [AZURE.NOTE] Al copiar y pegar la cadena de conexión de la cuenta de almacenamiento de Azure, asegúrese de que no hay espacios incluidos.

También debe una cola de Bus de servicio para habilitar el procesamiento confiable de mensajes interactivos. Puede crear una cola mediante programación, con una ventana de la duplicación de una hora, como se explica en [cómo usar colas de Bus de servicio de][cola de Bus de servicio]. Como alternativa, puede usar el [portal clásica Azure][lnk-classic-portal], siguiendo estos pasos:

1. En la esquina inferior izquierda, haga clic en **nuevo** . A continuación, haga clic en **Servicios de aplicación** > **Bus de servicio** > **cola** > **Crear personalizado**. Escriba el nombre **d2ctutorial**, seleccione una región y usar un espacio de nombres existente o cree uno nuevo. En la siguiente página, seleccione **Habilitar detección de duplicados**y establezca la **ventana de tiempo del historial de detección de duplicados** en una hora. A continuación, haga clic en la marca de verificación en la esquina inferior derecha para guardar la configuración de cola.

    ![Crear una cola en el portal de Azure][30]

2. En la lista de colas de Bus de servicio, haga clic en **d2ctutorial**y, a continuación, haga clic en **Configurar**. Crear dos directivas de acceso compartido, una llamada **Enviar** con permisos de **envío** y de llamado una **escuchar** con permisos de **escuchar** . Cuando haya terminado, haga clic en **Guardar** en la parte inferior.

    ![Configurar una cola de portal de Azure][31]

3. Haga clic en **panel** en la parte superior y, a continuación, **información de conexión** en la parte inferior. Tome nota de las dos cadenas de conexión.

    ![Panel de cola en el portal de Azure][32]

### <a name="create-the-event-processor"></a>Crear el procesador de eventos

1. En la solución actual de Visual Studio, para crear un proyecto de Visual C# Windows con la plantilla de proyecto de **Aplicación de consola** , haga clic en **archivo** > **Agregar** > **Nuevo proyecto**. Asegúrese de que la versión de .NET Framework es 4.5.1 o posterior. El proyecto **ProcessDeviceToCloudMessages**nombre y haga clic en **Aceptar**.

    ![Nuevo proyecto de Visual Studio][10]

2. En el Explorador de soluciones, haga clic en el proyecto **ProcessDeviceToCloudMessages** y, a continuación, haga clic en **Administrar paquetes de NuGet**. Aparece el cuadro de diálogo **Administrador de paquetes de NuGet** .

3. Busque **WindowsAzure.ServiceBus**, haga clic en **instalar**y acepte los términos de uso. Esta operación descargas, se instala y agrega una referencia al [paquete NuGet de Bus de servicio de Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), con todas sus dependencias.

4. Busque **Microsoft.Azure.ServiceBus.EventProcessorHost**, haga clic en **instalar**y acepte los términos de uso. Esta operación descargas, se instala y agrega una referencia al [Concentrador de eventos de Bus de servicio de Azure - paquete EventProcessorHost NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), con todas sus dependencias.

5. Haga clic en el proyecto **ProcessDeviceToCloudMessages** , haga clic en **Agregar**y, a continuación, haga clic en **clase**. Asigne un nombre a la nueva clase **StoreEventProcessor**y, a continuación, haga clic en **Aceptar** para crear la clase.

6. Agregue las siguientes instrucciones en la parte superior del archivo StoreEventProcessor.cs:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Sustituir el siguiente código del cuerpo de la clase:

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    La clase **EventProcessorHost** llama a esta clase para procesar los mensajes de la nube de dispositivo recibidos IoT concentrador. El código de esta clase implementa la lógica para almacenar mensajes confiable en un contenedor de blob y reenviar mensajes interactivos a la cola de Bus de servicio.

    El método **OpenAsync** inicializa la variable **currentBlockInitOffset** , que realiza un seguimiento el desplazamiento actual del primer mensaje leído por este procesador de evento. Recuerde que cada procesador es responsable de una sola partición.

    El método **ProcessEventsAsync** recibe un lote de mensajes IoT concentrador y los procesa como sigue: envía mensajes interactivos a la cola de Bus de servicio y agrega mensajes de punto de datos en el búfer de memoria llamado **toAppend**. Si el búfer de memoria alcanza el límite de 4 MB o las ventanas de tiempo desduplicación transcurre (una hora después de un punto de comprobación en este tutorial), la aplicación activa un punto de control.

    En primer lugar, el método **AppendAndCheckpoint** genera una blockId para el bloque de datos anexados. Almacenamiento de Azure requiere todos bloquear identificadores para tener la misma longitud, por lo que el método rellena el desplazamiento con ceros a la izquierda - `currentBlockInitOffset.ToString("0000000000000000000000000")`. A continuación, si un bloque con este ID ya está en el blob, el método sobrescribe con el contenido actual del búfer.

    > [AZURE.NOTE] Para simplificar el código, este tutorial usa un blob único por partición para almacenar los mensajes. Una solución real implemente sucesiva mediante la creación de archivos adicionales después de una determinada cantidad de tiempo, o cuando llegan a un determinado tamaño de archivo. Recuerde que un blobs de Windows Azure bloque pueden contener como máximo 195 GB de datos.

8. En la clase de **programa** , agregue la siguiente instrucción **using** en la parte superior:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modifique el método **Main** en la clase de **programa** como sigue. Reemplace **{cadena de conexión de hub iot}** por la cadena de conexión de **iothubowner** desde el tutorial de [Introducción a IoT concentrador] . Reemplace la cadena de conexión de almacenamiento por la cadena de conexión que anotó al comienzo de esta sección. Reemplazar la cadena de conexión de Bus de servicio con permisos de **envío** de la cola denominada **d2ctutorial** que anotó al comienzo de esta sección:

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

    > [AZURE.NOTE] Para simplificar, este tutorial usa una instancia de la clase [EventProcessorHost] . Para obtener más información, consulte la [Guía de programación de Hubs de evento].

## <a name="receive-interactive-messages"></a>Recibir mensajes interactivos
En esta sección, escriba una aplicación de consola de Windows que recibe los mensajes interactivos de la cola de Bus de servicio. Para obtener más información acerca de cómo crear una solución con Bus de servicio, vea [crear aplicaciones de varios niveles con Bus de servicio][].

1. En la solución actual de Visual Studio, cree un proyecto de Visual C# Windows con la plantilla de proyecto de **Aplicación de consola** . Nombre del proyecto **ProcessD2CInteractiveMessages**.

2. En el Explorador de soluciones, haga clic en el proyecto **ProcessD2CInteractiveMessages** y, a continuación, haga clic en **Administrar paquetes de NuGet**. Esta operación muestra la ventana **Administrador de paquetes de NuGet** .

3. Busque **WindowsAzure.ServiceBus**, haga clic en **instalar**y acepte los términos de uso. Esta operación descargas, se instala y agrega una referencia al [Bus de servicio de Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), con todas sus dependencias.

4. Agregue las siguientes instrucciones **using** en la parte superior del archivo **Program.cs** :

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Por último, agregue las siguientes líneas al método **Main** . Sustituya la cadena de conexión con **escuchar** permisos para la cola denominada **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ahora ya está listo para ejecutar las aplicaciones.

1.  En Visual Studio, en el Explorador de soluciones, haga clic en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **varios proyectos de inicio**y luego seleccione **Iniciar** como acción para los proyectos **ProcessDeviceToCloudMessages**, **SimulatedDevice**y **ProcessD2CInteractiveMessages** .

2.  Presione **F5** para iniciar las aplicaciones de tres consola. La aplicación **ProcessD2CInteractiveMessages** debe procesar cada mensaje interactivo enviado desde la aplicación **SimulatedDevice** .

  ![Tres aplicaciones de consola][50]

> [AZURE.NOTE] Para ver las actualizaciones en el blob, debe reducir la constante **MAX_BLOCK_SIZE** en la clase **StoreEventProcessor** en un valor más pequeño, como **1024**. Este cambio es útil porque tarda algún tiempo para alcanzar el límite de tamaño de bloque con los datos enviados por el dispositivo simulado. Con un tamaño de bloque, no es necesario que esperar tanto para ver el blob que se crean y se actualiza. Sin embargo, usando un tamaño de bloque más grande hace que la aplicación más scalable.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió confiable procesar interactivos mensajes de dispositivo a la nube y punto de datos utilizando la clase [EventProcessorHost] .

[Cómo enviar mensajes de dispositivo de nube con concentrador IoT] [ lnk-c2d] muestra cómo enviar mensajes a los dispositivos de back-end.

Para ver ejemplos de soluciones to-end completas que usan IoT concentrador, vea [El conjunto de aplicaciones de Azure IoT][lnk-suite].

Para obtener más información sobre cómo desarrollar soluciones con IoT concentrador, consulte la [Guía de desarrollador IoT concentrador].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Almacenamiento de blobs de Windows Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Generador de datos de Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Cola de Bus de servicio]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guía de programador de Azure IoT concentrador - dispositivo a la nube]: iot-hub-devguide-messaging.md

[Almacenamiento de Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus de servicio de Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guía del desarrollador de concentrador IoT]: iot-hub-devguide.md
[Introducción a IoT concentrador]: iot-hub-csharp-csharp-getstarted.md
[Centro para desarrolladores de Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Control de errores transitorias]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Información sobre el almacenamiento de Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introducción a Hubs de evento]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Escalabilidad de almacenamiento de Azure directrices]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Guía de programación de Hubs de evento]: ../event-hubs/event-hubs-programming-guide.md
[Control de errores transitorias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Crear aplicaciones de varios niveles con Bus de servicio]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
