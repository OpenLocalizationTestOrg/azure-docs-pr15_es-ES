<properties
    pageTitle="Plantillas de Visual Studio para el lote de Azure | Microsoft Azure"
    description="Descubra cómo estas plantillas de proyecto de Visual Studio pueden ayudarle a implementar y ejecutar las cargas de trabajo de descarga intensiva en lotes de Azure"
    services="batch"
    documentationCenter=".net"
    authors="fayora"
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

# <a name="visual-studio-project-templates-for-azure-batch"></a>Plantillas de proyecto de Visual Studio para el lote de Azure

El **Administrador de trabajos** y **plantillas de tarea procesador Visual Studio** para lote proporcionan código para ayudarle a implementar y ejecutar las cargas de trabajo de descarga intensiva en lote con la menor cantidad de esfuerzo. Este documento describe estas plantillas y proporciona pautas para cómo usarlas.

>[AZURE.IMPORTANT] En este artículo se trata solo información aplicable a estas dos plantillas y se supone que está familiarizado con el servicio del lote y conceptos clave relacionados con él: grupos, calcular nodos, tareas y tareas, el administrador tareas, variables de entorno y otra información relevante. Puede encontrar más información en [Conceptos básicos de Azure lote](batch-technical-overview.md), [información general de la característica de lote para desarrolladores](batch-api-basics.md)y [empezar a trabajar con la biblioteca de Azure lote para .NET](batch-dotnet-get-started.md).

## <a name="high-level-overview"></a>Descripción general

Las plantillas de administrador de trabajos y procesador de tareas pueden utilizarse para crear dos componentes útiles:

* Tareas de administrador de trabajo que implementa un separador de trabajo que se puede dividir una tarea en varias tareas que pueden ejecutarse de forma independiente, en paralelo.

* Procesador de tareas que se pueden usar para realizar preprocesamiento y procesamiento posterior alrededor de una línea de comandos de la aplicación.

Por ejemplo, en un escenario de representación de película, el divisor de trabajo debería convertir un trabajo de película único en cientos o miles de tareas diferentes que procesan marcos individuales por separado. En consecuencia, el procesador de tareas ' d invocar la aplicación de procesamiento y todos los procesos dependientes que se necesitan para representar a cada uno de marco, así como realizan acciones adicionales (por ejemplo, copiar el marco representado en una ubicación de almacenamiento).

>[AZURE.NOTE] Las plantillas de administrador de trabajos y procesador de tareas son independientes entre sí, por lo que puede utilizar ambos o uno de ellos, dependiendo de los requisitos de su trabajo de cálculo y en las preferencias.

Como se muestra en el siguiente diagrama, un trabajo de cálculo que usa estas plantillas pasará a través de tres fases:

1. El código de cliente (por ejemplo, aplicación, servicio web, etc.), envía un trabajo del servicio de lote en Azure, que se especifica como el programa de administrador de trabajos de la tarea de su administrador de trabajos.

2. El servicio del lote ejecuta la tarea del Administrador de trabajo en un nodo de cálculo y el divisor de trabajo inicia el número especificado de las tareas del procesador de tareas, en como muchos nodos de cálculo según sea necesario, en función de los parámetros y especificaciones en el código de separador de trabajo.

3. Las tareas del procesador de tareas de forma independiente, ejecutarán en paralelo, para procesar los datos de entrada y generar los datos de salida.

![Diagrama que muestra cómo interactúa el código de cliente con el servicio de proceso por lotes][diagram01]

## <a name="prerequisites"></a>Requisitos previos

Para usar las plantillas de proceso por lotes, necesita lo siguiente:

* Un equipo con Visual Studio de 2015, o una versión posterior, ya instalado en él.

* Las plantillas de proceso por lotes, que están disponibles en la [Galería de Visual Studio] [ vs_gallery] como extensiones de Visual Studio. Hay dos maneras de obtener las plantillas:

  * Instalar las plantillas utilizando el cuadro de diálogo **extensiones y actualizaciones** en Visual Studio (para obtener más información, consulte [Buscar y usar extensiones de Visual Studio][vs_find_use_ext]). En el cuadro de diálogo **extensiones y actualizaciones** , buscar y descargar las dos extensiones siguientes:

    * Administrador de trabajos de lote Azure con separador de trabajo
    * Procesador de tareas por lotes de Azure

  * Descargar las plantillas de la galería en línea para Visual Studio: [Plantillas de proyectos de Microsoft Azure por lotes][vs_gallery_templates]

