<properties
    pageTitle="Introducción a Monitor de Azure | Microsoft Azure"
    description="Introducción al uso de Monitor de Azure obtenga información sobre el funcionamiento de los recursos y tomar medidas basado en los datos."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-azure-monitor"></a>Introducción a Monitor de Azure

Monitor de Azure es el servicio de plataforma que proporciona un solo origen para supervisar los recursos de Azure. Monitor de Azure, puede visualizar, la consulta, enrutar, archivar y tomar medidas sobre las mediciones y los registros procedentes de recursos en Azure. Puede trabajar con estos datos utilizando el módulo de portal del Monitor, [Monitor Cmdlets de PowerShell](./insights-powershell-samples.md), [Entre plataformas CLI](insights-cli-samples.md)o [API de REST de Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx). En este artículo, le guiaremos a través de algunos de los componentes principales de Azure Monitor, con el portal de demostración.

1. En el portal, vaya a **más servicios** y busque la opción **Monitor** . Haga clic en el icono de estrella para agregar esta opción a la lista de favoritos para que siempre sea fácilmente accesible desde la barra de navegación izquierda.

    ![Supervisar en la lista de servicios](./media/monitoring-get-started/monitor-more-services.png)

2. Haga clic en la opción de **Monitor** para abrir la placa **Monitor** . Este módulo reúne las supervisión configuración y los datos en una sola vista consolidada. En primer lugar, se abre en la sección **registro de actividad** .

    ![Navegación de módulo Monitor](./media/monitoring-get-started/monitor-blade-nav.png)

    > [AZURE.WARNING] Las opciones de **notificaciones de servicio** y **grupos de notificación** que se muestran encima aparecerá sólo a aquellos que se han unido a la vista previa privada de estas características.

    Monitor de Azure tiene tres categorías básicas de datos de supervisión: el **registro de actividad**, **métricas**y **registros de diagnóstico**.

3. Haga clic en el **registro de actividad** para asegurarse de que se muestra la sección de registro de actividad.

    ![Módulo de registro de actividad](./media/monitoring-get-started/monitor-act-log-blade.png)

    El [**registro de actividad**](./monitoring-overview-activity-logs.md) describe todas las operaciones realizadas en los recursos de la suscripción. Usar el registro de actividad, puede determinar la ' qué, quién y cuándo ' para cualquier crear, actualizar o eliminar operaciones en los recursos de la suscripción. Por ejemplo, el registro de actividad indica cuando se ha detenido una aplicación web y quién detenido. Eventos de registro de actividad se almacenan en la plataforma y disponible para consultar 90 días.
   
    Puede crear y guardar las consultas de filtros comunes y luego anclar las consultas más importantes a un panel portal para siempre sepa si se han producido eventos que cumplen los criterios.

4. Filtrar la vista a un grupo de recursos determinado en la última semana, haga clic en el botón **Guardar** .

    ![Guardar consulta de registro de actividad](./media/monitoring-get-started/monitor-act-log-save.png)

5. Ahora, haga clic en el botón de **Pin** .

    ![Haga clic en pin para el registro de actividad](./media/monitoring-get-started/monitor-act-log-pin.png)

    La mayoría de las vistas en este tutorial puede estar anclada a un panel. Esto le permite crear una única fuente de información para datos operativos en los servicios. 

6. Volver al panel del. Ahora puede ver que la consulta (y el número de resultados) se muestran en el panel. Esto es útil si desea ver rápidamente las acciones de alto perfil que se han producido recientemente en su suscripción, por ejemplo. se ha asignado una nueva función o se eliminó una máquina virtual.

    ![Registro de actividad anclado a paneles](./media/monitoring-get-started/monitor-act-log-db.png)

7. Volver a la ventana **Monitor** y haga clic en la sección **métricas** . En primer lugar, debe seleccionar un recurso, filtrado y seleccione con la lista desplegable de opciones en la parte superior de la hoja.

    ![Filtrar recursos para métricas](./media/monitoring-get-started/monitor-met-filter.png)

    Todos los recursos de Azure emiten [**métricas**](./monitoring-overview-metrics.md). Esta vista reúne todas las medidas en un lugar único para que pueda comprender fácilmente el rendimiento de los recursos.

