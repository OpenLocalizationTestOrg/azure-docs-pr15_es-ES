<properties 
    pageTitle="Trabajo de análisis de secuencia de descripción supervisión | Microsoft Azure" 
    description="Descripción de la supervisión de trabajos de análisis de secuencia" 
    keywords="monitor de consulta"
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

# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Comprender cómo supervisar consultas y supervisión de trabajo de análisis de secuencia

## <a name="introduction-the-monitor-page"></a>Introducción: La página de monitor

El portal de administración de Azure y Azure Portal superficie métricas clave de rendimiento que se pueden usar para supervisar y solucionar problemas de rendimiento de la consulta y el trabajo. 

En el portal de administración de Azure, haga clic en la pestaña **Monitor** de un trabajo de análisis de secuencia de ejecución para ver estas métricas. Hay un retraso de más de 1 minuto en las métricas de rendimiento que se muestran en la página Monitor.  

  ![Panel de trabajo de supervisión](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

En el Portal de Azure, vaya a la tarea de análisis de secuencia que le interesa ver métricas para y consulte la sección **supervisión** .  

  ![Trabajo de supervisión de Portal Azure paneles](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)  

La primera vez que se crea una tarea de análisis de secuencia en una región, debe configurar los diagnósticos de la región. Para ello, haga clic en cualquier parte de la sección de **supervisión** y el módulo de **Diagnósticos** aparecerá. Aquí puede habilitar diagnósticos y especifique una cuenta de almacenamiento para la supervisión de datos.  

  ![Consulta de configurar Portal diagnósticos de Azure](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Métrica disponible para el análisis de secuencia


| Métrica | Definición |
|--------|-------------|
| % De SU uso | El uso de la transmisión asignado a un trabajo en la pestaña escala de la tarea. Este indicador debe llegar al 80% o anterior, hay probabilidad alta que procesamiento de eventos podría retrasarse o detenido progresando. |
| Eventos de entrada | Cantidad de datos recibidos por el trabajo de análisis de secuencia, en número de eventos. Esto puede usarse para validar que se están enviando eventos al origen de entrada. |
| Eventos de salida | Cantidad de datos que se envía el trabajo de análisis de secuencia para el destino de salida, en número de eventos. |
| Eventos de fuera de orden | Número de eventos recibidos fuera del pedido que se han colocado o dada una marca de hora ajustada en función de la directiva de ordenación del evento. Esto puede verse afectado por la configuración de la configuración de la ventana de tolerancia fuera de pedido. |
| Errores de conversión de datos | Número de errores de conversión de datos contraídos por trabajo de análisis de la secuencia. |
| Errores de tiempo de ejecución | Número de errores que se producen durante la ejecución de un análisis de flujo de trabajo. |
| Eventos de entrada de tiempo | Número de eventos que llegan tarde desde el origen que se han quitado o bien o su marca de tiempo se ha ajustado, según la configuración de directiva de orden de eventos de la configuración de la ventana tolerancia de llegada más tarde. |

## <a name="customizing-monitoring-in-the-azure-management-portal"></a>Personalizar la supervisión en el portal de administración de Azure ##

Se pueden mostrar hasta 6 métricas en un gráfico.

Para cambiar entre mostrar valores relativos (valor final solo para cada métrica) y los valores absolutos (se muestra un eje de Y), seleccione relativa o absoluta en la parte superior del gráfico.

  ![Monitor de consulta relativa absoluta](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)  

Métricas se pueden visualizar en el gráfico del Monitor de agregaciones de 12 horas, 24 horas o 7 días de 1 hora.

Para cambiar el intervalo de tiempo métricas gráfico muestra, seleccione 1 hora, 24 horas o 7 días en la parte superior del gráfico.

  ![Monitor de consulta escala de tiempo](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)  

Puede establecer reglas que pueden notificar por correo electrónico en caso de que el trabajo cruza un umbral definido. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizar la supervisión en el Portal de Azure ##

Puede ajustar el tipo de gráfico, métricas se muestra y el intervalo de la configuración de Editar gráfico de tiempo. Para obtener más información, vea [cómo personalizar supervisión](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Escala de tiempo del Monitor de Azure consulta Portal](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  

## <a name="job-status"></a>Estado del trabajo

El estado de los trabajos de análisis de secuencia puede verse en el Portal de clásico de Azure donde verá una lista de trabajos. Puede ver la lista de trabajos haciendo clic en el icono de análisis de secuencia en el Portal de clásico de Azure.

| Estado | Definición |
|--------|------------|
| Creado | Un trabajo se ha creado, pero no se ha iniciado. |
| Iniciar | Un usuario hace clic en iniciar el trabajo y, a continuación, se inicia el trabajo |
| Ejecutar | Se asigna el trabajo de procesamiento de entrada o esperando para procesar la entrada. Si el trabajo muestra el estado de ejecución sin producir resultados, es probable que la ventana de tiempo de procesamiento de datos es grande o complicada la lógica de consulta. Otra razón puede ser que actualmente no hay los datos que se envían a la tarea. |
| Detener | Un usuario que hizo clic en detener la tarea y, a continuación, el trabajo se ha detenido. |
| Detener | Se ha detenido la tarea. |
| Degradado | Este estado indica que una tarea de análisis de secuencia está encontrando errores transitorios (para ex. Errores de entrada/salidos, errores de procesamiento, errores de conversión de etcetera). Aún se está ejecutando el trabajo, pero hay una gran cantidad de errores generando. Este trabajo necesita atención al cliente y el cliente puede ver los registros de las operaciones de los errores. |
| No se pudo | Esto indica que el trabajo ha fallado debido a errores y se ha detenido el procesamiento. El cliente debe buscar en los registros de las operaciones con el fin de depurar los errores. |
| Eliminar | Esto indica que se elimina el trabajo. |

## <a name="diagnosis"></a>Diagnóstico

En el portal de administración de Azure, el panel de trabajo proporciona información sobre dónde debe buscar el diagnóstico, es decir, entradas, salidas y las operaciones de registro. Puede hacer clic en el vínculo para ir a la ubicación apropiada para mirar el diagnóstico.

  ![Error de monitor de consulta](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)  

Al hacer clic en el recurso de entrada o salido proporciona información de diagnóstico detallada. Esto se actualiza con la información de diagnóstico más reciente mientras se está ejecutando el trabajo.

  ![Diagnósticos de consulta](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
