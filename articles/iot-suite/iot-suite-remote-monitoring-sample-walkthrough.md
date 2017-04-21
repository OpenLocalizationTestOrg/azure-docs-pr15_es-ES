<properties
 pageTitle="Supervisión remota preconfigurado solución tutorial | Microsoft Azure"
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
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="dobett"/>

# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Tutorial de solución preconfigurada supervisión remota

## <a name="introduction"></a>Introducción

Remoto IoT Suite supervisión [preconfigurado solución] [ lnk-preconfigured-solutions] es una implementación de un llevar a cabo supervisión solución para varios equipos que ejecutan en ubicaciones remotas. La solución combina servicios de Azure clave para proporcionar una implementación genérica del escenario empresarial y puede usarlo como punto de partida para su implementación. Puede [Personalizar] [ lnk-customize] la solución para satisfacer sus propios requisitos empresariales específicos.

En este artículo le guiará a través de algunos de los elementos clave de la solución de supervisión para que pueda entender cómo funciona. Este conocimiento le ayuda a:

- Solución de problemas en la solución.
- Planear cómo personalizar la solución para satisfacer sus propios requisitos específicos. 
- Diseñe su propia solución IoT que usa servicios de Azure.

## <a name="logical-architecture"></a>Arquitectura lógica

El diagrama siguiente resume los componentes lógicos de la solución preconfigurado:

