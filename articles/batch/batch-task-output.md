<properties
    pageTitle="Trabajo y tareas de salida persistentes en lotes de Azure | Microsoft Azure"
    description="Aprenda a usar el almacenamiento de Azure como un almacén permanente para su tarea por lotes y el trabajo de salida y habilitar la visualización de este resultado almacenado en el portal de Azure."
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
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="persist-azure-batch-job-and-task-output"></a>Conservar la salida de trabajo y tareas por lotes de Azure

Las tareas que se ejecuta en lote normalmente producen resultados que deben almacenarse y, a continuación, más adelante recuperados por otras tareas en el trabajo, la aplicación cliente que ejecuta el trabajo, o ambos. El resultado podría ser archivos creados mediante el procesamiento de datos de entrada o asociados con la ejecución de la tarea de archivos de registro. En este artículo se presenta una biblioteca de clases de .NET que usa una técnica basada en convenciones para conservar como resultado de tareas con el almacenamiento de blobs de Windows Azure, hace que esté disponible incluso después de eliminar los grupos de tareas y nodos de cálculo.

Mediante la técnica de este artículo, también puede ver el resultado de la tarea en **archivos de salida de guardado** y **registros de guardado** en el [portal de Azure][portal].

![Archivos de salida de guardado y selectores de registros de guardado en el portal][1]

>[AZURE.NOTE] Las [Convenciones de archivo por lotes de Azure] [ nuget_package] biblioteca de clases .NET analizado en este artículo está en vista previa. Algunas de las características descritas aquí pueden cambiar antes de su disponibilidad general.

## <a name="task-output-considerations"></a>Consideraciones de salida de tarea

Cuando diseñe su solución por lotes, debe tener en cuenta varios factores relacionados con los resultados de trabajo y tareas.

* **Calcular la duración de nodo**: calcular nodos suelen transitorios, especialmente en grupos de Autoescala. Los resultados de las tareas que se ejecutan en un nodo están disponibles solo mientras el nodo existe y solo dentro del período de retención de archivo que ha definido para la tarea. Para asegurarse de que se conservan los resultados de la tarea, las tareas, por tanto, deben cargar sus archivos de salida a un almacén permanente, por ejemplo, el almacenamiento de Azure.

* **Almacenamiento de salida**: para conservar los datos de salida de la tarea al almacenamiento resistente, puede usar el [SDK de almacenamiento de Azure](../storage/storage-dotnet-how-to-use-blobs.md) en el código de la tarea para cargar los resultados de la tarea en un contenedor de almacenamiento de blobs de Windows. Si implementa un contenedor y la convención de nomenclatura de archivos, otras tareas en el trabajo o la aplicación cliente pueden encontrar y descargar este resultado basándose en la convención.

* **Recuperación de salida**: puede recuperar resultados de la tarea directamente desde los nodos de cálculo en el grupo, o desde el almacenamiento de Azure si las tareas mantiene su resultado. Para recuperar los resultados de la tarea directamente desde un nodo de cálculo, necesita el nombre de archivo y su ubicación en el nodo de salida. Si continúa el resultado con el almacenamiento de Azure, tareas de nivel inferiores o la aplicación cliente debe tener la ruta completa al archivo en el almacenamiento de Azure descargar mediante el SDK de almacenamiento de Azure.

* **Visualización de salida**: cuando vaya a una tarea por lotes en el portal de Azure y seleccione **archivos en el nodo**, se presentan con todos los archivos asociados a la tarea, no solo los archivos de resultados que le interesa. De nuevo, los archivos en los nodos de cálculo están disponibles solo mientras el nodo existe y solo dentro del período de retención de archivo que ha definido para la tarea. Para ver el resultado de la tarea que ha persistido al almacenamiento de Azure en el portal o una aplicación como el [Explorador de almacenamiento de Azure][storage_explorer], debe conocer su ubicación y desplácese hasta el archivo directamente.

## <a name="help-for-persisted-output"></a>Ayuda para el resultado almacenado

Para ayudar a que más fácilmente conservan trabajo y tareas de salida, el grupo por lotes tiene definido e implementa un conjunto de convenciones de nomenclatura, así como una biblioteca de clases .NET las [Convenciones de archivo por lotes de Azure] [ nuget_package] biblioteca, que puede usar en las aplicaciones de proceso por lotes. Además, el portal de Azure es tener en cuenta estas convenciones de nomenclatura para que encuentre fácilmente los archivos que ha almacenado mediante el uso de la biblioteca.

