<properties
    pageTitle="Usar propiedades de doble | Microsoft Azure"
    description="Este tutorial muestra cómo usar las propiedades de doble"
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>

# <a name="tutorial-use-desired-properties-to-configure-devices-preview"></a>Tutorial: Utilizar las propiedades que desee para configurar dispositivos (vista preliminar)

[AZURE.INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Al final de este tutorial, tendrá dos aplicaciones de consola de Node.js:

* **SimulateDeviceConfiguration.js**, una aplicación de dispositivo simulado que espera una actualización de la configuración deseada e informa del estado de un proceso de actualización de configuración simulada.
* **SetDesiredConfigurationAndQuery**, una aplicación de consola de .NET pensada para ejecutarse desde el back-end, que establece la configuración deseada en un dispositivo y consultas el proceso de actualización de la configuración.

> [AZURE.NOTE] El artículo [IoT concentrador SDK] [ lnk-hub-sdks] proporciona información sobre los distintos SDK que puede usar para crear aplicaciones dispositivo y back-end.

Para completar este tutorial, se necesita lo siguiente:

+ Microsoft Visual Studio 2015.

+ Node.js versión 0.10.x o posterior.

+ Una cuenta de Azure active. (Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.)

Si ha seguido la [Introducción a twins dispositivo] [ lnk-twin-tutorial] tutorial, ya tiene un concentrador de administración habilitada del dispositivo y la identidad de un dispositivo denominado **myDeviceId**; y vaya al [crear la aplicación de dispositivo simulado] [ lnk-how-to-configure-createapp] sección.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>Crear la aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Node.js que se conecta a su centro como **myDeviceId**, espera una actualización de la configuración que desee e informa de las actualizaciones en el proceso de actualización de configuración simulada.

1. Crear una nueva carpeta vacía denominada **simulatedeviceconfiguration**. En la carpeta **simulatedeviceconfiguration** , cree un nuevo archivo package.json mediante el comando siguiente en el símbolo. Aceptar todos los valores predeterminados:

    ```
    npm init
    ```

2. En la línea de comandos en la carpeta **simulatedeviceconfiguration** , ejecute el comando siguiente para instalar el paquete de **azure-iot-dispositivo-mqtt** y **dispositivo de azure iot**:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo de **SimulateDeviceConfiguration.js** en la carpeta **simulatedeviceconfiguration** .

4. Agregue el código siguiente al archivo **SimulateDeviceConfiguration.js** y sustituya el marcador de posición de **{cadena de conexión de dispositivo}** con la cadena de conexión que ha copiado al crear la identidad del dispositivo de **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });

    El objeto de **cliente** expone todos los métodos necesarios para interactuar con twins dispositivo desde el dispositivo. El código anterior, después, inicializa el objeto de **cliente** , recupera al doble para **myDeviceId**y asocia un controlador para la actualización de propiedades que desee. El controlador comprueba que exista es una solicitud de cambio de configuración real comparando la configIds, invoca un método que se inicia el cambio de configuración.

    Observe que, para simplificar, el código anterior utiliza un valor predeterminado para la configuración inicial. Una aplicación real probablemente ¿cargar dicha configuración desde un almacenamiento local.
    
    > [AZURE.IMPORTANT] Eventos de cambio de propiedad deseada siempre se emiten una vez en conexión con el dispositivo, asegúrese de comprobar que hay un cambio real en las propiedades que desee antes de realizar ninguna acción.

5. Agregue los métodos siguientes antes de la `client.open()` invocación de funciones:

        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";

            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }

        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
            
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;

            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };

    El método **initConfigChange** actualiza notificadas propiedades en el objeto de doble local con la solicitud de actualización de la configuración y configura el estado como **pendiente**, actualiza al doble de dispositivo en el servicio. Después de actualizar correctamente el doble, simula un proceso de larga ejecución que finaliza la ejecución de **completeConfigChange**. Este método actualiza propiedades notificado de doble local configurar el estado de **éxito** y quitar el objeto **pendingConfig** . A continuación, actualiza al doble en el servicio.

    Tenga en cuenta que, para ahorrar ancho de banda, notificadas propiedades se actualizan especificando solo las propiedades para modificarse (con nombre de **revisión** en el código anterior), en lugar de reemplazar todo el documento.

    > [AZURE.NOTE] Este tutorial no simular cualquier comportamiento de las actualizaciones de configuración simultáneas. Algunos procesos de actualización de la configuración posible que pueda adaptarse a los cambios de configuración de destino mientras se está ejecutando la actualización, otras personas que deba cola y, a continuación, otros usuarios pueden rechazar con una condición de error. Asegúrese de tener en cuenta el comportamiento deseado para el proceso de configuración específica y agregar la lógica adecuada antes de iniciar el cambio de configuración.

6. Ejecute la aplicación de dispositivo:

        node SimulateDeviceConfiguration.js

    Verá el mensaje `retrieved device twin`. Mantener la ejecución de la aplicación.

## <a name="create-the-service-app"></a>Crear la aplicación de servicio

En esta sección, creará una aplicación de consola de .NET que actualiza las *propiedades que desee* en el doble asociada **myDeviceId** con un nuevo objeto de configuración de telemetría. A continuación, consulta la twins dispositivo almacenados en el hub y muestra la diferencia entre las configuraciones que desee y notificadas del dispositivo.

1. En Visual Studio, agregue un proyecto de Visual C# escritorio clásico de Windows a la solución actual con la plantilla de proyecto de **Aplicación de consola** . Nombre del proyecto **SetDesiredConfigurationAndQuery**.

    ![Nuevo proyecto de Visual C# escritorio clásico de Windows][img-createapp]

2. En el Explorador de soluciones, haga clic en el proyecto **SetDesiredConfigurationAndQuery** y, a continuación, haga clic en **Administrar paquetes de Nuget**.

3. En la ventana **Administrador de paquetes de Nuget** , asegúrese de que la **versión preliminar de incluir** está activada, busque **microsoft.azure.devices**, seleccione **instalar** para instalar el la versión *preliminar* de la **Microsoft.Azure.Devices** empaquetar y acepte los términos de uso. Este procedimiento descargas, instalaciones y agrega una referencia en el [SDK de servicio de Microsoft Azure IoT] [ lnk-nuget-service-sdk] Nuget paquete y sus dependencias.

    ![Ventana del Administrador de paquetes de NuGet][img-servicenuget]

4. Agregue las siguientes `using` instrucciones en la parte superior del archivo **Program.cs** :

        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;

5. Agregue los campos siguientes a la clase **Program** . Reemplace el valor de marcador de posición con la cadena de conexión para el concentrador IoT que creó en la sección anterior.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Agregue el método siguiente a la clase **Program** :

        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
            
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired state");

            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }

    El objeto de **registro** expone todos los métodos necesarios para interactuar con twins dispositivo del servicio. El código anterior, después inicializa el objeto de **registro** , recupera al doble para **myDeviceId**y actualiza las propiedades que desee con un nuevo objeto de configuración de telemetría.
    Después de eso, cada 10 segundos, consulta a la twins almacenados en el hub e imprimen las configuraciones de telemetría deseada y notificado. Consulte el [lenguaje de consulta de concentrador IoT] [ lnk-query] para obtener información sobre cómo generar informes completos en todos sus dispositivos.

    > [AZURE.IMPORTANT] Esta aplicación consulta IoT concentrador cada 10 segundos con fines ilustrativos. Usar las consultas para generar informes de cara al usuario en muchos dispositivos y no para detectar cambios. Si su solución requiere notificaciones en tiempo real de los eventos de dispositivo use [los mensajes de nube de dispositivo][lnk-d2c].

7. Por último, agregue las siguientes líneas al método **Main** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();

8. Con **SimulateDeviceConfiguration.js** en ejecución, ejecutar la aplicación .NET de Visual Studio mediante **F5** y debe ver la configuración notificada cambiar de **éxito** a **pendiente** a **Success** nuevo con la nueva activa enviar frecuencia de cinco minutos en lugar de 24 horas.

    > [AZURE.IMPORTANT] Hay un retardo de hasta un minuto entre la operación de informe de dispositivo y el resultado de la consulta. Esto es para habilitar la infraestructura de consulta para que funcione con escala muy alta. Para recuperar vistas coherentes de una sola doble usan el método de **getDeviceTwin** en la clase de **registro** .

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, establecer una configuración deseada como *deseado propiedades* desde el back-end y escribir una aplicación de dispositivo para detectar ese cambio y simular un proceso de actualización de varios pasos informar de su estado como *Propiedades notificados* para los dos.

Use los siguientes recursos para obtener información sobre cómo:

- enviar telemetría desde dispositivos con la [Introducción a IoT concentrador] [ lnk-iothub-getstarted] tutorial,
- programar o realizar operaciones en grandes conjuntos de dispositivos, vea los [trabajos de uso para programar y difundir las operaciones de dispositivo] [ lnk-schedule-jobs] tutorial.
- controlar los dispositivos de forma interactiva (por ejemplo, si un ventilador desde una aplicación controlada por el usuario), con los [métodos de uso directos] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
