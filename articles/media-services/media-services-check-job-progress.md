<properties 
    pageTitle="Supervisar el progreso de tarea con .NET" 
    description="Obtenga información sobre cómo usar el código del controlador de eventos para realizar un seguimiento del progreso de trabajo y enviar las actualizaciones de estado. El código de ejemplo escrito en C# y usa el SDK de servicios de medios para .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016"   
    ms.author="juliako"/>

# <a name="monitor-job-progress-using-net"></a>Supervisar el progreso de tarea con .NET

> [AZURE.SELECTOR]
- [Portal](media-services-portal-check-job-progress.md)
- [.NET](media-services-check-job-progress.md)
- [REST](media-services-rest-check-job-progress.md)

Cuando se ejecutan trabajos, a menudo requieren una manera de realizar un seguimiento del progreso de la tarea. Puede comprobar el progreso define un controlador de eventos StateChanged (como se describe en este tema) o utilizando el almacenamiento de Azure cola para supervisar las notificaciones del trabajo Media Services (como se describe en [este](media-services-dotnet-check-job-progress-with-queues.md) tema).

## <a name="define-statechanged-event-handler-to-monitor-job-progress"></a>Definir StateChanged el controlador de eventos para supervisar el progreso de tarea

En el ejemplo siguiente se define el controlador de eventos StateChanged. Este controlador de eventos realiza un seguimiento de progreso de tarea y proporciona estado actualizado, dependiendo del estado. El código también define el método LogJobStop. Este método auxiliar registra los detalles del error.

    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
    
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("********************");
                Console.WriteLine("Job is finished.");
                Console.WriteLine("Please wait while local tasks or downloads complete...");
                Console.WriteLine("********************");
                Console.WriteLine();
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
    
    private static void LogJobStop(string jobId)
    {
        StringBuilder builder = new StringBuilder();
        IJob job = GetJob(jobId);
    
        builder.AppendLine("\nThe job stopped due to cancellation or an error.");
        builder.AppendLine("***************************");
        builder.AppendLine("Job ID: " + job.Id);
        builder.AppendLine("Job Name: " + job.Name);
        builder.AppendLine("Job State: " + job.State.ToString());
        builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
        builder.AppendLine("Media Services account name: " + _accountName);
        builder.AppendLine("Media Services account location: " + _accountLocation);
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
        // Write the output to a local file and to the console. The template 
        // for an error output file is:  JobStop-{JobId}.txt
        string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
        WriteToFile(outputFile, builder.ToString());
        Console.Write(builder.ToString());
    }
    
    private static string JobIdAsFileName(string jobID)
    {
        return jobID.Replace(":", "_");
    }



##<a name="next-step"></a>Siguiente paso

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
