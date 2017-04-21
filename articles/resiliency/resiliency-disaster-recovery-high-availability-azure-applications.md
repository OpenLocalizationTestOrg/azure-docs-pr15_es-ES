<properties
   pageTitle="Recuperación y alta disponibilidad para las aplicaciones de Azure | Microsoft Azure"
   description="Información general técnica y la información detallada sobre cómo diseñar aplicaciones para alta disponibilidad y recuperación ante desastres de aplicaciones integradas en Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="disaster-recovery-and-high-availability-for-applications-built-on-microsoft-azure"></a>Recuperación y alta disponibilidad de las aplicaciones integradas en Microsoft Azure

##<a name="introduction"></a>Introducción

En este artículo se centra en alta disponibilidad de aplicaciones que se ejecutan en Azure. Una estrategia de alta disponibilidad general también incluye el aspecto de recuperación. Planificación de fallos y problemas en la nube requiere que reconoce los errores rápidamente. A continuación, implementar una estrategia que coincida con su tolerancia de inactividad de la aplicación. Además, debe tener en cuenta el grado de pérdida de datos que puede tolerar la aplicación sin provocar consecuencias de empresa negativos cuando se restaura.

La mayoría de las empresas supongamos que están preparados para los errores temporales y a gran escala. ¿Sin embargo, antes de responder a esa pregunta para usted, su compañía ensaya estos errores? ¿Puede probar la recuperación de bases de datos para asegurarse de que los procesos correctos en su lugar? Probablemente no. Eso es porque la recuperación correcta comienza con una gran cantidad de planeación y arquitectura para implementar estos procesos. Al igual que muchos otros requisitos no funcionales, como la seguridad, recuperación casi nunca obtiene el análisis inicial y la asignación de tiempo que requiere. Además, la mayoría de las empresas no tienen el presupuesto regiones geográfico distribuido con capacidad redundante. Por consiguiente, incluso aplicaciones importantes con frecuencia se excluyen de planeación de recuperación de desastres correcta.

Plataformas de nube, como Azure, proporcionan geográfico dispersas regiones del mundo. Estas plataformas también proporcionan capacidades que admiten una variedad de escenarios de recuperación de desastres y disponibilidad. Ahora, puede especificarse cada aplicación de nube crítica objetivos cuenta para desastres corrección del sistema. Azure tiene resistencia y recuperación integrado en muchos de sus servicios. Debe estudiar estas funciones de plataforma cuidadosamente y completar con estrategias de aplicación.

