<properties
    pageTitle="Información general de retransmisión de Bus de servicio | Microsoft Azure"
    description="Descripción general de retransmisión de Bus de servicio."
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
    ms.date="09/01/2016"
    ms.author="sethm"/>


# <a name="overview-of-service-bus-relay"></a>Información general de retransmisión de Bus de servicio

Un componente principal de Bus de servicio es un servicio de *retransmisión* de centralizada (pero muy equilibrio de carga) que le permite crear aplicaciones híbridas que se ejecutan en un centro de datos de Azure y su propio entorno local de la empresa.  La retransmisión de Bus de servicio compatible con una variedad de diferentes protocolos de transporte y estándares de servicios web. Esto incluye SOAP, WS-* e incluso el resto. El servicio de retransmisión facilita sus aplicaciones híbridas habilitando exponer de forma segura servicios Windows Communication Foundation (WCF) que se encuentran dentro de una red corporativa enterprise en la nube pública, sin tener que abrir una conexión del firewall, o requerirán cambios intrusivos en una infraestructura de red corporativa. 

![Conceptos de retransmisión](./media/service-bus-relay-overview/sb-relay-01.png)

El servicio de retransmisión admite mensajería unidireccional tradicional, convocatoria y respuesta de mensajería y la mensajería de punto a punto. También es compatible con distribución de evento en el ámbito de internet para habilitar la comunicación bidireccional socket para eficiencia de aumento de punto a punto y escenarios de publicación o suscripción. 

En el patrón de mensajería retransmitido un servicio local se conecta al servicio de retransmisión a través de un puerto de salida y crea un socket bidireccionales para la comunicación están vinculada a una dirección particular cita. El cliente, a continuación, puede comunicarse con el servicio de local mediante el envío de mensajes al servicio de retransmisión dirige a la dirección de la cita. El servicio de retransmisión, a continuación, se "relé" mensajes al servicio local a través de la toma de bidireccional ya. El cliente no necesita una conexión directa al servicio local, no es necesario saber donde reside el servicio y el servicio local no necesitan puertos entrantes abierto en el firewall.

Iniciar la conexión entre su servicio local y el servicio de retransmisión mediante un conjunto de enlaces de retransmisión"WCF". En segundo plano, los enlaces de retransmisión asignan a nuevos elementos de enlace de transporte diseñados para crear componentes del canal WCF que se integran con Bus de servicio en la nube. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de la retransmisión de Bus de servicio, vea los temas siguientes.

- [Información general de arquitectura de Bus de servicio de Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Cómo usar el servicio de retransmisión de Bus de servicio](service-bus-dotnet-how-to-use-relay.md)

 