<properties
    pageTitle="Introducción a mensajería Bus de servicio | Microsoft Azure"
    description="Mensajería de Bus de servicio: entrega de datos flexible en la nube"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="sethm"/>


# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Mensajería de Bus de servicio: entrega flexible de los datos en la nube

Bus de servicio de Microsoft Azure es un servicio de entrega de información fiable. El propósito de este servicio es facilitar la comunicación. Cuando dos o más partes desean intercambiar información, que necesitan un mecanismo de comunicación. Bus de servicio es un mecanismo de comunicación con o de terceros. Esto es similar a un servicio postal en el mundo real. Servicios postales que sea muy fácil enviar diferentes tipos de cartas y paquetes con una amplia variedad de garantías de entrega, en cualquier lugar del mundo.

Es similar al servicio de correos realiza letras, Bus de servicio de entrega de información flexible desde el remitente y el destinatario. El servicio de mensajería garantiza que la información se entregue incluso si las dos partes nunca ambas en línea al mismo tiempo, o si no están disponibles al mismo tiempo. De este modo, la mensajería es similar a enviar la carta, mientras no negociada comunicación es similar a realizar una llamada de teléfono (o cómo solía ser - antes de la llamada en espera e Id., que son mucho más como la mensajería con una llamada de teléfono).

El remitente del mensaje también puede requerir una gran variedad de características de entrega, incluyendo transacciones, detección de duplicados, expiración basada en el tiempo y por lotes. Estos patrones tienen también analogías postales: repita entrega, firma requerida, cambia la dirección o recuperar.

Bus de servicio compatible con dos patrones de mensajería distintas: *retransmisión* y *comerciales de mensajería*.

## <a name="service-bus-relay"></a>Retransmisión de Bus de servicio

El componente de [retransmisión](../service-bus-relay/service-bus-relay-overview.md) de Bus de servicio es un servicio centralizado (pero muy equilibrio de carga) que es compatible con una variedad de diferentes protocolos de transporte y estándares de servicios Web. Esto incluye SOAP, WS-* e incluso el resto. El [servicio de retransmisión](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) ofrece una variedad de opciones de conectividad de retransmisión diferente y puede ayudar a negociar conexiones de punto a punto directas cuando sea posible. Bus de servicio está optimizado para desarrolladores de .NET que usan Windows Communication Foundation (WCF), ambos con respecto al rendimiento y facilidad de uso y proporciona acceso completo a su servicio de retransmisión a través de interfaces SOAP y el resto. Esto posibilita SOAP o resto entorno de programación integrar con Bus de servicio de.

El servicio de retransmisión admite mensajería unidireccional tradicional, convocatoria y respuesta de mensajería y la mensajería de punto a punto. También admite la distribución de evento en el ámbito de Internet para habilitar publicación suscripción escenarios y comunicación de socket bidireccional para eficiencia de aumento de punto a punto. En el patrón de mensajería retransmitido un servicio local se conecta al servicio de retransmisión a través de un puerto de salida y crea un socket bidireccionales para la comunicación están vinculada a una dirección particular cita. El cliente, a continuación, puede comunicarse con el servicio de local mediante el envío de mensajes al servicio de retransmisión dirige a la dirección de la cita. El servicio de retransmisión, a continuación, se "relé" mensajes al servicio local a través de la toma de bidireccional ya. El cliente no tenga una conexión directa al servicio local ni es necesario saber donde reside el servicio y el servicio local no necesitan puertos entrantes abierto en el firewall.

Iniciar la conexión entre el servicio local y el servicio de retransmisión, mediante un conjunto de enlaces de retransmisión"WCF". En segundo plano, los enlaces de retransmisión asignan elementos de enlace diseñados para crear componentes del canal WCF que se integran con Bus de servicio en la nube de transporte.

Retransmisión de Bus de servicio ofrece muchas ventajas, pero requiere que el servidor y cliente tanto a estar en línea al mismo tiempo para poder enviar y recibir mensajes. Esto no es óptima para la comunicación de estilo de HTTP, en el que las solicitudes no pueden ser normalmente larga, ni para los clientes que se conectan sólo en ocasiones, como exploradores, aplicaciones móviles y así sucesivamente. Con la comunicación desacopladas es compatible con la mensajería y tiene sus propias ventajas; pueden conectar clientes y servidores cuando es necesario y realizar sus operaciones de forma asincrónica.

## <a name="brokered-messaging"></a>Negociada mensajería

A diferencia de la combinación de retransmisión [con mensajería](service-bus-queues-topics-subscriptions.md) puede considerar como asincrónica, o "temporalmente desacoplado". Productor (remitentes) y los consumidores (receptores) no tienen que estar en línea al mismo tiempo. La infraestructura de mensajería confiable almacena mensajes en un agente de"" (por ejemplo, una cola) hasta la parte que esté lista para recibirlas. Esto permite a los componentes de la aplicación distribuida desconectarán, ya sea voluntario; Por ejemplo, para realizar el mantenimiento o debido a un bloqueo de componente, sin que afecte a todo el sistema. Además, la aplicación receptora solo puede tener conectarse durante determinadas horas del día, como un sistema de administración de inventario solo es necesario para ejecutar al final del día de la empresa.

Los componentes principales de la infraestructura de mensajería con Bus de servicio son colas, temas y suscripciones.  La diferencia principal es que temas admiten capacidades de publicación o suscripción que se pueden usar para basado en contenido enrutamiento y la entrega lógica sofisticada, incluido el envío a varios destinatarios. Estos componentes permiten nuevos escenarios de mensajería asincrónicos, como disociación temporal, publicación o suscripción y equilibrio de carga. Para obtener más información acerca de estas entidades de mensajería, consulte [suscripciones, temas y colas de Bus de servicio](service-bus-queues-topics-subscriptions.md).

Como ocurre con la infraestructura de retransmisión, se proporciona la capacidad de mensajería con para programadores de WCF y .NET Framework y también a través del resto.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la mensajería de Bus de servicio, vea los temas siguientes.

- [Conceptos básicos de Bus de servicio](service-bus-fundamentals-hybrid-solutions.md)
- [Suscripciones, temas y colas de Bus de servicio](service-bus-queues-topics-subscriptions.md)
- [Cómo usar colas de Bus de servicio](service-bus-dotnet-get-started-with-queues.md)
- [Cómo utilizar las suscripciones y temas de Bus de servicio](./service-bus-dotnet-how-to-use-topics-subscriptions.md)
 
