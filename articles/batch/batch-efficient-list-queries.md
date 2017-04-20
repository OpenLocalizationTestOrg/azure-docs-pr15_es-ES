<properties
    pageTitle="Consultas de uso eficiente de la lista en lotes de Azure | Microsoft Azure"
    description="Aumentar el rendimiento al filtrar las consultas cuando se solicita información de recursos de proceso por lotes como grupos, tareas, tareas y nodos de cálculo."
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

# <a name="query-the-azure-batch-service-efficiently"></a>Consultar el servicio de Azure lote eficaz

Aquí aprenderá cómo aumentar el rendimiento de la aplicación de Azure lote al reducir la cantidad de datos devueltos por el servicio cuando trabajos, tareas, la consulta y calcular los nodos con el [Lote .NET] [ api_net] biblioteca.

Casi todas las aplicaciones de lote necesitan realizar algún tipo de supervisión u otra operación que consulta el servicio de proceso por lotes, a menudo a intervalos regulares. Por ejemplo, para determinar si hay cualquier tarea en cola restante de una tarea, debe obtener datos en todas las tareas en el trabajo. Para determinar el estado de los nodos en su grupo, debe obtener datos en todos los nodos en el grupo. En este artículo se explica cómo ejecutar este tipo de consultas de la manera más eficaz.

## <a name="meet-the-detaillevel"></a>Cumplir el DetailLevel

En una aplicación por lotes de producción, pueden numerar entidades como trabajos, tareas y los nodos de cálculo en miles. Cuando se solicita información acerca de estos recursos, una gran cantidad de datos debe "entre el cable" del servicio de proceso por lotes a la aplicación en cada consulta. Limitando el número de elementos y el tipo de información que devueltos por una consulta, puede aumentar la velocidad de las consultas y, por lo tanto, el rendimiento de la aplicación.

Este [Lote .NET] [ api_net] fragmento de código de API muestra *todas* las tareas que está asociada a una tarea, junto con *todos los* de las propiedades de cada tarea:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Sin embargo, puede realizar una consulta de lista mucho más eficaz, aplicando "nivel de detalle" a la consulta. Para ello, proporcione un [ODATADetailLevel] [ odata] objeto a la [JobOperations.ListTasks] [ net_list_tasks] método. Este fragmento de código devuelve el identificador, línea de comandos y propiedades de información de nodo de cálculo de las tareas completadas:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

