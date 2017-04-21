<properties
    pageTitle="Usar las actividades personalizadas de una canalización de generador de datos de Azure"
    description="Aprenda a crear actividades personalizadas y usarlos en una canalización del generador de datos de Azure."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="spelluru"/>

# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Usar las actividades personalizadas de una canalización de generador de datos de Azure
> [AZURE.SELECTOR]
[Sección](data-factory-hive-activity.md)  
[Cerdo](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmisión de Hadoop](data-factory-hadoop-streaming-activity.md)
[El aprendizaje](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimiento almacenado](data-factory-stored-proc-activity.md)
[Datos lago análisis U-SQL](data-factory-usql-activity.md)
[.NET personalizada](data-factory-use-custom-activities.md)

Existen dos tipos de actividades que puede usar en una canalización del generador de datos de Azure.
 
- [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para mover datos entre [compatibles almacena datos](data-factory-data-movement-activities.md#supported-data-stores).
- [Las actividades de transformación de datos](data-factory-data-transformation-activities.md) y el proceso de transformación de datos con se calcula como HDInsight de Azure, lote de Azure y Azure el aprendizaje. Por ejemplo: HDInsight subárbol y ejecución de lotes de aprendizaje de equipo.  

Si necesita mover los datos de un almacén de datos que no es compatible con el generador de datos de Azure, puede crear una actividad de .NET personalizada con su propia lógica de movimiento de datos y usar la actividad de la canalización. 

Del mismo modo, si necesita proceso o transformar datos de forma que no es compatible con el generador de datos, puede crear una actividad personalizada con su propia lógica de procesamiento de datos y usar la actividad de la canalización.

> [AZURE.NOTE] Actualmente, Data Management Gateway admite solo la copia actividad y procedimiento almacenado en el generador de datos. No es posible usar la puerta de enlace de una actividad personalizada para tener acceso a orígenes de datos locales.
 
Puede configurar la actividad de .NET personalizada para ejecutar con un servicio de **Azure lote** o en un clúster de **HDInsight de Azure** .   

El siguiente tutorial proporciona instrucciones paso a paso para crear una actividad de .NET personalizada y utilizar la actividad personalizada en una canalización. El tutorial utiliza el servicio de **Azure lote** vinculado. Usar el HDInsight de Azure vinculado servicio en su lugar, se crea un servicio vinculado de tipo **HDInsight** (si está utilizando su propio clúster HDInsight) o **HDInsightOnDemand** (si desea que el generador de datos para crear un clúster HDInsight a petición) y usar en la sección de la actividad de la canalización de JSON (**linkedServiceName**). Vea la sección [uso Azure HDInsight vinculado servicios](#use-azure-hdinsight-linked-services) para obtener más información sobre el uso de HDInsight de Azure para ejecutar la actividad personalizada.

> [AZURE.IMPORTANT] Versión del conjunto 4.5.2 de .NET Framework como marco de destino para el proyecto de actividad personalizada .NET en Visual Studio. Generador de datos no admite actividades personalizadas compiladas en las versiones de .NET Framework más adelante 4.5.2.   

## <a name="walkthrough"></a>Tutorial 

### <a name="prerequisites"></a>Requisitos previos

- Visual Studio 2012/2013/2015
- Descargar e instalar el [SDK de .NET de Azure][azure-developer-center]


### <a name="azure-batch-prerequisites"></a>Requisitos previos de lote Azure
En el tutorial, ejecute sus actividades de .NET personalizadas con Azure lote como un recurso de cálculo. Vea los [conceptos básicos de Azure lote] [ batch-technical-overview] para obtener información general del lote de Azure de servicio y consulte [Introducción a la biblioteca por lotes de Azure para .NET] [ batch-get-started] para comenzar rápidamente con el servicio de proceso de Azure.

Para el tutorial, debe crear una cuenta de Azure lote con un grupo de máquinas virtuales. Estos son los pasos:

1. Cree una **cuenta de Azure lote** con el [portal de Azure](http://manage.windowsazure.com). Consulte [crear y administrar una cuenta de Azure lote] [ batch-create-account] artículo para obtener instrucciones. Anote la clave de nombre y la cuenta de la cuenta del lote de Azure.

    También puede usar el [Nuevo AzureBatchAccount] [ new-azure-batch-account] cmdlet para crear una cuenta de Azure lote. Vea [con Azure administrar cuenta de Azure lote] [ azure-batch-blog] para obtener instrucciones detalladas sobre cómo usar este cmdlet.
2. Cree un **grupo de lotes de Azure**.
    1. En el [portal de Azure](https://portal.azure.com), haga clic en **Examinar** en el menú de la izquierda y haga clic en **Cuentas de lote**. 
    2. Seleccione la cuenta de Azure lote para abrir el módulo del **Lote de cuenta** . 
    3. Haga clic en **grupos de** mosaico.
    4. En el módulo de **grupos** , haga clic en el botón Agregar en la barra de herramientas para agregar un grupo.
        1. Introduzca un ID para el grupo (**Id. de grupo**). Nota el **identificador del grupo**; se necesita al crear la solución del generador de datos. 
        2. Especifique la configuración de la familia de sistemas operativos **Windows Server 2012 R2** .
        3. Seleccione un **nivel de precios de nodo**. 
        3. Escriba **2** como valor para la configuración **Dedicada de destino** .
        4. Escriba **2** como valor para la configuración de **las tareas de Max por nodo** .
    5. Haga clic en **Aceptar** para crear el grupo. 
 
    También puede usar el cmdlet [AzureBatchPool de nuevo](https://msdn.microsoft.com/library/mt628690.aspx) para crear un grupo por lotes de Azure.    

### <a name="high-level-steps"></a>Pasos de alto nivel 
1.  **Crear una actividad personalizada** para usar una canalización del generador de datos. La actividad personalizada en este ejemplo contiene la lógica de transformación o procesamiento de datos. 
    1.  En Visual Studio, cree un proyecto de biblioteca de clases .NET, agregue el código para procesar datos de entrada y compilar el proyecto. 
    2.  ZIP de todos los archivos binarios y el archivo PDB (opcional) en la carpeta de salida.  
    3.  Cargue el archivo zip con el almacenamiento de blobs de Windows Azure. Los pasos detallados se encuentran en la crear la sección de actividad personalizada. 
2. **Crear un generador de datos de Azure que usa la actividad personalizada**:
    1. Crear un generador de datos de Azure.
    2. Crear servicios vinculados.
        1. AzureStorageLinkedService: Proporciona las credenciales de almacenamiento para acceder BLOB.
        2. AzureBatchLinkedService: especifica Azure lote como calcular.
    3. Crear conjuntos de datos.
        1. InputDataset: especifica el contenedor de almacenamiento y una carpeta para la entrada blobs.
        1. OuputDataset: especifica el contenedor de almacenamiento y una carpeta para blobs de salida.
    2. Crear una canalización que usa la actividad personalizada.
    3. Ejecutar y probar la canalización.
    4. La canalización de depuración.

## <a name="create-the-custom-activity"></a>Crear la actividad personalizada
Para crear una actividad de .NET personalizada, cree un proyecto de **Biblioteca de clases .NET** con una clase que implementa esa interfaz **IDotNetActivity** . Esta interfaz tiene un solo método: [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) y su firma es:

    public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        

El método toma cuatro parámetros:

- **linkedServices**. Esta propiedad es una lista de servicios vinculados que vincular orígenes de datos de entrada y salida enumerable (por ejemplo: el almacenamiento de blobs de Windows Azure) para el generador de datos. En este ejemplo, hay un único servicio vinculado del tipo de almacenamiento de Azure utilizado para la entrada y salida. 
- **conjuntos de datos**. Esta propiedad es una lista enumerable de conjuntos de datos. Puede usar este parámetro para obtener las ubicaciones y esquemas definidos por los conjuntos de datos de entrada y salida.
- **actividad**. Esta propiedad representa el cálculo entidad actual - en este caso, un lote de Azure.
- **registrador**. Este objeto permite escribir comentarios de depuración dicha superficie como el registro de "Usuario" de la canalización. 

El método devuelve un diccionario que se pueden usar para encadenar actividades personalizadas en el futuro. Esta característica no se ha implementado todavía, así que devuelva un diccionario vacío desde el método.  

### <a name="procedure"></a>Procedimiento 
1.  Crear un proyecto de **Biblioteca de clases .NET** .
    <ol type="a">
        <li>Inicie <b>Visual Studio de 2015</b> o <b>2013 de Visual Studio</b> o <b>Visual Studio 2012</b>.</li>
        <li>Haga clic en <b>archivo</b>, elija <b>nuevo</b>y haga clic en <b>proyecto</b>.</li>
        <li>Expanda <b>plantillas</b>y a continuación, seleccione <b>Visual C#</b>. En este tutorial, utilice C#, pero puede utilizar cualquier lenguaje .NET para desarrollar la actividad personalizada.</li>
        <li>Seleccione la <b>Biblioteca de clases</b> de la lista de tipos de proyecto a la derecha.</li>
        <li>Escriba <b>MyDotNetActivity</b> para el <b>nombre</b>.</li>
        <li>Haga clic en la <b>ubicación</b> <b>C:\ADFGetStarted</b> .</li>
        <li>Haga clic en <b>Aceptar</b> para crear el proyecto.</li>
    </ol>
2.  Haga clic en **Herramientas**, elija **Administrador de paquetes de NuGet**y haga clic en la **Consola del Administrador de paquetes**.
3.  En la consola del Administrador de paquetes, ejecute el comando siguiente para importar **Microsoft.Azure.Management.DataFactories**.

        Install-Package Microsoft.Azure.Management.DataFactories

4. Importar el paquete de NuGet **Almacenamiento de Azure** en el proyecto.

        Install-Package Azure.Storage

5. Agregue las siguientes instrucciones **using** al archivo de origen en el proyecto.

        using System.IO;
        using System.Globalization;
        using System.Diagnostics;
        using System.Linq;

        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Runtime;

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

6. Cambiar el nombre del espacio de **nombres** para **MyDotNetActivityNS**.

        namespace MyDotNetActivityNS

7. Cambiar el nombre de la clase a **MyDotNetActivity** y derivan de la interfaz de **IDotNetActivity** como se muestra en el fragmento de código siguiente:

        public class MyDotNetActivity : IDotNetActivity

8. Implementar (Agregar) la **ejecución** del método de la interfaz de **IDotNetActivity** para la clase **MyDotNetActivity** y copie el siguiente código de ejemplo para el método.

    En el ejemplo siguiente cuenta el número de apariciones del término de búsqueda ("Microsoft") en cada blobs asociados con un sector de datos. 

        /// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement. 
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
        /// </summary>

        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {
            // to get extended properties (for example: SliceStart)
            DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
            string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

            // to log all extended properties           
            IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
            logger.Write("Logging extended properties if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }
        
            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same 
            // Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; 

            // To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);
                
                // Calculate method returns the number of occurrences of 
                // the search term (“Microsoft”) in each blob associated
                // with the data slice. definition of the method is shown in the next step.
 
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

            // The dictionary can be used to chain custom activities together in the future.
            // This feature is not implemented yet, so just return an empty dictionary.  

            return new Dictionary<string, string>();
        }

9. Agregue los siguientes métodos auxiliares. El método **Execute** invoca estos métodos auxiliares. El método **GetConnectionString** recupera la cadena de conexión de almacenamiento de Azure y el método **GetFolderPath** la ubicación de blobs de Windows. Lo más importante, el método **Calculate** aísla el código que recorre cada blob.

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.
        /// </summary>

        private static string GetFolderPath(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }

        /// <summary>
        /// Gets the fileName value from the input/output dataset.   
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file, 
        /// and prepares the output text that is written to the output blob. 
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }

    El método GetFolderPath devuelve la ruta de acceso a la carpeta que el conjunto de datos de destino y el método GetFileName devuelve el nombre del blob o archivo que el conjunto de datos de destino. Si havefolderPath se define mediante variables como {año}, {mes} devuelve {Day}, etc., el método de la cadena tal y como es sin reemplazándolos con valores de tiempo de ejecución. Para obtener más información sobre cómo acceder a SliceStart, SliceEnd, etcetera, vea la sección [acceso propiedades extendidas](#access-extended-properties) .    
    
            "name": "InputDataset",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService",
                "typeProperties": {
                    "fileName": "file.txt",
                    "folderPath": "adftutorial/inputfolder/",
    
    El método Calculate calcula el número de instancias de palabra clave de Microsoft en los archivos de entrada (BLOB en la carpeta). El término de búsqueda ("Microsoft") está codificado en el código.

10. Compilar el proyecto. Haga clic en **Generar** en el menú y haga clic en **Generar solución**.

    > [AZURE.IMPORTANT] Versión del conjunto 4.5.2 de .NET Framework como marco de destino para el proyecto: haga clic en el proyecto y a continuación, haga clic en **Propiedades** para establecer el marco de destino. Generador de datos no admite actividades personalizadas compiladas en las versiones de .NET Framework más adelante 4.5.2. 
11. Inicie **Windows Explorer**y vaya a la carpeta **bin\debug** o **bin\release** según el tipo de compilación.
12. Crear un archivo zip **MyDotNetActivity.zip** que contiene los archivos binarios en el <project folder>carpeta \bin\Debug. Desea incluir el archivo **MyDotNetActivity.pdb** para que obtenga detalles adicionales, como el número de línea en el código fuente que causaron el problema si se ha producido un error. Todos los archivos en el archivo zip de la actividad personalizada deben ser en el **nivel superior** con sin subcarpetas.

    ![Archivos de salida binaria](./media/data-factory-use-custom-activities/Binaries.png)
13. Cargar **MyDotNetActivity.zip** como un blob al contenedor blobs: usa **customactivitycontainer** en el almacenamiento de blobs de Windows Azure que el **AzureStorageLinkedService** vinculado servicio en la **ADFTutorialDataFactory** .  Crear el contenedor de blob **customactivitycontainer** si aún no existe.

> [AZURE.NOTE] Si agrega este proyecto de la actividad de .NET a una solución en Visual Studio que contiene un proyecto de fábrica de datos y agregue una referencia al proyecto de la actividad de .NET desde el proyecto de aplicación de la fábrica de datos, no es necesario llevar a cabo los dos últimos pasos de crear el archivo zip y cargarlo en el almacenamiento de blobs de Windows Azure manualmente. Cuando se publicación entidades de datos fábrica mediante Visual Studio, estos pasos se realizan automáticamente por el proceso de publicación. Consulte [crear su primera canalización mediante Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) y [copie los datos de blobs de Windows Azure a SQL Azure](data-factory-copy-activity-tutorial-using-visual-studio.md) artículos para obtener información sobre crear y publicar entidades de datos fábrica mediante Visual Studio.  

### <a name="execute-method"></a>Ejecutar método

Esta sección proporciona más detalles y notas acerca del código en el método **Execute** .
 
1. Los miembros para recorrer la colección de entrada se encuentran en el espacio de nombres [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) . Recorrer la colección de blobs requiere el uso de la clase **BlobContinuationToken** . Básicamente, debe usar un realice-bucle con el token como mecanismo para salir del bucle while. Para obtener más información, vea [cómo usar el almacenamiento de blobs de .NET](../storage/storage-dotnet-how-to-use-blobs.md). A continuación se muestra un bucle básico:

        // Initialize the continuation token.
        BlobContinuationToken continuationToken = null;
        do
        {   
            // Get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                      true,
                                      BlobListingDetails.Metadata,
                                      null,
                                      continuationToken,
                                      null,
                                      null);
            // Return a string derived from parsing each blob.
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
        } while (continuationToken != null);

    Consulte la documentación del método [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) para obtener más detalles.

2.  El código para trabajar en el conjunto de BLOB lógicamente va no-bucle while. En el método **Execute** , no-mientras bucle pasa a la lista de BLOB a un método denominado **calcular**. El método devuelve una variable de cadena denominada **salida** que es el resultado de tener una iteración todos los BLOB en el segmento. 

    Devuelve el número de apariciones del término de búsqueda (**Microsoft**) en el blob pasado al método **Calculate** . 

            output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.  Una vez que el método **Calculate** ha finalizado el trabajo, se debe escribir a un nuevo blob. Por lo que para cada conjunto de BLOB procesado, se puede escribir un nuevo blob con los resultados. Para escribir en un nuevo blob, busque primero el conjunto de datos de salida. 

            // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

            // Convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.  El código también llama a un método auxiliar: **GetFolderPath** para recuperar la ruta de acceso a la carpeta (el nombre del contenedor de almacenamiento).
 
            folderPath = GetFolderPath(outputDataset);

    La **GetFolderPath** convierte el objeto de conjunto de datos a un AzureBlobDataSet, que tiene una propiedad denominada FolderPath.
            
            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            
            return blobDataset.FolderPath;

5.  El código llama al método de **GetFileName** para recuperar el nombre de archivo (blobs nombre).  

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

            return blobDataset.FileName;

6.  El nombre del archivo se escribe mediante la creación de un objeto URI. El constructor URI usa la propiedad **BlobEndpoint** para devolver el nombre del contenedor. La ruta de acceso y nombre de la carpeta se agregarán a construir la URI de blobs de salida.  

            // Write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.  Se ha escrito el nombre del archivo y ahora puede escribir la cadena de salida desde el método de cálculo a un nuevo blob:

            // Create a blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

## <a name="create-the-data-factory-using-azure-portal"></a>Crear el generador de datos con el portal de Azure

En la sección **crear la actividad personalizada** , crear una actividad personalizada y cargar el archivo zip con archivos binarios y el archivo PDB en un contenedor de blobs de Windows Azure. En esta sección, creará un **Generador de datos** de Azure con una **canalización** que utiliza la **actividad personalizada**.
 
El conjunto de datos de entrada de la actividad personalizada representa el BLOB (archivos) en la carpeta de entrada (adftutorial\inputfolder) en el almacenamiento de blobs. El conjunto de datos de salida de la actividad representa el BLOB de salida en la carpeta de salida (adftutorial\outputfolder) en el almacenamiento de blobs. 

Crear un archivo llamado **archivo.txt** con el siguiente contenido y cárguelo en **adftutorial\inputfolder** (adftutorial es el nombre del contenedor de blobs de Windows Azure y inputfolder es el nombre de la carpeta de ese contenedor.)

    test custom activity Microsoft test custom activity Microsoft

La carpeta de entrada corresponde a un sector en el generador de datos de Azure, incluso si la carpeta tiene dos o más archivos. Cuando la canalización procesa cada sector, la actividad personalizada recorre todos los BLOB en la carpeta de entrada para ese sector. 

Puede ver un archivo con en la carpeta adftutorial\output de salida con uno o más líneas (igual que el número de BLOB en la carpeta de entrada):
 
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


Estos son los pasos que realice en esta sección:

1. Crear un **Generador de datos**.
2. **Servicios de vinculado** para el grupo de Azure lote de máquinas virtuales en que se ejecuta la actividad personalizada y el almacenamiento de Azure que contiene el BLOB de entrada y salida. 
2. Entrada y salida de **conjuntos de datos** que representa la entrada y salida de la actividad personalizada. 
3. **Canalización** que utiliza la actividad personalizada.
4. **Generador de datos**. Crear uno al publicar estas entidades en Azure. 

> [AZURE.NOTE] Crear el **archivo.txt** y cárguelo en un contenedor de blob si aún no lo ha hecho. Vea las instrucciones anteriores.  

### <a name="step-1-create-the-data-factory"></a>Paso 1: Crear el generador de datos

1.  Después de iniciar sesión el portal de Azure, siga los pasos siguientes:
    1.  En el menú de la izquierda, haga clic en **nuevo** .
    2.  Haga clic en **datos + análisis** en el módulo de **nuevo** .
    3.  Haga clic en **Generador de datos** en el módulo de **análisis de datos** .
2.  En el módulo de **Generador de datos nueva** , escriba **CustomActivityFactory** para el nombre. El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **el nombre del generador de datos "CustomActivityFactory" no está disponible**, cambiar el nombre de la fábrica de datos (por ejemplo, **yournameCustomActivityFactory**) y pruebe a crear de nuevo.
3.  Haga clic en **El nombre del grupo de recursos**y seleccione un grupo de recursos existente o crear un grupo de recursos. 
4.  Compruebe que está utilizando la correcta de **suscripción** y de **región** donde desea que el generador de datos que se cree. 
5.  En el módulo de **nuevo el generador de datos** , haga clic en **crear** .
6.  Consulte el generador de datos que se creó en el **panel** del portal de Azure.
7.  Después de que el generador de datos se ha creado correctamente, verá el módulo de generador de datos, que muestra el contenido del generador de datos.

### <a name="step-2-create-linked-services"></a>Paso 2: Crear servicios vinculados

Servicios vinculados vincular almacena datos o calculan servicios a un generador de datos de Azure. En este paso, vincular su cuenta de almacenamiento de Azure y cuenta de Azure lote a su generador de datos.

#### <a name="create-azure-storage-linked-service"></a>Crear el servicio de almacenamiento de Azure vinculado

1.  Haga clic en el **autor e implementar** mosaico en el módulo de **Generador de datos** para **CustomActivityFactory**. Vea el Editor del generador de datos.
2.  Haga clic en **almacenan nuevos datos** en la barra de comandos y elija el **almacenamiento de Azure**. Debe ver la secuencia de comandos JSON para crear un servicio de almacenamiento de Azure vinculado en el editor.
3.  Reemplace el **nombre de la cuenta** con el nombre de su cuenta de almacenamiento de Azure y la **clave de cuenta** con la tecla de acceso de la cuenta de almacenamiento de Azure. Para obtener información sobre cómo obtener la clave de acceso de almacenamiento, vea [Ver, copiar y las teclas de acceso de almacenamiento regenerar](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
4.  En la barra de comandos para implementar el servicio vinculado, haga clic en **implementar** .

#### <a name="create-azure-batch-linked-service"></a>Crear el lote de Azure vinculado servicio

2. En el Editor del generador de datos, haga clic en **calcular de nuevo** de la barra de comandos y seleccione el **Lote de Azure** en el menú.
3. Realice los cambios siguientes en la secuencia de comandos JSON:
    1. Especifique el nombre de la cuenta de Azure lote de la propiedad del **nombre de la cuenta** . La **dirección URL** desde el **módulo de la cuenta de Azure lote** está en el siguiente formato: http://**nombre de la cuenta**. region.batch.azure.com. Para la propiedad **batchUri** en el JSON, debe **Quitar "nombre de la cuenta."** desde la dirección URL y use el **nombre de la cuenta** para la propiedad JSON de **nombre de la cuenta** .
    2. Especificar la clave de cuenta de Azure lote de la propiedad **accessKey** . 
    3. Especifique el nombre del grupo que creado como parte de los requisitos previos para la propiedad **poolName** . También puede especificar el identificador del grupo de servidores en lugar del nombre del grupo.
    4. Especificar URI del lote de Azure para la propiedad **batchUri** . Consulte las notas por encima de la propiedad del **nombre de la cuenta** . Ejemplo: https://westus.batch.azure.com.  
    5. Especifique la **AzureStorageLinkedService** para la propiedad **linkedServiceName** .
        
            {
              "name": "AzureBatchLinkedService",
              "properties": {
                "type": "AzureBatch",
                "typeProperties": {
                  "accountName": "myazurebatchaccount",
                  "batchUri": "https://westus.batch.azure.com",
                  "accessKey": "<yourbatchaccountkey>",
                  "poolName": "myazurebatchpool",
                  "linkedServiceName": "AzureStorageLinkedService"
                }
              }
            }

        Para la propiedad **poolName** , también puede especificar el identificador del grupo de servidores en lugar del nombre del grupo.

    > [AZURE.NOTE] El servicio de generador de datos no admite una opción a petición para Azure lote igual HDInsight. Solo puede usar su propio grupo por lotes de Azure en un generador de datos de Azure.
    
### <a name="step-3-create-datasets"></a>Paso 3: Crear conjuntos de datos
En este paso, creará los conjuntos de datos para representar datos de entrada y salidos.

#### <a name="create-input-dataset"></a>Crear conjunto de datos de entrada
1.  En el **Editor** de la fábrica de datos, haga clic en el botón **nuevo conjunto de datos** en la barra de herramientas y haga clic en **almacenamiento de blobs de Windows Azure** en el menú desplegable.
2.  Reemplazar el JSON en el panel derecho con el fragmento JSON siguiente:

            {
                "name": "InputDataset",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "AzureStorageLinkedService",
                    "typeProperties": {
                        "folderPath": "adftutorial/customactivityinput/",
                        "format": {
                            "type": "TextFormat"
                        }
                    },
                    "availability": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            }

    Crear una canalización más adelante en este tutorial con hora de inicio: 2015-11-hora 16T00:00:00Z y finalización: 2015-11-16T05:00:00Z. Se ha programado para generar datos cada hora, por lo que hay sectores de entrada y salida 5 (entre **00**: 00:00 -> **05**: 00:00). 

    La **frecuencia** y el **intervalo** para el conjunto de datos de entrada se establece en **1**, lo que significa que la segmentación de datos de entrada está disponible cada hora y la **hora** . En este ejemplo, es el mismo archivo (file.txt) en la intputfolder. 

    Estas son las horas de inicio para cada sector, representada por la variable del sistema SliceStart en el fragmento de código anterior JSON. 

    
3.  En la barra de herramientas para crear e implementar la **InputDataset**, haga clic en **implementar** . Confirme que ve el mensaje **Tabla creado correctamente** en la barra de título del Editor.


#### <a name="create-an-output-dataset"></a>Crear un conjunto de datos de salida

1. En el **editor del generador de datos**, haga clic en **nuevo conjunto de datos**y, a continuación, haga clic en **almacenamiento de blobs de Windows Azure** desde la barra de comandos.
2. Reemplazar la secuencia de comandos JSON en el panel derecho con el siguiente script JSON:

        {
            "name": "OutputDataset",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService",
                "typeProperties": {
                    "fileName": "{slice}.txt",
                    "folderPath": "adftutorial/customactivityoutput/",
                    "partitionedBy": [
                        {
                            "name": "slice",
                            "value": {
                                "type": "DateTime",
                                "date": "SliceStart",
                                "format": "yyyy-MM-dd-HH"
                            }
                        }
                    ]
                },
                "availability": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        }

    Ubicación de salida es **adftutorial/customactivityoutput/** y nombre de archivo de salida es aaaa-MM-dd-HH.txt donde yyyy-MM-dd-HH es el año, mes, fecha y hora de la segmentación de datos que se produce. Vea la [Referencia del programador] [ adf-developer-reference] para obtener más detalles.

    Se genera un archivo o blobs de resultados para cada sector de entrada. Le mostramos cómo se da el nombre de un archivo de salida para cada sector. Todos los archivos de salida se generan en una carpeta de salida: **adftutorial\customactivityoutput**.

  	| Segmentación de datos | Hora de inicio | Archivo de salida |
  	| :---- | :--------- | :---------- | 
  	| 1 | 2015-11-16T00:00:00 | 2015-11-16-00.txt |
  	| 2 | 2015-11-16T01:00:00 | 2015-11-16-01.txt |
  	| 3 | 2015-11-16T02:00:00 | 2015-11-16-02.txt |
  	| 4 | 2015-11-16T03:00:00 | 2015-11-16-03.txt |
  	| 5 | 2015-11-16T04:00:00 | 2015-11-16-04.txt |

    Recuerde que todos los archivos en una carpeta de entrada forman parte de un sector con las horas de inicio mencionados arriba. Cuando se procesa este sector, la actividad personalizada recorre cada archivo y genera una línea en el archivo de salida con el número de apariciones del término de búsqueda ("Microsoft"). Si hay tres archivos en la inputfolder, hay tres líneas en el archivo de salida para cada sector por hora: 2015-11-16-00.txt 2015-11-16:01:00:00.txt etcetera. 


2. En la barra de comandos para implementar el **OutputDataset**, haga clic en **implementar** .


### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Crear y ejecutar una canalización que utiliza la actividad personalizada

1. En el Editor del generador de datos, haga clic en la **canalización de nuevo** en la barra de comandos. Si no ve el comando, haga clic en **... (Puntos suspensivos)** para verlo.
2. Reemplazar el JSON en el panel derecho con el siguiente script JSON. 

        {
          "name": "ADFTutorialPipelineCustom",
          "properties": {
            "description": "Use custom activity",
            "activities": [
              {
                "Name": "MyDotNetActivity",
                "Type": "DotNetActivity",
                "Inputs": [
                  {
                    "Name": "InputDataset"
                  }
                ],
                "Outputs": [
                  {
                    "Name": "OutputDataset"
                  }
                ],
                "LinkedServiceName": "AzureBatchLinkedService",
                "typeProperties": {
                  "AssemblyName": "MyDotNetActivity.dll",
                  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                  "PackageLinkedService": "AzureStorageLinkedService",
                  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
                  "extendedProperties": {
                    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
                  }
                },
                "Policy": {
                  "Concurrency": 2,
                  "ExecutionPriorityOrder": "OldestFirst",
                  "Retry": 3,
                  "Timeout": "00:30:00",
                  "Delay": "00:00:00"
                }
              }
            ],
            "start": "2015-11-16T00:00:00Z",
            "end": "2015-11-16T05:00:00Z",
            "isPaused": false
          }
        }

    Tenga en cuenta los siguientes puntos:

    - **Simultaneidad** se establece en **2** para que dos sectores se procesan en paralelo por 2 máquinas virtuales en el grupo por lotes de Azure.
    - Hay una actividad en la sección actividades y es de tipo: **DotNetActivity**.
    - **AssemblyName** se establece en el nombre del archivo DLL: **MyDotnetActivity.dll**.
    - **Punto de entrada** se establece en **MyDotNetActivityNS.MyDotNetActivity**.
    - **PackageLinkedService** se establece en **AzureStorageLinkedService** que apunta al almacenamiento de blobs de Windows que contiene el archivo zip de actividad personalizada. Si está utilizando cuentas de Azure almacenamiento diferentes para los archivos de entrada y salida y el archivo zip de actividad personalizada, cree otro servicio de almacenamiento de Azure vinculado. En este artículo se supone que está usando la misma cuenta de almacenamiento de Azure.
    - **PackageFile** se establece en **customactivitycontainer/MyDotNetActivity.zip**. Está en el formato: containerforthezip/nameofthezip.zip.
    - La actividad personalizada toma **InputDataset** como entrada y **OutputDataset** como resultado.
    - La propiedad linkedServiceName de la actividad personalizada de destino **AzureBatchLinkedService**, que indica el generador de datos de Azure que necesita ejecutarse en máquinas virtuales de Azure lote de la actividad personalizada.
    - propiedad de **isPaused** está establecida en **false** de forma predeterminada. La canalización se inicia inmediatamente en este ejemplo porque los sectores comenzar en el pasado. Puede establecer esta propiedad en true para pausar la canalización y establezca falso para reiniciar. 
    - La hora de **comienzo** y **finalización** **cinco** horas separadas sectores producidos a cada hora, para que cinco sectores producidos por la canalización. 

4. En la barra de comandos para implementar la canalización, haga clic en **implementar** .

### <a name="monitor-the-pipeline"></a>Supervisar la canalización
 
8. En el módulo de generador de datos en el portal de Azure, haga clic en **diagrama**.
    
    ![Mosaico de diagrama](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
 
9. En la vista de diagrama, haga clic en el OutputDataset.
 
    ![Vista de diagrama](./media/data-factory-use-custom-activities/diagram.png)

10. Verá que los sectores de cinco salida están en el estado listo si ya se han producido.

    ![Sectores de salida](./media/data-factory-use-custom-activities/OutputSlices.png)
    
12. Compruebe que los archivos de salida se generan en el almacenamiento de blobs de Windows en el contenedor **adftutorial** .

    ![resultado de actividad personalizada][image-data-factory-ouput-from-custom-activity]

9. Si abre el archivo de salida, verá el resultado similar al siguiente resultado:

    se encontraron 2 repeticiones (s) del término de búsqueda "Microsoft" en el archivo inputfolder/2015-11-16-00/file.txt.

10. Usar el [portal de Azure] [ azure-preview-portal] o cmdlets de PowerShell de Azure para supervisar el generador de datos, canalizaciones y conjuntos de datos. Puede ver mensajes de la **ActivityLogger** en el código de la actividad en los registros (específicamente 0.log usuario) que puede descargarse desde el portal o el uso de cmdlets personalizado.

    ![descargar los registros de actividad personalizada][image-data-factory-download-logs-from-custom-activity]


Vea [supervisar y administrar canalizaciones](data-factory-monitor-manage-pipelines.md) para conocer los pasos detallados para la supervisión de conjuntos de datos y canalizaciones.      

### <a name="data-factory-and-batch-integration"></a>Integración de fábrica de datos y por lotes
Servicio del generador de datos crea un trabajo en proceso de Azure con el nombre: **adf-poolname:job-largo y LARGOB**. 

![Generador de datos de Azure - trabajos por lotes](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Se crea una tarea para cada ejecución de la actividad de un sector. Si hay 10 sectores listos para procesarse, 10 tareas se crean en esta tarea. Puede tener más de una división que se ejecuta en paralelo, si tiene varios nodos de cálculo en el grupo. También puede tener más de una división que se ejecuta en el mismo cálculo si el número máximo de tareas por nodo de cálculo se establece en > 1. 

![Generador de datos de Azure - tareas por lotes](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

En el diagrama siguiente muestra la relación entre las tareas generador de datos de Azure y lote. 

![Lote y generador de datos](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)


## <a name="debug-the-pipeline"></a>La canalización de depuración
Depuración consta de algunas técnicas básicas:

1.  Si ve el siguiente mensaje de error, compruebe que el nombre de la clase en el archivo CS coincide con el nombre especificado para la propiedad de **punto de entrada** en la canalización de JSON. En el tutorial anterior, es el nombre de la clase: MyDotNetActivity y el punto de entrada en el JSON es: MyDotNetActivityNS. **MyDotNetActivity**. 

            MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly

    Si los nombres coinciden, confirme que todos los archivos binarios están en la **carpeta raíz** del archivo zip. Es decir, cuando abre el archivo zip, debería ver todos los archivos en la carpeta raíz, no en todas las subcarpetas.   
2.  Si no se establece la segmentación de datos de entrada en **Listo**, confirme que la estructura de la carpeta de entrada es correcta y **file.txt** existe en las carpetas de entrada. 
2.  En el método **Execute** de su actividad personalizada, use el objeto **IActivityLogger** para registrar la información que le ayuda a solucionar problemas. Los mensajes registrados aparecen en los archivos de registro de usuario (uno o más archivos con nombre: 0.log de usuario, el usuario 1.log, 2.log de usuario, etcetera.). 

    En el módulo **OutputDataset** , haga clic en la segmentación de datos para ver el módulo de **Sectores de datos** para ese sector. Vea la **actividad se ejecuta** para ese sector. Debería ver una actividad ejecutar para la segmentación de datos. Si hace clic en ejecutar en la barra de comandos, puede iniciar otra actividad ejecutar para la misma división. 

    Al hacer clic en la ejecución de la actividad, consulte el módulo de **Detalles de ejecución de actividad** con una lista de archivos de registro. Ver mensajes registrados en el archivo user_0.log. Cuando se produce un error, verá tres ejecuciones de actividad porque el número de reintentos está definido como 3 en la canalización o actividad JSON. Al hacer clic en la ejecución de la actividad, verá los archivos de registro que puede consultar para solucionar el error. 

    En la lista de archivos de registro, haga clic en el **usuario 0.log**. En el panel derecho son los resultados de usar el método **IActivityLogger.Write** . Si no puede ver todos los mensajes, compruebe si tiene más archivos de registro con el nombre: user_1.log, user_2.log etcetera. En caso contrario, el código posible error tras el último mensaje de inicio de sesión.

    También debe comprobar **0.log del sistema** para todos los mensajes de error de sistema y excepciones.

3.  Incluya el archivo **PDB** en el archivo zip para que los detalles del error tienen información como la **pila de llamadas** cuando se produce un error.
4.  Todos los archivos en el archivo zip de la actividad personalizada deben ser en el **nivel superior** con sin subcarpetas.
5.  Asegúrese de que el **assemblyName** (MyDotNetActivity.dll), el **punto de entrada**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) y **packageLinkedService** (deben apuntar al almacenamiento de blobs de Windows Azure que contiene el archivo zip) se establecen en valores correctos. 
6.  Si fija un error y desea volver a procesar la segmentación de datos, haga clic en la segmentación de datos en el módulo **OutputDataset** y haga clic en **Ejecutar**. 
7.  La actividad personalizada no utiliza el archivo **app.config** de su paquete, por lo que si su código lee las cadenas de conexión del archivo de configuración, no funciona en tiempo de ejecución. El método recomendado cuando se usa el lote de Azure es mantenga cualquier información confidencial en un **KeyVault de Azure**, utilice una entidad de seguridad de servicio basado en certificado para proteger la **keyvault**y distribuir el certificado de grupo por lotes de Azure. La actividad de .NET personalizada, a continuación, puede acceder a información confidencial de la KeyVault en tiempo de ejecución. Esta solución es una solución genérica y puede escalar a cualquier tipo de secreto, no solo la cadena de conexión.

    Hay una solución más fácil (pero no se recomienda): puede crear un **servicio de vinculado de SQL Azure** con conexión cadena configuración, crear un conjunto de datos que usa el servicio vinculado y encadenar el conjunto de datos como un conjunto de datos ficticia de entrada a la actividad de .NET personalizada. A continuación, puede acceder a cadena de conexión de servicio vinculadas en el código de actividad personalizada y debería funcionar correctamente en tiempo de ejecución.  



## <a name="update-the-custom-activity"></a>Actualizar la actividad personalizada
Si actualiza el código de la actividad personalizada, crear y cargar el archivo zip que contiene los nuevos binarios para el almacenamiento de blobs de Windows.

## <a name="copymove-data"></a>Copiar o mover datos 
Copiar copias de actividad de almacén de datos desde un **origen** de datos en un almacén de datos de **receptor** . Consulte [almacena de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores) para la lista de almacenamiento de datos compatibles como orígenes y receptores de la actividad de copia. 

Si necesita mover los datos de un almacén de datos que no es compatible con la **Actividad de copia**, puede usar la **actividad personalizada** en el generador de datos con su propia lógica para copiar o mover los datos. Vea [ejemplo de aplicación de descarga de datos HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) en GitHub.  

## <a name="appdomain-isolation"></a>Aislamiento de dominio de aplicación 
Vea [Cruzadas de ejemplo de dominio de aplicación](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) que muestra cómo crear una actividad de .NET personalizada para el generador de datos de Azure que no se limita a las versiones de ensamblado utilizadas por el iniciador del generador de datos de Azure (por ejemplo, WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etcetera).

## <a name="access-extended-properties"></a>Acceso a las propiedades extendidas
Puede declarar propiedades extendidas en la actividad JSON tal como se muestra a continuación: 

    "typeProperties": {
      "AssemblyName": "MyDotNetActivity.dll",
      "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
      "PackageLinkedService": "AzureStorageLinkedService",
      "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
      "extendedProperties": {
        "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
        "DataFactoryName": "CustomActivityFactory"
      }
    },

En el código, hay dos propiedades extendidas: **SliceStart** y **DataFactoryName**. El valor de SliceStart se basa en la variable del sistema SliceStart. Para obtener una lista de variables del sistema admitidos, vea [Variables del sistema](data-factory-scheduling-and-execution.md#data-factory-system-variables) . El valor de DataFactoryName es el código para "CustomActivityFactory". 

Para obtener acceso a estas propiedades extendidas en el método **Execute** , use código similar al siguiente código: 

    // to get extended properties (for example: SliceStart)
    DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
    string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

    // to log all extended properties                               
    IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
    logger.Write("Logging extended properties if any...");
    foreach (KeyValuePair<string, string> entry in extendedProperties)
    {
        logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
    }

## <a name="auto-scaling-feature-of-azure-batch"></a>Característica de escala automático del lote de Azure
También puede crear un grupo de Azure lote con la característica de **Autoescala** . Por ejemplo, puede crear un grupo de azure lote con 0 VM dedicadas y una fórmula Autoescala en función del número de tareas pendientes:

Una VM por pendiente de tareas a la vez (por ejemplo: cinco tareas pendientes -> cinco VM):

    pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
    $TargetDedicated = max(pendingTaskSampleVector);

Max de máquina virtual de uno en uno, independientemente del número de tareas pendientes:

    pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
    $TargetDedicated = (max(pendingTaskSampleVector)>0)?1:0;

Para obtener información detallada, vea [automáticamente escala nodos de cálculo en un grupo por lotes de Azure](../batch/batch-automatic-scaling.md) . 

Si el grupo utiliza la [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)de forma predeterminada, el servicio de lote podría tardar 15-30 minutos para preparar la máquina virtual antes de ejecutar la actividad personalizada.  Si usa un autoScaleEvaluationInterval diferentes en el grupo, el servicio de lote podría tardar autoScaleEvaluationInterval + 10 minutos.

## <a name="use-azure-hdinsight-linked-services"></a>Usar los servicios de HDInsight de Azure vinculado
En el tutorial, ha utilizado el cálculo por lotes de Azure para ejecutar la actividad personalizada. También puede usar su propio clúster HDInsight o que el generador de datos cree un clúster de HDInsight a petición y tiene la actividad personalizada que se ejecute en el clúster HDInsight. Estos son los pasos para usar un clúster de HDInsight.  

1. Crear un servicio de HDInsight de Azure vinculado.   
2. Usar el servicio de HDInsight vinculado en lugar de **AzureBatchLinkedService** en la canalización de JSON. 

Desea cambiar las horas de **Inicio** y **final** para la canalización de modo que puede probar el escenario con el servicio de HDInsight de Azure.

#### <a name="create-azure-hdinsight-linked-service"></a>Crear servicio HDInsight de Azure vinculado 
El servicio de generador de datos de Azure es compatible con la creación de un clúster a petición y usarlo para procesar la entrada para generar los datos de salida. También puede usar su propio clúster para realizar la misma. Cuando se utiliza el clúster de HDInsight a petición, se creará un clúster para cada sector. Mientras que, si utiliza su propio clúster HDInsight, el clúster está listo para procesar la segmentación de datos de forma inmediata. Por lo tanto, cuando utiliza clúster a petición, quizás no pueda ver los datos de salida tan pronto como cuando se utiliza su propio clúster.

> [AZURE.NOTE] En tiempo de ejecución una instancia de una actividad de .NET sólo se ejecuta en un nodo de trabajo en el clúster HDInsight; se no puede escalar para ejecutar en varios nodos. Pueden ejecutar varias instancias de la actividad de .NET en paralelo en distintos nodos del clúster HDInsight.

##### <a name="to-use-an-on-demand-hdinsight-cluster"></a>Usar un clúster de HDInsight a petición

1. En el **portal de Azure**, haga clic en **autor e implementación** en la página de inicio del generador de datos.
2. En el Editor del generador de datos, haga clic en **calcular de nuevo** de la barra de comandos y seleccione **clúster HDInsight a petición** en el menú.
2. Realice los cambios siguientes en la secuencia de comandos JSON:
    1. Para la propiedad **clusterSize** , especifique el tamaño del clúster HDInsight.
    3. Para la propiedad de **período de Vida** , especifique cuánto tiempo el cliente puede estar inactivo antes de que se elimine.
    4. Para la propiedad de **versión** , especifique la versión de HDInsight que desea usar. Si excluir esta propiedad, se utiliza la versión más reciente.  
    5. Para el **linkedServiceName**, especifique **AzureStorageLinkedService** que se han creado en el obtener el tutorial de inicio.

            {
                "name": "HDInsightOnDemandLinkedService",
                "properties": {
                    "type": "HDInsightOnDemand",
                    "typeProperties": {
                        "clusterSize": 4,
                        "timeToLive": "00:05:00",
                        "osType": "Windows",
                        "linkedServiceName": "AzureStorageLinkedService",
                    }
                }
            }

2. En la barra de comandos para implementar el servicio vinculado, haga clic en **implementar** .

##### <a name="to-use-your-own-hdinsight-cluster"></a>Para usar su propio clúster HDInsight:

1. En el **portal de Azure**, haga clic en **autor e implementación** en la página de inicio del generador de datos.
2. En el **Editor del generador de datos**, haga clic en **calcular de nuevo** de la barra de comandos y seleccione **clúster HDInsight** desde el menú.
2. Realice los cambios siguientes en la secuencia de comandos JSON:
    1. Para la propiedad **clusterUri** , escriba la dirección URL para su HDInsight. Por ejemplo: https://<clustername>.azurehdinsight.net/     
    2. Para la propiedad del **nombre de usuario** , escriba el nombre de usuario que tiene acceso al clúster HDInsight.
    3. Para la propiedad de **contraseña** , escriba la contraseña para el usuario.
    4. Para la propiedad **LinkedServiceName** , escriba **AzureStorageLinkedService**. En el tutorial de introducción de obtener creó este servicio vinculado.

2. En la barra de comandos para implementar el servicio vinculado, haga clic en **implementar** .

Para obtener más información, vea [calcular servicios vinculados](data-factory-compute-linked-services.md) . 

En la **canalización de JSON**, utilice HDInsight (a petición o su propio) vinculado de servicio: 

    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "HDInsightOnDemandLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2015-11-16T00:00:00Z",
        "end": "2015-11-16T05:00:00Z",
        "isPaused": false
      }
    }

## <a name="examples"></a>Ejemplos

Ejemplo | ¿Qué actividad personalizada realiza 
------ | ----------- 
[Aplicación de descarga de datos HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). | Descarga datos de un extremo HTTP en el almacenamiento de blobs de Azure con actividad personalizada de C# en el generador de datos.
[Ejemplo de análisis de opinión de Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) | Invoca un modelo de Azure m y hacer análisis de opinión, puntuación, predicción etcetera.
[Ejecutar secuencia de comandos de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). | Invoca script R ejecutando RScript.exe en el clúster HDInsight que ya tiene instalado R en él. 
[Dominio de aplicación .NET actividad combinación cruzada](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) | Usa las versiones de ensamblado diferente desde el que utiliza el selector del generador de datos  
 

## <a name="see-also"></a>Vea también

[Actualizaciones de generador de datos de azure: actividades de ejecutar ADF personalizada .NET con Azure lote](https://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
