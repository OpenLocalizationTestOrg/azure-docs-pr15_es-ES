<properties
 pageTitle="Leer los mensajes se conservan en el almacenamiento de Azure | Microsoft Azure"
 description="Supervisar los mensajes de la nube de dispositivo como se escriben en el almacenamiento de tablas de Azure."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="33-read-messages-persisted-in-azure-storage"></a>3.3 mensajes leídos se conservan en el almacenamiento de Azure

## <a name="331-what-will-you-do"></a>3.3.1 ¿qué hará

Supervisar los mensajes de dispositivo a la nube que se envían desde su frambuesas Pi 3 a su centro IoT como los mensajes se escriben en el almacenamiento de tablas de Azure. Si se cumple algún problema, buscar soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="332-what-will-you-learn"></a>3.3.2 lo que aprenderá

- Cómo utilizar la tarea de mensaje leído solo golpe para leer los mensajes se conserva en el almacenamiento de tablas de Azure.

## <a name="333-what-do-you-need"></a>3.3.3 lo que necesita

- Debe haber completado correctamente la sección anterior, [ejecute la aplicación de ejemplo de Azure intermitente en su frambuesas Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md).

## <a name="334-read-new-messages-from-your-storage-account"></a>3.3.4 leer los mensajes nuevos de la cuenta de almacenamiento

En la sección anterior, ejecutó una aplicación de ejemplo en el Pi. Los mensajes de la aplicación enviado de ejemplo para su centro de Azure IoT. Los mensajes enviados a su centro IoT se almacenan en el almacenamiento de tablas de Azure mediante la aplicación de la función de Azure. Necesita la cadena de conexión de almacenamiento de Azure para leer mensajes desde el almacenamiento de tablas de Azure.

Para leer mensajes almacenados en el almacenamiento de tablas de Azure, siga estos pasos:

1. Obtener la cadena de conexión, ejecute los comandos siguientes:

    ```bash
    az storage account list -g iot-sample --query [].name
    az storage account show-connection-string -g iot-sample -n {storage name}
    ```

    El primer comando recupera la `storage name` que se utiliza en el segundo comando para obtener la cadena de conexión. `iot-sample`es el valor predeterminado de `{resource group name}` si no cambia el valor en la lección 2.

2. Abra el archivo de configuración `config-raspberrypi.json` archivo en código de Visual Studio, ejecute el siguiente comando:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json

    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

3. Reemplazar `[Azure Storage connection string]` con la cadena de conexión que obtuvo en el paso 1.
4. Guardar la `config-raspberrypi.json` archivo.
5. Enviar mensajes de nuevo y leer desde el almacenamiento de tablas de Azure, ejecute el siguiente comando:

    ```bash
    gulp run --read-storage
    ```

    La lógica de lectura de almacenamiento de tablas de Azure está en la `azure-table.js` archivo.

    ![solo golpe ejecutar--almacenamiento de lectura](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message.png)

## <a name="335-summary"></a>3.3.5 resumen de

Correctamente ha conectado su Pi a su centro IoT en la nube y utiliza la aplicación de ejemplo intermitente para enviar mensajes de dispositivo a la nube. También se usa la aplicación de la función Azure para almacenar los mensajes entrantes de concentrador IoT para el almacenamiento de tablas de Azure. Puede mover a la siguiente lección sobre cómo enviar mensajes de dispositivo de nube de su centro IoT a su Pi.

## <a name="next-steps"></a>Pasos siguientes

[Lección 4: Enviar mensajes de dispositivo de nube](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)
