<properties
 pageTitle="Mantenimiento predictivo preconfigurado solución | Microsoft Azure"
 description="Descripción de la solución de mantenimiento predictiva preconfigurado IoT de Azure."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
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

# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Información general de solución de mantenimiento predictiva preconfigurado

La solución de *mantenimiento predictivo* preconfigurado es una de las [soluciones preconfiguradas] [ lnk_preconfigured_solutions] publicado como parte del [Conjunto de programas de Microsoft Azure IoT][lnk_iot_suite]. Esta solución integra colección de telemetría de dispositivo en tiempo real con un modelo de predicción creado con [El aprendizaje Azure][lnk_machine_learning].


Con Azure IoT Suite, las empresas pueden rápida y fácilmente conectarse a y supervisar los activos y analizar datos en tiempo real. La solución de mantenimiento predictiva preconfigurado toma datos y utiliza paneles enriquecidos y visualizaciones para proporcionar a las empresas con inteligencia nuevo que puede impulsar una eficiencia y mejorar los flujos de ingresos.

## <a name="the-scenario"></a>El escenario

Fabrikam es una de las aerolíneas regional que se centra en la experiencia del cliente excelente a precios competitivos. Una causa de retrasos de vuelo es problemas de mantenimiento y el mantenimiento del motor de avión es especialmente difícil. Error de motor durante vuelo debe evitar a toda costa para que Fabrikam inspecciona sus motores con regularidad y se agrega a un programa de mantenimiento programado. Sin embargo, los motores de avión no llevar siempre el mismo. Algunos mantenimiento innecesario se realiza en motores. Lo más importante, surgen problemas que puede masa un avión hasta que se realiza el mantenimiento. Esto hace demoras costosas, especialmente si se trata de un avión en una ubicación donde el derecho técnicos o los componentes no están disponibles.

Los motores de avión de Fabrikam se instrumentan con sensores que supervisión condiciones del motor durante el vuelo. Fabrikam use Azure IoT Suite para recopilar los datos de sensor recopilados durante el vuelo. Después de acumular años de motor operativa y datos de error, científicos de datos de Fabrikam han modelado una manera de predecir la vida útil restante (RUL) de un motor de avión. ¿Qué ha identificado es una relación entre los datos de cuatro de los sensores motor con la ropa de motor de clientes potenciales para el error final. Mientras sigue Fabrikam llevar a cabo inspecciones periódicas para garantizar la seguridad, ahora puede usar los modelos para calcular la RUL para cada motor después de cada vuelo utilizando la telemetría recopilado los motores durante el vuelo. Fabrikam ahora puede predecir futuros puntos de error y plan de mantenimiento y reparar con anticipación.

> [AZURE.NOTE] El modelo de solución usa datos de uso real motor.

Por el punto de la predicción cuando se requiere mantenimiento, Fabrikam puede optimizar sus operaciones para reducir los costos. Los coordinadores de mantenimiento trabajan con programadores para planear mantenimiento coincide con un avión detener en una ubicación concreta y garantizar el tiempo suficiente para que el avión a estar fuera de servicio sin causar interrupción de programación. Fabrikam puede programar a técnicos en consecuencia; avión asegurarse de que es atendidas eficazmente sin tiempo de espera. Los administradores de control de inventario reciban planes de mantenimiento, por lo que puede optimizar su proceso de pedidos y se reserva el inventario de elementos. Todo esto permite Fabrikam para minimizar el tiempo de la superficie de aviones y a reducir los costos operativos al garantizar la seguridad de personas y personal.

Para comprender cómo [El conjunto de aplicaciones de Azure IoT] [ lnk_iot_suite] proporciona los clientes capacidades que necesitan obtener el potencial de mantenimiento predictivo, revise esta [infographic][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Cómo se crea la solución de mantenimiento predictiva

La solución aprovecha un modelo de aprendizaje de Azure máquina existente disponible como una plantilla para mostrar estas capacidades de telemetría dispositivo recolectado a través de los servicios de IoT Suite. Microsoft ha creado un [modelo de regresión] [ lnk_regression_model] de un motor de avión y publicar la plantilla completa, datos<sup>\[1\]</sup>y las instrucciones paso a paso sobre cómo usar el modelo.

La solución de mantenimiento predictiva preconfigurado Azure IoT utiliza el modelo de regresión creado a partir de esta plantilla; es implementado en su suscripción de Azure y se expone a través de una API generada automáticamente. La solución incluye un subconjunto de los datos de pruebas que representa 4 (del total de 100) motores y 4 (de 21 total) flujos de datos de sensor que proporcionan un resultado exacto del modelo capacitado.

*\[1\] r. Saxena y K. Goebel (2008). "Conjunto de datos de simulación de turbofan motor degradación", repositorio de datos NASA Ames Prognostics (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), el centro de investigación de NASA Ames, Moffett campo, CA*

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de cómo Azure IoT permite escenarios de mantenimiento predictiva, lea [capturar valor desde Internet de lo][lnk_capture_value].

Realizar un [tutorial] [ lnk-predictive-walkthrough] del mantenimiento predictiva preconfigurado solución.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

También puede explorar algunas de las otras características y las capacidades de las soluciones de Suite IoT preconfiguradas:

- [Preguntas más frecuentes sobre IoT Suite][lnk-faq]
- [Seguridad IoT conceptos básicos][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
