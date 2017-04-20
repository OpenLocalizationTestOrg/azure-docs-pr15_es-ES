<properties
    pageTitle="Ejecutar aplicaciones de MPI por lotes de Azure con tareas de instancias múltiples | Microsoft Azure"
    description="Obtenga información sobre cómo ejecutar aplicaciones de interfaz de paso de mensajes (MPI) utilizando el tipo de tarea de instancias múltiples en lotes de Azure."
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
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-azure-batch"></a>Usar tareas de instancias múltiples para ejecutar aplicaciones de la interfaz de paso de mensajes (MPI) en el lote de Azure

Tareas de instancias múltiples permiten ejecutar una tarea por lotes de Azure en varios nodos de cálculo al mismo tiempo. Estas tareas permiten escenarios como aplicaciones de la interfaz de paso de mensajes (MPI) en lotes de informática de alto rendimiento. En este artículo, aprenderá a ejecutar tareas de instancias múltiples mediante el [Lote .NET] [ api_net] biblioteca.

>[AZURE.NOTE] Aunque los ejemplos de este artículo centran en .NET por lotes, MS-MPI y nodos de cálculo de Windows, los conceptos de tarea de instancias múltiples analizados aquí son aplicables a otras plataformas y tecnologías (Python y MPI Intel en los nodos de Linux, por ejemplo).

## <a name="multi-instance-task-overview"></a>Información general de la tarea de instancias múltiples

En el lote, cada tarea suele ejecutarse en un nodo de cálculo solo--enviar varias tareas a una tarea y el servicio de lote programa cada tarea de ejecución en un nodo. Sin embargo, mediante la configuración de la tarea **configuración de instancias múltiples**, puede decirle a lote en su lugar, cree una tarea principal y varias subtareas, a continuación, se ejecutan en varios nodos.

![Información general de la tarea de instancias múltiples][1]

Cuando envíe una tarea con la configuración de instancias múltiples a una tarea, proceso por lotes ejecuta varios pasos únicos a las tareas de instancias múltiples:

1. El servicio por lotes crea una **principal** y varias **subtareas** según la configuración de instancias múltiples. El número total de tareas (principales plus todas las subtareas) coincide con el número de **instancias** (nodos de cálculo) que especifique en la configuración de instancias múltiples.
1. Lote designa uno de los nodos de cálculo como **principal**y programa la tarea principal para ejecutar en el patrón. Programar las subtareas para ejecutar el resto de los nodos de cálculo asignado a la tarea de instancias múltiples, las uno subtareas por nodo.
1. Principal y todas las subtareas descargar los **archivos de recursos comunes** que especifique en la configuración de instancias múltiples.
1. Después del recurso comunes archivos se han descargado, principal y subtareas ejecutan el **comando junto** especificada en la configuración de instancias múltiples. El comando junto normalmente se usa para preparar nodos para ejecutar la tarea. Esto puede incluir el inicio de servicios de fondo (como [Microsoft MPI][msmpi_msdn]de `smpd.exe`) y comprobar que los nodos están listos procesar los mensajes entre nodos.
1. La tarea principal ejecuta el **comando de la aplicación** en el nodo maestro *después de que* el comando junto se ha completado correctamente, principal y todas las subtareas. El comando de la aplicación es la línea de comandos de la tarea de instancias múltiples propio y solo se ejecuta la tarea principal. En un [MS-MPI][msmpi_msdn]-solución, basada en es donde se ejecuta la aplicación MPI habilitado mediante `mpiexec.exe`.

> [AZURE.NOTE] Aunque es funcional distinct, la tarea"instancias múltiples" no es un tipo de tarea único como el que [se ha StartTask] [ net_starttask] o [JobPreparationTask][net_jobprep]. Instancias múltiples trata simplemente una tarea por lotes estándar ([CloudTask] [ net_task] en .NET lote) se han configurado cuya configuración de instancias múltiples. En este artículo, nos referimos a esto como la **tarea de instancias múltiples**.

## <a name="requirements-for-multi-instance-tasks"></a>Requisitos para las tareas de instancias múltiples

Instancias múltiples tareas que requieren un grupo con **habilitada la comunicación entre nodo**y **ejecución de la tarea simultáneas deshabilitada**. Si intenta ejecutar una tarea de instancias múltiples en un grupo con la comunicación deshabilitada o con un valor *maxTasksPerNode* mayor que 1, la tarea no se programará nunca--indefinidamente permanece en el estado "activo". Este fragmento de código muestra la creación de un grupo de este tipo mediante la biblioteca de .NET lote.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicated: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

Además, las tareas de instancias múltiples pueden ejecutar *sólo* en los nodos en **grupos que se crearon después de 14 de diciembre de 2015**.

