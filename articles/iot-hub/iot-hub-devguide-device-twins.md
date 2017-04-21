<properties
 pageTitle="Programador guía: comprender twins dispositivo | Microsoft Azure"
 description="Guía de programador de Azure IoT concentrador - usar dispositivo twins para sincronizar los datos de estado y la configuración entre IoT concentrador y los dispositivos"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="understand-device-twins---preview"></a>Comprender a twins dispositivo: obtener una vista previa

## <a name="overview"></a>Información general

*Twins dispositivo* son documentos JSON que almacenan información de estado del dispositivo (metadatos, las configuraciones y condiciones). Concentrador IoT continúa a un doble dispositivo para cada dispositivo que se conecta a IoT concentrador. Este artículo se describe:

* la estructura de los dos dispositivos: *etiquetas*, *deseada* y *notificadas propiedades*, y
* las operaciones de aplicaciones del dispositivo y back-end puede llevar a cabo twins del dispositivo.

> [AZURE.NOTE] En este momento, twins dispositivo sólo son accesibles desde dispositivos que se conectan a concentrador IoT mediante el protocolo MQTT. Consulte el [soporte técnico MQTT] [ lnk-devguide-mqtt] artículo para obtener instrucciones sobre cómo convertir la aplicación de dispositivo existente usar MQTT.

### <a name="when-to-use"></a>Cuándo usar

Use twins dispositivo para:

* Almacenar metadatos específicos dispositivo en la nube, por ejemplo, la ubicación de implementación de una máquina expendedora.
* Informe información de estado actual como capacidades disponibles y condiciones de la aplicación de dispositivo, por ejemplo, un dispositivo conectarse a través de Wi-Fi o de telefonía móvil.
* Sincronizar el estado de flujos de trabajo de ejecución larga entre dispositivo aplicación y back final, por ejemplo, back-end que especifica la nueva versión de firmware para instalar y la aplicación de dispositivo informar de las distintas etapas del proceso de actualización.
* Los metadatos del dispositivo, la configuración o el estado de la consulta.

Use [los mensajes de nube de dispositivo] [ lnk-d2c] secuencias de eventos de marca como serie de tiempo de datos de sensor o alarmas. Utilizar [métodos de dispositivo de nube] [ lnk-methods] para el control interactivo de dispositivos, como activar un ventilador.

## <a name="device-twins"></a>Gemelos de dispositivo

Twins dispositivo almacenan información relacionada con el dispositivo que:

- Extremos atrás y dispositivo pueden utilizar para sincronizar la configuración y las condiciones del dispositivo.
- Puede usar el servidor de aplicaciones para consulta y destino larga ejecución operaciones.

El ciclo de vida de un doble dispositivo está vinculado a la [identidad del dispositivo]de correspondiente[lnk-identity]. Twins implícitamente se crean y se eliminan cuando se crea una nueva identidad de dispositivo o se elimina en IoT Hub.

Doble de un dispositivo es un documento JSON que incluye:

* **Etiquetas**. Un documento JSON leer y escribir el back-end. Etiquetas no son visibles para las aplicaciones del dispositivo.
* **Propiedades de deseado**. Utilizar junto con las propiedades del informes para sincronizar la configuración de dispositivo o condición. Propiedades que desee solo se pueden establecer la aplicación volver final y pueden leer por la aplicación del dispositivo. La aplicación de dispositivo también puede recibir notificaciones en tiempo real de los cambios en las propiedades que desee.
* **Propiedades de notificado**. Se usa junto con las propiedades que desee sincronizar la configuración de dispositivo o condición. Propiedades notificados sólo se puede establecer por la aplicación del dispositivo y pueden leer y consultados por el servidor de aplicaciones.

Además, la raíz de la doble dispositivo contiene las propiedades de sólo lectura de la identidad correspondiente, como contenidas en el [registro de la identidad de dispositivo][lnk-identity].

![][img-twin]

Aquí tiene un ejemplo de un documento JSON doble de dispositivo:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

En el objeto de raíz, son las propiedades del sistema y objetos de contenedor para `tags` y `reported` y `desired properties`. La `properties` contenedor contiene algunos elementos de solo lectura (`$metadata`, `$etag`, y `$version`) que se describen en los [metadatos de doble] respectivamente[ lnk-twin-metadata] y [simultaneidad optimista] [ lnk-concurrency] secciones.

### <a name="reported-property-example"></a>Ejemplo de la propiedad notificado

En el ejemplo anterior, el doble de dispositivo contiene una `batteryLevel` propiedad notificado por la aplicación del dispositivo. Esta propiedad hace posible la consulta y trabajar en dispositivos basados en el último nivel de la batería notificado. Otro ejemplo tendría las capacidades de dispositivo de informe de aplicación de dispositivo u opciones de conectividad.

Observe cómo notificados propiedades simplificar los escenarios donde el back-end está interesado en el último valor conocido de una propiedad. Use [los mensajes de nube de dispositivo] [ lnk-d2c] si necesita procesar telemetría de dispositivo en el formulario de secuencias de eventos de la marca, como la serie temporal el back-end.

