<properties
 pageTitle="Guía de programador - registro de identidad de dispositivo | Microsoft Azure"
 description="Guía de programador de Azure IoT concentrador - descripción del registro de identidad del dispositivo y cómo usarlo para administrar los dispositivos"
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

# <a name="manage-device-identities-in-iot-hub"></a>Administrar identidades de dispositivo en el IoT Hub

## <a name="overview"></a>Información general

Cada concentrador IoT tiene un registro de la identidad de dispositivo que almacena información acerca de los dispositivos que pueden conectarse al concentrador. Para que un dispositivo puedan conectarse a un concentrador, debe ser una entrada para ese dispositivo en registro de identidad de dispositivo del concentrador. Un dispositivo también debe autenticarse en el hub en función de las credenciales almacenadas en el registro de la identidad de dispositivo.

En un nivel alto, el registro de la identidad de dispositivo es una colección compatible con el resto de recursos de identidad del dispositivo. Al agregar una entrada en el registro, IoT concentrador crea un conjunto de recursos por dispositivo en el servicio como la cola que contiene los mensajes de dispositivo de nube en curso.

### <a name="when-to-use"></a>Cuándo usar

Usar el registro de la identidad de dispositivo cuando necesite aprovisionar dispositivos que se conectan a IoT concentrador y cuando es necesario controlar el acceso por dispositivo a los extremos de orientación de dispositivo en su centro.

> [AZURE.NOTE] Registro de identidad del dispositivo no contiene los metadatos específicos de la aplicación.

## <a name="device-identity-registry-operations"></a>Operaciones de registro de identidad de dispositivo

El registro de identidad de dispositivo concentrador IoT expone las siguientes operaciones:

* Crear la identidad del dispositivo
* Actualizar la identidad del dispositivo
* Recuperar la identidad del dispositivo por Id.
* Eliminar la identidad del dispositivo
* Lista de identidades hasta 1000
* Exportar todas las identidades al almacenamiento de blobs de Windows Azure
* Importar identidades de almacenamiento de blobs de Windows Azure

Todas estas operaciones pueden usar simultaneidad optimista, como especificado en [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] La única manera de recuperar todas las identidades de registro de identidad de un concentrador es usar la [exportación] [ lnk-export] funcionalidad.

Un registro de identidad de concentrador IoT dispositivos:

- No contiene los metadatos de la aplicación.
- Puede tener acceso como un diccionario, usando el **deviceId** como clave.
- No se admite consultas expresivas.

Una solución IoT normalmente tiene un almacén específica de la solución independiente que contiene los metadatos específicos de la aplicación. Por ejemplo, el almacén específica de la solución en una solución de creación inteligente registra el salón en el que se implementa un sensor de temperatura.

> [AZURE.IMPORTANT] Solo puede usar el registro de la identidad de dispositivo para operaciones de aprovisionamiento y administración de dispositivos. Operaciones de alto rendimiento en tiempo de ejecución no deben depender realizar operaciones en el registro de la identidad de dispositivo. Por ejemplo, la comprobación del estado de conexión de un dispositivo antes de enviar un comando no es un modelo compatible. Asegúrese de comprobar el [límite de tasas] [ lnk-quotas] para el registro de la identidad de dispositivo y el [latido de dispositivo] [ lnk-guidance-heartbeat] trama.

## <a name="disable-devices"></a>Deshabilitar dispositivos

Puede deshabilitar dispositivos actualizando la propiedad de **estado** de una identidad en el registro. Normalmente, utilice esta propiedad en dos escenarios:

- Durante el proceso de orquestación. Para obtener más información, vea el [Aprovisionamiento de dispositivos][lnk-guidance-provisioning].
- Si, por cualquier motivo, tenga en cuenta un dispositivo está comprometido o se ha convertido en no autorizado.

## <a name="import-and-export-device-identities"></a>Importar y exportar identidades de dispositivo

Puede exportar las identidades de los dispositivos de forma masiva desde el registro de la identidad de un concentrador IoT, con operaciones asíncronas en el [extremo del proveedor de recursos de concentrador IoT][lnk-endpoints]. Exportaciones son trabajos de larga ejecución que utilizan un contenedor de blob proporcionada por el cliente para guardar los datos de identidad de dispositivo leer desde el registro de la identidad.

