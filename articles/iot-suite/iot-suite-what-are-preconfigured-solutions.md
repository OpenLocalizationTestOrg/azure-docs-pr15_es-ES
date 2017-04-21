<properties
 pageTitle="Soluciones preconfiguradas de Azure IoT | Microsoft Azure"
 description="Una descripción de la IoT Azure había preconfigurado soluciones y su arquitectura con vínculos a recursos adicionales."
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
 ms.date="08/09/2016"
 ms.author="dobett"/>

# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>¿Cuáles son las soluciones de Azure IoT Suite preconfigurado?

Las soluciones de Azure IoT Suite preconfiguradas son implementaciones de patrones de solución IoT comunes que puede implementar en Azure con su suscripción. Puede usar las soluciones preconfiguradas:

- Como punto de partida para sus propias soluciones IoT.
- Para obtener información sobre patrones comunes de desarrollo y diseño de la solución IoT.

Cada solución preconfigurado es una implementación completa-to-end que utiliza dispositivos simulados para generar telemetría.

Además de implementación y ejecución de las soluciones de Azure, puede descargar el código fuente completo y, a continuación, personalizar y ampliar la solución para satisfacer sus requisitos de IoT específicos.

> [AZURE.NOTE] Para implementar una de las soluciones preconfiguradas, visite [Microsoft Azure IoT Suite][lnk-azureiotsuite]. El artículo [Introducción a las soluciones IoT preconfigurado] [ lnk-getstarted-preconfigured] proporciona más información sobre cómo implementar y ejecutar una de las soluciones.

La siguiente tabla muestra cómo se asignan las soluciones para características específicas de IoT:

| Solución | Recopilación de datos | Dispositivo identidad | Comando y Control | Reglas y acciones | Análisis predictivo |
|------------------------|-----|-----|-----|-----|-----|
| [Supervisión remota][lnk-getstarted-preconfigured] | Sí | Sí | Sí | Sí | -   |
| [Mantenimiento predictivo][lnk-predictive-maintenance] | Sí | Sí | Sí | Sí | Sí |

- *Recopilación de datos*: entrada de datos a escala en la nube.
- *Dispositivo identidad*: administrar identidades únicas de todos los dispositivos conectados.
- *Comando y control*: enviar mensajes a un dispositivo de la nube para hacer que el dispositivo a tomar algunas medidas.
- *Reglas y acciones*: back-end solución utiliza reglas para actuar en datos específicos de dispositivo a la nube.
- *Análisis predictivo*: se aplica el back-end de solución analizan los datos del dispositivo a la nube para predecir cuándo deben realizarse acciones específicas. Por ejemplo, análisis de telemetría de motor de avión para determinar cuándo vence mantenimiento de motor.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Supervisión remota preconfigurado general de la solución

Hemos elegido abordar la solución de supervisión preconfigurada en este artículo porque muestra muchos elementos de diseño comunes que comparten las otras soluciones.

El siguiente diagrama muestra los elementos clave de la solución de supervisión. Las siguientes secciones proporcionan más información sobre estos elementos.

![Arquitectura de la solución de supervisión preconfigurado remoto][img-remote-monitoring-arch]

## <a name="devices"></a>Dispositivos

Cuando se implementa la solución preconfigurada supervisión remota, cuatro dispositivos simulados se aprovisiona previamente en la solución que simular un dispositivo de refrigeración. Estos dispositivos simulados tienen un modelo de temperatura y humedad integrado que emite telemetría. Estos dispositivos simulados se incluyen para ilustrar el flujo de llevar a cabo de datos a través de la solución y proporcionar un origen conveniente de telemetría y un destino para comandos si es programador de back-end con la solución como punto de partida para una implementación personalizada.

Cuando un dispositivo primero se conecta al concentrador IoT en la solución preconfigurada supervisión remota, el mensaje de información de dispositivo enviado al concentrador IoT enumera la lista de comandos que puede responder el dispositivo. En la solución preconfigurada supervisión remota, los comandos son: 

- *Dispositivo de ping*: el dispositivo responde a este comando de confirmación. Esto es útil para comprobar que el dispositivo está todavía activo y escuchando.
- *Iniciar telemetría*: indica que el dispositivo para iniciar el envío de telemetría.
- *Dejar de telemetría*: indica que el dispositivo para detener el envío de telemetría.
- *Cambiar punto establecido temperatura*: controla los valores de telemetría de temperatura simulado envía el dispositivo. Esto es útil para probar la lógica de back-end.
- *Diagnóstico de telemetría*: controla si el dispositivo debe enviar la temperatura externa como telemetría.
- *Cambiar el estado del dispositivo*.: establece la propiedad de metadatos de estado de dispositivo que informa sobre el dispositivo. Esto es útil para probar la lógica de back-end.

