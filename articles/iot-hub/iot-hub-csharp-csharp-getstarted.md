<properties
    pageTitle="Azure concentrador IoT para C# Introducción | Microsoft Azure"
    description="Tutorial de inicio de Azure concentrador IoT con C# Introducción. Use Azure IoT concentrador y C# con el SDK de Microsoft Azure IoT implementar una solución de Internet de las cosas."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-net"></a>Introducción a Azure IoT concentrador para .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al final de este tutorial, tiene tres aplicaciones de consola de Windows:

* **CreateDeviceIdentity**, que crea una identidad de dispositivo y una clave de seguridad asociadas para conectar el dispositivo simulado.
* **ReadDeviceToCloudMessages**, que muestra la telemetría enviada por el dispositivo simulado.
* **SimulatedDevice**, que se conecta a su centro IoT con la identidad del dispositivo creada anteriormente y envía un mensaje de telemetría cada segundo mediante el protocolo AMQP.

> [AZURE.NOTE] Para obtener información sobre los distintos SDK que puede usar para crear dos aplicaciones para ejecutarse en dispositivos y back-end solución, consulte [IoT concentrador SDK][lnk-hub-sdks].

Para completar este tutorial, necesita lo siguiente:

+ Microsoft Visual Studio 2015.

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ahora ha creado su centro IoT y tiene la cadena de conexión y nombre de host que necesita para completar el resto de este tutorial.

## <a name="create-a-device-identity"></a>Crear una identidad de dispositivo

En esta sección, creará una aplicación de consola de Windows que crea una identidad de dispositivo en el registro de identidad en el hub de IoT. No se puede conectar un dispositivo a IoT concentrador a menos que tenga una entrada en el registro de la identidad de dispositivo. Para obtener más información, vea la sección "Registro de identidad de dispositivo" de la [Guía de desarrollador de concentrador IoT][lnk-devguide-identity]. Cuando se ejecuta esta aplicación de consola, que genera un identificador único del dispositivo y clave que puede usar el dispositivo para identificarse cuando envía mensajes de dispositivo a la nube a IoT concentrador.