![Arquitectura lógica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## <a name="simulated-devices"></a>Dispositivos simulados

En la solución preconfigurada, dispositivo simulado representa un dispositivo de refrigeración (por ejemplo, un edificio aire acondicionado o unidad de gestión de las instalaciones air). Cuando se implementa la solución preconfigurada, también automáticamente aprovisionar cuatro dispositivos simulados que se ejecutan en un [WebJob de Azure][lnk-webjobs]. Los dispositivos simulados facilitan explorar el comportamiento de la solución sin necesidad de implementar los dispositivos físicos. Para implementar un dispositivo físico real, consulte la [solución preconfigurada de supervisión de conectar el dispositivo en el equipo remoto] [ lnk-connect-rm] tutorial.

Cada dispositivo simulado puede enviar los siguientes tipos de mensaje a IoT concentrador:

| Mensaje  | Descripción |
|----------|-------------|
| Inicio  | Cuando se inicia el dispositivo, envía un mensaje de **información del dispositivo** que contiene información sobre sí mismo al back-end. Esta información incluye el identificador del dispositivo, los metadatos de dispositivo, una lista de los comandos admite el dispositivo y a continuación, la configuración actual del dispositivo. |
| Presencia | Un dispositivo periódicamente envía un mensaje de **presencia** para informar de si el dispositivo puede detectar la presencia de un sensor. |
| Telemetría | Un dispositivo periódicamente envía un mensaje de **telemetría** que informa simulada de los valores de la temperatura y humedad recopilado sensores simulada del dispositivo. |


Los dispositivos simulados envían las siguientes propiedades de dispositivos en un mensaje de **información del dispositivo** :

| (Propiedad)               |  Propósito |
|------------------------|--------- |
| Id. de dispositivo              | Identificador que se proporcionan o asignado cuando se crea un dispositivo de la solución. |
| Fabricante           | Fabricante del dispositivo |
| Número de modelo           | Número de modelo del dispositivo |
| Número de serie          | Número de serie del dispositivo |
| Firmware               | Versión actual del firmware en el dispositivo |
| Plataforma               | Arquitectura de la plataforma del dispositivo |
| Procesador              | Procesador ejecutando el dispositivo |
| Memoria RAM instalada          | Cantidad de RAM instalada en el dispositivo |
| Estado de concentrador habilitado      | Propiedad de estado de concentrador IoT del dispositivo |
| Hora de creación           | Tiempo que se creó el dispositivo en la solución |
| Hora de actualización           | Última vez que se actualizaron las propiedades del dispositivo |
| Latitud               | Ubicación de latitud del dispositivo |
| Latitud              | Ubicación de la longitud del dispositivo |

El simulador utiliza estas propiedades en dispositivos simulados con valores de ejemplo.  Cada vez que el simulador Inicializa un dispositivo simulado, el dispositivo registra los metadatos predefinidos a IoT concentrador. Observe cómo se sobrescribe cualquier actualización de metadatos realizada en el portal de dispositivo.


Los dispositivos simulados pueden controlar los siguientes comandos que se envía desde el panel de la solución a través del concentrador IoT:

| Comando                | Descripción                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Envía un _ping_ al dispositivo para comprobar que está activo   |
| StartTelemetry         | Inicia el envío de telemetría de dispositivo                 |
| StopTelemetry          | El dispositivo de envío de telemetría se detiene             |
| ChangeSetPointTemp     | Cambia el valor de punto establecido por el que se generaron los datos aleatorios |
| DiagnosticTelemetry    | Activa el simulador de dispositivos para enviar un valor de telemetría adicionales (externalTemp) |
| ChangeDeviceState      | Cambia una propiedad de estado extendido para el dispositivo y envía el mensaje de información de dispositivo desde el dispositivo |

La confirmación de comando de dispositivo al back-end solución se proporciona a través del concentrador IoT.

## <a name="iot-hub"></a>Concentrador IoT

El [concentrador IoT] [ lnk-iothub] recopila los datos enviados desde los dispositivos en la nube y hace que esté disponible para los trabajos de análisis de secuencia de Azure (ASA). Concentrador IoT envía también comandos a sus dispositivos en nombre del portal de dispositivo. Cada trabajo ASA de secuencia utiliza un grupo independiente de consumidor IoT concentrador para leer la secuencia de mensajes desde sus dispositivos.

## <a name="azure-stream-analytics"></a>Análisis de secuencia de Azure

En la solución de supervisión, [Análisis de secuencia de Azure] [ lnk-asa] (ASA) envía mensajes de dispositivo recibidos por el concentrador IoT a otros componentes de back-end para procesamiento o almacenamiento. Las distintas tareas ASA realizan funciones específicas basándose en el contenido de los mensajes.

**Trabajo 1: información de dispositivo** filtra los mensajes de información de dispositivo de la secuencia de mensajes entrantes y envía a un extremo de concentrador de evento. Un dispositivo envía mensajes de información de dispositivo de inicio y, en respuesta a un comando **SendDeviceInfo** . Este trabajo utiliza la definición de la consulta siguiente para identificar los mensajes de **información del dispositivo** :

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Este trabajo envía sus resultados a un concentrador de evento para el procesamiento de más.

**Trabajo 2: reglas** evalúa los valores de telemetría temperatura y humedad entrantes con umbrales por dispositivo. Valores de umbral se establecen en el editor de reglas disponible en el panel de la solución. Cada pareja de valor del dispositivo se almacena en la marca de tiempo en un blob que el análisis de secuencia lee en como **Datos de referencia**. El trabajo compara el valor de cualquier no vacía con el umbral establecido para el dispositivo. Si supera el ' >' condición, la tarea envía un evento de **alarma** que indica que se ha superado el umbral y que proporciona el dispositivo, el valor y los valores de la marca de tiempo. Este trabajo utiliza la definición de la consulta siguiente para identificar los mensajes de telemetría que deben desencadenar una alarma:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

El trabajo envía sus resultados a un concentrador de evento para la transformación y guarda los detalles de cada alerta al almacenamiento de blobs de en el panel de la solución puede leer la información de alerta.

**Trabajo 3: telemetría** opera en la secuencia entrante de telemetría de dispositivo de dos maneras. La primera envía todos los mensajes de telemetría de los dispositivos con almacenamiento de blobs persistente almacenamiento a largo plazo. La segunda calcula los valores de humedad promedio, máximos y mínimos en una ventana deslizante de cinco minutos y envía estos datos al almacenamiento de blobs. El panel de la solución lee los datos de telemetría de almacenamiento de blobs para rellenar los gráficos. Este trabajo utiliza la definición de la consulta siguiente:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Evento Hubs

Los trabajos ASA de **información del dispositivo** y **reglas** de salida sus datos de evento Hubs confiable reenviar en el **Procesador de eventos** que se ejecuta en el WebJob.

## <a name="azure-storage"></a>Almacenamiento de Azure

La solución utiliza almacenamiento de blobs de Windows Azure para conservar todos los datos de telemetría sin formato y resumidos de los dispositivos de la solución. El panel lee los datos de telemetría de almacenamiento de blobs para rellenar los gráficos. Para mostrar las alertas, el panel lee los datos de almacenamiento de blobs que registre cuando los valores de telemetría superan los valores de umbral configurado. La solución también usa almacenamiento de blobs de grabar los valores de umbral establecido en el panel.

## <a name="webjobs"></a>WebJobs

Además de alojar simuladores de dispositivos, la WebJobs en la solución también hospeda el **Procesador de eventos** que se ejecuta en una WebJob de Azure que controladores de dispositivo de información de mensajes y respuestas de comando. Utiliza:

- Mensajes de información del dispositivo para actualizar el registro de dispositivo (almacenado en la base de datos DocumentDB) con la información de dispositivo actual.
- Mensajes de respuesta de comando para actualizar el historial de comandos de dispositivo (almacenado en la base de datos de DocumentDB).

## <a name="documentdb"></a>DocumentDB

La solución utiliza una base de datos de DocumentDB para almacenar información sobre los dispositivos conectados a la solución. Esta información incluye los metadatos de dispositivo y el historial de comandos que se envían a los dispositivos desde el panel.

## <a name="web-apps"></a>Aplicaciones Web

### <a name="remote-monitoring-dashboard"></a>Panel control remoto
Esta página en la aplicación web usa controles de javascript PowerBI (consulte [PowerBI visuales repo](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar los datos de telemetría de los dispositivos. La solución utiliza el trabajo de telemetría ASA para escribir los datos de telemetría para el almacenamiento de blobs.


### <a name="device-administration-portal"></a>Portal de administración de dispositivos

Esta aplicación web le permite:

- Aprovisionar un nuevo dispositivo. Esta acción establece el identificador único del dispositivo y genera la clave de autenticación. Escribe información acerca del dispositivo en el registro de identidad IoT concentrador y la base de datos de DocumentDB específica de la solución.
- Administrar propiedades de dispositivos. Esta acción incluye la visualización de las propiedades existentes y actualizar con nuevas propiedades.
- Enviar comandos a un dispositivo.
- Ver el historial de comando de un dispositivo.
- Habilitar y deshabilitar dispositivos.

## <a name="next-steps"></a>Pasos siguientes

Las siguientes entradas de blog de TechNet proporcionan más detalles sobre la solución de preconfigurados supervisión:

- [Supervisión remota IoT Suite - en Mostrar opciones avanzadas:](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [Conjunto de IoT - supervisión remota - Agregar dispositivos directo y simulados](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Puede seguir Introducción a Suite IoT lea los siguientes artículos:

- [Conectar el dispositivo a la solución preconfigurada supervisión remota][lnk-connect-rm]
- [Permisos en el sitio de azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md