En este escenario de ejemplo, si hay miles de tareas en el trabajo, los resultados de la segunda consulta normalmente se devolverá mucho más rápido que la primera. Para obtener más información sobre el uso de ODATADetailLevel cuando la lista de elementos con la API de .NET lote es incluye [a continuación](#efficient-querying-in-batch-net).

> [AZURE.IMPORTANT]
> Le recomendamos encarecidamente que *siempre* proporcionar un objeto ODATADetailLevel a sus llamadas de lista de la API de .NET para garantizar la máxima eficacia y rendimiento de la aplicación. Al especificar un nivel de detalle, puede ayudar a reducir los tiempos de respuesta del servicio de proceso por lotes, mejorar la utilización de la red y minimizar el uso de memoria por aplicaciones cliente.

## <a name="filter-select-and-expand"></a>Filtrar, seleccione y expandir

El [Lote de .NET] [ api_net] y [Pase el mouse por lotes] [ api_rest] API proporcionan la capacidad para reducir el número de elementos que se devuelven en una lista, así como la cantidad de información que se devuelve para cada uno. Para ello, mediante la especificación de **filtro**, **Seleccione**y **expanda cadenas** al realizar consultas de la lista.

### <a name="filter"></a>Filtro
La cadena de filtro es una expresión que reduce el número de elementos devueltos. Por ejemplo, solo las tareas en ejecución de una tarea de la lista, o solo los nodos de cálculo que están listos ejecutar las tareas de la lista.

- La cadena de filtro se compone de una o varias expresiones con una expresión que consta de un nombre de propiedad, un operador y un valor. Las propiedades que se pueden especificar son específicas para cada tipo de entidad de consulta, como los operadores que son compatibles para cada propiedad.
- Pueden combinar varias expresiones mediante los operadores lógicos `and` y `or`.
- En este ejemplo filtrar listas de cadena solo la ejecución "representar" tareas: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Seleccione
La cadena de selección limita los valores de propiedad que se devuelven para cada elemento. Especificar una lista de nombres de propiedad y, a continuación, se devuelven sólo los valores de propiedad para los elementos en los resultados de la consulta.

- La cadena select consta de una lista de valores separados por comas de nombres de propiedad. Puede especificar cualquiera de las propiedades para el tipo de entidad que está consultando.
- En este ejemplo de cadena select especifica que se deben devolver solo tres valores de propiedad para cada tarea: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expandir
La cadena de expandir reduce el número de llamadas a la API que se requieren para obtener cierta información. Cuando se utiliza una cadena de expansión, puede obtenerse más información acerca de cada elemento con una sola llamada API. En lugar de obtener primero la lista de entidades, a continuación, solicitar información de cada elemento en la lista, utilice una cadena de expandir para obtener la misma información en una sola llamada API. Menos llamadas a la API supone un mejor rendimiento.

- De forma similar a la cadena de selección, la cadena de expandir controla si determinados datos se incluyen en los resultados de la consulta de lista.
- La cadena de expandir solo es compatible cuando se utiliza en la lista de tareas, las programaciones de trabajo, tareas y grupos. Actualmente, solo admite información estadística.
- Cuando todas las propiedades que son necesarias y no se especifica ninguna cadena select, la cadena de expandir *debe* usarse para obtener información de estadísticas. Si se usa una cadena de selección para obtener un subconjunto de propiedades, a continuación, `stats` pueden especificar en la cadena de selección y, a continuación, la cadena de expandir no es necesario que se especifique.
- En este ejemplo expandir cadena especifica que se debe devolver información estadística para cada elemento de la lista: `stats`.

> [AZURE.NOTE] Cuando se construye cualquiera de los tipos de cadena de tres consulta (filtrar, seleccione y expanda), debe asegurarse de que los nombres y las mayúsculas y minúsculas coinciden con de sus homólogos de elemento de la API de REST. Por ejemplo, cuando se trabaja con la clase .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) , debe especificar el **estado** en lugar de **estado**, aunque la propiedad .NET es [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Vea las tablas siguientes para las asignaciones de propiedad entre las API de REST y .NET.

### <a name="rules-for-filter-select-and-expand-strings"></a>Las reglas de filtro, seleccione y expanda cadenas

- Nombres de propiedades de filtro, seleccione y expanda cadenas debería aparecer como lo hacen en el [Resto del lote] [ api_rest] API--incluso cuando utilice [.NET lote] [ api_net] o uno de los otros SDK lote.
- Todos los nombres de propiedad distinguen mayúsculas de minúsculas, pero los valores de propiedad distinguen mayúsculas de minúsculas.
- Fecha y hora cadenas puede ser uno de los dos formatos y debe ir precedidas `DateTime`.

  - Ejemplo de formato DTF W3C:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Ejemplo de formato RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Cadenas de tipo Boolean son `true` o `false`.
- Si se especifica una propiedad no válida o un operador, un `400 (Bad Request)` producirá un error.

## <a name="efficient-querying-in-batch-net"></a>Eficaz consultar de lote .NET

En el [Lote de .NET] [ api_net] API, la [ODATADetailLevel] [ odata] clase se utiliza para proporcionar el filtro, seleccione y expanda cadenas a la lista de operaciones. La clase ODataDetailLevel tiene tres propiedades de cadena público que pueden especificados en el constructor o establecer directamente en el objeto. A continuación, pase el objeto de ODataDetailLevel como un parámetro a las distintas operaciones de lista como [ListPools][net_list_pools], [ListJobs][net_list_jobs]y [ListTasks][net_list_tasks].

- [ODATADetailLevel][odata]. [FilterClause] [ odata_filter]: Limitar el número de elementos devueltos.
- [ODATADetailLevel][odata]. [SelectClause] [ odata_select]: Especificar los valores de propiedad se devuelven con cada elemento.
- [ODATADetailLevel][odata]. [ExpandClause] [ odata_expand]: Recuperar datos para todos los elementos de una sola API llamar en lugar de llamadas independientes para cada elemento.

Fragmento de código siguiente utiliza la API de .NET de lote eficazmente consultar el servicio de lote para las estadísticas de un conjunto específico de grupos. En este escenario, el usuario de lote tiene grupos de prueba y de producción. El grupo de prueba identificadores llevan el prefijo "prueba" y, a continuación, el grupo de producción identificadores llevan el prefijo "ord". En el fragmento de código, *myBatchClient* es una instancia de la clase [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) inicializada correctamente.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] Una instancia de [ODATADetailLevel] [ odata] que está configurado con Select y también se pueden pasar cláusulas de expandir para corresponda métodos Get, como [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx)para limitar la cantidad de datos que se devuelven.

