<properties
    pageTitle="Configurar alertas para las consultas de análisis de secuencia | Microsoft Azure"
    description="Análisis de secuencia de descripción alertas"
    keywords="configurar alertas"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para trabajos de análisis de secuencia de Azure

## <a name="introduction-monitor-page"></a>Introducción: Página Monitor

Puede configurar alertas para activar una alerta cuando una métrica alcanza una condición que se especifique.

Por ejemplo, "si es de eventos de salida para los últimos 15 minutos < 100, enviar notificación por correo electrónico a id de correo electrónico: xyz@company.com”.

Las reglas se pueden configurar métrica a través del portal, o pueden configurado [mediante programación](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sobre los datos de los registros de operación.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Configure alertas a través del Portal de clásico de Azure

Hay dos formas de configurar alertas en el portal de Azure clásica:  

1.  La ficha **Monitor** de su trabajo de análisis de secuencia  
2.  El registro de las operaciones en los servicios de administración  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Configurar alerta a través de la pestaña Monitor del trabajo en el portal

1.  Seleccione la métrica en la pestaña monitor, haga clic en el botón **Agregar regla** en la parte inferior del panel y las reglas de instalación.  

    ![Panel](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  

2.  Defina el nombre y la descripción de la alerta  

    ![Crear regla](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  

3.  Especifique los umbrales, ventana de alerta de evaluación y las acciones de la alerta  

    ![Definir condiciones](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Configure alertas a través de los registros de operaciones

1.  Vaya a la pestaña **alertas** en los servicios de administración en el [Portal de Azure clásico](https://manage.windowsazure.com).  
2.  Haga clic en **Agregar regla**  

    ![Criterios](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  

3.  Defina el nombre y la descripción de la alerta. Seleccione 'Análisis de secuencia' como tipo de servicio y el nombre del trabajo como el nombre de servicio.  

    ![Definir alerta](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas en el portal de Azure ##

En el portal de Azure, vaya a la tarea de análisis de secuencia que le interesa alertas en y haga clic en la sección de **supervisión** .  En el módulo de **métrica** que se abre, haga clic en el comando **Agregar aviso** .

  ![Configuración de portal de Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

Puede asignar un nombre a la regla de alerta y elija una descripción que aparecerá en el correo electrónico de notificación.

Cuando se selecciona métricas elegiré una condición y el valor de umbral de la métrica.

  ![Azure métrica seleccione portal](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Para obtener más detalles sobre la configuración de alertas en el portal de Azure, vea [recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