Este artículo describe los pasos necesarios arquitectura debe realizar para desastres prueba una implementación de Azure. A continuación, puede implementar el proceso de continuidad empresarial más grande. Un plan de continuidad empresarial es un plan para continuar las operaciones en las circunstancias negativos. Esto podría ser un error con la tecnología, como un servicio inactivo o un desastre natural, como un corte de tormenta o power. Resistencia de aplicación para desastres es solo un subconjunto del proceso de recuperación de desastres más grande, como se describe en este documento NIST: [Guía de planificación de contingencia para sistemas de tecnología de información](https://www.fismacenter.com/sp800-34.pdf).

Las secciones siguientes definen distintos niveles de errores, técnicas para tratar con ellos y arquitecturas que admiten estas técnicas. Esta información proporciona la entrada para los procesos de recuperación de desastres y procedimientos, para asegurarse de que la estrategia de recuperación funciona correctamente y eficaz.

##<a name="characteristics-of-resilient-cloud-applications"></a>Características de las aplicaciones de nube flexibles

Una aplicación bien diseñada puede resistir errores de capacidad de un nivel táctico y también tolera estratégicos errores de todo el sistema en el nivel de región. Las secciones siguientes definen la terminología hace referencia a todo el documento para describir varios aspectos de los servicios en la nube y son resistentes.

###<a name="high-availability"></a>Alta disponibilidad

Una aplicación de nube altamente disponible implementa estrategias para absorber la interrupción de dependencias, como los servicios administrados ofrecidas por la plataforma de nube. A pesar de posibles errores de capacidades de la plataforma de nube, este enfoque permite a la aplicación para continuar muestran las características sistemáticas funcionales y no funcionales esperadas. Esto se trata en profundidad en la serie de vídeos de canal 9 [a prueba de errores: pautas para arquitecturas de nube flexibles](https://channel9.msdn.com/Series/FailSafe).

Cuando se implementa la aplicación, debe tener en cuenta la probabilidad de una interrupción de funcionalidad. Además, considere el impacto que tendrá una interrupción en la aplicación desde la perspectiva de la empresa, antes de comenzar profundizar en las estrategias de implementación. Sin vencimiento cuenta el impacto de la empresa y la probabilidad de que alcance la condición de riesgo, la implementación puede ser muy costosas y potencialmente innecesarias.

Considere la posibilidad de automóviles analogía para alta disponibilidad. Incluso piezas de calidad e ingeniería superior no evita errores ocasionales. Por ejemplo, cuando el automóvil Obtiene un neumático plana, se ejecuta el automóvil, pero funciona con la funcionalidad de degradado. Si han planificado para esta repetición posible, puede usar uno de esos neumáticos reserva con marco de finos hasta llegar a un centro de reparación. Aunque el neumático reserva no permite la velocidad, aún puede funcionar vehículo hasta que cambie el neumático. Del mismo modo, un servicio de nube planes de posibles pérdidas de capacidades puede impedir que un problema menor relativamente baja toda la aplicación. Esto es verdadero, incluso si debe ejecutar el servicio de nube con la funcionalidad de degradado.

Existen algunas características clave de servicios de nube altamente disponible: disponibilidad, escalabilidad y tolerancia a errores. Aunque estas características están interrelacionadas, es importante conocer cada uno y cómo contribuir a la disponibilidad general de la solución.

###<a name="availability"></a>Disponibilidad

Una aplicación disponible considera la disponibilidad de su infraestructura subyacente y los servicios dependientes. Aplicaciones disponibles quitar puntos únicos de error mediante la redundancia y diseño flexible. Al ampliar el ámbito para tener en cuenta la disponibilidad en Azure, es importante comprender el concepto de la disponibilidad de la plataforma eficaz. Disponibilidad efectiva considera que los contratos de nivel de servicio (SLA) de cada servicio dependiente y su efecto acumulado de la disponibilidad total del sistema.

Disponibilidad del sistema es la medida del porcentaje de una ventana de tiempo que el sistema pueda funcionar. Por ejemplo, la disponibilidad SLA de al menos dos instancias de una función web o trabajo en Azure es 99,95 por ciento (fuera de 100 por ciento). No medir el rendimiento o la funcionalidad de los servicios que se ejecutan en esos roles. Sin embargo, la disponibilidad de su servicio de nube eficaz también se ve afectada por el SLA distintos de los otros servicios dependientes. Las partes más móviles dentro del sistema, la atención de más que debe realizar para garantizar la aplicación resiliently puede cumplir con los requisitos de disponibilidad de sus usuarios finales.

Tenga en cuenta los siguientes SLA para un servicio de Azure que usa servicios de Azure: proceso, la base de datos de SQL Azure y el almacenamiento de Azure.

|Servicio de Azure|SLA   |Posibles minutos el tiempo de inactividad, mes (30 días)|
|:------------|:-----|:----------------------------------------:|
|Calcular      |99,95%|21,6 minutos                              |
|Base de datos SQL |99,99%|4.3 minutos                              |
|Almacenamiento de información      |99,90%|43,2 minutos                              |

Debe planear para que todos los servicios potencialmente ir hacia abajo en momentos diferentes. En este ejemplo simplificado, el número total de minutos por mes que la aplicación podría estar desconectado es 108 minutos. Un mes de 30 días tiene un total de 43,200 minutos. 108 minutos es.25 porcentaje del número total de minutos en un mes de 30 días (43,200 minutos). Esto le da una disponibilidad eficaz de 99.75% para el servicio de nube.

Sin embargo, mediante técnicas de disponibilidad descritas en este artículo puede mejorar esto. Por ejemplo, si diseñar su aplicación para seguir ejecutando cuando la base de datos de SQL no está disponible, puede quitar de la ecuación. Esto podría significar que la aplicación se ejecuta con las funcionalidades de reducción, por lo que también existen requisitos empresariales para tener en cuenta. Para obtener una lista completa de Azure SLA, consulte [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

###<a name="scalability"></a>Escalabilidad

Escalabilidad afecta directamente a la disponibilidad. Una aplicación que se produce un error en la carga aumentada ya no está disponible. Aplicaciones Scalable están disponibles para satisfacer la creciente demanda con resultados coherentes, en ventanas de tiempo aceptable. Cuando un sistema scalable, escala horizontal o verticalmente para administrar el aumento de carga y mantener un rendimiento coherente. En términos básicos, escala horizontal agrega más máquinas del mismo tamaño (procesador, memoria y ancho de banda), mientras aumenta escalado vertical el tamaño de los equipos existentes. Para Azure, tiene las opciones de escala verticales para seleccionar varios tamaños de equipo para el cálculo. Sin embargo, requiere una implementación de volver a cambiar el tamaño del equipo. Por lo tanto, las soluciones más flexibles están diseñadas para escala horizontal. Esto es cierto para calcular, porque puede aumentar fácilmente el número de instancias de una función web o de trabajo en ejecución. Estas otras instancias controlan aumenta el tráfico a través del portal Web de Azure, secuencias de comandos de PowerShell o código. Base esta decisión en incrementos en las medidas supervisadas específicas. En este escenario, el rendimiento de usuario o métricas no se ven afectados una colocación perceptible bajo carga. Normalmente, los roles de web y trabajador almacenan cualquier estado externamente. Esto permite equilibrio de carga flexible y tratamiento correcto de los cambios a la cuenta de instancia. Escala horizontal también funciona bien con los servicios, como el almacenamiento de Azure, que proporciona opciones en niveles de escala vertical.

Implementaciones de nube deben aparecer como una colección de unidades de escala. Esto permite la aplicación esté elásticos en atender las necesidades de rendimiento de los usuarios finales. Las unidades de escala son más fáciles de visualizar en el servidor web y aplicaciones. Esto es porque Azure ya proporciona nodos de cálculo independiente a través de los roles de web y trabajador. Agregar que más unidades de la escala a la implementación de calcular no provocará los efectos de lado de administración del estado de aplicación, porque no tienen estados unidades de escala de cálculo. Una unidad de escala de almacenamiento es responsable de administrar una partición de datos (estructurados o). Algunos ejemplos de unidades de escala de almacenamiento partición de tabla de Azure, contenedor de blobs de Windows Azure y base de datos de SQL Azure. Incluso el uso de varias cuentas de Azure almacenamiento afecta directamente en la escalabilidad de la aplicación. Debe diseñar un servicio de nube altamente scalable para incorporar unidades de escala de almacenamiento varios. Por ejemplo, si una aplicación utiliza datos relacionales, dividir los datos en varias bases de datos SQL. Esto permite el almacenamiento de información para mantenerse con el modelo de unidad de la escala de cálculo elástico. Asimismo, el almacenamiento de Azure permite partición esquemas que requieren deliberadamente diseños para satisfacer las necesidades de rendimiento de la capa de cálculo de datos. Para obtener una lista de los procedimientos recomendados para diseñar los servicios de nube scalable, consulte [Prácticas recomendadas para el diseño de los servicios a gran escala en servicios de nube de Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

###<a name="fault-tolerance"></a>Tolerancia a errores

Aplicaciones deben asumir que puede cada capacidad de nube dependientes y se enviará en algún momento en el tiempo. Una aplicación de tolerancia a errores de errores detecta y maniobras alrededor de los elementos errores para continuar y devolver los resultados correctos dentro de un período específico. Para condiciones de error temporales, un sistema de tolerancia a errores de errores utiliza una directiva de reintento. Para los errores más graves, la aplicación puede detectar problemas y conmutar hardware alternativo o plan de contingencia hasta que se corrija el error. Una aplicación confiable correctamente puede administrar el error de uno o más elementos y continuar funciona correctamente. Las aplicaciones con tolerancia a errores pueden usar uno o más estrategias de diseño, como la redundancia, la replicación o la funcionalidad degradada.

##<a name="disaster-recovery"></a>Recuperación

Una implementación de nube puede dejar de funcionar debido a un corte sistemático de los servicios dependientes o la infraestructura subyacente. En estas condiciones, un plan de continuidad empresarial activa el proceso de recuperación de desastres. Este proceso normalmente implica personal de operaciones y procedimientos automatizados para reactivar la aplicación en un área disponibles. Esto requiere a la transferencia de los usuarios de la aplicación, datos y servicios a la nueva región. También implica el uso de medios de copia de seguridad o replicación en curso.

Considere la posibilidad de la comparación anterior en comparación con alta disponibilidad a la capacidad de recuperación de un neumático plano mediante el uso de una reserva. En cambio, recuperación implica los pasos realizados después de un bloqueo de vehículo, donde el automóvil ya no está operativo. En ese caso, la mejor solución es encontrar un modo eficaz cambiar automóviles, mediante una llamada a un servicio de viaje o a un amigo. En este escenario, hay probablemente será un retardo mayor en volviendo de viaje. También hay más complejidad en reparar y volver a del vehículo original. En la misma manera, recuperación para otra región es una tarea compleja que implica algún tiempo de inactividad y posibles pérdidas de datos. Para comprender mejor y evaluar estrategias, es importante definir dos términos: objetivo (RPO) de punto de recuperación y objetivo de tiempo de recuperación (RTO).

###<a name="recovery-time-objective"></a>Objetivo de tiempo de recuperación

El RTO es la cantidad máxima de tiempo asignado para restaurar la funcionalidad de la aplicación. Esto se basa en los requisitos empresariales, y está relacionado con la importancia de la aplicación. Aplicaciones empresariales críticas requieren un bajo RTO.

###<a name="recovery-point-objective"></a>Objetivo de punto de recuperación

El RPO es la ventana de tiempo aceptable de pérdida de datos por el proceso de recuperación. Por ejemplo, si el RPO es una hora, completamente debe hacer copia de seguridad o replicar los datos al menos cada hora. Una vez que aparezca la aplicación en una región alternativo, los datos de copia de seguridad puede no aparecer hasta una hora de datos. Como RTO, aplicaciones críticas destinar un cantidad RPO más pequeño.

##<a name="checklist"></a>Lista de comprobación

Vamos a resumir los puntos clave que se han tratado en este artículo (y sus artículos relacionados en [alta disponibilidad](./resiliency-high-availability-azure-applications.md) y [recuperación](./resiliency-disaster-recovery-azure-applications.md) para las aplicaciones de Azure). Este resumen actúe como una lista de comprobación de elementos que debe considerar para su propio disponibilidad y los planes de recuperación de desastres. Estos son los procedimientos recomendados que se han útiles para clientes que buscan seriedad de implementar una solución correcta.

1. Llevar a cabo una evaluación de riesgos para cada aplicación, porque cada uno puede tener requisitos distintos. Algunas aplicaciones más importantes que otros usuarios y pudiera justificar el costo para ellos la arquitectura de recuperación adicional.
1. Use esta información para definir la recuperaci para cada aplicación.
1. Diseño de error, comenzando por la arquitectura de la aplicación.
1. Implementar los procedimientos recomendados para alta disponibilidad, mientras equilibrio de costo, la complejidad y el riesgo.
1. Implementar planes de recuperación de desastres y procesos.
  * Considere la posibilidad de errores que abarquen el nivel de módulo a un corte de nube completa.
  * Establecer estrategias de copia de seguridad para todos los datos de transacciones y referencia.
  * Elija una arquitectura de recuperación de desastres de múltiples sitios.
1. Definir un propietario específico para procesos de recuperación de desastres, automatización y pruebas. El propietario debe administrar y el propietario de todo el proceso.
1. Documente los procesos de manera que se puede repetir fácilmente. Aunque no existe un propietario, varias personas debería poder comprender y seguir los procesos en caso de emergencia.
1. Entrenar al personal para implementar el proceso.
1. Usar simulaciones desastres normal de aprendizaje y validación del proceso.

##<a name="summary"></a>Resumen

Cuando el hardware o las aplicaciones no dentro de Azure, las técnicas y estrategias para trabajar con ellos son diferentes de cuando se produce el error en los sistemas locales. El motivo principal es que las soluciones de nube tienen normalmente más dependencias en infraestructura que se distribuye a través de un área de Azure y administrarse como servicios independientes. Debe tratar errores parciales mediante técnicas de alta disponibilidad. Para administrar los errores más graves, posiblemente debido a un evento de desastre, utilice estrategias.

Azure detecta y controla muchos errores, pero hay muchos tipos de errores que requieren estrategias específicos de la aplicación. Conocidas debe preparar y administrar los errores de datos, aplicaciones y servicios.

Al crear la disponibilidad y el plan de recuperación de la aplicación, tenga en cuenta las consecuencias de empresa de error de la aplicación. Definir los procesos, directivas y procedimientos para restaurar los sistemas críticos después de un evento grave tarda, planificación y compromiso. Y una vez que se establecen los planes, no puede detener allí. Debe analizar con regularidad, probar y mejorar continuamente los planes basados en el conjunto de aplicaciones, necesidades empresariales y las tecnologías disponibles para usted. Azure ofrece nuevas capacidades y genera nuevos desafíos con la creación de aplicaciones sólidas que resistan errores.

##<a name="additional-resources"></a>Recursos adicionales

[Alta disponibilidad para las aplicaciones integradas en Microsoft Azure](resiliency-high-availability-azure-applications.md)

[Recuperación de las aplicaciones integradas en Microsoft Azure](resiliency-disaster-recovery-azure-applications.md)

[Orientación técnica de la resistencia de Azure](resiliency-technical-guidance.md)

[Información general: Empresa continuidad y base de datos de recuperación de base de datos SQL de nube](../sql-database/sql-database-business-continuity.md)

[Alta disponibilidad y recuperación ante desastres para SQL Server en Azure máquinas virtuales de Windows](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)

[A prueba de errores: Guía para arquitecturas de nube flexibles](https://channel9.msdn.com/Series/FailSafe)

[Prácticas recomendadas para el diseño de los servicios a gran escala en servicios de nube de Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

##<a name="next-steps"></a>Pasos siguientes

En este artículo forma parte de una serie de artículos centrados en la recuperación y alta disponibilidad para las aplicaciones de Azure. El siguiente artículo de esta serie es [alta disponibilidad de las aplicaciones integradas en Microsoft Azure](resiliency-high-availability-azure-applications.md).
