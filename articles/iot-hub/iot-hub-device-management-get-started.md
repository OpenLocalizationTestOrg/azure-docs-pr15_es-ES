<properties
 pageTitle="Introducción a la administración de dispositivos | Microsoft Azure"
 description="En este tutorial se muestra cómo empezar a trabajar con la administración de dispositivos en Azure IoT concentrador"
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

# <a name="tutorial-get-started-with-device-management-preview"></a>Tutorial: Introducción a la administración de dispositivos (vista preliminar)

## <a name="introduction"></a>Introducción
Aplicaciones de nube IoT pueden usar a Fundamentos en Azure IoT concentrador, es decir, el doble de dispositivo y directa de los métodos para iniciar de forma remota y supervisar las acciones de administración de dispositivos en dispositivos.  Este artículo proporciona instrucciones y el código de funcionamiento de una aplicación de nube IoT y un dispositivo para iniciar y supervisar reiniciar dispositivo remoto usando IoT concentrador.

Para iniciar y supervisar las acciones de administración de dispositivos en los dispositivos desde una aplicación basada en la nube y back-end de forma remota, utilice los fundamentos de Azure IoT concentrador como [doble dispositivo] [ lnk-devtwin] y [métodos de dispositivo de nube (C2D)][lnk-c2dmethod]. Este tutorial le muestra cómo una aplicación de back-end y un dispositivo pueden funcionar juntos para permitirle iniciar y supervisar el reinicio del dispositivo remoto IoT concentrador.

Usar un método C2D para iniciar acciones de administración del dispositivo (por ejemplo, reinicie, restablecimiento de fábrica y actualización de firmware) desde una aplicación de back-end en la nube. El dispositivo es responsable de:

- Control de la solicitud de método enviada desde IoT concentrador.
- Iniciar la acción específica de dispositivo correspondiente en el dispositivo.
- Proporcionar actualizaciones de estado a través de los dos dispositivos registrado propiedades IoT concentrador.

Puede usar una aplicación de back-end en la nube para ejecutar consultas de doble dispositivo para informar del progreso de las acciones de administración de dispositivos.

En este tutorial se muestra cómo para:

- Usar el portal de Azure para crear un concentrador IoT y crear una identidad de dispositivo en el hub de IoT.
- Crear un dispositivo simulado con un método directo que permite reiniciar el equipo que puede llamar a la nube.
- Crear una aplicación de consola que llama el método directo de reiniciar el equipo en el dispositivo a través de su centro IoT simulado.

Al final de este tutorial, tiene dos aplicaciones de consola de Node.js:

**dmpatterns_getstarted_device.js**, que se conecta a su centro IoT con la identidad del dispositivo creada anteriormente, recibe un método directo de reinicio, simula físico reiniciar el equipo, informes y la hora para el último reinicio.

**dmpatterns_getstarted_service.js**, que se llama a un método directo en el dispositivo simulado, muestra la respuesta y muestra al doble de dispositivo actualizado informa de propiedades.

Para completar este tutorial, necesita lo siguiente:

Node.js versión 0.12.x o posterior, <br/>  [Preparar el entorno de desarrollo] [ lnk-dev-setup] describe cómo instalar Node.js para este tutorial en Windows o Linux.

Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Crear una aplicación de dispositivo simulado

En esta sección, se crea una aplicación de consola Node.js que responde a un método directo llamado a la nube, que se activa un dispositivo simulado reiniciar el equipo y utiliza el doble de dispositivo notificado propiedades para habilitar las consultas de doble del dispositivo identificar los dispositivos y cuándo reinicia última.

1. Crear una nueva carpeta vacía denominada **manageddevice**.  En la carpeta **manageddevice** , cree un archivo de package.json mediante el comando siguiente en el símbolo.  Aceptar todos los valores predeterminados:

    ```
    npm init
    ```
    
2. En la línea de comandos en la carpeta **manageddevice** , ejecute el comando siguiente para instalar la **azure-iot-device@dtpreview** paquete SDK del dispositivo y **azure-iot-device-mqtt@dtpreview** paquete:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo de **dmpatterns_getstarted_device.js** en la carpeta **manageddevice** .

