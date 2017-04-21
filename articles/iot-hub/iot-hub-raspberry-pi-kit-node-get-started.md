<properties
 pageTitle="Introducción a Pi frambuesas 3 | Microsoft Azure"
 description="Introducción a frambuesas Pi 3, crear su centro de IoT Azure y conectar su Pi al concentrador IoT"
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

# <a name="get-started-with-raspberry-pi-3"></a>Introducción a Pi frambuesas 3

En este tutorial, empiece por aprender los conceptos básicos del trabajo con frambuesas Pi 3 que está ejecutando Raspbian. A continuación, aprenderá a conectarse fácilmente los dispositivos a la nube con [Azure IoT concentrador](iot-hub-what-is-iot-hub.md). Para Windows 10 IoT muestras, visite [windowsondevices.com](http://www.windowsondevices.com/).

## <a name="lesson-1-configure-your-device"></a>Lección 1: Configurar el dispositivo

![Diagrama de E2E lección 1](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

En esta lección, configurar el dispositivo de frambuesas Pi 3 con un sistema operativo, configurar el entorno de desarrollo e implementar una aplicación en el Pi.

### <a name="configure-your-device"></a>Configurar el dispositivo

Configurar su frambuesas Pi 3 para el primer uso e instalar el sistema operativo Raspbian, un sistema operativo gratuito que está optimizado para el hardware frambuesas Pi.

*Tiempo estimado para completar: 30 minutos* 

[Vaya a configurar la su dispositivo](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)

### <a name="get-the-tools"></a>Obtener las herramientas
Descargar el software para crear e implementar su primera aplicación para frambuesas Pi 3 y herramientas.

*Tiempo estimado para completar: 20 minutos* 

[Vaya a 'Obtener las herramientas'](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

### <a name="create-and-deploy-the-blink-application"></a>Crear e implementar la aplicación intermitente

Clonar la aplicación de Node.js de ejemplo de Github y gulp para implementar esta aplicación en el panel de frambuesas Pi 3. Esta aplicación de ejemplo parpadea el LED conectado al panel cada dos segundos.

*Tiempo estimado para completar: 5 minutos* 

[Vaya a ' crear e implementar la aplicación intermitente '](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)

## <a name="lesson-2-create-your-iot-hub"></a>Lección 2: Crear su centro IoT

![Diagrama de E2E lección 2](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

En esta lección, crear una cuenta gratuita de Azure, proporcionando su centro de Azure IoT y crear su primer dispositivo en Azure IoT concentrador.

Completar la lección 1 antes de comenzar esta lección.

### <a name="get-the-azure-tools"></a>Obtener las herramientas de Azure

Instalar Azure de línea de comandos interfaz Azure.

*Tiempo estimado para completar: 10 minutos* 

[Vaya a 'Obtener Azure herramientas'](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)

### <a name="create-your-iot-hub-and-register-your-raspberry-pi-3"></a>Crear su concentrador IoT y registrar su frambuesas Pi 3

Crear el grupo de recursos, aprovisionar el primer concentrador de Azure IoT y agregue el primer dispositivo al concentrador IoT Azure mediante CLI de Azure. 

*Tiempo estimado para completar: 10 minutos* 

[Vaya a 'Crear su centro IoT y registrar su frambuesas Pi 3'](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)


## <a name="lesson-3-send-device-to-cloud-messages"></a>Lección 3: Enviar mensajes de la nube de dispositivo

![Lección 3 E2E diagrama](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

En esta lección, enviar mensajes desde el Pi a su centro IoT. También crea una aplicación de la función Azure que recoge los mensajes entrantes desde su centro IoT y escribe en el almacenamiento de tablas de Azure.

Completar lecciones 1 y 2 antes de comenzar esta lección.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Crear una aplicación de la función Azure y la cuenta de almacenamiento de Azure

Usar una plantilla de administrador de recursos de Azure para crear una aplicación de la función Azure y una cuenta de almacenamiento de Azure.

*Tiempo estimado para completar: 10 minutos* 

[Vaya a 'Crear una aplicación de la función Azure y la cuenta de almacenamiento de Azure'](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)

### <a name="run-sample-application-to-send-device-to-cloud-messages"></a>Ejecutar la aplicación de ejemplo para enviar mensajes de la nube de dispositivo

Implementar y ejecutar una aplicación de ejemplo en el dispositivo de frambuesas Pi 3 que envía mensajes a un concentrador IoT.

*Tiempo estimado para completar: 10 minutos* 

[Vaya a 'Ejecutar la aplicación de ejemplo para enviar mensajes de la nube de dispositivo'](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

### <a name="read-messages-persisted-in-azure-storage"></a>Leer los mensajes se conservan en el almacenamiento de Azure
Supervisar los mensajes de la nube de dispositivo como se escriben en el almacenamiento de Azure.

*Tiempo estimado para completar: 5 minutos* 

[Vaya a 'leer los mensajes se conservan en el almacenamiento de Azure'](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)


## <a name="lesson-4-send-cloud-to-device-messages"></a>Lección 4: Enviar mensajes de dispositivo de nube

![Lección 4 E2E diagrama](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

Esta lección demostraciones cómo enviar mensajes de su centro de Azure IoT a su frambuesas Pi 3. Los mensajes de controlan el activado y desactivado el comportamiento de LED que está conectado a su Pi. Una aplicación de ejemplo está preparada para que lograr esta tarea.

Completar lecciones 1, 2 y 3 antes de comenzar esta lección.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Ejecute la aplicación de ejemplo para recibir mensajes de dispositivo de nube

La aplicación de ejemplo en la lección 4 se ejecuta en el Pi y supervisa los mensajes entrantes desde su centro IoT. Una nueva tarea solo golpe envía mensajes a su Pi desde su centro IoT parpadeando el LED.

*Tiempo estimado para completar: 10 minutos* 

[Vaya a 'Ejecutar la aplicación de ejemplo para recibir mensajes de dispositivo de nube'](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Sección opcional: cambiar el activado y desactivado el comportamiento de LED

Personalizar los mensajes para cambiar el LED activar y desactivar el comportamiento.

*Tiempo estimado para completar: 10 minutos* 

[Vaya a ' sección opcional: cambiar el activado y desactivado el comportamiento de LED'](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)


## <a name="troubleshooting"></a>Solución de problemas

Si cumple los problemas durante las lecciones, puede buscar soluciones en esta página.

[Vaya a 'Solución de problemas'](iot-hub-raspberry-pi-kit-node-troubleshooting.md)