1. En Visual Studio, agregue un proyecto de Visual C# escritorio clásico de Windows a la solución actual con la plantilla de proyecto de **Aplicación de consola** . Asegúrese de que la versión de .NET Framework es 4.5.1 o posterior. Nombre del proyecto **CreateDeviceIdentity**.

    ![Nuevo proyecto de Visual C# escritorio clásico de Windows][10]

2. En el Explorador de soluciones, haga clic en el proyecto **CreateDeviceIdentity** y, a continuación, haga clic en **Administrar paquetes de Nuget**.

3. En la ventana **Administrador de paquetes de Nuget** , seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **instalar** para instalar el paquete de **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento descargas, instalaciones y agrega una referencia en el [SDK de servicio de Microsoft Azure IoT] [ lnk-nuget-service-sdk] Nuget paquete y sus dependencias.

    ![Ventana del Administrador de paquetes de NuGet][11]

4. Agregue las siguientes `using` instrucciones en la parte superior del archivo **Program.cs** :

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Agregue los campos siguientes a la clase **Program** . Reemplace el valor de marcador de posición con la cadena de conexión para el concentrador IoT que creó en la sección anterior.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Agregue el método siguiente a la clase **Program** :

        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    Este método crea la identidad de un dispositivo con identificador **myFirstDevice**. (Si ya existe ese ID de dispositivo en el registro, el código simplemente recupera la información de dispositivo existente). La aplicación, a continuación, muestra la clave principal de esa identidad. Use esta clave en el dispositivo simulado para conectarse a su centro IoT.

7. Por último, agregue las siguientes líneas al método **Main** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Ejecutar esta aplicación y tome nota de la clave del dispositivo.

    ![Clave de dispositivo generado por la aplicación][12]

> [AZURE.NOTE] El registro de la identidad de concentrador IoT sólo almacena las identidades de dispositivo para permitir el acceso seguro al concentrador. Almacena los identificadores de dispositivos y claves para usar como credenciales de seguridad y un indicador de habilitado o deshabilitado que puede usar para deshabilitar el acceso para un dispositivo individual. Si la aplicación debe almacenar otros metadatos específicos del dispositivo, debe utilizar una tienda específica de la aplicación. Para obtener más información, vea [Guía de desarrollador de concentrador IoT][lnk-devguide-identity].

## <a name="receive-device-to-cloud-messages"></a>Recibir mensajes de la nube de dispositivo

En esta sección, creará una aplicación de consola de Windows que lee los mensajes de la nube de dispositivo de concentrador IoT. Un concentrador IoT expone un [Azure evento Hubs][lnk-event-hubs-overview]-extremo compatible para que pueda leer mensajes de dispositivo a la nube. Para mantener las cosas sencillas, este tutorial crea un lector básico que no es adecuado para una implementación de alto rendimiento. Para obtener información sobre cómo procesar los mensajes de la nube de dispositivo en escala, vea los [mensajes de nube de dispositivo de proceso] [ lnk-process-d2c-tutorial] tutorial. Para obtener más información acerca de cómo procesar los mensajes de evento Hubs, vea la [Introducción a evento Hubs] [ lnk-eventhubs-tutorial] tutorial. (Este tutorial es aplicable a los extremos de evento de concentrador IoT concentrador compatible).

> [AZURE.NOTE] El extremo de evento concentrador compatibles para leer mensajes de la nube de dispositivo siempre usa el protocolo AMQP.

1. En Visual Studio, agregue un proyecto de Visual C# escritorio clásico de Windows a la solución actual, con la plantilla de proyecto de **Aplicación de consola** . Asegúrese de que la versión de .NET Framework es 4.5.1 o posterior. Nombre del proyecto **ReadDeviceToCloudMessages**.

    ![Nuevo proyecto de Visual C# escritorio clásico de Windows][10]

2. En el Explorador de soluciones, haga clic en el proyecto **ReadDeviceToCloudMessages** y, a continuación, haga clic en **Administrar paquetes de Nuget**.

3. En la ventana **Administrador de paquetes de Nuget** , busque **WindowsAzure.ServiceBus**, seleccione **instalar**y acepte los términos de uso. Este procedimiento descargas, instalaciones y agrega una referencia a [Bus de servicio de Azure][lnk-servicebus-nuget], con todas sus dependencias. Este paquete permite a conectarse al extremo en su centro IoT evento concentrador compatible con la aplicación.

4. Agregue las siguientes `using` instrucciones en la parte superior del archivo **Program.cs** :

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Agregue los campos siguientes a la clase **Program** . Reemplace el valor de marcador de posición con la cadena de conexión para el concentrador IoT que creó en la sección "Crear un concentrador IoT".

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Agregue el método siguiente a la clase **Program** :

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Este método utiliza una instancia de **EventHubReceiver** para recibir mensajes de todas la IoT concentrador dispositivo-a-nube reciban particiones. Observe cómo pasar un `DateTime.Now` parámetro cuando se crea el objeto **EventHubReceiver** , para que sólo recibe mensajes enviados después de que se inicia. Este filtro es útil en un entorno de prueba para que pueda ver el conjunto actual de mensajes. En un entorno de producción su código debe asegurarse de que procesa todos los mensajes. Para obtener más información, consulte [cómo procesar los mensajes de nube de dispositivo concentrador IoT] [ lnk-process-d2c-tutorial] tutorial.

7. Por último, agregue las siguientes líneas al método **Main** :

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## <a name="create-a-simulated-device-app"></a>Crear una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Windows que simula un dispositivo que envía mensajes de nube de dispositivo a un concentrador IoT.

1. En Visual Studio, agregue un proyecto de Visual C# escritorio clásico de Windows a la solución actual, con la plantilla de proyecto de **Aplicación de consola** . Asegúrese de que la versión de .NET Framework es 4.5.1 o posterior. Nombre del proyecto **SimulatedDevice**.

    ![Nuevo proyecto de Visual C# escritorio clásico de Windows][10]

2. En el Explorador de soluciones, haga clic en el proyecto **SimulatedDevice** y, a continuación, haga clic en **Administrar paquetes de Nuget**.

3. En la ventana **Administrador de paquetes de Nuget** , seleccione **Examinar**, busque **Microsoft.Azure.Devices.Client**, seleccione **instalar** para instalar el paquete de **Microsoft.Azure.Devices.Client** y acepte los términos de uso. Este procedimiento descargas, instalaciones y agrega una referencia a la [Azure IoT - paquete dispositivo SDK Nuget] [ lnk-device-nuget] y sus dependencias.

4. Agregue las siguientes `using` instrucción en la parte superior del archivo **Program.cs** :

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Agregue los campos siguientes a la clase **Program** . Sustituir los valores de marcador de posición con el nombre de host de concentrador IoT que ha recuperado en la sección "Crear un concentrador IoT" y recuperar la clave de dispositivo en la sección "Crear una identidad de dispositivo".

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Agregue el método siguiente a la clase **Program** :

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    Este método envía un nuevo mensaje de nube de dispositivo cada segundo. El mensaje contiene un objeto JSON número de serie, con el identificador de dispositivo y un número generado aleatoriamente para simular un sensor de velocidad del viento.

7. Por último, agregue las siguientes líneas al método **Main** :

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  De forma predeterminada, el método **Create** crea una instancia de **DeviceClient** que utiliza el protocolo AMQP para comunicarse con IoT concentrador. Para utilizar el protocolo HTTP, utilice el reemplazo del método **Create** que le permite especificar el protocolo. Si usa el protocolo HTTP, también debe agregar el paquete de Nuget **Microsoft.AspNet.WebApi.Client** a su proyecto para incluir el espacio de nombres **System.Net.Http.Formatting** .

Este tutorial le guiará por los pasos para crear a un cliente de dispositivo IoT concentrador. También puede usar el [Servicio conectado de Azure IoT concentrador] [ lnk-connected-service] extensión de Visual Studio para agregar el código necesario para la aplicación cliente del dispositivo.


> [AZURE.NOTE] Para mantener las cosas sencillas, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, una interrupción exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores][lnk-transient-faults].

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ya está listo para ejecutar las aplicaciones.

1.  En Visual Studio, en el Explorador de soluciones, haga clic en la solución y, a continuación, haga clic en **proyectos de inicio de establecer**. Seleccione **varios proyectos de inicio**y, a continuación, seleccione **Iniciar** como acción para los **ReadDeviceToCloudMessages** **SimulatedDevice** proyectos y.

    ![Propiedades del proyecto de inicio][41]

2.  Presione **F5** para iniciar las aplicaciones que ejecuta. El resultado de la consola de la aplicación **SimulatedDevice** muestra los mensajes de que su dispositivo simulado envía a su centro IoT. El resultado de la consola de la aplicación **ReadDeviceToCloudMessages** muestra los mensajes que recibe de su centro IoT.

    ![Resultado de la consola de aplicaciones][42]

3. El mosaico de **uso** en el [portal de Azure] [ lnk-portal] muestra el número de mensajes enviados al concentrador:

    ![Mosaico de uso de portal Azure][43]


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se configura un concentrador IoT en el portal de Azure y a continuación, se crea la identidad de un dispositivo en registro de identidad del concentrador. Utilizar este dispositivo identidad para habilitar la aplicación de dispositivo simulado enviar mensajes de la nube de dispositivo al concentrador. También crea una aplicación que muestra los mensajes recibidos por el concentrador. 

Para continuar la introducción a IoT concentrador y explorar otros escenarios IoT, consulte:

- [Conectar el dispositivo][lnk-connect-device]
- [Introducción a la administración de dispositivos][lnk-device-management]
- [Introducción al trabajo con el SDK de puerta de enlace IoT][lnk-gateway-SDK]

Para obtener información sobre cómo ampliar su solución IoT y procesar los mensajes de la nube de dispositivo en escala, vea los [mensajes de nube de dispositivo de proceso] [ lnk-process-d2c-tutorial] tutorial.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
