<properties
    pageTitle="Información general sobre las alertas de Microsoft Azure | Microsoft Azure"
    description="Las alertas permiten supervisar métricas recursos Azure, eventos o registros y recibir una notificación cuando se cumple una condición especificada."
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
    ms.date="09/24/2016"
    ms.author="robb"/>

# <a name="overview-of-alerts-in-microsoft-azure"></a>Información general sobre las alertas de Microsoft Azure


Este artículo describe qué avisos, sus ventajas y cómo empezar a trabajar con el uso de ellos.  

## <a name="what-are-alerts"></a>¿Qué son las alertas?
Las alertas son un método de supervisión métricas recursos Azure, eventos, o los registros y se recibe una notificación cuando una condición especificada se cumple.

Puede recibir alertas basadas en:

- **Valores de métrica**: esta alerta activa cuando el valor de una métrica especificado cruza un umbral de asignar en cualquier dirección. Es decir, active ambas cuando la condición se cumple en primer lugar y, a continuación, posteriormente cuando condición que ya no se cumple.
- **Eventos de registro de actividad**: esta alerta puede desencadenar todos los eventos o solo cuando se produce un número determinado de eventos.

## <a name="alerts-in-different-azure-services"></a>Alertas en los distintos servicios de Azure

Alertas están disponibles en los diferentes servicios, incluidos:

- **Información de la aplicación**: permite web alertas de prueba y métrica. Consulte [establecer alertas en aplicación perspectivas](../application-insights/app-insights-alerts.md) y [disponibilidad de Monitor y capacidad de respuesta de cualquier sitio Web](../application-insights/app-insights-monitor-web-app-availability.md).
- **Análisis de registro (operaciones Management Suite)**: permite el enrutamiento de registros de diagnóstico para el análisis de registro. Serie de administración de operaciones permite métrica, registro y otros tipos de alertas. Para obtener más información, consulte [las alertas de análisis de registro](../log-analytics/log-analytics-alerts.md).  
- **Monitor de Azure**: permite alertas basadas en los valores de métrica y eventos de registro de actividad. Monitor de Azure incluye la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).  Para obtener más información, vea [usar el portal de Azure, PowerShell o la interfaz de línea de comandos para crear alertas](insights-alerts-portal.md).

## <a name="alert-actions"></a>Acciones de alerta
Puede configurar una alerta para hacer lo siguiente:

- Enviar notificaciones de correo electrónico al administrador del servicio, a coadministradores o a las direcciones de correo electrónico que especifique.
- Llamar a un webhook, que le permite iniciar acciones de automatización adicionales.

 ![Explicada de alertas](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)


## <a name="next-steps"></a>Pasos siguientes

Obtener información sobre las reglas de alertas y configurar con:

- [Portal de Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [Línea de comandos de interfaz](insights-alerts-command-line-interface.md)
- [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)
