<properties 
    pageTitle="Usar el almacenamiento de Azure cola para supervisar las notificaciones del trabajo Media Services con .NET | Microsoft Azure" 
    description="Obtenga información sobre cómo usar el almacenamiento de Azure cola para supervisar las notificaciones del trabajo de Media Services. El código de ejemplo escrito en C# y usa el SDK de servicios de medios para .NET." 
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

# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Usar el almacenamiento de Azure cola para supervisar las notificaciones del trabajo Media Services con .NET

Cuando se ejecutan trabajos, a menudo requieren una manera de realizar un seguimiento del progreso de la tarea. Puede comprobar el progreso mediante el almacenamiento de Azure cola para supervisar las notificaciones del trabajo Media Services (como se describe en este tema) o definir un controlador de eventos StateChanged (como se describe en [este](media-services-check-job-progress.md) tema.  

## <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications"></a>Usar almacenamiento en cola de Azure para supervisar las notificaciones del trabajo Media Services

Microsoft Azure Media Services tiene la capacidad para enviar mensajes de notificación para el [almacenamiento de Azure cola](../storage-dotnet-how-to-use-queues.md#what-is) al procesamiento de trabajos de medios. Este tema muestra cómo obtener estos mensajes de notificación de almacenamiento en la cola.

Mensajes entregados al almacenamiento de cola pueden tener acceso desde cualquier lugar del mundo. La cola de Azure arquitectura de mensajería es confiable y de alta escalabilidad. Almacenamiento en cola de sondeo es preferible con otros métodos. 

Un escenario común para escuchar las notificaciones de Media Services es si va a desarrollar un sistema de administración de contenido que debe llevar a cabo algunas tareas adicionales después de un trabajo de codificación completa (por ejemplo, el siguiente paso de un flujo de trabajo o publicar contenido de desencadenador). 

###<a name="considerations"></a>Consideraciones

Tenga en cuenta lo siguiente al desarrollar aplicaciones de Media Services que usan cola de almacenamiento de Azure.

- El servicio de colas no proporciona una garantía de primero en el primero en salir (FIFO) entrega ordenada. Para obtener más información, vea [colas de Azure y Azure servicio Bus colas comparan y Contrasted](https://msdn.microsoft.com/library/azure/hh767287.aspx).
- Azure colas de almacenamiento no es un servicio de inserción; tiene que sondear la cola. 
- Puede tener cualquier número de colas. Para obtener más información, vea [API de REST de cola de servicio](https://msdn.microsoft.com/library/azure/dd179363.aspx).
- Colas de almacenamiento Azure tiene algunas limitaciones y características que se describen en el siguiente artículo: [colas de Azure y Azure servicio Bus colas comparan y Contrasted](https://msdn.microsoft.com/library/azure/hh767287.aspx).

###<a name="code-example"></a>Ejemplo de código

El ejemplo de código en esta sección hace lo siguiente:

1. Define la clase **EncodingJobMessage** que se asigna el formato de mensaje de notificación. El código deserializa mensajes recibidos desde la cola en objetos del tipo **EncodingJobMessage** .
1. Carga la información de cuenta Media Services y almacenamiento del archivo app.config. Usa esta información para crear los objetos **CloudMediaContext** y **CloudQueue** .
1. Crea la cola que recibe los mensajes de notificación sobre el trabajo de codificación.
1. Crea el punto final de notificación que se asigna a la cola.
1. Asocia el punto final de notificación para el trabajo y envía el trabajo de codificación. Puede tener varios puntos finales de notificación adjuntos a un trabajo.
1. En este ejemplo, hemos sólo está interesados en Estados finales de procesamiento de trabajo, por lo que se pasa **NotificationJobState.FinalStatesOnly** al método **AddNew** . 
        
        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
1. Si se pasa NotificationJobState.All, debería obtener todas las notificaciones de cambio de estado: en cola -> programada -> procesamiento -> terminado. Sin embargo, como se mencionó anteriormente, el servicio de colas de almacenamiento de Azure no garantiza la entrega solicitada. Puede usar la propiedad de marca de tiempo (definida en el tipo de EncodingJobMessage en el ejemplo siguiente) a los mensajes de pedido. Es posible que recibe mensajes de notificación duplicados. Use la propiedad ETag (definida en el tipo de EncodingJobMessage) para comprobar si existen duplicados. También es posible que se omitirán algunas notificaciones de cambio de estado. 
1. Espera el trabajo obtener el estado finalizado activando la cola cada 10 segundos. Elimina los mensajes después de que se ha procesado.
1. Elimina la cola y el punto final de notificación.

>[AZURE.NOTE]La manera recomendada para supervisar el estado de la tarea está escuchando a los mensajes de notificación, tal como se muestra en el ejemplo siguiente.
>
>Como alternativa, puede comprobar estado del trabajo mediante la propiedad **IJob.State** .  Es posible que llegue un mensaje de notificación sobre la finalización de la tarea antes de establece el estado en **IJob** a **terminado**. La propiedad **IJob.State** refleja el estado preciso con una demora.

    
    using System;
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Web;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Runtime.Serialization.Json;
    
    namespace JobNotification
    {
        public class EncodingJobMessage
        {
            // MessageVersion is used for version control. 
            public String MessageVersion { get; set; }
        
            // Type of the event. Valid values are 
            // JobStateChange and NotificationEndpointRegistration.
            public String EventType { get; set; }
        
            // ETag is used to help the customer detect if 
            // the message is a duplicate of another message previously sent.
            public String ETag { get; set; }
        
            // Time of occurrence of the event.
            public String TimeStamp { get; set; }
    
            // Collection of values specific to the event.
    
            // For the JobStateChange event the values are:
            //     JobId - Id of the Job that triggered the notification.
            //     NewState- The new state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
            //     OldState- The old state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
    
            // For the NotificationEndpointRegistration event the values are:
            //     NotificationEndpointId- Id of the NotificationEndpoint 
            //          that triggered the notification.
            //     State- The state of the Endpoint. 
            //          Valid values are: Registered and Unregistered.
    
            public IDictionary<string, object> Properties { get; set; }
        }
    
        class Program
        {
            private static CloudMediaContext _context = null;
            private static CloudQueue _queue = null;
            private static INotificationEndPoint _notificationEndPoint = null;
    
            private static readonly string _singleInputMp4Path =
                Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");
    
            static void Main(string[] args)
            {
                // Get the values from app.config file.
                string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
                string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
                string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];
    
    
                string endPointAddress = Guid.NewGuid().ToString();
    
                // Create the context. 
                _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);
    
                // Create the queue that will be receiving the notification messages.
                _queue = CreateQueue(storageConnectionString, endPointAddress);
    
                // Create the notification point that is mapped to the queue.
                _notificationEndPoint = 
                        _context.NotificationEndPoints.Create(
                        Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);
    
    
                if (_notificationEndPoint != null)
                {
                    IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                    WaitForJobToReachedFinishedState(job.Id);
                }
    
                // Clean up.
                _queue.Delete();      
                _notificationEndPoint.Delete();
           }
    
    
            static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
            {
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);
    
                // Create the queue client
                CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
                // Retrieve a reference to a queue
                CloudQueue queue = queueClient.GetQueueReference(endPointAddress);
    
                // Create the queue if it doesn't already exist
                queue.CreateIfNotExists();
    
                return queue;
            }
     
    
            public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");
    
                //Create an encrypted asset and upload the mp4. 
                IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
                    inputMediaFilePath);
    
                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
                // Create a task with the conversion details, using a configuration file. 
                ITask task = job.Tasks.AddNew("My encoding Task",
                    processor,
                    "H264 Multiple Bitrate 720p",
                    Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
    
                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);
    
                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);
    
                // Add a notification point to the job. You can add multiple notification points.  
                job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, 
                    _notificationEndPoint);
    
                job.Submit();
    
                return job;
            }
    
            public static void WaitForJobToReachedFinishedState(string jobId)
            {
                int expectedState = (int)JobState.Finished;
                int timeOutInSeconds = 600;
    
                bool jobReachedExpectedState = false;
                DateTime startTime = DateTime.Now;
                int jobState = -1;
    
                while (!jobReachedExpectedState)
                {
                    // Specify how often you want to get messages from the queue.
                    Thread.Sleep(TimeSpan.FromSeconds(10));
    
                    foreach (var message in _queue.GetMessages(10))
                    {
                        using (Stream stream = new MemoryStream(message.AsBytes))
                        {
                            DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                            settings.UseSimpleDictionaryFormat = true;
                            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                            EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);
    
                            Console.WriteLine();
    
                            // Display the message information.
                            Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                            Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                            Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                            Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                            foreach (var property in encodingJobMsg.Properties)
                            {
                                Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                            }
    
                            // We are only interested in messages 
                            // where EventType is "JobStateChange".
                            if (encodingJobMsg.EventType == "JobStateChange")
                            {
                                string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                                if (JobId == jobId)
                                {
                                    string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                    string newJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "NewState").FirstOrDefault().Value;
    
                                    JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                    JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);
    
                                    if (newJobState == (JobState)expectedState)
                                    {
                                        Console.WriteLine("job with Id: {0} reached expected state: {1}", 
                                            jobId, newJobState);
                                        jobReachedExpectedState = true;
                                        break;
                                    }
                                }
                            }
                        }
                        // Delete the message after we've read it.
                        _queue.DeleteMessage(message);
                    }
    
                    // Wait until timeout
                    TimeSpan timeDiff = DateTime.Now - startTime;
                    bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                    if (timedOut)
                    {
                        Console.WriteLine(@"Timeout for checking job notification messages, 
                                            latest found state ='{0}', wait time = {1} secs",
                            jobState,
                            timeDiff.TotalSeconds);
    
                        throw new TimeoutException();
                    }
                }
            }
       
            static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
            {
                var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(), 
                    assetCreationOptions);
    
                var fileName = Path.GetFileName(singleFilePath);
    
                var assetFile = asset.AssetFiles.Create(fileName);
    
                Console.WriteLine("Created assetFile {0}", assetFile.Name);
                Console.WriteLine("Upload {0}", assetFile.Name);
    
                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading of {0}", assetFile.Name);
    
                return asset;
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

El ejemplo anterior, produce el siguiente resultado. Se valores variarán.
    
    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4
    
    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35
    
    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected 
    State: Finished
    

## <a name="next-step"></a>Siguiente paso

Revisar Media Services rutas de aprendizaje

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