### <a name="use-a-starttask-to-install-mpi"></a>Use un se ha StartTask para instalar MPI

Para ejecutar aplicaciones de MPI con una tarea de instancias múltiples, debe instalar una implementación de MPI (MS-MPI o Intel MPI, por ejemplo) en los nodos de cálculo en el grupo. Este es un buen momento para usar una [se ha StartTask][net_starttask], que se ejecuta cuando un nodo une a un grupo o se reinicia. Este fragmento de código crea una se ha StartTask que especifica el paquete de instalación de MS-MPI como un [archivo de recursos][net_resourcefile]. Línea de comandos de inicio de la tarea se ejecuta después de descargar el archivo de recursos al nodo. En este caso, la línea de comandos realiza una instalación desatendida de MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Remoto acceso directo a memoria (RDMA)

Al elegir un [tamaño RDMA capaz](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) como A9 para los nodos de cálculo en el grupo por lotes, la aplicación de MPI puede aprovechar las ventajas de red de access (RDMA) de alto rendimiento, baja latencia remoto directa de memoria de Azure.

Busque los tamaños especificados como "RDMA capaz" en los artículos siguientes:

* Grupos de **CloudServiceConfiguration**

  * [Tamaños de los servicios en la nube](../cloud-services/cloud-services-sizes-specs.md) (Sólo Windows)

* Grupos de **VirtualMachineConfiguration**

  * [Tamaños de máquinas virtuales de Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux)

  * [Tamaños de máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows)

