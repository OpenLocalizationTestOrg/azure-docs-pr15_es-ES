<properties
 pageTitle="Azure concentrador de IoT escala | Microsoft Azure"
 description="Describe cómo escalar Azure IoT concentrador."
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
 ms.date="09/19/2016"
 ms.author="elioda"/>

# <a name="scaling-iot-hub"></a>Escala IoT concentrador

Hub de Azure IoT pueden admitir hasta un millón de dispositivos conectados al mismo tiempo. Para obtener más información, vea [IoT concentrador precios][lnk-pricing]. Cada unidad de concentrador IoT permite a un determinado número de mensajes diarios.

Para ajustar correctamente la solución, considere el uso de concentrador IoT particular. En particular, tenga en cuenta el rendimiento de pico necesarios para las categorías de las operaciones siguientes:

* Mensajes de la nube de dispositivo
* Mensajes de dispositivo de nube
* Operaciones de registro de identidad

Además de esta información de rendimiento, consulte [límites y las cuotas de concentrador IoT][] y diseñar su solución según corresponda.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Rendimiento del dispositivo a la nube y dispositivo de nube de mensaje

Es la mejor manera de cambiar el tamaño de una solución IoT concentrador evaluar el tráfico de forma por unidad.

Mensajes de la nube de dispositivo siguen estas directrices de rendimiento constante.

| Nivel | Rendimiento prolongado | Tasa de envío prolongado |
| ---- | -------------------- | ------------------- |
| S1 | Hasta 1111 KB por minuto por unidad<br/>(1,5 GB/día/unidad) | Promedio de mensajes por minuto 278 por unidad<br/>(400.000 mensajes al día por unidad) |
| S2 | Hasta 16 MB por minuto por unidad<br/>(22,8 GB/día/unidad) | Promedio de 4167 mensajes por minuto por unidad<br/>(6 millones mensajes al día por unidad) |
| S3 | Hasta 814 MB por minuto por unidad<br/>(1144.4 GB/día/unidad) | Promedio de 208,333 mensajes por minuto por unidad<br/>(300 millones de mensajes al día por unidad) |

## <a name="identity-registry-operation-throughput"></a>Rendimiento de operación de registro de identidad

Las operaciones de registro de concentrador IoT identidad no se supone que las operaciones de tiempo de ejecución, como principalmente están relacionados con aprovisionamiento de dispositivos.

Para los números de rendimiento de ráfaga específico, vea [límites y las cuotas de concentrador IoT][].

## <a name="sharding"></a>Sharding

Mientras un concentrador IoT solo puede escalar a millones de dispositivos, a veces su solución requiere características de rendimiento específicas que no se puede garantizar un concentrador IoT único. En ese caso, se recomienda que dividir sus dispositivos en varias hubs IoT. Varios hubs IoT suavizados ráfagas de tráfico y obtienen el rendimiento necesario o tasas de operación necesarios.

## <a name="next-steps"></a>Pasos siguientes

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Límites y las cuotas de concentrador IoT]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