Puede importar identidades de dispositivo en masa al registro de identidad de un concentrador IoT, con operaciones asíncronas en el [extremo del proveedor de recursos de concentrador IoT][lnk-endpoints]. Importaciones son trabajos de larga ejecución que use datos en un contenedor de blob proporcionada por el cliente para escribir los datos de identidad de dispositivo en el registro de la identidad de dispositivo.

- Para obtener información detallada sobre la importación y exportación API, vea el [proveedor de recursos de concentrador IoT API de REST][lnk-resource-provider-apis].
- Para obtener más información acerca de la importación y exportación de trabajos, vea [administración de masa de identidades de dispositivo concentrador IoT][lnk-bulk-identity].

## <a name="device-provisioning"></a>Aprovisionamiento de dispositivos

Los datos del dispositivo que almacena una solución IoT determinada dependen de los requisitos específicos de esa solución. Sin embargo, como mínimo, debe almacenar una solución identidades del dispositivo y claves de autenticación. Hub de Azure IoT incluye un registro de la identidad que puede almacenar valores para cada dispositivo como identificadores, las claves de autenticación y códigos de estado. Una solución puede usar otros servicios de Azure como almacenamiento de tablas Azure, almacenamiento de blobs de Windows Azure o DocumentDB de Azure para almacenar datos adicionales del dispositivo.

*Aprovisionamiento de dispositivos* es el proceso de agregar los datos del dispositivo inicial para la almacena en su solución. Para habilitar un nuevo dispositivo para conectarse a su centro, debe agregar un nuevo ID de dispositivo y las teclas en el registro de identidad IoT concentrador. Como parte del proceso de configuración, debe iniciar datos específicos del dispositivo en otros stores solución.

## <a name="device-heartbeat"></a>Latido de dispositivo

El registro de la identidad de concentrador IoT contiene un campo denominado **connectionState**. Solo debe usar el campo **connectionState** durante el desarrollo y depuración IoT soluciones no deben consultar el campo en tiempo de ejecución (por ejemplo, para comprobar si un dispositivo está conectado con el fin de decidir si desea enviar un mensaje de dispositivo de nube o un SMS).

Si su solución IoT necesita saber si un dispositivo está conectado (en tiempo de ejecución, o con más precisión que la propiedad **connectionState** proporciona), la solución debe implementar el *patrón de latido*.

En el modelo de latido, el dispositivo envía mensajes de nube de dispositivo al menos una vez cada cantidad fija de tiempo (por ejemplo, al menos una vez cada hora). Esto significa que incluso si un dispositivo no tiene ningún dato que envíe, aún envía un mensaje de nube de dispositivo vacío (normalmente con una propiedad que lo identifica como un latido). En el lado de servicio, la solución mantiene un mapa con el último latido recibido para cada dispositivo y se supone que hay un problema con un dispositivo si no recibe un mensaje de latido en el tiempo esperado.

Una implementación más compleja puede incluir la información de la [supervisión de operaciones] [ lnk-devguide-opmon] para identificar los dispositivos que están intentando conectarse o comunicarse pero con errores. Cuando se implementa el modelo de latido, asegúrese de comprobar [IoT concentrador cuotas y límites de][lnk-quotas].

> [AZURE.NOTE] Si una solución para IoT requiere el estado de conexión de dispositivo únicamente para determinar si se va a enviar mensajes de la nube para el dispositivo y no se emiten mensajes para grandes conjuntos de dispositivos, una trama mucho más sencilla para tener en cuenta es usar una fecha de caducidad corto. Consigue el mismo resultado que mantener un registro de estado de conexión de dispositivo utilizando el modelo de latidos, mientras se está mucho más eficaz. También es posible, al solicitar confirmaciones de mensajes, que se le notifique por IoT concentrador de los dispositivos que se pueda recibir mensajes y que no están en línea o erróneos.

## <a name="reference-topics"></a>Temas de referencia:

Los siguientes temas de referencia le proporciona más información acerca del registro de identidad devcie.

## <a name="device-identity-properties"></a>Propiedades de identidad de dispositivo

Las identidades de dispositivo están representadas como documentos JSON con las siguientes propiedades.

