<properties 
    pageTitle="Arquitectura de Bus de servicio | Microsoft Azure"
    description="Describe el mensaje y retransmisión de procesamiento de arquitectura de Bus de servicio de Azure."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm" />

# <a name="service-bus-architecture"></a>Arquitectura de Bus de servicio

Este artículo describe el mensaje y retransmisión de procesamiento de arquitectura de Bus de servicio de Azure.

## <a name="service-bus-scale-units"></a>Unidades de la escala de Bus de servicio

Bus de servicio está organizada por *unidades de escala*. Una unidad de escala es una unidad de implementación y todos los contiene componentes necesarios ejecutarán el servicio. Cada región implementa una o más unidades de la escala de Bus de servicio.

Un espacio de nombres de Bus de servicio se asigna a una unidad de escala. La unidad de escala controla todos los tipos de entidades de Bus de servicio: transmisiones y con mensajería entidades (colas, temas, suscripciones). Una unidad de escala de Bus de servicio consta de los siguientes componentes:

- **Un conjunto de nodos de puerta de enlace.** Los nodos de puerta de enlace autentican las solicitudes entrantes y administrar solicitudes de retransmisión. Cada nodo de puerta de enlace tiene una dirección IP pública.

- **Un conjunto de nodos de agente de mensajería.** Mensajería nodos de agente procesan solicitudes de mensajería entidades.

- **Almacenamiento de una puerta de enlace.** El almacén de puerta de enlace contiene los datos de todas las entidades que se definen en la unidad de la escala. El almacén de puerta de enlace se implementa sobre una base de datos de SQL Azure.

- **Varios stores mensajería.** Mensajería stores almacenar los mensajes de todas las colas, temas y las suscripciones que se definen en esta unidad de escala. También contiene todos los datos de la suscripción. A menos que [divididas mensajería entidades](service-bus-partitioning.md) está habilitada, una cola o tema se asigna a un almacén de mensajería. Las suscripciones se almacenan en el mismo almacén de mensajería como su tema principal. Excepto Bus de servicios [De mensajería Premium](service-bus-premium-messaging.md), la mensajería stores se implementan sobre bases de datos de SQL Azure.

## <a name="containers"></a>Contenedores

Cada entidad mensajería se asigna a un contenedor específico. Un contenedor es una construcción lógica que usa exactamente un mensajería al almacén de todos los datos pertinentes para este contenedor. Cada contenedor se asigna a un nodo de intermediario mensajería. Normalmente, hay más contenedores de mensajería nodos de agente. Por lo tanto, cada nodo agente mensajería carga varios contenedores. La distribución de los contenedores de un nodo de intermediario mensajería está organizada tal que todos los nodos de agente mensajería igualmente se cargan. Si la carga patrón cambios (por ejemplo, uno de lo obtiene contenedores muy ocupado), o si un nodo de intermediario mensajería no estará disponible temporalmente, los contenedores se redistribuyen entre los nodos de agente mensajería.

## <a name="processing-of-incoming-messaging-requests"></a>Procesamiento de las solicitudes entrantes de mensajería

Cuando un cliente envía una solicitud al Bus de servicio, el equilibrador de carga de Azure lo enruta a cualquiera de los nodos de puerta de enlace. El nodo de puerta de enlace autoriza la solicitud. Si la solicitud se refiere a una entidad de mensajería (cola, tema, suscripción), el nodo de puerta de enlace busca la entidad en el almacén de puerta de enlace y determina en qué almacén de mensajería se encuentra la entidad. A continuación, busca qué nodo agente mensajería está dando servicio a este contenedor y envía la solicitud a ese nodo agente mensajería. El nodo agente mensajería procesa la solicitud y actualiza el estado de la entidad en el almacén de contenedor. El nodo agente mensajería envía la respuesta al nodo de puerta de enlace, que envía una respuesta adecuada al cliente que emitió la solicitud original.

![Procesamiento de las solicitudes entrantes de mensajería](./media/service-bus-architecture/IC690644.png)

## <a name="processing-of-incoming-relay-requests"></a>Procesamiento de las solicitudes entrantes de retransmisión

Cuando un cliente envía una solicitud al Bus de servicio, el equilibrador de carga de Azure lo enruta a cualquiera de los nodos de puerta de enlace. Si la solicitud es una solicitud de escucha, el nodo de puerta de enlace crea una retransmisión de nueva. Si la solicitud es una solicitud de conexión para una retransmisión específica, el nodo de puerta de enlace reenvía la solicitud de conexión para el nodo de puerta de enlace que posee el relé. El nodo de puerta de enlace que posee el relé envía una convocatoria de reunión al cliente escucha, pidiendo la escucha para crear un canal temporal para el nodo de puerta de enlace que recibió la solicitud de conexión.

Cuando se establece la conexión de retransmisión, los clientes pueden intercambiar mensajes mediante el nodo de puerta de enlace que se usa para la cita.

![Procesamiento de las solicitudes entrantes de retransmisión](./media/service-bus-architecture/IC690645.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha leído una descripción general de la arquitectura de Bus de servicio, para empezar visite los siguientes vínculos:

- [Introducción a mensajería Bus de servicio](service-bus-messaging-overview.md)
- [Conceptos básicos de Bus de servicio](service-bus-fundamentals-hybrid-solutions.md)
- [Una solución de mensajería en cola mediante colas de Bus de servicio](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
