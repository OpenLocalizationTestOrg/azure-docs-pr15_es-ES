<properties
    pageTitle="Azure concentrador IoT para Node.js Introducción | Microsoft Azure"
    description="Tutorial de inicio de Azure concentrador IoT con Node.js Introducción. Use Azure IoT concentrador y Node.js con el SDK de Microsoft Azure IoT implementar una solución de Internet de las cosas."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-nodejs"></a>Introducción a Azure IoT concentrador para Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al final de este tutorial, tiene tres aplicaciones de consola de Node.js:

* **CreateDeviceIdentity.js**, que crea una identidad de dispositivo y una clave de seguridad asociadas para conectar el dispositivo simulado.
* **ReadDeviceToCloudMessages.js**, que muestra la telemetría enviada por el dispositivo simulado.
* **SimulatedDevice.js**, que se conecta a su centro IoT con la identidad del dispositivo creada anteriormente y envía un mensaje de telemetría cada segundo mediante el protocolo AMQP.

> [AZURE.NOTE] El artículo [IoT concentrador SDK] [ lnk-hub-sdks] proporciona información sobre los distintos SDK que puede usar para crear dos aplicaciones para ejecutarse en dispositivos y back-end solución.

Para completar este tutorial, necesita lo siguiente:

+ Node.js versión 0.10.x o posterior.

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ya ha creado su centro IoT. Tiene el nombre de host de concentrador IoT y la cadena de conexión de IoT Hub que necesita para completar el resto de este tutorial.

## <a name="create-a-device-identity"></a>Crear una identidad de dispositivo

En esta sección, creará una aplicación de consola Node.js que crea una identidad de dispositivo en el registro de identidad en el hub de IoT. No se puede conectar un dispositivo a IoT concentrador a menos que tenga una entrada en el registro de la identidad de dispositivo. Consulte la sección **Registro de identidad de dispositivo** de la [Guía de desarrollador de concentrador IoT] [ lnk-devguide-identity] para obtener más información. Cuando se ejecuta esta aplicación de consola, que genera un identificador único del dispositivo y clave que puede usar el dispositivo para identificarse cuando envía mensajes de dispositivo a la nube a IoT concentrador.

1. Crear una nueva carpeta vacía denominada **createdeviceidentity**. En la carpeta **createdeviceidentity** , cree un archivo de package.json mediante el comando siguiente en el símbolo. Aceptar todos los valores predeterminados:

    ```
    npm init
    ```

2. En la línea de comandos en la carpeta **createdeviceidentity** , ejecute el comando siguiente para instalar el paquete de servicio SDK **iothub de azure** :

    ```
    npm install azure-iothub --save
    ```

3. Con un editor de texto, cree un archivo **CreateDeviceIdentity.js** en la carpeta **createdeviceidentity** .

4. Agregue las siguientes `require` instrucción al inicio del archivo **CreateDeviceIdentity.js** :

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Agregue el código siguiente al archivo **CreateDeviceIdentity.js** y reemplace el valor de marcador de posición con la cadena de conexión para el concentrador IoT que creó en la sección anterior: 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Agregue el código siguiente para crear una definición de dispositivo en el registro de la identidad de dispositivo en el hub de IoT. Este código crea un dispositivo si el identificador del dispositivo no existe en el registro, en caso contrario, devuelve la clave del dispositivo existente:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Guarde y cierre el archivo **CreateDeviceIdentity.js** .

8. Para ejecutar la aplicación **createdeviceidentity** , ejecute el comando siguiente en el símbolo en la carpeta createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Anote el **identificador del dispositivo** y la **clave de dispositivo**. Necesitará estos valores más adelante cuando se crea una aplicación que se conecta a IoT concentrador como un dispositivo.

> [AZURE.NOTE] El registro de la identidad de concentrador IoT sólo almacena las identidades de dispositivo para permitir el acceso seguro al concentrador. Almacena los identificadores de dispositivos y claves para usar como credenciales de seguridad y un indicador de habilitado o deshabilitado que puede usar para deshabilitar el acceso para un dispositivo individual. Si la aplicación debe almacenar otros metadatos específicos del dispositivo, debe utilizar una tienda específica de la aplicación. Consulte [Guía de desarrollador de concentrador IoT] [ lnk-devguide-identity] para obtener más información.

## <a name="receive-device-to-cloud-messages"></a>Recibir mensajes de la nube de dispositivo

En esta sección, creará una aplicación de consola Node.js que lee los mensajes de la nube de dispositivo de concentrador IoT. Un concentrador IoT expone un [Evento Hubs][lnk-event-hubs-overview]-extremo compatible para que pueda leer mensajes de dispositivo a la nube. Para mantener las cosas sencillas, este tutorial crea un lector básico que no es adecuado para una implementación de alto rendimiento. Los [mensajes de nube de dispositivo de proceso] [ lnk-process-d2c-tutorial] tutorial muestra cómo procesar los mensajes de la nube de dispositivo en escala. La [Introducción a evento Hubs] [ lnk-eventhubs-tutorial] tutorial proporciona más información sobre cómo para procesar los mensajes de evento Hubs y se aplica a los extremos compatibles con el concentrador IoT concentrador evento.

