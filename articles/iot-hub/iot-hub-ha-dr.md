<properties
 pageTitle="HA de concentrador IoT y DR | Microsoft Azure"
 description="Describe las características que ayudan a crear altamente disponible IoT soluciones con desastres capacidades de recuperación."
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
 ms.date="02/03/2016"
 ms.author="elioda"/>

# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Concentrador IoT alta disponibilidad y recuperación de desastres

Como un servicio de Azure, concentrador IoT proporciona alta disponibilidad (HA) mediante redundantes en el nivel de la región de Azure, sin ningún trabajo adicional necesario para la solución. Además, Azure ofrece diversas características que ayudan a crear soluciones con la capacidad de recuperación (DR) o entre región disponibilidad si es necesario. Debe diseñar y preparar las soluciones para aprovechar las ventajas de estas características de DR si desea proporcionar global, entre región alta disponibilidad para dispositivos o usuarios. El artículo [Orientación técnica de Azure Business continuidad](../resiliency/resiliency-technical-guidance.md) describe las características integradas de Azure para continuidad empresarial y DR. Papel [de recuperación y alta disponibilidad para las aplicaciones de Azure][] proporciona orientación sobre la arquitectura en estrategias para aplicaciones de Azure lograr HA y DR.

## <a name="azure-iot-hub-dr"></a>Azure DR IoT concentrador
Además de HA dentro de un área, concentrador IoT implementa mecanismos de migración tras error de recuperación que no requieren intervención del usuario. IoT Hub de DR se inicia automáticamente y tiene un objetivo de tiempo de recuperación (RTO) de 2-26 horas y los siguientes objetivos de punto de recuperación (RPOs).

| Funcionalidad | RPO |
| ------------- | --- |
| Disponibilidad del servicio para operaciones de registro y comunicación | Posibles pérdidas de CName |
| Datos de identidad en registro de identidad de dispositivo | 0-5 minutos pérdida de datos |
| Mensajes de la nube de dispositivo | Se pierden todos los mensajes no leídos |
| Operaciones de supervisión de mensajes | Se pierden todos los mensajes no leídos |
| Mensajes de dispositivo de nube | 0-5 minutos pérdida de datos |
| Cola de dispositivo de nube comentarios | Se pierden todos los mensajes no leídos |

## <a name="regional-failover-with-iot-hub"></a>Regional migración tras error con concentrador IoT

Una descripción completa de topologías de implementación de soluciones de IoT está fuera del ámbito de este artículo, pero con el fin de alta disponibilidad y recuperación tendremos en cuenta el modelo de implementación *regional migración tras error* .

En un modelo regional migración tras error, se ejecuta el back-end de solución principalmente en una ubicación de centro de datos, pero un concentrador IoT adicional y back-end se implementan en otra ubicación del centro de datos con fines de migración tras error, en caso de una interrupción se ve afectado el concentrador IoT en el centro de datos principal o la conectividad de red desde el dispositivo en el centro de datos principal se interrumpe de algún modo. Dispositivos utilizan un extremo de servicio secundario siempre que no se puede llegar la puerta de enlace principal. Con una capacidad entre región migración tras error, se puede mejorar la disponibilidad de la solución más allá de la alta disponibilidad de una sola región.

A un alto nivel, para implementar un modelo de regional migración tras error con IoT concentrador, necesitará la siguiente.

* **Un concentrador IoT y dispositivo enrutamiento lógica secundario**: en el caso de una interrupción del servicio en su región principal, dispositivos deben comenzar a conectarse a su región secundario. Dada la naturaleza tener en cuenta el estado de la mayoría de los servicios que implica, es común para los administradores de solución desencadenar el proceso de migración tras error de entre región. La mejor manera de comunicar el nuevo extremo a los dispositivos, sin perder el control del proceso, es que compruebe periódicamente un servicio de *soporte* del extremo activo actual. El servicio de soporte puede ser una aplicación web simple replicar y mantener accesibles mediante técnicas de redirección de DNS (por ejemplo, con el [Administrador de tráfico de Azure][]).
* **Replicación de registro de identidad** - para ser utilizable, el secundario IoT concentrador debe contener todas las identidades de dispositivo que pueden conectarse a la solución. La solución debe mantener replicar geo copias de seguridad de las identidades del dispositivo y cargarlos en el hub de IoT secundario antes de cambiar del extremo activo para los dispositivos. La funcionalidad de exportación de identidad de dispositivo de concentrador IoT es muy útil en este contexto. Para obtener más información, vea [Guía de desarrollador de concentrador IoT - registro de identidad][].
* **Combinación lógica** - cuando vuelva a estar disponible la región principal, todo el estado y los datos que se han creado en el sitio secundario deben migrarse vuelta a la región principal. Esto principalmente se relaciona con las identidades de dispositivo y aplicación metadatos, que deben combinarse con el concentrador IoT principal y cualquier otros stores específicos de la aplicación en el área principal. Para simplificar este paso, por lo general se recomienda que use idempotente operaciones. Esto minimiza efectos secundarios no solo desde posterior distribución coherente de eventos, sino también de duplicados o de entrega de fuera de la secuencia de eventos. Además, la lógica de aplicación debe diseñarse tolerar posibles incoherencias o "ligeramente" fuera de la fecha de estado. Esto es debido a la más tiempo tarda el sistema de "limpieza" basado en los objetivos de punto de recuperación (RPO).

## <a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información acerca de Azure IoT concentrador:

- [Introducción a IoT Hubs (Tutorial)][lnk-get-started]
- [¿Qué es Azure IoT concentrador?][]

[Recuperación y alta disponibilidad para las aplicaciones de Azure]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Administrador de tráfico de Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Guía del desarrollador de concentrador IoT - registro de identidad]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[¿Qué es Azure IoT concentrador?]: iot-hub-what-is-iot-hub.md
