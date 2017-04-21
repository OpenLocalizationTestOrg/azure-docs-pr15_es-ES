<properties
 pageTitle="Guía de programador - trabajos | Microsoft Azure"
 description="Guía de programador de Azure IoT concentrador - programar trabajos para que se ejecute en varios dispositivos conectados a su centro"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="schedule-jobs-on-multiple-devices-preview"></a>Programar trabajos en varios dispositivos (vista preliminar)

## <a name="overview"></a>Información general

Como se describe en artículos anteriores, Azure IoT concentrador permite un número de bloques de creación ([etiquetas y propiedades de dispositivos doble] [ lnk-twin-devguide] y [métodos de dispositivo de nube][lnk-dev-methods]).  Normalmente, las aplicaciones de back-end de IoT permiten administradores de dispositivo y operadores actualizar e interactuar con los dispositivos IoT en masa y en un momento determinado.  Trabajos encapsulan la ejecución de métodos de C2D con un conjunto de dispositivos y actualizaciones de doble de dispositivos en un momento de programación.  Por ejemplo, un operador usaría una aplicación de back-end que desea iniciar y realizar un seguimiento de un trabajo para un conjunto de dispositivos en generar 43 y floor 3 en un momento en que no estaría interrupciones a las operaciones de la creación de reiniciar.

### <a name="when-to-use"></a>Cuándo usar

Considere la posibilidad de usar los trabajos al: solución back end necesidades para programar y realizar un seguimiento del progreso de cualquiera de las acciones siguientes en un conjunto de dispositivos:

- Actualizar propiedades de doble deseado del dispositivo
- Actualizar las etiquetas del dispositivo doble
- Invocar métodos C2D

## <a name="job-lifecycle"></a>Ciclo de vida de trabajo

Trabajos son iniciados por el back-end de solución y mantiene IoT concentrador.  Puede iniciar un trabajo a través de un URI de orientación de servicio (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-09-30-preview`) y para consultar el progreso en una tarea de ejecución a través de un URI de orientación de servicio (`{iot hub}/jobs/v2/<jobId>?api-version=2016-09-30-preview`).  Una vez que se inicia un trabajo, la consulta de trabajos permita que la aplicación de back-end actualizar el estado de la ejecución de trabajos.

> [AZURE.NOTE] Al iniciar un trabajo, nombres y los valores sólo pueden contener EE imprimible alfanuméricos, excepto en el siguiente conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

## <a name="reference-topics"></a>Temas de referencia:

Los siguientes temas de referencia le proporciona más información sobre el uso de tareas.

## <a name="jobs-to-execute-direct-methods"></a>Trabajos para ejecutar métodos directos

Los siguientes son los detalles de solicitud de HTTP 1.1 para ejecutar un [método directo] [ lnk-dev-methods] en un conjunto de dispositivos con un trabajo:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            timeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
    
## <a name="jobs-to-update-device-twin-properties"></a>Trabajos actualizar propiedades de dispositivos doble

A continuación se muestra los detalles de la solicitud de HTTP 1.1 para actualizar propiedades de dos dispositivos con un trabajo:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Consultar el progreso de tareas

El siguiente es los detalles de la solicitud de HTTP 1.1 para [consultar para trabajos][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-09-30-preview[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```
    
Se proporciona la continuationToken de la respuesta.  

## <a name="jobs-properties"></a>Propiedades de trabajos

A continuación se muestra una lista de propiedades y sus correspondientes descripciones, que se pueden utilizar al consultar para trabajos o resultados de la tarea.

| (Propiedad) | Descripción |
| -------------- | -----------------|
| **Id.** | Aplicación había proporcionado ID para el trabajo. |
| **hora de inicio** | Aplicación proporciona la hora de inicio (ISO 8601) para el trabajo. |
| **hora de finalización** | Concentrador IoT había proporcionado fecha (ISO 8601) para que se completó el trabajo. Válido solo después de que el trabajo alcanza el estado 'completado'. | 
| **tipo** | Tipos de tareas: |
| | **scheduledUpdateTwin**: un trabajo que se usa para actualizar un conjunto de etiquetas o propiedades de doble deseado. |
| | **scheduledDeviceMethod**: un trabajo que se usa para llamar a un método de dispositivo en un conjunto de doble. |
| **estado** | Estado actual de la tarea. Valores posibles de estado: |
| | **pendiente** : programar y esperando para recoger el servicio de trabajo. |
| | **programada** : programado para una hora en el futuro. |
| | **Ejecutar** : trabajo actualmente activo. |
| | **ha cancelado** : se ha cancelado. |
| | **error** : error del trabajo. |
| | **completado** : trabajo se ha completado. |
| **deviceJobStatistics** | Estadísticas de ejecución de la tarea. |

Durante la vista previa, el objeto deviceJobStatistics está disponible solo una vez completada la tarea.

| (Propiedad) | Descripción |
| -------------- | -----------------|
| **deviceJobStatistics.deviceCount** | Número de dispositivos en el trabajo. |
| **deviceJobStatistics.failedCount** | Número de dispositivos que ha fallado la tarea. |
| **deviceJobStatistics.succeededCount** | Número de dispositivos donde se realizó correctamente el trabajo. |
| **deviceJobStatistics.runningCount** | Número de dispositivos que se están ejecutando el trabajo. |
| **deviceJobStatistics.pendingCount** | Número de dispositivos que están pendientes para ejecutar el trabajo. |


### <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la Guía del programador incluyen:

- [Extremos de concentrador IoT] [ lnk-endpoints] describe los diversos extremos que expone cada concentrador IoT para operaciones de administración y el tiempo de ejecución.
- [Cuotas y regulación] [ lnk-quotas] describe las cuotas que se aplican al servicio IoT concentrador y el comportamiento de limitación que va a pasar al usar el servicio.
- [SDK de dispositivos y servicios de concentrador IoT] [ lnk-sdks] listas el idioma diversos SDK que un uso al desarrollar aplicaciones dispositivos y servicios que interactúan con IoT concentrador.
- [Lenguaje de consulta para gemelos, métodos y trabajos] [ lnk-query] describe el lenguaje de consulta que se puede usar para recuperar información de concentrador IoT sobre sus twins de dispositivo, métodos y trabajos.
- [Soporte técnico de IoT concentrador MQTT] [ lnk-devguide-mqtt] proporciona más información acerca del soporte técnico de IoT concentrador para el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Si desea probar algunos de los conceptos descritos en este artículo, puede estar interesado en el tutorial de concentrador IoT siguiente:

- [Tareas de programación y difusión][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
