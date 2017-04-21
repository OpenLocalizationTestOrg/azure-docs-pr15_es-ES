<properties
 pageTitle="Soluciones de Azure para Internet de cosas | Microsoft Azure"
 description="Una visión general de IoT en Azure como una arquitectura de la solución de ejemplo y cómo se relaciona con Azure IoT concentrador, SDK de dispositivos y soluciones preconfiguradas"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Pasos siguientes

Concentrador de IoT Azure es un servicio de Azure que permite seguro y comunicaciones de confianza bidireccional entre su aplicación final y millones de dispositivos. Permite que el servidor de aplicaciones para:

- Recibir telemetría a escala desde sus dispositivos.
- Distribuir datos desde sus dispositivos a un procesador de eventos de la secuencia.
- Recibir cargas de archivos de dispositivos.
- Enviar comandos de dispositivo de nube dispositivos específicos.

Puede utilizar IoT concentrador para implementar su propio back-end de solución. Además, concentrador IoT incluye un registro de la identidad de dispositivo usado para aprovisionar dispositivos, sus credenciales de seguridad y sus derechos para conectarse al concentrador. Para obtener más información sobre IoT concentrador, consulte [¿Qué es IoT concentrador?] [lnk-iot-hub].

Para obtener información sobre cómo Azure IoT concentrador permite basada en estándares de administración de dispositivos de IoT le permiten administrar, configurar y actualizar sus dispositivos de forma remota, consulte [administración de dispositivos de información general de Azure IoT concentrador][lnk-device-management].

Para implementar aplicaciones cliente en una amplia variedad de sistemas operativos y plataformas de hardware de dispositivo, puede usar el dispositivo IoT SDK. El dispositivo IoT SDK incluye bibliotecas que facilitan el envío telemetría a un concentrador IoT y recibir comandos de dispositivo de nube. Al usar el SDK, puede elegir desde varios protocolos de red para comunicarse con IoT concentrador. Para obtener más información, vea la [información sobre el dispositivo SDK][lnk-device-sdks].

Para empezar a escribir código y ejecutar algunos ejemplos, consulte la [Introducción a IoT concentrador] [ lnk-getstarted] tutorial.

También puede estar interesado en [El conjunto de aplicaciones de Azure IoT][lnk-iot-suite], que es un conjunto de soluciones preconfiguradas. IoT Suite le permite empezar rápidamente y escalar IoT proyectos para escenarios comunes de IoT--como supervisión remota, administración de activos y mantenimiento predictivo.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md