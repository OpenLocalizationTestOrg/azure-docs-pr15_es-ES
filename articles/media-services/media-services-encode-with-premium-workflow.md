<properties 
    pageTitle="Avanzado de codificación con el flujo de trabajo de Media Encoder Premium | Microsoft Azure" 
    description="Obtenga información sobre cómo codificar con el flujo de trabajo de Media Encoder Premium. Ejemplos de código están escritos en C# y usan el SDK de servicios de medios para .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>

#<a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Avanzado de codificación con el flujo de trabajo de Media Encoder Premium

>[AZURE.NOTE] Procesador de medios de flujo de trabajo de Media Encoder Premium analizado en este tema no está disponible en China.

Para las preguntas de codificador premium, correo electrónico mepd en Microsoft.com.

##<a name="overview"></a>Información general

Microsoft Azure Media Services presenta el procesador de media de **Flujo de trabajo de Media Encoder Premium** . Este avance de ofertas de procesador codificación capacidades de sus flujos de trabajo de premium a petición. 

Los temas siguientes describen detalles relacionados con **El flujo de trabajo de Media Encoder Premium**: 

- [Formatos admitidos por el flujo de trabajo de Media Encoder Premium](media-services-premium-workflow-encoder-formats.md) : explica los formatos de archivo y códecs compatibles con **El flujo de trabajo de Media Encoder Premium**.

- La sección [comparar codificadores](media-services-encode-asset.md#compare_encoders) compara las capacidades de codificación de **Flujo de trabajo de Media Encoder Premium** y **Media Encoder estándar**.

En este tema se muestra cómo codificar con **El flujo de trabajo de Media Encoder Premium** con .NET.

Tareas de codificación para el **Flujo de trabajo de Media Encoder Premium** requieren un archivo de configuración independiente, denominado un archivo de flujo de trabajo. Estos archivos tienen una extensión .workflow y creados con la herramienta de [Diseñador de flujo de trabajo](media-services-workflow-designer.md) .

##<a name="encode"></a>Codificar

Tareas de codificación para el **Flujo de trabajo de Media Encoder Premium** requieren un archivo de configuración independiente, denominado un archivo de flujo de trabajo. Estos archivos tienen una extensión .workflow y creados con la herramienta de [Diseñador de flujo de trabajo](media-services-workflow-designer.md) .


También puede obtener el valor predeterminado archivos de flujo de trabajo [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). La carpeta también contiene la descripción de estos archivos.

Los archivos de flujo de trabajo deben cargarse en su cuenta de Media Services como un activo, y este activo se debe pasar a la tarea de codificación.

En el ejemplo siguiente se muestra cómo codificar con **El flujo de trabajo de Media Encoder Premium**. 

Se realizan los siguientes pasos: 
 
1. Crear un activo y cargar un archivo de flujo de trabajo. 
2. Crear un activo y cargar un archivo multimedia de origen.
3. Obtener el procesador multimedia de "Flujo de trabajo de Media Encoder Premium".
4. Crear un trabajo y una tarea. 

    En la mayoría de los casos, la cadena de configuración de la tarea está vacía (como en el ejemplo siguiente). Hay algunos escenarios avanzados (que se requieren establecer el tiempo de ejecución propiedades dinámicamente) en cuyo caso deberá proporcionar una cadena XML a la tarea de codificación. Ejemplos de estas situaciones son: creación de una superposición, secuencial o paralelo multimedia panorámicas, subtitulado.
5. Agregar dos activos de entrada a la tarea.
    
    una. 1 º – activo de flujo de trabajo.

    b. 2 º – el recurso de vídeo.
    
    **Nota**: los activos de flujo de trabajo deben agregarse a la tarea antes de activos de medios. La cadena de configuración para esta tarea debe estar vacía. 

6. Enviar el trabajo de codificación.

El siguiente es un ejemplo completo. Para obtener información sobre cómo configurar con el desarrollo de .NET de servicios de medios, consulte [desarrollo de Media Services con .NET](media-services-dotnet-how-to-use.md).


    using System; 
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.MediaServices.Client;
    
    namespace MediaEncoderPremiumWorkflowSample
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            private static readonly string _supportFiles =
                Path.GetFullPath(@"../..\Media");
    
            private static readonly string _workflowFilePath =
                Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");
            
            private static readonly string _singleMP4InputFilePath =
                Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");
    
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
    
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
                var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
                IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset); 
    
            }
    
            static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
            {
                var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
                var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
                var fileName = Path.GetFileName(singleFilePath);
    
                var assetFile = asset.AssetFiles.Create(fileName);
    
                Console.WriteLine("Created assetFile {0}", assetFile.Name);
    
                var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                    AccessPermissions.Write | AccessPermissions.List);
    
                var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
    
                Console.WriteLine("Upload {0}", assetFile.Name);
    
                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading {0}", assetFile.Name);
    
                locator.Delete();
                accessPolicy.Delete();
    
                return asset;
            }
    
            static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Premium Workflow encoding job");
                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");
    
                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                    processor,
                    "",
                    TaskOptions.None);
    
                // Specify the input asset to be encoded.
                task.InputAssets.Add(workflow);
                task.InputAssets.Add(video); // we add one asset
                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is not encrypted. 
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);
    
                // Use the following event handler to check job progress.  
                job.StateChanged += new
                        EventHandler<JobStateChangedEventArgs>(StateChanged);
    
                // Launch the job.
                job.Submit();
    
                // Check job execution and wait for job to finish. 
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                progressJobTask.Wait();
    
                // If job state is Error the event handling 
                // method for job progress should log errors.  Here we check 
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    throw new Exception("\nExiting method due to job error.");
                }
    
                return job.OutputMediaAssets[0];
            }
    
            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);
    
                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine();
                        break;
                    case JobState.Canceling:
                    case JobState.Queued:
                    case JobState.Scheduled:
                    case JobState.Processing:
                        Console.WriteLine("Please wait...\n");
                        break;
                    case JobState.Canceled:
                    case JobState.Error:
                        // Cast sender as a job.
                        IJob job = (IJob)sender;
                        // Display or log error details as needed.
                        LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }
    
            static private void LogJobStop(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();
    
                builder.AppendLine("\nThe job stopped due to cancellation or an error.");
                builder.AppendLine("***************************");
                builder.AppendLine("Job ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job State: " + job.State.ToString());
                builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
                builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
                // Log job errors if they exist.  
                if (job.State == JobState.Error)
                {
                    builder.Append("Error Details: \n");
                    foreach (ITask task in job.Tasks)
                    {
                        foreach (ErrorDetail detail in task.ErrorDetails)
                        {
                            builder.AppendLine("  Task Id: " + task.Id);
                            builder.AppendLine("    Error Code: " + detail.Code);
                            builder.AppendLine("    Error Message: " + detail.Message + "\n");
                        }
                    }
                }
                builder.AppendLine("***************************\n");
    
                Console.Write(builder.ToString());
            }
    
    
            static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
    
                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
    
                return processor;
            }
        }
    }


Para las preguntas de codificador premium, correo electrónico mepd en Microsoft.com.

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
