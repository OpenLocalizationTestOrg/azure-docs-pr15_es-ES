<properties
    pageTitle="¿Qué es Hubs de evento de Azure? | Microsoft Azure"
    description="Información general y una descripción de Azure evento Hubs"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="sethm"/>

# <a name="what-is-azure-event-hubs"></a>¿Qué es Hubs de evento de Azure?

Azure Hubs de evento es un servicio de entrada de datos de gran escalabilidad que puede recopilar millones de eventos por segundo para que pueda procesar y analizar el grandes cantidades de datos creados mediante los dispositivos conectados y las aplicaciones. Evento Hubs actúa como la "puerta principal" para la canalización de un evento, y una vez que se recopilan los datos a un concentrador de evento, se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de almacenamiento por lotes. Evento Hubs separa la producción de una secuencia de eventos desde el consumo de esos eventos, para que los consumidores de eventos pueden tener acceso a los eventos en su propia programación. Para obtener más información y detalles técnicos, vea la [información general de evento Hubs](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Capacidades de Hubs de evento

Hubs de evento es un servicio que proporciona eventos y procesamiento de telemetría en escala masiva, con baja latencia y alta confiabilidad de procesamiento de evento. Este servicio es especialmente útil para:

- Instrumentación de aplicaciones
- Experiencia de usuario o el procesamiento de flujo de trabajo
- Escenarios de Internet de cosas (IoT)

Algunas capacidades evento Hubs clave incluyen el comportamiento de seguimiento en aplicaciones móviles, el tráfico de información de conjuntos de servidores web, captura de eventos de juego en juegos de consola o telemetría recopilado máquinas industriales o vehículos conectados.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información detallada sobre el evento Hubs, vea los temas siguientes.

- [Información general de Hubs de evento](event-hubs-overview.md)
- [Guía de programación de Hubs de evento](event-hubs-programming-guide.md)
- [Evento Hubs P+F de disponibilidad y soporte técnico](event-hubs-availability-and-support-faq.md)
- Introducción a un [evento Hubs tutorial][]
- Una completa [aplicación de ejemplo que usa Hubs de evento][]

[Tutorial de Hubs de evento]: event-hubs-csharp-ephcs-getstarted.md
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
