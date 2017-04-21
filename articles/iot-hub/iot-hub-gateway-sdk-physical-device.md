<properties
    pageTitle="Usar un dispositivo real con el SDK de puerta de enlace IoT | Microsoft Azure"
    description="Azure tutorial IoT SDK de puerta de enlace que usa un dispositivo de Texas instrumentos SensorTag para enviar datos a través de una puerta de enlace que se ejecuta en un módulo de Intel Edison calcular IoT concentrador"
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


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-real-device-using-linux"></a>SDK de puerta de enlace de Azure IoT (beta): enviar mensajes de la nube de dispositivo con un dispositivo real con Linux

En este tutorial de [ejemplo de energía bajo Bluetooth] [ lnk-ble-samplecode] muestra cómo usar el [SDK de puerta de enlace de Azure IoT] [ lnk-sdk] para reenviar telemetría de nube de dispositivo concentrador IoT desde un dispositivo físico y cómo enrutar comandos IoT concentrador en un dispositivo físico.

Este tutorial abarca:

* **Arquitectura**: información importante de arquitectura acerca de la muestra de energía bajo Bluetooth.

* **Crear y ejecutar**: los pasos necesarios para crear y ejecutar el ejemplo.

## <a name="architecture"></a>Arquitectura

El tutorial muestra cómo crear y ejecutar una puerta de enlace IoT en un módulo de calcular Edison Intel que ejecuta Linux. La puerta de enlace se ha creado mediante el SDK de puerta de enlace IoT. El ejemplo usa un dispositivo Texas instrumentos SensorTag Bluetooth baja energía (tabla) para recopilar datos de temperatura.

Cuando se ejecuta la puerta de enlace:

- Se conecta a un dispositivo de SensorTag con el protocolo de Bluetooth baja energía (tabla).
- Se conecta al concentrador IoT mediante el protocolo HTTP.
- Reenvía telemetría desde el dispositivo de SensorTag a IoT concentrador.
- Enruta comandos IoT concentrador para el dispositivo SensorTag.

La puerta de enlace contiene los módulos siguientes:

- *Módulo de tabla* que interactúa con un dispositivo de la tabla para recibir datos de temperatura de los comandos de dispositivo y enviar al dispositivo.
- *Nube de tabla a módulo de dispositivo* que convierte los mensajes JSON procedentes de la nube en las instrucciones de la tabla para el *módulo de la tabla*.
- *Módulo de registrador* que registra todos los mensajes de la puerta de enlace.
- *Módulo de asignación de identidad* que traduce direcciones MAC de dispositivos de tabla y las identidades de dispositivo de Azure IoT concentrador.
- *Módulo de concentrador IoT* que carga los datos de telemetría a un concentrador IoT y recibe comandos de dispositivo de un concentrador IoT.
- Un *módulo de impresora de tabla* que interpreta telemetría desde el dispositivo de la tabla y se imprime con formato de datos en la consola para habilitar la solución de problemas y depuración.

### <a name="how-data-flows-through-the-gateway"></a>Cómo fluyen los datos a través de la puerta de enlace

El diagrama de bloque siguiente muestra la canalización de flujo de datos de telemetría carga:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Los pasos que lleve a un elemento de telemetría de viaje desde un dispositivo de la tabla a IoT concentrador son:

1. El dispositivo de tabla genera una muestra de temperatura y envía a través de Bluetooth en el módulo de tabla en la puerta de enlace.
2. El módulo de tabla recibe la muestra y publica al intermediario junto con la dirección MAC del dispositivo.
3. El módulo de identidad de asignación toma este mensaje y utiliza una tabla interna para traducir la dirección MAC del dispositivo a una identidad de dispositivo concentrador IoT (Id. de dispositivo y clave de dispositivo). A continuación, se publica un mensaje nuevo que contiene los datos de ejemplo de temperatura, la dirección MAC del dispositivo, el identificador del dispositivo y la clave del dispositivo.
4. El módulo de concentrador IoT recibe este mensaje nuevo (generado por el módulo de identidad de asignación) y publica a IoT concentrador.
5. El módulo de registrador registra todos los mensajes de los agentes a un archivo de disco.

