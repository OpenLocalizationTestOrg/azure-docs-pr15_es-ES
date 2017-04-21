<properties
 pageTitle="Métrica de diagnóstico IoT concentrador"
 description="Información general sobre métricas de Azure IoT concentrador, habilitar a los usuarios a evaluar el estado general de los recursos"
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

# <a name="introduction-to-diagnostic-metrics"></a>Introducción a las mediciones de diagnóstico

Métrica de diagnóstico dar mejor datos sobre el estado de los recursos de Azure en la suscripción de Azure. Métricas le permiten evaluar el estado general del servicio y los dispositivos conectados a él. Estadísticas de usuario orientación son importantes porque ayudarle a ver qué ocurre con los problemas de causa IoT concentrador y ayuda sin necesidad de ponerse en contacto con soporte técnico de Azure.

Puede habilitar diagnósticos métricas desde el portal de Azure.

## <a name="how-to-enable-diagnostic-metrics"></a>Cómo habilitar métrica de diagnóstico

1. Crear un concentrador IoT. Puede encontrar las instrucciones sobre cómo crear un concentrador IoT en la [Introducción] [ lnk-get-started] guía.

2. Abra el módulo de su centro IoT. Desde allí, haga clic en **Diagnósticos**.

    ![][1]

3. Configurar los diagnósticos de establecer el estado en **On** y seleccionando una cuenta de almacenamiento de Azure para almacenar los datos de diagnóstico. Comprobar **métricas**y, a continuación, presione **Guardar**. Tenga en cuenta que se debe crear la cuenta de almacenamiento de Azure antelación y que se cargará por separado para el almacenamiento. También puede elegir enviar los datos de diagnóstico a un extremo de Hubs de evento.

    ![][2]

4. Después de configurar los diagnósticos, volver al módulo de concentrador IoT **información general** . Información de métricas se rellena en la sección **supervisión** del módulo. Haga clic en el gráfico, se abre el panel de métricas donde puede ver un resumen de la información de métricas para su centro IoT y editar la selección de métricas que se muestra en el gráfico. También puede configurar alertas basadas en los valores de métrica.

    ![][3]

## <a name="metrics-and-how-to-use-them"></a>Métricas y cómo usarlas

Concentrador IoT proporciona varias métricas que le proporcionará información general sobre el estado de su centro y el número total de dispositivos conectados a él. Puede combinar la información de varias métricas para dibujar una imagen del estado del concentrador IoT más grande. La siguiente tabla describe las métricas que realiza un seguimiento de cada concentrador IoT y cómo cada métrica se relaciona con el estado general del concentrador IoT.

| Métrica | Descripción de métrica | Para qué se usa la métrica |
| ---- | ---- | ---- |
| d2c.Telemetry.Ingress.allProtocol | El recuento de mensajes enviados en todos los dispositivos | Descripción general de los datos en el mensaje se envía |
| d2c.Telemetry.Ingress.Success | El recuento de todos los mensajes de éxito al concentrador | Información general de entrada de mensaje con éxito al concentrador |
| c2d.Commands.egress.complete.Success | El recuento de todos los mensajes de comando completado dispositivo receptor en todos los dispositivos | Junto con las mediciones en abandonar y rechazar, ofrece una descripción general de la tasa de éxito de comando de C2D general |
| c2d.Commands.egress.Abandon.Success | El recuento de todos los mensajes correctamente abandonados dispositivo receptor en todos los dispositivos | Resalta posibles problemas si mensajes están recibiendo abandonados con más frecuencia de lo esperado |
| c2d.Commands.egress.Reject.Success | El recuento de todos los mensajes correctamente rechazado por el dispositivo receptor en todos los dispositivos | Resalta posibles problemas si recibe rechazados mensajes con más frecuencia de lo esperado |
| devices.totalDevices | El promedio, Mín y Máx del número de dispositivos registrados al concentrador IoT | El número de dispositivos registrados al concentrador |
| devices.connectedDevices.allProtocol | El promedio, min y max del número de dispositivos conectados simultáneos | Información general sobre el número de dispositivos conectados al concentrador |

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha visto una descripción general de diagnósticos métricas, siga este vínculo para obtener más información sobre la administración de Azure IoT concentrador:

- [Operaciones de supervisión][lnk-monitor]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
