<properties
 pageTitle="Tutorial de mantenimiento predictiva | Microsoft Azure"
 description="Un tutorial acerca de la solución de mantenimiento predictiva preconfigurado IoT de Azure."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Tutorial de solución de mantenimiento predictiva preconfigurado

## <a name="introduction"></a>Introducción

La solución de mantenimiento predictiva preconfigurado IoT Suite es una solución de llevar a cabo para un escenario de negocio que prevé el punto cuando es probable que se produzcan errores. Puede usar esta solución preconfigurada proactiva para actividades como la optimización de mantenimiento. La solución combina los servicios de Azure IoT Suite clave, incluidos un [Aprendizaje del equipo de Azure] [ lnk_machine_learning] área de trabajo. Esta área de trabajo contiene experimentación, en función de un conjunto de datos de ejemplo público predecir la vida útil restante (RUL) de un motor de avión. La solución implementa por completo el escenario de empresa IoT como punto de partida para usted planear e implementar una solución que satisfaga sus propios requisitos empresariales específicos.

## <a name="logical-architecture"></a>Arquitectura lógica

El diagrama siguiente resume los componentes lógicos de la solución preconfigurado:

![][img-architecture]

Los elementos de color azules son servicios de Azure aprovisionados en la ubicación que seleccione al aprovisionar la solución preconfigurada. Puede aprovisionar la solución preconfigurada en región oriental de Estados Unidos, Europa del Norte o Asia oriental.

Algunos recursos no están disponibles en las regiones donde se aprovisiona la solución preconfigurada. Los elementos de naranja en el diagrama representan los servicios de Azure aprovisionados en la más cercana disponible región (sur Central de EE, oeste de Europa o Asia Sureste) dada la región seleccionada.

El elemento verde es un dispositivo simulado que representa un motor del avión. Puede obtener más información sobre estos dispositivos simulados en la sección siguiente.

