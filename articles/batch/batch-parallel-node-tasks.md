<properties
    pageTitle="Maximizar el uso de nodo lote con tareas paralelas | Microsoft Azure"
    description="Aumentar la eficiencia y reducir los costos con menos nodos de cálculo y tareas marcha simultáneas en cada nodo de un grupo de lotes de Azure"
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
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="maximize-azure-batch-compute-resource-usage-with-concurrent-node-tasks"></a>Maximizar el uso de recursos de cálculo por lotes de Azure con tareas de nodo simultáneas

Al ejecutar más de una tarea simultáneamente en cada nodo de cálculo en el grupo por lotes de Azure, puede maximizar el uso de un número menor de nodos en el grupo de recursos. Para algunas cargas de trabajo, esto puede dar lugar a tiempos de trabajo y disminuir los costos.

Si bien algunos escenarios beneficiarán de todos los recursos de un nodo dedica a una sola tarea, varias situaciones benefician permitir varias tareas compartir los recursos:

 - **Minimizar la transferencia de datos** cuando se pueden compartir datos tareas. En este escenario, puede reducir considerablemente cargos de transferencia de datos copiando datos compartidos en un número menor de nodos y ejecución de tareas en paralelo en cada nodo. Esto se aplica especialmente si los datos que se copiarán a cada nodo deben transferirse entre regiones geográficas.

 - **Uso de la memoria maximización** cuando tareas requieren una gran cantidad de memoria, pero solo durante breves períodos de tiempo y momentos variable durante la ejecución. Puede usar los nodos de cálculo de menos, pero más grandes, con más memoria de controlar estos picos de forma eficaz. Estos nodos tendría varias tareas que se ejecutan en paralelo en cada nodo, pero cada tarea podría aprovechar las ventajas de memoria abundante de los nodos en momentos diferentes.

 - **Mitigar los límites de número de nodo** cuando se requiere dentro de un grupo de la comunicación entre nodos. Actualmente, grupos configuradas para la comunicación entre nodo están limitados a 50 nodos de cálculo. Si puede ejecutar tareas en paralelo cada nodo de un grupo de servidores, un mayor número de tareas se puede ejecutar simultáneamente.

 - **Replicar en local clúster de cálculo**, como cuando mueve primero un entorno de cálculo en Azure. Si su solución local actual ejecuta varias tareas por nodo de cálculo, puede aumentar el número máximo de tareas de nodo para reflejar mejor que la configuración.

## <a name="example-scenario"></a>Escenario de ejemplo

