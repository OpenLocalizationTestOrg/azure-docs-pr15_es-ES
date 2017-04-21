<properties 
    pageTitle="Operación de depuración y registros de servicio en el análisis de secuencia | Microsoft Azure" 
    description="Registros de operación de análisis de secuencia de procedimientos de uso" 
    keywords="registros del servicio"
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

# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Depurar mediante los registros de servicio y la operación de trabajos de análisis de secuencia

Todos los servicios de Azure proporcionan operaciones de registro de mensajes a los usuarios para registrar detalles relacionados con las operaciones de administración. En análisis de secuencia de Azure, esta información puede usarse para fines de depuración como ver el estado del trabajo, el progreso de la tarea, y mensajes de error para realizar un seguimiento del progreso de una tarea con el tiempo de inicio al procesamiento de salida.

## <a name="find-operation-logs-in-the-azure-management-portal"></a>Buscar registros de operación en el portal de administración de Azure

Pueden tener acceso a los registros de las operaciones de dos maneras:  

- Panel de la tarea de análisis de secuencia  
- Servicios de administración en el portal de Azure clásica  

## <a name="dashboard-of-the-stream-analytics-job"></a>Panel de la tarea de análisis de secuencia

Un vínculo a los registros correspondientes de una tarea de análisis de secuencia se muestra en la pestaña del panel de la tarea. Si hace clic en dicho vínculo, establecerá los filtros de manera que muestra registros más recientes para esa tarea específica.

  ![Seleccione los registros de los servicios de administración](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Servicios de administración

Para desplazarse manualmente los registros de operación para el análisis de flujo y otros servicios en el portal de Azure clásica:

1.  Haga clic en **Servicios de administración** en el [portal de Azure clásico](https://manage.windowsazure.com).
2.  Seleccione **Análisis de secuencia** de **tipo** y el nombre de la tarea para **Nombre de servicio**.  

  ![Seleccione el análisis de secuencia](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Buscar los registros de auditoría en el portal de Azure ##

Para buscar registros operativos para el trabajo de análisis de secuencia en el portal de Azure, haga clic en **Examinar** y, a continuación, seleccione los **registros de auditoría**.

  ![Portal de Azure Active el análisis de secuencia](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Se abrirá un módulo que muestra los eventos de los últimos 7 días para todos los recursos en la suscripción.  Puede filtrar para ver los eventos de un período de tiempo o tipo de especificar haciendo clic en el comando de **filtro** .

  ![Portal de Azure Active el análisis de secuencia](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Obtener los detalles de registro

Puede filtrar por intervalo de tiempo y estado para ver los registros de su trabajo.

En el portal de administración de Azure, haga clic en el botón **Detalles** en la parte inferior de la ventana para ver más detalles sobre un evento seleccionado. 

  ![Seleccione detalles](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

En el portal de Azure, haga clic en una entrada de registro para ver los eventos detallados dentro de ella.

  ![Seleccione detalles de portal de Azure](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

Desde allí, puede abrir el módulo de **detalle** haciendo clic en el evento.

  ![Seleccione detalles de portal de Azure](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Depurar un trabajo con error

En el portal de administración de Azure, haga clic en el icono de búsqueda y el tipo 'Error'. Esto le da un resultado de todos los registros con errores. 

  ![Depurar un trabajo con error](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

En el portal de Azure, puede filtrar por nivel de mensaje para ver los eventos de **tareas críticas** .

  ![Depuración de portal de Azure](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Puede seleccionar cualquiera de los errores y haga clic en los **Detalles** para obtener más información sobre el error.  Algunos mensajes de error también proporcionan información acerca de cómo mitigar el problema. 

  ![Detalles de la operación](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

En caso de que necesite ponerse en contacto con [soporte técnico](https://azure.microsoft.com/support/options/) o proporcionar información al equipo mediante el [foro de MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), tenga en cuenta los detalles de la operación, específicamente el **Identificador de correlación**. 

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
