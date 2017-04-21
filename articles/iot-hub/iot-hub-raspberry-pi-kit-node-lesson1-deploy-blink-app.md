<properties
 pageTitle="Crear e implementar la aplicación intermitente | Microsoft Azure"
 description="Clonar la aplicación de Node.js de ejemplo de Github y gulp para implementar esta aplicación en el panel de frambuesas Pi 3. Esta aplicación de ejemplo parpadea el LED conectado al panel cada dos segundos."
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

# <a name="13-create-and-deploy-the-blink-application"></a>1.3 crear e implementar la aplicación intermitente

## <a name="131-what-you-will-do"></a>1.3.1 lo que debe hacer

Clonar la aplicación de Node.js de ejemplo de Github y use la herramienta solo golpe para implementar la aplicación de ejemplo para el Pi frambuesas 3. La aplicación de ejemplo parpadea el LED conectado al panel cada dos segundos. Si se cumple algún problema, buscar soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="132-what-you-will-learn"></a>1.3.2 lo que aprenderá

- Cómo usar la `device-discover-cli` herramienta para recuperar información red sobre el Pi.
- Cómo implementar y ejecutar la aplicación de ejemplo en el Pi.
- Aprenda a implementar y depurar aplicaciones que se ejecutan de forma remota en el Pi.

## <a name="133-what-you-need"></a>1.3.3 lo que necesita

Debe haber completado correctamente las siguientes secciones en la lección 1:

- [Configurar el dispositivo](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)
- [Obtener las herramientas](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

## <a name="134-obtain-the-ip-address-and-host-name-of-your-pi"></a>1.3.4 obtener el nombre de host y la dirección IP de su PI.

Abra un símbolo del sistema de Windows o una ventana de terminal en Mac OS o Ubuntu y, a continuación, ejecute el siguiente comando:

```bash
devdisco list --eth
```

Debe ver un resultado similar al siguiente:

![detección de dispositivo](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Tome nota de la `IP address` y `hostname` de su Pi. Necesitará esta información más adelante en esta sección.

> [AZURE.NOTE] Asegúrese de que su Pi está conectado a la misma red que el equipo. Por ejemplo, si su equipo está conectado a una red inalámbrica mientras el Pi está conectado a una red cableada, quizás no pueda ver la dirección IP en el resultado de devdisco.

## <a name="135-clone-the-sample-application"></a>1.3.5 clonar la aplicación de ejemplo

Para abrir el código de ejemplo, siga estos pasos:

1. Clonar el repositorio de ejemplo de Github, ejecute el siguiente comando:

    ```bash
    git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started.git
    ```

2. Abrir la aplicación de ejemplo en el código de Visual Studio, ejecute los comandos siguientes:

    ```bash
    cd iot-hub-node-raspberrypi-getting-started
    cd Lesson1
    code .
    ```

![Estructura de repo](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-mac.png)

La `app.js` de archivo de la `app` subcarpeta es el archivo de origen de la clave que contiene el código para controlar el LED.

### <a name="136-install-application-dependencies"></a>1.3.6 instalar dependencias de aplicaciones

Instalar las bibliotecas y otros módulos que necesita para la aplicación de ejemplo, ejecute el siguiente comando:

```bash
npm install
```

## <a name="137-configure-the-device-connection"></a>1.3.7 configurar la conexión del dispositivo

Para configurar la conexión del dispositivo, siga estos pasos:

1. Generar el archivo de configuración de dispositivo, ejecute el siguiente comando:

    ```bash
    gulp init
    ```

    El archivo de configuración `config-raspberrypi.json` contiene las credenciales de usuario que utiliza para iniciar el Pi. Para evitar la pérdida de credenciales de usuario, se genera el archivo de configuración de la subcarpeta `.iot-hub-getting-started` de la carpeta principal en el equipo.

2. Abrir el archivo de configuración de dispositivo en el código de Visual Studio, ejecute el siguiente comando:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json

    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

3. Reemplazar el marcador de posición `[device hostname or IP address]` con la dirección IP o el nombre de host que recibe en sección 1.3.4.

    ![Config.JSON](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

¡Felicidades! Ha creado correctamente la primera aplicación de ejemplo para el Pi.

## <a name="138-deploy-and-run-the-sample-application"></a>1.3.8 implementar y ejecutar la aplicación de ejemplo

### <a name="1381-install-nodejs-and-npm-on-your-pi"></a>1.3.8.1 instalar Node.js y NPM en el Pi

Instalar Node.js y NPM en el Pi, ejecute el siguiente comando:

```bash
gulp install-tools
```

Puede llevar diez minutos para completar la primera vez que ejecute esta tarea.

### <a name="1382-deploy-and-run-the-sample-app"></a>1.3.8.2 implementar y ejecute la aplicación de ejemplo

Implementar y ejecutar la aplicación de ejemplo en ejecutando el siguiente comando:

```bash
gulp deploy && gulp run
```

### <a name="1383-verify-the-app-works"></a>1.3.8.3 comprobar la aplicación funciona

Ahora debe ver el LED en el Pi parpadeando cada dos segundos.  Si no ve el LED parpadean, consulte la [Guía de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md) para soluciones a problemas comunes.
![LED parpadean](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

> [AZURE.NOTE] Usar `Ctrl + C` para terminar la aplicación.

## <a name="139-summary"></a>1.3.9 resumen de

Ha instalado las herramientas necesarias para trabajar con su Pi e implementado una aplicación de ejemplo para el Pi parpadeando el LED. Ahora puede mover a la lección siguiente para crear, implementar y ejecutar otra aplicación de ejemplo que se conecta la Pi a Azure IoT concentrador para enviar y recibir mensajes.

## <a name="next-steps"></a>Pasos siguientes

Ya está listo para iniciar la lección 2 que comienza con [obtener las herramientas de Azure](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
