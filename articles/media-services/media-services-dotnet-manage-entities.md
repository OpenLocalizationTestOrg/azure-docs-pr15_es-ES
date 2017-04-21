
<properties 
    pageTitle="Administración de activos y entidades relacionadas con los servicios de Media .NET SDK" 
    description="Obtenga información sobre cómo administrar activos y entidades relacionadas con el SDK de servicios de medios para .NET." 
    authors="juliako" 
    manager="dwrede" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016"
    ms.author="juliako"/>


#<a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Administración de activos y entidades relacionadas con los servicios de Media .NET SDK


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [REST](media-services-rest-manage-entities.md)


Este tema muestra cómo realizar las siguientes tareas de administración de Media Services:

- Obtener una referencia de activos 
- Obtener una referencia de trabajo 
- Lista de todos los activos 
- Lista tareas y activos 
- Lista de todas las directivas de acceso 
- Todos los localizadores de lista
- Enumeración a través de grandes conjuntos de entidades
- Eliminar un activo 
- Eliminar un trabajo 
- Eliminar una directiva de acceso 

##<a name="prerequisites"></a>Requisitos previos 

Vea [Configurar el entorno](media-services-set-up-computer.md)

##<a name="get-an-asset-reference"></a>Obtener una referencia de activos

Es una tarea frecuente obtener una referencia a un activo existente de Media Services. En el ejemplo siguiente se muestra cómo puede obtener una referencia de activos de la colección de activos en el servidor de objeto de contexto, basado en un Id. de activo
En el ejemplo siguiente se usa una consulta Linq para obtener una referencia a un objeto IAsset existente.

    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();
    
        return asset;
    }

##<a name="get-a-job-reference"></a>Obtener una referencia de trabajo

Cuando se trabaja con el procesamiento de tareas en el código de Media Services, a menudo necesitará obtener una referencia a una tarea existente basada en un Id. En el ejemplo siguiente se muestra cómo obtener una referencia a un objeto de IJob de la colección de trabajos.
Advertenciacuadro debe obtener una referencia de trabajo al iniciar un trabajo de codificación de ejecución larga y a continuación, necesita comprobar el estado del trabajo en un subproceso. En casos así, cuando el método devuelve desde un subproceso, debe recuperar una referencia a un trabajo actualizada.

    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();
    
        return job;
    }

##<a name="list-all-assets"></a>Lista de todos los activos

A medida que crece el número de activos que tiene en almacenamiento, resulta útil para los activos de la lista. En el ejemplo siguiente se muestra cómo recorrer la colección de activos en el objeto de contexto de servidor. Con cada activo, el ejemplo también escriben algunos de sus valores de propiedad en la consola. Por ejemplo, cada activo puede contener muchos archivos multimedia. El ejemplo de código escribe todos los archivos asociados con cada activo.



    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");
    
            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-jobs-and-assets"></a>Lista tareas y activos

Es una tarea importante relacionada con su trabajo asociado en servicios de medios de los activos de lista. En el siguiente ejemplo se muestra cómo cada objeto IJob, de la lista y, a continuación, para cada tarea, muestra propiedades sobre el trabajo, todas las tareas relacionadas, todos los activos y todos los activos de salida de la entrada. El código de este ejemplo puede ser útil para muchas otras tareas. Por ejemplo, si desea que aparezcan los activos de salida de uno o más trabajos de codificación ejecutó anteriormente, este código muestra cómo tener acceso a los activos de salida. Cuando tenga una referencia a un activo de salida, puede ofrecer el contenido a otros usuarios o aplicaciones descargarlo o proporcionar las direcciones URL. 

