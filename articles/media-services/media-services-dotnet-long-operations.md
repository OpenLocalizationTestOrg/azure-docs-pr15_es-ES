<properties 
    pageTitle="Operaciones de ejecución larga de sondeo | Microsoft Azure" 
    description="Este tema muestra cómo sondear operaciones de ejecución larga." 
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


#<a name="delivering-live-streaming-with-azure-media-services"></a>Realiza la transmisión directo con Azure Media Services

##<a name="overview"></a>Información general

Microsoft Azure Media Services ofrece las API que envían solicitudes a Media Services para iniciar operaciones (por ejemplo: crear, iniciar, detener o eliminar un canal). Estas operaciones son mucho tiempo de ejecución.

Media Services .NET SDK proporciona API que envíe la solicitud y espere a que finalice la operación (internamente, las API realizan un sondeo de progreso de la operación en algunos intervalos). Por ejemplo, cuando llame a canal. Start(), el método devuelve cuando se inicia el canal. También puede usar la versión asincrónica: espera canal. StartAsync() (para obtener información sobre el modelo asincrónico basado en tareas, consulte [PUNTEE](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)). API que enviar una solicitud de operación y, a continuación, sondean para el estado hasta que se complete la operación se denominan "métodos de sondeo". Estos métodos (especialmente la versión asincrónica) se recomiendan para aplicaciones de cliente enriquecido o servicios con estado.

Existen escenarios donde una aplicación no se puede esperar una solicitud de http larga y desea sondear manualmente el progreso de la operación. Un ejemplo típico sería un explorador interactuar con un servicio web sin estado: cuando se solicita el explorador para crear un canal, el servicio web inicia una operación larga y devuelve el identificador de operación para el explorador. El explorador podría pedir a continuación, el servicio web para obtener el estado de la operación según el Id. Media Services .NET SDK proporciona API que son útiles para este escenario. Estas API se denominan "métodos de sondeo no".
Los métodos de sondeo no"" tienen el siguiente modelo de nomenclatura: enviar*OperationName*operación (por ejemplo, SendCreateOperation). Enviar los métodos de operación*OperationName*devuelven el objeto **IOperation** ; el objeto devuelto contiene información que puede utilizarse para realizar un seguimiento de la operación. Los métodos de envío*OperationName*OperationAsync devuelven **tareas<IOperation>**.

Actualmente, las clases siguientes admiten métodos no sondeo: **canal**, **StreamingEndpoint**y **programa**.

Para sondear el estado de la operación, use el método de **GetOperation** en la clase **OperationBaseCollection** . Use los siguientes intervalos para comprobar el estado de la operación: para operaciones de **canal** y **StreamingEndpoint** , use 30 segundos. para operaciones de **programa** , use 10 segundos.


##<a name="example"></a>Ejemplo

En el ejemplo siguiente se define una clase denominada **ChannelOperations**. Definición de la clase podría ser un punto de partida para la definición de clase de servicio web. Para simplificar, los siguientes ejemplos utilizan las versiones no asincrónica de métodos.

El ejemplo también muestra cómo el cliente puede utilizar esta clase.

###<a name="channeloperations-class-definition"></a>Definición de la clase ChannelOperations

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;
    
        public ChannelOperations()
        {
                _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
                _context = new CloudMediaContext(_cachedCredentials);    }
    
        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });
    
            return operation.Id;
        }
    
        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;
    
            channelId = null;
    
            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }
    
    
        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelPreview CreateChannelPreview()
        {
            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

###<a name="the-client-code"></a>El código de cliente

    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");
    
    string channelId = null;
    bool isCompleted = false;
    
    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }
    
    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);
 


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
