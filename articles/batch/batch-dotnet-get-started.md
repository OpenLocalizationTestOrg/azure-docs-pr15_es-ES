<properties
    pageTitle="Tutorial: Introducción a la biblioteca de Azure lote .NET | Microsoft Azure"
    description="Obtenga información sobre los conceptos básicos del lote de Azure y cómo desarrollar para el servicio de lote con un escenario de ejemplo."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="08/15/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-library-for-net"></a>Introducción a la biblioteca por lotes de Azure para .NET

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Aprenda los conceptos básicos del [Lote de Azure] [ azure_batch] y [Lote .NET] [ net_api] biblioteca en este artículo como veremos una aplicación de ejemplo paso a paso de C#. Veremos cómo esta aplicación de ejemplo aprovecha el servicio por lotes para procesar una carga de trabajo paralelo en la nube, así como el modo en que interactúa con [El almacenamiento de Azure](../storage/storage-introduction.md) para ensayo de archivo y la recuperación. Aprenderá técnicas de flujo de trabajo de aplicación lote comunes. También podrá obtener un conocimiento de base de los componentes principales del lote, como tareas, tareas, grupos y nodos de cálculo.

![Flujo de trabajo de solución de lote (basic)][11]<br/>

## <a name="prerequisites"></a>Requisitos previos

En este artículo se supone que tiene un conocimiento práctico de C# y Visual Studio. También se supone que es capaz de satisfacer los requisitos de creación de cuenta se especifican debajo de Azure y los servicios de almacenamiento y por lotes.

### <a name="accounts"></a>Cuentas

- **Cuenta de Azure**: si todavía no tiene una suscripción de Azure, [crear una cuenta gratuita de Azure][azure_free_account].
- **Cuenta de proceso por lotes**: una vez que tenga una suscripción de Azure, [Cree una cuenta de Azure lote](batch-account-create-portal.md).
- **Cuenta de almacenamiento**: consulte [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md).

> [AZURE.IMPORTANT] Lote actualmente admite *únicamente* el tipo de cuenta de almacenamiento de **Propósito General** , como se describe en el paso 5 # [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md).

### <a name="visual-studio"></a>Visual Studio

Debe tener **2015 de Visual Studio** para generar el proyecto de ejemplo. Puede encontrar versiones gratuitas y de prueba de Visual Studio en la [Visión general de los productos de Visual Studio de 2015][visual_studio].

### <a name="dotnettutorial-code-sample"></a>Ejemplo de código de *DotNetTutorial*

La [DotNetTutorial] [ github_dotnettutorial] muestra es uno de los muchos ejemplos de código que se encuentra en los [ejemplos del lote de azure] [ github_samples] repositorio de GitHub. Puede descargar los datos de ejemplo haciendo clic en el botón **Descargar ZIP** en la página de inicio de repositorio o haciendo clic en el [azure-lote-ejemplos-master.zip] [ github_samples_zip] vínculo de descarga directa. Una vez que haya extraído el contenido del archivo ZIP, encontrará la solución en la carpeta siguiente:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Explorador de Azure lote (opcional)

El [Explorador de Azure lote] [ github_batchexplorer] es una utilidad gratuita que se incluye en los [ejemplos del lote de azure] [ github_samples] repositorio de GitHub. Aunque no es necesario para completar este tutorial, puede ser útil al desarrollar y depurar las soluciones de lote.

## <a name="dotnettutorial-sample-project-overview"></a>Información general del proyecto de ejemplo de DotNetTutorial

El ejemplo de código de *DotNetTutorial* es una solución de Visual Studio de 2015 que consta de dos proyectos: **DotNetTutorial** y **TaskApplication**.

- **DotNetTutorial** es la aplicación cliente que interactúa con los servicios de almacenamiento y lote para ejecutar una carga de trabajo paralelo en calcular nodos (máquinas virtuales). DotNetTutorial se ejecuta en su estación de trabajo.

