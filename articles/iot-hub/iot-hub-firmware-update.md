<properties
 pageTitle="Cómo hacer una actualización de firmware | Microsoft Azure"
 description="Este tutorial muestra cómo realizar una actualización del firmware"
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

# <a name="tutorial-how-to-do-a-firmware-update-preview"></a>Tutorial: Cómo hacer un firmware update (vista preliminar)

## <a name="introduction"></a>Introducción
En la [Introducción a la administración de dispositivos] [ lnk-dm-getstarted] tutorial, ha visto cómo usar el [doble de dispositivo] [ lnk-devtwin] y [métodos de dispositivo de nube (C2D)] [ lnk-c2dmethod] Fundamentos reiniciar un dispositivo de forma remota. Este tutorial utiliza a la misma Fundamentos de concentrador IoT y proporciona orientación y le muestra cómo realizar una actualización de firmware simulada de llevar a cabo.  Este patrón se utiliza en la implementación de la actualización de firmware de la muestra de dispositivo Intel Edison.

En este tutorial se muestra cómo para:

- Crear una aplicación de consola que llama el método directo de firmwareUpdate en el dispositivo a través de su centro IoT simulado.
- Crear un dispositivo simulado que implementa un método directo de firmwareUpdate que se envía a través de un proceso de varias fases que espera para descargar la imagen de firmware, descargas de la imagen de firmware y por último se aplica a la imagen de firmware th.  Durante la ejecución de cada fase utiliza el dispositivo el doble notificadas propiedades Actualizar progreso.

Al final de este tutorial, tiene dos aplicaciones de consola de Node.js:

**dmpatterns_fwupdate_service.js**, que se llama a un método directo en el dispositivo simulado, muestra la respuesta y periódicamente informaron de (cada 500 ms) muestra los dos dispositivos actualizados propiedades.

**dmpatterns_fwupdate_device.js**, que se conecta a su centro IoT con la identidad del dispositivo creada anteriormente, recibe un método directo de firmwareUpdate, se ejecuta a través de un proceso de varios estado para simular una actualización de firmware incluidos: esperando la descarga de imágenes, descarga la nueva imagen y por último aplicar la imagen.


Para completar este tutorial, necesita lo siguiente:

Node.js versión 0.12.x o posterior, <br/>  [Preparar el entorno de desarrollo] [ lnk-dev-setup] describe cómo instalar Node.js para este tutorial en Windows o Linux.

Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

Siga el artículo [Introducción a la administración de dispositivos](iot-hub-device-management-get-started.md) para crear su centro IoT y obtener la cadena de conexión.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Crear una aplicación de dispositivo simulado

En esta sección, se crea una aplicación de consola Node.js que responde a un método directo llamado a la nube, que se activa una actualización de firmware del dispositivo simulado y utiliza el doble de dispositivo notificado propiedades para habilitar las consultas de doble del dispositivo identificar los dispositivos y cuándo reinicia última.

1. Crear una nueva carpeta vacía denominada **manageddevice**.  En la carpeta **manageddevice** , cree un archivo de package.json mediante el comando siguiente en el símbolo.  Aceptar todos los valores predeterminados:

    ```
    npm init
    ```
    
2. En la línea de comandos en la carpeta **manageddevice** , ejecute el comando siguiente para instalar la **azure-iot-device@dtpreview** paquete SDK del dispositivo y **azure-iot-device-mqtt@dtpreview** paquete:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo de **dmpatterns_fwupdate_device.js** en la carpeta **manageddevice** .

4. Agregar el siguiente 'requerir' instrucciones al principio del archivo **dmpatterns_fwupdate_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Agregar una variable de **cadena de conexión** y usarlo para crear a un cliente del dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Agregue la siguiente función que se utilizará para actualizar doble notificadas propiedades

    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };

      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
    
7. Agregue las siguientes funciones que simular la descarga y aplicar de la imagen de firmware.

    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
      
      console.log("Downloading image from " + imageUrl);
      
      callback(error, image);
    }

    var simulateApplyImage = function(imageData, callback) {
      var error = null;
      
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
      
      callback(error);
    }
    ```

8. Agregue la siguiente función que se actualizará el estado de actualización de firmware a través de la doble registrado propiedades esperando para descargar.  Normalmente, se le informará de dispositivos de una actualización disponible y causas directiva define un administrador del dispositivo para iniciar la descarga y aplicación de la actualización.  Esto es donde se ejecuta la lógica para habilitar esta directiva.  Para simplificar, estamos retrasando de 4 segundos y realizar la descarga de la imagen de firmware. 

    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
      
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
    
9. Agregue la siguiente función que se actualizará el estado de actualización de firmware a través de la doble notificadas propiedades para descargar la imagen de firmware.  Sigue hacia arriba al simular una descarga de firmware y por último actualiza el estado de actualización de firmware para informar de un error o de éxito de descarga.

    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
      
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
          
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
            
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
        
      }, 4000);
    }
    ```
    
