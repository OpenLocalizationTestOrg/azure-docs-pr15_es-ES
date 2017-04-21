<properties
 pageTitle="Guía de programador - carga de archivos | Microsoft Azure"
 description="Guía de programador de Azure IoT concentrador - cargar archivos desde un dispositivo concentrador IoT"
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

# <a name="upload-files-from-a-device"></a>Cargar archivos desde un dispositivo

## <a name="overview"></a>Información general

Como se describe en los [extremos de concentrador IoT] [ lnk-endpoints] artículo dispositivos pueden iniciar cargas de archivos mediante el envío de una notificación a través de un extremo de la orientación del dispositivo (**/devices/ {deviceId} / archivos**).  Cuando un dispositivo notifica IoT concentrador de una carga completada, concentrador IoT genera notificaciones de carga de archivo que pueda recibir a través de un extremo de servicio orientación (**/messages/servicebound/filenotifications**) como mensajes.

En lugar de intermediación mensajes a través de concentrador IoT propio, IoT concentrador en su lugar actúa como un distribuidor a una cuenta de almacenamiento de Azure asociada. Un dispositivo solicita un token de almacenamiento de concentrador IoT específico para el archivo en el dispositivo que desea cargar. El dispositivo utiliza el URI de SAS para cargar el archivo en el almacenamiento y una vez completada la carga el dispositivo envía una notificación de finalización a IoT concentrador. Concentrador IoT comprueba que el archivo se cargó y, a continuación, agrega una notificación de carga de archivo al nuevo servicio orientación archivo notificación mensajería extremo.

Antes de cargar un archivo a concentrador IoT desde un dispositivo, debe configurar su centro asociando [El almacenamiento de Azure] [ lnk-associate-storage] de la cuenta a él.

El dispositivo, a continuación, puede [inicializar una carga] [ lnk-initialize] y, a continuación, [Notificar IoT concentrador] [ lnk-notify] cuando finaliza la carga. Si lo desea, cuando un dispositivo notifica IoT concentrador que la carga está completa, el servicio puede generar un [mensaje de notificación][lnk-service-notification].

### <a name="when-to-use"></a>Cuándo usar

Use esta característica IoT concentrador cuando necesita cargar un archivo desde un dispositivo en el servicio de back-end en lugar de enviar un mensaje a través de concentrador IoT.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Asociar una cuenta de almacenamiento de Azure IoT concentrador

Para usar la funcionalidad de carga de archivos, primero debe vincular una cuenta de almacenamiento de Azure al concentrador IoT. Puede hacerlo ya sea a través del [portal de Azure][lnk-management-portal], o mediante programación a través del [proveedor de recursos de concentrador IoT API de REST][lnk-resource-provider-apis]. Una vez que ha asociado a una cuenta de almacenamiento de Azure su centro IoT, el servicio devuelve un URI SAS a un dispositivo cuando el dispositivo inicia una solicitud de carga de archivo.

> [AZURE.NOTE] El [SDK de concentrador de Azure IoT] [ lnk-sdks] controlar automáticamente recuperar el URI SAS, cargar el archivo y notificarle IoT concentrador de una carga completada.

## <a name="initialize-a-file-upload"></a>Inicializar una carga de archivos

El concentrador IoT tiene un extremo específicamente para dispositivos solicitar un SAS URI de almacenamiento para cargar un archivo. El dispositivo inicia el proceso de carga de archivo mediante el envío de una publicación en el hub de IoT en `{iot hub}.azure-devices.net/devices/{deviceId}/files` con el siguiente cuerpo JSON:

```
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

Concentrador IoT devuelve el siguiente, que el dispositivo se usa para cargar el archivo:

```
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Obsoleto: inicializar una carga de archivos con GET

> [AZURE.NOTE] Esta sección describe funcionalidad obsoleta para saber cómo recibir un URI SAS IoT concentrador. Use el método de entrada descrito anteriormente.

El concentrador IoT tiene dos extremos REST para admitir la carga de archivos, uno para obtener el URI de SAS para el almacenamiento y el otro para notificar el concentrador IoT de una carga completada. El dispositivo inicia el proceso de carga de archivo enviando un GET al concentrador IoT en `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. El concentrador devuelve un URI SAS específico en el archivo que va a cargar y un ID de correlación para utilizar una vez completada la carga.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notificar IoT concentrador de carga de archivos completada

El dispositivo es responsable de cargar el archivo al almacenamiento usando el SDK de almacenamiento de Azure. Una vez completada la carga, el dispositivo envía una publicación en el hub de IoT en `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` con el siguiente cuerpo JSON:

```
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

El valor de `isSuccess` es un valor Boolean que indica si o no el archivo se cargó correctamente. El código de estado de `statusCode` es el estado de la carga del archivo de almacenamiento y la `statusDescription` corresponde a la `statusCode`.

## <a name="reference-topics"></a>Temas de referencia:

Los siguientes temas de referencia proporcionan más información sobre cómo cargar archivos desde un dispositivo.

## <a name="file-upload-notifications"></a>Notificaciones de carga de archivo

Cuando un dispositivo carga un archivo y notifica IoT concentrador de finalización de la carga, el servicio de manera opcional genera un mensaje de notificación que contiene el nombre y ubicación de almacenamiento del archivo.

Como se explica en los [extremos][lnk-endpoints], concentrador IoT proporciona notificaciones de carga de archivos a través de un extremo de servicio orientación (**/messages/servicebound/fileuploadnotifications**) como mensajes. La semántica de recepción de notificaciones de carga de archivo son los mismos que los mensajes de dispositivo de nube y tiene el mismo [ciclo de vida del mensaje][lnk-lifecycle]. Cada mensaje recuperado desde el extremo de notificación de carga de archivo es un registro JSON con las siguientes propiedades:

| (Propiedad) | Descripción |
| -------- | ----------- |
| EnqueuedTimeUtc | Marca de tiempo que indica cuando se creó la notificación. |
| DeviceId | **DeviceId** del dispositivo que cargar el archivo. |
| BlobUri | URI del archivo cargado. |
| BlobName | Nombre del archivo cargado. |
| LastUpdatedTime | Marca de tiempo que indica cuándo se actualizó por última vez el archivo. |
| BlobSizeInBytes | Tamaño del archivo cargado. |

**Ejemplo**. Se trata de un cuerpo de ejemplo de un mensaje de notificación de carga de archivo.

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opciones de configuración de notificación de carga de archivo

Cada concentrador IoT expone las siguientes opciones de configuración para las notificaciones de carga de archivo:

| (Propiedad) | Descripción | Rango y predeterminado |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | Controla si las notificaciones de carga de archivo se escriben en el extremo de las notificaciones de archivo. | BOOL. Valor predeterminado: True. |
| **fileNotifications.ttlAsIso8601** | TTL predeterminado para las notificaciones de carga de archivo. | ISO_8601 intervalo hasta 48 H (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| **fileNotifications.lockDuration** | Duración de bloqueo de la cola de notificaciones de carga de archivo. | 5 y 300 segundos (mínimo 5). Valor predeterminado: 60 segundos. |
| **fileNotifications.maxDeliveryCount** | Número máximo de entrega para el archivo cargar cola de notificación. | 1 a 100. Valor predeterminado: 100. |

## <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la Guía del programador incluyen:

- [Extremos de concentrador IoT] [ lnk-endpoints] describe los diversos extremos que expone cada concentrador IoT para operaciones de administración y el tiempo de ejecución.
- [Cuotas y regulación] [ lnk-quotas] describe las cuotas que se aplican al servicio IoT concentrador y el comportamiento de limitación que va a pasar al usar el servicio.
- [SDK de dispositivos y servicios de concentrador IoT] [ lnk-sdks] listas el idioma diversos SDK que un uso al desarrollar aplicaciones dispositivos y servicios que interactúan con IoT concentrador.
- [Lenguaje de consulta para gemelos, métodos y trabajos] [ lnk-query] describe el lenguaje de consulta que se puede usar para recuperar información de concentrador IoT sobre sus twins de dispositivo, métodos y trabajos.
- [Soporte técnico de IoT concentrador MQTT] [ lnk-devguide-mqtt] proporciona más información acerca del soporte técnico de IoT concentrador para el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a cargar archivos desde dispositivos con IoT concentrador, puede estar interesado en los siguientes temas de la Guía del programador:

- [Administrar identidades de dispositivo en el IoT Hub][lnk-devguide-identities]
- [Controlar el acceso a IoT concentrador][lnk-devguide-security]
- [Utilizar a twins dispositivo para sincronizar estado y configuraciones][lnk-devguide-device-twins]
- [Llamar a un método directo en un dispositivo][lnk-devguide-directmethods]
- [Programar trabajos en varios dispositivos][lnk-devguide-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede estar interesado en el tutorial de concentrador IoT siguiente:

- [Cómo cargar archivos desde dispositivos a la nube con concentrador IoT][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
