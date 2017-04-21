<properties
 pageTitle="Habilitar dispositivos administrados detrás de una puerta de enlace IoT | Microsoft Azure"
 description="Tema de guía mediante una puerta de enlace IoT creados con el SDK de puerta de enlace IoT junto con dispositivos administrados por IoT concentrador."
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# <a name="enable-managed-devices-behind-an-iot-gateway"></a>Habilitar dispositivos administrados detrás de una puerta de enlace IoT

## <a name="basic-device-isolation"></a>Aislamiento de dispositivo básica

Organizaciones suelen usar IoT puertas de enlace para mejorar la seguridad de sus soluciones IoT global. Algunos dispositivos necesitan enviar datos a la nube, pero no son capaces de protegerse de amenazas de internet. Puede proteger estos dispositivos de subprocesos externos por necesidad de comunicarse con el exterior a través de una puerta de enlace.

La puerta de enlace se encuentra en el borde entre un entorno seguro y abrir internet. Dispositivos póngase en contacto con la puerta de enlace y la puerta de enlace pasa los mensajes a lo largo de al destino nube correcto. La puerta de enlace está consolidado contra subprocesos externos, bloquea las solicitudes no autorizadas, permite el tráfico entrante autorizado y reenvía en enlazado al dispositivo correcto.

![][1]

También puede colocar los dispositivos que pueden protegerse detrás de una puerta de enlace para agregar un nivel de seguridad. En este escenario solo debe mantener la puerta de enlace OS revisada contra la vulnerabilidad más reciente, en lugar de actualizar el sistema operativo en todos los dispositivos.

## <a name="isolation-plus-intelligence"></a>Aislamiento más inteligencia

Un enrutador consolidado es una puerta de enlace suficiente para aislar simplemente dispositivos. Sin embargo, IoT soluciones a menudo requieren que una puerta de enlace proporciona inteligencia más que simplemente aislar dispositivos. Por ejemplo, desea administrar los dispositivos de la nube. Usted es poder usar [LWM2M](https://github.com/OpenMobileAlliance/OMA_LwM2M_for_Developers/wiki), un protocolo de administración de dispositivos estándar para el elemento de administración de la nube de la solución. Sin embargo, los dispositivos envían telemetría utilizando TCP/IP no protocolo habilitado. Además, los dispositivos generar una gran cantidad de datos y desea cargar un subconjunto de la telemetría filtrado. Puede crear una solución que incorpora una puerta de enlace IoT capaz de trabajar con dos secuencias distintas de datos. La puerta de enlace debe:

-   Entender la **telemetría**, filtrar y luego cárguelo en la nube a través de la puerta de enlace. La puerta de enlace ya no es un enrutador sencillo que simplemente reenvía datos entre el dispositivo y la nube.

-   Simplemente intercambiar los **datos de administración de dispositivos de LWM2M** entre los dispositivos y la nube. La puerta de enlace no es necesario comprender los datos entra en él y solo debe para asegurarse de que los datos se pasan hacia adelante y hacia atrás entre los dispositivos y la nube.

La figura siguiente muestra este escenario:

![][2]

## <a name="the-solution-azure-iot-device-management-and-the-iot-gateway-sdk"></a>La solución: administración de dispositivos de Azure IoT y el SDK de puerta de enlace IoT 

El público obtener una vista previa de la versión de [administración de dispositivos de Azure IoT] [ lnk-device-management] y versión beta de la [Puerta de enlace SDK de Azure IoT] habilitar este escenario. La puerta de enlace controla cada flujo de datos como sigue:

-   **Telemetría**: puede utilizar el SDK de puerta de enlace IoT para generar una canalización que comprende, filtros y envía los datos de telemetría en la nube. El SDK de puerta de enlace de IoT proporciona código que implementa partes de esta canalización en nombre del programador. Puede encontrar más información sobre la arquitectura de SDK en el [SDK de puerta de enlace IoT - Introducción] [ lnk-gateway-get-started] tutorial.

-   **Administración de dispositivos**: administración de dispositivos de Azure ofrece un cliente de LWM2M que se ejecuta en el dispositivo, así como una interfaz de nube para emitir comandos de administración en el dispositivo.
    
    No requieren ninguna lógica especial en la puerta de enlace ya no es necesario procesar los datos de LWM2M intercambiados entre el dispositivo y su centro IoT. Puede habilitar la conexión a internet de uso compartido, una característica de muchos sistemas operativos modernos, en la puerta de enlace para habilitar el intercambio de datos de LWM2M. Puede elegir un sistema operativo adecuado para este escenario porque el SDK de puerta de enlace IoT es compatible con una amplia variedad de sistemas operativos. Estas son instrucciones para habilitar la conexión a internet de uso compartido en [Windows 10] y [Ubuntu], dos de los muchos sistemas operativos compatibles.

La siguiente ilustración muestra la arquitectura de alto nivel para habilitar este escenario mediante la [administración de dispositivos de Azure IoT] [ lnk-device-management] y el [SDK de Azure IoT puerta de enlace].

![][3]

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo usar el SDK de puerta de enlace IoT, consulte los siguientes tutoriales:

- [Puerta de enlace IoT SDK: empezar a usar Linux][lnk-gateway-get-started]
- [Puerta de enlace IoT SDK: enviar mensajes de nube de dispositivo con un dispositivo simulado con Linux][lnk-gateway-simulated]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway-simulated]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[SDK de puerta de enlace de Azure IoT]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/en-us/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-devguide]: iot-hub-devguide.md