El diagrama de bloque siguiente muestra la canalización de flujo de datos de comandos de dispositivo:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. El módulo de concentrador IoT explora periódicamente el concentrador IoT para mensajes nuevos de comando.
2. Cuando el módulo de concentrador IoT recibe un nuevo mensaje de comando, publica al intermediario.
3. El módulo de identidad de asignación toma el mensaje de comando y usa una tabla interna para traducir el identificador de dispositivo concentrador IoT a un dispositivo de dirección MAC. A continuación, se publica un mensaje nuevo que incluya la dirección MAC del dispositivo de destino en el mapa de propiedades del mensaje.
4. La nube de tabla a módulo de dispositivo recoge este mensaje y la convierte en la instrucción de tabla adecuada para el módulo de la tabla. A continuación, publica un nuevo mensaje.
5. El módulo de tabla recoge este mensaje y ejecuta la instrucción de i/OS mediante la comunicación con el dispositivo de la tabla.
6. El módulo de registrador registra todos los mensajes de los agentes a un archivo de disco.

## <a name="prepare-your-hardware"></a>Preparar el hardware

En este tutorial se supone que está usando un dispositivo de [Texas instrumentos SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) conectado a un panel de Intel Edison.

### <a name="set-up-the-edison-board"></a>Configurar el panel Edison

Antes de empezar, asegúrese de que el dispositivo Edison puede conectarse a su red inalámbrica. Para configurar el dispositivo Edison, debe conectarse a un equipo host. Intel proporciona Introducción guías para los siguientes sistemas operativos:

- [Introducción a los paneles de desarrollo de Intel Edison en Windows de 64 bits][lnk-setup-win64].
- [Introducción a los paneles de desarrollo de Intel Edison en Windows de 32 bits][lnk-setup-win32].
- [Introducción a los paneles de desarrollo de Intel Edison en Mac OS X][lnk-setup-osx].
- [Introducción a los paneles de Edison Intel® en Linux][lnk-setup-linux].

Para configurar el dispositivo Edison y familiarizarse con ella, debe completar todos los pasos en estas "Introducción" artículos excepto el último paso, "Elija IDE", que es necesario para el tutorial actual. Al final del proceso de instalación Edison tiene:

- Marcadas a su Edison con el firmware más reciente.
- Establecer una conexión del host a la Edison serie.
- Ejecute el script **configure_edison** para establecer una contraseña y habilitar Wi-Fi en su Edison.

### <a name="enable-connectivity-to-the-sensortag-device-from-your-edison-board"></a>Habilitar la conexión con el dispositivo de SensorTag de su panel Edison

Antes de ejecutar el ejemplo, debe comprobar que su panel Edison puede conectar el dispositivo de SensorTag.

Primero tiene que comprobar que su Edison puede conectar el dispositivo de SensorTag.

1. Desbloquear bluetooth en el Edison y compruebe que el número de versión es **5.37**.
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. Ejecute el comando **bluetoothctl** . Ya está en el shell de bluetooth interactivo. 

3. Escriba el comando **power en** encender el controlador bluetooth. Verá el resultado similar al:
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

4. Mientras se encuentra en la consola interactiva bluetooth, escriba el comando **Buscar en** para buscar dispositivos bluetooth. Verá el resultado similar al:
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

5. Hacer que el dispositivo de SensorTag reconocible presionando el pequeño botón (el LED debe flash). La Edison debería detectar el dispositivo SensorTag:
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    En este ejemplo, puede ver que la dirección MAC del dispositivo SensorTag es **A0:E6:F8:B5:F6:00**.

6. Desactivar el análisis escribiendo el comando **análisis desactivar** .
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