>[AZURE.NOTE] Para sacar partido de RDMA en [Linux nodos de cálculo](batch-linux-nodes.md), debe usar **MPI Intel** en los nodos. Para obtener más información acerca de los grupos CloudServiceConfiguration y VirtualMachineConfiguration, vea la sección de [grupo](batch-api-basics.md#Pool) de la información general sobre la característica lote.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Crear una tarea de instancias múltiples con .NET por lotes

Ahora que hemos analizado los requisitos de grupo y el paquete de instalación de MPI, vamos a crear la tarea de instancias múltiples. En este fragmento de código, creamos una estándar [CloudTask][net_task], a continuación, configure su [MultiInstanceSettings] [ net_multiinstance_prop] propiedad. Como se mencionó anteriormente, la tarea de instancias múltiples no es un tipo de tarea distintas, pero una tarea por lotes estándar configurado con la configuración de instancias múltiples.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Principales tareas y subtareas

Cuando se crea la configuración de varias instancias de una tarea, especifique el número de nodos de cálculo que se ejecuta la tarea. Cuando envíe la tarea a un trabajo, el servicio de lote crea una tarea **principal** y suficiente **subtareas** que juntos que coincida con el número de nodos especificada.

Estas tareas se asignan un identificador de entero en el intervalo de 0 a *numberOfInstances* - 1. La tarea con identificador de 0 es la tarea principal y todos los demás identificadores son las subtareas. Por ejemplo, si crea la siguiente configuración de instancias múltiples para una tarea, la tarea principal tendría un identificador de 0 y las subtareas tendría identificadores de 1 a 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Nodo maestro

Cuando envíe una tarea de instancias múltiples, el servicio de lote designa uno de los nodos de cálculo como el nodo "maestro" y programe la tarea principal para ejecutar el nodo maestro. Las subtareas se programan para ejecutar el resto de los nodos asignado a la tarea de instancias múltiples.

## <a name="coordination-command"></a>Comando junto

Se ejecuta el **comando junto** con ambos principal y subtareas.

Está bloqueando la invocación del comando junto--lote no ejecuta el comando de la aplicación hasta que el comando junto ha devuelto correctamente para todas las subtareas. El comando junto, por tanto, debe iniciar los servicios de fondo necesarios, compruebe que están listos para usarse y salga. Por ejemplo, este comando junto para una solución con la versión de MS-MPI 7 inicia el servicio SMPD en el nodo, a continuación, sale:

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

Observe el uso de `start` en este comando junto. Esto es necesario porque la `smpd.exe` no devuelve inmediatamente después de ejecutar la aplicación. Sin usar el [Inicio] de[ cmd_start] de comandos, este comando junto no devolverá y, por tanto, bloqueará la ejecución del comando aplicación.

## <a name="application-command"></a>Comando de la aplicación

Una vez que han terminado la tarea principal y todas las subtareas de ejecutar el comando junto, línea de comandos de la tarea de instancias múltiples ejecuta la tarea principal *solo*. Esto llamamos el **comando de la aplicación** para distinguir desde el comando junto.

Para las aplicaciones de MS-MPI, use el comando de la aplicación para ejecutar la aplicación MPI habilitado con `mpiexec.exe`. Por ejemplo, aquí es un comando de la aplicación para una solución mediante MS-MPI versión 7:

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] Porque MS-MPI `mpiexec.exe` utiliza la `CCP_NODES` variable, de forma predeterminada (consulte [variables de entorno](#environment-variables)) en el ejemplo se excluye la línea de comandos de la aplicación anterior.

## <a name="environment-variables"></a>Variables de entorno

Proceso por lotes crea varias [variables de entorno] [ msdn_env_var] específicos de las tareas de instancias múltiples en los nodos de cálculo asignados a una tarea de instancias múltiples. Las líneas de comando junto y aplicación puede hacer referencia a estas variables de entorno, como las secuencias de comandos y los programas que se ejecuten.

Las variables de entorno siguientes son creadas por el servicio de lote para usar las tareas de instancias múltiples:

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Para obtener más detalles acerca de estas y las demás lote cálculo nodo entorno variables, incluida su contenido y visibilidad, vea [calcular variables de entorno de nodo][msdn_env_var].

>[AZURE.TIP] El ejemplo de código por lotes Linux MPI contiene un ejemplo de cómo se pueden utilizar varias de estas variables de entorno. El [junto-cmd] [ coord_cmd_example] script de fiesta descargas de aplicaciones comunes y entrada de archivos de almacenamiento de Azure, permite a un recurso compartido de sistema de archivos de red (NFS) en el nodo maestro y configura los demás nodos asignados a la tarea de instancias múltiples como clientes NFS.

## <a name="resource-files"></a>Archivos de recursos

Hay dos conjuntos de archivos de recursos para tener en cuenta para las tareas de instancias múltiples: **archivos de recursos comunes** que descargar *todas las* tareas (ambos principal y subtareas), y **archivos de recursos** especificados para la instancia de varias tareas propio descargas de tarea que *solo el principal* .

Puede especificar uno o más **archivos de recursos comunes** en la configuración de varias instancias de una tarea. Estos archivos de recursos comunes se descargan desde [El almacenamiento de Azure](./../storage/storage-introduction.md) en el **directorio compartido de tareas** de cada nodo principal y todas las subtareas. Puede tener acceso al directorio compartido de tareas desde las líneas de comandos de aplicación y junto usando la `AZ_BATCH_TASK_SHARED_DIR` variable de entorno. La `AZ_BATCH_TASK_SHARED_DIR` ruta de acceso es idéntico en todos los nodos asignado a la tarea de instancias múltiples, lo que puede compartir un comando junto único entre principal y todas las subtareas. Lote no "compartir" el directorio en un sentido de acceso remoto, pero puede usarla como un montaje o compartir punto como se mencionó anteriormente en la sugerencia las variables del entorno.

Archivos de recursos que se especifican para la tarea de instancias múltiples sí se descargan en el directorio de trabajo de la tarea, `AZ_BATCH_TASK_WORKING_DIR`, de forma predeterminada. Como se mencionó, a diferencia de los archivos de recursos comunes, solo la tarea principal descargas de archivos de recursos especificados para la propia tarea instancias múltiples.

> [AZURE.IMPORTANT] Usar siempre las variables de entorno `AZ_BATCH_TASK_SHARED_DIR` y `AZ_BATCH_TASK_WORKING_DIR` para hacer referencia a estos directorios en las líneas de comando. No intente construir las rutas de acceso de forma manual.

## <a name="task-lifetime"></a>Duración de tarea

La duración de la tarea principal controla la duración de la tarea de instancias múltiples todo. Cuando se cierra el sitio primario, se terminan todas las subtareas. El código de salida de la principal es el código de salida de la tarea y, por tanto, se utiliza para determinar el éxito o error de la tarea para fines de reintento.

Si se produce un error en cualquiera de las subtareas, salir con un código de retorno distinto de cero, por ejemplo, la tarea de instancias múltiples toda se produce un error. La tarea de instancias múltiples se terminada y reintento, hasta el límite de reintento.

Al eliminar una tarea de instancias múltiples, también se eliminan el servicio por lotes principal y todas las subtareas. Todos los directorios de subtarea y se eliminan los archivos de los nodos de cálculo, al igual que sucede una tarea estándar.

[TaskConstraints] [ net_taskconstraints] para una tarea de instancias múltiples, como la [MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock]y [RetentionTime] [ net_taskconstraint_retention] admiten las propiedades, ya que son para una tarea estándar y aplicarán a la principal y todas las subtareas. Sin embargo, si cambia la [RetentionTime] [ net_taskconstraint_retention] propiedad después de agregar la tarea de instancias múltiples a la tarea, este cambio se aplica solo a la tarea principal. Todas las subtareas seguirán usando el original [RetentionTime][net_taskconstraint_retention].

Lista de tareas recientes de un nodo de cálculo refleja el identificador de una de las subtareas, si la tarea reciente forma parte de una tarea de instancias múltiples.

## <a name="obtain-information-about-subtasks"></a>Obtener información acerca de las subtareas

Para obtener información sobre las subtareas mediante el uso de la biblioteca de .NET por lotes, llame a la [CloudTask.ListSubtasks] [ net_task_listsubtasks] método. Este método devuelve información sobre todas las subtareas e información sobre el nodo de cálculo que ejecuta las tareas. De esta información, puede determinar directorio de raíz de cada subtareas, el identificador de grupo, su estado actual, código de salida y más. Puede usar esta información en combinación con el [PoolOperations.GetNodeFile] [ poolops_getnodefile] método para obtener los archivos de las subtareas. Tenga en cuenta que este método no devuelve información de la tarea principal (Id. de 0).

> [AZURE.NOTE] A menos que se indique lo contrario, métodos lote .NET que funcionan en las instancias múltiples [CloudTask] [ net_task] propio aplicar *solo* a la tarea principal. Por ejemplo, al llamar a la [CloudTask.ListNodeFiles] [ net_task_listnodefiles] método en una tarea de instancias múltiples, se devuelven sólo los archivos de la tarea principal.

Fragmento de código siguiente muestra cómo obtener información de las subtareas, así como para solicitar el contenido del archivo de los nodos en el que ejecuta.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Código de ejemplo

La [MultiInstanceTasks] [ github_mpi] código de ejemplo en GitHub muestra cómo usar una tarea de instancias múltiples para ejecutar un [MS-MPI] [ msmpi_msdn] aplicación en el lote de nodos de cálculo. Siga los pasos de [preparación](#preparation) y [ejecución](#execution) para ejecutar el ejemplo.

### <a name="preparation"></a>Preparación

1. Siga los dos primeros pasos en [cómo compilar y ejecutar un programa de MS-MPI simple][msmpi_howto]. Esto satisface la prerequesites para el siguiente paso.
1. Crear una versión de *lanzamiento* de la [MPIHelloWorld] [ helloworld_proj] programa MPI de ejemplo. Éste es el programa que se ejecutará la tarea de instancias múltiples nodos de cálculo.
1. Crear un archivo zip con `MPIHelloWorld.exe` (que genera el paso 2) y `MSMpiSetup.exe` (que ha descargado paso 1). Deberá cargar este archivo zip como un paquete de aplicación en el siguiente paso.
1. Usar el [portal de Azure] [ portal] para crear un lote de [aplicación](batch-application-packages.md) denominado "MPIHelloWorld" y a continuación, especifique el archivo zip que creó en el paso anterior como versión "1.0" del paquete de aplicación. Para obtener más información, vea [cargar y administrar aplicaciones](batch-application-packages.md#upload-and-manage-applications) .

>[AZURE.TIP] Crear una versión de *lanzamiento* de `MPIHelloWorld.exe` para que no tiene que incluir las dependencias adicionales (por ejemplo, `msvcp140d.dll` o `vcruntime140d.dll`) en el paquete de aplicación.

### <a name="execution"></a>Ejecución

1. Descargar los [ejemplos del lote de azure] [ github_samples_zip] desde GitHub.
1. Abra la **solución** de MultiInstanceTasks en Visual Studio de 2015. La `MultiInstanceTasks.sln` solución archivo se encuentra en:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`

1. Escriba sus credenciales de cuenta de almacenamiento y lote en `AccountSettings.settings` en el proyecto **Microsoft.Azure.Batch.Samples.Common** .
1. **Crear y ejecutar** la solución MultiInstanceTasks para ejecutar la MPI la aplicación de ejemplo en nodos de cálculo en un grupo de lotes.
1. *Opcional*: Use el [portal de Azure] [ portal] o el [Explorador de lote] [ batch_explorer] para examinar el grupo de ejemplo, el trabajo y la tarea ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") antes de eliminan los recursos.

>[AZURE.TIP] Puede descargar la [Comunidad de Visual Studio] [ visual_studio] gratuitamente si no tiene Visual Studio.

Resultado de `MultiInstanceTasks.exe` es similar a la siguiente:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Pasos siguientes

- El blog de Microsoft HPC & Azure lote Team analiza [MPI soporte para Linux en lotes de Azure][blog_mpi_linux]e incluye información sobre cómo usar [OpenFOAM] [ openfoam] con el lote. Puede encontrar ejemplos de código de Python para el [ejemplo OpenFOAM en GitHub][github_mpi].

- Obtenga información sobre cómo [crear grupos de nodos de cálculo Linux](batch-linux-nodes.md) para su uso en las soluciones de Azure lote MPI.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "Información general de instancias múltiples"