Los elementos de color gris representan componentes que implementan capacidades de *administración de dispositivos* . La versión actual de la solución de mantenimiento predictiva preconfigurado no aprovisionar estos recursos. Para obtener más información acerca de la administración de dispositivos, consulte la [solución preconfigurada de supervisión remota][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Dispositivos simulados

En la solución preconfigurada, un dispositivo simulado representa un motor del avión. La solución se aprovisiona con dos motores de asignan a un único avión. Cada motor emite cuatro tipos de telemetría: Sensor 9, 11 de Sensor, Sensor 14 y 15 de Sensor proporcionan los datos necesarios para el modelo de aprendizaje para calcular la vida útil restante (RUL) para el motor. Cada dispositivo simulado envía los siguientes mensajes de telemetría a IoT concentrador:

*Recuento de ciclo*. Un ciclo representa un vuelo completado de longitud variable entre 2-10 horas en la que los datos de telemetría se genera cada media hora durante el vuelo.

*Telemetría*. Existen cuatro sensores que representan los atributos de motor. Los sensores están etiquetados genérica Sensor 9, 11 de Sensor, Sensor 14 y 15 de Sensor. Estos 4 sensores representan telemetría suficiente para obtener resultados útiles del modelo de aprendizaje para RUL. Este modelo es creado a partir de un conjunto de datos público que incluya datos de sensor motor real. Para obtener más información sobre cómo se creó el modelo de conjunto de datos original, vea la [Plantilla de mantenimiento predictiva de galería de inteligencia de Cortana][lnk-cortana-analytics].

Los dispositivos simulados pueden controlar los siguientes comandos que se envía desde un concentrador IoT:

| Comando | Descripción |
|---------|-------------|
| StartTelemetry | Controla el estado de la simulación.<br/>Inicia el envío de telemetría de dispositivo     |
| StopTelemetry  | Controla el estado de la simulación.<br/>El dispositivo de envío de telemetría se detiene |

Concentrador de IoT proporciona la confirmación de comando de dispositivo.

## <a name="azure-stream-analytics-job"></a>Trabajo de análisis de secuencia de Azure

**Trabajo: telemetría** opera en la secuencia de telemetría dispositivo entrantes con dos instrucciones. La primera selecciona telemetría todos de los dispositivos y envía los datos al almacenamiento desde donde lo se visualiza en la aplicación web de blobs. La segunda instrucción calcula sensor promedio valores en una ventana deslizante de dos minutos y envía estos datos a través del concentrador de evento a un **procesador de evento**.

## <a name="event-processor"></a>Procesador de evento

**Procesador de evento** toma los valores de sensor promedio para un ciclo completado. Las pasadas esos valores a una API que expone el aprendizaje de máquina capacitación modelo para calcular el RUL para un motor.

## <a name="azure-machine-learning"></a>Aprendizaje del equipo de Azure

Para obtener más información sobre cómo se creó el modelo de conjunto de datos original, vea la [Plantilla de mantenimiento predictiva de galería de inteligencia de Cortana][lnk-cortana-analytics].

## <a name="lets-start-walking"></a>Empezaremos algunos

Esta sección le guiará por los componentes de la solución, describe el uso de mayúsculas y minúsculas y proporciona ejemplos.

### <a name="predictive-maintenance-dashboard"></a>Panel de mantenimiento predictiva

Esta página en la aplicación web usa controles de PowerBI JavaScript (vea el [repositorio de efectos visuales de PowerBI][lnk-powerbi]) para visualizar:

- Los datos de salida de los trabajos de análisis de secuencia de almacenamiento de blobs.
- El recuento RUL y desplazarse por el motor del avión.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observar el comportamiento de la solución de nube

En el portal de Azure, vaya al grupo de recursos con el nombre de la solución que eligió para ver los recursos de preparación.

![][img-resource-group]

Cuando aprovisionar la solución preconfigurada, recibirá un correo electrónico con un vínculo al área de trabajo de aprendizaje del equipo. También puede ir al área de trabajo de aprendizaje del equipo desde la [azureiotsuite.com] [ lnk-azureiotsuite] página para la solución aprovisiona cuando está en el estado **Listo** .

![][img-machine-learning]

En el portal de la solución, puede ver que los datos de ejemplo se aprovisiona con cuatro dispositivos simulados para representar dos aviones con dos motores por avión, cada una con cuatro sensores. Cuando en primer lugar, vaya al portal de solución, se detiene la simulación.

![][img-simulation-stopped]

Haga clic en **simulación de inicio** para iniciar la simulación en el que ve el historial de sensor, RUL, ciclos, e historial RUL rellenar el panel.

![][img-simulation-running]

Cuando RUL es menor que 160 (un umbral arbitrario elegido para la demostración), el portal de solución muestra un símbolo de advertencia al lado de la pantalla RUL y resalta el motor del avión en amarillo. Observe cómo los valores RUL tienen una tendencia descendente general general, pero suelen rebote arriba y hacia abajo. Este comportamiento resulta de ciclo de longitud variable y la precisión del modelo.

![][img-simulation-warning]

La simulación completa tarda aproximadamente 35 minutos en completarse 148 ciclos. El umbral RUL 160 se cumple por primera vez alrededor de 5 minutos y ambos motores ha alcanzado el umbral de alrededor de 8 minutos.

La simulación se ejecuta mediante el conjunto de datos completo para 148 ciclos y liquida finales valores RUL y ciclo de.

Puede dejar la simulación en cualquier momento, pero haciendo clic en **Iniciar simulación** reproduce la simulación desde el inicio del conjunto de datos.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha ejecutado la solución de mantenimiento predictiva preconfigurado desea modificarlo, consulte [instrucciones sobre cómo personalizar soluciones preconfiguradas][lnk-customize].

La entrada de blog [IoT Suite - avanzada - predictiva mantenimiento](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) TechNet proporciona detalles adicionales sobre la solución de mantenimiento predictiva preconfigurado.

También puede explorar algunas de las otras características y las capacidades de las soluciones de Suite IoT preconfiguradas:

- [Preguntas más frecuentes sobre IoT Suite][lnk-faq]
- [Seguridad IoT conceptos básicos][lnk-security-groundup]


[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
