<properties
    pageTitle="¿Qué es la retransmisión de Azure? | Microsoft Azure"
    description="Información general de retransmisión de Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="banisadr"
    manager="timlt"
    editor="" />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="babanisa" />

# <a name="what-is-azure-relay"></a>¿Qué es la retransmisión de Azure?

Servicio de retransmisión de Azure facilita sus aplicaciones híbridas habilitando exponer de forma segura servicios que se encuentran dentro de una red corporativa enterprise en la nube pública, sin tener que abrir una conexión del firewall, o requerirán cambios intrusivos en una infraestructura de red corporativa. Retransmisión es compatible con una variedad de diferentes protocolos de transporte y estándares de servicios web.

Es compatible con el servicio de retransmisión tradicional unidireccional convocatoria y respuesta y tráfico de punto a punto. También es compatible con distribución de evento en el ámbito de internet para habilitar la comunicación bidireccional socket para eficiencia de aumento de punto a punto y escenarios de publicación o suscripción. 

En el modelo de transferencia de datos retransmitidos, un servicio local se conecta al servicio de retransmisión a través de un puerto de salida y crea un socket bidireccionales para la comunicación están vinculada a una dirección particular cita. El cliente, a continuación, puede comunicarse con el servicio de local mediante el envío de tráfico al servicio de retransmisión dirige a la dirección de la cita. El servicio de retransmisión, a continuación, se "relé" datos al servicio local a través de un socket bidireccional dedicado a cada cliente. El cliente no necesita una conexión directa al servicio local, no es necesario saber donde reside el servicio y el servicio local no necesitan puertos entrantes abierto en el firewall.

Los elementos de capacidad clave proporcionados por relé son comunicación bidireccional, sin búfer a través de los límites de red con TCP como limitación, la detección de extremo, el estado de conectividad y superponen de seguridad de extremo. Capacidades de retransmisión diferencian de las tecnologías de integración de nivel de red como VPN en que puede ámbito un extremo de aplicación única en un único equipo, mientras que la tecnología VPN es mucho más intrusiva mientras se basa en alterar el entorno de red.

Retransmisión de Azure tiene dos funciones:

1. [Conexiones de híbrido](#hybrid-connections) - utiliza Sockets estándar Web abiertos habilitación de escenarios de varias plataformas

2. [Transmisiones de WCF](#wcf-relays) - usa Windows Communication Foundation para habilitar las llamadas de procedimiento remoto

Conexiones de híbrido y transmisiones de WCF habilitar una conexión segura a assests que hay dentro de una red empresarial corporativa. Uso de una encima de la otra depende de sus necesidades particulares descritos a continuación:

|                                    | Retransmisión WCF | Conexiones de híbrido |
| ---------------------------------- |:---------:|:------------------:|
| **WCF**                            |     x     |                    |
| **Núcleo de .NET**                      |           |         x          |
| **.NET framework**                 |     x     |         x          |
| **JavaScript/NodeJS**              |           |         x          |
| **Java***                          |           |         x          |
| **Protocolo abierto basado en estándares**  |           |         x          |
| **Varios modelos de Programing RPC** |           |         x          |
*<sub>Por disponibilidad General</sub>

## <a name="hybrid-connections"></a>Conexiones de híbrido

Conexiones de híbrido de Azure retransmisión capacidad es una evolución segura, abrir protocolo de las características de retransmisión existentes que se puede implementar en cualquier plataforma y en cualquier idioma que tiene una capacidad de WebSocket básica, que incluya explícitamente la API WebSocket en común exploradores web. Conexiones de híbrido se basa en HTTP y WebSockets.

## <a name="wcf-relays"></a>Transmisiones WCF

La retransmisión de WCF funciona para el marco de .NET completa (NETFX) y WCF. Iniciar la conexión entre su servicio local y el servicio de retransmisión mediante un conjunto de enlaces de retransmisión"WCF". En segundo plano, los enlaces de retransmisión asignan a nuevos elementos de enlace de transporte diseñados para crear componentes del canal WCF que se integran con Bus de servicio en la nube.

## <a name="service-history"></a>Historial de servicio

Conexiones de híbrido trasplantes la primera, igualmente denominado característica "BizTalk Services" que se creó en la retransmisión de WCF de Bus de servicio de Azure. La nueva capacidad de conexiones híbrido complementa la retransmisión de WCF existente y estas capacidades de dos servicio habrá en paralelo en el servicio de retransmisión en el futuro; están compartir una puerta de enlace común, pero que lo contrario implementaciones diferentes.

Retransmisión de WCF es la retransmisión heredada ofreciendo que muchos clientes ya pueden usar con sus modelos WCF programing.

## <a name="next-steps"></a>Pasos siguientes:

- [Retransmisión de preguntas más frecuentes](relay-faq.md)
- [Crear un espacio de nombres](relay-create-namespace-portal.md)
- [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Introducción a nodo](relay-hybrid-connections-node-get-started.md)