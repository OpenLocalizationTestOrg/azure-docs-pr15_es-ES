<properties
    pageTitle="Enviar mensajes de dispositivo de nube con concentrador IoT | Microsoft Azure"
    description="Siga este tutorial para obtener información sobre cómo enviar mensajes de dispositivo de nube con Azure IoT concentrador con C#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-net"></a>Tutorial: Cómo enviar mensajes de dispositivo de nube con IoT concentrador y .net

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introducción

Hub de Azure IoT es un servicio totalmente gestionado que ayuda a habilitar a confiables y seguras comunicaciones bidireccional entre millones de dispositivos IoT y finalizar una aplicación de nuevo. El tutorial de [Introducción a IoT concentrador] muestra cómo crear un concentrador IoT, aprovisionar la identidad de un dispositivo en ella y el código de un dispositivo simulado que envía mensajes de dispositivo a la nube.

En este tutorial se basa en [Introducción a IoT concentrador]. Muestra cómo para:

- Desde su aplicación nube back-end, enviar mensajes de dispositivo de nube a un único dispositivo a través de concentrador IoT.
- Recibir mensajes de la nube para el dispositivo en un dispositivo.
- En la nube de aplicación back-end, solicitar confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo concentrador IoT.

Puede encontrar más información sobre los mensajes de dispositivo de nube en la [Guía de desarrollador de concentrador IoT][IoT Hub Developer Guide - C2D].

Al final de este tutorial, se ejecutará dos aplicaciones de consola de Windows:

* **SimulatedDevice**, una versión modificada de la aplicación creada en [Introducción a IoT concentrador], que se conecta a su centro IoT y recibe mensajes de la nube al dispositivo.
* **SendCloudToDevice**, que envía un mensaje de dispositivo de nube al dispositivo simulado a través de concentrador IoT y, a continuación, recibe la confirmación de entrega.

> [AZURE.NOTE] Concentrador IoT admite SDK muchas plataformas de dispositivos e idiomas (incluidos C, Java y Javascript) a través de Azure IoT SDK de dispositivos. Para obtener instrucciones detalladas sobre cómo conectar el dispositivo a código de este tutorial y generalmente a Azure IoT concentrador, consulte el [Centro para desarrolladores de Azure IoT].

Para completar este tutorial, necesitará la siguiente:

+ Microsoft Visual Studio 2015

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

## <a name="receive-messages-on-the-simulated-device"></a>Recibir mensajes en el dispositivo simulado

En esta sección, podrá modificar la aplicación de dispositivo simulado creado en [Introducción a IoT concentrador] para recibir mensajes de dispositivo de nube de concentrador IoT.

1. En Visual Studio, en el proyecto **SimulatedDevice** , agregue el método siguiente a la clase **Program** .

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    La `ReceiveAsync` método asincrónica devuelve el mensaje recibido en el momento en que sea recibida por el dispositivo. Devuelve un *valor nulo* tras un periodo de tiempo de espera puede especificar (en este caso, se usa el valor predeterminado de un minuto). Cuando esto ocurre, el código debería continuar esperando para mensajes nuevos. Este es el motivo de la `if (receivedMessage == null) continue` línea.

    La llamada a `CompleteAsync()` notifica IoT concentrador que el mensaje se ha procesado correctamente. El mensaje se puede quitar de forma segura de la cola del dispositivo. Si ocurrió algo que impedir que la aplicación de dispositivo el procesamiento del mensaje, concentrador IoT lo entrega de nuevo. A continuación, es importante que la lógica de procesamiento de mensaje en la aplicación de dispositivo esté *idempotente*, poder recibir el mismo mensaje varias veces genera el mismo resultado. Una aplicación puede abandonar temporalmente un mensaje, que da como resultado concentrador IoT conserva el mensaje en la cola de consumo futuro. O bien, la aplicación puede rechazar un mensaje que se elimina permanentemente el mensaje de la cola. Para obtener más información sobre el ciclo de vida del mensaje de dispositivo de nube, consulte la [Guía de desarrollador de concentrador IoT][IoT Hub Developer Guide - C2D].

    > [AZURE.NOTE] Cuando se utiliza HTTP en lugar de MQTT o AMQP como transporte, la `ReceiveAsync` método devuelve inmediatamente. El modelo admitido para los mensajes de la nube para dispositivos con HTTP es dispositivos conectados de forma intermitente comprobación para mensajes con poca frecuencia (menor que cada 25 minutos). Emitir HTTP más recibe resultados en concentrador IoT limitar las solicitudes. Para obtener más información acerca de las diferencias entre soporte MQTT, AMQP y HTTP y limitación IoT concentrador, consulte la [Guía de desarrollador de concentrador IoT][IoT Hub Developer Guide - C2D].

