<properties
    pageTitle="Plataformas de sistemas operativos y compatibilidad de hardware | Microsoft Azure"
    description="Resume la compatibilidad SDK IoT dispositivo con plataformas de sistema operativo y el hardware del dispositivo."
    services="iot-hub"
    documentationCenter=""
    authors="hegate"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/04/2016"
     ms.author="hegate"/>

# <a name="os-platforms-and-hardware-compatibility-with-device-sdks"></a>Plataformas de sistemas operativos y hardware compatibilidad con dispositivos SDK

Este documento describe la compatibilidad SDK con distintas plataformas de sistema operativo, así como las configuraciones de dispositivo específico incluidas en la [Microsoft Azure programa certificado para IoT](#microsoft-azure-certified-for-iot). Si ya tiene un dispositivo, consulte la lista de dispositivos incluidos en el programa para buscar información específica del dispositivo en compatibilidad. Si no está seguro de qué dispositivo usar, dedique un vistazo a la sección de compatibilidad de [plataforma de sistema operativo y bibliotecas](#os-platforms) .


## <a name="os-platforms"></a>Plataformas de sistemas operativos

Las bibliotecas de Azure IoT se han probado en las siguientes plataformas de sistema operativo:


|Plataformas de sistemas operativos Linux/Unix  |   Versión|
|:---------------|:------------:|
|Linux Debian| 7.5|
|Fedora Linux|20|
|Raspbian Linux| 3.18 |
|Ubuntu Linux| 14.04 |
|Yocto Linux|2.1 |

|Plataformas de sistema operativo Android  |   Versión|
|:---------------|:------------:|
|Android| 4.2 >|

|Plataformas de sistema operativo Windows  |   Versión|
|:---------------|:------------:|
|Escritorio de Windows| 10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|Otras plataformas  |   Versión|
|:---------------|:------------:|
|Arduino | IDE 1.6.8 |
|mbed | 2.0 |
|RTOS DE TI | 2.x |



## <a name="c-libraries"></a>Bibliotecas de C

El [dispositivo de Microsoft Azure IoT SDK para C](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) se ha probado en las configuraciones siguientes:

|Plataforma de sistema operativo| Versión|Protocolos|
|:---------|:----------:|:----------:|
|Arduino| IDE 1.6.8 | HTTPS |
|Linux Debian| 7.5 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|mbed OS| 2.0 | HTTPS, AMQP, MQTT |
|RTOS DE TI| 2.x | HTTPS |
|Ubuntu Linux| 14.04 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Escritorio de Windows| 10 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Yocto Linux|2.1  | HTTPS, AMQP|



## <a name="nodejs-libraries"></a>Bibliotecas de Node.js

El [dispositivo de Microsoft Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) se ha probado en las configuraciones siguientes:

|Tiempo de ejecución| Versión|Protocolos|
|:---------|:----------:|:----:|
|Node.js| 4.1.0 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |

El servicio de Microsoft Azure IoT SDK por Node.js se ha probado en las configuraciones siguientes:

|Tiempo de ejecución| Versión|
|:---------|:----------:|
|Node.js| 4.1.0 |


## <a name="java-libraries"></a>Bibliotecas de Java

El [dispositivo de Microsoft Azure IoT SDK de Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) se ha probado en las configuraciones siguientes:

|Tiempo de ejecución| Versión|Protocolos|
|:---------|:----------:|----|
|SE Java (Windows)| 1,8 | HTTPS, AMQP, MQTT |
|SE Java (Linux)| 1,8 | HTTPS, AMQP, MQTT|
|Android| API 15 | HTTPS, MQTT |

El servicio de Microsoft Azure IoT SDK para Java se ha probado en las configuraciones siguientes:

|Tiempo de ejecución| Versión|Protocolos|
|:---------|:----------:|:-----|
|SE Java| 1,8 | HTTPS, AMQP, MQTT |


## <a name="csharp"></a>CSharp

El [dispositivo de Microsoft Azure IoT SDK para .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) se ha probado en las configuraciones siguientes:

|Plataforma de sistema operativo| Versión|Protocolos|
|:---------|:----------:|:----------:|
|Escritorio de Windows| 10 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Windows IoT Core|10 | HTTPS |
|PCL (Xamarin, Mono, UWP, WP8.1, Win8.1)|  | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |

El servicio de Microsoft Azure IoT SDK para CSharp se ha probado en las configuraciones siguientes:

|Tiempo de ejecución| Versión|
|:---------|:----------:|
|.NET| 4.5 |
|UWP|  |

Código administrado agente requiere Microsoft .NET Framework 4.5


## <a name="python-libraries"></a>Bibliotecas de Python

El [dispositivo de Microsoft Azure IoT SDK para Python](https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md) se ha probado en las configuraciones siguientes:

|Tiempo de ejecución| Versión|Protocolos|
|:---------|:----------:|:----------:|
|Python | 2.7.x, 3.4. x, 3.5 | HTTPS, AMQP, MQTT |


## <a name="microsoft-azure-certified-for-iot"></a>Microsoft Azure certificados para IoT

**Microsoft Azure certificados para IoT** es el programa de partners que conecta ecosistema IoT más amplio con Microsoft Azure para que los programadores y arquitectos comprenden los escenarios de compatibilidad. Más concretamente, proporciona una lista de confianza de combinaciones de sistema operativo o dispositivo que le ayudarán a empezar rápidamente con un proyecto IoT: si está en una prueba de concepto o la fase piloto. Dispositivo certificada y combinaciones de sistema operativo, el proyecto IoT pueda empezar rápidamente, con menos trabajo y personalización necesaria para asegurarse de que los dispositivos son compatibles con [El conjunto de aplicaciones de Azure IoT] [ lnk-iot-suite] y Azure IoT concentrador.

## <a name="certified-for-iot-devices"></a>Certificados para dispositivos IoT

Dispositivos de **certificado para IoT** han probado la compatibilidad con el SDK de IoT Azure y están listos para usarse en la aplicación IoT. Más concretamente, identificaremos compatibilidad basada en idioma de plataforma y el código del sistema operativo.

¿No encuentra el dispositivo que está buscando en la lista? No es un problema, nuestro SDK de Azure IoT está abierto originado y diseñados para ser portátiles. Visite nuestro cliente biblioteca GitHub [repositorio](https://github.com/Azure/azure-iot-sdks) para empezar y Díganos si surge algún problema para conectar el dispositivo en la sección de [problemas](https://github.com/Azure/azure-iot-sdks/issues) .

#### <a name="devices-list"></a>Lista de dispositivos

Cada dispositivo ha sido certificado para trabajar con nuestro SDK en el sistema operativo y el idioma elegido por el fabricante del dispositivo. Por ejemplo, BeagleBone Black funciona en Debian con nuestro lenguaje C, Javascript y Java. Esto significa que los desarrolladores pueden crear aplicaciones en cualquiera de los idiomas y combinaciones de sistema operativo en los dispositivos específicos. Para una visualización más cómoda de esta tabla, puede comprobar el blog post http://bit.ly/azureiotdevices, que explica cómo visualizar mediante Power BI.

|Dispositivo| Sistema operativo probado |Idioma|Instrucciones|
|:---------|:----------|:----------|:----------|
|[AAEON ACP-1104](http://www.aaeon.com/en/p/infotainment-multi-touch-panel-solutions-1104/) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-acp-1104-csharp.md)|
|[AAEON BOXEADOR-6614](http://www.aaeon.com/en/p/fanless-embedded-computers-boxer-6614/) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-boxer-6614-csharp.md)|
|[AAEON GÉNICA-BT05](http://www.aaeon.com/en/p/3-and-half-inches-subcompact-boards-gene-bt05/) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-gene-bt05-csharp.md)|
|[AAEON GÉNICA-BT05](http://www.aaeon.com/en/p/3-and-half-inches-subcompact-boards-gene-bt05) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-gene-bt05-c.md)|
|[AAEON PICO-BT01](http://www.aaeon.com/en/p/pico-itx-boards-pico-bt01) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-PICO-BT01-c.md)|
|[AAEON PICO-BT01](http://www.aaeon.com/en/p/pico-itx-boards-pico-bt01) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-%20pico-bt01-csharp.md)|
|[AAEON HACIA ARRIBA](http://www.up-board.org/) |ubilinux | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubilinux-up-board.md)|
|[AAEON HACIA ARRIBA](http://www.up-board.org/) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-aaeon-up-csharp.md)|
|[Acme sistemas Arietta G25](http://www.acmesystems.it/arietta) |Debian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-arietta-g25-c.md)|
|[Adafruit desvanecimiento M0 Wifi](https://www.adafruit.com/product/3010) |Arduino IDE(RTOS) | Arduino, C|[Introducción](https://azure.microsoft.com/en-us/documentation/samples/iot-hub-c-m0wifi-getstartedkit/)|
|[Huzzah Adafruit Desvanecer](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/overview) |Arduino IDE(RTOS) | Arduino, C|[Introducción](https://azure.microsoft.com/en-us/documentation/samples/iot-hub-c-huzzah-getstartedkit/)|
|[ADLINK IMB-M43](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1600&seq=&id=&sid=&category=Industrial-Motherboards-&-SBC_ ATX&utm_source=#) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-imb-m43-csharp.md)|
|[ADLINK MXE-200](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1505&seq=&id=&sid=&category=Fanless-Embedded-Computer_Integrated-Embedded-Computer&utm_source=) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mxe-200-csharp.md)|
|[ADLINK MXE-200](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1505&seq=&id=&sid=&category=Fanless-Embedded-Computer_Integrated-Embedded-Computer&utm_source=) |Windows10 Iot principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-mxe-200-csharp.md)|
|[ADLINK MXE-202i](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1589) |Viento río | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-mxe-202i-nodejs.md)|
|[ADLINK MXE-5400](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1318) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mxe-5400-csharp.md)|
|[ADLINK MXC-6300](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1242&seq=&id=&sid=&category=Fanless-Embedded-Computer_Expandable-Embedded-Computer&utm_source=) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mxc-6300-csharp.md)|
|[ADLINK NuPRO-E43]( http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1634&seq=&id=&sid=&category=Industrial-Motherboards-&-SBC_PICMG-1.x-CPU-boards&utm_source=) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-nupro-e43-csharp.md)|
|[Advantech Co., 203 AIMB](http://www.advantech.com/products/68ccaea2-9ff5-4f85-97f2-3d11244b0a08/aimb-203/mod_a46984f8-caa9-433a-a720-e1815a8c16a3) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-aimb-203-csharp.md)|
|[Advantech Co., 215 AIMB](http://www.advantech.com/products/f910a32b-088c-4f15-8a94-f130f22bc1b5/aimb-215/mod_2fd07ef0-7445-4131-a3b0-379548b78e27) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-aimb-215-csharp.md)|
|[Advantech Co., 501 AIMB](http://www.advantech.com/products/1-2jkkgc/aimb-501/mod_3ed96dd6-792f-431d-b3e7-c524380b15d6) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-aimb-501-csharp.md)|
|[Advantech Co., 503 AIMB](http://www.advantech.com/products/1-2jkkgc/aimb-503/mod_5db22cce-25c7-488d-a5a4-f76a4061161b) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-aimb-503-csharp.md)|
|[Advantech Co., ARK - 1123H](http://www.advantech.com/products/92d96fda-cdd3-409d-aae5-2e516c0f1b01/ark-1123h/mod_590fcfdc-278c-41b1-a8c7-d29152cd96d7) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ark-1123h-csharp.md)|
|[Advantech Co., ARK - 2121L](http://www.advantech.com/products/ark-2000_series_embedded_box_pcs/ark-2121l/mod_dd092808-0832-44bc-b38a-945eb7e016bd) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ark-2121l-csharp.md)|
|[Advantech Co., DPX S430](http://www.advantech.com/products/standalone_(s_series)/dpx-s430/mod_2c298749-cd5f-4c67-928a-0325836dd991) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-dpx-s430-csharp.md)|
|[Advantech Co., DPX S435](http://www.advantech.com/products/standalone_(s_series)/dpx-s435/mod_fe4f6841-5900-44fe-8912-d86de7d15c65) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-dpx-s435-csharp.md)|
|[Advantech Co., DS-060](http://www.advantech.com/products/85d19842-5892-4e78-bf14-0d4d6ce36e04/ds-060/mod_c739322c-c2c7-43f1-9f4b-8483fea4e3d7) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ds-060-csharp.md)|
|[Advantech Co., 370 DS](http://www.advantech.com/products/5408f56b-e978-41fa-9509-0b9fcd27e902/ds-370/mod_ada71950-a0b5-4ee2-91b2-9aed2d1c6af8) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ds-370-csharp.md)|
|[Advantech Co., 2261 millones](http://www.advantech.com/products/mi%7B%7B--o_ultra_single_board_computers/mio-2261/mod_a06b85aa-8734-42c1-8ff7-a3854bf1eadf) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mio-2261-csharp.md)|
|[Advantech Co., 2263 millones](http://www.advantech.com/products/mi%7B%7B--o_ultra_single_board_computers/mio-2263/mod_3c9191fb-edf1-49a5-a58c-b4734867ecda) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mio-2263-csharp.md)|
|[Advantech Co., 5250 millones](http://www.advantech.com/products/bc6786b7-b69b-4fdd-9b63-79180dbd5dcb/mio-5250/mod_eea5e107-b1e6-4989-a8e0-03a042f5dbef) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mio-5250-csharp.md)|
|[Advantech Co., 5251 millones](http://www.advantech.com/products/bc6786b7-b69b-4fdd-9b63-79180dbd5dcb/mio-5251/mod_ff8534cc-e858-40ba-9bc2-386f19bfee4a) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-mio-5251-csharp.md)|
|[Advantech Co., PCM 3356](http://www.advantech.com/products/1-2jkltu/pcm-3356/mod_0706f4d5-2e44-473a-a7b7-53bd1a7bd1a0) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-pcm-3356-csharp.md)|
|[Advantech Co., PCM 9365](http://www.advantech.com/products/1-2jkd1i/pcm-9365/mod_5051a369-13cb-4cda-a44b-cc34e37d6196) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-pcm-9365-csharp.md)|
|[Advantech Co., PCM 9376](http://www.advantech.com/products/1-2jkd1i/pcm-9376/mod_9870f332-c45f-4ac1-acf0-3407038c0d4a) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-pcm-9376-csharp.md)|
|[Advantech Co., SOM 5893](http://www.advantech.com/products/1-e0e7i7/som-5893/mod_7f19c5d2-2fee-4b7f-a551-43c826c20c25) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-som-5893-csharp.md)|
|[Advantech Co., SOM 6867](http://www.advantech.com/products/1-34h3r7/som-6867/mod_4cbd463d-32cd-4440-921a-07b2421ec744) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-som-6867-csharp.md)|
|[Advantech Co., ARK - 2121L](http://www.advantech.com/products/ark-2000_series_embedded_box_pcs/ark-2121l/mod_dd092808-0832-44bc-b38a-945eb7e016bd) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-ark-2121l-csharp.md)|
|[Advantech Co., ARK - 1123C](http://www.advantech.com/products/92d96fda-cdd3-409d-aae5-2e516c0f1b01/ark-1123c/mod_0b91165c-aa8c-485d-8d25-fde6f88f4873) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-ark-1123c-csharp.md)|
|[Advantech Co., UNO - 1372G](http://www.advantech.com/products/gf-bvl2/uno-1372g/mod_8e63b3c9-b606-4725-a1af-94fccb98bb1a) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-uno-1372g-csharp.md)|
|[Advantech Co., UNO - 1372G](http://www.advantech.tw/products/gf-bvl2/uno-1372g/mod_8e63b3c9-b606-4725-a1af-94fccb98bb1a) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-advantech-uno-1372g-c.md)|
|[Advantech Co., UNO - 2272G](http://www.advantech.tw/products/1-2mlj9a/uno-2272g/mod_2f889619-f9ba-4735-a432-7ac7a08669c4) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-advantech-uno-2272g-jxae-c.md)|
|[Advantech Co., UTX 3115](http://www.advantech.com/products/bda911fe-28bc-4171-aed3-67f76f6a12c8/utx-3115/mod_fa00d5cd-7d2b-430b-8983-c232bfb9f315) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-utx-3115-csharp.md)|
|[Advantech Co., 674 de viaje](http://www.advantech.com/products/1-2jsj5t/trek-674/mod_88a737dd-819b-4c8e-8f2e-2bb75b04619b) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-trek-674-csharp.md)|
|[Alleantia IoT SCADA SERVER](http://www.alleantia.com/en/iot-scada-server-2) |Ubuntu | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-IoT-scada-server-java.md)|
|[Tecnología Allwinner plátanos Pi BPI-M64](http://www.banana-pi.com/eacp_view.asp?id=95) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-banana-pi-bpi-m64-csharp.md)|
|[Tecnología Allwinner Pine64](https://www.pine64.com/product#features) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-pine64-csharp.md)|
|[FATBOX amplificado G3]( http://www.amplified.com.au/#!4gltegateway/c192n) |OpenWRT Linux | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-openwrt-fatbox-g3-c.md)|
|[AOPEN Inc. eTILE15-FB](http://www.aopen.com/global/shop/products/etile-15m) |Windows 10 IoT Enterprise LTSB | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-etile15-fb-csharp.md)|
|[AOPEN Inc. ETILE19-FB](http://www.aopen.com/global/shop/products/etile-19m) |Windows 10 IoT Enterprise LTSB | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-etile19-fb-csharp.md)|
|[AOPEN Inc. ETILE22-FW](http://www.aopen.com/global/shop/products/etile-22m) |Windows 10 IoT Enterprise LTSB | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-ltsb-etile22-fw-csharp.md)|
|[Árbol IEC-3300](http://www.arbor-technology.com/tw/Product/Pro/Model/IEC-3300_6) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-Arbor-IEC-3300-csharp.md)|
|[Arduino MKR1000](https://www.arduino.cc/en/Main/ArduinoMKR1000) |Arduino IDE | Arduino, C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/arduinoide-arduino-wifi101-c.md)|
|[Arduino Yun](https://www.arduino.cc/en/Main/ArduinoBoardYun) |Arduino IDE | Arduino, C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/openwrt-arduino-yun-c.md)|
|[Arduino cero](https://www.arduino.cc/en/Guide/ArduinoZero) |Arduino IDE | Arduino, C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/arduinoide-arduino-wifi101-c.md)|
|[Flecha DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-dragonboard-410c-csharp.md)|
|[Atmark Armadillo-IoT G2 de puerta de enlace](http://armadillo.atmark-techno.com/armadillo-iot/specs) |Debian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-armadillo-IoT_GW_G2-c.md)|
|[Atmark Armadillo-IoT G2 de puerta de enlace](http://armadillo.atmark-techno.com/armadillo-iot-g2/specs) |Debian | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-armadillo-iot-gateway-g2-linux-java.md)|
|[Atmark Armadillo-IoT G3 de puerta de enlace](http://armadillo.atmark-techno.com/armadillo-iot-g3) |Debian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-armadillo-iot-gw-g3-c.md)|
|[Atmark Armadillo-IoT G3 de puerta de enlace](http://armadillo.atmark-techno.com/armadillo-iot-g3) |Debian | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-armadillo-iot-gw-g3-java.md)|
|[Atmark Armadillo-IoT G3L de puerta de enlace](http://armadillo.atmark-techno.com/armadillo-iot-g3l) |Debian | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-armadillo-iot-gw-g3l-java.md)|
|[Atmark Armadillo-IoT G3L de puerta de enlace](http://armadillo.atmark-techno.com/armadillo-iot-g3l) |Debian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-armadillo-iot-gateway-g3l-c.md)|
|[Autobase ATB-1A15C](http://www.autobase.biz/Korean/autobasetouch/ATB-1A15C.htm) |Windows 10 IoT Enterprise LTSB | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-enterprise-ltsb-atb-1a15c-c.md)|
|[Un valor EPC-BTCR](http://www.avalue.com.tw/Product/Embedded-Computing/Fanless-Industrial-System/Embedded-Computing-System/EPC-BTCR_2323) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/Windows10-epc-btcr-csharp.md)|
|[Un valor RIPAC - 10P 1]( http://www.avalue.com.tw/product/Intelligent-Systems/POS-Terminal/POS-hardware-systems/RiPac-10P1_2399) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-ripac-10p1-csharp.md)|
|[Un valor RITAB-10T1](http://www.avalue.com.tw/product/Intelligent-Systems/Mobile-Solution/Mobile-Solution/RiTab-10T1_2384) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-ritab-10t1-csharp.md)|
|[Un valor OFT-07W01](http://www.avalue.com.tw/Product/Panel-PC/Open-Frame/Open-Frame-Tablet/OFT-07W01_2394) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-oft-07w01-csharp.md)|
|[Un valor OFT-10W01](http://www.avalue.com.tw/Product/Panel-PC/Open-Frame/Open-Frame-Tablet/OFT-10W01_2324) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-oft-10w01-csharp.md)|
|[Un valor OFT-15W01](http://www.avalue.com.tw/Product/Panel-PC/Open-Frame/Open-Frame-Tablet/OFT-15W01_2325) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-oft-15w01-csharp.md)|
|[Un valor OFT-21W01](http://www.avalue.com.tw/Product/Panel-PC/Open-Frame/Open-Frame-Tablet/OFT-21W01_2326) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-oft-21w01-csharp.md)|
|[Axiomtek eBOX560-300](http://www.axiomtek.com/Default.aspx?MenuId=Products&FunctionId=ProductView&ItemId=17308&upcat=144) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ebox560-300-csharp.md)|
|[Axiomtek ICO300](http://www.axiomtek.com/Default.aspx?MenuId=Products&FunctionId=ProductView&ItemId=1151) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-axiomtek-ico300-csharp.md)|
|[Puerta de enlace de Sensor QuiX de tecnología de base](  https://www.quix.jp/iot/iotqsx/) |Raspbian | Python|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/raspbian-quix-sensor-gateway-python.md)|
|[BeagleBone negro](http://beagleboard.org/black) | Debian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-beaglebone-black-c.md)|
|[BeagleBone negro](http://beagleboard.org/black) | Debian | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-beaglebone-black-java.md)|
|[BeagleBone negro](http://beagleboard.org/black) | Debian | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample.md)|
|[BeagleBone verde](http://beagleboard.org/green) |Debian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-beaglebone-green-c.md)|
|[BeagleBone verde](http://beagleboard.org/green) |Debian | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-beaglebone-green-java.md)|
|[BeagleBone verde](http://beagleboard.org/green) |Debian | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample.md)|
|[Cuadro de Bitjoule mágico](http://www.bjbox.io/) |Raspbian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/raspbian-bitjoule-magic-box-c.md)|
|[BM180 de combinación](http://www.mypidion.com/product/product_tab.asp?bmenu=1&t_idx=508) |Windows 10 Mobile IoT Enterprise | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-mobile-enterprise-BM180-csharp.md)|
|[BP30 de combinación](http://www.mypidion.com/product/product_tab.asp?bmenu=1&t_idx=509) |Windows 10 Mobile IoT Enterprise | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-mobile-enterprise-BP30-csharp.md)|
|[EF400 de combinación](http://www.mypidion.com/product/product_tab.asp?bmenu=1&t_idx=519) |Windows 10 Mobile IoT Enterprise| C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-mobile-enterprise-EF400-csharp.md)|
|[EF500 de combinación](http://www.mypidion.com/product/product_tab.asp?bmenu=1&t_idx=511) |Windows 10 Mobile IoT Enterprise | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-mobile-enterprise-EF500-csharp.md)|
|[EF500R de combinación](http://www.mypidion.com/product/product_tab.asp?bmenu=1&t_idx=511) |Windows 10 Mobile IoT Enterprise | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-mobile-enterprise-EF500R-csharp.md)|
|[Clientron IT800](http://www.clientron.com/en/goods.php?act=view&no=38) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-it800-c.md)|
|[Clientron PT6500](http://www.clientron.com/en/goods.php?act=view&no=20) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-PT6500-c.md)|
|[Túnel de ComponentSoft radio frecuencia ID](http://www.componentsoft.com/) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-rfid-tunnel-csharp.md)|
|[CONTEC BX-320](http://www3.contec.co.jp) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-BX-320-csharp.md)|
|[CONTEC BX-830](http://www3.contec.co.jp) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-BX-830-csharp.md)|
|[CONTEC CPS-MC341-ADSC2](http://www3.contec.co.jp/B2B/ConIWCatProductPage_B2B.process?Merchant_Id=1&Section_Id=0&Catalog_Id=0&Product_Id=2537) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-CPS-MC341-ADSC2-c.md)|
|[Serie de DataVan Glamor]( http://www.datavan.com.tw/zh_hant/product_4.html ) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-glamor-series-csharp.md)|
|[Serie de puerta de enlace 5000 Dell borde](http://www.dell.com/IoTgateway) |Ubuntu | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-dell-edge-gateway-5000-series-java.md)|
|[DFI EC700-BT](http://www.dfi.com.tw/Upload/Product/Documents/BT700.pdf) |Windows 10 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows7-ec700-bt-c.md)|
|[DUX Inc. HFBX-100](http://dux.jp/product/hfbx-100.html) |Windows10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-HFBX-100-csharp.md)|
|[e-con sistemas Almach](http://www.e-consystems.com/DM3730-development-board.asp) |Linux Yocto | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-almach-c.md)|
|[e-con sistemas Ankaa](http://www.e-consystems.com/iMX6-development-board.asp) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-ankaa-c.md)|
|[Nube de Ecomott registrador LTE](http://www.ecomott.co.jp/product/cloudloggerlte.html) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-cloud-logger-lte-c.md)|
|[sistemas incrustados LogicMachine serie](http://openrb.com/products/) |Linux personalizado | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-logicmachine-c.md)|
|[Fitivision FDG-1000](http://www.fitivision.com/) |Linux | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/openwrt-honeybee-c.md)|
|[Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) |mbed 2.0 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/mbed-freescale-k64f-c.md)|
|[Tableta FUJITSU STYLISTIC con UBIQUITOUSWARE](http://www.fmworld.net/biz/uware/) |Windows10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-arrows-tab-csharp.md)|
|[Hanasis hardware-5000](http://hanasis.com/index_eng_test/hanasis/product/pos/pos_hw-5000.php) |Windows 10 IoT Enterprise 2015 LTSB| C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-enterprise-2015-ltsb-hw-5000-c.md)|
|[Hitachi HF-W/IoT](http://www.hitachi-ics.co.jp/product/iot_ctr/index.html) |Windows 10| C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-HF-WIoT-c.md)|
|[HPE Edgeline EL10](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884747.pdf) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-8.1-embedded-industry-enterprise-el10-csharp.md)|
|[HPE Edgeline EL20](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884769.pdf) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-8.1-embedded-industry-enterprise-el20-csharp.md)|
|[iBase 897 CSB200](http://www.ibase-usa.com/english/ProductDetail/EmbeddedComputing/CSB200-897) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-csb200-897-csharp.md)|
|[iBase Hueco-12](http://www.ibase.com.tw/english/ProductDetail/DigitalSignagePlayer/SI-12) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-si-12-csharp.md)|
|[IEI ECW-281B-BT](http://tw.ieiworld.com/product_groups/industrial/content.aspx?keyword=ECW-281&gid=09049552811981014603&cid=0D182494345754583862&id=0F330533395123928332#.Vufhbvl97Dc) |Windows 10| C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iei-ecw-281b-bt-csharp.md)|
|[ICECARE IEI-10 W](http://www.ieimobile.com/index.php?option=com_content&view=article&id=222&Itemid=21) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-icecare-10w-csharp.md)|
|[IEI DRPC-120](http://www.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0D182494345754583862&id=0E318374091597499543#.VqW3Q_l97Dd) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-drpc-120-csharp.md)|
|[IEI VECTORES DE INICIALIZACIÓN-100-BT](http://tw.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0F202412454715193114&id=0F202496627608256517#.VqH1hvl97Dc) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-ivs-100-bt-csharp.md)|
|[IEI CISTERNA-801-BT](http://www.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0D182496000560957303&id=0E316420627555447071#.VuZ1evl97IV) |Windows 10| C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iei-tank-801-bt-csharp.md)|
|[IEI uIBX-230-BT](http://www.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0D182494345754583862&id=0E317335590193360443) |Windows10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iei-uibx-230-bt-csharp.md)|
|[Servidor de EVA Ilevia](http://www.ilevia.com/eve-server/) |Debian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-eve-raspberry-c.md)|
|[Puerta de enlace de campo Innovactive IoT.NET](http://www.innovactive.it/microsoft-azure-certified-for-iot) |.NET Framework micro | C# | [Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/dotnet-microframework-iot-net-field-gateway-csharp.md)|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/yocto-intel-edison-c.md)|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample.md)|
|[Inventec Corp Avatar](http://www.ioeworld.net/) |Android | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/android-avatar-dev-board-java.md)|
|[Inventec Corp Avatar](http://www.ioeworld.net/) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-avatar-csharp.md)|
|[iWave iW-arco iris-G15M](http://www.iwavesystems.com/i-mx6-qseven-som.html) |WEC2013 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/wec2013-iw-rainbow-G15m-c.md)|
|[Keith & Koep Myon](http://keith-koep.com/en/products/products-som/myon-1-features-snapdragon-410/) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-myon-csharp.md)|
|[Keith & Koep TrizepsVII](http://keith-koep.com/en/products/products-trizeps/trizeps-vii-features/) |Debian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-trizeps-vii-c.md)|
|[Lanner LEC 2580 IPC](http://www.lannerinc.com/products/embedded-box-pcs/industrial-automation/lec-2580) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-lanner-lec-2580-ipc-csharp.md)|
|[Lanner LEC-6030](http://www.lannerinc.com/products/industrial-communication-platforms/industrial-cyber-security-box-pc/lec-6030) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-lanner-lec-6030e-csharp.md)|
|[Lanner NCA-1010](http://www.lannerinc.com/products/network-appliances/x86-desktop-network-appliances/nca-1010) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-lanner-nca-1010-csharp.md)|
|[Libelium Meshlium Xtreme](http://www.libelium.com/products/meshlium/) |Debian | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/Debian-meshlium-java.md)|
|[MDS tecnología IDM-IWP-hardware-2](http://mdstec.com/en/contents/?no=326) |Windows | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-idm-iwp-hw-2-java.md)|
|[MechaTracks 3GPI](http://www.mechatrax.com/products/3gpi) |Raspbian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/raspbian-3gpi-c.md)|
|[Estrella de Micro internacional Co. Ltd. NB31](https://www.msi.com/product/tablet/NB31.html#hero-overview) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-nb31-csharp.md)|
|[Minnowboard Max](http://www.minnowboard.org/meet-minnowboard-max/) |Windows 7,8, 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-minnowboard-max-csharp.md)|
|[MiTAC informática tecnología Plutón E220](http://client.mitac.com/products-Embedded-Box-PC-PlutoE220.html ) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-pluto-e220-csharp.md)|
|[Panel de animación Control Henry](http://www.runele.com/ca1/38/p-r-s/) |Yocto | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-henry-board-javascript.md)|
|[Moxa ioPAC5542-C](http://www.moxa.com.tw/Product/ioPAC_5500.htm) |Linux | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-iopac5542-c-c.md)|
|[MSC tecnologías Baytrail familiares](https://www.msc-technologies.eu/de/support/boards/qseven/msc-q7-bt.html) |Linux (MSC-ld k) | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-msc-ldk-baytrail-c.md)|
|[MSC tecnologías Baytrail familiares](https://www.msc-technologies.eu/de/support/boards/qseven/msc-q7-bt.html) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-msc-baytrail-csharp.md)|
|[Nexcom NDiS B535]( http://www.nexcom.com.tw/Products/multi-media-solutions/digital-signage-player/high-performance-player/digital-signage-player-ndis-b535 ) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-ndis-b535-csharp.md)|
|[Nexcom NIFE100](http://www.nexcom.com.tw/Products/industrial-computing-solutions/pc-based-factory-automation/industr) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-NIFE100-csharp.md)|
|[Nexcom NIFE200](http://www.nexcom.com.tw/Products/industrial-computing-solutions/pc-based-factory-automation/industr) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-NIFE200-csharp.md)|
|[Nexcom NISE50](http://www.nexcom.com.tw/Products/industrial-computing-solutions/industrial-fanless-computer/atom-co) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-NISE50-csharp.md)|
|[NEXCOM NISE 50C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-iot-core-nexcom-nise50-csharp.md)|
|[Nexcom PDSB 325](http://www.nexcom.com/Products/multi-media-solutions/bulletin-board-solutions/media-player-appliance/network-player-pdsb-325) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-pdsb-325-csharp.md)|
|[Nexcom VTC1010](http://www.nexcom.com.tw/Products/mobile-computing-solutions/in-vehicle-pc/in-vehicle-pc/in-vehicle-) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-VTC1010-csharp.md)|
|[Nexcom VTC6210](http://www.nexcom.com.tw/Products/mobile-computing-solutions/train-pc/train-pc/transportation-comput) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-10-iot-core-VTC6210-csharp.md)|
|[RMN ND13](https://www.icp-nmr.com/news/IPC/msi-tablet.html) |Windows 10 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-nd13-c.md)|
|[Cuadro de sensor Iomote](http://www.iomote.com/iomotesensorbox.php) |OpenWRT | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/openwrt-iomote-sensor-box-javascript%20.md)|
|[G2021ES de sistemas de Control del Pacífico](http://www.pacificcontrols.net/products/G2021ES-Gateway.html) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-g2021es-c.md)|
|[PANASONIC Toughpad FZ-F1](http://www.panasonic.com/global/home.html) |Windows 10 Mobile IoT Enterprise | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-enterprise-fz-f1-csharp.md)|
|[Partner tecnológica EM-300](http://www.partner.com.tw/product/default.asp?prober=134) |Windows 10| C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-EM-300-c.md)|
|[PFU AR2100-modelo - 120K.](http://www.pfu.fujitsu.com/prodes/product/ar/ar2100_120k.html) |Windows 10 |C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-AR2100-model-120K-c.md)|
|[PFU AR2200-modelo - 120K.](http://www.pfu.fujitsu.com/prodes/product/ar/ar2200_120k.html) |Windows 10 |C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-AR2200-model-120K-c.md)|
|[Plat'Home OpenBlocks IoT BX1G](https://www.plathome.com/en/bx1g/bx1/) |Debian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-openblocks-iot-bx1g-c.md)|
|[SE-8124 protech](http://www.protech-usa.net/new_web/Products/product_en.asp?P_id=546&P_typeNo=4&P_subtypeNo=2#tab-1) | Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/Windows10-epc-btcr-csharp.md)|
|[Pi frambuesas 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/raspbian-raspberrypi2-c.md)|
|[Pi frambuesas 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample.md)|
|[Pi frambuesas 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample.md)|
|[Pi frambuesas 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Windows 10 IoT principales| C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iotcore-csharp.md)|
|[Pi frambuesas 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) | Raspbian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/raspbian-raspberrypi2-c.md)|
|[Pi frambuesas 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) | Raspbian | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample.md)|
|[Pi frambuesas 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) | Raspbian | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample.md)|
|[Pi frambuesas 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) | Windows 10 IoT principales| C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iotcore-csharp.md)|
|[Renesas Electronics Corporation GR-MELOCOTÓN](http://gadget.renesas.com/en/product/peach.html) |Mbed | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/mbed-renesas-gr-peach-c.md)|
|[RICOH IT9](http://industry.ricoh.com/fbx_board/it9/index.html) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-IT9-csharp.md)|
|[Sam4s TITAN-500](http://sam4s.co.kr/english/service/product/product_detail.asp?product_num=100073) |Windows 10 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-sam4s-titan-500-c.md)|
|[Samsung ARTIK](http://developer.samsung.com/artik) |Fedora | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/fedora-samsung-artik-c.md)|
|[Puerta de enlace de nube IoT SADE](http://sade.io/sade-iot-cloud-family) |mbed 2.0 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/mbed-sade-iot-cloud-gateway-c.md)|
|[SBC SECO-A80-eNUC](http://www.seco.com/prods/eu/sbc-a80-enuc.html) |Windows10 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-sbc-a80-enuc-c.md)|
|[SBC SECO-A80-eNUC](http://www.seco.com/prods/eu/sbc-a80-enuc.html) |Ubuntu | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ubuntu-sbc-a80-enuc-c.md)|
|[Cuadro de Midori SERAKU](https://midori-cloud.net/service.html) |Debian | Python|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-midoribox-python.md)|
|[SinTau SrL GROPIUS](http://www.sintau.it/index.php?option=com_virtuemart&view=productdetails&virtuemart_product_id=6&virtuemart_category_id=1&Itemid=360&lang=it) |Debian | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-gropius-c.md)|
|[SinTau SrL GROPIUS](http://www.sintau.it/index.php?option=com_virtuemart&view=productdetails&virtuemart_product_id=6&virtuemart_category_id=1&Itemid=360&lang=it) |Debian | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/debian-gropius-java.md)|
|[Puerta de enlace de Smarthesia PasSy](http://www.smarthesia.com/#!home-page/gj699) |Yocto| JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/yocto-PasSy-Gateway-nodejs.md)|
|[SOTEC CloudPlug](http://cloudplug.info/) |YOCTO | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-sotec-cloudplug-c.md)|
|[Sparkfun ThingDev](https://www.sparkfun.com/products/13711) |Arduino IDE(RTOS)| Arduino, C|[Introducción](https://azure.microsoft.com/en-us/documentation/samples/iot-hub-c-thingdev-getstartedkit/)|
|[Spreadtrum SC98xx](http://www.spreadtrum.com/en/SC9830A.html) |Android Mallorca (v6.0.1)| Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/android-sc98xx-board-java.md)|
|[Supermicro sistema-D 5018-FN4T](https://www.supermicro.com/products/system/1u/5018/sys-5018d-fn4t.cfm) |Windows 10 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-supermicro-5018d-fn4t-c.md)|
|[Supermicro sistema-D 5018-FN8T](https://www.supermicro.com/products/system/1u/5018/SYS-5018D-FN8T.cfm) |Windows 10 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-supermicro-5018d-fn8t-c.md)|
|[Supermicro sistema-D 5018-LN4T](https://www.supermicro.com/products/system/1u/5018/SYS-5018D-LN4T.cfm) |Windows 10 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-supermicro-5018d-ln4t-c.md)|
|[E200 sistema supermicro-8 b](https://www.supermicro.com.tw/products/system/Mini-ITX/SYS-E200-8B.cfm) |Windows 10 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-supermicro-sys-e200-8b-c.md)|
|[Supermicro de sistema-E200 9B](https://www.supermicro.com.tw/products/system/Mini-ITX/SYS-E200-9B.cfm) |Windows 10 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-supermicro-sys-e200-9b-c.md)|
|[TM5 Techman Robot Inc.](http://tm-robot.com/robot/) |Windows incrustado estándar 7 | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-embedded-standard-7-tm5-c.md)|
|[W2810PRO Thecus tecnología Corporation](http://wss.thecus.com/product_W2810PRO.php) |Almacenamiento de Windows Server 2012 R2 Essentials | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-storage-server-2012-r2-essentials-thecus w2810pro-csharp.md)|
|[W5810 Thecus tecnología Corporation](http://wss.thecus.com/product_W5810.php) |Almacenamiento de Windows Server 2012 R2 Essentials | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows-storage-server-2012-r2-essentials-thecus w5810-csharp.md)|
|[CC3200 DE TI](http://www.ti.com/product/cc3200) |TI RTOS 2.x | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/ti-rtos-ti-cc3200-c.md)|
|[Toradex Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto)  | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample-toradex-linux.md)|
|[Toradex Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto)  | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample-toradex-linux.md)|
|[Toradex Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto)  | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample-toradex-linux.md)|
|[Toradex Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto)  | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample-toradex-linux.md)|
|[Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample-toradex-linux.md)|
|[Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample-toradex-linux.md)|
|[Toradex Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2) |Linux Angstrom(Yocto) | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample-toradex-linux.md)|
|[Toradex Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iotcore-csharp.md)|
|[Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | Java|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-run-sample-toradex-linux.md)|
|[Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | JavaScript|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-run-sample-toradex-linux.md)|
|[Toshiba FAB-s320](http://www.toshiba-tops.co.jp/embedded/fab_p/index_j.html) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-FAB-s320-csharp.md)|
|[TREX NGP](http://www.trex.com.tr/en/donanim_dcasngp8739_73.php) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-trex-ngp-csharp.md)|
|[Trueverit V4](http://www.trueverit.com/) |Linux personalizado | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-trueverit-v4-c.md)|
|[EDA8909 USISH](http://www.usish.com/) |Windows 10 | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-eda8909-csharp.md)|
|[VaneLife VHo1-X]( http://www.vanelife.com/product/detail/2) |Linux Beaglebone | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-vho1-x-c.md)|
|[Vantron VT-M2M-BTA-DE](http://vantrontech.com/hardwares/VT-M2M-BTA-DE.htm) |Windows 10 IoT principales | C#|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/windows10-iot-core-vt-m2m-bta-de-csharp.md)|
|[Vantron VT-M2M-LV](http://vantrontech.com/hardwares/VT-M2M-LV.htm) |Linux personalizado | Python|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-vt-m2m-lv-python.md)|
|[YASKAWA MMLink-GW](http://www.ysknet.co.jp/product/type/networkboard/mmlink-gw/index.html) |Linux | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linux-MMLink-GW-c.md)|
|[Zerotech inteligente](http://www.zerotech.com/smart-en.html) |Linaro | C|[Introducción](https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/linaro-SMART-c.md)|

[Empezar a usar estos dispositivos](https://azure.microsoft.com/develop/iot/get-started/) o visite nuestro GitHub [repositorio](https://github.com/Azure/azure-iot-sdks) y buscar en documentos de dispositivo por idioma.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo desarrollar soluciones con [certificados para dispositivos IoT](http://azure.com/iotdev).

Para obtener más información sobre cómo planear la implementación de concentrador IoT, consulte:

- [Admitir protocolos adicionales][lnk-protocols]
- [Comparar con Hubs de evento][lnk-compare]
- [Ajuste de escala, HA y DR][lnk-scaling]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