### <a name="desired-configuration-example"></a>Ejemplo de configuración deseada

En el ejemplo anterior, la `telemetryConfig` se usan propiedades que quiera y notificadas por la aplicación final y dispositivo atrás para sincronizar la configuración de telemetría para este dispositivo. Por ejemplo:

1. El back-end de aplicación establece la propiedad que desee con el valor de configuración que desee. Esta es la parte del documento con la propiedad que desee:

        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
        
2. La aplicación de dispositivo se notifica del cambio inmediatamente si conectado o en la primera conectar de nuevo. La aplicación de dispositivo informa de la configuración actualizada (o una condición de error con la `status` propiedad). Esta es la parte de propiedades del informes:

        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...

3. Puede mantener el back-end de aplicación realizar el seguimiento de los resultados de la operación de configuración en varios dispositivos, [consultando] [ lnk-query] twins.

> [AZURE.NOTE] Los fragmentos de código anteriores son ejemplos, optimizados para mejorar la legibilidad, de forma posible para codificar una configuración de dispositivo y su estado. Concentrador IoT imponer un esquema para las propiedades que desee y notificados en twins de dispositivo específico.

En muchos casos twins se utilizan para sincronizar las operaciones prolongadas como las actualizaciones de firmware. Consulte usar las [propiedades que desee para configurar dispositivos] [ lnk-twin-properties] para obtener más información sobre cómo usar las propiedades para sincronizar y realizar un seguimiento de cuánto tiempo ejecuta las operaciones en todos los dispositivos.

## <a name="back-end-operations"></a>Operaciones de back-end

El back-end funciona en el doble usando las siguientes operaciones atómicas expuestas a través de HTTP:

1. **Recuperar doble por id**. Esta operación devuelve el contenido del documento de doble, incluidas las etiquetas y lo desea, notificadas y propiedades del sistema.
2. **Actualizar parcialmente doble**. Esta operación permite el back-end parcialmente actualizar las dos etiquetas o propiedades que desee. La actualización parcial se expresa como un documento JSON que agrega o actualiza cualquier propiedad mencionada. Establecen propiedades en `null` se quitan. Por ejemplo, el siguiente crea una nueva propiedad deseada con valor `{"newProperty": "newValue"}`, sobrescribe el valor existente de `existingProperty` con `"otherNewValue"`y se quita por completo `otherOldProperty`. Cambios no ocurren a otras propiedades que desee existentes o etiquetas:

        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }

3. **Reemplazar las propiedades que desee**. Esta operación permite el back-end completamente sobrescribir todas las propiedades que desee y sustituir un nuevo documento JSON para `properties/desired`.
4. **Reemplazar las etiquetas**. Forma análoga para reemplazar las propiedades que desee, este operaciones permite el back-end completamente sobrescribir todas las etiquetas existentes y sustituir un nuevo documento JSON para `tags`.

Todas las operaciones anteriores admiten [simultaneidad optimista] [ lnk-concurrency] y requerir el permiso **ServiceConnect** , según se define en la [seguridad] [ lnk-security] artículo.

Además de estas operaciones, el back-end puede consultar la twins usando un [lenguaje de consulta]de SQL como[lnk-query]y realizar operaciones en grandes conjuntos de twins utilizando los [trabajos][lnk-jobs].

## <a name="device-operations"></a>Operaciones de dispositivo

La aplicación de dispositivo funciona en el uso de las siguientes operaciones atómicas de doble:

1. **Recuperar doble**. Esta operación devuelve el contenido del documento de doble (incluidas etiquetas y lo desea, notificadas y propiedades del sistema) para el dispositivo conectado actualmente.
2. **Parcialmente actualización notificadas propiedades**. Esta operación permite la actualización parcial de las propiedades notificadas del dispositivo conectado actualmente. Utiliza el mismo formato de actualización JSON como el back-end opuestas actualización parcial de propiedades que desee.
3. **Respetar había deseado propiedades**. El dispositivo conectado actualmente puede elegir que se le notifiquen las actualizaciones de las propiedades que desee tan pronto como se producen. El dispositivo recibe el mismo formulario de actualización (reemplazo completo o parcial) ejecutado por el back-end.

Todas las operaciones anteriores requieren el permiso **DeviceConnect** , según se define en la [seguridad] [ lnk-security] artículo.

El [dispositivo de Azure IoT SDK] [ lnk-sdks] que sea más fácil usar las operaciones de varios idiomas y plataformas anteriores. Encontrará más información sobre los detalles de los fundamentos del IoT concentrador para la sincronización de propiedades que desee en el [flujo de dispositivo volver][lnk-reconnection].

> [AZURE.NOTE] Actualmente, twins dispositivo sólo son accesibles desde dispositivos que se conectan a concentrador IoT mediante el protocolo MQTT.

## <a name="reference-topics"></a>Temas de referencia:

Los temas de referencia siguientes le proporciona más información acerca de cómo controlar el acceso a su centro IoT.

## <a name="tags-and-properties-format"></a>Formato de etiquetas y propiedades

