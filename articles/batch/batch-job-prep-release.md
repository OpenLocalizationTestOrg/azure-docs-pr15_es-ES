<properties
    pageTitle="Trabajo de preparación y limpieza lote | Microsoft Azure"
    description="Usar tareas de nivel de trabajo de preparación para minimizar la transferencia de datos a los nodos de cálculo de Azure lote y a continuación, suelte las tareas de limpieza de nodo al final de la tarea."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="09/16/2016"
    ms.author="marsma" />

# <a name="run-job-preparation-and-completion-tasks-on-azure-batch-compute-nodes"></a>Ejecutar tareas de preparación y finalización en los nodos de cálculo por lotes de Azure

 Un proceso de Azure a menudo requiere algún tipo de instalación antes de que las tareas se ejecutan y posteriores a la tarea mantenimiento cuando se han completado sus tareas. Posible que necesite descargar los datos de entrada de tareas comunes en los nodos de cálculo o cargar datos de salida de la tarea en el almacenamiento de Azure después de que finalice el trabajo. Puede usar las tareas de **preparación del trabajo** y **suelte el trabajo** para realizar estas operaciones.

## <a name="what-are-job-preparation-and-release-tasks"></a>¿Qué son preparación del trabajo y suelte tareas?

Antes de ejecutan las tareas de la tarea, la tarea de preparación de trabajo se ejecuta en todos los nodos de cálculo programados para que se ejecute al menos una tarea. Una vez completada la tarea, la tarea de lanzamiento de trabajo se ejecuta en cada nodo en el grupo que ejecuta al menos una tarea. Al igual que con las tareas por lotes normal, puede especificar la línea de comandos que se invoca cuando se ejecuta una tarea de trabajo de preparación o versión.

Tareas de preparación y versión ofrecen características familiares de tarea por lotes, como la descarga de archivos ([archivos de recursos][net_job_prep_resourcefiles]), elevados ejecución, variables de entorno personalizado, duración de la ejecución máximo, número de reintentos y tiempo de retención de archivo.

En las siguientes secciones, aprenderá cómo usar la [JobPreparationTask] [ net_job_prep] y [JobReleaseTask] [ net_job_release] clases se encuentran en el [Lote de .NET] [ api_net] biblioteca.

> [AZURE.TIP] Tareas de preparación y release son especialmente útiles en entornos de "compartido grupo", en el que un grupo de nodos de cálculo persiste entre ejecuciones del trabajo y se usa en muchos trabajos.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Cuándo utilizar la preparación del trabajo y tareas de lanzamiento

Preparación del trabajo y las tareas de lanzamiento son una buena opción para las siguientes situaciones:

**Descargar datos de tareas comunes**

Tareas por lotes a menudo requieren un conjunto común de datos como entrada para las tareas del proyecto. Por ejemplo, en los cálculos de análisis de riesgo diarias, datos de mercado son específico de trabajo, aún más comunes a todas las tareas de la tarea. Estos datos de mercado, a menudo varios gigabytes, deben descargar solo una vez a cada nodo de cálculo para que puede utilizar cualquier tarea que se ejecuta en el nodo. Use una **tarea de preparación** para descargar estos datos en cada nodo antes de la ejecución del trabajo está otras tareas.

**Eliminar la salida de trabajo y tareas**

En un entorno de "compartido grupo", donde los nodos de cálculo de la agrupación no están retirados entre tareas, debe eliminar datos entre ejecuciones del trabajo. Debe ahorrar espacio en disco en los nodos o cumplir con las directivas de seguridad de su organización. Use una **versión de tarea** para eliminar los datos que se ha descargado por una tarea de la tarea de preparación o generado durante la ejecución de la tarea.

**Retención de registros**

Puede que desee conservar una copia de los archivos de registro que generan las tareas, o tal vez bloquee los archivos de descarga que se pueden generar aplicaciones con errores. Use una **versión de la tarea** en estos casos para comprimir y cargar estos datos en un [Almacenamiento de Azure] [ azure_storage] cuenta.

>[AZURE.TIP] Otra forma de conservar los registros y otros trabajo y tareas de salida de datos están usar la biblioteca de [Convenciones de archivo por lotes de Azure](batch-task-output.md) .

## <a name="job-preparation-task"></a>Tarea de preparación

Antes de la ejecución de las tareas de la tarea, lote ejecuta la tarea de preparación de trabajo en cada nodo de cálculo que se ha programado para ejecutar una tarea. De forma predeterminada, el servicio de lote espera de la tarea de preparación de trabajo que se complete antes de ejecutar las tareas programadas para ejecutar en el nodo. Sin embargo, puede configurar el servicio no que esperar. Si se reinicia el nodo, la tarea de preparación de trabajo se ejecuta de nuevo, pero también puede deshabilitar este comportamiento.

La tarea de preparación de trabajo se ejecuta sólo en los nodos que están programados para ejecutar una tarea. Esto impide que la ejecución de una tarea de preparación innecesaria en caso de un nodo no está asignado a una tarea. Esto puede ocurrir cuando el número de tareas para una tarea es menor que el número de nodos en un grupo. También se aplica cuando se habilita [la ejecución de tareas simultáneas](batch-parallel-node-tasks.md) , que deja algunos nodos inactivo si el recuento de tarea es menor que el total de posible tareas simultánea. No ejecutando la tarea de la tarea de preparación en nodos inactivos, puede ahorrar dinero en cargos de transferencia de datos.

