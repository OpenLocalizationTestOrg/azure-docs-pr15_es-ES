<properties
 pageTitle="Configurar el dispositivo | Microsoft Azure"
 description="Configurar su frambuesas Pi 3 para el primer uso e instalar el sistema operativo Raspbian, un sistema operativo gratuito que está optimizado para el hardware frambuesas Pi."
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

# <a name="11-configure-your-device"></a>1.1 configurar su dispositivo

## <a name="111-what-you-will-do"></a>1.1.1 lo que debe hacer

Configurar el Pi para el primer uso e instalar el sistema operativo Raspbian, un sistema operativo gratuito que está optimizado para el hardware frambuesas Pi. Si se cumple algún problema, buscar soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="112-what-you-will-learn"></a>1.1.2 lo que aprenderá

En esta sección, aprenderá:

- Cómo instalar Raspbian en el Pi
- Cómo encender el Pi usando un cable USB
- Cómo conectar el Pi a la red mediante un cable Ethernet o Wi-Fi
- Cómo agregar un LED a la breadboard y conectarse a su Pi

## <a name="113-what-you-need"></a>1.1.3 lo que necesita

Para completar esta sección, necesita los siguientes elementos de su frambuesas Pi 3 Starter Kit:

- El panel de frambuesas Pi 3
- La tarjeta de MicroSD de 16GB
- Los 5 v 2A de alimentación con el cable USB de seis pies micro
- La breadboard
- Hilos de conector
- Una resistencia 560 Ohm
- Un LED difusos de 10mm
- El cable Ethernet

![Cosas en su Starter Kit](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

También necesitará:

- Una conexión con cable o inalámbrica para su Pi para conectarse a
- Una SD USB adaptador mini SD tarjeta o para grabar la imagen de sistema operativo en la tarjeta de MicroSD.
- Un equipo con Windows, Mac o Linux. El equipo se usa para instalar Raspbian en la tarjeta de MicroSD.
- Una conexión a Internet para descargar el software y herramientas necesarias

## <a name="114-install-raspbian-on-the-microsd-card"></a>1.1.4 instalar Raspbian en la tarjeta de MicroSD

Preparar la tarjeta MicroSD para escribir la imagen Raspbian.

1. Descargar Raspbian.
  1. [Descargar](https://www.raspberrypi.org/downloads/raspbian/) el archivo zip para Raspbian Jessie con píxeles.
  2. Extraer la imagen Raspbian en una carpeta en el equipo.
2. Instale Raspbian a la tarjeta de MicroSD.
  1. [Descargar](https://www.etcher.io) e instalar la utilidad de grabadora de tarjeta SD de creación.
  2. Ejecutar la creación y seleccione la imagen Raspbian extraído en el paso 1.
  3. Seleccione la unidad de tarjeta MicroSD.
    Nota: Creación que ya ha seleccionado la unidad correcta.
  4. Haga clic en Flash para instalar Raspbian a la tarjeta de MicroSD.
  5. Quitar la tarjeta de MicroSD de su equipo cuando haya finalizado.
    Nota: Es seguro quitar la tarjeta MicroSD directamente porque creación automática retira o desmonta la tarjeta MicroSD al finalizar.
  6. Inserte la tarjeta MicroSD en el Pi.

![Insertar la tarjeta SD](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="115-power-on-your-pi"></a>1.1.5 encienda el Pi

Encienda el Pi con el cable USB micro y la fuente de alimentación.

![Encendido](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [AZURE.NOTE] Es importante usar la fuente de alimentación en el kit de al menos 2A para asegurarse de que están introduciendo su frambuesas con suficiente capacidad para que funcione correctamente.

## <a name="116-connect-your-raspberry-pi-3-to-the-network"></a>1.1.6 conectar su frambuesas Pi 3 a la red

Puede conectar su Pi a una red cableada o a una red inalámbrica. Asegúrese de que su Pi está conectado a la misma red que el equipo. Por ejemplo, puede conectar su Pi al mismo conmutador que su equipo está conectado a.

### <a name="1161-connect-to-a-wired-network"></a>1.1.6.1 conectarse a una red cableada

Utilice el cable Ethernet para conectar su Pi con su red cableada. Los dos LED en el Pi activar si se establece la conexión.

![Conectar utilizando cable Ethernet](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="1162-connect-to-a-wireless-network"></a>1.1.6.2 conectarse a una red inalámbrica

Siga las [instrucciones](https://www.raspberrypi.org/learning/software-guide/wifi/) de la base de Pi frambuesas conectar su Pi con su red inalámbrica. Estas instrucciones requieren primero conectar un monitor y un teclado para el Pi.

## <a name="117-connect-the-led-to-your-pi"></a>1.1.7 conectar el LED a su Pi

Para completar esta tarea, utilice la [breadboard](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard), los cables de conector, el LED y la resistencia. Conectarlos a los puertos de [entrada y salida de uso general](https://www.raspberrypi.org/documentation/usage/gpio/) (GPIO) de su Pi. 

![Breadboard, LED y resistencia eléctrica](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. Conecte el segmento más corto de LED a **GPIO GND (6 Pin)**.
2. Conecte el segmento más largo de LED a un segmento de la resistencia.
3. Conecte el otro segmento de la resistencia a **GPIO 4 (Pin 7)**.

Observe que la polaridad LED es importante. Esta configuración de polaridad se conoce como bajo activo.

![PIN](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

¡Felicitación! Se ha configurado correctamente el Pi.

## <a name="118-summary"></a>1.1.8 resumen de

En esta sección, ha aprendido cómo configurar su Pi instalando Raspbian, su Pi a conectarse a una red y un LED a conectarse a su Pi. Tenga en cuenta que el LED aún no encenderá. En la siguiente sección, instale el software de preparación para la ejecución de una aplicación de ejemplo en el Pi y herramientas necesarias.

![Hardware está listo](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>Pasos siguientes

[1.2 obtener las herramientas](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