## <a name="batch-rest-to-net-api-mappings"></a>Lote resto a las asignaciones de la API de .NET

Nombres de las propiedades de filtro, seleccione y expanda cadenas *debe* reflejar sus homólogos API de REST, tanto en el nombre y las mayúsculas y minúsculas. Las tablas siguientes proporcionan las asignaciones entre los equivalentes de .NET y las API de REST.

### <a name="mappings-for-filter-strings"></a>Asignaciones de las cadenas de filtro

- **Lista de métodos de .NET**: cada uno de los métodos de la API de .NET en esta columna acepta una [ODATADetailLevel] [ odata] objeto como parámetro.
- **Solicitudes de lista REST**: página a cada API de REST vinculado de esta columna contiene una tabla que especifica las propiedades y operaciones que se permiten en las cadenas de *filtro* . Cuando se crea un [ODATADetailLevel.FilterClause] que usa estos nombres de propiedad y operaciones[ odata_filter] cadena.

| Lista de métodos de .NET | Solicitudes de lista REST |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Los certificados de una cuenta de la lista][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Lista de los archivos asociados a una tarea][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [El estado de la preparación del trabajo y tareas de la versión de trabajo para un trabajo de lista][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Enumerar los trabajos de una cuenta][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Lista de los archivos en un nodo][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [Lista de las tareas asociadas a una tarea][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Las programaciones de trabajo en una cuenta de la lista][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Lista de tareas asociadas a una tarea programada][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Lista de los nodos de cálculo en un grupo][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Los grupos de una cuenta de la lista][rest_list_pools]

### <a name="mappings-for-select-strings"></a>Asignaciones para seleccionadas cadenas

- **Tipos de lote .NET**: tipos de la API de .NET lote.
- **API de REST entidades**: cada página de esta columna contiene una o varias tablas que los nombres de propiedades de la API de REST para el tipo de lista. Estos nombres de propiedad se usan cuando se construcción *Seleccione* cadenas. Que usa los mismos nombres de propiedad cuando se crea un [ODATADetailLevel.SelectClause] [ odata_select] cadena.

| Tipos de lotes .NET | Entidades de la API de REST |
|---|---|
| [Certificado][net_cert] | [Obtener información sobre un certificado][rest_get_cert] |
| [CloudJob][net_job] | [Obtener información sobre un trabajo][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Obtener información sobre una tarea programada][rest_get_schedule] |
| [ComputeNode][net_node] | [Obtener información acerca de un nodo][rest_get_node] |
| [CloudPool][net_pool] | [Obtener información acerca de un grupo][rest_get_pool] |
| [CloudTask][net_task] | [Obtener información sobre una tarea][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Ejemplo: crear una cadena de filtro

Cuando se construye una cadena de filtro para [ODATADetailLevel.FilterClause][odata_filter], consulte la tabla anterior en "Asignaciones de las cadenas de filtro" a la página de la documentación de la API de REST de búsqueda que corresponde a la operación de lista que desee realizar. Encontrará las propiedades pueden filtrarse y los operadores compatibles en la primera tabla multirow en esa página. Si desea recuperar todas las tareas cuyo código de salida es distinto de cero, por ejemplo, esta fila en la [lista de las tareas asociadas a una tarea] [ rest_list_tasks] especifica la cadena de propiedad aplicables y los operadores permitidos:

| (Propiedad) | Operaciones permitidas | Tipo |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

De este modo, sería la cadena de filtro para mostrar todas las tareas con un código de salida distinto de cero:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Ejemplo: crear una cadena de selección

Construir [ODATADetailLevel.SelectClause][odata_select], consulte la tabla anterior en "Asignaciones seleccionadas cadenas de" y vaya a la página de la API de REST que corresponda al tipo de entidad que se lista. Encontrará las propiedades seleccionables y los operadores compatibles en la primera tabla multirow en esa página. Si desea recuperar solo el identificador y la línea de comandos para cada tarea en una lista, por ejemplo, encontrará estas filas en la tabla correspondiente en [obtener información acerca de una tarea][rest_get_task]:

| (Propiedad) | Tipo | Notas |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

La cadena de selección para incluir únicamente el ID y la línea de comandos con cada tarea de la lista sería:

`id, commandLine`

## <a name="code-samples"></a>Ejemplos de código

### <a name="efficient-list-queries-code-sample"></a>Ejemplo de código de consultas de uso eficiente de la lista

Consulte la [EfficientListQueries] [ efficient_query_sample] proyecto de ejemplo en GitHub para ver la consulta de forma eficaz lista puede afectar al rendimiento en una aplicación. Esta aplicación de consola de C# se crea y agrega una gran cantidad de tareas a una tarea. A continuación, realiza varias llamadas a la [JobOperations.ListTasks] [ net_list_tasks] método y pasadas [ODATADetailLevel] [ odata] objetos que estén configurados con distintos valores de propiedad para modificar la cantidad de datos que se devolverán. Produce un resultado similar al siguiente:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Como se muestra en los tiempos transcurridos, puede reducir en gran medida los tiempos de respuesta de consulta limitando las propiedades y el número de elementos que se devuelven. Puede encontrar este y otros proyectos de muestra en los [ejemplos del lote de azure] [ github_samples] repositorio de GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Ejemplo de biblioteca y el código de BatchMetrics

Además de la EfficientListQueries código de ejemplo anterior, puede encontrar el [BatchMetrics] [ batch_metrics] proyecto en los [ejemplos del lote de azure] [ github_samples] GitHub repositorio. El proyecto de ejemplo BatchMetrics muestra cómo eficazmente supervisar el progreso de trabajo de Azure lote con la API de lote.

La [BatchMetrics] [ batch_metrics] ejemplo incluye un proyecto de biblioteca de clase .NET que se puede incorporar a sus propios proyectos y un programa de línea de comandos simple ejercicio y muestran el uso de la biblioteca.

La aplicación de ejemplo dentro del proyecto muestra las siguientes operaciones:

1. Seleccionar atributos específicos para descargar solo las propiedades que necesarias
2. Filtrado en los tiempos de transición de estado para descargar solo los cambios desde la última consulta

Por ejemplo, el siguiente método aparece en la biblioteca de BatchMetrics. Devuelve un ODATADetailLevel que especifica que solo los `id` y `state` propiedades deben obtenerse para las entidades que se consultarán. También especifica que sólo entidades cuyo estado ha cambiado desde el especificado `DateTime` parámetro se debe devolver.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Pasos siguientes

### <a name="parallel-node-tasks"></a>Tareas de nodo paralelo

[Maximizar el lote de Azure calcular uso de recursos con tareas de nodo simultáneas](batch-parallel-node-tasks.md) es otro artículo relacionados con el rendimiento de la aplicación de lote. Algunos tipos de cargas de trabajo pueden beneficiarse de ejecutar tareas paralelas en mayor, pero menos--nodos de cálculo. Consulte el [escenario de ejemplo](batch-parallel-node-tasks.md#example-scenario) en el artículo para obtener información detallada en un escenario de este tipo.

### <a name="batch-forum"></a>Foro de proceso por lotes

El [Foro de Azure lote] [ forum] en MSDN es un lugar ideal para tratar el lote y formular preguntas acerca del servicio. Cabeza de sobre para publicaciones "rápidas" útiles y publique sus preguntas que surjan mientras crea soluciones lote.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

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

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
