> [AZURE.SELECTOR]
- [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)

## <a name="introduction"></a>Introducción

En [comenzar con concentrador IoT gemelos][lnk-twin-tutorial], aprendido a configurar dispositivos metadatos de su solución back-end mediante *etiquetas*, condiciones de dispositivo de informe desde una aplicación de dispositivo utilizando *propiedades conocidas*y consultar esta información mediante un lenguaje similar a SQL.

En este tutorial, aprenderá a utilizar la de twin *propiedades deseadas* junto con *registrados propiedades*, configurar de forma remota aplicaciones del dispositivo. Más concretamente, este tutorial muestra cómo se informa del gemelo y propiedades que desee habilitar una configuración de varios pasos de configuración de la aplicación de dispositivo y proporcionan la visibilidad para el back-end solución del estado de esta operación en todos los dispositivos.

A un alto nivel, este tutorial sigue el *modelo de estado deseado* para administración de dispositivos. La idea fundamental de este modelo es que el back-end de solución especificar el estado deseado de los dispositivos administrados, en lugar de enviar comandos específicos. Esto pone el dispositivo encargado de establecer la mejor manera de alcanzar el estado deseado (muy importante en escenarios de IoT donde condiciones de dispositivo específico afecta a la capacidad inmediatamente para llevar a cabo comandos específicos), al informar continuamente al back-end, el estado actual y las posibles condiciones de error. El modelo de estado deseado es fundamental para la gestión de grandes conjuntos de dispositivos, ya que permite que el servidor tenga total visibilidad del estado del proceso de configuración en todos los dispositivos.
Puede encontrar más información sobre la función del modelo de estado deseado en la administración de dispositivos de [administración de dispositivos de introducción de Azure IoT concentrador][lnk-dm-overview].

> [AZURE.NOTE] En escenarios donde los dispositivos se controlan de manera más interactiva (activar un ventilador de una aplicación controlada por el usuario), considere el uso de [métodos de nube para dispositivos][lnk-methods].

En este tutorial, los cambios de back-end de la aplicación la configuración de un dispositivo de destino y, como consecuencia de esto, la aplicación del dispositivo de telemetría sigue un proceso de varios pasos para aplicar una actualización de la configuración (por ejemplo, que requieren un reinicio del módulo de software), que en este tutorial se simula con un simple retraso).

El back-end almacena la configuración de propiedades de twin el dispositivo que desee de la siguiente manera:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [AZURE.NOTE] Puesto que las configuraciones pueden ser objetos complejos, normalmente se asignan identificadores únicos ( [GUID]o hash[lnk-guid]) para simplificar las comparaciones.

La aplicación de dispositivo indica su configuración actual de creación de reflejo de la propiedad deseada **telemetryConfig** en las propiedades del informes:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Observe cómo la conocida **telemetryConfig** tiene una propiedad adicional de **estado**, utilizado para notificar el estado del proceso de actualización de configuración.

Cuando se recibe una nueva configuración deseada, la aplicación del dispositivo presenta una configuración pendiente cambiando la información:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

A continuación, en algún momento posterior, la aplicación de dispositivo informará el éxito de la falta de esta operación mediante la actualización de la propiedad anterior.
Observe cómo el back-end es capaz, en cualquier momento, para consultar el estado del proceso de configuración a través de todos los dispositivos.

Este tutorial muestra cómo para:

- Crear un dispositivo simulado que recibe las actualizaciones de configuración desde el back-end e informa varias actualizaciones como *Propiedades informados* sobre el proceso de actualización de la configuración.
- Crear una aplicación de servicios de fondo que actualiza la configuración deseada de un dispositivo y, a continuación, consulta el proceso de actualización de la configuración.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier