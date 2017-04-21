<properties
 pageTitle="Cómo programar trabajos | Microsoft Azure"
 description="En este tutorial se muestra cómo programar trabajos"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="tutorial-schedule-and-broadcast-jobs-preview"></a>Tutorial: Programar y difundir trabajos (vista preliminar)

## <a name="introduction"></a>Introducción
Hub de Azure IoT es un servicio totalmente administrado que permite a un servidor de aplicaciones crear y realizar un seguimiento de los trabajos de programación y actualización millones de dispositivos.  Trabajos de pueden usarse para las siguientes acciones:

- Actualizar propiedades de doble deseado del dispositivo
- Actualizar las etiquetas del dispositivo doble
- Invocar métodos de dispositivo de nube

Conceptualmente, un trabajo ajusta a una de estas acciones y realiza un seguimiento del progreso de la ejecución de un conjunto de dispositivos, que se define mediante una consulta de doble.  Por ejemplo, mediante un trabajo de un servidor de aplicaciones puede invocar un método de reinicio en 10.000 dispositivos, especificada por una consulta de doble y programada en el futuro.  Aplicación, a continuación, seguir el progreso como cada uno de los dispositivos que reciban y ejecutar el método de reiniciar el equipo.

Más información sobre cada una de estas funciones en estos artículos:

- Dos dispositivos y propiedades: [Introducción a doble] [ lnk-get-started-twin] y [Tutorial: cómo usar las propiedades de doble][lnk-twin-props]
- Métodos de dispositivo de nube: [Guía para programadores - métodos directos] [ lnk-dev-methods] y [Tutorial: métodos de C2D][lnk-c2d-methods]

En este tutorial se muestra cómo para:

- Crear un dispositivo simulado con un método directo que permite lockDoor que se puede llamar por la aplicación volver final.
- Crear una aplicación de consola que llama el método directo de lockDoor en el dispositivo simulado con un trabajo y actualice las propiedades de doble deseado mediante un trabajo de dispositivo.

Al final de este tutorial, tiene dos aplicaciones de consola de Node.js:

**simDevice.js**, que se conecta a su centro IoT con la identidad del dispositivo y recibe un método directo de lockDoor.

**scheduleJobService.js**, que llama a un método directo en el dispositivo simulado y actualizar las propiedades de deseados de doble mediante un trabajo.

Para completar este tutorial, necesita lo siguiente:

Node.js versión 0.12.x o posterior, <br/>  [Preparar el entorno de desarrollo] [ lnk-dev-setup] describe cómo instalar Node.js para este tutorial en Windows o Linux.

Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Crear una aplicación de dispositivo simulado

En esta sección, se crea una aplicación de consola Node.js que responde a un método directo llamado a la nube, que se activa un dispositivo simulado reiniciar el equipo y utiliza el doble de dispositivo notificado propiedades para habilitar las consultas de doble del dispositivo identificar los dispositivos y cuándo reinicia última.

1. Crear una nueva carpeta vacía denominada **simDevice**.  En la carpeta **simDevice** , cree un archivo de package.json mediante el comando siguiente en el símbolo.  Aceptar todos los valores predeterminados:

    ```
    npm init
    ```
    
2. En la línea de comandos en la carpeta **simDevice** , ejecute el comando siguiente para instalar el **dispositivo de azure iot** dispositivo SDK y paquete de **azure-iot-dispositivo-mqtt** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo de **simDevice.js** en la carpeta **simDevice** .

4. Agregar el siguiente 'requerir' instrucciones al principio del archivo **simDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Agregar una variable de **cadena de conexión** y usarlo para crear a un cliente del dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Agregue la siguiente función para controlar el método lockDoor.

    ```
    var onLockDoor = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        console.log('Locking Door!');
    };
    ```

7. Agregue el código siguiente para registrar el controlador para el método lockDoor.

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for reboot direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
    
8. Guarde y cierre el archivo **simDevice.js** .

> [AZURE.NOTE] Para mantener las cosas sencillas, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, una interrupción exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores][lnk-transient-faults].

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-twins-properties"></a>Programar trabajos para llamar a un método directo y actualizar las propiedades de un doble

En esta sección, crear una aplicación de consola Node.js que inicia un lockDoor remoto en un dispositivo con un método directo y actualizar las propiedades de doble.

1. Crear una nueva carpeta vacía denominada **scheduleJobService**.  En la carpeta **scheduleJobService** , cree un archivo de package.json mediante el comando siguiente en el símbolo.  Aceptar todos los valores predeterminados:

    ```
    npm init
    ```
    
2. En la línea de comandos en la carpeta **scheduleJobService** , ejecute el comando siguiente para instalar el paquete de dispositivo SDK **iothub de azure** y **azure-iot-dispositivo-mqtt** paquete:

    ```
    npm install azure-iothub@dtpreview uuid --save
    ```
    
3. Con un editor de texto, cree un nuevo archivo de **scheduleJobService.js** en la carpeta **scheduleJobService** .

4. Agregar el siguiente 'requerir' instrucciones al principio del archivo **dmpatterns_gscheduleJobServiceetstarted_service.js** :

    ```
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Agregue las siguientes declaraciones de variables y reemplace los valores de marcador de posición:

    ```
    var connectionString = '{iothubconnectionstring}';
    var deviceArray = ['myDeviceId'];
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  3600;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
    
6. Agregue la siguiente función que se utilizará para supervisar la ejecución del trabajo:

    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Agregue el código siguiente para programar el trabajo que llama al método de dispositivo:

    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        timeoutInSeconds: 45
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                deviceArray,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
        
8. Agregue el código siguiente para programar el trabajo para actualizar al doble:

    ```
    var twinPatch = {
        etag: '*',
        desired: {
            building: '43',
            floor: 3
        }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                deviceArray,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
    
9. Guarde y cierre el archivo **scheduleJobService.js** .

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ya está listo para ejecutar las aplicaciones.

1. En la línea de comandos en la carpeta **simDevice** , ejecute el comando siguiente para comenzar el método directo de reiniciar el equipo.

    ```
    node simDevice.js
    ```

2. En la línea de comandos en la carpeta **scheduleJobService** , ejecute el comando siguiente para activar el reinicio remoto y para consultar los dos dispositivos buscar la última reiniciar el tiempo.

    ```
    node scheduleJobService.js
    ```

3. Verá el resultado de dispositivo y aplicaciones de back-end.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha utilizado un trabajo para programar un método directo a un dispositivo y la actualización de propiedades de la doble dispositivo.

Para continuar con la introducción a IoT concentrador y modelos de administración de dispositivos como remoto sobre la actualización de firmware de aire, consulte:

[Tutorial: Cómo realizar una actualización del firmware][lnk-fwupdate]

Para continuar con la introducción a IoT concentrador, consulte [Introducción al SDK de puerta de enlace IoT][lnk-gateway-SDK].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx