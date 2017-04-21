<properties
 pageTitle="Temas de la guía para programadores de concentrador IoT | Microsoft Azure"
 description="Guía de programador IoT concentrador Azure que incluye extremos IoT concentrador, seguridad, registro de identidad de dispositivos, administración de dispositivos y mensajería"
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

# <a name="azure-iot-hub-developer-guide"></a>Guía de programador de Azure IoT concentrador

Hub de Azure IoT es un servicio totalmente gestionado que ayuda a habilitar a confiables y seguras comunicaciones bidireccional entre millones de dispositivos IoT y finalizar una aplicación de nuevo.

Hub de Azure IoT proporciona:

* Proteger las comunicaciones con las credenciales de seguridad por dispositivo y control de acceso.
* Dispositivo a la nube y dispositivo de nube hyper-escala mensajería confiable.
* Conectividad de dispositivo fácil con las bibliotecas de dispositivo para los idiomas y plataformas más populares.

Esta guía del programador de concentrador IoT incluye los siguientes artículos:

- [Enviar y recibir mensajes con concentrador IoT] [ devguide-messaging] describe las características de mensajería (dispositivo a la nube y dispositivo de nube) que expone IoT concentrador. El artículo también incluye información sobre temas como formatos de mensaje y los protocolos de comunicaciones compatibles y los números de puerto que se utilicen.
- [Cargar archivos desde un dispositivo] [ devguide-upload] describe cómo puede cargar archivos desde un dispositivo. El artículo también incluye información sobre temas como las notificaciones, puede enviar el proceso de uplaod.
- [Administrar identidades de dispositivo en el IoT Hub] [ devguide-identities] describe qué información de registro de identidad de dispositivo del concentrador de cada IoT almacena y cómo puede acceder y modificarlo.
- [Controlar el acceso a IoT concentrador] [ devguide-security] describe el modelo de seguridad que se utilizan para conceder acceso a la funcionalidad de IoT concentrador para ambos dispositivos y componentes de nube. El artículo incluye información sobre el uso de tokens y certificados X.509 y detalles de los permisos que puede conceder.
- [Utilizar twins dispositivo para sincronizar estado y configuraciones] [ devguide-device-twins] describe el concepto de *doble del dispositivo* y la funcionalidad que expone como sincronizar un dispositivo con su doble. El artículo incluye información sobre los datos almacenados en un doble del dispositivo.
- [Llamar a un método directo en un dispositivo] [ devguide-directmethods] describe el ciclo de vida de un método directo, información sobre cómo invocar métodos en un dispositivo de la aplicación de back-end y controlar el método directo en el dispositivo.
- [Programar trabajos en varios dispositivos] [ devguide-jobs] describe cómo puede programar trabajos en varios dispositivos. El artículo describe cómo enviar trabajos que realizan tareas como ejecutar un método directo, actualizar un devcie con un doble devcie. También se describe cómo consultar el estado de una tarea.
- [Referencia: extremos de concentrador IoT] [ devguide-endpoints] describe los diversos extremos que expone cada concentrador IoT para operaciones de administración y el tiempo de ejecución. El artículo también describe cómo puede usar una puerta de enlace de campo para habilitar algunos dispositivos para conectarse a los extremos de concentrador IoT.
- [Referencia: lenguaje de consulta para gemelos, métodos y trabajos] [ devguide-query] describe ese lenguaje de consulta que permite para recuperar la información de su centro sobre sus twins dispositivo y trabajos.
- [Referencia: las cuotas y límite de] [ devguide-quotas] resume las cuotas en el servicio de concentrador IoT y el comportamiento de limitación sucederá cuando superan la cuota.
- [Referencia: dispositivo y servicio SDK] [ devguide-sdks] listas los SDK puede usar desarrollan aplicaciones de dispositivos y servicios que interactúan con su centro IoT. El artículo incluye vínculos a la documentación de la API en línea.
- [Referencia: soporte técnico IoT concentrador MQTT] [ devguide-mqtt] proporciona información detallada sobre cómo IoT concentrador es compatible con el protocolo MQTT. El artículo describe la compatibilidad con el protocolo MQTT integrado para los SDK y proporciona información sobre cómo usar el protocolo MQTT directamente.
- [Glosario de] [ devguide-glossary] una lista de condiciones relacionadas con el concentrador IoT comunes.



[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md

