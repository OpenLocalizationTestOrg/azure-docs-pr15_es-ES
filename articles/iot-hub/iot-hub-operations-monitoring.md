<properties
 pageTitle="Operaciones de concentrador IoT supervisión"
 description="Información general de las operaciones de Azure IoT concentrador supervisión, que permite controlar el estado de las operaciones en su centro IoT en tiempo real"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-operations-monitoring"></a>Introducción a las operaciones de supervisión

Operaciones de concentrador IoT supervisión le permite controlar el estado de las operaciones de su centro IoT en tiempo real. Concentrador IoT realiza un seguimiento de eventos a través de varias categorías de operaciones y puede participar en el envío de eventos de una o varias categorías a un extremo de su centro IoT para el procesamiento. Puede supervisar los datos de errores o configurar el procesamiento de más compleja basada en modelos de datos.

IoT concentrador supervisa cinco categorías de eventos:

- Operaciones de identidad de dispositivo
- Telemetría de dispositivo
- Comandos de dispositivo de nube
- Conexiones
- Cargas de archivos

## <a name="how-to-enable-operations-monitoring"></a>Cómo habilitar las operaciones de supervisión

1. Crear un concentrador IoT. Puede encontrar las instrucciones sobre cómo crear un concentrador IoT en la [Introducción] [ lnk-get-started] guía.

2. Abra el módulo de su centro IoT. Desde allí, haga clic en **supervisión de operaciones**.

    ![][1]

3. Seleccione las categorías de supervisión que desee supervisar y, a continuación, haga clic en **Guardar**. Los eventos están disponibles para su lectura desde el extremo de concentrador-compatible con el evento que aparece en **configuración de supervisión**. El extremo de concentrador IoT se denomina `messages/operationsmonitoringevents`.

    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>Categorías de eventos y cómo usarlas

Cada categoría pistas de supervisión de operaciones otro tipo de interacción con IoT concentrador y cada categoría supervisión tiene un esquema que define cómo están estructurados los eventos de esa categoría.

### <a name="device-identity-operations"></a>Operaciones de identidad de dispositivo

La categoría de operaciones de identidad de dispositivo realiza un seguimiento de los errores que se producen al intentar crear, actualizar o eliminar una entrada identidad registro de su centro IoT. Seguimiento de esta categoría es útil para escenarios de aprovisionamiento.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Telemetría de dispositivo

La categoría de telemetría dispositivo realiza un seguimiento de los errores que se producen en el hub de IoT y están relacionadas con la canalización de telemetría. Esta categoría incluye los errores que se producen al enviar eventos de telemetría (como limitación) y recibir eventos de telemetría (como lector no autorizado). Tenga en cuenta que esta categoría no puede detectar errores de código que se ejecuta en el propio dispositivo.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Comandos de dispositivo de nube

La categoría comandos de dispositivo de nube realiza un seguimiento de los errores que se producen en el hub de IoT y están relacionadas con la canalización de comandos de dispositivo. Esta categoría incluye errores que se producen al enviar comandos (como el remitente no autorizado), recibir comandos (como excedido el número de entrega) y recibir comentarios de comando (como comentarios caducado). Esta categoría no detecta errores desde un dispositivo que trata incorrectamente un comando si el comando se ha entregado correctamente.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Conexiones

La categoría de conexiones mantiene errores que se producen cuando los dispositivos conectan o desconexión un concentrador IoT. Seguimiento de esta categoría es útil para identificar intentos de conexión no autorizado y para realizar el seguimiento cuando se pierde para dispositivos en las áreas de conectividad deficiente la conexión.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Cargas de archivos

La categoría de carga de archivo realiza un seguimiento de los errores que se producen en el hub de IoT y relacionadas con la funcionalidad de carga de archivo. Esta categoría incluye los errores que se producen con el URI de SAS (por ejemplo, cuando expira antes de que un dispositivo notifica el hub de una carga completada), cargas error notificadas por el dispositivo y cuando un archivo no se encuentra en almacenamiento durante la creación de mensaje de notificación de concentrador IoT. Tenga en cuenta que esta categoría no puede detectar errores que aparecen directamente mientras el dispositivo está cargando un archivo al almacenamiento.

    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## <a name="next-steps"></a>Pasos siguientes

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md