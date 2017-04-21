<properties
   pageTitle="Conectar un dispositivo con C en mbed | Microsoft Azure"
   description="Describe cómo conectar un dispositivo con la solución de supervisión de Azure IoT Suite preconfigurado remota mediante una aplicación escrita en C que se ejecuta en un dispositivo de mbed."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Conectar el dispositivo a la solución preconfigurada supervisión remota (mbed)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Crear y ejecutar la solución de ejemplo C

Las siguientes instrucciones describen los pasos para conectar un [Habilitado mbed Freescale FRDM-K64F] [ lnk-mbed-home] dispositivo para la solución de supervisión.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Conecte el dispositivo de mbed a su equipo de red y escritorio

1. Conecte el dispositivo de mbed a su red mediante un cable Ethernet. Este paso es necesario porque la aplicación de ejemplo requiere acceso a internet.

2. Consulte [Introducción a mbed] [ lnk-mbed-getstarted] para conectar el dispositivo de mbed a su PC de escritorio.

3. Si ejecuta Windows en su PC de escritorio, consulte [Configuración de PC] [ lnk-mbed-pcconnect] para configurar el acceso de puerto serie al dispositivo mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Crear un proyecto de mbed e importar el código de ejemplo

1. En el explorador web, vaya al [sitio para desarrolladores](https://developer.mbed.org/)de mbed.org. Si no dispone, verá una opción para crear una cuenta (es gratuita). En caso contrario, inicie sesión con sus credenciales de cuenta. A continuación, haga clic en el **compilador** en la esquina superior derecha de la página. Esta acción permite a la interfaz de *área de trabajo* .

2. Asegúrese de que está usando la plataforma de hardware aparece en la esquina superior derecha de la ventana o haga clic en el icono en la esquina derecha para seleccionar la plataforma de hardware.

3. En el menú principal, haga clic en **Importar** . A continuación, haga clic en la **haga clic aquí** para importar desde el vínculo de dirección URL situada junto al logotipo del globo terráqueo mbed.

    ![][6]

4. En la ventana emergente, escriba el vínculo de la https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ código muestra, a continuación, haga clic en **Importar**.

    ![][7]

5. Puede ver en la ventana de compilador mbed que importar este proyecto, también importa varias bibliotecas. Algunos se proporcionan y mantienen el equipo de Azure IoT ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), mientras que otros están disponibles en el catálogo de bibliotecas de mbed de bibliotecas de terceros.

    ![][8]

6. Abra el archivo remote_monitoring\remote_monitoring.c y busque el código siguiente en el archivo:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Reemplazar [Id. de dispositivo] y [clave de dispositivo], con los datos del dispositivo para habilitar el programa de ejemplo para conectarse a su centro IoT. Use el nombre de host de concentrador IoT reemplazar [nombre IoTHub] y [sufijo IoTHub, es decir, de azure devices.net] marcadores de posición. Por ejemplo, si el nombre de host de concentrador IoT es **contoso.azure devices.net**, **contoso** es el **hubName** y todo el contenido después de la **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### <a name="walk-through-the-code"></a>Recorra el código

Si está interesado en cómo funciona el programa, esta sección describen algunas partes principales del código de ejemplo. Si desea ejecutar el código, continúe para [generar y ejecuta el programa](#buildandrun).

#### <a name="defining-the-model"></a>Definir el modelo

Este ejemplo utiliza el [serializador] [ lnk-serializer] biblioteca para definir un modelo que especifica los mensajes que puede enviar a IoT concentrador y recibir IoT concentrador del dispositivo. En este ejemplo, el espacio de nombres de **Contoso** define un modelo de **termostato** que especifica los datos de telemetría **temperatura**, **ExternalTemperature**y **humedad** junto con los metadatos como el identificador del dispositivo, propiedades de dispositivos y los comandos que el dispositivo responde:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

Relacionados con la definición del modelo son las definiciones de los comandos **SetTemperature** y **SetHumidity** que el dispositivo responde:

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### <a name="connecting-the-model-to-the-library"></a>El modelo a conectarse a la biblioteca

Las funciones **sendMessage** y **IoTHubMessage** son código reutilizable para el envío de telemetría desde el dispositivo y conectar mensajes IoT concentrador de los controladores de comando.

#### <a name="the-remotemonitoringrun-function"></a>La función remote_monitoring_run

La función del programa **principal** invoca la función **remote_monitoring_run** cuando se inicie la aplicación ejecutar el comportamiento del dispositivo como un cliente de dispositivo IoT concentrador. Esta función **remote_monitoring_run** principalmente consta de pares anidados de funciones:

- **plataforma\_inicialización** y **plataforma\_deinit** realizar operaciones de inicialización y cierre específicas de la plataforma.
- **serializador\_inicialización** y **serializador\_deinit** inicializar y anule la iniciar la biblioteca de serializador.
- **IoTHubClient\_crear** y **IoTHubClient\_Destroy** crear un identificador de cliente, **iotHubClientHandle**, con el dispositivo las credenciales para conectarse a su centro IoT.

En la sección principal de la función **remote_monitoring_run** , el programa realiza las siguientes operaciones con el controlador de **iotHubClientHandle** :

- Crea una instancia del modelo de termostato Contoso y configura las devoluciones de llamada del mensaje para los comandos de dos.
- Envía información sobre el dispositivo, incluidos los comandos que admite el concentrador IoT mediante la biblioteca de serializador. Cuando el concentrador recibe este mensaje, cambia el estado del dispositivo en el panel de **pendiente** **en ejecución**.
- Inicia un bucle **mientras** que envía temperatura, temperatura externo y los valores de humedad a concentrador IoT cada segundo.

Para referencia, le presentamos un mensaje de **DeviceInfo** de ejemplo enviado a concentrador IoT inicial:

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Para referencia, le presentamos un mensaje de **telemetría** de ejemplo enviado a IoT concentrador:

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Para referencia, este es un ejemplo que se recibió el **comando** IoT concentrador de:

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### <a name="build-and-run-the-program"></a>Crear y ejecutar el programa

1. Haga clic en **compilar** para generar el programa. Forma segura, puede omitir las advertencias, pero si la compilación genera errores, corregirlos antes de continuar.

2. Si la compilación es correcta, el sitio Web de compilador de mbed genera un archivo bin con el nombre del proyecto y descargas en el equipo local. Copie el archivo bin al dispositivo. Guardar el archivo bin en el dispositivo hace que el dispositivo reiniciar y ejecute el programa contenido en el archivo bin. Puede reiniciar el programa manualmente en cualquier momento presionando el botón Restablecer en el dispositivo mbed.

3. Conecte el dispositivo mediante una aplicación de cliente SSH, como PuTTY. Puede determinar el puerto serie que usa el dispositivo, consulte Administrador de dispositivos de Windows.

    ![][11]

4. Deformar, haga clic en el tipo de conexión **serie** . El dispositivo normalmente se conecta a 9600 baudios, así que debe especificar 9600 en el cuadro **velocidad** . A continuación, haga clic en **Abrir**.

5. Inicia la ejecución del programa. Puede que tenga que restablecer los paneles (presione CTRL + salto o presione el botón de restablecimiento de la placa) si el programa no inicia automáticamente al conectarse.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer
