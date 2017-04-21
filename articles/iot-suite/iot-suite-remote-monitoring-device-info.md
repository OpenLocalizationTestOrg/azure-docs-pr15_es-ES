<properties
 pageTitle="Metadatos de información de dispositivo en la solución de supervisión | Microsoft Azure"
 description="Una descripción de la IoT Azure había preconfigurado supervisión remota de solución y la arquitectura."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/12/2016"
 ms.author="dobett"/>

# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Metadatos de información de dispositivo en la solución preconfigurado supervisión remota

El conjunto de aplicaciones de Azure IoT supervisión solución preconfigurada remota se muestra un método para administrar metadatos del dispositivo. En este artículo se describe el enfoque esta solución tiene para que pueda entender:

- La solución de los metadatos de qué dispositivo almacena.
- ¿Cómo la solución administra los metadatos del dispositivo.

## <a name="context"></a>Contexto

La solución de preconfigurados supervisión usa [Azure IoT concentrador] [ lnk-iot-hub] para permitir que los dispositivos enviar datos a la nube. Concentrador IoT incluye un [registro de la identidad de dispositivo] [ lnk-identity-registry] para controlar el acceso a IoT concentrador. El registro de identidad de dispositivo IoT concentrador es independiente del mando a distancia supervisión específica de la solución de *registro de dispositivos* que almacena los metadatos de información del dispositivo. La solución de supervisión usa un [DocumentDB] [ lnk-docdb] base de datos para implementar su registro de dispositivo para almacenar los metadatos de información del dispositivo. La [Arquitectura de referencia de Microsoft Azure IoT] [ lnk-ref-arch] describe la función de registro del dispositivo en una solución IoT típica.

> [AZURE.NOTE] La solución de preconfigurados supervisión mantiene el registro de la identidad de dispositivo sincronizado con el registro del dispositivo. Ambos utilizan el mismo id para identificar cada dispositivo conectado a su centro IoT.

La [vista previa de administración de dispositivos de concentrador IoT] [ lnk-dm-preview] agrega características a IoT concentrador que son similares a las características de administración de información de dispositivo descritas en este artículo. Actualmente, la solución de supervisión solo usa las características disponibles de forma general (GA) en el IoT Hub.

## <a name="device-information-metadata"></a>Metadatos de información de dispositivo

Un documento de JSON de metadatos de dispositivo información almacenado en la base de datos de DocumentDB de registro de dispositivo tiene la estructura siguiente:

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: el propio dispositivo escribe estas propiedades y el dispositivo es la autoridad para estos datos. Otras propiedades de dispositivo de ejemplo incluyen el fabricante, el número de modelo y el número de serie. 
- **DeviceID**: el identificador único del dispositivo. Este valor es el mismo en el registro de identidad de dispositivo IoT concentrador.
- **HubEnabledState**: el estado del dispositivo en IoT concentrador. Inicialmente se establece este valor en **null** hasta que se conecte el dispositivo por primera vez. En el portal de la solución, se representa un valor **nulo** como el dispositivo está "registrados, pero no se encuentra".
- **CreatedTime**: el tiempo que se creó el dispositivo.
- **DeviceState**: el estado notificado por el dispositivo.
- **UpdatedTime**: la hora del dispositivo se actualizó por última vez a través del portal de solución.
- **SystemProperties**: el portal de solución escribe las propiedades del sistema y el dispositivo no tiene conocimiento de estas propiedades. Un ejemplo de la propiedad de sistema es la **ICCID** si la solución está autorizada con y conectado a un servicio de administración de dispositivos habilitados para SIM.
- **Comandos**: compatible con el dispositivo de una lista de los comandos. El dispositivo proporciona esta información para la solución.
- **CommandHistory**: una lista de los comandos enviados por la solución de supervisión en el dispositivo y el estado de los comandos.
- **IsSimulatedDevice**: un indicador que identifica un dispositivo como un dispositivo simulado.
- **ID**: el identificador único de DocumentDB para este documento del dispositivo.