- **TaskApplication** es el programa que se ejecute en los nodos de cálculo en Azure para realizar el trabajo real. En el ejemplo, `TaskApplication.exe` analiza el texto en un archivo descargado desde el almacenamiento de Azure (el archivo de entrada). A continuación, genera un archivo de texto (el archivo de salida) que contiene una lista de las tres palabras principales que aparecen en el archivo de entrada. Después de crear el archivo de salida, TaskApplication carga el archivo en el almacenamiento de Azure. Esto hace que esté disponible para la aplicación cliente para descargar. TaskApplication se ejecuta en paralelo en varios nodos de cálculo en el servicio de lote.

El siguiente diagrama muestra las principales operaciones realizadas por la aplicación cliente, *DotNetTutorial*y la aplicación que se ejecuta por las tareas, *TaskApplication*. Este flujo de trabajo básico es típico de muchas soluciones de cálculo que se crean con lote. Mientras no muestra todas las características disponibles en el servicio de proceso por lotes, casi todas las situaciones lote incluyen procesos similares.

![Flujo de trabajo de ejemplo de proceso por lotes][8]<br/>

[**Paso 1.**](#step-1-create-storage-containers) Crear **contenedores** de almacenamiento de blobs de Windows Azure.<br/>
[**Paso 2.**](#step-2-upload-task-application-and-data-files) Cargar archivos de la aplicación de tareas y archivos de entrada a contenedores.<br/>
[**Paso 3.**](#step-3-create-batch-pool) Crear un lote del **grupo**.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** El grupo **se ha StartTask** descarga los archivos binarios de tarea (TaskApplication) en los nodos como unirse a la agrupación.<br/>
[**Paso 4.**](#step-4-create-batch-job) Crear un lote del **trabajo**.<br/>
[**Paso 5.**](#step-5-add-tasks-to-job) Agregar **tareas** a la tarea.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Las tareas se programan para ejecutar en nodos.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5 b.** Cada tarea descarga sus datos de entrada desde el almacenamiento de Azure y luego comienza la ejecución.<br/>
[**Paso 6.**](#step-6-monitor-tasks) Supervisar las tareas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Como tareas están completadas, cargan sus datos de salida para el almacenamiento de Azure.<br/>
[**Paso 7.**](#step-7-download-task-output) Descargar los resultados de la tarea de almacenamiento.

Como se mencionó, no cada solución lote realiza estos pasos exactos y puede incluir muchos otros, pero la aplicación de ejemplo *DotNetTutorial* muestra los procesos comunes que se encuentra en una solución por lotes.

## <a name="build-the-dotnettutorial-sample-project"></a>Crear el proyecto de ejemplo de *DotNetTutorial*

Antes de poder ejecutar correctamente el ejemplo, debe especificar el lote y el almacenamiento de credenciales de la cuenta en el proyecto *DotNetTutorial* `Program.cs` archivo. Si aún no lo ha hecho, abra la solución en Visual Studio haciendo doble clic en el `DotNetTutorial.sln` archivo de solución. O abrir desde dentro de Visual Studio mediante la **archivo > Abrir > proyecto o solución** menú.

Abrir `Program.cs` dentro del proyecto *DotNetTutorial* . A continuación, agregue sus credenciales como se especifica en la parte superior del archivo:

```
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [AZURE.IMPORTANT] Como se mencionó anteriormente, debe especificar las credenciales de una cuenta de almacenamiento de **Propósito General** actualmente en el almacenamiento de Azure. Las aplicaciones de lote utilizar almacenamiento de blobs dentro de la cuenta de almacenamiento de **Propósito General** . No especifique las credenciales de una cuenta de almacenamiento que se creó, seleccione el tipo de cuenta de *almacenamiento de blobs* .

Puede encontrar el lote y el almacenamiento de credenciales de su cuenta en el módulo de cuenta de cada servicio en el [portal de Azure][azure_portal]:

![Credenciales en el portal de lotes][9]
![credenciales de almacenamiento en el portal][10]<br/>

Ahora que ha actualizado el proyecto con sus credenciales, haga clic en la solución en el Explorador de soluciones y haga clic en **Generar solución**. Si se le solicita, confirme la restauración de los paquetes de NuGet.

> [AZURE.TIP] Si no se restauran automáticamente los paquetes de NuGet, o si ve errores sobre un error de restaurar los paquetes, asegúrese de que tiene el [Administrador de paquetes de NuGet] [ nuget_packagemgr] instalado. A continuación, habilite la descarga de paquetes omitidos. Vea [Habilitar paquete restaurar durante la compilación] [ nuget_restore] para habilitar la descarga del paquete.

En las secciones siguientes, dividir la aplicación de ejemplo en los pasos que realiza para procesar una carga de trabajo en el servicio de lote y tratar esos pasos en detalle. Le recomendamos que hacen referencia a la solución abierta en Visual Studio, mientras que trabaja con el resto de este artículo, ya que no todas las líneas de código en el ejemplo se tratan.

Desplácese hasta la parte superior de la `MainAsync` método en el proyecto *DotNetTutorial* `Program.cs` archivo para iniciar el paso 1. A continuación, a continuación, aproximadamente cada paso sigue a la progresión de llamadas de método de `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Paso 1: Crear contenedores de almacenamiento

![Crear contenedores de almacenamiento de Azure][1]
<br/>

Proceso por lotes incluye compatibilidad integrada para interactuar con el almacenamiento de Azure. Contenedores en su cuenta de almacenamiento le proporcionará los archivos necesarios por las tareas que se ejecutan en su cuenta por lotes. Los contenedores también proporcionan un lugar para almacenar los datos de salida que producen las tareas. Lo primero que hace la aplicación de cliente de *DotNetTutorial* es crear tres contenedores de [Almacenamiento de blobs de Windows Azure](../storage/storage-introduction.md):

- **aplicación**: este contenedor guardará la ejecución por las tareas, así como cualquiera de sus dependencias, como los archivos DLL de la aplicación.
- **entrada**: tareas descargarán los archivos de datos para procesar desde el contenedor de *entrada* .
- **resultado**: tareas completar el procesamiento de archivo de entrada, cargará los resultados al contenedor de *salida* .

Para interactuar con una cuenta de almacenamiento y crear contenedores, usamos la [Biblioteca de cliente de almacenamiento de Azure para .NET][net_api_storage]. Crear una referencia a la cuenta con [CloudStorageAccount][net_cloudstorageaccount]y de que crear un [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Usamos el `blobClient` referencia en toda la aplicación y pasar como un parámetro para varios métodos. Es un ejemplo de esto en el bloque de código que sigue inmediatamente a la anterior, donde llamamos `CreateContainerIfNotExistAsync` para crear los contenedores.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Una vez que se han creado los contenedores, la aplicación puede cargar los archivos que se usará en las tareas.

> [AZURE.TIP] [Cómo usar el almacenamiento de blobs de .NET](../storage/storage-dotnet-how-to-use-blobs.md) proporciona una buena introducción de trabajar con BLOB y contenedores de almacenamiento de Azure. Al empezar a trabajar con el lote debe ser cerca de la parte superior de la lista de lectura.

## <a name="step-2-upload-task-application-and-data-files"></a>Paso 2: Cargar archivos de datos y la aplicación de la tarea

![Cargar archivos de entrada (datos) y aplicación de la tarea en contenedores][2]
<br/>

En la operación de carga de archivo, *DotNetTutorial* define primero colecciones de rutas de acceso de archivo de **entrada** y de **aplicación** que ya existen en el equipo local. A continuación, cargará estos archivos a los contenedores que creó en el paso anterior.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Existen dos métodos de `Program.cs` que intervienen en el proceso de carga:

- `UploadFilesToContainerAsync`: Este método devuelve una colección de [ResourceFile] [ net_resourcefile] objetos (se explica a continuación) e internamente llamadas `UploadFileToContainerAsync` cargar cada archivo que se pasa en el parámetro *filePaths* .
- `UploadFileToContainerAsync`: Este es el método que realmente realiza la carga de archivos y crea la [ResourceFile] [ net_resourcefile] objetos. Después de cargar el archivo, obtiene una firma de acceso compartido (SA) para el archivo y devuelve un objeto ResourceFile que lo representa. Compartir acceso firmas también se describen a continuación.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath, FileMode.Open);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles

Un [ResourceFile] [ net_resourcefile] proporciona tareas en el lote con la dirección URL a un archivo de almacenamiento de Azure que se descarga en un nodo de cálculo antes de que se ejecute esa tarea. La [ResourceFile.BlobSource] [ net_resourcefile_blobsource] propiedad especifica la dirección URL completa del archivo, ya que existe en el almacenamiento de Azure. La dirección URL también puede incluir una firma de acceso compartido (SA) que proporciona acceso seguro para el archivo. Los tipos de tareas mayoría dentro de lote .NET incluyen una propiedad *ResourceFiles* , incluidos:

- [CloudTask][net_task]
- [Se ha StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

La aplicación de ejemplo de DotNetTutorial no utiliza los tipos de tareas JobPreparationTask o JobReleaseTask, pero puede obtener más información acerca de ellos en [tareas de preparación y finalización de trabajo de ejecución en lotes de Azure nodos de cálculo](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Firma de acceso compartido (SA)

Acceso compartido firmas son cadenas que, cuando se incluye como parte de una dirección URL, proporcione acceso seguro a contenedores y BLOB en el almacenamiento de Azure. La DotNetTutorial aplicación utiliza blob y contenedor compartido acceso a direcciones URL de firma y se muestra cómo obtener estas cadenas de firma acceso compartido desde el servicio de almacenamiento.

- **BLOB compartido firmas de acceso**: se ha de StartTask del grupo en DotNetTutorial utiliza firmas de acceso de blobs compartido cuando descargue los archivos de datos de entrada y archivos binarios de aplicación de almacenamiento (vea el paso 3 a continuación). La `UploadFileToContainerAsync` método de DotNetTutorial `Program.cs` contiene el código que obtiene la firma de acceso compartido de cada blob. Para ello, llamando [CloudBlob.GetSharedAccessSignature][net_sas_blob].

- **Contenedor compartido firmas de acceso**: como cada tarea finalice su trabajo en el nodo de cálculo, carga su archivo de salida para el contenedor de *salida* en el almacenamiento de Azure. Para ello, TaskApplication utiliza una firma de acceso compartido de contenedor que proporciona acceso de escritura al contenedor como parte de la ruta de acceso cuando carga el archivo. Obtener la firma de acceso compartido de contenedor se realiza de manera similar a como al obtener el blob compartido firma de access. En DotNetTutorial, verá que la `GetContainerSasUrl` método auxiliar llama [CloudBlobContainer.GetSharedAccessSignature] [ net_sas_container] para hacerlo. Podrá leer más información acerca de cómo TaskApplication usa el contenedor comparten firma de acceso en "paso 6: tareas Monitor."

> [AZURE.TIP] Consulte la serie de dos partes en firmas de acceso compartido, [parte 1: información sobre el modelo de firma (SA) acceso compartido](../storage/storage-dotnet-shared-access-signature-part-1.md) y [parte 2: crear y usar una firma de acceso compartido (SA) con el almacenamiento de blobs](../storage/storage-dotnet-shared-access-signature-part-2.md), para obtener más información sobre cómo proporcionar acceso seguro a los datos de su cuenta de almacenamiento.

## <a name="step-3-create-batch-pool"></a>Paso 3: Crear grupo por lotes

![Crear un grupo por lotes][3]
<br/>

Un **grupo** de lote es una colección de nodos de cálculo (máquinas virtuales) en los que el lote ejecuta las tareas de la tarea.

Después de que cargue los archivos de datos y la aplicación a la cuenta de almacenamiento, *DotNetTutorial* inicia su interacción con el servicio de lote mediante la biblioteca de .NET lote. Para hacerlo, un [BatchClient] [ net_batchclient] se crea por primera vez:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

A continuación, se crea un grupo de nodos de cálculo en la cuenta de proceso por lotes con una llamada a `CreatePoolAsync`. `CreatePoolAsync`usa el [BatchClient.PoolOperations.CreatePool] [ net_pool_create] método para crear un grupo en el servicio de lote.

```csharp
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,           // 3 compute nodes
            virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
            cloudServiceConfiguration:
                new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Al crear un grupo con [CreatePool][net_pool_create], especifique varios parámetros como el número de nodos de cálculo, el [tamaño de los nodos](../cloud-services/cloud-services-sizes-specs.md)y sistema operativo de los nodos. En *DotNetTutorial*, usamos [CloudServiceConfiguration] [ net_cloudserviceconfiguration] para especificar Windows Server 2012 R2 de [Servicios en la nube](../cloud-services/cloud-services-guestos-update-matrix.md). Sin embargo, especificando una [VirtualMachineConfiguration] [ net_virtualmachineconfiguration] en su lugar, puede crear grupos de nodos creados a partir de imágenes de Marketplace, que incluya imágenes de Windows y Linux: vea [aprovisionar Linux nodos de cálculo en grupos de Azure lote](batch-linux-nodes.md) para obtener más información.

> [AZURE.IMPORTANT] Se cargan para recursos de cálculo en lote. Para reducir los costos, puede reducir `targetDedicated` a 1 antes de ejecutar el ejemplo.

Junto con estas propiedades de nodo físico, también puede especificar un [se ha StartTask] [ net_pool_starttask] para el grupo. El se ha StartTask se ejecuta en cada nodo como nodo une el grupo y, a continuación, cada vez que se reinicia un nodo. El se ha StartTask es especialmente útil para instalar aplicaciones en los nodos de cálculo antes de la ejecución de las tareas. Por ejemplo, si las tareas para procesar datos mediante secuencias de comandos de Python, podría usar un se ha StartTask instalar Python en los nodos de cálculo.

En este ejemplo de aplicación, la se ha StartTask copia los archivos que descarga de almacenamiento (que se especifican mediante la [se ha StartTask][net_starttask].[ ResourceFiles] [ net_starttask_resourcefiles] propiedad) desde el directorio de trabajo se ha StartTask al directorio compartido que pueden tener acceso *todas las* tareas que se ejecuta en el nodo. Básicamente, Esto copiará `TaskApplication.exe` sus dependencias al directorio compartido en cada nodo mientras el nodo une a la agrupación, para que las tareas que se ejecutan en el nodo pueden obtener acceso a ella.

> [AZURE.TIP] La característica de **paquetes de aplicaciones** del lote de Azure proporciona otra forma de obtener la aplicación en los nodos de cálculo en un grupo. Para obtener información detallada, vea [implementación de aplicaciones con los paquetes de aplicaciones de Azure lote](batch-application-packages.md) .

También notables del fragmento de código anterior es el uso de dos variables de entorno en la propiedad de la *línea de comandos* de la se ha StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` y `%AZ_BATCH_NODE_SHARED_DIR%`. Cada nodo de cálculo dentro de un grupo de lote se configura automáticamente con varias variables de entorno específicas de lote. Todos los procesos que se ejecutan una tarea tiene acceso a estas variables de entorno.

> [AZURE.TIP] Para obtener más información sobre el entorno de variables que están disponibles en los nodos de cálculo en un grupo de lote y la información de directorios de trabajo de tarea, consulte las secciones de [configuración de entorno para las tareas](batch-api-basics.md#environment-settings-for-tasks) y [directorios y archivos](batch-api-basics.md#files-and-directories) en la [Visión general de la característica de lote para desarrolladores](batch-api-basics.md).

## <a name="step-4-create-batch-job"></a>Paso 4: Crear trabajo por lotes

![Crear tarea por lotes][4]<br/>

**Trabajo** por lotes es un conjunto de tareas y está asociado con un grupo de nodos de cálculo. Las tareas de un trabajo se ejecutan en los nodos de cálculo del grupo asociado.

Puede usar un trabajo no sólo para organizar y seguimiento de tareas en las cargas de trabajo relacionadas, sino también para imponer ciertas restricciones, como el tiempo de ejecución máximo para el trabajo (y por extensión, sus tareas), así como de prioridad de trabajo en relación con otros trabajos en la cuenta de proceso por lotes. En este ejemplo, sin embargo, el trabajo sólo está asociado con el grupo que se creó en el paso 3 de #. No se configuran propiedades adicionales.

Todas las tareas por lotes están asociadas a un grupo específico. Esta asociación indica qué tareas de trabajo se ejecutarán en los nodos. Especificarlo mediante la [CloudJob.PoolInformation] [ net_job_poolinfo] propiedad, como se muestra en el fragmento de código siguiente.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Ahora que se ha creado un trabajo, las tareas se agregan a realizar el trabajo.

## <a name="step-5-add-tasks-to-job"></a>Paso 5: Agregar tareas a la tarea

![Agregar tareas a la tarea][5]<br/>
*(1) tareas se agregan a la tarea, (2) las tareas se programan para que se ejecute en los nodos y (3) las tareas descargar los archivos de datos para procesar*

Lote las **tareas** son las unidades de trabajo que se ejecutan en los nodos de cálculo. Una tarea tiene una línea de comandos y ejecuta los scripts o ejecutables que especifique en la línea de comandos.

Para llevar a cabo trabajo, las tareas deben agregarse a una tarea. Cada [CloudTask] [ net_task] está configurado con una propiedad de línea de comandos y [ResourceFiles] [ net_task_resourcefiles] (como las se ha StartTask del grupo) que la tarea se descarga al nodo antes de la línea de comandos se ejecuta automáticamente. En el proyecto de ejemplo *DotNetTutorial* , cada tarea procesa un solo archivo. Por lo tanto, su colección de ResourceFiles contiene un solo elemento.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Cuando tengan acceso a las variables de entorno como `%AZ_BATCH_NODE_SHARED_DIR%` o ejecutar una aplicación no se encuentra en el nodo `PATH`, deberá ir precedidas líneas de comandos de tareas `cmd /c`. Esto explícitamente ejecutar el intérprete de comandos e indicarle que se va a finalizar después de ejecutar el comando. Este requisito es innecesario si las tareas ejecutan una aplicación en el nodo `PATH` (como *robocopy.exe* o *powershell.exe*) y no se utilizan variables de entorno.

Dentro de la `foreach` bucle del fragmento de código anterior, puede ver que se crea la línea de comandos de la tarea que se pasan tres argumentos de línea de comandos para *TaskApplication.exe*:

1. El **primer argumento** es la ruta de acceso del archivo para procesar. Esta es la ruta de acceso local al archivo tal y como existe en el nodo. Cuando la ResourceFile objeto en `UploadFileToContainerAsync` se crea por primera vez, el nombre de archivo se utiliza para esta propiedad (como un parámetro al constructor ResourceFile). Esto indica que el archivo puede encontrarse en el mismo directorio que *TaskApplication.exe*.

2. El **segundo argumento** especifica que deben escribirse las palabras *N* superiores al archivo de salida. En el ejemplo, esto es modificable para que se escriben las tres palabras principales en el archivo de salida.

3. El **tercer argumento** es la firma de acceso compartido (SA) que proporciona acceso de escritura en el contenedor de **salida** en el almacenamiento de Azure. *TaskApplication.exe* usa esta dirección URL de firma de acceso compartido cuando carga el archivo de salida para el almacenamiento de Azure. Puede encontrar el código para esta característica en la `UploadFileToContainer` método en el proyecto TaskApplication `Program.cs` archivo:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath, FileMode.Open);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Paso 6: Tareas de Monitor

![Supervisar tareas][6]<br/>
*La aplicación de cliente (1) supervisa las tareas de finalización y estado correcto y, a continuación, (2) las tareas cargar los datos de resultado en el almacenamiento de Azure*

Cuando las tareas se agregan a una tarea, automáticamente están en la cola y programadas para que se ejecute en los nodos de cálculo en el grupo asociado con el trabajo. Según la configuración que especifique, por lotes controladores de cola de todas las tareas, programación, Reintentar y otras tareas de administración de tareas para usted. Existen varios métodos para supervisar la ejecución de la tarea. DotNetTutorial muestra un ejemplo sencillo que informa solamente de finalización y tareas o fracaso Estados.

Dentro de la `MonitorTasks` método de DotNetTutorial `Program.cs`, hay tres conceptos lote .NET que garantizan la discusión. Se muestran debajo de su orden de aparición:

1. **ODATADetailLevel**: especificar [ODATADetailLevel] [ net_odatadetaillevel] en la lista es fundamental para garantizar el rendimiento de la aplicación de lote operaciones (por ejemplo, para obtener una lista de tareas de la tarea). Agregar [consultar el servicio de Azure lote eficazmente](batch-efficient-list-queries.md) a la lista de lectura si planea hacer a algún tipo de estado de supervisión dentro de sus aplicaciones de lote.

2. **TaskStateMonitor**: [TaskStateMonitor] [ net_taskstatemonitor] proporciona a las aplicaciones de .NET lote con utilidades auxiliares para supervisar los Estados de tarea. En `MonitorTasks`, *DotNetTutorial* espera para que todas las tareas llegar a [TaskState.Completed] [ net_taskstate] en un plazo. A continuación, finaliza el trabajo.

3. **TerminateJobAsync**: terminar un trabajo con [JobOperations.TerminateJobAsync] [ net_joboperations_terminatejob] (o el bloqueo JobOperations.TerminateJob) marca ese trabajo como completado. Es fundamental para hacerlo si su solución de lote utiliza un [JobReleaseTask][net_jobreltask]. Esto es un tipo especial de tarea, que se describe en [las tareas de preparación y finalización de tarea](batch-job-prep-release.md).

La `MonitorTasks` método de *DotNetTutorial* `Program.cs` aparece debajo de:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a scheduling error
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the
        // Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.SchedulingError != null)
        {
            // A scheduling error indicates a problem starting the task on the node.
            // It is important to note that the task's state can be "Completed," yet
            // still have encountered a scheduling error.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}",
                task.Id,
                task.ExecutionInformation.SchedulingError.Message);
        }
        else if (task.ExecutionInformation.ExitCode != 0)
        {
            // A non-zero exit code may indicate that the application executed by
            // the task encountered an error during execution. As not every
            // application returns non-zero on failure by default (e.g. robocopy),
            // your implementation of error checking may differ from this example.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Paso 7: Descargar el resultado de la tarea

![Descargar los resultados de la tarea de almacenamiento][7]<br/>

Ahora que se haya completado el trabajo, el resultado de las tareas puede descargarse desde el almacenamiento de Azure. Esto se hace con una llamada a `DownloadBlobsFromContainerAsync` en *DotNetTutorial* `Program.cs`:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] La llamada a `DownloadBlobsFromContainerAsync` en la *DotNetTutorial* aplicación especifica que se deben descargar los archivos a su `%TEMP%` carpeta. No dude en modificar esta ubicación de salida.

## <a name="step-8-delete-containers"></a>Paso 8: Contenedores de eliminar

Dado que se cargan de datos que residen en el almacenamiento de Azure, siempre es una buena idea para quitar cualquier BLOB que ya no es necesarias para los procesos. En de DotNetTutorial `Program.cs`, esto se hace con tres llamadas al método auxiliar `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

El mismo método simplemente obtiene una referencia al contenedor y, a continuación, llama [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Paso 9: Eliminar el trabajo y la agrupación

En el paso final, el usuario se pregunte si desea eliminar el trabajo y el grupo creados por la aplicación DotNetTutorial. Aunque no se cargan para trabajos y tareas, *son* de nodos de cálculo. Por lo tanto, se recomienda asignar nodos sólo según sea necesario. Eliminar grupos sin usar puede ser parte del proceso de mantenimiento.

De BatchClient [JobOperations] [ net_joboperations] y [PoolOperations] [ net_pooloperations] tienen métodos de eliminación correspondientes, que se denominan si el usuario confirma la eliminación:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] Tenga en cuenta que se cargará para recursos de cálculo: eliminar grupos sin usar minimizará costo. Además, tenga en cuenta que la eliminación de un grupo elimina todos los nodos de cálculo dentro de ese grupo, y que todos los datos en los nodos será no recuperables después de elimina el grupo.

## <a name="run-the-dotnettutorial-sample"></a>Ejecutar el ejemplo *DotNetTutorial*

Cuando se ejecuta la aplicación de ejemplo, el resultado de la consola será similar al siguiente. Durante la ejecución, experimentará una pausa en `Awaiting task completion, timeout in 00:30:00...` mientras se inician los nodos de cálculo del grupo. Usar el [portal de Azure] [ azure_portal] para supervisar la agrupación, calcular nodos, trabajo y tareas durante y después de la ejecución. Usar el [portal de Azure] [ azure_portal] o el [Explorador de almacenamiento de Azure] [ storage_explorers] para ver los recursos de almacenamiento (contenedores y BLOB) creados por la aplicación.

Tiempo de ejecución típica es **aproximadamente 5 minutos** cuando ejecute la aplicación en su configuración predeterminada.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Pasos siguientes

No dude en realizar cambios en *DotNetTutorial* y *TaskApplication* a experimentar con escenarios de cálculo diferentes. Por ejemplo, intente agregar un retraso de ejecución a *TaskApplication*, como con [Thread.Sleep][net_thread_sleep], para simular las tareas de ejecución larga y supervisión en el portal. Intente agregar más tareas o ajustar el número de nodos de cálculo. Agregar lógica para buscar y permitir el uso de un grupo existente para acelerar el tiempo de ejecución (*Sugerencia*: consulte `ArticleHelpers.cs` en la [Microsoft.Azure.Batch.Samples.Common] [ github_samples_common] proyecto en [ejemplos del lote de azure][github_samples]).

Ahora que ya está familiarizado con el flujo de trabajo básica de una solución por lotes, es el momento para profundizar las características adicionales del servicio de lote.

- Lea la [información general de la característica de lote para desarrolladores](batch-api-basics.md), que se recomienda para todos los nuevos usuarios por lotes.
- Iniciar en los otros artículos de desarrollo de lote en **desarrollo en profundidad** en la [ruta de aprendizaje de lote][batch_learning_path].
- Desproteger una implementación diferente de procesamiento de la carga de trabajo de "palabras top N" mediante el lote de la [TopNWords] [ github_topnwords] muestra.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Crear contenedores de almacenamiento de Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Cargar archivos de entrada (datos) y aplicación de la tarea en contenedores"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Crear grupo por lotes"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Crear tarea por lotes"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Agregar tareas a la tarea"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Supervisar tareas"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Descargar los resultados de la tarea de almacenamiento"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Flujo de trabajo de solución de lote (diagrama completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenciales de proceso por lotes de Portal"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenciales de almacenamiento de Portal"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Flujo de trabajo de solución de lote (diagrama mínima)"
