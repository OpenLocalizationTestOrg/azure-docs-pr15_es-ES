<properties
 pageTitle="Guía de programador - IoT concentrador SDK | Microsoft Azure"
 description="Azure IoT concentrador developer guide - información sobre y vínculos a los diversos SDK de dispositivos y servicios de Azure IoT concentrador."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016"
 ms.author="dobett"/>

# <a name="iot-hub-sdks"></a>Concentrador IoT SDK

## <a name="iot-hub-device-sdks"></a>Dispositivo concentrador IoT SDK

El dispositivo de Microsoft Azure IoT SDK contienen código que facilita la creación dispositivos y aplicaciones que se conectan a y se administran mediante servicios de Azure IoT concentrador.

El siguiente dispositivo IoT SDK están disponibles para descargar de GitHub:

- [Dispositivo Azure IoT SDK para C] [ lnk-c-device-sdk] escrita en ANSI C (C99) para la compatibilidad de plataforma amplia y portabilidad.
- [Dispositivo Azure IoT SDK para .NET][lnk-dotnet-device-sdk]
- [Dispositivo Azure IoT SDK para Java][lnk-java-device-sdk]
- [Dispositivo Azure IoT SDK para Node.js][lnk-node-device-sdk]
- [Dispositivo de Microsoft Azure IoT SDK para Python 2.7][lnk-python-device-sdk]

> [AZURE.NOTE] Vea el archivo Léame en los repositorios GitHub para obtener información sobre cómo usar los jefes de paquete específico de la plataforma y de idioma para instalar archivos binarios y las dependencias en el equipo de desarrollo.

## <a name="os-platforms-and-hardware-compatibility"></a>Compatibilidad de hardware y plataformas de sistemas operativos

Para obtener más información sobre la compatibilidad SDK con dispositivos de hardware específicos, consulte la [Azure certificados para el catálogo de dispositivo IoT][lnk-certified].

## <a name="iot-hub-service-sdks"></a>Concentrador IoT servicio SDK

El SDK de servicio de Microsoft Azure IoT contiene código que facilita la creación de aplicaciones que interactúan directamente con IoT concentrador para administrar dispositivos y seguridad.

El servicio de IoT SDK siguiente están disponibles para descargar de GitHub:

- [Servicio Azure IoT SDK para .NET][lnk-dotnet-service-sdk]
- [Servicio Azure IoT SDK para Node.js][lnk-node-service-sdk]
- [Servicio Azure IoT SDK para Java][lnk-java-service-sdk]

> [AZURE.NOTE] Vea el archivo Léame en los repositorios GitHub para obtener información sobre cómo usar los jefes de paquete específico de la plataforma y de idioma para instalar archivos binarios y las dependencias en el equipo de desarrollo.

## <a name="azure-iot-gateway-sdk"></a>SDK de puerta de enlace de Azure IoT

Este SDK de puerta de enlace de Azure IoT contiene la infraestructura y módulos para crear soluciones de puerta de enlace IoT. Puede extender el SDK para crear puertas de enlace adaptadas a cualquier escenario de llevar a cabo.

Puede descargar el [SDK de puerta de enlace de Azure IoT] [ lnk-gateway-sdk] desde GitHub.

## <a name="online-api-reference-documentation"></a>Documentación en línea de referencia de API

A continuación se muestra una lista de vínculos a la documentación en línea de referencia de API de Azure IoT dispositivo, servicio y bibliotecas de puerta de enlace:

- [Internet de cosas (IoT) .NET][lnk-dotnet-ref]
- [Concentrador IoT REST][lnk-rest-ref]
- [Dispositivo Azure IoT SDK para C][lnk-c-ref]
- [Dispositivo Azure IoT SDK para Java][lnk-java-ref]
- [Servicio Azure IoT SDK para Java][lnk-java-service-ref]
- [Dispositivo Azure IoT SDK para Node.js][lnk-node-ref]
- [Servicio Azure IoT SDK para Node.js][lnk-node-service-ref]
- [Puerta de enlace de Azure IoT SDK][lnk-gateway-ref]

## <a name="next-steps"></a>Pasos siguientes

Otros temas de referencia en esta guía para programadores de concentrador IoT incluyen:

- [Extremos de concentrador IoT][lnk-devguide-endpoints]
- [Lenguaje de consulta para gemelos, métodos y trabajos][lnk-devguide-query]
- [Cuotas y límite][lnk-devguide-quotas]
- [Soporte técnico de IoT concentrador MQTT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md