* Si tiene previsto usar la característica de [Paquetes de aplicaciones](batch-application-packages.md) para implementar el Administrador de trabajos y procesador de tareas con el lote de nodos de cálculo, debe vincular una cuenta de almacenamiento a su cuenta de lote.

## <a name="preparation"></a>Preparación

Recomendamos crear una solución que puede contener su jefe de proyecto, así como su procesador de tareas, ya que esto puede que sea más fácil compartir código entre el Administrador de trabajos y los programas de procesador de tareas. Para crear esta solución, siga estos pasos:

1. Abra 2015 de Visual Studio y seleccione **archivo** > **nuevo** > **proyecto**.

2. En **plantillas**, expanda **Otros tipos de proyectos**, haga clic en **Soluciones de Visual Studio**y, a continuación, seleccione la **Solución en blanco**.

3. Escriba un nombre que describa la aplicación y la finalidad de esta solución (por ejemplo, "LitwareBatchTaskPrograms").

4. Para crear la solución nueva, haga clic en **Aceptar**.

## <a name="job-manager-template"></a>Plantilla de administrador de trabajos

La plantilla de administrador de trabajos le ayuda a implementar una administrador de tarea que puede realizar las siguientes acciones:

* Dividir una tarea en varias tareas.
* Enviar las tareas que se ejecuten en lote.

