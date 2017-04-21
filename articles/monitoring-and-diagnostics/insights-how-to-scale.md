<properties
    pageTitle="Escalar recuento de instancias de forma manual o automática | Microsoft Azure"
    description="Obtenga información sobre cómo ajustar el tamaño de los servicios de Azure."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="scale-instance-count-manually-or-automatically"></a>Ajustar el recuento de instancias manual o automáticamente

En el [Portal de Azure](https://portal.azure.com/), puede establecer manualmente el recuento de instancia del servicio o puede establecer parámetros para que automáticamente en función de petición de escala. Normalmente se conoce como *escala fuera* o *en*.

Antes de ajuste de escala basándose en recuento de instancia, debe tener en cuenta que escala se ve afectada por **nivel de precios** además del recuento de la instancia. Pueden tener diferentes niveles de precios memoria y núcleos distintos números y, por lo tanto, estos podrán obtener un mejor rendimiento para el mismo número de instancias (que es la *escala hacia arriba* o *hacia abajo*). Este artículo trata específicamente *escala en la* entrada y *salida*.

Puede cambiar la escala en el portal y también puede usar la [API de REST](https://msdn.microsoft.com/library/azure/dn931953.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para ajustar la escala manual o automáticamente.

> [AZURE.NOTE] En este artículo se describe cómo crear una configuración de Autoescala en el portal en [http://portal.azure.com](http://portal.azure.com). Configuración de Autoescala creada en este portal no se puede editar el portal clásico ([http://manage.windowsazure.com](http://manage.windowsazure.com)).

## <a name="scaling-manually"></a>Escala de forma manual

1. En el [Portal de Azure](https://portal.azure.com/), haga clic en **Examinar**y navegue hasta el recurso que desee escalar, como un **plan de servicios de aplicación**.

2. El mosaico de **escala** en **operaciones** le indicará el estado de ajuste de escala: **desactivar** para cuando se escala manualmente, **en** para cuando se escala por uno o más métricas de rendimiento.
    ![Mosaico de escala](./media/insights-how-to-scale/Insights_UsageLens.png)

3. Al hacer clic en el mosaico le llevará a la hoja de **escala** . En la parte superior de la hoja de escala puede ver un historial de acciones de ajustar automáticamente el servicio.  
    ![Módulo de escala](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

>[AZURE.NOTE] Solo las acciones que se realizan por Autoescala aparecerá en este gráfico. Si ajustar manualmente el recuento de instancia, el cambio no se reflejarán en este gráfico.

4. Puede ajustar manualmente las **instancias** de número con el control deslizante.
5. Haga clic en el comando **Guardar** y deberá escalar a ese número de instancias de casi inmediatamente.

## <a name="scaling-based-on-a-pre-set-metric"></a>Ajuste de escala en función de una métrica preestablecida

Si desea que el número de instancias para ajustar automáticamente en función de una métrica, seleccione la métrica que desee en la lista desplegable de **escala por** . Por ejemplo, para un **plan de servicios de aplicación** puede escalar por **Porcentaje de CPU**.

1. Cuando se selecciona una métrica obtendrá un control deslizante o cuadros de texto para escribir el número de instancias que desee escalar entre:

    ![Módulo de escala con el porcentaje de CPU](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

    Autoescala nunca tendrá su servicio debajo o por encima de los límites que establece, independientemente de la carga.

2. En segundo término, seleccione el rango de destino para la métrica. Por ejemplo, si ha elegido el **porcentaje de CPU**, puede establecer un destino para la media de la CPU en todas las instancias en el servicio. Un escalado sucederá cuando la media de la CPU supera el máximo que defina, del mismo modo, ocurrirá una escala en cada vez que el promedio de CPU baje el mínimo.

3. Haga clic en el comando **Guardar** . Autoescala deberá comprobar cada pocos minutos para asegurarse de que se encuentra en el rango de la instancia y el destino de la métrica. Cuando el servicio recibe tráfico adicional, obtendrá más instancias sin hacer nada.

## <a name="scale-based-on-other-metrics"></a>Escala basada en otras métricas

Puede escalar métricas en función de distintos de los valores preestablecidos que aparecen en la lista desplegable **Ajustar a** y pueden incluso tiene un conjunto complejo de escalado y escalar en reglas.

### <a name="adding-or-changing-a-rule"></a>Agregar o cambiar una regla

1. Seleccione las **reglas de programación y rendimiento** en el menú desplegable de **escala por** : ![reglas de rendimiento](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. Si previamente tenía Autoescala, en verá una vista de las reglas exactas que tenía.

3. Para cambiar la escala en función de otro clic métrica la fila de **Agregar regla** . También puede hacer clic en una de las filas existentes para cambiar de la métrica a que previamente a la métrica que desea ajustar.
![Agregar regla](./media/insights-how-to-scale/Insights_AddRule.png)

4. Ahora debe seleccionar qué métrica desea ajustar a. Al elegir una métrica hay un par de cosas para tener en cuenta:
    * El *recurso* . que procede de la métrica. Normalmente, esto será el mismo que el recurso que se ajuste de escala. Sin embargo, si desea ajustar la profundidad de una cola de almacenamiento, el recurso es la cola que desea ajustar a.
    * *Métrica nombre* propio.
    * *Agregación de tiempo* de la métrica. Esto es cómo los datos combinar durante la *duración*.

5. Después de elegir la métrica elija el umbral de la métrica y el operador. Por ejemplo, podría decir **mayor que** **80%**.

6. A continuación, elija la acción que desee realizar. Hay un par de otro tipo de acciones:
    * Aumentar o disminuir según - esto se agregue o quite el **valor** numérico de instancias que defina
    * Aumentar o disminuir porcentaje: cambiará el recuento de instancia por un porcentaje. Por ejemplo, podría colocar 25 en el campo de **valor** y si actualmente tenía 8 instancias, se agregaría 2.
    * Aumentar o disminuir a - Esto establecerá el recuento de instancia en el **valor** que defina.

7. Por último, puede elegir buenas hacia abajo - ¿cuánto tiempo debe esperar esta regla después de la acción anterior de escala escalar nuevamente.

8. Después de configurar su regla haga clic en **Aceptar**.

9. Una vez que haya configurado todas las reglas que desea, asegúrese de presionar el comando **Guardar** .

### <a name="scaling-with-multiple-steps"></a>Escala con varios pasos

Los ejemplos anteriores son muy básicos. Sin embargo, si desea que sean más rigurosa sobre la escala hacia arriba (o hacia abajo), puede incluso agregar varias reglas de escala de la misma métrica. Por ejemplo, puede definir dos reglas de escala de porcentaje de CPU:

1. Escalar por 1 instancia si el porcentaje de CPU está por encima de 60%
2. Escalar instancias 3 si el porcentaje de CPU es superior al 85%

![Varias reglas de escala](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Con esta regla adicional, si la carga supera el 85% antes de una acción de escala, obtendrá dos instancias adicionales en lugar de uno.

## <a name="scale-based-on-a-schedule"></a>Escala basada en una programación


De forma predeterminada, al crear una regla de escala se aplicarán siempre. Puede ver al hacer clic en el encabezado de perfil:

![Perfil](./media/insights-how-to-scale/Insights_Profile.png)

Sin embargo, desea tener más rigurosa escala durante el día o la semana que el fin de semana. Incluso puede apagar su servicio de horas de trabajo por completo.

1. Para ello, en el perfil que tiene, seleccione **Periodicidad** en lugar de **siempre** y elija las horas que desea aplicar el perfil.

2. Por ejemplo, para tener un perfil que se aplica durante la semana, en el menú desplegable de **días** desactive **sábado** y **domingo**.

3. Para que un perfil que se aplica durante el día, establezca la **hora de inicio** a la hora del día que desee que empiece en.
    ![Periodicidad predeterminado](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. Haga clic en **Aceptar**.

5. A continuación, debe agregar el perfil que desea aplicar en otro momento. Haga clic en la fila de **Agregar el perfil** .
    ![Día libre](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. Nombre de su nueva, segundo, perfil, por ejemplo podría llamar **de trabajo**.

7. A continuación, vuelva a seleccionar **Periodicidad** y elija el intervalo de recuento de instancia que desee durante este período.

8. Como con el perfil predeterminado, seleccione los **días** que desea aplicar a este perfil y la **hora de inicio** durante el día.

>[AZURE.NOTE] Autoescala utiliza las reglas de ahorro de verano para la **zona horaria** que seleccione. Sin embargo, durante el horario de verano el desplazamiento de UTC mostrará el desplazamiento de zona horaria base, no el desplazamiento de UTC de ahorro de verano.

9. Haga clic en **Aceptar**.

10. Ahora, debe agregar las reglas que desea aplicar durante el segundo perfil. Haga clic en **Agregar regla**y, a continuación, puede crear la misma regla que tiene durante el perfil predeterminado.
    ![Agregar regla para desactivar el trabajo](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. Asegúrese de crear una regla de ambos para escala horizontal y escala en, o bien durante el perfil de la cuenta de instancias sólo aumentar (o disminuir).

12. Por último, haga clic en **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

* [Medidas de servicio del monitor](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.
* [Habilitar la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en el servicio.
* [Recibir notificaciones de alerta](insights-receive-alert-notifications.md) cuando se producen eventos operativos o métricas entre un umbral.
* [Supervisar el rendimiento de aplicación](../application-insights/app-insights-azure-web-apps.md) si desea saber exactamente cómo está funcionando su código en la nube.
* [Ver eventos y registros de auditoría](insights-debugging-with-events.md) para obtener todo lo que ha sucedido en el servicio.
* [Disponibilidad de monitor y capacidad de respuesta de una página web](../application-insights/app-insights-monitor-web-app-availability.md) con la aplicación perspectivas por lo que puede averiguar si la página es hacia abajo.