> [AZURE.NOTE] Información del dispositivo también puede incluir metadatos para describir la telemetría que el dispositivo envía a IoT concentrador. La solución de supervisión usa estos metadatos de telemetría para personalizar cómo muestra el panel de [telemetría dinámico][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Ciclo de vida

Cuando crea por primera vez un dispositivo en el portal de solución, la solución crea una entrada en el registro de dispositivo tal como se muestra anteriormente. Gran parte de la información es auxiliar inicialmente y la **HubEnabledState** se establece en **null**. En este momento, la solución también crea una entrada para el dispositivo en el registro de identidad de dispositivo, que genera las claves que el dispositivo se usa para autenticar con IoT concentrador.

Cuando un dispositivo se conecta primero a la solución, envía un mensaje de información del dispositivo. Este mensaje de información de dispositivo incluye propiedades de dispositivos como el fabricante del dispositivo, el número de modelo y el número de serie. Un mensaje de información de dispositivo también incluye una lista de los comandos es compatible con el dispositivo incluye información sobre los parámetros de comando. Cuando la solución recibe este mensaje, actualiza los metadatos de información de dispositivo en el registro del dispositivo.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Ver y editar la información de dispositivo en el portal de solución

La lista de dispositivos en el portal de solución muestra las siguientes propiedades de dispositivo como columnas: **estado**, **DeviceId**, **fabricante**, **Número de modelo**, **número de serie**, **Firmware**, **plataforma**, **procesador**y **RAM instalado**. Las propiedades del dispositivo **latitud** y **longitud** unidad la ubicación en el mapa de Bing en el panel. 

![Lista de dispositivos][img-device-list]

Si hace clic en **Editar** en el panel de **Detalles del dispositivo** en el portal de la solución, puede editar todas estas propiedades. Editar estas propiedades, actualiza el registro del dispositivo en la base de datos de DocumentDB. Sin embargo, si un dispositivo envía un mensaje de información actualizada del dispositivo, se sobrescribe cualquier cambio realizado en el portal de solución. No puede editar el **DeviceId** **Hostname**, **HubEnabledState**, **CreatedTime**, **DeviceState**y **UpdatedTime** propiedades en el portal de solución porque solo el dispositivo tiene autoridad sobre estas propiedades.

![Edición de dispositivos][img-device-edit]

Puede usar el portal de solución para quitar un dispositivo de la solución. Al quitar un dispositivo, la solución quita los metadatos de información del dispositivo de registro del dispositivo de solución y elimina la entrada de dispositivo en el registro de identidad de dispositivo de concentrador IoT. Antes de quitar un dispositivo, se debe deshabilitar.

![Quitar dispositivo][img-device-remove]

## <a name="device-information-message-processing"></a>Procesamiento de mensaje de información de dispositivo

Mensajes de información del dispositivo enviados por un dispositivo son distintos de los mensajes de telemetría. Mensajes de información de dispositivo incluyen información como propiedades de dispositivos, los comandos de a que un dispositivo puede responder y el historial de los comandos. Concentrador IoT propio no tiene conocimiento de los metadatos contenidos en un mensaje de información del dispositivo y procesa el mensaje de la misma manera que procesa cualquier mensaje del dispositivo a la nube. En la solución de supervisión, un [Análisis de secuencia de Azure] [ lnk-stream-analytics] trabajo (ASA) lee los mensajes de concentrador IoT. El análisis de secuencia **DeviceInfo** trabajo filtros para los mensajes que contengan **"ObjectType": "DeviceInfo"** y reenvía a la instancia de host de **EventProcessorHost** que se ejecute en un trabajo web. Lógica de la instancia de **EventProcessorHost** utiliza el identificador de dispositivo para buscar el registro de DocumentDB para el dispositivo específico y actualice el registro. El registro de registro de dispositivo ahora incluye información como propiedades de dispositivos, comandos y el historial de comando.

> [AZURE.NOTE] Un mensaje de información de dispositivo es un dispositivo a la nube estándar. La solución distingue entre mensajes de información del dispositivo y de telemetría mediante consultas ASA.

## <a name="example-device-information-records"></a>Registros de información del dispositivo de ejemplo

La solución de preconfigurados supervisión usa dos tipos de registros de información del dispositivo: registros para los dispositivos simulados implementados con la solución y los registros de los dispositivos personalizados se conecta a la solución.

### <a name="simulated-device"></a>Dispositivo simulado

En el ejemplo siguiente se muestra el registro de información del dispositivo JSON para un dispositivo simulado. Este registro tiene un valor para **UpdatedTime**, lo que indica que el dispositivo ha enviado un mensaje de **DeviceInfo** a IoT concentrador. El registro incluye algunas propiedades comunes del dispositivo, define los seis comandos de soporte técnico de los dispositivos simulados y se la marca **IsSimulatedDevice** establecido en **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### <a name="custom-device"></a>Dispositivo personalizado

En el ejemplo siguiente se muestra el registro de información del dispositivo JSON para un dispositivo personalizado y tiene la marca **IsSimulatedDevice** establecida en **0**. Puede ver que este dispositivo personalizado admite dos comandos y que el portal de solución ha enviado un comando **SetTemperature** en el dispositivo:

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

La siguiente muestra el mensaje JSON **DeviceInfo** el dispositivo enviado para actualizar los metadatos de información del dispositivo:

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que haya terminado de cómo puede personalizar las soluciones preconfiguradas de aprendizaje, puede explorar algunas de las otras características y las capacidades de las soluciones de Suite IoT preconfigurado:

- [Información general de solución de mantenimiento predictiva preconfigurado][lnk-predictive-overview]
- [Preguntas más frecuentes sobre IoT Suite][lnk-faq]
- [Seguridad IoT conceptos básicos][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
