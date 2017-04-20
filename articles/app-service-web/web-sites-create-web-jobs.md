<properties 
    pageTitle="Ejecutar tareas en segundo plano con WebJobs" 
    description="Obtenga información sobre cómo realizar tareas en segundo plano en las aplicaciones web de Azure." 
    services="app-service" 
    documentationCenter="" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2016" 
    ms.author="tdykstra"/>

# <a name="run-background-tasks-with-webjobs"></a>Ejecutar tareas en segundo plano con WebJobs

## <a name="overview"></a>Información general

Puede ejecutar programas o scripts en WebJobs en la aplicación de [Servicio de aplicación](http://go.microsoft.com/fwlink/?LinkId=529714) web de tres maneras: a petición, continuamente, o en una programación. No hay ningún costo adicional para usar WebJobs.

Este artículo le muestra cómo implementar WebJobs mediante el [Portal de Azure](https://portal.azure.com). Para obtener información sobre cómo implementar con Visual Studio o un proceso de entrega continuo, vea [cómo implementar Azure WebJobs a aplicaciones Web](websites-dotnet-deploy-webjobs.md).

El SDK de WebJobs de Azure simplifica WebJobs muchas tareas de programación. Para obtener más información, consulte [¿Qué es el SDK de WebJobs](websites-dotnet-webjobs-sdk.md).

 Funciones de Azure (actualmente en la vista previa) es otra forma de ejecutar programas y secuencias de comandos en la aplicación de servicio de Azure. Para obtener más información, vea [información general de las funciones de Azure](../azure-functions/functions-overview.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="acceptablefiles"></a>Tipos de archivo aceptables para las secuencias de comandos o programas

Se aceptarán los siguientes tipos de archivo:

* cmd, bat, .exe (con windows cmd)
* . ps1 (con powershell)
* .sh (con fiesta)
* .PHP (con php)
* .py (con python)
* js (usando el nodo)
* .jar (con java)

## <a name="CreateOnDemand"></a>Crear un a petición WebJob en el portal

1. En el módulo de la **Aplicación Web** del [Portal de Azure](https://portal.azure.com), haga clic en **todas las opciones > WebJobs** para mostrar el módulo **WebJobs** .
    
    ![Módulo de WebJob](./media/web-sites-create-web-jobs/wjblade.png)
    
5. Haga clic en **Agregar**. Aparece el cuadro de diálogo **Agregar WebJob** .
    
    ![Agregar placa WebJob](./media/web-sites-create-web-jobs/addwjblade.png)
    
2. En **nombre**, proporcione un nombre para el WebJob. El nombre debe comenzar con una letra o un número y no puede contener caracteres especiales que no sea "-" y "_".
    
4. En el cuadro **cómo ejecutar** , elija **Ejecutar a petición**.
    
3. En el cuadro de **Carga de archivos** , haga clic en el icono de carpeta y busque el archivo zip que contiene la secuencia de comandos. El archivo zip debe contener el archivo ejecutable (.exe cmd bat .sh .php .py js), así como los archivos auxiliares necesarios para ejecutar el programa o script.
    
5. Seleccione **crear** para cargar la secuencia de comandos en la aplicación web. 
    
    El nombre especificado para el WebJob aparece en la lista en el módulo **WebJobs** .
    
6. Para ejecutar el WebJob, haga clic en su nombre en la lista y haga clic en **Ejecutar**.
    
    ![Ejecutar WebJob](./media/web-sites-create-web-jobs/runondemand.png)
    
## <a name="CreateContinuous"></a>Crear un WebJob ejecución continua

1. Para crear una constante ejecución WebJob, siga los mismos pasos para crear un WebJob que se ejecuta una vez, pero en el cuadro **cómo ejecutar** , elija **continuo**.

2. Para iniciar o detener un WebJob continuo, haga clic en el WebJob en la lista y haga clic en **Iniciar** o **Detener**.
    
> [AZURE.NOTE] Si la aplicación web se ejecuta en más de una instancia, una ejecución continua WebJob se ejecutará en todas las instancias. WebJobs a petición y programados ejecutar en una sola instancia seleccionada por Microsoft Azure de equilibrio de carga.
    
> Para que WebJobs continua para que se ejecute en todas las instancias y confiable, habilitar siempre en * configuración para la aplicación web de lo contrario puede dejar de ejecutarse cuando el sitio del host SCM ha estado inactivo durante mucho tiempo.

## <a name="CreateScheduledCRON"></a>Crear un WebJob programada mediante una expresión CRON

Esta técnica está disponible para las aplicaciones de Web que se ejecuta en modo básico, estándar o Premium y requiere la configuración **Siempre** estén habilitadas en la aplicación.

Para convertir un WebJob en petición en un WebJob programada, simplemente incluir un `settings.job` archivo en la raíz del archivo zip WebJob. Este archivo JSON debe incluir un `schedule` propiedad con una [expresión CRON](https://en.wikipedia.org/wiki/Cron), por ejemplo siguiente.

La expresión CRON se compone de 6 campos: `{second} {minute} {hour} {day} {month} {day of the week}`.

Por ejemplo, para desencadenar la WebJob cada 15 minutos, su `settings.job` tendría:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

Otros ejemplos de programación CRON:

- Cada hora (es decir, cada vez que el número de minutos es 0):`0 0 * * * *` 
- Cada hora de 9 A.M. a 5 P.M.:`0 0 9-17 * * *` 
- A 9:30 A.M. todos los días:`0 30 9 * * *`
- A 9:30 A.M. cada día de la semana:`0 30 9 * * 1-5`

**Nota**: al implementar una WebJob de Visual Studio, asegúrese de marcar la `settings.job` propiedades de archivo como 'Copiar si es posterior'.


## <a name="CreateScheduled"></a>Crear un WebJob programada mediante el programador de Azure

La siguiente alternativa técnica hace uso del programador de Azure. En este caso, la WebJob no tiene ningún conocimiento directo de la programación. En su lugar, el programador de Azure obtiene configurado para desencadenar la WebJob en una programación. 

El Portal de Azure todavía no tiene la capacidad para crear un WebJob programada, pero hasta que se agrega característica que lo hace con el [portal clásico](http://manage.windowsazure.com).

1. En el [portal clásica](http://manage.windowsazure.com) vaya a la página WebJob y haga clic en **Agregar**.

1. En el cuadro **cómo ejecutar** , elija **ejecutar en una programación**.
    
    ![Nuevo trabajo programado][NewScheduledJob]
    
2. Seleccionar la **Región del programador** de su trabajo y, a continuación, haga clic en la flecha situada a la derecha de la parte inferior del cuadro de diálogo para pasar a la pantalla siguiente.

3. En el cuadro de diálogo **Crear trabajo** , elija el tipo de **Periodicidad** que desee: **trabajo único** o **tarea repetitiva**.
    
    ![Frecuencia de programación][SchdRecurrence]
    
4. También elegir una hora de **Inicio** : **ahora** o **en un momento determinado**.
    
    ![Hora de inicio de programación][SchdStart]
    
5. Si desea empezar a una hora determinada, elija los valores de hora inicial en el cuadro **Iniciar en**.
    
    ![Inicio de la programación en un momento determinado][SchdStartOn]
    
6. Si elige una tarea periódica, tendrá la **Repetir cada** opción para especificar la frecuencia de repetición y la opción **Que termina en** para especificar una hora de finalización.
    
    ![Frecuencia de programación][SchdRecurEvery]
    
7. Si elige **semanas**, puede seleccionar el cuadro **En una programación concreto** y especificar los días de la semana que desee ejecutar el trabajo.
    
    ![Días de la programación de la semana][SchdWeeksOnParticular]
    
8. Si elige **meses** y Active la casilla **De una programación concreto** , puede establecer el trabajo se ejecutará en particular numerados **días** del mes. 
    
    ![Programar las fechas determinado el mes][SchdMonthsOnPartDays]
    
9. Si elige **Días de la semana**, puede seleccionar qué día o días de la semana en el mes que desee ejecutar el trabajo.
    
    ![Programar los días de la semana en un mes][SchdMonthsOnPartWeekDays]
    
10. Por último, también puede usar la opción de **repeticiones** para elegir qué semana del mes (en primer lugar, segundo, tercero etc.) que desee ejecutar en los días de la semana que especificó el trabajo.
    
    ![Programar los días de la semana concreto en Particular semanas en un mes][SchdMonthsOnPartWeekDaysOccurences]
    
11. Después de haber creado uno o más trabajos, sus nombres aparecerán en la ficha WebJobs con su estado, tipo de programación y otra información. Se mantiene información histórica de los últimos 30 WebJobs.
    
    ![Lista de trabajos][WebJobsListWithSeveralJobs]
    
### <a name="Scheduler"></a>Tareas programadas y programador de Azure

Tareas programadas se puede configurar en las páginas de Azure programador del [portal clásica](http://manage.windowsazure.com).

1.  En la página WebJobs, haga clic en el vínculo de **programación** de la tarea para desplazarse a la página del portal programador de Azure. 
    
    ![Vínculo a Programador de Azure][LinkToScheduler]
    
2. En la página del programador, haga clic en el trabajo.
    
    ![Trabajo en la página del portal programador][SchedulerPortal]
    
3. Se abrirá la página de la **acción del trabajo** , donde puede configurar aún más el trabajo. 
    
    ![Acción de trabajo PageInScheduler][JobActionPageInScheduler]
    
## <a name="ViewJobHistory"></a>Ver el historial de trabajos

1. Para ver el historial de ejecución de una tarea, incluidas las tareas creadas con el SDK WebJobs, haga clic en el vínculo correspondiente en la columna de **registros** de la hoja de WebJobs. (Puede usar el icono del Portapapeles para copiar la dirección URL de la página de archivo de registro en el Portapapeles si lo desea).
    
    ![Vincular registros](./media/web-sites-create-web-jobs/wjbladelogslink.png)
        
2. Haciendo clic en el vínculo, se abre la página de detalles de la WebJob. Esta página le muestra el nombre del comando Ejecutar, los tiempos de últimos ejecución, y su éxito o error. **Se ejecuta el trabajo reciente**, haga clic en una hora para ver más detalles.
    
    ![WebJobDetails][WebJobDetails]
    
3. Aparece la página **Detalles de ejecución de WebJob** . Haga clic en **Resultados de alternancia** para ver el texto del contenido del registro. El registro de salida se encuentra en formato de texto. 
    
    ![Detalles de ejecución de trabajo de Web][WebJobRunDetails]
    
4. Para ver el texto de salida en una ventana independiente del explorador, haga clic en el vínculo de **descarga** . Para descargar el texto, haga clic en el vínculo y use las opciones del explorador para guardar el contenido del archivo.
    
    ![Descargar los resultados del registro][DownloadLogOutput]
    
5. El vínculo de **WebJobs** en la parte superior de la página proporciona una manera conveniente de obtener acceso a una lista de WebJobs en el panel de historial.
    
    ![Vínculo a la lista de WebJobs][WebJobsLinkToDashboardList]
    
    ![Lista de WebJobs en el panel de historial][WebJobsListInJobsDashboard]
    
    Haga clic en uno de estos vínculos le lleva a la página de detalles de WebJob para la tarea seleccionada.


## <a name="WHPNotes"></a>Notas
    
- Aplicaciones Web en modo gratuita pueden tiempo de espera después de 20 minutos si no hay ninguna solicitud al sitio scm (implementación) y el portal de la aplicación web no se abren en Azure. No se restablece solicitudes al sitio real.
- Código para un trabajo continuo debe escribirse para ejecutarse en un bucle infinito.
- Trabajos continuos ejecutan continuamente solo cuando la aplicación web.
- Básica, y en los modos estándar oferta siempre en que, cuando se habilita, impide que se quedan inactivos aplicaciones web.
- Solo puede depurar continuamente ejecutando WebJobs. No se admite la depuración WebJobs programada o a petición.

## <a name="NextSteps"></a>Pasos siguientes
 
Para obtener más información, vea [Recursos recomendados de Azure WebJobs][WebJobsRecommendedResources].

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
 