7. Conectarse a su dispositivo de SensorTag usando su dirección MAC especificando **Conectar \<dirección MAC >**. Tenga en cuenta que es abreviar la salida de ejemplo siguiente:
    
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
    
    Nota: Se pueden mostrar las características de GATT del dispositivo nuevo utilizando el comando de **atributos de la lista** .

8. Puede desconectar el dispositivo usando el comando **Desconectar** ahora y salga de la consola de bluetooth utilizando el comando **Salir** :
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Ahora ya está listo para ejecutar el ejemplo de la puerta de enlace en el dispositivo Edison.

## <a name="run-the-ble-gateway-sample"></a>Ejecutar el ejemplo de la puerta de enlace

Para ejecutar el ejemplo de tabla en su Edison, debe completar tres tareas:

- Configurar dos dispositivos de ejemplo en el IoT Hub.
- Crear el SDK de puerta de enlace IoT en su dispositivo Edison.
- Configurar y ejecutar el ejemplo de tabla en el dispositivo Edison.

En el momento de escritura, el SDK de puerta de enlace IoT solo es compatible con puertas de enlace que utilizan módulos de tabla en Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurar dos dispositivos de ejemplo en el IoT Hub

- [Crear un concentrador IoT] [ lnk-create-hub] en su suscripción de Azure, tendrá el nombre de su centro de completar este tutorial. Si no tiene una cuenta, puede crear una [cuenta gratuita] de[ lnk-free-trial] de dos minutos.
- Agregar un dispositivo denominado **SensorTag_01** a su centro IoT y tome nota de su clave de identificador y el dispositivo. Puede usar el [Explorador de dispositivo o el Explorador de iothub] [ lnk-explorer-tools] herramientas para agregar este dispositivo al concentrador IoT que creó en el paso anterior y recuperar su clave. Se asignará este dispositivo en el dispositivo SensorTag al configurar la puerta de enlace.

### <a name="build-the-iot-gateway-sdk-on-your-edison-device"></a>Crear el SDK de puerta de enlace IoT en su dispositivo Edison

La versión de **git** en el Edsion no admite submodules. Para descargar el código fuente completo para el SDK de puerta de enlace IoT la Edison tiene dos opciones:

- Opción #1: Clonar el [SDK de puerta de enlace de Azure IoT] [ lnk-sdk] repositorio de su Edison y, a continuación, clonar manualmente el repositorio para cada submodule.
- Opción #2: Clonar el [SDK de puerta de enlace de Azure IoT] [ lnk-sdk] repositorio en un equipo de escritorio donde **git** admite submodules y, a continuación, copie el repositorio completo con submodules en su Edison.

Si elige la opción #2, utilice los siguientes comandos **git** clonar el SDK de puerta de enlace IoT y todos sus submodules:

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

A continuación, debe zip todo el repositorio local en un solo archivo comprimido antes de copiarla a la Edison. Puede usar una herramienta como **pscp** que se incluye con **Putty** para copiar el archivo de almacenamiento a la Edison. Por ejemplo:

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Cuando tenga una copia completa del repositorio IoT SDK de puerta de enlace en su Edison, se pueden generar mediante el comando siguiente de la carpeta que contiene el SDK:

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-edison-device"></a>Configurar y ejecutar el ejemplo de tabla en el dispositivo Edison

Para iniciar y ejecutar el ejemplo, debe configurar cada módulo que participa en la puerta de enlace. Esta configuración se proporciona en un archivo JSON y necesita configurar todos los módulos participantes cinco. Existe un archivo JSON de muestra proporcionado en el repositorio de **gateway_sample.json** que sirve como punto de partida para crear su propio archivo de configuración. Este archivo está en la carpeta de **ejemplos, ble_gateway_hl/src** en una copia local del repositorio IoT SDK de puerta de enlace.

