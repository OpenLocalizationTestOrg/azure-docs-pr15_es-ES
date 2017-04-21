<properties 
    pageTitle="Iniciar la transmisión de trabajos de análisis de secuencia | Microsoft Azure" 
    description="¿Cómo ejecutar un trabajo streaming en análisis de secuencia de Azure | segmento de ruta de aprendizaje."
    keywords="transmisión trabajos"
    documentationCenter=""
    services="stream-analytics"
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

# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Cómo ejecutar un trabajo streaming en análisis de secuencia de Azure

Cuando una tarea de entrada, consulta y salida todos han especificado que puede iniciar el trabajo de análisis de la secuencia.

Para iniciar su trabajo:

1.  En el portal de Azure clásica, desde el panel de trabajo, haga clic en **Iniciar** en la parte inferior de la página.

    ![Botón de trabajo de inicio](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  

    En el portal de Azure, haga clic en **Iniciar** en la parte superior de la página de trabajo.

    ![Trabajo de inicio de portal Azure botón](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  

2.  Especifique un valor de **Salida de inicio** para determinar cuándo iniciará este trabajo generar un resultado. La configuración predeterminada para las tareas que anteriormente no se ha iniciado es **Hora de comienzo del trabajo**, lo que significa que la tarea iniciará inmediatamente procesamiento de datos. También puede especificar una hora **personalizado** en el pasado (para consumir datos históricos) o en el futuro (para retrasar el proceso hasta el futuro). En caso de cuando un trabajo se ha iniciado y detenido, previamente la opción **Última vez detenido** está disponible para reanudar el trabajo desde la última vez de salida y evitar la pérdida de datos.  

    ![Iniciar flujo de trabajo](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  

    ![Azure portal comienzo transmisión trabajo](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  

3.  Confirmar la selección. El estado del trabajo cambiará a *Iniciar* y poco se moverá a *Ejecutar* cuando se ha iniciado el trabajo. Puede supervisar el progreso de la operación de **Inicio** en el **Hub de notificación**:

    ![transmisión de progreso de tarea](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  

    ![Portal de Azure transmisión progreso de tarea](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