| (Propiedad) | Opciones | Descripción |
| -------- | ------- | ----------- |
| deviceId | es necesario, de sólo lectura en actualizaciones | Una cadena distingue mayúsculas de minúsculas (hasta 128 caracteres) de los caracteres alfanuméricos de ASCII 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | es necesario, sólo lectura | Cadena distingue mayúsculas de minúsculas, generados por el concentrador hasta 128 caracteres. Esto se usa para distinguir entre dispositivos con el mismo **deviceId**, cuando eliminados y vuelve a crear. |
| ETag | es necesario, sólo lectura | Una cadena que representa un poco etag para la identidad del dispositivo, según lo que [RFC7232][lnk-rfc7232].|
| AUTH | opcional | Un objeto compuesto que contiene los materiales de seguridad y la información de autenticación. |
| auth.symkey | opcional | Un objeto compuesto que contiene un principal y clave secundaria, se almacena en formato base 64. |
| estado | Obligatorio | Un indicador de acceso. Puede ser **habilitado** o **deshabilitado**. Si **habilitado**, el dispositivo se puede conectar. Si **deshabilitado**, este dispositivo no puede acceder a cualquier extremo de la orientación del dispositivo. |
| statusReason | opcional | Una cadena de caracteres de longitud 128 que almacena la razón para el estado de la identidad de dispositivo. Se admiten todos los caracteres de UTF-8. |
| statusUpdateTime | sólo lectura | Un indicador temporal, que muestra la fecha y hora de la última actualización de estado. |
| connectionState | sólo lectura | Un campo que indica el estado de conexión: **conectado** o **desconectado**. Este campo representa la vista de concentrador IoT del estado de conexión del dispositivo. **Importante**: este campo se debe utilizar únicamente para fines de desarrollo y depurar. El estado de conexión sólo se actualiza para dispositivos con MQTT o AMQP. Además, se basa en el nivel de protocolo ping (ping MQTT o AMQP ping) y puede tener un retraso máximo de 5 minutos solo. Para ello, puede haber falsas, como dispositivos notifican como conectado pero que realmente está desconectado. |
| connectionStateUpdatedTime | sólo lectura | Se ha actualizado un indicador temporal, que muestra la fecha y la última vez que el estado de conexión. |
| lastActivityTime  | sólo lectura | Un indicador temporal, que muestra la fecha y la última vez que el dispositivo conectado, recibido o enviado un mensaje. |

> [AZURE.NOTE] Estado de conexión sólo puede representar la vista IoT concentrador del estado de la conexión. Actualizaciones de este estado pueden retrasarse según las configuraciones y condiciones de la red.

## <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la Guía del programador incluyen:

- [Extremos de concentrador IoT] [ lnk-endpoints] describe los diversos extremos que expone cada concentrador IoT para operaciones de administración y el tiempo de ejecución.
- [Cuotas y regulación] [ lnk-quotas] describe las cuotas que se aplican al servicio IoT concentrador y el comportamiento de limitación que va a pasar al usar el servicio.
- [SDK de dispositivos y servicios de concentrador IoT] [ lnk-sdks] listas el idioma diversos SDK que un uso al desarrollar aplicaciones dispositivos y servicios que interactúan con IoT concentrador.
- [Lenguaje de consulta para gemelos, métodos y trabajos] [ lnk-query] describe el lenguaje de consulta que se puede usar para recuperar información de concentrador IoT sobre sus twins de dispositivo, métodos y trabajos.
- [Soporte técnico de IoT concentrador MQTT] [ lnk-devguide-mqtt] proporciona más información acerca del soporte técnico de IoT concentrador para el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar el registro de identidad de dispositivo IoT concentrador, puede estar interesado en los siguientes temas de la Guía del programador:

- [Controlar el acceso a IoT concentrador][lnk-devguide-security]
- [Utilizar a twins dispositivo para sincronizar estado y configuraciones][lnk-devguide-device-twins]
- [Llamar a un método directo en un dispositivo][lnk-devguide-directmethods]
- [Programar trabajos en varios dispositivos][lnk-devguide-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede estar interesado en el tutorial de concentrador IoT siguiente:

- [Introducción a Azure IoT concentrador][lnk-getstarted-tutorial]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md