<properties
    pageTitle="Entorno de servicio de aplicaciones y ajuste automático | Microsoft Azure"
    description="Entorno de servicio de aplicaciones y ajuste automático"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"
/>

# <a name="autoscaling-and-app-service-environment"></a>Entorno de servicio de aplicaciones y ajuste automático

*Ajuste automático*de soporte técnico de Azure entornos de servicio de aplicación. Puede que los grupos de trabajo individuales de Autoescala basados en métricas o programación.

![Ajustar automáticamente las opciones para un grupo de trabajo.][intro]

Ajuste automático optimiza el uso de recursos automáticamente crecimiento y reducción de un entorno de servicio de aplicación para ajustar su presupuesto o cargar el perfil.

## <a name="configure-worker-pool-autoscale"></a>Configurar Autoescala de grupo de trabajo

Puede acceder a la funcionalidad de Autoescala de la pestaña **configuración** del grupo de trabajo.

![Pestaña Configuración de grupo de trabajo.][settings-scale]

Desde allí, la interfaz debe estar bastante familiarizado, ya que es la misma experiencia que verá cuando ajustar el tamaño de un plan de servicio de aplicación. 

![Configuración de escala manual.][scale-manual]

También puede configurar un perfil Autoescala.

![Ajustar automáticamente la configuración.][scale-profile]

Autoescala perfiles son útiles para establecer los límites de la escala. De este modo, puede tener un rendimiento coherente experiencia estableciendo un valor de escala de límite inferior (1) y un extremo de gastos predecibles estableciendo un límite superior (2).

![Configuración de escala en perfil.][scale-profile2]

Después de definir un perfil, puede agregar reglas de Autoescala escalar hacia arriba o hacia abajo el número de instancias en el grupo de trabajo dentro de los límites definidos por el perfil. Autoescala reglas se basan en métricas.

![Regla de escala.][scale-rule]

 Cualquier grupo de trabajo o métricas front-end pueden usarse para definir reglas Autoescala. Estas métricas son la misma métrica puede supervisar en los gráficos de la hoja de recursos o establecer alertas para.

## <a name="autoscale-example"></a>Ejemplo de Autoescala

Autoescala de un entorno de servicio de la aplicación se puede ilustrar mejor a través de un escenario.

Este artículo explica las consideraciones de necesarias al configurar Autoescala. El artículo le guiará a través de las interacciones que entran en juego al factor de ajuste automático entornos de servicio de aplicaciones que se hospedan en el entorno de aplicación de servicio.

### <a name="scenario-introduction"></a>Introducción de escenario

Antonio es un administrador del sistema para la empresa que ha migrado una parte de las cargas de trabajo que se encarga de un entorno de servicio de aplicación.

El servicio de aplicación entorno a escala manual como sigue:

* **Front-end:** 3
* **Grupo de trabajo 1**: 10
* **Grupo de trabajo 2**: 5
* **Grupo de trabajo 3**: 5

Grupo de trabajo 1 se utiliza para cargas de trabajo de producción, mientras que el grupo de trabajo 2 y 3 de grupo de trabajo se usan para cargas de trabajo de desarrollo y control de calidad (AC).

Los planes de servicio de aplicación de QA y desarrollo están configurados para escala manual. El plan de servicio de aplicaciones de producción se establece en Autoescala a atender las variaciones de carga y tráfico.

Antonio es muy familiarizado con la aplicación. Sabe que las horas de máximo de carga se encuentren entre 9:00 A.M. y 6:00 P.M., porque esta es una aplicación de línea de negocio (LOB) que los empleados utilizar mientras se encuentran en la oficina. Uso quita después de cuando los usuarios se hacen para ese día. Fuera de las horas, hay algunas carga porque los usuarios pueden acceder de forma remota la aplicación con sus dispositivos móviles o equipos domésticos. El plan de servicio de la aplicación de producción ya está configurada para ajustar automáticamente en función de uso de CPU con las siguientes reglas:

![Configuración específica de la aplicación LOB.][asp-scale]

|   **Autoescala perfil: días laborables: plan de servicios de aplicación**     |   **Autoescala perfil: los fines de semana: plan de servicios de aplicación**     |
|   ----------------------------------------------------    |   ----------------------------------------------------    |
|   **Nombre:** Perfil de día de la semana                               |   **Nombre:** Perfil de fin de semana                               |
|   **Ajustar a:** Reglas de programación y rendimiento            |   **Ajustar a:** Reglas de programación y rendimiento            |
|   **Perfil:** Días laborables                                   |   **Perfil:** Fin de semana                                    |
|   **Tipo:** Periodicidad                                    |   **Tipo:** Periodicidad                                    |
|   **Intervalo de destino:** instancias de 5 a 20                     |   **Intervalo de destino:** instancias de 3 a 10                     |
|   **Días:** Lunes, el martes, el miércoles, el jueves, el viernes  |   **Días:** Sábado, el domingo                              |
|   **Hora de inicio:** de 9:00 A.M.                                 |   **Hora de inicio:** de 9:00 A.M.                                 |
|   **Zona horaria:** UTC-08                                   |   **Zona horaria:** UTC-08                                   |
|                                                           |                                                           |
|   **Autoescala regla (escala arriba)**                           |   **Autoescala regla (escala arriba)**                           |
|   **Recursos:** Producción (entorno de servicio de aplicaciones)      |   **Recursos:** Producción (entorno de servicio de aplicaciones)      |
|   **Métrica:** % DE LA CPU                                       |   **Métrica:** % DE LA CPU                                       |
|   **Operación:** Superior al 60%                         |   **Operación:** Superior al 80%                         |
|   **Duración:** 5 minutos                                 |   **Duración:** 10 minutos                                |
|   **Tiempo agregación:** Promedio                           |   **Tiempo agregación:** Promedio                           |
|   **Acción:** Aumentar el número 2                         |   **Acción:** Aumentar el recuento en 1                         |
|   **Interesantes hacia abajo (minutos):** 15                             |   **Interesantes hacia abajo (minutos):** 20                             |
|                                                           |                                                           |
  	|   **Autoescala regla (escala)**                     |   **Autoescala regla (escala)**                         |
|   **Recursos:** Producción (entorno de servicio de aplicaciones)      |   **Recursos:** Producción (entorno de servicio de aplicaciones)      |
|   **Métrica:** % DE LA CPU                                       |   **Métrica:** % DE LA CPU                                       |
|   **Operación:** Menor que 30%                            |   **Operación:** Inferior al 20%                            |
|   **Duración:** 10 minutos                                |   **Duración:** 15 minutos                                |
|   **Tiempo agregación:** Promedio                           |   **Tiempo agregación:** Promedio                           |
|   **Acción:** Reducir el recuento en 1                         |   **Acción:** Reducir el recuento en 1                         |
|   **Interesantes hacia abajo (minutos):** 20                             |   **Interesantes hacia abajo (minutos):** 10                             |

### <a name="app-service-plan-inflation-rate"></a>Tasa de inflación del plan de servicio de aplicaciones

Planes de servicio de la aplicación que se han configurado para ajustar automáticamente hacerlo a una velocidad máxima por hora. Este tipo se puede calcular basándose en los valores proporcionados en la regla Autoescala.

Descripción y calcular la *tasa de inflación del plan de servicio de la aplicación* es importante para Autoescala de entorno de servicio de aplicación porque los cambios de escala a un grupo de trabajo no son instantáneos.

La tasa de inflación del plan de servicio de la aplicación se calcula como sigue:

![Cálculo de tasa de inflación del plan de servicio de aplicación.][ASP-Inflation]

En función de la Autoescala: escala de la regla para el perfil de día de la semana de la producción plan de servicios de aplicación:

![Servicio de aplicación plan la tasa de inflación para días laborables según Autoescala: escala de la regla.][Equation1]

En el caso de la Autoescala: escala de la regla para el perfil de fin de semana de la producción plan de servicios de aplicación, la fórmula se resuelva:

![Servicio de aplicación plan la tasa de inflación para los fines de semana en función de Autoescala: escala de la regla.][Equation2]

También puede calcular este valor para operaciones de escala.

En función de la Autoescala-escala hacia abajo de la regla para el perfil de día de la semana de la producción plan de servicios de aplicación, esto tendría el siguiente aspecto:

![Servicio de aplicación plan la tasa de inflación para días laborables según Autoescala-escala hacia abajo de la regla.][Equation3]

En el caso de la Autoescala-escala hacia abajo de la regla para el perfil de fin de semana de la producción plan de servicios de aplicación, la fórmula se resuelva:  

![Servicio de aplicación plan la tasa de inflación para los fines de semana en función de Autoescala-escala hacia abajo de la regla.][Equation4]

El plan de servicio de la aplicación de producción puede creciendo a una velocidad máxima de ocho instancias por hora durante la semana y cuatro instancias por hora durante el fin de semana. Puede liberar instancias a una velocidad máxima de cuatro instancias por hora durante la semana y seis instancias por hora durante los fines de semana.

Si varios planes de servicio de la aplicación se está alojados en un grupo de trabajo, debe calcular la *tasa de inflación total* como la suma de la tasa de inflación para todos los planes de servicio de la aplicación que se hospeda en ese grupo de trabajo.

![Cálculo de la tasa de inflación total para varios planes de servicio de aplicación hospedada en un grupo de trabajo.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Use la tasa de inflación del plan de servicio de aplicaciones para definir reglas de Autoescala de grupo de trabajo

Grupos de trabajo que alojan necesitan asignar un búfer de capacidad de planes de servicio de la aplicación que se han configurado para ajustar automáticamente. El búfer permite que las operaciones Autoescala reducir y ampliar el plan de servicio de aplicaciones según sea necesario. El mínimo de búfer sería la calculado Total aplicación de servicio planear tasa de inflación.

Puesto operaciones de escala del entorno de servicio de aplicación tardan algún tiempo en aplicarse, cualquier cambio debe cuenta aún más los cambios de petición que pueden llevarse a cabo mientras está en curso una operación de escala. Para adaptarse a esta latencia, se recomienda usar el Total aplicación servicio planear inflación índice calculado como el número mínimo de instancias que se agregan para cada operación Autoescala.

Con esta información, Antonio puede definir el siguiente perfil Autoescala y reglas:

![Ajustar automáticamente las reglas de perfil por ejemplo LOB.][Worker-Pool-Scale]

|   **Autoescala perfil: días laborables**                        |   **Autoescala perfil: los fines de semana**                |
|   ----------------------------------------------------    |   --------------------------------------------    |
|   **Nombre:** Perfil de día de la semana                               |   **Nombre:** Perfil de fin de semana                       |
|   **Ajustar a:** Reglas de programación y rendimiento            |   **Ajustar a:** Reglas de programación y rendimiento    |
|   **Perfil:** Días laborables                                   |   **Perfil:** Fin de semana                            |
|   **Tipo:** Periodicidad                                    |   **Tipo:** Periodicidad                            |
|   **Intervalo de destino:** instancias de 13 y 25                    |   **Intervalo de destino:** instancias de 6 a 15             |
|   **Días:** Lunes, el martes, el miércoles, el jueves, el viernes  |   **Días:** Sábado, el domingo                      |
|   **Hora de inicio:** de 7:00 A.M.                                 |   **Hora de inicio:** de 9:00 A.M.                         |
|   **Zona horaria:** UTC-08                                   |   **Zona horaria:** UTC-08                           |
|                                                           |                                                   |
|   **Autoescala regla (escala arriba)**                           |   **Autoescala regla (escala arriba)**                   |
|   **Recursos:** Grupo de trabajo 1                             |   **Recursos:** Grupo de trabajo 1                     |
|   **Métrica:** WorkersAvailable                            |   **Métrica:** WorkersAvailable                    |
|   **Operación:** Menor que 8                              |   **Operación:** Menos de 3                      |
|   **Duración:** 20 minutos                                |   **Duración:** 30 minutos                        |
|   **Tiempo agregación:** Promedio                           |   **Tiempo agregación:** Promedio                   |
|   **Acción:** Aumentar el recuento en 8                         |   **Acción:** Aumentar el recuento por 3                 |
|   **Interesantes hacia abajo (minutos):** 180                            |   **Interesantes hacia abajo (minutos):** 180                    |
|                                                           |                                                   |
|   **Autoescala regla (escala)**                         |   **Autoescala regla (escala)**                 |
|   **Recursos:** Grupo de trabajo 1                             |   **Recursos:** Grupo de trabajo 1                     |
|   **Métrica:** WorkersAvailable                            |   **Métrica:** WorkersAvailable                    |
|   **Operación:** Mayor que 8                           |   **Operación:** Mayor que 3                   |
|   **Duración:** 20 minutos                                |   **Duración:** 15 minutos                        |
|   **Tiempo agregación:** Promedio                           |   **Tiempo agregación:** Promedio                   |
|   **Acción:** Disminuir el recuento por 2                         |   **Acción:** Disminuir el recuento por 3                 |
|   **Interesantes hacia abajo (minutos):** 120                            |   **Interesantes hacia abajo (minutos):** 120                    |

El rango de destino definido en el perfil se calcula mediante las instancias mínimas definidas en el perfil para el plan de servicios de aplicación + búfer.

El rango máximo sería la suma de todos los rangos máximos para todos los planes de servicio de aplicación hospedados en el grupo de trabajo.

El recuento de aumento de la escala de las reglas se debe establecer al menos 1 X la tasa de inflación del Plan de servicio de aplicación de escala de.

Recuento de disminución puede ajustarse al algo entre 1/2 X o 1 X la tasa de inflación del Plan de servicio de aplicación de escala de abajo.

### <a name="autoscale-for-front-end-pool"></a>Autoescala de grupo de servidores front-end

Reglas para Autoescala front-end son más sencillas que para grupos de trabajo. En primer lugar, debe  
Asegúrese de que la duración de la medida y los temporizadores enfriamiento, tenga en cuenta que las operaciones de escala en un plan de servicio de aplicación no son instantáneas.

En este escenario, Antonio sabe que la tasa de error aumenta front-end que alcancen el 80% de la CPU y configura la regla Autoescala aumentar instancias como sigue:

![Ajustar automáticamente la configuración para un grupo de servidores front-end.][Front-End-Scale]

|   **Autoescala perfil: extremos de frente**              |
|   --------------------------------------------    |
|   **Nombre:** Autoescala: extremos de frente                |
|   **Ajustar a:** Reglas de programación y rendimiento    |
|   **Perfil:** Todos los días                           |
|   **Tipo:** Periodicidad                            |
|   **Intervalo de destino:** instancias de 3 a 10             |
|   **Días:** Todos los días                              |
|   **Hora de inicio:** de 9:00 A.M.                         |
|   **Zona horaria:** UTC-08                           |
|                                                   |
|   **Autoescala regla (escala arriba)**                   |
|   **Recursos:** Grupo de servidores front-end                    |
|   **Métrica:** % DE LA CPU                               |
|   **Operación:** Superior al 60%                 |
|   **Duración:** 20 minutos                        |
|   **Tiempo agregación:** Promedio                   |
|   **Acción:** Aumentar el recuento por 3                 |
|   **Interesantes hacia abajo (minutos):** 120                    |
|                                                   |
|   **Autoescala regla (escala)**                 |
|   **Recursos:** Grupo de trabajo 1                     |
|   **Métrica:** % DE LA CPU                               |
|   **Operación:** Menor que 30%                    |
|   **Duración:** 20 minutos                        |
|   **Tiempo agregación:** Promedio                   |
|   **Acción:** Disminuir el recuento por 3                 |
|   **Interesantes hacia abajo (minutos):** 120                    |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
