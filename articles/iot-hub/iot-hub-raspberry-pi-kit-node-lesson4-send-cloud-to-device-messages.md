<properties
 pageTitle="Ejecute la aplicación de ejemplo para recibir mensajes de dispositivo de nube | Microsoft Azure"
 description="La aplicación de ejemplo en la lección 4 se ejecuta en el Pi y supervisa los mensajes entrantes desde su centro IoT. Una nueva tarea solo golpe envía mensajes a su Pi desde su centro IoT parpadeando el LED."
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

# <a name="41-run-the-sample-application-to-receive-cloud-to-device-messages"></a>4.1 ejecute la aplicación de ejemplo para recibir mensajes de dispositivo de nube

En esta sección, implementar una aplicación de ejemplo en su frambuesas Pi 3. La aplicación de ejemplo supervisa los mensajes entrantes desde su centro IoT. También ejecutar una tarea solo golpe en el equipo para enviar mensajes a su Pi desde su centro IoT. Al recibir los mensajes, la aplicación de ejemplo parpadea el LED. Si se cumple algún problema, buscar soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="411-what-you-will-do"></a>4.1.1 lo que debe hacer

- Conectar la aplicación de ejemplo para su centro IoT.
- Implementar y ejecutar la aplicación de ejemplo.
- Enviar mensajes de su centro IoT a su Pi parpadeando el LED.

## <a name="412-what-you-will-learn"></a>4.1.2 lo que aprenderá

- Cómo supervisar los mensajes entrantes desde su centro IoT.
- Cómo enviar mensajes de dispositivo de nube de su centro IoT a su Pi. 

## <a name="413-what-do-you-need"></a>4.1.3 lo que necesita

- 3 de Pi frambuesas que se ha configurado para su uso. Para obtener información sobre cómo configurar su Pi, consulte [lección 1: Introducción a su dispositivo frambuesas Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md)
- Un concentrador IoT que se crea en la suscripción de Azure. Para aprender a crear su centro de IoT Azure, consulte [lección 2: crear su centro de Azure IoT](iot-hub-raspberry-pi-kit-node-get-started.md)

## <a name="414-connect-the-sample-application-to-your-iot-hub"></a>4.1.4 conectar la aplicación de ejemplo para su centro IoT

1. Asegúrese de que se encuentra en la carpeta repo `iot-hub-node-raspberrypi-getting-started`. Abrir la aplicación de ejemplo en el código de Visual Studio, ejecute los comandos siguientes:

    ```bash
    cd Lesson4
    code .
    ```

    Aviso de la `app.js` de archivo de la `app` subcarpeta. La `app.js` es el archivo de origen de la clave que contiene el código para supervisar los mensajes entrantes IoT concentrador. La `blinkLED` función parpadea el LED.

    ![Estructura de repo](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)

2. Inicializar el archivo de configuración con los siguientes comandos:

    ```bash
    npm install
    gulp init
    ```

    Si ha completado lección 3 en este equipo, se heredan todas las configuraciones para que vaya al paso 4.1.5. Si ha realizado la lección 3 en un equipo diferente, debe reemplazar los marcadores de posición en la `config-raspberrypi.json` archivo. La `config-raspberrypi.json` archivo se encuentra en la subcarpeta de la carpeta Inicio.

    ![Config](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

- Reemplazar **[nombre de host del dispositivo o la dirección IP]** con la dirección IP o el nombre de host que obtiene con el comando de su Pi`devdisco list --eth`
- Reemplazar **[cadena de conexión de dispositivo IoT]** con la cadena de conexión del dispositivo que recibe, ejecute el comando `az iot hub show-connection-string --name {my hub name} --resource-group {resource group name}`.
- Reemplazar **[cadena de conexión de hub IoT]** con la cadena de conexión de hub IoT que obtiene con el comando `az iot device show-connection-string --hub {my hub name} --device-id {device id} --resource-group {resource group name}`.

## <a name="415-deploy-and-run-the-sample-application"></a>4.1.5 implementar y ejecute la aplicación de ejemplo

Implementar y ejecutar la aplicación de ejemplo en el Pi, ejecute los comandos siguientes:
  
```
gulp
```

El comando solo golpe primero ejecuta la tarea de herramientas de la instalación. A continuación, implementa la aplicación de ejemplo para el Pi. Por último, se ejecuta la aplicación en el Pi y una tarea diferente en el equipo host para enviar mensajes de intermitente 20 a su Pi desde su centro IoT.

Una vez que se ejecuta la aplicación de ejemplo, se inicia escuchar los mensajes de su centro IoT. Mientras tanto, la tarea solo golpe envía varios mensajes "intermitente" desde su centro IoT a su Pi. Para cada mensaje intermitente recibido, la aplicación de ejemplo llama a la función blinkLED para parpadea el LED.

Verá el LED parpadean cada dos segundos, como la tarea solo golpe envía mensajes de 20 desde su centro IoT a su Pi. El último es un mensaje "Detener" que se indica a la aplicación para detener la ejecución.

![Solo golpe](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="416-summary"></a>4.1.6 resumen de

Correctamente ha enviado mensajes de su centro IoT a su Pi parpadeando el LED. Sección siguiente es opcional que muestra cómo cambiar el activado y desactivado el comportamiento de LED.

## <a name="next-steps"></a>Pasos siguientes

[Sección opcional: cambiar el activado y desactivado el comportamiento de LED](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)