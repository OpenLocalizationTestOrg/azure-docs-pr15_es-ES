<properties 
    pageTitle="Información general del servicio Bus AMQP | Microsoft Azure" 
    description="Obtenga información sobre el uso de la avanzada mensaje Queue protocolo (AMQP) 1.0 en Azure." 
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
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sethm"/>



# <a name="amqp-10-support-in-service-bus"></a>Soporte técnico de AMQP 1.0 de Bus de servicio

El servicio de nube de Bus de servicio de Azure y el local la avanzada mensaje cola protocolo (AMQP) 1.0 de soporte técnico de [Bus de servicio de Windows Server (1.1 de Bus de servicio)](https://msdn.microsoft.com/library/dn282144.aspx) . AMQP le permite crear aplicaciones híbridas con un protocolo estándar abierto entre plataformas. Puede crear aplicaciones que utilizan componentes que se generan con diferentes idiomas y marcos y que se ejecutan en sistemas operativos diferentes. Todos estos componentes pueden conectarse al servicio Bus y sin problemas intercambian mensajes de negocios estructurados eficaz y a plena fidelidad.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introducción: ¿Qué es AMQP 1.0 y por qué es importante?

Tradicionalmente, los productos de software intermedio orientado a mensajes han utilizado protocolos propietarios para la comunicación entre las aplicaciones cliente y agentes. Esto significa que, una vez que haya seleccionado a agente de mensajería de un proveedor determinado, debe utilizar bibliotecas del proveedor para conectar las aplicaciones cliente a ese agente. Resultados en un grado de dependencia del proveedor, ya que trasladar una aplicación a otro producto requiere cambios de código en todas las aplicaciones conectadas. 

Además, es difícil conectar mensajería agentes de diferentes proveedores. Normalmente, esto requiere nivel de aplicación puente para mover los mensajes de un sistema a otro y para traducir entre los formatos de mensaje propietarios. Se trata de un requisito común; Por ejemplo, cuando debe proporcionar una nueva interfaz unificada anteriores a sistemas distintos o integrar siguiendo una fusión de sistemas de TI.

El sector de software es una empresa rápida evolución; se presentan nuevos lenguajes de programación y marcos de aplicación a un ritmo a veces desconcertante. Asimismo, evolucionan los requisitos de los sistemas de TI y los desarrolladores desean sacar partido de las características más recientes de la plataforma. Sin embargo, a veces el proveedor de mensajería seleccionado no admite estas plataformas. Dado que pertenecen los protocolos de mensajería, no es posible para que otras personas proporciona bibliotecas para estas nuevas plataformas. Por lo tanto, debe utilizar otros enfoques como crear puertas de enlace o puentes que le permite seguir usando el producto de mensajería.

El desarrollo de la avanzada mensaje Queue protocolo (AMQP) 1.0 fue motivado por estos problemas. Se ha originado en JP Morgan Chase, que, como las empresas de servicios financieras más usuarios pesados de software intermedio orientado a mensajes. El objetivo era sencillo: crear un protocolo de mensajería estándar abierto que permite crear aplicaciones basadas en el mensaje con componentes creados con diferentes idiomas, marcos y sistemas operativos, todo utilizando el mejor de componentes de un rango de proveedores.

## <a name="amqp-10-technical-features"></a>Características técnicas de AMQP 1.0

AMQP 1.0 es un protocolo de mensajería eficaz, confiable nivel de alambre que puede usar para generar sólidos, en varias plataformas, aplicaciones de mensajería. El protocolo tiene un objetivo simple: definir la mecánica de la transferencia de mensajes segura, confiable y eficaz entre dos partes. Los mensajes están codificados mediante una representación de datos portátil que habilita los receptores y remitentes heterogéneos intercambiar mensajes de negocios estructurada en fidelidad completa. A continuación se muestra un resumen de las características más importantes:

*    **Eficiente**: AMQP 1.0 está orientada a una conexión de protocolo que usa una codificación binaria para las instrucciones de protocolo y los mensajes de empresa transfiere a través de él. Incorpora combinaciones de control de flujo sofisticados maximizar la utilización de la red y los componentes conectados. Dicho esto, el protocolo se ha diseñado para equilibrar la eficiencia, flexibilidad e interoperabilidad.
*    **Confiabilidad**: 1.0 de AMQP el protocolo permite intercambiar exactamente con un rango de garantías de confiabilidad, desde fire y olvidar a confiable, mensajes-una vez confirmado entrega.
*    **Flexible**: AMQP 1.0 es un protocolo flexible que puede usar para admitir topologías diferentes. El mismo protocolo puede utilizarse para las comunicaciones de cliente a cliente, agente de cliente y agente de agente.
*    **Modelo de agente independiente**: especificación la 1.0 AMQP no convierte los requisitos en el modelo de mensajería utilizado por un agente. Esto significa que es posible agregar fácilmente compatibilidad con AMQP 1.0 a agentes de mensajería existentes.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 es un estándar (con una mayúscula ')

AMQP 1.0 es un estándar internacional, aprobados por ISO y IEC como ISO/IEC 19464:2014.

AMQP 1.0 ha estado en desarrollo desde 2008 de un grupo de más de 20 compañías, proveedores de tecnología y empresas de usuario final. Durante ese período, empresas de usuario han contribuido sus requisitos empresariales reales y los proveedores de tecnología han desarrollado a partir del protocolo para satisfacer dichos requisitos. Durante el proceso, proveedores han participado en talleres en el que colabora para validar la interoperabilidad entre sus implementaciones.

En octubre de 2011, el trabajo de desarrollo transición a un Comité técnico dentro de la organización para el avance de estructuradas información estándares OASIS () y el estándar 1.0 de OASIS AMQP se publicó en octubre de 2012. Las siguientes empresas ha participado en el Comité técnico durante el desarrollo del estándar:

*    **Proveedores de tecnología**: Axway Software, Huawei tecnologías de Haberla Software, INETCO sistemas, Kaazing, Microsoft, Mitre Corporation, Primeton tecnologías, el progreso de Software, rojo sombrero, SITA, Software AG, Solace sistemas, VMware, WSO2, Zenika.
*    **Empresas de usuario**: banco de América, Suiza de crédito, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Algunas de las ventajas de estándares abiertos comúnmente citas son:

*    Menos posibilidades de bloqueo del proveedor
*    Interoperabilidad
*    Amplia disponibilidad de herramientas y bibliotecas
*    Protección contra vuelva obsoleta
*    Disponibilidad de personal especializado
*    Riesgo inferior y fácil de administrar

## <a name="amqp-10-and-service-bus"></a>Bus de servicio y 1,0 AMQP

Compatibilidad de AMQP 1.0 de Bus de servicio de Azure significa que ahora puede aprovechar la cola de Bus de servicio y de publicación o suscripción con características de mensajería de un intervalo de plataformas con un protocolo binario eficaz. Además, puede crear aplicaciones conformadas de los componentes creados mediante una combinación de idiomas, marcos y sistemas operativos.

La figura siguiente muestra una implementación de ejemplo en el que los clientes de Java que se ejecutan en Linux, escrita mediante el estándar Java Message Service (JMS) API y clientes de .NET que se ejecutan en Windows, intercambian mensajes a través de Bus de servicio mediante AMQP 1.0.

![][0]

**Figura 1: Escenario de implementación de ejemplo que muestra la mensajería con Bus de servicio y AMQP 1.0 entre plataformas**

En este momento se conocen las siguientes bibliotecas de cliente para que funcione con Bus de servicio:

| Idioma | Biblioteca                                                                       |
|----------|-------------------------------------------------------------------------------|
| Java     | Cliente de Apache Qpid Java Message Service (JMS)<br/>Cliente de Haberla Software SwiftMQ Java |
| C        | Apache Qpid protones-C                                                          |
| PHP      | Apache Qpid protones-PHP                                                        |
| Python   | Apache de Qpid protones Python                                                     |
| C#       | AMQP .net interacción                                                                |

**Figura 2: Tabla de bibliotecas de cliente de AMQP 1.0**

## <a name="summary"></a>Resumen

*    AMQP 1.0 es un protocolo de mensajería confiable abierto que puede usar para generar aplicaciones híbridas entre plataformas. AMQP 1.0 es un estándar OASIS.
*    Compatibilidad con AMQP 1.0 está ahora disponible en Azure Bus de servicio, así como Bus de servicio de Windows Server (1.1 de Bus de servicio). Precio es el mismo que para los protocolos existentes.

## <a name="next-steps"></a>Pasos siguientes

¿Listo para obtener más información? Visite los siguientes vínculos:

- [Uso de Bus de servicio de .NET con AMQP]
- [Uso de Bus de servicio de Java con AMQP]
- [Uso de Bus de servicio de Python con AMQP]
- [Uso de Bus de servicio de PHP con AMQP]
- [Instalar Apache Qpid protones-C en una máquina virtual Linux de Azure]
- [AMQP de Bus de servicio de Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Uso de Bus de servicio de .NET con AMQP]: service-bus-amqp-dotnet.md
[Uso de Bus de servicio de Java con AMQP]: service-bus-amqp-java.md
[Uso de Bus de servicio de Python con AMQP]: service-bus-amqp-python.md
[Uso de Bus de servicio de PHP con AMQP]: service-bus-amqp-php.md
[Instalar Apache Qpid protones-C en una máquina virtual Linux de Azure]: service-bus-amqp-apache.md
[AMQP de Bus de servicio de Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx