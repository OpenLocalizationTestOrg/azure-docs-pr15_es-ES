<properties
    pageTitle="Información general sobre métricas en Microsoft Azure | Microsoft Azure"
    description="Aprenda a personalizar gráficos supervisión en Azure."
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

# <a name="overview-of-metrics-in-microsoft-azure"></a>Información general sobre métricas en Microsoft Azure

Todos los servicios de Azure seguimiento de las mediciones clave que permiten supervisar el estado, el rendimiento, la disponibilidad y el uso de los servicios. Puede ver estas métricas en el portal de Azure, y también puede usar la [API de REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para tener acceso a todo el conjunto de métricas mediante programación.

Para algunos servicios, debe activar los diagnósticos para ver estadísticas. Para que otras personas, como máquinas virtuales, obtendrá un conjunto básico de mediciones, pero necesita para habilitar todo el conjunto de métricas de alta frecuencia. Vea [Habilitar la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para obtener más información.

## <a name="using-monitoring-charts"></a>Uso de gráficos de supervisión

Puede representar cualquiera de las mediciones ellos durante un período de tiempo que elija.

1. En el [Portal de Azure](https://portal.azure.com/), haga clic en **Examinar**y, a continuación, en un recurso que le interesa supervisión.

2. La sección **supervisión** contiene la métrica más importante para cada recurso de Azure. Por ejemplo, una aplicación web tiene **errores y solicitudes**, mientras que una máquina virtual tendría el **porcentaje de CPU** y **disco de lectura y escritura**:  ![lente de supervisión](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. Al hacer clic en cualquier gráfico, se mostrará el módulo de **métrica** . En el módulo, además del gráfico, es una tabla que muestra agregaciones de las mediciones (por ejemplo, promedio, mínima y máxima, durante el intervalo de tiempo que eligió). A continuación que son las reglas de alertas para el recurso.
    ![Métrica placa](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Para personalizar las líneas que aparecen, haga clic en el botón **Editar** en el gráfico, o bien, el comando **Editar gráfico** en el módulo métrico.

5. En el módulo de Editar consulta puede hacer tres cosas:
    - Cambiar el intervalo de tiempo
    - Cambiar la apariencia entre líneas y barras
    - Elija diferentes metics ![Editar consulta](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Cambiar el intervalo de tiempo es tan fácil como seleccionar un rango diferente (como **Última hora**) y haga clic en **Guardar** en la parte inferior de la hoja. También puede elegir **personalizada**, que le permite elegir un período de tiempo en las últimas 2 semanas. Por ejemplo, puede ver la todos dos semanas, o, simplemente 1 hora de ayer. Escriba en el cuadro de texto para escribir una hora diferente.
    ![Intervalo de tiempo personalizado](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Debajo del intervalo de tiempo el canal elija cualquier número de métricas para mostrar en el gráfico.

8. Al hacer clic en Guardar los cambios se guardan para ese recurso. Por ejemplo, si tiene dos máquinas virtuales y cambiar un gráfico en uno, no afectará al otro.

## <a name="creating-side-by-side-charts"></a>Creación de gráficos en paralelo

Con la personalización eficaces en el portal puede agregar tantas gráficos como desee.

1. En el menú **...** en la parte superior de la hoja, haga clic en **Agregar mosaicos**:  
    ![Agregar menú](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. A continuación, se puede seleccionar un gráfico de la **Galería** en el lado derecho de la pantalla:  ![Galería](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Si no ve la métrica que desee, puede siempre agregar una de las mediciones preestablecidos y **Editar** el gráfico para mostrar la métrica que necesita.

## <a name="monitoring-usage-quotas"></a>Supervisar las cuotas de uso

Métricas mayoría mostrar tendencias con el tiempo, pero son de determinados datos, como las cuotas de uso, información de punto en el tiempo con un umbral.

También puede ver las cuotas de uso en el módulo de recursos que tengan las cuotas de:

![Uso](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Al igual que con métricas, puede usar la [API de REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) tener acceso el conjunto completo de las cuotas de uso de programación.

## <a name="next-steps"></a>Pasos siguientes

* [Recibir notificaciones de alerta](insights-receive-alert-notifications.md) cada vez que una métrica cruza un umbral.
* [Habilitar la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en el servicio.
* [Ajustar automáticamente el recuento de instancias de](insights-how-to-scale.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.
* [Supervisar el rendimiento de aplicación](../application-insights/app-insights-azure-web-apps.md) si desea saber exactamente cómo está funcionando su código en la nube.
* Utilice [Perspectivas de aplicación para aplicaciones de JavaScript y páginas web](../application-insights/app-insights-web-track-usage.md) para obtener análisis de cliente acerca de los exploradores que visite una página web.
* [Disponibilidad de monitor y capacidad de respuesta de una página web](../application-insights/app-insights-monitor-web-app-availability.md) con la aplicación perspectivas por lo que puede averiguar si la página es hacia abajo.