>[AZURE.NOTE] Para obtener más información sobre el Administrador de tareas, consulte [Introducción a las características lote para desarrolladores](batch-api-basics.md#job-manager-task).

### <a name="create-a-job-manager-using-the-template"></a>Crear un administrador de trabajo con la plantilla

Para agregar un administrador de trabajos para la solución que creó anteriormente, siga estos pasos:

1. Abra la solución existente en Visual Studio de 2015.

2. En el Explorador de soluciones, haga clic en la solución, haga clic en **Agregar** > **Nuevo proyecto**.

3. En **Visual C#**, haga clic en la **nube**y, a continuación, haga clic en **Administrador de trabajos por lotes de Azure con separador de trabajo**.

4. Escriba un nombre que describe la aplicación e identifica este proyecto como el Administrador de trabajos (por ejemplo, "LitwareJobManager").

5. Para crear el proyecto, haga clic en **Aceptar**.

6. Por último, genere el proyecto para forzar Visual Studio para cargar todos los paquetes de NuGet que se hace referencia y comprobar que es válido el proyecto antes de comenzar a modificarlo.

### <a name="job-manager-template-files-and-their-purpose"></a>Archivos de plantilla de administrador de trabajos y su finalidad

Cuando se crea un proyecto mediante la plantilla de administrador de trabajos, genera tres grupos de archivos de código:

* El archivo de programa principal (Program.cs). Esta página contiene el punto de entrada del programa y de excepciones de nivel superior. Normalmente no es necesario modificar esto.

* El directorio de Framework. Esta página contiene los archivos de trabajo 'repetitivo' realizado por el programa de administrador de trabajos – hay problema parámetros, agregar tareas a la tarea por lotes, etcetera. Normalmente no es necesario que modificar estos archivos.

* El archivo de división de trabajo (JobSplitter.cs). Esto es donde se ponga su lógica específica de la aplicación para dividir una tarea en tareas.

Por supuesto puede agregar archivos adicionales según sea necesario para admitir el código de separador de trabajo, según la complejidad de la división lógica de trabajo.

La plantilla también genera archivos de proyecto estándares de .NET como un archivo app.config, packages.config, etcetera.

El resto de esta sección describe los diferentes archivos y su estructura de código y explica qué hace cada clase.

![Explorador de soluciones de Visual Studio que muestra al administrador de trabajos de solución de plantilla][solution_explorer01]

**Archivos de marco**

* `Configuration.cs`: Encapsula la carga de datos de configuración de trabajo como detalles de la cuenta de proceso por lotes, las credenciales de cuenta de almacenamiento vinculado, trabajo e información de la tarea y parámetros de trabajo. También proporciona acceso a las variables de entorno definidas por el lote (consulte Configuración del entorno de tareas, en la documentación del lote) a través de la clase Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Resume la implementación de la clase de configuración para que pueda prueba de unidad el divisor de trabajo mediante un objeto de configuración falsa o simulada.

* `JobManager.cs`: Organiza los componentes del programa de administrador de trabajos. Es responsable de inicialización el divisor de trabajo, invocar el divisor de trabajo y enviar las tareas devueltas por el divisor de trabajo en el remitente de la tarea.

* `JobManagerException.cs`: Representa un error que requiere el Administrador de trabajos terminar. JobManagerException se usa para ajustar errores 'esperados' dónde se puede proporcionar información de diagnóstico específica como parte de la cancelación.

* `TaskSubmitter.cs`: Esta clase es responsable de agregar tareas devueltas por el divisor de trabajo en el proceso. Los agregados de clase de administrador de trabajos la secuencia de tareas en lotes eficaz pero oportuna inclusión en el trabajo, a continuación, llama a TaskSubmitter.SubmitTasks en un subproceso de fondo para cada sección.

**Divisor de trabajo**

`JobSplitter.cs`: Esta clase contiene lógica específica de la aplicación para dividir el trabajo en tareas. El marco de trabajo invoca el método JobSplitter.Split para obtener una secuencia de tareas, que se agrega a la tarea como el método devuelve. Esta es la clase donde se insertará el valor lógico de su trabajo. Implementar el método de división para devolver una secuencia de instancias de CloudTask que representa las tareas en las que desea dividir el trabajo.

**Archivos de proyecto de línea de comandos de .NET estándares**

* `App.config`: Archivo de configuración de la aplicación .NET estándar.

* `Packages.config`: Archivo de dependencia de paquete NuGet estándar.

* `Program.cs`: Contiene el punto de entrada del programa y de excepciones de nivel superior.

### <a name="implementing-the-job-splitter"></a>Implementar el divisor de trabajo

Al abrir el proyecto de la plantilla de administrador de trabajos, el proyecto tendrá el archivo JobSplitter.cs abrir de forma predeterminada. Puede implementar la lógica de las tareas de la división de la carga de trabajo mediante el uso de la presentación de método Split() siguiente:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

>[AZURE.NOTE] La sección anotada en la `Split()` método es la sección sola el Administrador de trabajos de código de plantilla que sirve para que modificar agregando la lógica para dividir los trabajos en distintas tareas. Si desea modificar una sección diferente de la plantilla, asegúrese de que se familiarice con el funcionamiento del lote y pruebe algunos de los [ejemplos de código de proceso por lotes][github_samples].

La implementación Split() tiene acceso a:

* Los parámetros de trabajo a través de la `_parameters` campo.
* El objeto CloudJob que representa el trabajo a través de la `_job` campo.
* El objeto CloudTask que representa la tarea de tarea de administrador, a través de la `_jobManagerTask` campo.

Su `Split()` implementación no es necesario agregar tareas a la tarea directamente. En su lugar, el código debe devolver una secuencia de objetos de CloudTask y se agregarán al trabajo automáticamente las clases de framework que invocar el divisor de trabajo. Es común iterador del utilizar C# (`yield return`) característica implementar separadores de trabajo como permite las tareas Iniciar ejecutar tan pronto como sea posible en lugar de esperar para que todas las tareas que va a calcular.

**Error de división del trabajo**

Si el divisor de trabajo encuentra un error, correctamente:

* Terminar la secuencia utilizando C# `yield break` instrucción, en la que el Administrador de trabajos de mayúsculas y minúsculas se tratará como correcta; o

* Iniciar una excepción, en cuyo caso que se tratará como el Administrador de trabajos, no se pudo y puede volver a intentar dependiendo de cómo haya configurado el cliente).

En ambos casos, las tareas ya devueltos por el divisor de trabajo y agrega el trabajo por lotes se podrán ejecutar. Si no desea que esto suceda, a continuación, puede:

* Terminar el trabajo antes de abandonar el divisor de trabajo

* Formular la colección de tareas enteras antes de devolverlo (es decir, devolver un `ICollection<CloudTask>` o `IList<CloudTask>` en lugar de implementar el divisor de trabajo mediante un iterador C#)

* Usar las dependencias entre tareas para realizar todas las tareas dependen de la finalización correcta del Administrador de trabajos

**Reintentos de administrador de trabajo**

Si se produce un error en el Administrador de trabajos, puede a intentar por el servicio de lote según la configuración de reintentos de cliente. En general, esto es seguro, porque cuando el marco agregue tareas a la tarea, pasa por alto todas las tareas que ya existen. Sin embargo, si es muy costosa calcular tareas, no puede incurre en el costo de volver a calcular las tareas que ya se han agregado a la tarea; por el contrario, si no se garantiza que los vuelva a ejecutar para generar la misma tarea identificadores el comportamiento 'Omitir duplicados' no se ejecutará. En estos casos debe diseñar el divisor de trabajo para detectar el trabajo que ya se ha hecho y, por ejemplo no repetir realizando una CloudJob.ListTasks antes de empezar a tareas de rendimiento.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Códigos de salida y las excepciones en la plantilla de administrador de trabajos

Códigos de salida y excepciones proporcionan un mecanismo para determinar el resultado de la ejecución de un programa, y puede ayudar a identificar los problemas con la ejecución del programa. La plantilla de administrador de trabajos implementa los códigos de salida y excepciones descritas en esta sección.

Una administrador de tarea que se implementa con la plantilla de administrador de trabajos puede devolver tres códigos de salida posibles:

| Código | Descripción |
|------|-------------|
| 0    | El Administrador de trabajos que se completó correctamente. El código de separador de trabajo se ejecuta completamente y todas las tareas se han agregado al trabajo. |
| 1    | Error de la tarea de administrador de trabajo con una excepción en un elemento 'esperado' del programa. Se traduce la excepción un JobManagerException con información de diagnóstico y, cuando sea posible, sugerencias para solucionar el error. |
| 2    | Error de la tarea de administrador de trabajo con una excepción 'inesperada'. Se registró la excepción a la salida estándar, pero el Administrador de trabajos no pudo agregar información de diagnóstico o corrección adicional. |

En el caso de los errores de tareas de administrador de trabajos, algunas tareas pueden aún se agregaron al servicio antes de que se produjo el error. Estas tareas se ejecutarán con normalidad. Consulte "Error de división del trabajo" por encima de discusión de esta ruta de acceso de código.

Toda la información devuelta por excepciones se escribe en los archivos stdout.txt y stderr.txt. Para obtener más información, vea [Control de errores](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Consideraciones acerca del cliente

Esta sección describen algunos requisitos de implementación de cliente cuando se invoca un administrador de trabajo basado en esta plantilla. Vea [cómo pasar parámetros y variables de entorno del código de cliente](#pass-environment-settings) para obtener más información sobre cómo pasar parámetros y configuración del entorno.

**Credenciales obligatorio**

Para agregar tareas a la tarea por lotes de Azure, la tarea de administrador de trabajos requiere la dirección URL de cuenta de Azure lote y la clave. Se deben pasar en variables de entorno denominadas YOUR_BATCH_URL y YOUR_BATCH_KEY. También puede establecerlas en el Administrador de trabajos configuración de entorno de la tarea. Por ejemplo, en un cliente de C#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenciales de almacenamiento**

Normalmente, el cliente no es necesario proporcionar las credenciales de cuenta de almacenamiento vinculado a la tarea de la tarea de administrador porque no es necesario explícitamente acceso a la cuenta de almacenamiento vinculado a más administradores de trabajos y (b) la cuenta de almacenamiento vinculado a menudo se proporciona todas las tareas como una configuración de entorno común para el trabajo. Si no se proporciona la cuenta de almacenamiento vinculado a través de la configuración de entorno común y el Administrador de trabajos requiere acceso al almacenamiento vinculado, debe proporcionar las credenciales de almacenamiento vinculado como sigue:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Configuración de la tarea de trabajo administrador**

El cliente debe establecer el indicador de *killJobOnCompletion* del Administrador de trabajo como **Falso**.

Normalmente es seguro para el cliente establecer *runExclusive* en **false**.

El cliente debe usar la colección de *resourceFiles* o *applicationPackageReferences* para que el trabajo manager ejecutable (y sus DLL necesario) implementa en el nodo de cálculo.

De forma predeterminada, el Administrador de trabajos no se volverá si se produce un error. Dependiendo de la lógica de administrador de trabajo, el cliente que desee habilitar reintentos mediante *restricciones*/*maxTaskRetryCount*.

**Configuración del trabajo**

Si el divisor de trabajo emite tareas con dependencias, el cliente debe establecer usesTaskDependencies del trabajo en true.

En el modelo de división de trabajo, es poco común para los clientes que desea agregar tareas a proyectos más allá de lo que crea el divisor de trabajo. El cliente debe, por tanto, normalmente establecer del trabajo *onAllTasksComplete* **terminatejob**.

## <a name="task-processor-template"></a>Plantilla de procesador de tareas

Una plantilla de procesador de tareas le ayuda a implementar un procesador de tareas que puede realizar las siguientes acciones:

* Configurar la información requerida por cada tarea por lotes para ejecutar.
* Ejecutar todas las acciones necesarios para cada tarea por lotes.
* Guardar resultados de tarea en el almacenamiento permanente.

Aunque no es necesario un procesador de tareas para ejecutar las tareas en el proceso por lotes, la ventaja de usar un procesador de tareas clave es que proporciona un contenedor para implementar todas las acciones de ejecución de tareas en una ubicación. Por ejemplo, si necesita para ejecutar varias aplicaciones en el contexto de cada tarea, o si necesita copiar datos en un almacenamiento persistente después de completar cada tarea.

Las acciones realizadas por el procesador de tareas pueden ser como simple o compleja y muchos o pocos, según sea necesario por la carga de trabajo. Además, mediante la implementación de todas las acciones de tareas en un procesador de tareas, puede fácilmente actualizar o agregar acciones según los cambios en las aplicaciones o los requisitos de carga de trabajo. Sin embargo, en algunos casos un procesador de tareas podría no ser la solución óptima para su implementación puede agregar complejidad innecesaria, por ejemplo cuando se ejecuta trabajos que se pueden iniciar rápidamente desde una línea de comandos simple.

### <a name="create-a-task-processor-using-the-template"></a>Crear un procesador de tareas con la plantilla

Para agregar un procesador de tareas a la solución que creó anteriormente, siga estos pasos:

1. Abra la solución existente en Visual Studio de 2015.

2. En el Explorador de soluciones, haga clic en la solución, haga clic en **Agregar**y, a continuación, haga clic en **Nuevo proyecto**.

3. En **Visual C#**, haga clic en la **nube**y, a continuación, haga clic en **Procesador de tareas por lotes de Azure**.

4. Escriba un nombre que describe la aplicación e identifica este proyecto como procesador de tareas (por ejemplo, "LitwareTaskProcessor").

5. Para crear el proyecto, haga clic en **Aceptar**.

6. Por último, genere el proyecto para forzar Visual Studio para cargar todos los paquetes de NuGet que se hace referencia y comprobar que es válido el proyecto antes de comenzar a modificarlo.

### <a name="task-processor-template-files-and-their-purpose"></a>Archivos de plantilla de procesador de tareas y su finalidad

Cuando se crea un proyecto mediante la plantilla de procesador de tareas, genera tres grupos de archivos de código:

* El archivo de programa principal (Program.cs). Esta página contiene el punto de entrada del programa y de excepciones de nivel superior. Normalmente no es necesario modificar esto.

* El directorio de Framework. Esta página contiene los archivos de trabajo 'repetitivo' realizado por el programa de administrador de trabajos – hay problema parámetros, agregar tareas a la tarea por lotes, etcetera. Normalmente no es necesario que modificar estos archivos.

* El archivo de procesador de tareas (TaskProcessor.cs). Esto es donde se ponga la lógica específica de la aplicación para ejecutar una tarea (normalmente al llamar a un archivo ejecutable existente). Código de procesamiento anterior y posterior, como descargar datos adicionales o cargar archivos de resultados, también aquí.

Por supuesto puede agregar archivos adicionales según sea necesario para admitir el código de procesador de tareas, dependiendo de la complejidad de la tarea de división lógica.

La plantilla también genera archivos de proyecto estándares de .NET como un archivo app.config, packages.config, etcetera.

El resto de esta sección describe los diferentes archivos y su estructura de código y explica qué hace cada clase.

![Explorador de soluciones de Visual Studio que muestra la solución de plantilla de procesador de tareas][solution_explorer02]

**Archivos de marco**

* `Configuration.cs`: Encapsula la carga de datos de configuración de trabajo como detalles de la cuenta de proceso por lotes, las credenciales de cuenta de almacenamiento vinculado, trabajo e información de la tarea y parámetros de trabajo. También proporciona acceso a las variables de entorno definidas por el lote (consulte Configuración del entorno de tareas, en la documentación del lote) a través de la clase Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Resume la implementación de la clase de configuración para que pueda prueba de unidad el divisor de trabajo mediante un objeto de configuración falsa o simulada.

* `TaskProcessorException.cs`: Representa un error que requiere el Administrador de trabajos terminar. TaskProcessorException se usa para ajustar errores 'esperados' dónde se puede proporcionar información de diagnóstico específica como parte de la cancelación.

**Procesador de tareas**

* `TaskProcessor.cs`: Ejecuta la tarea. El marco de trabajo invoca el método de TaskProcessor.Run. Esta es la clase donde se insertan la lógica específica de la aplicación de la tarea. Implementar el método Run para:
  * Analizar y validar los parámetros de la tarea
  * Redacte la línea de comandos para cualquier programa externo que desea llamar
  * Inicie cualquier información de diagnóstico que puede requerir con fines de depuración
  * Iniciar un proceso utilizando la línea de comandos
  * Espere a que termine el proceso
  * Capturar el código de salida del proceso para determinar si realizó correctamente o no
  * Guarde los archivos de salida que desee conservar en un almacenamiento persistente

**Archivos de proyecto de línea de comandos de .NET estándares**

* `App.config`: Archivo de configuración de la aplicación .NET estándar.
* `Packages.config`: Archivo de dependencia de paquete NuGet estándar.
* `Program.cs`: Contiene el punto de entrada del programa y de excepciones de nivel superior.

## <a name="implementing-the-task-processor"></a>Implementar el procesador de tareas

Al abrir el proyecto de plantilla de procesador de tareas, el proyecto tendrá el archivo TaskProcessor.cs abrir de forma predeterminada. Puede implementar la lógica de ejecución de las tareas de su carga de trabajo a través del método Run() que se muestra a continuación:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
>[AZURE.NOTE] La sección anotada en el método Run() es la única del código de plantilla de procesador de tareas está destinado a modificar agregando la lógica de ejecución de las tareas de su carga de trabajo. Si desea modificar una sección diferente de la plantilla, por favor, familiarizarse con el funcionamiento del lote revisando la documentación del lote y probando algunos de los ejemplos de código del lote.

El método Run() es responsable de ejecutar la línea de comandos, iniciar uno o varios procesos, esperando todos los procesos completar, guardar los resultados y, por último regreso con un código de salida. El método Run() es donde implementar la lógica de procesamiento de tareas. El marco de trabajo de tarea procesador invoca el método Run() no es necesario llamarlo usted mismo.

La implementación Run() tiene acceso a:

* Los parámetros de la tarea a través de la `_parameters` campo.
* Los identificadores de trabajo y tareas, a través de la `_jobId` y `_taskId` campos.
* La configuración de la tarea a través de la `_configuration` campo.

**Error en la tarea**

En caso de error, puede salir del método Run() iniciando una excepción, pero esto deja el controlador de excepciones de nivel superior en el control de los códigos de salida de la tarea. Si necesita controlar el código de salida para que pueda distinguir diferentes tipos de error, por ejemplo con fines de diagnóstico o porque algunos modos de error deben terminar el trabajo y otros no, debe salir del método Run() devolver un código de salida distinto de cero. Se convertirá en el código de salida de la tarea.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Códigos de salida y las excepciones en la plantilla de procesador de tareas

Códigos de salida y excepciones proporcionan un mecanismo para determinar el resultado de la ejecución de un programa, y puede ayudar a identificar los problemas con la ejecución del programa. La plantilla de tarea procesador implementa los códigos de salida y excepciones descritas en esta sección.

Una tarea de procesador de tareas que se implementa con la plantilla de procesador de tareas puede devolver tres códigos de salida posibles:

| Código | Descripción |
|------|-------------|
|  [Process.ExitCode][process_exitcode] | Procesador de tareas se ejecuta completamente. Tenga en cuenta que esto no implica que el programa que fue correcto: solo que el procesador de tareas invoca de correctamente y realizar cualquier procesamiento posterior sin excepciones. El significado de los códigos de salida depende del programa invocado: normalmente el código de salida 0 significa el programa se realizó correctamente y cualquier otro tipo de código de salida significa que el error del programa. |
| 1    | Procesador de tareas error con una excepción en un elemento 'esperado' del programa. Se traduce la excepción un `TaskProcessorException` con información de diagnóstico y, cuando sea posible, sugerencias para solucionar el error. |
| 2    | Procesador de tareas error con una excepción 'inesperada'. Se registró la excepción a la salida estándar, pero no puede agregar información de diagnóstico o corrección adicional procesador de tareas. |

>[AZURE.NOTE] Si el programa que se invoca utiliza códigos de salida de 1 y 2 para indicar modos de error específico, a continuación, usar códigos de salida de 1 y 2 para errores del procesador de tareas es ambiguo. Puede cambiar estos códigos de error del procesador de tareas a códigos de salida distintivo editando los casos de excepción en el archivo Program.cs.

Toda la información devuelta por excepciones se escribe en los archivos stdout.txt y stderr.txt. Para obtener más información, vea control de errores, en la documentación del lote.

### <a name="client-considerations"></a>Consideraciones acerca del cliente

**Credenciales de almacenamiento**

Si su procesador de tareas utiliza almacenamiento de blobs de Windows Azure para conservar los resultados, por ejemplo utilizando la biblioteca auxiliar de convenciones de archivo, a continuación, necesita acceso a *en* la nube almacenamiento cuenta credenciales *o* una dirección URL de contenedor de blob que incluye una firma de acceso compartido (SA). La plantilla incluye compatibilidad para proporcionar credenciales a través de variables de entorno común. El cliente puede pasar las credenciales de almacenamiento como sigue:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

La cuenta de almacenamiento, a continuación, está disponible en la clase TaskProcessor a través de la `_configuration.StorageAccount` propiedad.

Si prefiere usar una dirección URL de contenedor con SA, también puede pasar esto a través de una configuración de entorno común de trabajo, pero la plantilla de procesador de tareas actualmente no incluye compatibilidad integrada para esto.

**Instalación de almacenamiento**

Se recomienda que la tarea de administrador de trabajo o el cliente de crear los contenedores necesarios para que las tareas antes de agregar las tareas a la tarea. Esto es obligatorio que si usa una dirección URL de contenedor con SA, como por ejemplo una dirección URL no incluye permisos para crear el contenedor. Se recomienda incluso si pasa credenciales de cuenta de almacenamiento, tal y como se guarda todas las tareas que tener que llamar a CloudBlobContainer.CreateIfNotExistsAsync en el contenedor.

## <a name="pass-parameters-and-environment-variables"></a>Pasar parámetros y variables de entorno

### <a name="pass-environment-settings"></a>Pasar de la configuración de entorno

Un cliente puede pasar la información a la tarea de administrador de trabajo en el formulario de configuración del entorno. Esta información, a continuación, puede ser usada por el Administrador de tarea al generar las tareas de procesador de tarea que se ejecutarán como parte del trabajo de cálculo. Ejemplos de la información que se puede pasar como la configuración de entorno son:

* Teclas de nombre y la cuenta de la cuenta de almacenamiento
* Dirección URL de cuenta por lotes
* Clave de cuenta por lotes

El servicio de lote tiene un mecanismo simple para pasar la configuración de entorno para una tarea de la tarea de administrador mediante la `EnvironmentSettings` propiedad en [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Por ejemplo, para obtener la `BatchClient` instancia de una cuenta por lotes, puede pasar la dirección URL y credenciales de claves compartidas para la cuenta del lote de código de variables de entorno del cliente. Del mismo modo, para obtener acceso a la cuenta de almacenamiento que está vinculada a la cuenta de proceso por lotes, puede pasar el nombre de la cuenta de almacenamiento y la clave de cuenta de almacenamiento como variables de entorno.

### <a name="pass-parameters-to-the-job-manager-template"></a>Pasar parámetros a la plantilla de administrador de trabajos

En muchos casos, es útil pasar parámetros por trabajo a la tarea de administrador de trabajo para controlar el proceso de división de trabajo o para configurar las tareas para el trabajo. Para ello, puede cargar un archivo JSON denominado parameters.json como un archivo de recursos de la tarea de administrador de trabajos. Los parámetros pueden están disponibles en la `JobSplitter._parameters` campo en la plantilla de administrador de trabajos.

>[AZURE.NOTE] El controlador de parámetro integrados admite solamente los diccionarios de cadena de cadena. Si desea pasar valores JSON complejos como valores de parámetro, debe pasar estos elementos como cadenas y su análisis en el divisor de trabajo o modificar el marco `Configuration.GetJobParameters` método.

### <a name="pass-parameters-to-the-task-processor-template"></a>Pasar parámetros a la plantilla de procesador de tareas

También puede pasar parámetros a tareas individuales implementadas con la plantilla de procesador de tareas. Al igual que con la plantilla de administrador de tareas, la plantilla de procesador de tareas busca un archivo de recursos con el nombre

Parameters.JSON y si encuentra carga como el diccionario de parámetros. Hay un par de opciones para cómo pasar parámetros a las tareas del procesador de tareas:

* Volver a usar los parámetros de trabajo JSON. Esto funciona bien si los únicos parámetros son todo el trabajo (por ejemplo, una representación del alto y ancho). Para ello, al crear un CloudTask en el divisor de trabajo, agregue una referencia al objeto de archivo de recursos de parameters.json de ResourceFiles de la tarea de administrador de trabajos (`JobSplitter._jobManagerTask.ResourceFiles`) a la colección de ResourceFiles de CloudTask.

* Generar y cargar un documento de parameters.json específicos de la tarea como parte de la ejecución de división del trabajo y referencia blob en colección de archivos de recursos de la tarea. Esto es necesario si diferentes tareas tienen diferentes parámetros. Un ejemplo podría ser un escenario de procesamiento 3D donde el índice de fotograma se pasa a la tarea como un parámetro.

>[AZURE.NOTE] El controlador de parámetro integrados admite solamente los diccionarios de cadena de cadena. Si desea pasar valores JSON complejos como valores de parámetro, debe pasar estos elementos como cadenas y su análisis en el procesador de tareas o modificar el marco `Configuration.GetTaskParameters` método.

## <a name="next-steps"></a>Pasos siguientes

### <a name="persist-job-and-task-output-to-azure-storage"></a>Conservar los resultados de tarea y el trabajo con el almacenamiento de Azure

Otra herramienta útil en el desarrollo de soluciones de lote es [Convenciones de archivo por lotes de Azure][nuget_package]. Use esta biblioteca de clases .NET (actualmente en la vista previa) en las aplicaciones de .NET lote para almacenar y recuperar los resultados de la tarea a y desde el almacenamiento de Azure fácilmente. [Conservar el proceso de Azure y tareas de salida](batch-task-output.md) contiene una descripción completa de la biblioteca y su uso.

### <a name="batch-forum"></a>Foro de proceso por lotes

El [Foro de Azure lote] [ forum] en MSDN es un lugar ideal para tratar el lote y formular preguntas acerca del servicio. Cabeza de sobre para publicaciones "rápidas" útiles y publique sus preguntas que surjan mientras crea soluciones lote.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
