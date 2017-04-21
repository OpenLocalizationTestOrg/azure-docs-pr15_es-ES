<properties 
    pageTitle="Cómo crear un trabajo de procesamiento de análisis de datos para el análisis de secuencia | Microsoft Azure" 
    description="Crear un trabajo de procesamiento de análisis de datos para el análisis de secuencia | segmento de ruta de aprendizaje."
    keywords="procesamiento de análisis de datos"
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

# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Cómo crear un trabajo de procesamiento de análisis de datos para el análisis de secuencia

El recurso de nivel superior en el análisis de secuencia de Azure es un trabajo de análisis de la secuencia.  Consta de uno o varios orígenes de datos de entrada, una consulta expresar la transformación de datos y uno o más destinos de salida resultados se escriben en. Juntas estos permiten al usuario realizar análisis de datos de procesamiento de transmisión escenarios de datos.

Para empezar a usar el análisis de flujo, empiece por crear una nueva tarea de análisis de secuencia.  No tenga en cuenta que esta acción implicaciones facturación hasta que se inicia el trabajo.

1.  Inicie sesión en online [Azure portal clásico](http://manage.windowsazure.com) o el [portal de Azure](https://portal.azure.com/).
2.  En el portal: **Haga clic en nuevo**y, a continuación, haga clic en **Servicios de datos** o **El análisis de datos** según su portal y, a continuación, haga clic en **Análisis de secuencia de Azure** o **Análisis de secuencia** y, a continuación, **Crear rápido**.

    ![Asistente para trabajos de procesamiento de análisis de datos](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  

    ![Crear tarea de procesamiento de análisis de datos](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  

3.  Especificar la configuración que desee para el trabajo de análisis de la secuencia.
    - En el cuadro **Nombre de tarea** , escriba un nombre para identificar el análisis de flujo de trabajo. Cuando se valida el **Nombre del trabajo** , aparece una marca de verificación verde en el cuadro Nombre de tarea. El **Nombre de la tarea** puede contener solo caracteres alfanuméricos y '-' caracteres y debe estar entre 3 y 63 caracteres.
    - Use **región** en el portal de Azure o la **ubicación** en el portal de Azure para especificar la ubicación geográfica donde desea ejecutar el trabajo.
    - Si usa el portal de Azure, seleccione o cree una cuenta de almacenamiento para usarla como la **Cuenta de almacenamiento de supervisión Regional**. Esta cuenta de almacenamiento se usa para almacenar los datos del seguimiento de todos los trabajos de análisis de secuencia ejecutando en esta área.
    - Si usa el portal de Azure, especifique un **Grupo de recursos** de nuevo o existente que contenga recursos relacionados para la aplicación.

4.  Una vez que se haya configurado las nuevas opciones de trabajo de análisis de secuencia, haga clic en **Crear tarea de análisis de secuencia**. Este proceso puede demorarse unos minutos para el trabajo de análisis de secuencia que debe crearse. Para comprobar el estado, puede supervisar el progreso en el hub de notificaciones.

    ![Hub de notfications de datos análisis procesamiento de trabajo](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  

    ![Análisis de datos de portal Azure procesamiento trabajo crear trabajo](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  

5.  La nueva tarea se mostrarán con el estado **creado**. Observe que el botón **Inicio** está deshabilitado. Configure la entrada de trabajo, la consulta y la salida antes de empezar el trabajo.

    ![Estado del trabajo de trabajo de procesamiento de análisis de datos](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  

    ![Análisis de datos de portal Azure procesamiento de estado de la tarea de trabajo](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