Las secciones siguientes describen cómo modificar este archivo de configuración de la muestra de la tabla y se supone que es el repositorio de IoT SDK de puerta de enlace en el **/home/root/azure-iot-gateway-sdk /** carpeta en el dispositivo Edison. Si el repositorio está en otro sitio, debe ajustar las rutas de acceso según corresponda:

#### <a name="logger-configuration"></a>Configuración de registrador

Suponiendo que el repositorio de puerta de enlace se encuentra en la carpeta **/home/root/azure-iot-gateway-sdk /**, configurar el módulo de registrador como sigue:

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### <a name="ble-module-configuration"></a>Configuración del módulo de tabla

La configuración de ejemplo para el dispositivo de la tabla supone un dispositivo Texas instrumentos SensorTag. Cualquier dispositivo de tabla estándar que puede funcionar como una GATT periférico debería funcionar, pero tendrá actualizar los identificadores de característica GATT y los datos (para obtener instrucciones de escritura). Agregue la dirección MAC del dispositivo SensorTag: 

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>Módulo de concentrador IoT

Agregue el nombre de su centro IoT. El valor de sufijo suele ser **devices.net de azure**:

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuración del módulo de asignación de identidad

Agregue la dirección de MAC de su dispositivo SensorTag y el identificador y la clave del dispositivo **SensorTag_01** que haya agregado a su centro IoT:

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuración del módulo de la impresora

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### <a name="routing-configuration"></a>Configuración de enrutamiento

La siguiente configuración garantiza lo siguiente:
- El módulo de **registrador** recibe y registrar todos los mensajes.
- El módulo **SensorTag** envía mensajes a la **asignación** y los módulos de **La impresora** .
- El módulo de **asignación** envía mensajes al módulo **IoTHub** para enviarse a su centro IoT.
- El módulo **IoTHub** envía mensajes en el módulo de **asignación** .
- El módulo de **asignación** envía mensajes en el módulo **SensorTag** .

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

Para ejecutar el ejemplo ejecute la **ble_gateway_hl** binario pasando la ruta de acceso al archivo de configuración de JSON. Si utiliza el archivo **gateway_sample.json** , el comando ejecutar es similar a esta:

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Debe presionar el pequeño botón en el dispositivo SensorTag para que sea reconocible antes de ejecutar el ejemplo.

Cuando se ejecuta el ejemplo, puede usar el [Explorador de dispositivo o el Explorador de iothub] [ lnk-explorer-tools] herramienta para supervisar los mensajes que reenvía la puerta de enlace desde el dispositivo de SensorTag.

## <a name="send-cloud-to-device-messages"></a>Enviar mensajes de dispositivo de nube

El módulo de habilitar también admite enviar instrucciones del concentrador de Azure IoT al dispositivo. Puede usar el [Explorador de dispositivo de Azure IoT concentrador](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) o el [Explorador de concentrador IoT](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) para enviar mensajes JSON que pasa el módulo de puerta de enlace de tabla el dispositivo de la tabla. Por ejemplo, si está utilizando el dispositivo Texas instrumentos SensorTag puede enviar los siguientes mensajes JSON al dispositivo IoT concentrador.

- Restablecer todos los indicadores LED y el timbre (desactivar)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- Configurar i/OS como 'remoto'

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Activar el LED rojo

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Activar el LED verde

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- Activar el timbre

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

El comportamiento predeterminado para un dispositivo con el protocolo HTTP para conectarse a IoT concentrador es comprobar cada 25 minutos para un comando nuevo. Por lo tanto, si envía varios comandos independientes debe esperar 25 minutos para el dispositivo recibir cada comando.

> [AZURE.NOTE] La puerta de enlace también comprueba si hay nuevos comandos siempre que se inicia por lo que puede forzar para procesar un comando al detener e iniciar la puerta de enlace.

## <a name="next-steps"></a>Pasos siguientes

Si desea obtener un conocimiento más avanzado del SDK de puerta de enlace IoT y experimentar con algunos ejemplos de código, visite los siguientes tutoriales de desarrollador y recursos:

- [SDK de puerta de enlace de Azure IoT][lnk-sdk]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
