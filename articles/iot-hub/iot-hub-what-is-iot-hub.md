<properties
 pageTitle="Información general de concentrador IoT Azure | Microsoft Azure"
 description="Información general del servicio de Azure IoT concentrador: ¿qué es iot concentrador, conectividad de dispositivos, internet de patrones de comunicación cosas y patrón de comunicación de servicio de asistencia"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>

# <a name="what-is-azure-iot-hub"></a>¿Qué es Azure IoT concentrador?

Bienvenido a Azure concentrador IoT. Este artículo proporciona una descripción general de Azure IoT concentrador y describe por qué debería usar este servicio para implementar una solución de Internet de cosas (IoT). Hub de Azure IoT es un servicio totalmente gestionado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos IoT y back-end una solución. Hub de Azure IoT:

- Proporciona confiable dispositivo a la nube y mensajería a escala de nube al dispositivo.
- Permite proteger las comunicaciones con las credenciales de seguridad por dispositivo y control de acceso.
- Proporciona la supervisión extensiva para conectividad de dispositivos y eventos de administración de identidades de dispositivo.
- Incluye bibliotecas de dispositivo para los idiomas y plataformas más populares.

El artículo de la [comparación de IoT concentrador y Hubs de evento] [ lnk-compare] describe las diferencias clave entre estos dos servicios y se resaltan las ventajas de usar IoT concentrador en sus soluciones IoT.

![Azure concentrador IoT como puerta de enlace de nube en internet de solución de cosas][img-architecture]

> [AZURE.NOTE] Para obtener información detallada de la arquitectura de IoT, consulte la [Arquitectura de referencia de Microsoft Azure IoT][lnk-refarch].

## <a name="iot-device-connectivity-challenges"></a>Retos de la conectividad de dispositivos de IoT

Concentrador IoT y las bibliotecas de dispositivo le ayudan a satisfacer los desafíos de cómo fiable y segura conectar dispositivos al back-end solución. IoT dispositivos:

- A menudo son sistemas incrustados sin operador humana.
- Puede estar en ubicaciones remotas, donde el acceso físico es caro.
- Sólo pueden ser accesibles a través de back-end solución.
- Puede tienen limitada power y recursos de procesamiento.
- Puede tener conectividad de red intermitentes, lenta o cara.
- Necesite utilizar protocolos de aplicaciones de propiedad, personalizado o específicas de la industria.
- Se pueden crear con un amplio conjunto de plataformas populares de hardware y software.

Además de los requisitos anteriores, cualquier solución IoT también debe entregar escala, seguridad y confiabilidad. El conjunto de requisitos de conectividad resultante es difícil y lento implementar al usar tecnologías tradicionales, como los contenedores de web y mensajería agentes.

## <a name="why-use-azure-iot-hub"></a>¿Por qué usar Azure IoT concentrador

Hub de Azure IoT trata los desafíos de conectividad de dispositivos de las siguientes maneras:

-   **Por dispositivo autenticación y conectividad segura**. Aprovisionamiento de cada dispositivo con su propia [clave de seguridad] [ lnk-devguide-security] para que pueda conectarse a IoT concentrador. El [registro de la identidad de concentrador IoT] [ lnk-devguide-identityregistry] almacena las identidades de dispositivo y las teclas en una solución. Un back-end de solución puede agregar dispositivos individuales para permitir o denegar listas para habilitar un control completo sobre el acceso a los dispositivos.

-   **Supervisión de las operaciones de conectividad del dispositivo**. Puede recibir registros de operación detallada acerca de las operaciones de administración de identidades de dispositivo y eventos de conectividad del dispositivo. Esta capacidad de supervisión permite la solución IoT identificar problemas de conectividad, como dispositivos que intenta conectar con credenciales incorrectos, enviar mensajes con demasiada frecuencia o rechazar todos los mensajes de la nube al dispositivo.

-   **Un amplio conjunto de bibliotecas de dispositivos**. [Dispositivo de IoT Azure SDK] [ lnk-device-sdks] están disponibles y compatibles para distintos idiomas y plataformas--C para muchas distribuciones de Linux, Windows y sistemas operativos en tiempo real. SDK de dispositivos de Azure IoT también admite lenguajes administrados, como C#, Java y JavaScript.

-   **Capacidad de ampliación y IoT protocolos**. Si su solución no puede usar las bibliotecas de dispositivo, concentrador IoT expone un protocolo público que permite dispositivos para usar la MQTT v3.1.1, HTTP 1.1 o 1.0 AMQP protocolos de forma nativa. También puede ampliar IoT concentrador para proporcionar soporte técnico para los protocolos personalizados por:

    - Creación de una puerta de enlace de campo con el [SDK de puerta de enlace de Azure IoT] [ lnk-gateway-sdk] que convierte el protocolo personalizado a uno de los tres protocolos entendidos por IoT concentrador. 
    - Personalización de la [puerta de enlace de protocolo de Azure IoT][protocol-gateway], un componente de código abierto que se ejecuta en la nube.

-   **Escala**. Hub de Azure IoT escala a millones de dispositivos conectados simultáneamente y millones de eventos por segundo.

Estos beneficios son genéricos para varios patrones de comunicación. Concentrador IoT actualmente le permite implementar los patrones de comunicación específicas siguientes:

-   **Recopilación de nube de dispositivo basado en eventos.** Concentrador IoT confiable puede recibir millones de eventos por segundo desde sus dispositivos. A continuación, puede procesar en la ruta de acceso rápida usando un motor de procesador de evento. También pueden almacenar en la ruta de acceso y para el análisis. Concentrador IoT conserva los datos del evento hasta siete días para garantizar un procesamiento confiable y absorber picos en la carga.

-   * *Mensajería confiable de dispositivo de nube (o *comandos*). ** back-end solución puede usar IoT concentrador para enviar mensajes con una garantía de entrega en-menos una vez a dispositivos individuales. Cada mensaje tiene una configuración time to live individual y el back-end puede solicitar confirmaciones de entrega y vencimiento. Estas confirmaciones garantizan visibilidad completa del ciclo de vida de un mensaje de nube al dispositivo. A continuación, puede implementar la lógica empresarial que incluye las operaciones que se ejecutan en dispositivos.

-   **Cargar archivos y los datos almacenados en caché sensor a la nube.** Los dispositivos pueden cargar archivos al almacenamiento de Azure usando URI SA gestionado por IoT concentrador. IoT concentrador puede generar notificaciones cuando llegan la nube para habilitar el back-end procesar archivos.

## <a name="gateways"></a>Puertas de enlace

Una puerta de enlace en una solución IoT suele ser una [puerta de enlace de protocolo] [ lnk-gateway] que se implementa en la nube o una [puerta de enlace de campo] [ lnk-field-gateway] que se implementa localmente con sus dispositivos. Una puerta de enlace de protocolo realiza traducción de protocolo, por ejemplo MQTT a AMQP. Una puerta de enlace de campo puede ejecutar el análisis en el borde, tomar decisiones urgentes para reducir la latencia, proporcionar servicios de administración de dispositivos, exigir restricciones de privacidad y seguridad y también realizar traducción de protocolo. Ambos tipos de puerta de enlace actúan como intermediarios entre los dispositivos y su centro IoT.

Una puerta de enlace de campo difiere de un dispositivo de enrutamiento de tráfico simple (por ejemplo, un dispositivo de traducción de direcciones de red o firewall) porque normalmente realiza una función activa en la administración de flujo de información y acceso en su solución.

Una solución puede incluir las puertas de enlace de protocolo y el campo.

## <a name="how-does-iot-hub-work"></a>¿Cómo funciona IoT concentrador?

Hub de Azure IoT implementa la [comunicación de servicio de asistencia] [ lnk-service-assisted-pattern] trama para mediar las interacciones entre los dispositivos y back-end solución. El objetivo de comunicación de servicio de asistencia consiste en establecer fidedigna, rutas de comunicación bidireccional entre un sistema de control, como concentrador IoT y dispositivos de usos especiales que se implementan en espacio físico no es de confianza. El modelo establece los principios siguientes:

- Seguridad tiene prioridad sobre todas las otras funciones.
- Dispositivos no aceptan la información de red no solicitado. Un dispositivo establece todas las conexiones y rutas de forma sólo saliente. Para que un dispositivo recibir un comando de back-end, el dispositivo regularmente debe iniciar una conexión para comprobar si los comandos pendientes a proceso.
- Dispositivos solo deben conectarse a o establecer rutas a servicios conocidos que se peered, como concentrador IoT.
- La ruta de comunicación entre el dispositivo y servicio o entre dispositivos y la puerta de enlace es segura en el nivel de protocolo de aplicación.
- Autenticación y la autorización de nivel de sistema se basan en las identidades por dispositivo. Hacen que sea credenciales de acceso y permisos revocable casi al instante.
- Comunicación bidireccional para dispositivos que se conectan esporádicamente debido a la conectividad o power preocupaciones es más fácil manteniendo comandos y las notificaciones del dispositivo hasta que se conecte un dispositivo para recibirlas. Concentrador IoT mantiene colas específicas del dispositivo para los comandos que se envía.
- Datos de carga de la aplicación está protegidos por separado para tránsito protegida a través de puertas de enlace a un servicio en particular.

El sector móvil ha utilizado el patrón de comunicación de servicio de asistencia a gran escala para implementar los servicios de notificación de inserción, como [Servicios de notificación de inserción de Windows][lnk-wns], [Mensajería de nube de Google][lnk-google-messaging], [Servicio de notificación de inserción de Apple]y[lnk-apple-push].

Concentrador IoT es compatible en ruta de interconexión pública de ExpressRoute.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo Azure IoT concentrador permite basada en estándares de administración de dispositivos de IoT le permiten administrar, configurar y actualizar sus dispositivos de forma remota, consulte [administración de dispositivos de información general de Azure IoT concentrador][lnk-device-management].

Para implementar aplicaciones cliente en una amplia variedad de sistemas operativos y plataformas de hardware de dispositivo, puede usar el dispositivo IoT SDK. El dispositivo IoT SDK incluye bibliotecas que facilitan el envío telemetría a un concentrador IoT y recibir comandos de dispositivo de nube. Al usar el SDK, puede elegir desde distintos protocolos de red para comunicarse con IoT concentrador. Para obtener más información, vea la [información sobre el dispositivo SDK][lnk-device-sdks].

Para empezar a escribir código y ejecutar algunos ejemplos, consulte la [Introducción a IoT concentrador] [ lnk-get-started] tutorial.

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicación asistido del servicio, entrada de blog de Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md