Como ejemplo para ilustrar las ventajas de la ejecución de la tarea paralelas, supongamos que la aplicación de la tarea tiene requisitos de CPU y memoria que [estándar\_D1](../cloud-services/cloud-services-sizes-specs.md#general-purpose-d) nodos son suficientes. Sin embargo, necesarios para finalizar el trabajo en el tiempo necesario, 1.000 de estos nodos.

En lugar de usar estándar\_nodos D1 que tienen 1 núcleo de CPU, podría usar [estándar\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nodos que tienen 16 núcleos y permiten la ejecución de tareas paralelas. Por tanto, se podrían usar *16 veces menos nodos* , en lugar de 1.000 nodos, 63 sólo sería necesario. Además, si se necesitan para cada nodo de archivos de la aplicación de gran tamaño o datos de referencia, eficiencia y la duración de la tarea se vuelva a mejorado ya que los datos se copian a solo 16 nodos.

## <a name="enable-parallel-task-execution"></a>Habilitar la ejecución de tareas paralelas

Configurar los nodos de cálculo para la ejecución de tareas paralelas en el nivel de grupo. Con la biblioteca .NET por lotes, establezca la [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propiedad al crear un grupo. Si está utilizando la API de REST de proceso por lotes, establezca la [maxTasksPerNode] [ rest_addpool] elemento en el cuerpo de la solicitud durante la creación del grupo.

Lote de Azure permite establecer el número máximo de tareas por nodo hasta cuatro veces (4 x) el número de nodo núcleos. Por ejemplo, si el grupo está configurado con los nodos de tamaño "Grande" (cuatro núcleos), a continuación, `maxTasksPerNode` se puede establecer en 16. Para obtener detalles sobre el número de núcleos para cada uno de los tamaños de nodo, vea [tamaños de servicios en la nube](../cloud-services/cloud-services-sizes-specs.md). Para obtener más información sobre los límites de servicio, consulte [cuotas y límites del servicio del lote de Azure](batch-quota-limit.md).

> [AZURE.TIP] Asegúrese de tener en cuenta la `maxTasksPerNode` valor cuando se crea una [fórmula Autoescala] [ enable_autoscaling] para la agrupación. Por ejemplo, una fórmula que se evalúa como `$RunningTasks` podría ser afectada considerablemente por un aumento de las tareas por nodo. Para obtener más información, vea [automáticamente escala nodos de cálculo en un grupo por lotes de Azure](batch-automatic-scaling.md) .

## <a name="distribution-of-tasks"></a>Distribución de tareas

Cuando los nodos de cálculo en un grupo pueden ejecutar las tareas al mismo tiempo, es importante especificar cómo desea que las tareas se distribuye entre los nodos en el grupo.

Mediante el uso de la [CloudPool.TaskSchedulingPolicy] [ task_schedule] propiedad, puede especificar que las tareas deben asignarse de manera uniforme en todos los nodos en el grupo ("Extender"). O bien, puede especificar que las tareas como sea posible deben asignarse a cada nodo antes de tareas se asignan a otro nodo en el grupo ("embalaje").

Como un ejemplo de cómo esta característica es importante, considere la posibilidad de la agrupación de [estándar\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d) nodos (en el ejemplo anterior) que está configurado con un [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] valor de 16. Si la [CloudPool.TaskSchedulingPolicy] [ task_schedule] está configurado con un [ComputeNodeFillType] [ fill_type] del *paquete*, maximizar el uso de las 16 muestras de cada nodo y permitir que un [grupo de ajuste automático](batch-automatic-scaling.md) de eliminar nodos sin usar del grupo (nodos sin tareas asignadas). Esto minimiza el uso de recursos y ahorra dinero.

## <a name="batch-net-example"></a>Ejemplo de .NET por lotes

Este [Lote .NET] [ api_net] fragmento de código de API muestra una solicitud para crear un grupo que contiene los cuatro nodos de gran tamaño con un máximo de cuatro tareas por nodo. Especifica una directiva que rellenará cada nodo con las tareas antes de asignar tareas a otro nodo en el grupo de programación de tareas. Para obtener más información sobre cómo agregar grupos mediante la API de .NET por lotes, consulte [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicated: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Ejemplo del resto de proceso por lotes

En el [Resto del lote] [ api_rest] fragmento de código de API muestra una solicitud para crear un grupo que contiene dos nodos de gran tamaño con un máximo de cuatro tareas por nodo. Para obtener más información sobre cómo agregar grupos mediante el uso de la API de REST, vea [Agregar un grupo a una cuenta de][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicated":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [AZURE.NOTE] Puede establecer la `maxTasksPerNode` elemento y [MaxTasksPerComputeNode] [ maxtasks_net] propiedad en la hora de creación del grupo. No puede modificarse después de crear un grupo.

## <a name="code-sample"></a>Código de ejemplo

La [ParallelNodeTasks] [ parallel_tasks_sample] proyecto en GitHub muestra el uso de la [CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] propiedad.

Esta aplicación de consola de C# utiliza el [Lote .NET] [ api_net] biblioteca para crear un grupo con uno o varios nodos de cálculo. Se ejecuta un número configurable de las tareas en los nodos para simular carga variable. Resultados de la aplicación especifica los nodos que ejecutan cada tarea. La aplicación también proporciona un resumen de los parámetros de trabajo y duración. La parte de resumen de los resultados de dos distintas ejecuciones de la aplicación de ejemplo aparece debajo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La primera ejecución de la aplicación de ejemplo muestra que con un solo nodo en el grupo y la configuración predeterminada de una tarea por nodo, la duración de la tarea es más de 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La segunda ejecución de muestra en el ejemplo una disminución significativa en la duración de la tarea. Esto es porque el grupo se ha configurado con cuatro tareas por nodo, que permite la ejecución de la tarea paralelo completar el trabajo en casi un cuarto de la hora.

> [AZURE.NOTE] Las duraciones de trabajo en los resúmenes anteriores no incluyen la hora de creación del grupo. Cada uno de los trabajos anteriores se envió al creado previamente grupos cuyos nodos de cálculo se encontraban en el estado *inactivo* en tiempo de envío.

## <a name="next-steps"></a>Pasos siguientes

### <a name="batch-explorer-heat-map"></a>Mapa de calor del explorador por lotes

El [Explorador de Azure lote][batch_explorer], una de las [aplicaciones de ejemplo]de Azure lote[github_samples], contiene una característica de *Mapa de calor* que proporciona la visualización de ejecución de la tarea. Cuando está ejecutando el [ParallelTasks] [ parallel_tasks_sample] aplicación de ejemplo, puede usar la característica de mapa de calor para visualizar fácilmente la ejecución de tareas en paralelo en cada nodo.

![Mapa de calor del explorador por lotes][1]

*Mapa de calor Explorer lote que muestra un conjunto de cuatro nodos, con cada nodo ejecutando actualmente cuatro tareas*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png
