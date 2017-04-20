## <a name="send-messages-to-event-hubs"></a>Enviar mensajes a Hubs de evento

En esta sección, deberá escribir una aplicación de consola de Windows que envía eventos a su centro de evento.

1. En Visual Studio, cree un nuevo proyecto de aplicación de escritorio de Visual C# mediante la plantilla de proyecto de **Aplicación de consola** . Nombre del **remitente**del proyecto.

    ![](./media/service-bus-event-hubs-getstarted-send-csharp/create-sender-csharp1.png)

2. En el Explorador de soluciones, haga clic en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet para la solución**. 

3. Haga clic en la ficha **Examinar** , busque `Microsoft Azure Service Bus`. Asegúrese de que el nombre del proyecto (**remitente**) especificado en el cuadro de **versiones** . Haga clic en **instalar**y acepte los términos de uso. 

    ![](./media/service-bus-event-hubs-getstarted-send-csharp/create-sender-csharp2.png)

    Visual Studio descargas, instalaciones y agrega una referencia al [paquete de Bus de servicio de Azure biblioteca NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

4. Agregue las siguientes `using` instrucciones en la parte superior del archivo **Program.cs** :

    ```
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Agregue los campos siguientes a la clase de **programa** , sustituir los valores de marcador de posición con el nombre del evento concentrador que creó en la sección anterior y a continuación, la cadena de conexión en el nivel de espacio de nombres que ha guardado previamente.

    ```
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```

6. Agregue el método siguiente a la clase **Program** :

    ```
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }

            Thread.Sleep(200);
        }
    }
    ```

    Este método envía continuamente eventos a su centro de evento con un retraso de 200 ms.

7. Por último, agregue las siguientes líneas al método **Main** :

    ```
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```
