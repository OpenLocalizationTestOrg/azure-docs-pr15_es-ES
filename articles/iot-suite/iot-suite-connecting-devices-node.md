<properties
   pageTitle="Conectar un dispositivo con Node.js | Microsoft Azure"
   description="Describe cómo conectar un dispositivo con la solución de supervisión de Azure IoT Suite preconfigurado remota mediante una aplicación escrita en Node.js."
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


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Conectar el dispositivo a la solución preconfigurada supervisión remota (Node.js)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Crear y ejecutar la solución de ejemplo de node.js

1. Para duplicar el repositorio de *Microsoft Azure IoT SDK* GitHub e instalar el *dispositivo de Microsoft Azure IoT SDK para Node.js* en su entorno de escritorio, siga el [Preparar el entorno de desarrollo] [ lnk-github-prepare] instrucciones.

2. Desde su copia local de los [SDK de azure iot] [ lnk-github-repo] repositorio, copiar las dos siguientes archivos desde la carpeta de dispositivo de nodo/ejemplos a una carpeta en el dispositivo:

  - Packages.JSON
  - remote_monitoring.js

3. Abra el archivo remote_monitoring.js y busque la variable siguiente:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Reemplace **[cadena de conexión del dispositivo de concentrador IoT]** con la cadena de conexión del dispositivo. Puede encontrar los valores para su centro IoT hostname, el identificador de dispositivo y clave de dispositivo en el panel de solución de supervisión remota. Una cadena de conexión de dispositivo tiene el siguiente formato:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Si su nombre de host de concentrador IoT es **contoso** y el identificador de dispositivo es **mydevice**, la cadena de conexión tendrá este aspecto:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Guarde el archivo. En el símbolo del sistema en la carpeta que contiene estos archivos para instalar los paquetes necesarios y, a continuación, ejecute la aplicación de ejemplo, ejecute los comandos siguientes:

    ```
    npm install --save
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md