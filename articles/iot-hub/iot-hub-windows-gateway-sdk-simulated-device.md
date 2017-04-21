<properties
    pageTitle="Simular un dispositivo con el SDK de puerta de enlace IoT | Microsoft Azure"
    description="Tutorial de Azure SDK de puerta de enlace IoT usa Windows para ilustrar telemetría envío desde un dispositivo simulado con el SDK de Azure IoT puerta de enlace."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-simulated-device-using-windows"></a>SDK de puerta de enlace de Azure IoT (beta): enviar mensajes de la nube de dispositivo con un dispositivo simulado con Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Crear y ejecutar el ejemplo

Antes de empezar, debe:

- [Configurar el entorno de desarrollo] [ lnk-setupdevbox] para trabajar con el SDK de Windows.
- [Crear un concentrador IoT] [ lnk-create-hub] en su suscripción de Azure, tendrá el nombre de su centro de completar este tutorial. Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.
- Agregar dos dispositivos a su centro IoT y tome nota de sus identificadores y las teclas del dispositivo. Puede usar el [Explorador de dispositivo o el Explorador de iothub] [ lnk-explorer-tools] herramienta para agregar los dispositivos al concentrador IoT que creó en el paso anterior y recuperar sus claves.

Para generar el ejemplo:

1. Abra un símbolo del sistema de **símbolo del programador para VS2015** .
2. Vaya a la carpeta raíz de su copia local del repositorio de **puerta de enlace sdk azure-iot** .
3. Ejecutar la **herramientas\\build.cmd** secuencia de comandos. Esta secuencia de comandos crea un archivo de solución de Visual Studio, la solución, crea y ejecuta las pruebas. Puede encontrar la solución de Visual Studio en la carpeta de **compilación** de su copia local del repositorio de **puerta de enlace sdk azure-iot** .

Para ejecutar el ejemplo:

En un editor de texto, abra el archivo **ejemplos\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** en su copia local del repositorio de **puerta de enlace sdk azure-iot** . Este archivo configura los módulos en la puerta de enlace de ejemplo:

- El módulo **IoTHub** se conecta a su centro IoT. Debe configurarlo para enviar datos a su centro IoT. En concreto, establezca el valor de **IoTHubName** en el nombre de su centro IoT y establezca el valor de **IoTHubSuffix** a **devices.net de azure**. Establezca el valor de **transporte** en uno de: "HTTP", "AMQP" o "MQTT". Tenga en cuenta que en este momento solo "HTTP" compartirán una conexión de TCP para todos los mensajes del dispositivo. Si establece el valor de "AMQP" o "MQTT", la puerta de enlace mantendrá una conexión TCP independiente a IoT concentrador para cada dispositivo.
- El módulo de **asignación** asigna las direcciones MAC de los dispositivos simuladas a sus identificadores de dispositivo IoT concentrador. Asegúrese de que los valores de **deviceId** coinciden con los identificadores de los dos dispositivos que haya agregado a su centro IoT y que los valores de **deviceKey** contienen las claves de los dos dispositivos.
- Los módulos **BLE1** y **BLE2** son los dispositivos simulados. Observe cómo ajustan a sus direcciones MAC en el módulo de **asignación** .
- El módulo de **registrador** registra la actividad de la puerta de enlace a un archivo.
- Los valores de **ruta de acceso del módulo** que se muestra a continuación, se supone que clonar el repositorio IoT SDK de puerta de enlace en la raíz de la unidad **C:** . Si se ha descargado a otra ubicación, debe ajustar los valores de la **ruta de acceso del módulo** en consecuencia.
- La matriz de **vínculos** en la parte inferior del archivo JSON conecta a los módulos **BLE1** y **BLE2** para el módulo de la **asignación** y el módulo de **asignación** en el módulo **IoTHub** . También garantiza que todos los mensajes se registran por el módulo de **registrador** .

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\iothub\\Debug\\iothub_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\identitymap\\Debug\\identity_map_hl.dll",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}
```

Guarde los cambios realizados en el archivo de configuración.

Para ejecutar el ejemplo:

1. En el símbolo del sistema, vaya a la carpeta raíz de su copia local del repositorio de **puerta de enlace sdk azure-iot** .
2. Ejecute el siguiente comando:
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. Puede usar el [Explorador de dispositivo o el Explorador de iothub] [ lnk-explorer-tools] herramienta para supervisar los mensajes que recibe IoT concentrador de la puerta de enlace.


## <a name="next-steps"></a>Pasos siguientes

Si desea obtener un conocimiento más avanzado del SDK de puerta de enlace IoT y experimentar con algunos ejemplos de código, visite los siguientes tutoriales de desarrollador y recursos:

- [Enviar mensajes de dispositivo a la nube desde un dispositivo real con el SDK de puerta de enlace IoT][lnk-physical-device]
- [SDK de puerta de enlace de Azure IoT][lnk-gateway-sdk]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 