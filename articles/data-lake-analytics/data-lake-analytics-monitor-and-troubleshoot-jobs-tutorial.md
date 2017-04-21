<properties 
   pageTitle="Solución de problemas de trabajos de análisis de lago de datos de Azure con el Portal de Azure | Azure" 
   description="Obtenga información sobre cómo usar el Portal de Azure para solucionar problemas de trabajos de análisis de datos lago. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Solución de problemas de trabajos de análisis de lago de datos de Azure con el Portal de Azure

Obtenga información sobre cómo usar el Portal de Azure para solucionar problemas de trabajos de análisis de datos lago.

En este tutorial, tendrá un problema falta de archivo de origen de instalación y usar el Portal de Azure para solucionar el problema.

**Requisitos previos**

Antes de comenzar este tutorial, debe tener el siguiente:

- El **proceso de trabajo de conocimientos básicos de análisis de datos lago**. Consulte [Introducción a Azure datos lago análisis con el Portal de Azure](data-lake-analytics-get-started-portal.md).
- **Análisis de lago de datos de una cuenta**. Consulte [Introducción a Azure datos lago análisis con el Portal de Azure](data-lake-analytics-get-started-portal.md#create-adl-analytics-account).
- **Copiar los datos de ejemplo en la cuenta de almacén de datos de lago predeterminada**.  Vea [preparar los datos de origen](data-lake-analytics-get-started-portal.md#prepare-source-data)

##<a name="submit-a-data-lake-analytics-job"></a>Enviar un trabajo de análisis de datos lago

Ahora creará un trabajo SQL U con un nombre de archivo de origen incorrecto.  

**Para enviar el trabajo**

1. Desde el Portal de Azure, haga clic en **Microsoft Azure** en la esquina superior izquierda.
2. Haga clic en el mosaico con su nombre de cuenta de análisis de datos lago.  Se ha anclado aquí cuando se creó la cuenta.
Si la cuenta no está anclada allí, vea [Abrir una cuenta de análisis desde el portal](data-lake-analytics-manage-use-portal.md#access-adla-account).
3. En el menú superior, haga clic en **Nueva tarea** .
4. Escriba un nombre para el trabajo y el siguiente script U SQL:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv1"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/output/SearchLog-from-adls.csv"
        USING Outputters.Csv();

    El archivo de origen definido en la secuencia de comandos es **/Samples/Data/SearchLog.tsv1**, que será **/Samples/Data/SearchLog.tsv**.
     
5. Haga clic en **Enviar trabajo** desde la parte superior. Se abre un nuevo panel de detalles de la tarea. En la barra de título, se muestra el estado del trabajo. Se necesitan unos minutos para finalizar. Puede hacer clic en **Actualizar** para obtener el último estado.
6. Espere a que el estado del trabajo cambia a **error**.  Si la tarea es **correcta**, es porque no quitar la carpeta/Samples. Vea la sección **requisitos previos** al principio del tutorial.

Es posible que se pregunte - ¿por qué tarda tanto tiempo para una tarea pequeña.  Recuerde que el análisis de datos lago está diseñado para procesar datos grandes.  Destaca al procesar una gran cantidad de datos con su sistema distribuido.

Vamos a se supone que ha enviado el trabajo y a continuación, cierre el portal.  En la siguiente sección, aprenderá cómo solucionar problemas de la tarea.


## <a name="troubleshoot-the-job"></a>Solucionar problemas de la tarea

En la última sección, ha enviado un trabajo y ha fallado la tarea.  

**Para ver todos los trabajos**

1. Desde el portal de Azure, haga clic en **Microsoft Azure** en la esquina superior izquierda.
2. Haga clic en el mosaico con su nombre de cuenta de análisis de datos lago.  La tarea de resumen se muestra en el mosaico de la **Administración del trabajo** .

    ![Administración de trabajos de análisis de lago de datos de Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)
    
    El trabajo de administración le da un vistazo del estado del trabajo. Observe que hay un trabajo con error.
   
3. Haga clic en el icono de **Gestión de tareas** para ver los proyectos. Las tareas se clasifican en **ejecución**, **en cola**y **terminado**. Se verá su trabajo con error en la sección **terminado** . Será una primera en la lista. Cuando tenga una gran cantidad de tareas, puede hacer clic en **filtro** para ayudarle a encontrar trabajos.

    ![Trabajos de filtro de análisis de lago de datos de Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. Haga clic en el trabajo con error de la lista para abrir los detalles del trabajo en un nuevo módulo:

    ![Error de análisis de lago datos Azure trabajo](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)
    
    Observe el botón **Enviar** . Después de corregir el problema, puede volver a enviar el trabajo.

5. Haga clic en la parte resaltada en la captura de pantalla anterior para abrir los detalles del error.  Se verá algo similar:

    ![Error de análisis de lago datos Azure detalles del trabajo](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Indica que no se encuentra la carpeta de origen.
    
6. Haga clic en **Duplicar Script**.
7. Actualizar la ruta de acceso **desde** el que sigue:

    "/ Samples/Data/SearchLog.tsv"

8. Haga clic en **Enviar a trabajo**.


##<a name="see-also"></a>Vea también

- [Información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md)
- [Introducción a análisis de lago de datos de Azure con PowerShell de Azure](data-lake-analytics-get-started-powershell.md)
- [Introducción a análisis de lago de datos de Azure y U-SQL mediante Visual Studio](data-lake-analytics-u-sql-get-started.md)
- [Administrar el análisis de lago de datos de Azure con el Portal de Azure](data-lake-analytics-manage-use-portal.md)