Para obtener más información sobre las opciones para la entrega de activos, vea [Ofrecer activos con el SDK de servicios de medios para .NET](media-services-deliver-streaming-content.md).

    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");
    
    
            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }
    
            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {
    
                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-all-access-policies"></a>Lista de todas las directivas de acceso

En servicios de medios, puede definir una directiva de acceso de un activo o sus archivos. Una directiva de acceso define los permisos para un archivo o de un activo (¿qué tipo de acceso y la duración). En el código de Media Services, suelen definir una directiva de acceso mediante la creación de un objeto IAccessPolicy y se asocia con un activo existente. A continuación, cree un objeto ILocator, que le permite proporcionar acceso directo a los activos Media Services. El proyecto de Visual Studio que acompaña a esta serie de documentación contiene varios ejemplos de código que muestran cómo crear y asignar las directivas de acceso y localizadores activos.

En el ejemplo siguiente se muestra cómo todas las directivas de acceso en el servidor de la lista y muestra el tipo de permisos asociados a cada uno. Es otra forma útil para ver las directivas de acceso a todos los objetos de ILocator en el servidor de la lista y, a continuación, para cada localizador, puede mostrar su directiva de acceso asociada mediante su propiedad AccessPolicy.

    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");
    
        }
    }

##<a name="list-all-locators"></a>Todos los localizadores de lista

Un localizador es una dirección URL que proporciona una ruta de acceso directo para tener acceso a un activo, junto con los permisos en activo definida por directiva de acceso asociado del localizador. Cada activo puede tener una colección de objetos ILocator asociadas con él en su propiedad localizadores. El contexto de servidor también tiene una colección de localizadores que contiene todos los localizadores.

En el siguiente ejemplo se enumera todos los localizadores en el servidor. Para cada localizador muestra el identificador para la directiva de acceso y activos relacionado. También se muestra el tipo de permisos, la fecha de vencimiento y la ruta completa al activo.

Observe que una ruta de acceso de localizador de un activo es solo una dirección URL base para el activo. Para crear una ruta de acceso directo a los archivos individuales que podría busque un usuario o una aplicación, su código debe agregar la ruta de acceso de archivo específico a la ruta de acceso de localizador. Para obtener más información acerca de cómo hacerlo, vea el tema [Entregar activos con el SDK de servicios de medios para .NET](media-services-deliver-streaming-content.md).

    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }

## <a name="enumerating-through-large-collections-of-entities"></a>Enumeración a través de grandes conjuntos de entidades

Al consultar entidades, hay un límite de 1000 entidades devuelto a la vez porque público v2 resto los límites de los resultados de la consulta a los resultados de 1000. Debe usar saltar y tomar cuando enumere a través de grandes conjuntos de entidades. 
    
La siguiente función recorre todos los trabajos en la cuenta de servicios multimedia proporcionado. Media Services devuelve 1000 trabajos de colección de trabajos. La función emplea saltar y tomar para asegurarse de que todos los trabajos se enumeran (en caso de que tenga más de 1000 trabajos en su cuenta).
    
    static void ProcessJobs()
    {
        try
        {
    
            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;
    
            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }
    
                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

##<a name="delete-an-asset"></a>Eliminar un activo

En el ejemplo siguiente se elimina un activo.

    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();
    
        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");
    
    }

##<a name="delete-a-job"></a>Eliminar un trabajo

Para eliminar una tarea, debe comprobar el estado de la tarea tal como se indica en la propiedad de estado. Se pueden eliminar trabajos que se completa o se cancela, mientras tareas que están en otros Estados, como en cola, programada o procesamiento, es necesario cancelar antes y, a continuación, se pueden eliminar.
En el ejemplo siguiente se muestra un método para eliminar un trabajo de comprobación Estados de trabajo y, a continuación, eliminar cuando el estado es terminado o cancelado. Este código depende de la sección anterior de este tema para obtener una referencia a un trabajo: obtener una referencia de trabajo.

    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;
    
        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);
    
            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }
    
        }
    }


##<a name="delete-an-access-policy"></a>Eliminar una directiva de acceso

En el ejemplo siguiente se muestra cómo obtener una referencia a una directiva de acceso basada en un Id y, a continuación, elimine la directiva.

    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();
    
        policy.Delete();
    
    }
    


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