8. Una vez que haya seleccionado un recurso, todas las medidas disponibles aparecerán en el lado izquierdo de la hoja. Puede varias métricas del gráfico a la vez seleccionando métricas y modificar el intervalo de tipo y la hora del gráfico. También puede ver todas las alertas de métrica establecer en este recurso.

    ![Métrica placa](./media/monitoring-get-started/monitor-metric-blade.png)

    > [AZURE.NOTE] Algunas métricas solo están disponibles habilitando [Perspectivas de aplicación](../application-insights/app-insights-overview.md) o Windows o Linux diagnóstico de Azure en el recurso.

9. Cuando esté satisfecho con el gráfico, puede usar el botón **alfiler** para anclar al escritorio.

10. Vuelva a la hoja de **Monitor** y haga clic en **registros de diagnóstico**.

    ![Módulo de registros de diagnóstico](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**Registros de diagnóstico**](monitoring-overview-of-diagnostic-logs.md) son registros emitidos *por* un recurso que proporcionan datos sobre el funcionamiento de ese recurso. Por ejemplo, contadores de regla de grupo de seguridad de red y los registros de flujo de trabajo de aplicación lógica son ambos tipos de registros de diagnóstico. Estos registros se pueden almacenar en una cuenta de almacenamiento, transmite a un concentrador de evento o enviados a [Análisis de registro](../log-analytics/log-analytics-overview.md). Análisis de registro es producto de inteligencia operativas de Microsoft para la búsqueda avanzada y alertas.
   
    En el portal puede ver y filtrar una lista de todos los recursos en su suscripción a identificar si tienen los registros de diagnóstico habilitados.

11. Haga clic en un recurso en el módulo de registros de diagnóstico. Si los registros de diagnóstico que se almacenan en una cuenta de almacenamiento, verá una lista de registros por hora que puede descargarse directamente.

    ![Registros de diagnóstico para un recurso](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    También puede hacer clic en **Configuración de diagnóstico**, que le permite establecer o modificar la configuración de archivado a una cuenta de almacenamiento, transmisión al evento Hubs o enviar a un área de trabajo de análisis de registro.

    ![Habilitar los registros de diagnóstico](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Si ha configurado registros de diagnóstico para el análisis de registro, puede buscarlos en la sección **búsqueda de registros** del portal.

12. Vaya a la sección de **alertas** del módulo Monitor.

    ![módulo de alertas para el público](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Aquí puede administrar todas las [**alertas**](./monitoring-overview-alerts.md) de los recursos de Azure. Esto incluye alertas en métricas, eventos de registro de actividad (en privado preview), pruebas de perspectivas de aplicación web (ubicaciones) y diagnósticos proactivas perspectivas de aplicación. Alertas pueden activar un correo electrónico que se envíen o HTTP POST a una dirección URL de webhook.
   
13. Haga clic en **Agregar aviso métrica** para crear una alerta.

    ![Agregar alerta métrica](./media/monitoring-get-started/monitor-alerts-add.png)

    A continuación, puede anclar una alerta para el panel para ver fácilmente su estado en cualquier momento.

14. La sección Monitor también incluye vínculos a aplicaciones de [Aplicación perspectivas](../application-insights/app-insights-overview.md) y soluciones de administración de [Análisis de registro](../log-analytics/log-analytics-overview.md) . Estos otros productos de Microsoft tienen integración con Monitor de Azure.

15. Si no está utilizando perspectivas de aplicación o el análisis de registro, lo más probable es que el Monitor de Azure tiene una asociación con los productos de alertas y supervisión, el registro actual. Consulte nuestra [página de socios](./monitoring-partners.md) para obtener una lista completa e instrucciones sobre cómo integrar.

Siguiendo estos pasos y anclada todos los mosaicos correspondientes a un panel, puede crear vistas completas de la aplicación y la infraestructura como este:

![Panel de Azure Monitor](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Pasos siguientes
- Lea la [información general del Monitor de Azure](./monitoring-overview.md)