> [AZURE.NOTE] El extremo de evento concentrador compatibles para leer mensajes de la nube de dispositivo siempre usa el protocolo AMQP.

1. Crear una nueva carpeta vacía denominada **readdevicetocloudmessages**. En la carpeta **readdevicetocloudmessages** , cree un archivo de package.json mediante el comando siguiente en el símbolo. Aceptar todos los valores predeterminados:

    ```
    npm init
    ```

2. En la línea de comandos en la carpeta **readdevicetocloudmessages** , ejecute el comando siguiente para instalar el paquete de **hubs de evento de azure** :

    ```
    npm install azure-event-hubs --save
    ```

3. Con un editor de texto, cree un archivo **ReadDeviceToCloudMessages.js** en la carpeta **readdevicetocloudmessages** .

4. Agregue las siguientes `require` instrucciones al principio del archivo **ReadDeviceToCloudMessages.js** :

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Agregue la siguiente declaración de variables y reemplace el valor de marcador de posición con la cadena de conexión para su centro IoT:

    ```
    var connectionString = '{iothub connection string}';
    ```

6. Agregue las siguientes dos funciones que imprimir los resultados de la consola:

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Agregue el código siguiente para crear el **EventHubClient**, abra la conexión a su centro IoT y crear un receptor para cada partición. Esta aplicación utiliza un filtro cuando se crea un receptor para que el receptor sólo lee mensajes enviados a concentrador IoT el receptor se inicia ejecutando. Este filtro es útil en un entorno de prueba para ver solo el conjunto actual de mensajes. En un entorno de producción, el código debe asegurarse de que procesa todos los mensajes: vea [cómo procesar los mensajes de nube de dispositivo concentrador IoT] [ lnk-process-d2c-tutorial] tutoriales para obtener más información:

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Guarde y cierre el archivo **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Crear una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola Node.js que simula un dispositivo que envía mensajes de nube de dispositivo a un concentrador IoT.

1. Crear una nueva carpeta vacía denominada **simulateddevice**. En la carpeta **simulateddevice** , cree un archivo de package.json mediante el comando siguiente en el símbolo. Aceptar todos los valores predeterminados:

    ```
    npm init
    ```

2. En la línea de comandos en la carpeta **simulateddevice** , ejecute el comando siguiente para instalar el **dispositivo de azure iot** dispositivo SDK y paquete de **azure-iot-dispositivo-amqp** :

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Con un editor de texto, cree un nuevo archivo de **SimulatedDevice.js** en la carpeta **simulateddevice** .

4. Agregue las siguientes `require` instrucciones al principio del archivo **SimulatedDevice.js** :

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Agregar una variable de **cadena de conexión** y usarlo para crear a un cliente del dispositivo. Reemplace **{youriothostname}** con el nombre del concentrador IoT que creó la sección *crear un concentrador IoT* . Reemplace **{yourdevicekey}** con el valor de clave de dispositivo generadas en la sección *crear una identidad del dispositivo* :

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Agregue la siguiente función para mostrar los resultados de la aplicación:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Crear una devolución de llamada y usar la función **setInterval** para enviar un mensaje a su centro IoT cada segundo:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

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

8. Abrir la conexión a su centro IoT y comenzar a enviar mensajes:

    ```
    client.open(connectCallback);
    ```

9. Guarde y cierre el archivo **SimulatedDevice.js** .

> [AZURE.NOTE] Para mantener las cosas sencillas, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, una interrupción exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores][lnk-transient-faults].


## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ya está listo para ejecutar las aplicaciones.

1. En el símbolo en la carpeta **readdevicetocloudmessages** , ejecute el comando siguiente para empezar a supervisar su centro IoT:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![Aplicación de cliente de servicios de Node.js IoT concentrador para supervisar los mensajes de la nube de dispositivo][7]

2. En una línea de comandos en la carpeta **simulateddevice** , ejecute el comando siguiente para comenzar a enviar los datos de telemetría a su centro IoT:

    ```
    node SimulatedDevice.js
    ```

    ![Aplicación de cliente de dispositivo Node.js IoT concentrador para enviar mensajes de la nube de dispositivo][8]

3. El mosaico de **uso** en el [portal de Azure] [ lnk-portal] muestra el número de mensajes enviados al concentrador:

    ![Azure portal uso mosaico mostrando el número de mensajes enviados a IoT concentrador][43]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, configurar un nuevo concentrador IoT en el portal de Azure y, a continuación, crea una identidad de dispositivo en registro de identidad del concentrador. Utilizar este dispositivo identidad para habilitar la aplicación de dispositivo simulado enviar mensajes de la nube de dispositivo al concentrador. También crea una aplicación que muestra los mensajes recibidos por el concentrador. 

Para continuar la introducción a IoT concentrador y explorar otros escenarios IoT, consulte:

- [Conectar el dispositivo][lnk-connect-device]
- [Introducción a la administración de dispositivos][lnk-device-management]
- [Introducción al trabajo con el SDK de puerta de enlace IoT][lnk-gateway-SDK]

Para obtener información sobre cómo ampliar su solución IoT y procesar los mensajes de la nube de dispositivo en escala, vea los [mensajes de nube de dispositivo de proceso] [ lnk-process-d2c-tutorial] tutorial.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
