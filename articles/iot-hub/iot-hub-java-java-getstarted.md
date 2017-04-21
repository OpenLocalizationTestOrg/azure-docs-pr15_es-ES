<properties
    pageTitle="Azure concentrador IoT para Java Introducción | Microsoft Azure"
    description="Tutorial de inicio de Azure concentrador IoT con Java Introducción. Use Azure IoT concentrador y Java con el SDK de Microsoft Azure IoT implementar una solución de Internet de las cosas."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-java"></a>Introducción a Azure IoT concentrador de Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al final de este tutorial, tiene tres aplicaciones de consola de Java:

* **identidad del dispositivo crear**, lo que crea una identidad de dispositivo y una clave de seguridad asociadas para conectar el dispositivo simulado.
* **leer mensajes de d2c**, que muestra la telemetría enviada por el dispositivo simulado.
* **dispositivo simulado**, que se conecta a su centro IoT con la identidad del dispositivo creada anteriormente y a continuación, envía un mensaje de telemetría cada segundo mediante el protocolo AMQP.

> [AZURE.NOTE] El artículo [IoT concentrador SDK] [ lnk-hub-sdks] proporciona información sobre los distintos SDK que puede usar para crear dos aplicaciones para ejecutarse en dispositivos y back-end solución.

Para completar este tutorial, necesita lo siguiente:

+ Java SE 8. <br/> [Preparar el entorno de desarrollo] [ lnk-dev-setup] describe cómo instalar Java para este tutorial en Windows o Linux.

+ Experto en 3.  <br/> [Preparar el entorno de desarrollo] [ lnk-dev-setup] describe cómo instalar experto en este tutorial en Windows o Linux.

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Como paso final, anote el valor de **clave principal** y, a continuación, haga clic en **mensajería**. En el módulo de **mensajería** , anote el **nombre de evento concentrador compatible** y el **extremo del evento concentrador compatible**. Estos tres valores es necesario cuando se crea la aplicación de **leer mensajes de d2c** .

![Módulo de mensajería de concentrador IoT portal de Azure][6]

Ahora ha creado su centro IoT y tiene el concentrador IoT hostname, cadena de conexión de IoT Hub, IoT concentrador clave principal, nombre compatible con evento concentrador y extremo compatible con evento concentrador que deberá realizar este tutorial.

## <a name="create-a-device-identity"></a>Crear una identidad de dispositivo

En esta sección, creará una aplicación de consola de Java que crea una nueva identidad de dispositivo en el registro de identidad en el hub de IoT. No se puede conectar un dispositivo a IoT concentrador a menos que tenga una entrada en el registro de la identidad de dispositivo. Consulte la sección **Registro de identidad de dispositivo** de la [Guía de desarrollador de concentrador IoT] [ lnk-devguide-identity] para obtener más información. Cuando se ejecuta esta aplicación de consola, que genera un identificador único del dispositivo y clave que puede usar el dispositivo para identificarse cuando envía mensajes de dispositivo a la nube a IoT concentrador.

1. Crear una nueva carpeta vacía denominada iot java-se empieza. En la carpeta iot java-se empieza, cree un nuevo proyecto de experto denominado **identidad dispositivo crear** mediante el comando siguiente en el símbolo. Tenga en cuenta que se trata de un comando largo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva identidad del dispositivo crear carpeta.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta de identidad de dispositivo de crear y agregar la siguiente dependencia al nodo **dependencias** . Esto le permite utilizar el paquete de iothub sdk de servicio en la aplicación:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
    
4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Agregue las siguientes instrucciones de **Importar** el archivo:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase de **aplicación** , reemplazar **{yourhubconnectionstring}** con el valor su se indicó anteriormente:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. Modifique la firma del método **principal** para incluir las excepciones como sigue:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Agregue el siguiente código como cuerpo del método **principal** . Este código crea un dispositivo denominado *javadevice* en el registro de la identidad de concentrador IoT si ya no existe. A continuación, se muestra el identificador del dispositivo y la clave que necesita más adelante:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Guarde y cierre el archivo App.java.

11. Para generar la aplicación de la **identidad del dispositivo de crear** mediante Maven, ejecute el siguiente comando en el símbolo en la carpeta de identidad de dispositivo de crear:

    ```
    mvn clean package -DskipTests
    ```

