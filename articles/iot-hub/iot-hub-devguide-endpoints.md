<properties
 pageTitle="Guía de programador - extremos de concentrador IoT | Microsoft Azure"
 description="Guía de programador de Azure IoT concentrador - información de referencia sobre los extremos de concentrador IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="reference---iot-hub-endpoints"></a>Referencia: extremos de concentrador IoT

## <a name="list-of-iot-hub-endpoints"></a>Lista de extremos de concentrador IoT

Hub de Azure IoT es un servicio de múltiples arrendatario que expone su funcionalidad a varios actores. El diagrama siguiente muestra los distintos extremos que expone IoT concentrador.

![Extremos de concentrador IoT][img-endpoints]

La siguiente es una descripción de los extremos.

* **Proveedor de recursos**. El proveedor de recursos de concentrador IoT expone un [Administrador de recursos de Azure] [ lnk-arm] interfaz que permite a los propietarios de Azure suscripción crear y eliminar IoT hubs y actualizar las propiedades de concentrador IoT. Propiedades de concentrador IoT controlan [las directivas de seguridad a nivel de concentrador][lnk-accesscontrol], en lugar de control de acceso de dispositivo y opciones funcionales para mensajería nube al dispositivo y el dispositivo a la nube. El proveedor de recursos de concentrador IoT también le permite [Exportar identidades de dispositivo][lnk-importexport].
* **Administración de identidades de dispositivo**. Cada concentrador IoT expone un conjunto de puntos finales de HTTP resto para administrar las identidades de dispositivo (crear, recuperar, actualizar y eliminar). [Las identidades de dispositivo] [ lnk-device-identities] se usan para dispositivo autenticación y control de acceso.
* **Administración de dispositivos de doble**. Cada concentrador IoT expone un conjunto de extremo de HTTP resto servicio orientación de la consulta y actualizar [twins dispositivo] [ lnk-twins] (Actualizar etiquetas y propiedades).
* **Administración de trabajos**. Cada concentrador IoT expone un conjunto de extremo de HTTP resto de orientación de servicio para consultar y administrar [trabajos][lnk-jobs].
* **Extremos de dispositivo**. Para cada dispositivo configurado en el registro de la identidad de dispositivo, concentrador IoT expone un conjunto de extremos que puede usar un dispositivo para enviar y recibir mensajes:
    - *Enviar mensajes de dispositivo a la nube*. Use este extremo para [enviar mensajes de la nube de dispositivo][lnk-d2c].
    - *Recibir mensajes de nube al dispositivo*. Un dispositivo usa este extremo para recibir [mensajes de dispositivo de nube]de destino[lnk-c2d].
    - *Iniciar la carga de archivos*. Un dispositivo usa este extremo para recibir un URI de Azure almacenamiento SA IoT concentrador de cargar [un archivo][lnk-upload].
    - *Recuperar y propiedades de actualización doble*. Un dispositivo usa esta extremos para tener acceso a su [doble dispositivo][lnk-twins]de propiedades.
    - *Las solicitudes de métodos directos de recepción*. Un dispositivo usa esta extremos para escuchar [métodos directos][lnk-methods]de solicitudes.

    Estos extremos se exponen con [v3.1.1 MQTT][lnk-mqtt], HTTP 1.1 y [1.0 AMQP] [ lnk-amqp] protocolos. Tenga en cuenta que AMQP también está disponible en [WebSockets] [ lnk-websockets] en el puerto 443.
    
    Endpoins las twins' y métodos están disponibles solo con [v3.1.1 MQTT][lnk-mqtt].

* **Extremos de servicio**. Cada concentrador IoT expone un conjunto de puntos finales de su servidor de aplicaciones que puede usar para comunicarse con los dispositivos. Los extremos son actualmente solo expuesto utilizando la [AMQP] [ lnk-amqp] protocolo, excepto el extremo de invocación de método que se expone a través de HTTP 1.1.
    - *Recibir mensajes de dispositivo a la nube*. Este extremo es compatible con [Azure evento Hubs][lnk-event-hubs]. Un servicio de back-end puede usarlo para leer todos los [mensajes de la nube de dispositivo] [ lnk-d2c] enviados por los dispositivos.
    - *Enviar mensajes de dispositivo de nube y recibir confirmaciones de entrega*. Estos extremos habilitar el servidor de aplicaciones enviar confiables [mensajes de dispositivo de nube][lnk-c2d]y para recibir la entrega correspondiente o confirmaciones de expiración.
    - *Recibir notificaciones de archivo*. Este extremo de mensajería le permite recibir notificaciones de cuando los dispositivos correctamente cargar un archivo. 
    - *Invocación de método directo*. Este extremo permite a invocar un [método directo] de un servicio de back-end[ lnk-methods] en un dispositivo.

[API de concentrador IoT y SDK] [ lnk-sdks] artículo describen las distintas maneras de obtener acceso a estos extremos.

Por último, es importante que recuerde que todos los extremos de concentrador IoT utilizan [TLS] [ lnk-tls] protocolo y ningún extremo nunca se expone en canales sin cifrar/no segura.

## <a name="field-gateways"></a>Puertas de enlace de campo

En una solución IoT, una *puerta de enlace de campo* se coloca entre los dispositivos y los extremos IoT concentrador. Es normalmente ubicado cerca de sus dispositivos. Los dispositivos comunican directamente con la puerta de enlace de campo con un protocolo compatible con los dispositivos. La puerta de enlace de campo se conecta a un extremo de concentrador IoT con un protocolo que es compatible con IoT concentrador. Una puerta de enlace de campo puede ser hardware muy especializado o un equipo de energía baja de software que lleva a cabo el escenario to-end que va dirigido la puerta de enlace.

Puede usar el [SDK de puerta de enlace de Azure IoT] [ lnk-gateway-sdk] implementar una puerta de enlace de campo. Este SDK ofrece funcionalidades específicas, como la capacidad de multiplexar comunicación desde varios dispositivos en la misma conexión a IoT concentrador.

## <a name="next-steps"></a>Pasos siguientes

Otros temas de referencia en esta guía para programadores de concentrador IoT incluyen:

- [Lenguaje de consulta para gemelos, métodos y trabajos][lnk-devguide-query]
- [Cuotas y límite][lnk-devguide-quotas]
- [Soporte técnico de IoT concentrador MQTT][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md