2. Agregue el método siguiente en el método **Main** , justo antes de la `Console.ReadLine()` línea:

        ReceiveC2dAsync();

> [AZURE.NOTE] Para simplificar, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, un crecimiento exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores].

## <a name="send-a-cloud-to-device-message"></a>Enviar un mensaje de dispositivo de nube

En esta sección, deberá escribir una aplicación de consola de Windows que envía mensajes de dispositivo de nube a la aplicación de dispositivo simulado.

1. En la solución actual de Visual Studio, cree un nuevo proyecto de aplicación de escritorio de Visual C# mediante la plantilla de proyecto de **Aplicación de consola** . Nombre del proyecto **SendCloudToDevice**.

    ![Nuevo proyecto de Visual Studio][20]

2. En el Explorador de soluciones, haga clic en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet para solución...**. 

    Se abrirá la ventana **Administrar paquetes de NuGet** .

3. Buscar `Microsoft Azure Devices`, haga clic en **instalar**y acepte los términos de uso. 

    Descargas, instalaciones y agrega una referencia a la [Azure IoT - paquete NuGet SDK de servicio].

4. Agregue las siguientes `using` instrucción en la parte superior del archivo **Program.cs** :

        using Microsoft.Azure.Devices;

5. Agregue los campos siguientes a la clase **Program** . Sustituir el valor de marcador de posición con la cadena de conexión de hub IoT de [Introducción a IoT concentrador]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Agregue el método siguiente a la clase **Program** :

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Este método envía un nuevo mensaje de dispositivo de nube al dispositivo con el identificador `myFirstDevice`. Cambie este parámetro según corresponda, en caso de que modificó utilizado en [Introducción a IoT concentrador].

7. Por último, agregue las siguientes líneas al método **Main** :

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Desde dentro de Visual Studio, haga clic en la solución y seleccione **Establecer inicio proyectos...**. Seleccione **varios proyectos de inicio**y luego seleccione la acción **Iniciar** para **ProcessDeviceToCloudMessages**, **SimulatedDevice**y **SendCloudToDevice**.

9.  Presione **F5**. Deben iniciar todas las aplicaciones de tres. Seleccione las ventanas **SendCloudToDevice** y presione **ENTRAR**. Verá el mensaje por la aplicación simulada.

    ![Mensaje de recepción de aplicación][21]

## <a name="receive-delivery-feedback"></a>Recibir comentarios de entrega
Es posible confirmaciones de entrega (o expiración) de la solicitud IoT concentrador para cada mensaje de nube al dispositivo. Esto permite el back-end nube informar fácilmente lógica de compensación o vuelva a intentarlo. Para obtener más información acerca de los comentarios de dispositivo de nube, consulte la [Guía de desarrollador de concentrador IoT][IoT Hub Developer Guide - C2D].

En esta sección, modificará la aplicación **SendCloudToDevice** para solicitar comentarios y recibir IoT concentrador.

1. En Visual Studio, en el proyecto **SendCloudToDevice** , agregue el método siguiente a la clase **Program** .
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Tenga en cuenta que el modelo de recepción es la misma utilizado para recibir mensajes de dispositivo de nube desde la aplicación del dispositivo.

2. Agregue el método siguiente en el método **Main** , justo después de la `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` línea:

        ReceiveFeedbackAsync();

3. Para solicitar comentarios de la entrega del mensaje de la nube para el dispositivo, tiene que especificar una propiedad en el método **SendCloudToDeviceMessageAsync** . Agregue la línea siguiente, justo después de la `var commandMessage = new Message(...);` línea:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Presione **F5**para ejecutar las aplicaciones. Debería ver las tres aplicaciones iniciar. Seleccione las ventanas **SendCloudToDevice** y presione **ENTRAR**. Verá el mensaje que se recibió la aplicación simulada y después de unos segundos, el mensaje de comentarios recibido por la aplicación **SendCloudToDevice** .

    ![Mensaje de recepción de aplicación][22]

> [AZURE.NOTE] Para simplificar, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, un crecimiento exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores].

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo enviar y recibir mensajes de la nube al dispositivo. 

Para ver ejemplos de soluciones to-end completas que usan IoT concentrador, vea [El conjunto de aplicaciones de Azure IoT].

Para obtener más información sobre cómo desarrollar soluciones con IoT concentrador, consulte la [Guía de desarrollador IoT concentrador].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT - paquete NuGet SDK de servicio]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Control de errores transitorias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md

[Guía del desarrollador de concentrador IoT]: iot-hub-devguide.md
[Introducción a IoT concentrador]: iot-hub-csharp-csharp-getstarted.md
[Centro para desarrolladores de Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Conjunto de aplicaciones de Azure IoT]: https://azure.microsoft.com/documentation/suites/iot-suite/