Puede agregar más dispositivos simulados la solución que emita el misma telemetría y responder a los comandos de la misma. 

## <a name="iot-hub"></a>Concentrador IoT

En esta solución preconfigurada, la instancia de concentrador IoT corresponde a la *Puerta de enlace de nube* en una típica [arquitectura de la solución IoT][lnk-what-is-azure-iot].

Un concentrador IoT recibe telemetría de los dispositivos en un solo extremo. Un concentrador IoT también mantiene extremos específicos de dispositivo donde cada dispositivos pueden recuperar los comandos que se envían a él.

El concentrador IoT habilita la telemetría recibida a través de la telemetría del servicio leer extremo.

## <a name="azure-stream-analytics"></a>Análisis de secuencia de Azure

La solución preconfigurada utiliza tres [Análisis de secuencia de Azure] [ lnk-asa] trabajos (ASA) para filtrar la secuencia de telemetría de los dispositivos:


- *Trabajo DeviceInfo* - envía los datos a un concentrador de evento que redirige el dispositivo registro determinados mensajes, enviados cuando se conecta un dispositivo por primera vez o en respuesta a un comando **Cambiar estado del dispositivo** , en el registro de dispositivo de la solución (una base de datos DocumentDB). 
- *Trabajo de telemetría* - envía todos telemetría sin formato con el almacenamiento de blobs de Windows Azure para el almacenamiento de fría y calcula agregaciones de telemetría que mostrar en el panel de la solución.
- *Trabajo de reglas* : filtra la secuencia de telemetría para los valores que superen los umbrales de la regla y envía los datos a un concentrador de evento. Cuando se activa una regla, la vista del panel portal de solución muestra este evento como una nueva fila en la tabla del historial de alarma y activa una acción en función de la configuración definida en las vistas de reglas y acciones en el portal de solución.

En esta solución preconfigurada, los trabajos ASA forman parte de la **solución IoT back-end** en una típica [arquitectura de la solución IoT][lnk-what-is-azure-iot].

## <a name="event-processor"></a>Procesador de evento

En esta solución preconfigurada, el procesador de eventos forma parte de la **solución IoT back-end** en una típica [arquitectura de la solución IoT][lnk-what-is-azure-iot].

Los trabajos **DeviceInfo** y **reglas** ASA envían sus resultados a hubs de evento para el envío de otros servicios de back-end. La solución utiliza un [EventPocessorHost] [ lnk-event-processor] instancia, que se ejecuta en una [WebJob][lnk-web-job], para leer los mensajes de estos hubs de evento. La **EventProcessorHost** utiliza los datos de **DeviceInfo** para actualizar los datos de dispositivo de la base de datos de DocumentDB y utiliza los datos de **las reglas** para llamar a la aplicación de la lógica y las alertas que se muestran en el portal de solución de actualización.

## <a name="device-identity-registry-and-documentdb"></a>Registro de identidad de dispositivo y DocumentDB

Cada concentrador IoT incluye un [registro de la identidad de dispositivo] [ lnk-identity-registry] que almacena las claves de dispositivo. Concentrador IoT usa esta información autenticar dispositivos - un dispositivo debe estar registrado y tiene una clave válida antes de que se pueda conectar al concentrador.

Esta solución almacena información adicional acerca de los dispositivos como su estado, los comandos que admiten y otros metadatos. La solución utiliza una base de datos de DocumentDB para almacenar los datos específicos de la solución de dispositivo y el portal de solución recupera datos de esta base de datos de DocumentDB para mostrar y editar.

La solución también debe conservar la información en el registro de la identidad de dispositivo sincronizado con el contenido de la base de datos de DocumentDB. La **EventProcessorHost** usa los datos de trabajo de análisis de secuencia de **DeviceInfo** para administrar la sincronización.

## <a name="solution-portal"></a>Portal de solución

![Panel de la solución][img-dashboard]

El portal de soluciones es una interfaz de usuario basada en web que se implementa en la nube como parte de la solución preconfigurada. Le permite:

- Ver el historial de telemetría y alarma en un panel.
- Aprovisionar nuevos dispositivos.
- Administrar y supervisar los dispositivos.
- Enviar comandos dispositivos específicos.
- Administrar reglas y acciones.

En esta solución preconfigurada, el portal de solución forma parte de la **solución IoT back-end** y parte del **procesamiento y conectividad empresarial** en la típica [arquitectura de la solución IoT][lnk-what-is-azure-iot].

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre IoT arquitecturas de solución, consulte [Servicios de Microsoft Azure IoT: arquitectura de referencia][lnk-refarch].

Ahora que sabe qué es una solución preconfigurada es, puede empezar por implementar la solución de *supervisión remota* preconfiguradas: [Introducción a las soluciones preconfiguradas][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md