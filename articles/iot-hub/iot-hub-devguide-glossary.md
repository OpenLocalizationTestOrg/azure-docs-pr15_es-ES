<properties
 pageTitle="Guía de programador - glosario | Microsoft Azure"
 description="Un glosario de términos comunes de concentrador IoT"
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

# <a name="glossary-of-iot-hub-terms"></a>Glosario de términos de concentrador IoT

Este artículo enumeran los términos comunes asociados con IoT concentrador.

## <a name="advanced-message-queueing-protocol-amqp"></a>Protocolo de cola de mensajes avanzada (AMQP)

[AMQP](https://www.amqp.org/) es uno de los protocolos de mensajería que admita IoT concentrador para comunicarse con los dispositivos. Para obtener más información acerca de los protocolos de mensajería que admita IoT concentrador, vea [Enviar y recibir mensajes con IoT concentrador](iot-hub-devguide-messaging.md).

## <a name="cloud-to-device-c2d"></a>Nube de dispositivo (C2D)

Normalmente se utiliza para referirse a los mensajes enviados desde IoT concentrador en un dispositivo conectado. A menudo, estos mensajes son comandos que indican el dispositivo para tomar algunas medidas. Para obtener más información, vea [Enviar y recibir mensajes con IoT concentrador](iot-hub-devguide-messaging.md).

## <a name="condition"></a>Condición

Se refiere a información de estado del dispositivo, como el método de conectividad en uso, notificado por una aplicación de dispositivo. Dispositivos también pueden informar a sus funciones. Puede consultar la condición y la capacidad mediante el doble de dispositivo.

## <a name="data-point-message"></a>Mensaje de punto de datos

Un mensaje de punto de datos es un mensaje de dispositivo de nube que contiene los datos de telemetría como viento o la temperatura.

## <a name="desired-properties"></a>Propiedades que desee

En el contexto de twins del dispositivo, se usan propiedades que desee junto con *notificadas propiedades* para sincronizar la configuración de dispositivo o condición. Propiedades que desee solo se pueden establecer la aplicación volver final y se observan que la aplicación de dispositivo. 

## <a name="device-to-cloud-d2c"></a>Dispositivo de nube (D2C)

Normalmente se utiliza para referirse a los mensajes enviados desde un dispositivo conectado a concentrador IoT. Estos mensajes pueden mensajes de [punto de datos](#data-point-message) o [interactivas](#interactive-message) . Para obtener más información, vea [Enviar y recibir mensajes con IoT concentrador](iot-hub-devguide-messaging.md).

## <a name="device-identity-registry"></a>Registro de identidad de dispositivo

El [registro de la identidad de dispositivo](iot-hub-devguide-identity-registry.md) es el componente integrado de un concentrador de IoT que almacena información acerca de los dispositivos individuales pueda conectarse a un concentrador.

## <a name="device"></a>Dispositivo

En el contexto de IoT, un dispositivo suele ser un dispositivo informática independiente pequeños, que puede recopilar datos o controlar otros dispositivos. Por ejemplo un dispositivo puede ser un dispositivo de supervisión ambiental o un controlador para los sistemas de riego y ventilación en un efecto de invernadero.

## <a name="device-twin"></a>Doble de dispositivo

Un [doble de dispositivo](iot-hub-devguide-device-twins.md) es una copia en IoT Hub de la condición y opciones de configuración de un dispositivo físico. Puede usar a un doble dispositivo para administrar la configuración del dispositivo físico.

## <a name="direct-method"></a>Método directo

Un [método directo](iot-hub-devguide-direct-methods.md) es una manera de activar un método para ejecutar en un dispositivo mediante una llamada a una API en su centro IoT.

## <a name="event-hub-compatible-endpoint"></a>Extremo compatibles con el concentrador de evento

Para leer mensajes de la nube de dispositivo enviados a su centro IoT, puede conectarse a un punto final de su centro y efectuar cualquier evento concentrador compatible para leer los mensajes. Métodos compatibles con el concentrador de eventos incluyen utilizando el evento Hubs SDK y el análisis de secuencia de Azure.

## <a name="field-gateway"></a>Puerta de enlace de campo

Una puerta de enlace de campo permite la conectividad para dispositivos que no se puede conectar directamente a IoT concentrador y normalmente se implementa localmente con sus dispositivos. Para obtener más información, vea [¿Qué es Azure IoT concentrador?](iot-hub-what-is-iot-hub.md).

## <a name="job"></a>Trabajo

Back-end solución puede usar trabajos para programar y realizar un seguimiento de las actividades en un conjunto de dispositivos registrados con el concentrador IoT. Actividades incluyen actualizar propiedades doble deseado del dispositivo, actualizar etiquetas doble del dispositivo e invocar métodos directos.

## <a name="protocol-gateway"></a>Puerta de enlace de protocolo

Una puerta de enlace de protocolo normalmente se implementa en la nube y proporciona servicios de traducción para dispositivos conectados a IoT concentrador de protocolo. Para obtener más información, vea [¿Qué es Azure IoT concentrador?](iot-hub-what-is-iot-hub.md).

## <a name="interactive-message"></a>Mensaje interactivo

Un mensaje interactivo es un mensaje de dispositivo de nube que inicia una acción inmediata en el servidor de aplicaciones. Por ejemplo, un dispositivo puede enviar una alarma sobre un error que debe haber iniciado sesión automáticamente en un sistema CRM.

## <a name="iot-hub"></a>Concentrador IoT

IoT concentrador es un servicio de Azure totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos IoT y back-end una solución. Para obtener más información, vea [¿Qué es Azure IoT concentrador?](iot-hub-what-is-iot-hub.md).

## <a name="iot-suite"></a>IoT Suite

El conjunto de aplicaciones de Azure IoT empaqueta juntos varios servicios Azure con soluciones preconfigurados. Estas soluciones preconfiguradas que pueda empezar rápidamente con implementaciones de llevar a cabo de escenarios comunes de IoT. Para obtener más información, vea [¿Qué es el conjunto de aplicaciones de Azure IoT?](../iot-suite/iot-suite-overview.md).

## <a name="job"></a>Trabajo

Un [trabajo](iot-hub-devguide-jobs.md) en el IoT Hub permite realizar operaciones como un firmware actualización en varios dispositivos conectados a su centro.

## <a name="mqtt"></a>MQTT

[MQTT](http://mqtt.org/) es uno de los protocolos de mensajería que admita IoT concentrador para comunicarse con los dispositivos. Para obtener más información acerca de los protocolos de mensajería que admita IoT concentrador, vea [Enviar y recibir mensajes con IoT concentrador](iot-hub-devguide-messaging.md).

## <a name="reported-properties"></a>Propiedades del informes

En el contexto de twins del dispositivo, se usan propiedades notificados junto con las *propiedades que desee* sincronizar la configuración de dispositivo o condición. Propiedades notificados sólo se puede establecer por la aplicación del dispositivo y pueden leer y consultados por el servidor de aplicaciones.

## <a name="tags"></a>Etiquetas

En el contexto de devcie gemelos, etiquetas son dispositivo metadatos almacenados y recuperados por el servidor de aplicaciones en el formulario de un documento JSON. Etiquetas no son visibles para aplicaciones en un dispositivo.

## <a name="system-properties"></a>Propiedades del sistema

En el contexto de twins del dispositivo, propiedades del sistema son de solo lectura e incluyen información sobre el uso del dispositivo, como el último estado de conexión y tiempo de actividad.