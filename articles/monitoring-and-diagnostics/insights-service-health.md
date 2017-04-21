<properties
    pageTitle="Realizar un seguimiento de estado del servicio de Azure con los registros de actividad del Monitor de Azure | Microsoft Azure"
    description="Averigüe cuándo Azure ha sufrido interrupciones de servicio o degradación del rendimiento. "
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
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Realizar un seguimiento de estado del servicio de Azure con los registros de actividad del Monitor de Azure

Azure publicizes cada vez que hay una degradación del rendimiento o interrupción de servicio. Puede examinar estos eventos en el portal de Azure, y también puede usar la [API de REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) tener acceso al conjunto completo de eventos mediante programación.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Examinar los registros de estado del servicio para la suscripción

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. En **Inicio** , verá un mosaico de **estado del servicio**. Haga clic en ella.

    ![Inicio](./media/insights-service-health/Insights_Home.png)

3. Vea una lista de todas las áreas de Azure. Haga clic en cualquier área para que aparezca la consulta de registro de actividad que muestra las incidencias de servicio que han afectado a cualquiera de sus suscripciones en las últimas 24 horas.

    ![Estado del servicio de suscripción de registro de actividad](./media/insights-service-health/AzureActivityLogServiceHealth3.png)

4. Puede ver los detalles de un incidente individual haciendo clic en el evento en la tabla.

5. Cambiar el **intervalo de tiempo** para ver un intervalo de tiempo más largo.

## <a name="next-steps"></a>Pasos siguientes

* [Disponibilidad de monitor y capacidad de respuesta de una página web](../application-insights/app-insights-monitor-web-app-availability.md) con la aplicación perspectivas por lo que puede averiguar si la página es hacia abajo.
