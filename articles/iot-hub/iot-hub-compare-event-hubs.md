<properties
 pageTitle="Comparar Azure Hub IoT Hubs evento Azure | Microsoft Azure"
 description="Comparación de los servicios de concentrador IoT de Azure y Azure evento Hubs resaltado diferencias funcionales y casos de uso."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="elioda"/>

# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparación de concentrador de IoT Azure y Azure evento Hubs

Uno de los casos de uso principal para IoT concentrador es recopilar telemetría de dispositivos. Por este motivo, IoT concentrador a menudo se compara con [Azure evento Hubs][]. Como concentrador IoT, Hubs de evento es un servicio que permite la entrada de evento y telemetría en la nube en escala masiva, con baja latencia y alta confiabilidad de procesamiento de evento.

Sin embargo, los servicios tienen muchas diferencias, que se detallan en la tabla siguiente.

| Área | Concentrador IoT | Evento Hubs |
| ---- | ------- | ---------- |
| Patrones de comunicación | Habilita el dispositivo a la nube y dispositivo de nube mensajería. | Solo se permite entrada de evento (normalmente se considera para escenarios de nube de dispositivo). |
| Compatibilidad con el protocolo dispositivo | Es compatible con MQTT, AMQP, AMQP WebSockets y HTTP. Además, concentrador IoT funciona con la [puerta de enlace de protocolo de Azure IoT][lnk-azure-protocol-gateway], una implementación de la puerta de enlace de protocolo personalizables para admitir protocolos personalizados. | Es compatible con AMQP, AMQP WebSockets y HTTP. |
| Seguridad | Proporciona control de acceso revocable y de identidad por dispositivo. Consulte la [sección seguridad de la Guía del programador IoT concentrador]. | Proporciona [directivas de acceso compartido]de todo el evento Hubs[Event Hubs - security], con revocación limitado soporte a través de [directivas de publisher][Event Hubs publisher policies]. Soluciones de IoT a menudo se requieren para implementar una solución personalizada para admitir credenciales por dispositivo y medidas contra la suplantación. |
| Operaciones de supervisión | Permite IoT soluciones para suscribirse a un amplio conjunto de eventos de conectividad y de administración de identidades de dispositivo como excepciones de formato incorrecto, errores de autenticación del dispositivo y límite. Estos eventos permiten identificar rápidamente los problemas de conectividad en el nivel de dispositivo individual. | Expone sólo agregan métricas. |
| Escala | Está optimizado para admitir millones de dispositivos conectados al mismo tiempo. | Puede admitir un número limitado de más de conexiones simultáneas--hasta 5.000 conexiones AMQP, según [las cuotas de Bus de servicio de Azure][]. Por otro lado, Hubs evento le permite especificar la partición para cada mensaje enviado. |
| SDK de dispositivos | Proporciona [dispositivo SDK] [ Azure IoT Hub SDKs] para una gran variedad de plataformas e idiomas. | Se admite en .NET y C. También proporciona AMQP y HTTP envían interfaces. |
| Carga de archivos | Permite IoT soluciones cargar archivos desde dispositivos a la nube. Incluye un extremo de notificación de archivo para la integración de flujo de trabajo y una categoría para soporte de depuración de supervisión de operaciones. | Usa un patrón de comprobación de crédito manualmente solicitar archivos desde dispositivos y proporcionar dispositivos con una clave de almacenamiento para la transacción. |

En resumen, aunque el único caso de uso es entrada de telemetría de dispositivo a la nube, concentrador IoT proporciona un servicio que está diseñado específicamente para la conectividad de dispositivo IoT. Para expandir las propuestas de valor para estos escenarios con características específicas de IoT continuará. Evento Hubs está diseñado para entrada de evento en una escala masiva, tanto en el contexto de escenarios de centro de datos entre y dentro de un centro de datos.

No es raro usar IoT concentrador y Hubs de evento en la misma solución--donde IoT concentrador controla la comunicación entre el dispositivo a la nube, y Hubs evento entrada de evento fase posterior en motores de procesamiento en tiempo real.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo planear la implementación de concentrador IoT, consulte [escala, HA y DR][lnk-scaling].

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

[Evento Azure Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Sección seguridad de la Guía del programador de concentrador IoT]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Azure cuotas de Bus de servicio]: ../service-bus-messaging/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
