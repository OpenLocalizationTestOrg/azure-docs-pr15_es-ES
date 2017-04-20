<properties
    pageTitle="Las dependencias entre Azure lote tareas | Microsoft Azure"
    description="Crear tareas que dependen de la correcta realización de otras tareas para el procesamiento de estilo MapReduce y datos grandes similares cargas de trabajo en el lote de Azure."
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
    ms.date="09/28/2016"
    ms.author="marsma" />

# <a name="task-dependencies-in-azure-batch"></a>Dependencias de tareas en el lote de Azure

La característica de dependencias de tareas del lote de Azure es una buena opción si desea procesar:

- Cargas de trabajo de estilo MapReduce en la nube.
- Trabajos cuyas tareas de procesamiento de datos se pueden expresar como un gráfico acíclico dirigido (DAG incluidos).
- Cualquier otro trabajo en el que descendentes tareas dependen de en los resultados de las tareas de nivel superior.

Dependencias de tareas por lotes permiten crear tareas que están programadas para ejecución nodos de cálculo solo después de la finalización correcta de una o varias tareas. Por ejemplo, puede crear un trabajo que procesa cada fotograma de una película 3D con tareas paralelas independientes. Al final de tareas--la combinación tarea""--combina los marcos representados en la película completa solo después de que todos los marcos se han procesado correctamente.

Puede crear tareas que dependen de otras tareas en una relación uno a uno o uno a varios. Incluso puede crear una dependencia de rango donde una tarea depende de la finalización correcta de un grupo de tareas en un intervalo específico de identificadores de la tarea. Puede combinar estas tres situaciones básicas para crear relaciones de varios a varios.

## <a name="task-dependencies-with-batch-net"></a>Dependencias de tareas con .NET por lotes

En este artículo se describe cómo configurar las dependencias entre tareas utilizando el [Lote de .NET] [ net_msdn] biblioteca. En primer lugar le mostraremos cómo [Habilitar la dependencia de tareas](#enable-task-dependencies) en los trabajos y, a continuación, se muestra cómo [Configurar una tarea con dependencias](#create-dependent-tasks). Por último, trataremos los [escenarios de dependencia](#dependency-scenarios) que admita el lote.

## <a name="enable-task-dependencies"></a>Habilitar las dependencias entre tareas

Para usar las dependencias entre tareas en la aplicación por lotes, se debe indicar el servicio por lotes que el trabajo utiliza las dependencias entre tareas. En .NET por lotes, habilitarlo en su [CloudJob] [ net_cloudjob] estableciendo su [UsesTaskDependencies] [ net_usestaskdependencies] propiedad `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

En el fragmento de código anterior, "batchClient" es una instancia de la [BatchClient] [ net_batchclient] clase.

## <a name="create-dependent-tasks"></a>Crear tareas dependientes

Para crear una tarea que depende de la finalización correcta de una o varias tareas, puede decirle a lote que depende la tarea"" otras tareas. En .NET por lotes, configure la [CloudTask][net_cloudtask]. [DependsOn] [net_dependson] propiedad con una instancia de la [TaskDependencies] [ net_taskdependencies] clase:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este fragmento de código, crea una tarea con el identificador de "Flores" que se programará para que se ejecute en un nodo de cálculo después de las tareas con identificadores de "Lluvia" y "Sol" se han completado correctamente.

 > [AZURE.NOTE] Se considera que una tarea se completa cuando se encuentra en el estado **completado** y su **código de salida** es `0`. En .NET por lotes, esto significa un [CloudTask][net_cloudtask]. [Estado] [net_taskstate] el valor de propiedad `Completed` y del CloudTask [TaskExecutionInformation][net_taskexecutioninformation]. [Código] [net_exitcode] es el valor de la propiedad `0`.

## <a name="dependency-scenarios"></a>Escenarios de dependencia

Hay tres escenarios de dependencia de tareas básicas que puede usar en el lote de Azure: uno a uno, uno a varios y el identificador de la tarea una dependencia de rango. Estos pueden combinarse para ofrecer un cuarto escenario, muchos a muchos.

 Escenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Ejemplo | |
 :-------------------: | ------------------- | -------------------
 [Uno a uno](#one-to-one) | *taskB* depende de *taskA* <p/> *taskB* no se programará para la ejecución hasta que se ha completado correctamente *taskA* | ![Diagrama: dependencia de tareas de uno a uno][1]
 [Uno a varios](#one-to-many) | *taskC* depende de *taskA* y *taskB* <p/> *taskC* no se programará para la ejecución hasta que se han completado correctamente *taskA* y *taskB* | ![Diagrama: dependencia entre tareas de uno a varios][2]
 [Intervalo de Id. de tarea](#task-id-range) | *taskD* depende de un intervalo de tareas <p/> *taskD* no se programará para la ejecución hasta que se han completado correctamente las tareas con identificadores *1* al *10* | ![Diagrama: Dependencia de rango de id de tareas][3]

>[AZURE.TIP] Puede crear relaciones de **varios a varios** , por ejemplo, donde tareas C, D, E y F cada dependen de las tareas A y B. Esto es útil, por ejemplo, en escenarios de preprocesamiento paralelizados donde las tareas de nivel inferiores dependen de los resultados de varias tareas de nivel superior.

### <a name="one-to-one"></a>Uno a uno

Para crear una tarea que tiene una dependencia de otra tarea finalice correctamente, proporcione un identificador de tarea único para la [TaskDependencies][net_taskdependencies]. [OnId] [net_onid] método estático al rellenar el [DependsOn] [ net_dependson] propiedad de [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Uno a varios

Para crear una tarea que tiene una dependencia en la finalización correcta de varias tareas, proporcionar una colección de identificadores a la [TaskDependencies][net_taskdependencies]. [OnIds] [net_onids] método estático al rellenar el [DependsOn] [ net_dependson] propiedad de [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Intervalo de Id. de tarea

Para crear una tarea que tiene una dependencia en la finalización correcta de un grupo de tareas se encuentran dentro de un rango cuyos identificadores, especifica la primera y última identificadores de tareas en el rango de la [TaskDependencies][net_taskdependencies]. [OnIdRange] [net_onidrange] método estático al rellenar el [DependsOn] [ net_dependson] propiedad de [CloudTask][net_cloudtask].

>[AZURE.IMPORTANT] Al usar intervalos de Id. de tarea para las dependencias, los identificadores de la tarea en el intervalo que *debe* ser representaciones de cadena de valores enteros. Además, todas las tareas en el intervalo deben completar para programarse para ejecutarse la tarea dependiente.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>Código de ejemplo

La [TaskDependencies] [ github_taskdependencies] proyecto de ejemplo es uno de los [ejemplos de código de Azure lote] [ github_samples] en GitHub. Esta solución 2015 de Visual Studio muestra cómo habilitar la dependencia de tareas en una tarea, crear tareas que dependen de otras tareas y ejecutar esas tareas en un grupo de nodos de cálculo.

## <a name="next-steps"></a>Pasos siguientes

### <a name="application-deployment"></a>Implementación de aplicaciones

La característica de [paquetes de aplicaciones](batch-application-packages.md) del lote proporciona una forma sencilla tanto a implementar y versión de las aplicaciones que se ejecutan las tareas en nodos de cálculo.

### <a name="installing-applications-and-staging-data"></a>Instalación de aplicaciones y datos de ensayo

Desproteger los [nodos de cálculo de instalar las aplicaciones y los datos de ensayo en lote] [ forum_post] publicar en el foro de Azure lote para obtener información general de los distintos métodos para preparar los nodos para ejecutar las tareas. Escrito por uno de los miembros del equipo por lotes de Azure, esta entrada es un buen manual de las diferentes maneras de pasar los archivos (incluidas las aplicaciones y los datos de entrada de tarea) en los nodos de cálculo.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagrama: dependencia de uno a uno"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependencia de uno a varios"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependencia de rango de id de tareas"