12. Para ejecutar la aplicación de **identidad de dispositivo crear** mediante Maven, ejecute el comando siguiente en el símbolo en la carpeta de identidad de dispositivo de crear:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Anote el **identificador del dispositivo** y la **clave de dispositivo**. Necesitará más adelante al crear una aplicación que se conecta a IoT concentrador como un dispositivo.

> [AZURE.NOTE] El registro de la identidad de concentrador IoT sólo almacena las identidades de dispositivo para permitir el acceso seguro al concentrador. Almacena los identificadores de dispositivos y claves para usar como credenciales de seguridad y un indicador de habilitado o deshabilitado que puede usar para deshabilitar el acceso para un dispositivo individual. Si la aplicación debe almacenar otros metadatos específicos del dispositivo, debe utilizar una tienda específica de la aplicación. Consulte [Guía de desarrollador de concentrador IoT] [ lnk-devguide-identity] para obtener más información.

## <a name="receive-device-to-cloud-messages"></a>Recibir mensajes de la nube de dispositivo

En esta sección, creará una aplicación de consola de Java que lee los mensajes de la nube de dispositivo de concentrador IoT. Un concentrador IoT expone un [Evento concentrador][lnk-event-hubs-overview]-extremo compatible para que pueda leer mensajes de dispositivo a la nube. Para mantener las cosas sencillas, este tutorial crea un lector básico que no es adecuado para una implementación de alto rendimiento. Los [mensajes de nube de dispositivo de proceso] [ lnk-process-d2c-tutorial] tutorial muestra cómo procesar los mensajes de la nube de dispositivo en escala. La [Introducción a evento Hubs] [ lnk-eventhubs-tutorial] tutorial proporciona más información sobre cómo para procesar los mensajes de evento Hubs y se aplica a los extremos compatibles con el concentrador IoT concentrador evento.

> [AZURE.NOTE] El extremo de evento concentrador compatibles para leer mensajes de la nube de dispositivo siempre usa el protocolo AMQP.

1. En la carpeta iniciado una get de java iot que creó en la sección *crear una identidad de dispositivo* , cree un nuevo proyecto de experto denominado **leer mensajes de d2c** mediante el comando siguiente en el símbolo. Tenga en cuenta que se trata de un comando largo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva carpeta de leer mensajes de d2c.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta de leer mensajes de d2c y agregue la siguiente dependencia al nodo **dependencias** . Esto le permite utilizar el paquete de cliente de eventhubs en la aplicación para leer desde el extremo del evento concentrador compatible:

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Agregue las siguientes instrucciones de **Importar** el archivo:

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Agregue las siguientes variables de nivel de clase para la clase de **aplicación** . Reemplace **{youriothubkey}**, **{youreventhubcompatibleendpoint}**y **{youreventhubcompatiblename}** con los valores que anotó anteriormente:

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Agregue el siguiente método **receiveMessages** a la clase de **aplicación** . Este método crea una instancia de **EventHubClient** para conectarse al extremo compatible con evento concentrador y crea de manera asincrónica una instancia de **PartitionReceiver** lectura desde una partición de concentrador de evento. Se repite continuamente y se imprimen los detalles del mensaje hasta que finaliza la aplicación.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] Este método utiliza un filtro cuando se crea el receptor para que el receptor sólo lee mensajes enviados a concentrador IoT el receptor se inicia ejecutando. Esto es útil en un entorno de prueba para que pueda ver el conjunto actual de mensajes. En un entorno de producción debe asegurarse de que el código que TI procesa todos los mensajes: vea [cómo procesar los mensajes de nube de dispositivo concentrador IoT] [ lnk-process-d2c-tutorial] tutoriales para obtener más información.

9. Modifique la firma del método **principal** para incluir la excepción como sigue:

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Agregue el código siguiente al método **principal** de la clase de **aplicación** . Este código crea dos instancias de **EventHubClient** y **PartitionReceiver** y permite cerrar la aplicación cuando termine de procesar los mensajes:

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] Este código supone que creó el concentrador IoT en el nivel de F1 (gratis). Un concentrador IoT gratuito tiene dos particiones denominadas "0" y "1".

11. Guarde y cierre el archivo App.java.

12. Para generar la aplicación de **leer mensajes de d2c** con Maven, ejecute el siguiente comando en el símbolo en la carpeta d2c de lectura de mensajes:

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Crear una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Java que simula un dispositivo que envía mensajes de nube de dispositivo a un concentrador IoT.