## <a name="using-the-file-conventions-library"></a>Uso de la biblioteca de convenciones de archivo

[Convenciones de archivo por lotes Azure] [ nuget_package] es una biblioteca de clase .NET que pueden usar las aplicaciones .NET lote para almacenar y recuperar los resultados de la tarea a y desde el almacenamiento de Azure fácilmente. Está destinado en código tarea y el cliente en el código de la tarea para almacenar archivos, y en el código de cliente de la lista y recuperarlos. Las tareas también pueden usar la biblioteca para recuperar los resultados de las tareas de nivel superior, como en un escenario de [dependencias entre tareas](batch-task-dependencies.md) .

La biblioteca de convenciones se encarga de garantizar que los contenedores de almacenamiento y de salida se denominan según la convención de archivos y se cargan en el lugar correcto cuando se conserva en el almacenamiento de Azure. Al recuperar los resultados, puede encontrar fácilmente los resultados de una tarea o trabajo determinado por enumerar o recuperar los resultados por ID y finalidad, en lugar de conocer los nombres de archivo o su ubicación de almacenamiento.

Por ejemplo, puede usar la biblioteca para "lista de todos los archivos intermedios de tarea 7" o "obtener me la vista previa en miniatura de trabajo *mymovie*," sin necesidad de conocer los nombres de archivo o una ubicación dentro de su cuenta de almacenamiento.

### <a name="get-the-library"></a>Obtener la biblioteca

Puede obtener la biblioteca que contiene clases nuevas y extiende la [CloudJob] [ net_cloudjob] y [CloudTask] [ net_cloudtask] clases con nuevos métodos, de [NuGet][nuget_package]. Puede agregar a un proyecto de Visual Studio mediante el [Administrador de paquetes de NuGet biblioteca][nuget_manager].

>[AZURE.TIP] Puede encontrar el [código fuente] [ github_file_conventions] para la biblioteca de convenciones de archivo por lotes de Azure en GitHub en el SDK de Microsoft Azure de repositorio de .NET.

## <a name="requirement-linked-storage-account"></a>Requisito: cuenta de almacenamiento vinculado