Etiquetas, propiedades que quiera y notificadas son objetos JSON con las siguientes restricciones:

* Todas las claves de objetos JSON son distingue mayúsculas de minúsculas 128 de un carácter UNICODE. Permite caracteres excluir caracteres de control UNICODE (segmentos C0 y C1) y `'.'`, `' '`, y `'$'`.
* Todos los valores de objeto JSON pueden ser de los siguientes tipos JSON: booleano, número, cadena, objeto. No se permiten matrices.

## <a name="twin-size"></a>Tamaño doble

Concentrador IoT impone un límite de tamaño de 8KB en los valores de `tags`, `properties/desired`, y `properties/reported`, excluir elementos de solo lectura.
El tamaño se calcula mediante el recuento de todos los caracteres UNICODE de excluyendo controlan caracteres (segmentos C0 y C1) y espacio `' '` cuando aparece fuera de una constante de cadena.
Todas las operaciones que podrían aumentar el tamaño de dichos documentos por encima del límite IoT concentrador rechaza con error.

## <a name="twin-metadata"></a>Metadatos de doble

Concentrador IoT mantiene la marca de hora de la última actualización para cada objeto JSON en las propiedades que desee y notificadas. Las marcas de tiempo en UTC y codificado en el formato [ISO8601] `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Por ejemplo:

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Esta información se conserva en cada nivel (no solo las hojas de la estructura JSON) para preservar las actualizaciones que quitar claves de objeto.

## <a name="optimistic-concurrency"></a>Simultaneidad optimista

Etiquetas, propiedades que quiera y notificadas admiten simultaneidad optimista.
Etiquetas tienen un valor de etag, según lo que [RFC7232], que muestra la representación de JSON de la etiqueta. Puede usar en las operaciones de actualización condicional desde el back-end para garantizar la coherencia.

Las propiedades que desee y notificadas no tiene etags, pero tiene una `$version` valor que se garantiza que es incremental. Forma análoga a un valor de etag, la versión puede ser usada por la parte actualización como (una aplicación de dispositivo para una propiedad notificada) o el back-end para una propiedad deseada para exigir la coherencia de las actualizaciones.

Versiones también son útiles cuando un agente observar (por ejemplo, la aplicación de dispositivo observar las propiedades que desee) tiene conciliar carreras entre el resultado de una operación de recuperar y una notificación de actualización. La sección [flujo de dispositivo volver] [ lnk-reconnection] proporciona más información.

## <a name="device-reconnection-flow"></a>Flujo de volver de dispositivo

Concentrador IoT no conserva las notificaciones de actualización de propiedades que desee para dispositivos desconectados. Aparece después de que un dispositivo que se está conectando debe recuperar el documento de todas las propiedades que quiera, además de suscripción para las notificaciones de actualización. Dada la posibilidad de carreras entre las notificaciones de actualización y recuperación completa, es necesario garantizar el flujo siguiente:

1. Aplicación de dispositivo se conecta a un concentrador IoT.
2. Aplicación de dispositivo suscribe deseados propiedades de las notificaciones de actualización.
3. Aplicación de dispositivo recupera el documento completo para las propiedades que desee.

La aplicación de dispositivo puede pasar por alto todas las notificaciones con `$version` inferior o igual a la versión del documento recuperado completa. Esto es posible porque IoT concentrador garantiza que siempre incrementan versiones.

> [AZURE.NOTE] Esta lógica ya se ha implementado en el [dispositivo de Azure IoT SDK][lnk-sdks]. Esta descripción es útil si la aplicación de dispositivo no puede utilizar cualquiera de los dispositivos de Azure IoT SDK y debe programar la interfaz MQTT directamente.

## <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la Guía del programador incluyen:

- [Extremos de concentrador IoT] [ lnk-endpoints] describe los diversos extremos que expone cada concentrador IoT para operaciones de administración y el tiempo de ejecución.
- [Cuotas y regulación] [ lnk-quotas] describe las cuotas que se aplican al servicio IoT concentrador y el comportamiento de limitación que va a pasar al usar el servicio.
- [SDK de dispositivos y servicios de concentrador IoT] [ lnk-sdks] listas el idioma diversos SDK que un uso al desarrollar aplicaciones dispositivos y servicios que interactúan con IoT concentrador.
- [Lenguaje de consulta para gemelos, métodos y trabajos] [ lnk-query] describe el lenguaje de consulta que se puede usar para recuperar información de concentrador IoT sobre sus twins de dispositivo, métodos y trabajos.
- [Soporte técnico de IoT concentrador MQTT] [ lnk-devguide-mqtt] proporciona más información acerca del soporte técnico de IoT concentrador para el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido acerca twins del dispositivo, puede estar interesado en los siguientes temas de la Guía del programador:

- [Llamar a un método directo en un dispositivo][lnk-methods]
- [Programar trabajos en varios dispositivos][lnk-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede estar interesado en los siguientes tutoriales de concentrador IoT:

- [Cómo usar al doble de dispositivo][lnk-twin-tutorial]
- [Cómo utilizar las propiedades de doble][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png