4. Agregar el siguiente 'requerir' instrucciones al principio del archivo **dmpatterns_getstarted_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Agregar una variable de **cadena de conexión** y usarlo para crear a un cliente del dispositivo.  Reemplazar la cadena de conexión con la cadena de conexión del dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Agregue la siguiente función para implementar el método directo en el dispositivo

    ```
    var onReboot = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
        
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Abra la conexión a su centro IoT e inicie la escucha método directo:

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
    
8. Guarde y cierre el archivo **dmpatterns_getstarted_device.js** .

 [AZURE.NOTE] Para mantener las cosas sencillas, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, una interrupción exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores][lnk-transient-faults].

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Reiniciar el equipo remoto en el dispositivo usando un método directo de desencadenador 

En esta sección, creará una aplicación de consola Node.js que inicia un reinicio remoto en un dispositivo con un método directo y consultas de doble del dispositivo se utiliza para buscar la última vez que reiniciar el equipo para ese dispositivo.

1. Crear una nueva carpeta vacía denominada **triggerrebootondevice**.  En la carpeta **triggerrebootondevice** , cree un archivo de package.json mediante el comando siguiente en el símbolo.  Aceptar todos los valores predeterminados:

    ```
    npm init
    ```
    
2. En la línea de comandos en la carpeta **triggerrebootondevice** , ejecute el comando siguiente para instalar la **azure-iothub@dtpreview** paquete SDK del dispositivo y **azure-iot-device-mqtt@dtpreview** paquete:

    ```
    npm install azure-iothub@dtpreview --save
    ```
    
3. Con un editor de texto, cree un nuevo archivo de **dmpatterns_getstarted_service.js** en la carpeta **triggerrebootondevice** .

4. Agregar el siguiente 'requerir' instrucciones al principio del archivo **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Agregue las siguientes declaraciones de variables y reemplace los valores de marcador de posición:

    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
    
6. Agregue la siguiente función para invocar el método de dispositivo para reiniciar el dispositivo de destino:

    ```
    var startRebootDevice = function(twin) {

        var methodName = "reboot";
        
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
        
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Agregue la siguiente función para crear una consulta con el dispositivo y obtener la última vez que reiniciar el equipo:

    ```
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
    
8. Agregue el código siguiente para llamar a las funciones que se activarán el método directo de reiniciar el equipo y la consulta por última vez reinicio:

    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
    
9. Guarde y cierre el archivo **dmpatterns_getstarted_service.js** .

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ya está listo para ejecutar las aplicaciones.

1. En la línea de comandos en la carpeta **manageddevice** , ejecute el comando siguiente para comenzar el método directo de reiniciar el equipo.

    ```
    node dmpatterns_getstarted_device.js
    ```

2. En la línea de comandos en la carpeta **triggerrebootondevice** , ejecute el comando siguiente para activar el reinicio remoto y para consultar los dos dispositivos buscar la última reiniciar el tiempo.

    ```
    node dmpatterns_getstarted_service.js
    ```

3. Verá reaccionar el método directo al imprimir el mensaje

## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar y ampliar el dispositivo acciones de administración

Las soluciones IoT pueden expandir el conjunto de patrones de administración de dispositivos definido o habilitar modelos personalizados mediante el uso de los dos dispositivos y fundamentos de C2D método. Otras acciones de administración de dispositivos ejemplos de restablecimiento de fábrica, actualización de firmware, actualización de software, administración de energía, conectividad de red y administración y cifrado de datos.

## <a name="device-maintenance-windows"></a>Ventanas de mantenimiento de dispositivo

Normalmente, configurar dispositivos para realizar acciones en un momento que minimice las interrupciones y tiempo de inactividad.  Ventanas de mantenimiento de dispositivo son un patrón utilizado para definir el tiempo cuando un dispositivo debe actualizar su configuración. Las soluciones de back-end pueden usar las propiedades de los dos dispositivos que desee definir y activar una directiva en el dispositivo que permite una ventana de mantenimiento. Cuando un dispositivo recibe la directiva de la ventana de mantenimiento, puede usar la propiedad informada de los dos dispositivos para informar del estado de la directiva. La aplicación de back-end, a continuación, puede usar las consultas de doble del dispositivo para dar fe de cumplimiento de cada directiva y dispositivos.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, utiliza un método directo para desencadenar un reinicio remoto en un dispositivo, utilizados doble de dispositivo notificado propiedades para informar de la última vez que reinicie el dispositivo y consultar el doble de dispositivo descubrir la última vez que reinicio del dispositivo de la nube.

Para continuar con la introducción a IoT concentrador y modelos de administración de dispositivos como remoto sobre la actualización de firmware de aire, consulte:

[Tutorial: Cómo realizar una actualización del firmware][lnk-fwupdate]

Para obtener información sobre cómo ampliar su IoT método de solución y programación llama en varios dispositivos, vea [programación y trabajos difusión] [ lnk-tutorial-jobs] tutorial.

Para continuar con la introducción a IoT concentrador, consulte [Introducción al SDK de puerta de enlace IoT][lnk-gateway-SDK].


<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx