<properties
    pageTitle="Enviar mensajes de dispositivo de nube con concentrador IoT | Microsoft Azure"
    description="Siga este tutorial para obtener información sobre cómo enviar mensajes de dispositivo de nube con Azure IoT concentrador con Java."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-nodejs"></a>Tutorial: Cómo enviar mensajes de dispositivo de nube con IoT concentrador y Node.js

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introducción

Hub de Azure IoT es un servicio totalmente gestionado que ayuda a habilitar a confiables y seguras comunicaciones bidireccional entre millones de dispositivos IoT y finalizar una aplicación de nuevo. El tutorial de [Introducción a IoT concentrador] muestra cómo crear un concentrador IoT, aprovisionar la identidad de un dispositivo en ella y el código de un dispositivo simulado que envía mensajes de dispositivo a la nube.

En este tutorial se basa en [Introducción a IoT concentrador]. Muestra cómo para:

- Desde su aplicación nube back-end, enviar mensajes de dispositivo de nube a un único dispositivo a través de concentrador IoT.
- Recibir mensajes de la nube para el dispositivo en un dispositivo.
- En la nube de aplicación back-end, solicitar confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo concentrador IoT.

Puede encontrar más información sobre los mensajes de dispositivo de nube en la [Guía de desarrollador de concentrador IoT][IoT Hub Developer Guide - C2D].

Al final de este tutorial, ejecute dos aplicaciones de consola de Node.js:

* **SimulatedDevice**, una versión modificada de la aplicación creada en [Introducción a IoT concentrador], que se conecta a su centro IoT y recibe mensajes de la nube al dispositivo.
* **SendCloudToDeviceMessage**, que envía un mensaje de dispositivo de nube al dispositivo simulado a través de concentrador IoT y, a continuación, recibe la confirmación de entrega.

> [AZURE.NOTE] Concentrador IoT admite SDK muchas plataformas de dispositivos e idiomas (incluidos C, Java y Javascript) a través de Azure IoT SDK de dispositivos. Para obtener instrucciones detalladas sobre cómo conectar el dispositivo a código de este tutorial y generalmente a Azure IoT concentrador, consulte el [Centro para desarrolladores de Azure IoT].

Para completar este tutorial, necesita lo siguiente:

+ Node.js versión 0.10.x o posterior.

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

## <a name="receive-messages-on-the-simulated-device"></a>Recibir mensajes en el dispositivo simulado

En esta sección, modifique la aplicación de dispositivo simulado creado en [Introducción a IoT concentrador] para recibir mensajes de dispositivo de nube de concentrador IoT.

1. Con un editor de texto, abra el archivo SimulatedDevice.js.

2. Modifique la función **connectCallback** para controlar los mensajes enviados desde IoT concentrador. En este ejemplo, el dispositivo siempre invoca la función **completa** para que le notifique IoT concentrador que ha procesado el mensaje. La nueva versión de la función **connectCallback** es similar a esta:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] Si utiliza HTTP en lugar de MQTT o AMQP como transporte, la instancia de **DeviceClient** comprueba si hay mensajes IoT concentrador con poca frecuencia (menor que cada 25 minutos). Para obtener más información acerca de las diferencias entre soporte MQTT, AMQP y HTTP y limitación IoT concentrador, consulte la [Guía de desarrollador de concentrador IoT][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Enviar un mensaje de dispositivo de nube

En esta sección, creará una aplicación de consola Node.js que envía mensajes de dispositivo de nube a la aplicación de dispositivo simulado. Se necesita el identificador del dispositivo que agregó en el tutorial de [Introducción a IoT concentrador] de dispositivo. También necesitará la cadena de conexión para su centro IoT que puede encontrar en el [portal de Azure].

1. Crear una carpeta vacía denominada **sendcloudtodevicemessage**. En la carpeta **sendcloudtodevicemessage** , cree un archivo de package.json mediante el comando siguiente en el símbolo. Aceptar todos los valores predeterminados:

    ```
    npm init
    ```

2. En la línea de comandos en la carpeta **sendcloudtodevicemessage** , ejecute el comando siguiente para instalar el paquete de **azure iothub** :

    ```
    npm install azure-iothub --save
    ```

3. Con un editor de texto, cree un nuevo archivo de **SendCloudToDeviceMessage.js** en la carpeta **sendcloudtodevicemessage** .

4. Agregue las siguientes `require` instrucciones al principio del archivo **SendCloudToDeviceMessage.js** :

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Agregue el código siguiente al archivo **SendCloudToDeviceMessage.js** . Reemplace el valor de marcador de posición de la cadena de conexión con la cadena de conexión para el concentrador IoT que creó en el tutorial de [Introducción a IoT concentrador] . Reemplazar el marcador de posición del dispositivo de destino con el identificador de dispositivo del dispositivo que agregó en el tutorial de [Introducción a IoT concentrador] :

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Agregue la siguiente función para imprimir los resultados de la operación en la consola:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Agregue la siguiente función para imprimir los mensajes de comentarios de entrega en la consola:

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Agregue el código siguiente para enviar un mensaje a su dispositivo y controlar el mensaje de comentarios cuando el dispositivo reconoce el mensaje de dispositivo de nube:

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. Guarde y cierre el archivo **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ya está listo para ejecutar las aplicaciones.

1. En la línea de comandos en la carpeta **simulateddevice** , ejecute el siguiente comando para enviar telemetría IoT concentrador y para escuchar los mensajes de dispositivo de nube:

    ```
    node SimulatedDevice.js 
    ```

    ![Ejecute la aplicación de dispositivo simulado][img-simulated-device]

2. En una línea de comandos en la carpeta **sendcloudtodevicemessage** , ejecute el comando siguiente para enviar un mensaje de dispositivo de nube y espere a que los comentarios de confirmación:

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![Ejecute la aplicación para enviar el comando c2d][img-send-command]

    > [AZURE.NOTE] Para simplificar, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, un crecimiento exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores].

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo enviar y recibir mensajes de la nube al dispositivo. 

Para ver ejemplos de soluciones to-end completas que usan IoT concentrador, vea [El conjunto de aplicaciones de Azure IoT].

Para obtener más información sobre cómo desarrollar soluciones con IoT concentrador, consulte la [Guía de desarrollador IoT concentrador].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Introducción a IoT concentrador]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guía del desarrollador de concentrador IoT]: iot-hub-devguide.md
[Centro para desarrolladores de Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Control de errores transitorias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal de Azure]: https://portal.azure.com
[Conjunto de aplicaciones de Azure IoT]: https://azure.microsoft.com/documentation/suites/iot-suite/