10. Agregue la siguiente función que se actualizará el estado de actualización de firmware a través de la doble notificadas propiedades para aplicar la imagen de firmware.  Sigue hacia arriba al simular una aplicación de la imagen de firmware y por último actualiza el estado de actualización de firmware para informar de un error o de éxito de aplicar.

    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
      
      setTimeout(function() {
        
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
            
          }
        });
        
        setTimeout(callback, 4000);
        
      }, 4000);
    }
    ```

11. Agregar el siguiente functoin que controlar el método de firmwareUpdate e iniciar el proceso de actualización de firmware en varias fases.

    ```
    var onFirmwareUpdate = function(request, response) {
            
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });

      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;

      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
          
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });

        }
      });
    }
    ```
    
12. Por último, agregue el código siguiente que se conecta al concentrador IoT como un dispositivo 

    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
      
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [AZURE.NOTE] Para mantener las cosas sencillas, este tutorial implementar cualquier directiva de reintento. En el código de producción, debería implementar directivas de reintento (por ejemplo, una interrupción exponencial), según se sugiere en el artículo de MSDN [Transitorias tratamiento de errores][lnk-transient-faults].

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Desencadenar una actualización del firmware remoto en el dispositivo usando un método directo 

En esta sección, creará una aplicación de consola de Node.js que se inicia una actualización de firmware remota en un dispositivo con un método directo y usa consultas de doble del dispositivo para obtener periódicamente el estado de la actualización del firmware activa en ese dispositivo.


1. Crear una nueva carpeta vacía denominada **triggerfwupdateondevice**.  En la carpeta **triggerfwupdateondevice** , cree un archivo de package.json mediante el comando siguiente en el símbolo.  Aceptar todos los valores predeterminados:

    ```
    npm init
    ```
    
2. En la línea de comandos en la carpeta **triggerfwupdateondevice** , ejecute el comando siguiente para instalar el paquete de dispositivo SDK **iothub de azure** y **azure-iot-dispositivo-mqtt** paquete:

    ```
    npm install azure-iot-hub@dtpreview --save
    ```
    
3. Con un editor de texto, cree un nuevo archivo de **dmpatterns_getstarted_service.js** en la carpeta **triggerfwupdateondevice** .

4. Agregar el siguiente 'requerir' instrucciones al principio del archivo **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Agregue las siguientes declaraciones de variables y reemplace los valores de marcador de posición:

    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
    
6. Agregue la función siguiente para buscar y mostrar el valor de la firmwareUpdate notificado propiedad.

    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```

7. Agregue la siguiente función para invocar el método firmwareUpdate para reiniciar el dispositivo de destino:

    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
      
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
      
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
      
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```

8. Por último, agregue la siguiente función al código para iniciar el firmware actualizar secuencia e iniciar periódicamente que muestra al doble notificados propiedades:

    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
    
9. Guarde y cierre el archivo **dmpatterns_fwupdate_service.js** .

## <a name="run-the-applications"></a>Ejecutar las aplicaciones

Ya está listo para ejecutar las aplicaciones.

1. En la línea de comandos en la carpeta **manageddevice** , ejecute el comando siguiente para comenzar el método directo de reiniciar el equipo.

    ```
    node dmpatterns_fwupdate_device.js
    ```

2. En la línea de comandos en la carpeta **triggerfwupdateondevice** , ejecute el comando siguiente para activar el reinicio remoto y para consultar los dos dispositivos buscar la última reiniciar el tiempo.

    ```
    node dmpatterns_fwupdate_service.js
    ```

3. Verá reaccionar el método directo al imprimir el mensaje

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha utilizado un método directo para desencadenar una actualización del firmware remoto en un dispositivo y utiliza periódicamente informaron de los dos el proceso de actualización de propiedades para entender el progreso del firmware.  

Para obtener información sobre cómo ampliar su IoT método de solución y programación llama en varios dispositivos, vea [programación y trabajos difusión] [ lnk-tutorial-jobs] tutorial.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
