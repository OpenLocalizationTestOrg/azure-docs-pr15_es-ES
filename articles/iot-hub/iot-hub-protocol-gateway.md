<properties
   pageTitle="Puerta de enlace de Azure IoT protocolo | Microsoft Azure"
   description="Describe cómo usar la puerta de enlace de protocolo de Azure IoT para ampliar las capacidades y la compatibilidad con el protocolo de Azure IoT concentrador."
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="kdotchko"/>

# <a name="supporting-additional-protocols-for-iot-hub"></a>Compatibilidad con protocolos adicionales para IoT concentrador

Hub de Azure IoT admite comunicación sobre los protocolos MQTT, AMQP y HTTP. En algunos casos, dispositivos o puertas de enlace de campo no podrá usar uno de estos protocolos estándares y requieren adaptación de protocolo. En estos casos, puede usar una puerta de enlace personalizado. Una puerta de enlace personalizado puede habilitar adaptación de protocolo para los extremos de concentrador IoT puente el tráfico a y desde IoT concentrador. Puede usar la [puerta de enlace de protocolo de Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) como una puerta de enlace personalizado para habilitar adaptación de protocolo para IoT concentrador.

## <a name="azure-iot-protocol-gateway"></a>Puerta de enlace de Azure IoT protocolo

La puerta de enlace de protocolo de Azure IoT es un marco de adaptación de protocolo que está diseñado para la comunicación de dispositivo bidireccional con IoT concentrador de gran escala. La puerta de enlace de protocolo es un componente de paso a través que acepta conexiones de dispositivo sobre un protocolo específico. Une el tráfico a concentrador IoT sobre AMQP 1.0. La puerta de enlace de protocolo de Azure IoT está disponible como un proyecto de software de código abierto para proporcionar flexibilidad para agregar una gran variedad de protocolos y las versiones de protocolo.

Puede implementar la puerta de enlace de protocolo en Azure de forma muy scalable mediante funciones de trabajo de servicios de nube de Azure. Además, se puede implementar la puerta de enlace de protocolo en entornos locales, como puertas de enlace de campo.

La puerta de enlace de protocolo de Azure IoT incluye un adaptador de protocolo MQTT que le permite personalizar el comportamiento de protocolo MQTT si es necesario. Puesto que IoT concentrador proporciona compatibilidad integrada para el protocolo de v3.1.1 MQTT, solo debe tener en cuenta usando el adaptador de protocolo MQTT si tiene una necesidad de personalizaciones de protocolo o requisitos específicos para funciones adicionales.

El adaptador MQTT también muestra el modelo de programación para crear adaptadores de protocolo para otros protocolos. Además, el modelo de programación de puerta de enlace de protocolo IoT Azure le permite conectar componentes personalizados para procesamiento especializado como autenticación personalizada, transformaciones de mensajes, compresión y descompresión o cifrado/descifrado del tráfico entre los dispositivos y IoT concentrador.

Flexibilidad, la puerta de enlace de protocolo y la implementación de MQTT se proporcionan en un proyecto de software de código abierto. Esto le permite personalizar la implementación según sea necesario.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo utilizar e implementar como parte de su solución IoT y de la puerta de enlace de protocolo de Azure IoT, consulte:

* [Repositorio de puerta de enlace de protocolo de Azure IoT en GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guía de programador de puerta de enlace de protocolo de IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Para obtener más información sobre cómo planear la implementación de concentrador IoT, consulte:

- [Comparar con Hubs de evento][lnk-compare]
- [Ajuste de escala, HA y DR][lnk-scaling]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