Para almacenar los resultados del almacenamiento resistente mediante la biblioteca de convenciones de archivo y verlos en el portal de Azure, debe [vincular una cuenta de Azure almacenamiento](batch-application-packages.md#link-a-storage-account) a su cuenta de lote. Si todavía no lo ha hecho, vincular una cuenta de almacenamiento a su cuenta de lote mediante el portal de Azure:

Módulo de **cuenta lote** > **configuración** > **Cuenta de almacenamiento** > **Cuenta de almacenamiento** (ninguno) > Seleccionar una cuenta de almacenamiento en su suscripción

Para obtener una descripción más detallada sobre la vinculación de una cuenta de almacenamiento, vea [implementación de aplicaciones con los paquetes de aplicaciones de Azure lote](batch-application-packages.md).

## <a name="persist-output"></a>Conservar los resultados

Hay dos acciones principales para realizar al guardar los resultados de la tarea y el trabajo con la biblioteca de convenciones de archivo: crear el contenedor de almacenamiento y guardar la salida en el contenedor.

>[AZURE.WARNING] Debido a que todos los resultados de tarea y el trabajo se almacenan en el mismo contenedor, [los límites del límite de almacenamiento](../storage/storage-performance-checklist.md#blobs) puede aplicarse si trata de un gran número de tareas conservar los archivos al mismo tiempo.

### <a name="create-storage-container"></a>Crear el contenedor de almacenamiento

Antes de comiencen la salida conservar con el almacenamiento de las tareas, debe crear un contenedor de almacenamiento de blobs de Windows a la que cargará su resultado. Hacerlo llamando [CloudJob][net_cloudjob]. [PrepareOutputStorageAsync] [net_prepareoutputasync]. Este método de extensión tiene un [CloudStorageAccount] [ net_cloudstorageaccount] objeto como un parámetro y a continuación, crea un contenedor denominado de manera que su contenido sea reconocible, el portal de Azure y los métodos de recuperación descritos más adelante en el artículo.

Normalmente se coloca este código en la aplicación de cliente, la aplicación que crea los grupos, tareas y tareas.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Almacenar los resultados de tarea

Ahora que ha preparado un contenedor de almacenamiento de blobs, tareas pueden guardar resultados al contenedor mediante la [TaskOutputStorage] [ net_taskoutputstorage] clase se encuentra en la biblioteca de convenciones de archivo.

En el código de la tarea, cree primero una [TaskOutputStorage] [ net_taskoutputstorage] objeto, a continuación, cuando la tarea ha finalizado su trabajo, llame a la [TaskOutputStorage][net_taskoutputstorage]. [SaveAsync] [net_saveasync] método para guardar su resultado en el almacenamiento de Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

El parámetro "tipo de salida" clasifica los archivos almacenados. Hay cuatro predefinidos [TaskOutputKind] [ net_taskoutputkind] tipos: "TaskOutput", "TaskPreview", "TaskLog" y "TaskIntermediate." También puede definir tipos personalizados si sean útiles en el flujo de trabajo.

Estos tipos de salida le permiten especificar qué tipo de resultados de la lista al consultar más adelante por lotes de los resultados de una tarea determinada almacenados. En otras palabras, cuando se muestran los resultados de una tarea, puede filtrar la lista en uno de los tipos de resultados. Por ejemplo, "mí los resultados de la *vista previa* para tareas *109*." Más en la lista y recuperar resultados aparece en [recuperar resultados](#retrieve-output) más adelante en el artículo.

>[AZURE.TIP] El tipo de resultado también designa donde en el portal de Azure aparece un archivo en particular: *TaskOutput*-archivos clasificados aparecen en "Archivos de resultados de la tarea" y archivos *TaskLog* aparecen en "Registros de tareas".

### <a name="store-job-outputs"></a>Almacenar los resultados de la tarea

Además de almacenar los resultados de tarea, puede almacenar los resultados de todo el proyecto. Por ejemplo, en la tarea de la combinación de un trabajo de procesamiento de película, puede conservar la película totalmente procesada como un resultado de trabajo. Cuando haya finalizado su trabajo, la aplicación cliente simplemente lista y recuperar los resultados para el trabajo y no es necesario que las tareas individuales de la consulta.

Almacenar la salida de trabajo llamando el [JobOutputStorage][net_joboutputstorage]. [SaveAsync] [net_joboutputstorage_saveasync] método, y especifique la [JobOutputKind] [ net_joboutputkind] y nombre de archivo:

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Como con TaskOutputKind para los resultados de tarea, utilice la [JobOutputKind] [ net_joboutputkind] parámetro para clasificar un trabajo de conservar los archivos. Este parámetro le permite consultar una versión posterior para un tipo específico de salida (lista). La JobOutputKind incluye los tipos de resultados y vista previa y admite la creación de tipos personalizados.

### <a name="store-task-logs"></a>Almacenar registros de tareas

Además, de guardar un archivo de almacenamiento resistente cuando se complete una tarea o trabajo, puede ser necesario para conservar los archivos que se actualizan durante la ejecución de una tarea, archivos de registro o `stdout.txt` y `stderr.txt`, por ejemplo. Para ello, la biblioteca de convenciones de archivo por lotes de Azure proporciona la [TaskOutputStorage][net_taskoutputstorage]. [SaveTrackedAsync] [net_savetrackedasync] método. Con [SaveTrackedAsync][net_savetrackedasync], puede seguir las actualizaciones a un archivo en el nodo (en un intervalo que se especifique) y conservar las actualizaciones para el almacenamiento de Azure.

En el siguiente fragmento de código, usamos [SaveTrackedAsync] [ net_savetrackedasync] actualizar `stdout.txt` en el almacenamiento de Azure cada 15 segundos durante la ejecución de la tarea:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
    await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)`es simplemente un marcador de posición para el código que normalmente haría realizar la tarea. Por ejemplo, es posible que tenga el código que se descarga datos desde el almacenamiento de Azure y realiza el cálculo o transformación en él. La parte importante de este fragmento de código es que muestra cómo puede ajustar este código en una `using` bloque para actualizar periódicamente un archivo con [SaveTrackedAsync][net_savetrackedasync].

La `Task.Delay` es necesaria al final de este `using` bloque para asegurarse de que el agente de nodo tiene tiempo para vaciar el contenido de salida estándar al archivo stdout.txt en el nodo (el agente de nodo es un programa que se ejecuta en cada nodo en el grupo y proporciona la interfaz de comando y control entre el nodo y el servicio de lote). Inmediatamente, es posible pasar por alto los últimos segundos de salida. Este retraso no puede ser necesario para todos los archivos.

>[AZURE.NOTE] Al habilitar el seguimiento con SaveTrackedAsync de archivos, solo *anexa* al archivo marca se conservan en el almacenamiento de Azure. Use este método solo para el seguimiento de archivos de registro no girar u otros archivos que se anexan a, es decir, los datos solo se agrega al final del archivo cuando se actualiza.

## <a name="retrieve-output"></a>Recuperar resultados

Cuando se recupera la salida persistente mediante la biblioteca de convenciones de archivo por lotes de Azure, hágalo centrado en tareas y trabajo real. Puede solicitar la salida para tarea o trabajo sin necesidad de conocer la ruta de acceso en el almacenamiento de blobs de Windows o incluso su nombre de archivo. Puede simplemente decir "Dar los archivos de resultados para tareas *109"*.

Fragmento de código siguiente recorre todas las tareas de la tarea, imprime cierta información acerca de los archivos de salida de la tarea y luego descargas sus archivos de almacenamiento.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>Resultados de tarea y el portal de Azure

El portal de Azure muestra los resultados de tarea y los registros que se conservan a una cuenta de almacenamiento de Azure vinculada con las convenciones de nomenclatura que se encuentra en el [Archivo Léame de Azure lote archivo convenciones][github_file_conventions_readme]. Puede implementar estas convenciones en un idioma de su elección, o puede usar la biblioteca de convenciones de archivos en las aplicaciones de .NET.

### <a name="enable-portal-display"></a>Habilitar mostrar del portal

Para habilitar la visualización de los resultados en el portal, debe cumplir los requisitos siguientes:

 1. [Vincular una cuenta de Azure almacenamiento](#requirement-linked-storage-account) a su cuenta de lote.
 2. Siga las convenciones de nomenclatura predefinidas para los contenedores de almacenamiento y archivos al conservar salidas. Puede encontrar la definición de estas convenciones en la biblioteca de convenciones de archivo [Léame][github_file_conventions_readme]. Si usa las [Convenciones de archivo por lotes de Azure] [ nuget_package] biblioteca para conservar su resultado, este requisito se cumple.

### <a name="view-outputs-in-the-portal"></a>Resultados de la vista en el portal

Para ver los resultados de tarea y los registros en el portal de Azure, vaya a la tarea cuya salida que le interese, haga clic en **archivos de salida guardado** o **registros guardados**. Esta imagen muestra los **archivos de salida de guardado** de la tarea con ID "007":

![Módulo de resultados de tareas en el portal de Azure][2]

## <a name="code-sample"></a>Código de ejemplo

La [PersistOutputs] [ github_persistoutputs] proyecto de ejemplo es uno de los [ejemplos de código de Azure lote] [ github_samples] en GitHub. Esta solución 2015 de Visual Studio muestra cómo usar la biblioteca de convenciones de archivo por lotes de Azure para conservar los resultados de la tarea al almacenamiento resistente. Para ejecutar el ejemplo, siga estos pasos:

1. Abra el proyecto de **Visual Studio de 2015**.
2. Agregar el lote y almacenamiento de **las credenciales de cuenta** para **AccountSettings.settings** en el proyecto Microsoft.Azure.Batch.Samples.Common.
3. **Crear** (pero no se ejecutan) la solución. Restaurar los paquetes de NuGet si se le solicita.
4. Usar el portal de Azure para cargar un [paquete de aplicación](batch-application-packages.md) para **PersistOutputsTask**. Incluir la `PersistOutputsTask.exe` y sus ensamblados dependientes en el paquete zip, establezca el identificador de aplicación a "PersistOutputsTask" y la versión del paquete de aplicación a "1.0".
5. **Iniciar** proyecto (ejecutar) la **PersistOutputs** .

## <a name="next-steps"></a>Pasos siguientes

### <a name="application-deployment"></a>Implementación de aplicaciones

La característica de [paquetes de aplicaciones](batch-application-packages.md) del lote proporciona una forma sencilla tanto a implementar y versión de las aplicaciones que se ejecutan las tareas en nodos de cálculo.

### <a name="installing-applications-and-staging-data"></a>Instalación de aplicaciones y datos de ensayo

Desproteger los [nodos de cálculo de instalar las aplicaciones y los datos de ensayo en lote] [ forum_post] publicar en el foro de Azure lote para obtener información general de los distintos métodos de preparación de los nodos para ejecutar las tareas. Escrito por uno de los miembros del equipo por lotes de Azure, esta entrada es un buen manual de las diferentes maneras de pasar los archivos (incluidas las aplicaciones y los datos de entrada de tarea) en los nodos de cálculo y algunas consideraciones especiales para cada método.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Archivos de salida de guardado y selectores de registros de guardado en el portal"
[2]: ./media/batch-task-output/task-output-02.png "Módulo de resultados de tareas en el portal de Azure"