<properties
    pageTitle="Enviar mensajes de dispositivo de nube con concentrador IoT | Microsoft Azure"
    description="Siga este tutorial para obtener información sobre cómo enviar mensajes de dispositivo de nube con Azure IoT concentrador con Java."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-java"></a>Tutorial: Cómo enviar mensajes de dispositivo de nube con IoT concentrador y Java

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introducción

Hub de Azure IoT es un servicio totalmente gestionado que ayuda a habilitar a confiables y seguras comunicaciones bidireccional entre millones de dispositivos IoT y finalizar una aplicación de nuevo. El tutorial de [Introducción a IoT concentrador] muestra cómo crear un concentrador IoT, aprovisionar la identidad de un dispositivo en ella y el código de un dispositivo simulado que envía mensajes de dispositivo a la nube.

En este tutorial se basa en [Introducción a IoT concentrador]. Muestra cómo para:

- Desde su aplicación nube back-end, enviar mensajes de dispositivo de nube a un único dispositivo a través de concentrador IoT.
- Recibir mensajes de la nube para el dispositivo en un dispositivo.
- En la nube de aplicación back-end, solicitar confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo concentrador IoT.

Puede encontrar más información sobre los mensajes de dispositivo de nube en la [Guía de desarrollador de concentrador IoT][IoT Hub Developer Guide - C2D].

Al final de este tutorial, ejecute dos aplicaciones de consola de Java:

* **dispositivo simulado**, una versión modificada de la aplicación creada en [Introducción a IoT concentrador], que se conecta a su centro IoT y recibe mensajes de la nube al dispositivo.
* **enviar mensajes de c2d**, que envía un mensaje de dispositivo de nube al dispositivo simulado a través de concentrador IoT y, a continuación, recibe la confirmación de entrega.

> [AZURE.NOTE] Concentrador IoT admite SDK muchas plataformas de dispositivos e idiomas (incluidos C, Java y Javascript) a través de Azure IoT SDK de dispositivos. Para obtener instrucciones detalladas sobre cómo conectar el dispositivo a código de este tutorial y generalmente a Azure IoT concentrador, consulte el [Centro para desarrolladores de Azure IoT].

Para completar este tutorial, necesita lo siguiente:

+ Java SE 8. <br/> [Preparar el entorno de desarrollo] [ lnk-dev-setup] describe cómo instalar Java para este tutorial en Windows o Linux.

+ Experto en 3.  <br/> [Preparar el entorno de desarrollo] [ lnk-dev-setup] describe cómo instalar experto en este tutorial en Windows o Linux.

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

## <a name="receive-messages-on-the-simulated-device"></a>Recibir mensajes en el dispositivo simulado

En esta sección, modifique la aplicación de dispositivo simulado creado en [Introducción a IoT concentrador] para recibir mensajes de dispositivo de nube de concentrador IoT.

1. Con un editor de texto, abra el archivo simulated-device\src\main\java\com\mycompany\app\App.java.

2. Agregue la siguiente clase **MessageCallback** como una clase anidada dentro de la clase de **aplicación** . **Ejecutar** método se invoca cuando el dispositivo recibe un mensaje de concentrador IoT. En este ejemplo, el dispositivo siempre notifica el concentrador que ha finalizado el mensaje:

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modificar el método **principal** para crear una instancia de **MessageCallback** y llame al método **setMessageCallback** antes de abrir al cliente como sigue:

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Si utiliza HTTP en lugar de MQTT o AMQP como transporte, la instancia de **DeviceClient** comprueba si hay mensajes IoT concentrador con poca frecuencia (menor que cada 25 minutos). Para obtener más información acerca de las diferencias entre soporte MQTT, AMQP y HTTP y limitación IoT concentrador, consulte la [Guía de desarrollador de concentrador IoT][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Enviar un mensaje de dispositivo de nube

En esta sección, creará una aplicación de consola de Java que envía mensajes de dispositivo de nube a la aplicación de dispositivo simulado. Se necesita el identificador del dispositivo que agregó en el tutorial de [Introducción a IoT concentrador] de dispositivo. También necesitará la cadena de conexión para su centro IoT que puede encontrar en el [portal de Azure].

1. Crear un proyecto de experto denominado **enviar mensajes de c2d** mediante el comando siguiente en el símbolo. Tenga en cuenta que se trata de un comando largo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva carpeta de enviar mensajes de c2d.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta c2d de envío de mensajes y agregue la siguiente dependencia al nodo **dependencias** . Agregar la dependencia le permite utilizar el paquete de **iothub java servicio cliente** en la aplicación para comunicarse con el servicio de concentrador IoT:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Agregue las siguientes instrucciones de **Importar** el archivo:

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Agregue las siguientes variables de nivel de clase para la clase de **aplicación** , reemplazando **{yourhubconnectionstring}** y **{yourdeviceid}** con los valores de su anotados anteriormente:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQP;
    ```
    
8. Reemplace el método **principal** con el siguiente código en la que se conecta a su centro IoT, envía un mensaje a su dispositivo y, a continuación, espera una confirmación de que el dispositivo ha recibido y procesado el mensaje:

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] Para simplificar, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, un crecimiento exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores].

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ya está listo para ejecutar las aplicaciones.

1. En una línea de comandos en la carpeta de dispositivo simulado, ejecute el comando siguiente para empezar a enviar telemetría a su centro IoT y para escuchar los mensajes de dispositivo de nube enviados desde su centro:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Ejecute la aplicación de dispositivo simulado][img-simulated-device]

2. En el símbolo en la carpeta c2d de envío de mensajes, ejecute el comando siguiente para enviar un mensaje de dispositivo de nube y esperar una confirmación de comentarios:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Ejecute el comando para enviar el mensaje de dispositivo de nube][img-send-command]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo enviar y recibir mensajes de la nube al dispositivo. 

Para ver ejemplos de soluciones to-end completas que usan IoT concentrador, vea [El conjunto de aplicaciones de Azure IoT].

Para obtener más información sobre cómo desarrollar soluciones con IoT concentrador, consulte la [Guía de desarrollador IoT concentrador].


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Introducción a IoT concentrador]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guía del desarrollador de concentrador IoT]: iot-hub-devguide.md
[Centro para desarrolladores de Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Control de errores transitorias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal de Azure]: https://portal.azure.com
[Conjunto de aplicaciones de Azure IoT]: https://azure.microsoft.com/documentation/suites/iot-suite/