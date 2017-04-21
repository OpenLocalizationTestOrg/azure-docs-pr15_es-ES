<properties
 pageTitle="Usar métodos directos | Microsoft Azure"
 description="Este tutorial muestra cómo usar los métodos directos"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="nberdy"/>

# <a name="tutorial-use-direct-methods"></a>Tutorial: Usar métodos directos

## <a name="introduction"></a>Introducción

Azure concentrador IoT es un servicio totalmente gestionado que permite confiable y comunicaciones seguras de bidireccional entre millones de IoT dispositivos y una aplicación back-end. Tutoriales anterior ([Introducción a IoT concentrador] y [enviar mensajes de dispositivo de nube con concentrador IoT]) ilustran el dispositivo a la nube y dispositivo de nube mensajería funcionalidad básica del concentrador IoT. Concentrador IoT también le ofrece la capacidad de invocar métodos entrante en dispositivos de la nube. Métodos representan una interacción solicitud responder con un dispositivo similar a una llamada HTTP en que se realizan correctamente o un error inmediatamente (después de un tiempo de espera especificado por el usuario) para el usuario comunicar el estado de la llamada. [Llamar a un método directo en un dispositivo] [ lnk-devguide-methods] se describen los métodos con más detalle y ofrece orientación sobre cuándo usar métodos frente a mensajes de nube al dispositivo.

En este tutorial se muestra cómo para:

- Usar el portal de Azure para crear un concentrador IoT y crear una identidad de dispositivo en el hub de IoT.
- Crear un dispositivo simulado con un método directo que puede llamar a la nube.
- Crear una aplicación de consola que llama a un método directo en el dispositivo a través de su centro IoT simulado.

> [AZURE.NOTE] En este momento, métodos directos sólo son accesibles desde dispositivos que se conectan a concentrador IoT mediante el protocolo MQTT. Consulte el [soporte técnico MQTT] [ lnk-devguide-mqtt] artículo para obtener instrucciones sobre cómo convertir la aplicación de dispositivo existente usar MQTT.

Al final de este tutorial, tiene dos aplicaciones de consola de Node.js:

* **CallMethodOnDevice.js**, que llama a un método en el dispositivo simulado y muestra la respuesta.
* **SimulatedDevice.js**, que se conecta a su centro IoT con la identidad del dispositivo creada anteriormente y responde al método llamado a la nube.

> [AZURE.NOTE] El artículo [IoT concentrador SDK] [ lnk-hub-sdks] proporciona información sobre los distintos SDK que puede usar para crear dos aplicaciones para ejecutarse en dispositivos y back-end solución.

Para completar este tutorial, necesita lo siguiente:

+ Node.js versión 0.10.x o posterior.

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Crear una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola Node.js que responde a un método que se llama a la nube.

1. Crear una nueva carpeta vacía denominada **simulateddevice**. En la carpeta **simulateddevice** , cree un archivo de package.json mediante el comando siguiente en el símbolo. Aceptar todos los valores predeterminados:

    ```
    npm init
    ```

2. En la línea de comandos en la carpeta **simulateddevice** , ejecute el comando siguiente para instalar el **dispositivo de azure iot** dispositivo SDK y paquete de **azure-iot-dispositivo-mqtt** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo de **SimulatedDevice.js** en la carpeta **simulateddevice** .

4. Agregue las siguientes `require` instrucciones al principio del archivo **SimulatedDevice.js** :

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. Agregar una variable de **cadena de conexión** y usarlo para crear a un cliente del dispositivo. Reemplace **{cadena de conexión de dispositivo}** con la cadena de conexión generadas en la sección *crear una identidad del dispositivo* :

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. Agregue la siguiente función para implementar el método en el dispositivo:

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. Abrir la conexión a su concentrador IoT y empezar a inicializar la escucha método:

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. Guarde y cierre el archivo **SimulatedDevice.js** .

> [AZURE.NOTE] Para mantener las cosas sencillas, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (como intentos de conexión), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores][lnk-transient-faults].

## <a name="call-a-method-on-a-device"></a>Llamar a un método en un dispositivo

En esta sección, creará una aplicación de consola de Node.js que llama a un método en el dispositivo simulado y, a continuación, se muestra la respuesta.

1. Crear una nueva carpeta vacía denominada **callmethodondevice**. En la carpeta **callmethodondevice** , cree un archivo de package.json mediante el comando siguiente en el símbolo. Aceptar todos los valores predeterminados:

    ```
    npm init
    ```

2. En la línea de comandos en la carpeta **callmethodondevice** , ejecute el comando siguiente para instalar el paquete de **azure iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Con un editor de texto, cree un archivo **CallMethodOnDevice.js** en la carpeta **callmethodondevice** .

4. Agregue las siguientes `require` instrucciones al principio del archivo **CallMethodOnDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Agregue la siguiente declaración de variables y reemplace el valor de marcador de posición con la cadena de conexión para su centro IoT:

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. Crear el cliente para abrir la conexión a su centro IoT.

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. Agregue la siguiente función para invocar el método de dispositivo e imprimir la respuesta a la consola del dispositivo:

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. Guarde y cierre el archivo **CallMethodOnDevice.js** .

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ya está listo para ejecutar las aplicaciones.

1. En el símbolo en la carpeta **simulateddevice** , ejecute el comando siguiente para empezar a escuchar las llamadas de método de su centro IoT:

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. En el símbolo en la carpeta **callmethodondevice** , ejecute el comando siguiente para empezar a supervisar su centro IoT:

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. Verá el dispositivo ante el método imprimiendo el mensaje y la aplicación que llama a la pantalla método la respuesta desde el dispositivo:

    ![][9]
    
## <a name="next-steps"></a>Pasos siguientes

En este tutorial, configurar un nuevo concentrador IoT en el portal de Azure y, a continuación, crea una identidad de dispositivo en registro de identidad del concentrador. Utilizar este dispositivo identidad para habilitar la aplicación de dispositivo simulado ante los métodos invocados la nube. También crea una aplicación que se invoca métodos en el dispositivo y muestra la respuesta del dispositivo. 

Para continuar la introducción a IoT concentrador y explorar otros escenarios IoT, consulte:

- [Introducción a IoT concentrador]
- [Programar trabajos en varios dispositivos][lnk-devguide-jobs]

Para obtener información sobre cómo ampliar su IoT llamadas de método de solución y programación en varios dispositivos, vea [programación y trabajos difusión] [ lnk-tutorial-jobs] tutorial.

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Enviar mensajes de la nube para dispositivos con IoT concentrador]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introducción a IoT concentrador]: iot-hub-node-node-getstarted.md