> [AZURE.NOTE] [JobPreparationTask] [ net_job_prep_cloudjob] difiere [CloudPool.StartTask] [ pool_starttask] en que se ejecuta JobPreparationTask al comienzo de cada tarea, mientras que se ha StartTask ejecuta solo cuando un nodo de cálculo en primer lugar se unan a un grupo o se reinicia.

## <a name="job-release-task"></a>Liberar tarea de trabajo

Una vez que una tarea está marcada como completada, la tarea de lanzamiento de trabajo se ejecuta en cada nodo en el grupo que ejecuta al menos una tarea. Marcar una tarea como completada al emitir una solicitud de terminación. El servicio de lote establece el estado del trabajo para *Terminar*, finaliza las tareas activas o se está ejecutando asociadas con el trabajo y ejecuta la versión de tarea. La tarea se mueve el estado *completado* .

> [AZURE.NOTE] Eliminación de trabajo también ejecuta la versión de tarea. Sin embargo, si un trabajo ya ha finalizado, liberar tarea no se ejecuta una segunda vez si más adelante se elimina el trabajo.

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Trabajo de preparación y suelte tareas con .NET por lotes

Para utilizar una tarea de la tarea de preparación, asigne un [JobPreparationTask] [ net_job_prep] objeto [CloudJob.JobPreparationTask de su trabajo] [ net_job_prep_cloudjob] propiedad. Asimismo, inicializar un [JobReleaseTask] [ net_job_release] y asignar a [CloudJob.JobReleaseTask de la tarea] [ net_job_prep_cloudjob] propiedad para establecer la versión de la tarea.

En este fragmento de código, `myBatchClient` es una instancia de [BatchClient][net_batch_client], y `myPool` es un grupo existente dentro de la cuenta de proceso por lotes.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Como se mencionó anteriormente, liberar tarea se ejecuta cuando finalice o eliminar una tarea. Finalizar una tarea con [JobOperations.TerminateJobAsync][net_job_terminate]. Eliminar un trabajo con [JobOperations.DeleteJobAsync][net_job_delete]. Normalmente terminar o eliminar un trabajo cuando se han completado sus tareas, o cuando se ha alcanzado el tiempo de espera que haya definido.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Ejemplo de código de GitHub

Para ver la preparación del trabajo y suelte tareas en acción, consulte la [JobPrepRelease] [ job_prep_release_sample] proyecto de ejemplo en GitHub. Esta aplicación de consola hace lo siguiente:

1. Crea un grupo con dos nodos "pequeños".
2. Crea un trabajo con tareas estándares, versión y preparación de trabajo.
3. Se ejecuta la tarea de preparación del trabajo, que escribe primero el identificador de nodo a un archivo de texto en el directorio de "compartido" de un nodo.
4. Se ejecuta una tarea en cada nodo que escribe su identificador de la tarea en el mismo archivo de texto.
5. Una vez se hayan completado todas las tareas (o se alcance el tiempo de espera), se imprime el contenido del archivo de texto de cada nodo en la consola.
6. Cuando haya finalizado el trabajo, ejecuta la versión de tarea para eliminar el archivo desde el nodo.
6. Imprime los códigos de salida de las tareas de preparación y lanzamiento de trabajo para cada nodo en el que ejecuta.
7. Ejecución de pausa para permitir la confirmación de eliminación de trabajo o de grupo.

Resultado de la aplicación de ejemplo es similar a la siguiente:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] Debido a la hora de creación y empezar a variables de nodos en un nuevo grupo (algunos nodos están listos para tareas antes de otras personas), es posible que vea un resultado diferente. Más concretamente, porque las tareas completar rápidamente, uno de los nodos del grupo puede ejecutar todas las tareas del proyecto. Si esto sucede, verá que preparar el trabajo y las tareas de la versión no existen para el nodo que no ejecuta ninguna tarea.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspeccionar la preparación del trabajo y las tareas de publicación en el portal de Azure

Cuando se ejecuta la aplicación de ejemplo, puede usar el [portal de Azure] [ portal] para ver las propiedades de la tarea y sus tareas, o incluso descargar el archivo de texto compartido que es modificado por las tareas del proyecto.

La siguiente captura de pantalla muestra la **hoja de tareas de preparación** en el portal de Azure después de una ejecución de la aplicación de ejemplo. Vaya a las propiedades de *JobPrepReleaseSampleJob* una vez han finalizado las tareas (pero antes de eliminar su trabajo y grupo) y haga clic en **tareas de preparación** o **tareas de lanzamiento** para ver sus propiedades.

![Propiedades de la preparación de trabajo en el portal de Azure][1]

## <a name="next-steps"></a>Pasos siguientes

### <a name="application-packages"></a>Paquetes de aplicaciones

Además de la tarea de preparación del trabajo, también puede usar la característica de [paquetes de aplicaciones](batch-application-packages.md) del lote para preparar nodos de cálculo para la ejecución de la tarea. Esta característica es especialmente útil para implementar aplicaciones que no requieren que se ejecuta un instalador, aplicaciones que contienen muchos archivos (100 +) o aplicaciones que requieren control de versiones estricto.

### <a name="installing-applications-and-staging-data"></a>Instalación de aplicaciones y datos de ensayo

Esta entrada de foro MSDN proporciona una descripción general de varios métodos de preparación de los nodos para ejecutar las tareas:

[Instalación de aplicaciones y datos en lotes de ensayo nodos de cálculo][forum_post]

Escrito por uno de los miembros del equipo por lotes de Azure, describe varias técnicas que puede usar para implementar aplicaciones y datos para nodos de cálculo.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
