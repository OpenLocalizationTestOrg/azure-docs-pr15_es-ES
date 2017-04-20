<properties
    pageTitle="Ver eventos y registros de auditoría"
    description="Obtenga información sobre cómo ver todos los eventos que se producen en su suscripción de Azure."
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
    ms.date="04/28/2015"
    ms.author="robb"/>

# <a name="view-events-and-audit-logs"></a>Ver eventos y registros de auditoría

Todas las operaciones realizadas en Azure recursos se van a auditar completamente por el Administrador de recursos de Azure, desde la creación y eliminaciones para conceder o revocar el acceso. Puede examinar estos registros en el portal de Azure, y también puede usar la [API de REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) tener acceso al conjunto completo de eventos mediante programación.

## <a name="browse-the-events-impacting-your-azure-subscription"></a>Examinar los eventos que afectan a su suscripción de Azure

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar** y seleccione los **registros de auditoría**.  
    ![Examinar concentrador](./media/insights-debugging-with-events/Insights_Browse.png)
3. Se abrirá una placa que muestra todos los eventos que han afectado a cualquiera de sus suscripciones para los últimos 7 días. En la parte superior es un gráfico que muestra datos por nivel y, a continuación que es la lista completa de registros:  ![todos los eventos](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE] Solo puede ver los eventos más recientes 500 para una determinada suscripción en el portal de Azure.

4. Puede hacer clic en cualquier entrada de registro para ver los eventos que lo componen. Por ejemplo, cuando se implementa algo en un grupo de recursos, muchos recursos diferentes pueden creados o modificados. Para cada entrada puede ver:
    * El **nivel** de evento: por ejemplo, podría ser simplemente algo para realizar un seguimiento (**informativo**) o cuando algo ha ido mal que necesita saber sobre (**Error**).
    * El **estado** - el estado final generalmente será **éxito** o **error**, pero también puede ser **aceptado** para operaciones de ejecución larga.
    * Se produjo *cuando* el evento.
    * *¿Quién* realizó la operación, si todos los usuarios. No todas las operaciones realizadas por los usuarios, algunos realizada por los servicios de back-end para que no tendría un **llamador**.
    * El **Identificador de correlación** del evento: este es el identificador único para este conjunto de operaciones.

5. Desde allí puede ir a la hoja de detalles para ver los detalles del evento.

    ![Grupos de recursos](./media/insights-debugging-with-events/Insights_EventDetails.png)

    Para los eventos de **error** , esta página normalmente incluye un **subestado** y una sección de **Propiedades** que incluyen detalles útiles para la depuración.

## <a name="filter-to-specific-logs"></a>Filtrar registros específicos

Para ver los eventos que se aplican a una entidad concreta o de un tipo específico, puede filtrar el módulo de registros de auditoría haciendo clic en el comando de **filtro** . También se pueden utilizar el módulo de filtro para cambiar el **intervalo de tiempo** del módulo de registros de auditoría.

Una vez que haga clic en este comando, se abrirá un nuevo módulo:

![Filtro](./media/insights-debugging-with-events/Insights_EventFilter.png)

Hay cuatro tipos de filtros:

1. Por suscripción
2. Un **grupo de recursos**
3. Un **tipo de recurso**
4. Un determinado **recurso** - para este deberá anteriores en el *Id. de recurso* de completo del recurso que le interesa

Además, también puede filtrar eventos por quién ha realizado el evento o, en el nivel del evento.

Una vez haya terminado de seleccionar lo que desea ver, haga clic en el botón **Actualizar** en la parte inferior de la hoja.

## <a name="monitor-events-impacting-specific-resources"></a>Eventos del monitor que afectan a recursos específicos

1. Haga clic en **Examinar** para buscar el recurso que le interesa. También puede ver todos los registros de para todo un **grupo de recursos**.
2. En el módulo del recurso, desplácese hacia abajo hasta que encuentre el mosaico de **eventos** .  
    ![Mosaico de eventos](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Haga clic en ese mosaico para ver eventos filtrados a solo los recursos que haya seleccionado. Puede usar el comando **filtro** para cambiar el intervalo de tiempo o aplicar filtros más específicos.

## <a name="next-steps"></a>Pasos siguientes

* [Recibir notificaciones de alerta](insights-receive-alert-notifications.md) cuando se produce un evento.
* [Medidas de servicio del monitor](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.
* [Estado del servicio de seguimiento](insights-service-health.md) para averiguar cuándo Azure ha sufrido interrupciones de servicio o degradación del rendimiento.  
