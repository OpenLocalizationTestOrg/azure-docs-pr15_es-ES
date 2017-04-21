<properties
    pageTitle="Cómo supervisar una cuenta de almacenamiento | Microsoft Azure"
    description="Obtenga información sobre cómo supervisar una cuenta de almacenamiento en Azure a través del Portal de Azure."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitor de una cuenta de almacenamiento en el Portal de Azure

## <a name="overview"></a>Información general

Puede supervisar su cuenta de almacenamiento desde el [Portal de Azure](https://portal.azure.com). Al configurar su cuenta de almacenamiento para la supervisión a través del portal, el almacenamiento de Azure usa [El análisis de almacenamiento de](http://msdn.microsoft.com/library/azure/hh343270.aspx) seguimiento de las mediciones para su cuenta y datos de la solicitud de registro.

> [AZURE.NOTE]Costos adicionales están asociados a examinar los datos de seguimiento en el [Portal de Azure](https://portal.azure.com). Para obtener más información, vea <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">análisis de almacenamiento y facturación</a>. <br />

> Azure almacenamiento de archivos actualmente admite métricas de análisis de almacenamiento, pero aún no admite el registro. Puede habilitar la métrica de almacenamiento de archivos de Azure a través del [Portal de Azure](https://portal.azure.com).

> Cuentas de almacenamiento con un tipo de replicación de almacenamiento de zona redundantes (ZRS) no tiene la métrica o la capacidad de registro habilitado en este momento. 

> Para obtener una guía detallada sobre el uso de análisis de almacenamiento y otras herramientas para identificar, diagnosticar y solucionar problemas relacionados con el almacenamiento de Azure, consulte [Monitor, diagnosticar y solucionar problemas de Microsoft Azure almacenamiento](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="how-to-configure-monitoring-for-a-storage-account"></a>Cómo: configurar la supervisión de una cuenta de almacenamiento

1. En el [Portal de Azure](https://portal.azure.com), haga clic en **almacenamiento**y, a continuación, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en **Configurar**y a continuación, desplácese hacia abajo hasta la configuración de **supervisión** para blobs, tabla y servicios de cola.

    ![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. En la **supervisión**, establecer el nivel de supervisión y la directiva de retención de datos para cada servicio:

    -  Para establecer el nivel de supervisión, seleccione uno de estos procedimientos:

      **Mínima** - recopila métricas como porcentajes de entrada/salida, disponibilidad, latencia y éxito, que se agregan para blobs, tabla y servicios de cola.

      **Detallado** - además de las mediciones mínimas, recopila el mismo conjunto de métricas para cada operación de almacenamiento en la API del servicio de almacenamiento de Azure. Métricas detallados habilitar un análisis detallado de los problemas que se producen durante las operaciones de la aplicación.

      **Desactivar** - desactiva supervisión. Se conserva la supervisión de datos hasta el final del período de retención.

- Para establecer la directiva de retención de datos de **retención (en días)**, escriba el número de días de datos para conservar de 1 a 365 días. Si no desea establecer una directiva de retención, escriba cero. Si no hay ninguna directiva de retención, depende de eliminar los datos de supervisión. Se recomienda establecer una directiva de retención basada en cuánto tiempo desea conservar los datos de análisis de almacenamiento de la cuenta para que se pueden eliminar datos analytics antiguas y no utilizadas por sistema sin costo.

4. Cuando haya terminado la configuración de supervisión, haga clic en **Guardar**.

Debe empezar ven supervisar los datos en el panel y la página de **Monitor** después de aproximadamente una hora.

Hasta que configure la supervisión de una cuenta de almacenamiento, datos supervisión que no se recopilaron y los gráficos de métricas en la página de **Monitor** y paneles están vacíos.

Después de configurar las directivas de retención y niveles de supervisión, puede elegir cuál de las mediciones disponibles para supervisar en el [Portal de Azure](https://portal.azure.com)y las métricas que desea trazar en gráficos de métricas. Se muestra un conjunto de métricas predeterminado en cada nivel de supervisión. Puede usar **Métricas agregar** para agregar o quitar métricas de la lista de métricas.

Métricas se almacenan en la cuenta de almacenamiento de cuatro tablas denominado $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue y $MetricsCapacityBlob. Para obtener más información, vea [Información sobre el análisis de métrica de almacenamiento](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## <a name="how-to-customize-the-dashboard-for-monitoring"></a>Cómo: personalizar el panel de control

En el panel, puede elegir hasta seis métricas trazar en el gráfico de métrica de nueve métricas disponibles. Para cada servicio (blob, tabla y cola), están disponibles las métricas de disponibilidad, el porcentaje de éxito y solicitudes de Total. Las métricas disponibles en el panel son los mismos para supervisión mínima o detallado.

1. En el [Portal de Azure](https://portal.azure.com), haga clic en **almacenamiento**y, a continuación, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Para cambiar las medidas que se trazan en el gráfico, realice una de las siguientes acciones:

    - Para agregar una nueva métrica al gráfico, haga clic en la casilla de verificación color junto al encabezado métrica de la tabla debajo del gráfico.

    - Para ocultar una métrica que se traza en el gráfico, desactive la casilla de verificación color junto al encabezado métrica.

        ![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. De forma predeterminada, el gráfico muestra tendencias, mostrar solo el valor actual de cada métrica (la opción **relativa** en la parte superior del gráfico). Para mostrar un eje Y para que pueda ver valores absolutos, seleccione **absolutas**.

4. Para cambiar el intervalo de tiempo métricas gráfico muestra, seleccione 6 horas, 24 horas o 7 días en la parte superior del gráfico.


## <a name="how-to-customize-the-monitor-page"></a>Cómo: personalizar la página de Monitor

En la página **Monitor** , puede ver el conjunto completo de métricas para su cuenta de almacenamiento.

- Si su cuenta de almacenamiento tiene supervisión mínima configurados, se agregan métricas como entrada/salida, disponibilidad, latencia y porcentajes de éxito del blob, tabla y servicios de cola.

- Si su cuenta de almacenamiento tiene supervisión detallado configurado, las métricas están disponibles con una resolución mayor de operaciones de almacenamiento individuales además de los agregados de nivel de servicio.

Use los procedimientos siguientes para elegir qué métrica de almacenamiento para ver en la tabla y los gráficos de métricas que se muestran en la página de **Monitor** . Esta configuración no afectan a la colección, agregación y almacenamiento de datos en la cuenta de almacenamiento de supervisión.

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Cómo: agregar medidas a la tabla de métricas


1. En el [Portal de Azure](https://portal.azure.com), haga clic en **almacenamiento**y, a continuación, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en el **Monitor**.

    Se abrirá la página de **Monitor** . De forma predeterminada, la tabla métricas muestra un subconjunto de las mediciones que están disponibles para supervisar. La ilustración muestra la pantalla del Monitor predeterminado para una cuenta de almacenamiento con supervisión detallado configurado para todos los servicios de tres. Use **Agregar métricas** para seleccionar las medidas que desee supervisar en todas las métricas disponibles.

    ![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

    > [AZURE.NOTE] Tenga en cuenta los costos cuando se selecciona la métrica. Hay transacción y costos de salida asociados a la actualización muestra supervisión. Para obtener más información, vea [análisis de almacenamiento y facturación](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Haga clic en **Agregar métricas**.

    Las mediciones agregadas que están disponibles en supervisión mínima están en la parte superior de la lista. Si está activada la casilla de verificación, la métrica se muestra en la lista de métricas.

    ![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Mantenga el mouse sobre el lado derecho del cuadro de diálogo para mostrar una barra de desplazamiento que puede arrastrar para desplazar métricas adicionales en la vista.

    ![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Haga clic en la flecha hacia abajo por una métrica para expandir una lista de operaciones que la métrica es el ámbito para incluir. Seleccione cada acción que desea ver en la tabla de métricas en el [Portal de Azure](https://portal.azure.com).

    En la siguiente ilustración, se ha ampliado la métrica de porcentaje de ERROR de autorización.

    ![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Después de seleccionar métricas para todos los servicios, haga clic en Aceptar (marca) para actualizar la configuración de supervisión. Las mediciones seleccionadas se agregan a la tabla de métricas.

7. Para eliminar una métrica de la tabla, haga clic en la métrica para seleccionarlo y, a continuación, haga clic en **Eliminar métrica**.

    ![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## <a name="how-to-customize-the-metrics-chart-on-the-monitor-page"></a>Cómo: personalizar el gráfico de métrica en la página Monitor

1. En la página **Monitor** de la cuenta de almacenamiento, en la tabla métricas, seleccione métrico hasta 6 a trazar en el gráfico de métricas. Para seleccionar una métrica, haga clic en la casilla de verificación a su izquierda. Para quitar una métrica del gráfico, desactive la casilla de verificación.

2. Para cambiar el gráfico entre valores relativos (valor final solo muestra) y valores absolutos (el eje Y muestra), seleccione **relativa** o **absoluta** en la parte superior del gráfico.

3.  Para cambiar la hora de rango de la métrica gráfico muestra, seleccione **6 horas**, **24 horas**o **7 días** en la parte superior del gráfico.



## <a name="how-to-configure-logging"></a>Cómo: configurar el registro

Para cada uno de los servicios de almacenamiento disponibles con su cuenta de almacenamiento (blob, tabla y cola), puede guardar los registros de diagnóstico para las solicitudes de lectura, las solicitudes de escritura y eliminar solicitudes y puede establecer la directiva de retención de datos para cada uno de los servicios.

1. En el [Portal de Azure](https://portal.azure.com), haga clic en **almacenamiento**y, a continuación, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en **Configurar**y utilizar la flecha abajo del teclado para desplazarse hacia abajo hasta **el registro**.

    ![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. Para cada servicio (blob, tabla y cola), configure las siguientes opciones:

    - Los tipos de solicitud para iniciar sesión: las solicitudes de lectura, las solicitudes de escritura y eliminar solicitudes.

    - El número de días que se conservarán los datos registrados. Escriba cero es si no desea establecer una directiva de retención. Si no ha establecido una directiva de retención, es decidir si desea eliminar los registros.

4. Haga clic en **Guardar**.

Los registros de diagnósticos se guardan en un contenedor de blob llamado $logs en su cuenta de almacenamiento. Para obtener información sobre el contenedor $logs de acceso, vea [Registro de análisis de almacenamiento](http://msdn.microsoft.com/library/azure/hh343262.aspx).
