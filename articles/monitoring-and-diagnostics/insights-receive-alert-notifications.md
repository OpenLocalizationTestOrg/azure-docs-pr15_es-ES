<properties
    pageTitle="Recibir notificaciones de alerta para los servicios de Azure | Microsoft Azure"
    description="Recibir una notificación cuando se cumplen las condiciones de las reglas de alertas."
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

# <a name="receive-alert-notifications"></a>Recibir notificaciones de alerta

Puede recibir una alerta basada en supervisión métricas para o eventos en los servicios de Azure.

Para una regla de alerta en un valor de métrica, cuando el valor de una métrica especificado cruza un umbral asignado, la regla de alerta se activa y puede enviar una notificación. Para una regla de alerta en eventos, una regla puede enviar una notificación de *todos* los eventos o, solo cuando se producen un número determinado de eventos.

Al crear una regla de alerta, puede seleccionar opciones para enviar una notificación de correo electrónico al administrador del servicio y coadministradores o a otro administrador puede especificar. Cuando se activa la regla y cuando una condición de alerta se resuelve, se envía una notificación por correo electrónico.

Puede usar la [API de REST](https://msdn.microsoft.com/library/azure/dn931945.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para configurar y obtener información sobre las reglas de alertas mediante programación.

## <a name="create-an-alert-rule"></a>Crear una regla de alerta

1. En el [portal](https://portal.azure.com/), haga clic en **Examinar**y, a continuación, en un recurso que le interesa supervisión.

2. Haga clic en el mosaico de **reglas de alerta** en la lente de **operaciones** .

3. Haga clic en el comando **Agregar aviso** .

    ![Agregar alerta](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Puede asignar un nombre a la regla de alerta y elija una descripción que aparecerá en el correo electrónico de notificación.

5. Cuando se selecciona **métricas** elegiré una condición y el valor de umbral de la métrica. Este es el período de tiempo que usa Azure para supervisar y trazado actividad alerta.

    ![Condición y umbral](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. También puede elegir **eventos**y recibirá una notificación cuando se produce un evento determinado.

    ![Eventos](./media/insights-receive-alert-notifications/Insights_Events.png)

7. Por último, puede elegir enviar notificación por correo electrónico a los administradores responsables.

Después de hacer clic en **Guardar**, dentro de unos minutos será informado siempre la métrica que elija supera el umbral.

## <a name="managing-your-alert-rules"></a>Administrar las reglas de alerta

Una vez haya creado una regla de alerta, puede ver una vista previa de la alerta de umbral compara la métrica del día anterior.

![Eventos](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Por supuesto, puede editar esta regla de alerta y **Habilitar** o **Deshabilitar** si desea temporalmente dejar de recibir notificaciones sobre ella.

## <a name="next-steps"></a>Pasos siguientes

* [Configurar webhooks en las alertas de](insights-webhooks-alerts.md) notificaciones de ruta entre varios canales
* [Medidas de servicio del monitor](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y capacidad de respuesta.
* [Habilitar la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en el servicio.
* [Disponibilidad de monitor y capacidad de respuesta de una página web](../application-insights/app-insights-monitor-web-app-availability.md) con la aplicación perspectivas por lo que puede averiguar si la página es hacia abajo.
* [Supervisar el rendimiento de aplicación](../application-insights/app-insights-azure-web-apps.md) si desea saber exactamente cómo está funcionando su código en la nube.
* [Ver eventos y registros de auditoría](insights-debugging-with-events.md) para obtener todo lo que ha sucedido en el servicio.
* [Estado del servicio de seguimiento](insights-service-health.md) para averiguar cuándo Azure ha sufrido interrupciones de servicio o degradación del rendimiento.