1. En la carpeta iniciado una get de java iot que creó en la sección *crear una identidad de dispositivo* , cree un nuevo proyecto de experto llamado **dispositivo simulado** utilizando el comando siguiente en el símbolo. Tenga en cuenta que se trata de un comando largo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva carpeta del dispositivo simulado.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta de dispositivo simulado y agregue las siguientes dependencias al nodo **dependencias** . Esto le permite utilizar el paquete de cliente de java iothub en la aplicación para comunicarse con su centro IoT y serializar objetos de Java a JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo simulated-device\src\main\java\com\mycompany\app\App.java.

6. Agregue las siguientes instrucciones de **Importar** el archivo:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Agregue las siguientes variables de nivel de clase para la clase de **aplicación** , reemplazando **{youriothubname}** con el nombre del concentrador IoT y **{yourdevicekey}** con el valor de clave de dispositivo generadas en la sección *crear una identidad del dispositivo* :

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQP;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    Esta aplicación de ejemplo usa la variable de **protocolo** cuando se crea una instancia de un objeto **DeviceClient** . Puede usar el protocolo AMQP o HTTP para comunicarse con IoT concentrador.

8. Agregar que siguiente anidados **TelemetryDataPoint** clase dentro de la clase de **aplicación** para especificar los datos de telemetría que el dispositivo, se envía a su centro IoT:

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Agregue la siguiente clase **EventCallback** anidada dentro de la clase de **aplicación** para mostrar el estado de confirmación concentrador IoT devuelve cuando procesa un mensaje desde el dispositivo simulado. Este método también le notifica el subproceso principal de la aplicación cuando el mensaje se ha procesado:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Agregue la siguiente clase **MessageSender** anidada dentro de la clase de **aplicación** . El método **run** en esta clase genera datos de telemetría de ejemplo para enviar a su centro IoT y que espera una confirmación antes de enviar el mensaje siguiente:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Este método envía un nuevo mensaje de nube de dispositivo un segundo después de que el concentrador IoT reconoce el mensaje anterior. El mensaje contiene un objeto de número de serie de JSON con deviceId y un número generado aleatoriamente para simular un sensor de velocidad del viento.

11. Reemplace el método **principal** con el siguiente código que crea un subproceso para enviar mensajes de dispositivo a la nube a su centro IoT:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. Guarde y cierre el archivo App.java.

13. Para generar la aplicación de **dispositivo simulado** con Maven, ejecute el siguiente comando en el símbolo en la carpeta de dispositivo simulado:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Para mantener las cosas sencillas, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, una interrupción exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores][lnk-transient-faults].

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ya está listo para ejecutar las aplicaciones.

1. En el símbolo en la carpeta d2c de lectura, ejecute el comando siguiente para empezar a supervisar la primera partición en el hub de IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicación de cliente de servicio de Java IoT concentrador para supervisar los mensajes de la nube de dispositivo][7]

2. En una línea de comandos en la carpeta de dispositivo simulado, ejecute el comando siguiente para comenzar a enviar los datos de telemetría a su centro IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Aplicación de cliente de Java IoT concentrador dispositivo para enviar mensajes de la nube de dispositivo][8]

3. El mosaico de **uso** en el [portal de Azure] [ lnk-portal] muestra el número de mensajes enviados al concentrador:

    ![Azure portal uso mosaico mostrando el número de mensajes enviados a IoT concentrador][43]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, configurar un nuevo concentrador IoT en el portal de Azure y, a continuación, crea una identidad de dispositivo en registro de identidad del concentrador. Utilizar este dispositivo identidad para habilitar la aplicación de dispositivo simulado enviar mensajes de la nube de dispositivo al concentrador. También crea una aplicación que muestra los mensajes recibidos por el concentrador. 

Para continuar la introducción a IoT concentrador y explorar otros escenarios IoT vea:

- [Conectar el dispositivo][lnk-connect-device]
- [Introducción a la administración de dispositivos][lnk-device-management]
- [Introducción al trabajo con el SDK de puerta de enlace IoT][lnk-gateway-SDK]

Para obtener información sobre cómo ampliar su solución IoT y procesar los mensajes de la nube de dispositivo en escala, vea los [mensajes de nube de dispositivo de proceso] [ lnk-process-d2c-tutorial] tutorial.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/