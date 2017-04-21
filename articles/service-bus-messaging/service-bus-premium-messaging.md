<properties
    pageTitle="Premium de Bus de servicio y descripción general de niveles de precios de mensajería estándar | Microsoft Azure"
    description="Mensajería estándar y Premium de Bus de servicio"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2016"
    ms.author="darosa;sethm"/>

# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Niveles de mensajería estándares y Premium de Bus de servicio 

Bus de servicios de mensajería, que incluye mensajería entidades como colas y temas, enterprise combina mensajería capacidades con enriquecido publicación suscripción semántica a escala de la nube. Bus de servicios de mensajería se usa como la espina de comunicación muchas nube sofisticadas soluciones.

El nivel de *Premium* de Bus de servicios de mensajería direcciones comunes solicitudes de cliente alrededor de escala, rendimiento y disponibilidad de aplicaciones críticas. Aunque los conjuntos de características son casi idénticos, estos dos niveles de Bus de servicios de mensajería están diseñados para servir de casos de uso diferentes.

Algunas diferencias de alto nivel se resaltan en la tabla siguiente.

| Premium                               | Estándar                       |
|---------------------------------------|--------------------------------|
| Alto rendimiento                       | Rendimiento variable            |
| Performance predecible               | Latencia variable               |
| Precios predecibles                   | Pago variables precios |
| Capacidad de escalar arriba y abajo de la carga de trabajo | N/A.                            |
| Cambiar el tamaño de mensaje > 256KB                  | Tamaño del mensaje es 256KB          |

**Premium de Bus de servicio de mensajería** proporciona aislamiento de recursos en el nivel de CPU y la memoria para que se ejecute cada carga de trabajo de cliente por separado. Este contenedor de recursos se denomina una *unidad de mensajería*. Cada espacio de nombres de premium está asignada al menos una unidad de mensajería. Puede adquirir 1, 2 o 4 unidades de mensajería para cada espacio de nombres de servicio Bus Premium. Una sola carga de trabajo o la entidad puede abarcar varias unidades de mensajería y el número de unidades de mensajería puede cambiarse cuando lo desee, aunque es de facturación de cargos de tasa diaria o de 24 horas. El resultado es un desempeño reproducible y predecible para la solución de Bus de servicio.

No solo es este rendimiento más predecible y está disponible, pero también es más rápido. Premium de Bus de servicio de mensajería se basa en el motor de almacenamiento que introdujo en [Azure evento Hubs](https://azure.microsoft.com/services/event-hubs/). Con la mensajería Premium pleno rendimiento es mucho más rápido con el nivel estándar.

## <a name="premium-messaging-technical-differences"></a>Diferencias técnicas de mensajería Premium

Las siguientes son algunas de las diferencias entre los niveles de mensajería estándares y Premium.

### <a name="partitioned-queues-and-topics"></a>Temas y colas divididas

Temas y colas divididas son compatibles con Premium mensajería, pero no funcionan igual que en las capas estándar y Basic de Bus de servicios de mensajería. Mensajería Premium no utiliza como un almacén de datos SQL y ya no está asociada la competencia posibles recursos a una plataforma compartida. Por tanto, no es necesaria particiones. Además, ha cambiado el recuento de partición de 16 particiones en mensajería estándar a 2 particiones en Premium. Tiene dos particiones garantiza la disponibilidad y es un número más adecuado para el entorno de tiempo de ejecución de Premium. Para obtener más información sobre la partición, consulte [con particiones colas y temas](service-bus-partitioning.md).

### <a name="express-entities"></a>Express entidades

Como mensajería Premium se ejecuta en un entorno de runtime completamente aisladas, entidades express no son compatibles en los espacios de nombres Premium. Para obtener más información acerca de la característica express, vea la propiedad [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) .

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Bus de servicio, vea los temas siguientes.

- [Introducción a Azure servicio Bus Premium mensajería (entrada de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introducción a Azure servicio Bus Premium mensajería (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Introducción a mensajería Bus de servicio](service-bus-messaging-overview.md)
- [Cómo usar colas de Bus de servicio](service-bus-dotnet-get-started-with-queues.md)
