<properties 
    pageTitle="Cómo llevar a cabo la transmisión directo con Azure Media Services para crear secuencias de velocidad múltiple con .NET | Microsoft Azure" 
    description="Este tutorial le guiará por los pasos de la creación de un canal que recibe un flujo en vivo solo velocidad y codifica en secuencia de velocidad múltiple utilizando .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-net"></a>Cómo llevar a cabo la transmisión directo con Azure Media Services para crear secuencias de velocidad múltiple con .NET

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API DE REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

>[AZURE.NOTE]
> Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F).

##<a name="overview"></a>Información general

Este tutorial le guiará por los pasos de la creación de un **canal** que recibe un flujo en vivo solo velocidad y codifica en secuencia de velocidad múltiple.

Para obtener más información relacionada con los canales que están habilitados para la codificación en directo, vea [Live transmisión mediante Azure Media Services para crear secuencias de velocidad múltiple](media-services-manage-live-encoder-enabled-channels.md).


##<a name="common-live-streaming-scenario"></a>Escenario de transmisión directo comunes

Los pasos siguientes describen las tareas relacionadas con la creación de aplicaciones de streaming directo comunes.

>[AZURE.NOTE] Actualmente, la duración máxima recomendada de un evento en directo es 8 horas. Póngase en contacto con amslived en Microsoft.com si necesita para ejecutar un canal para largos períodos de tiempo.

1. Conectar una cámara de vídeo en un equipo. Iniciar y configurar un codificador live local que puede enviar una secuencia de velocidad solo en uno de los siguientes protocolos: RTMP, Smooth Streaming o RTP (MPEG-TS). Para obtener más información, vea [soporte técnico de Azure Media Services RTMP y codificadores Live](http://go.microsoft.com/fwlink/?LinkId=532824).

También se pudo realizar este paso después de crear el canal.

1. Crear e iniciar un canal.

1. Recuperar el canal recopile la dirección URL.

La dirección URL de ingesta se usa el codificador directo para enviar la secuencia para el canal.

1. Recuperar la dirección URL de vista previa de canal.

Utilizar esta dirección URL para comprobar que su canal correctamente recibe la secuencia en directo.

2. Crear un activo.
3. Si desea que el activo dinámicamente cifrado durante la reproducción, haga lo siguiente:
1. Crear una clave de contenido.
1. Configurar la directiva de autorización de la clave de contenido.
1. Configurar la directiva de entrega de activos (utilizada por embalaje dinámico y cifrado dinámico).
3. Crear un programa y especificar el uso de los activos que ha creado.
1. Publicar el activo asociado con el programa mediante la creación de un localizador de petición.

Asegúrese de tener al menos una unidad reservada en el extremo streaming desde la que desee para transmitir contenido de transmisión.

1. Cuando esté listo para iniciar la transmisión y archivado, inicie el programa.
2. Si lo desea, puede señalar el codificador directo para iniciar un anuncio. El anuncio se inserta en la secuencia de salida.
1. Detener el programa siempre que desee detener la transmisión y archivado el evento.
1. Eliminar el programa (y, opcionalmente, eliminar el activo).

## <a name="what-youll-learn"></a>Lo que aprenderá

Este tema muestra cómo ejecutar distintas operaciones en los canales y los programas que usan .NET SDK de Media Services. Dado que muchas operaciones son larga ejecución API de .NET que administrar largas en ejecución se usan operaciones.

El tema muestra cómo hacer lo siguiente:

1. Crear e iniciar un canal. Se usan las API de ejecución larga.
1. Obtener los canales recopilación extremo (entrada). Este extremo debe proporcionarse al codificador que puede enviar un flujo en vivo velocidad única.
1. Obtener el extremo de vista previa. Este extremo se usa para obtener una vista previa de la secuencia.
1. Crear un activo, que se utilizará para guardar el contenido. Deben configurar las directivas de entrega de activos físicos, como se muestra en este ejemplo.
1. Crear un programa y especificar el uso de los activos que se crearon con una versión anterior. Inicie el programa. Se usan las API de ejecución larga.
1. Crear un localizador para el activo, por lo que el contenido se publicará y puede transmitir a sus clientes.
1. Mostrar y ocultar las tabletas. Iniciar y detener anuncios. Se usan las API de ejecución larga.
1. Limpiar el canal y todos los recursos asociados.


##<a name="prerequisites"></a>Requisitos previos

Los siguientes son necesarios para completar el tutorial.

- Para completar este tutorial, necesita una cuenta de Azure.

Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Obtenga créditos que se pueden usar para probar los servicios de Azure pagados. Incluso después de que se usan los créditos hacia arriba, puede mantener la cuenta y usar las características, como la característica de aplicaciones Web de servicio de la aplicación de Azure y servicios gratuitos de Azure.
- Una cuenta de Media Services. Para crear una cuenta de Media Services, vea [Crear una cuenta](media-services-portal-create-account.md).
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express) o versiones posteriores.
- Debe usar Media Services .NET SDK versión 3.2.0.0 o posterior.
- Una cámara Web y un codificador que puede enviar un flujo en vivo velocidad única.

##<a name="considerations"></a>Consideraciones

- Actualmente, la duración máxima recomendada de un evento en directo es 8 horas. Póngase en contacto con amslived en Microsoft.com si necesita para ejecutar un canal para largos períodos de tiempo.
- Asegúrese de tener al menos una unidad reservada en el extremo streaming desde la que desee para transmitir contenido de transmisión.

##<a name="download-sample"></a>Descargar ejemplo

Obtener y ejecutar un ejemplo desde [aquí](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).


##<a name="set-up-for-development-with-media-services-sdk-for-net"></a>Configurar para el desarrollo con Media Services SDK para .NET

1. Crear una aplicación de consola mediante Visual Studio.
1. Agregue el SDK de servicios de medios para .NET a la aplicación de consola mediante Media Services NuGet paquete.

##<a name="connect-to-media-services"></a>Conectarse a servicios de medios
Como práctica recomendada, debe usar un archivo app.config para almacenar la clave de nombre y la cuenta de Media Services.

>[AZURE.NOTE]Para buscar el nombre y la clave de valores, vaya al portal de Azure y seleccione la cuenta. Aparece la ventana de configuración de la derecha. En la ventana Configuración, seleccione teclas. Al hacer clic en el icono junto a cada cuadro de texto, copia el valor al Portapapeles del sistema.

Agregue la sección appSettings al archivo app.config y establecer los valores de la clave de nombre y la cuenta de la cuenta de Media Services.


    <?xml version="1.0"?>
    <configuration>
      <appSettings>
          <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
          <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
      </appSettings>
    </configuration>
     
    
##<a name="code-example"></a>Ejemplo de código

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Net;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    
    namespace EncodeLiveStreamWithAmsClear
    {
        class Program
        {
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";
    
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                IChannel channel = CreateAndStartChannel();
    
                // The channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Intest URL: {0}", ingestUrl);
    
    
                // Use the previewEndpoint to preview and verify 
                // that the input from the encoder is actually reaching the Channel. 
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Preview URL: {0}", previewEndpoint);
    
                // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
                // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
                // The thumbnail is exposed via the same end-point as the Channel Preview URL.
                string thumbnailUri = new UriBuilder
                {
                    Scheme = Uri.UriSchemeHttps,
                    Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                    Path = "thumbnails/input.jpg"
                }.Uri.ToString();
    
                Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
    
                // Once you previewed your stream and verified that it is flowing into your Channel, 
                // you can create an event by creating an Asset, Program, and Streaming Locator. 
                IAsset asset = CreateAndConfigureAsset();
    
                IProgram program = CreateAndStartProgram(channel, asset);
    
                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
    
                // You can use slates and ads only if the channel type is Standard.  
                StartStopAdsSlates(channel);
    
                // Once you are done streaming, clean up your resources.
                Cleanup(channel);
    
            }
    
            public static IChannel CreateAndStartChannel()
            {
                var channelInput = CreateChannelInput();
                var channePreview = CreateChannelPreview();
                var channelEncoding = CreateChannelEncoding();
    
    
                ChannelCreationOptions options = new ChannelCreationOptions
                {
                    EncodingType = ChannelEncodingType.Standard,
                    Name = ChannelName,
                    Input = channelInput,
                    Preview = channePreview,
                    Encoding = channelEncoding
                };
    
                Log("Creating channel");
                IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
                string channelId = TrackOperation(channelCreateOperation, "Channel create");
    
                IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
    
                Log("Starting channel");
                var channelStartOperation = channel.SendStartOperation();
                TrackOperation(channelStartOperation, "Channel start");
    
                return channel;
            }
    
            /// <summary>
            /// Create channel input, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
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
    
            /// <summary>
            /// Create channel preview, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
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
    
            /// <summary>
            /// Create channel encoding, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelEncoding CreateChannelEncoding()
            {
                return new ChannelEncoding
                {
                    SystemPreset = "Default720p",
                    IgnoreCea708ClosedCaptions = false,
                    AdMarkerSource = AdMarkerSource.Api,
                    // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
                    AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
                };
            }
    
            /// <summary>
            /// Create an asset and configure asset delivery policies.
            /// </summary>
            /// <returns></returns>
            public static IAsset CreateAndConfigureAsset()
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
    
                IAssetDeliveryPolicy policy =
                    _context.AssetDeliveryPolicies.Create("Clear Policy",
                    AssetDeliveryPolicyType.NoDynamicEncryption,
                    AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
    
                asset.DeliveryPolicies.Add(policy);
    
                return asset;
            }
    
            /// <summary>
            /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
            /// however each Program must have a unique name within your Media Services account.
            /// </summary>
            /// <param name="channel"></param>
            /// <param name="asset"></param>
            /// <returns></returns>
            public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
            {
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                Log("Program created", program.Id);
    
                Log("Starting program");
                var programStartOperation = program.SendStartOperation();
                TrackOperation(programStartOperation, "Program start");
    
                return program;
            }
    
            /// <summary>
            /// Create locators in order to be able to publish and stream the video.
            /// </summary>
            /// <param name="asset"></param>
            /// <param name="ArchiveWindowLength"></param>
            /// <returns></returns>
            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                            (
                                "Live Stream Policy",
                                ArchiveWindowLength,
                                AccessPermissions.Read
                            )
                    );
    
                return locator;
            }
    
            /// <summary>
            /// Perform operations on slates.
            /// </summary>
            /// <param name="channel"></param>
            public static void StartStopAdsSlates(IChannel channel)
            {
                int cueId = new Random().Next(int.MaxValue);
                var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
    
                Log("Creating asset");
                var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
                Log("Slate asset created", slateAsset.Id);
    
                Log("Uploading file");
                var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
                assetFile.Upload(path);
                assetFile.IsPrimary = true;
                assetFile.Update();
    
                Log("Showing slate");
                var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
                TrackOperation(showSlateOpeartion, "Show slate");
    
                Log("Hiding slate");
                var hideSlateOperation = channel.SendHideSlateOperation();
                TrackOperation(hideSlateOperation, "Hide slate");
    
                Log("Starting ad");
                var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
                TrackOperation(startAdOperation, "Start ad");
    
                Log("Ending ad");
                var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
                TrackOperation(endAdOperation, "End ad");
    
                Log("Deleting slate asset");
                slateAsset.Delete();
            }
    
            /// <summary>
            /// Clean up resources associated with the channel.
            /// </summary>
            /// <param name="channel"></param>
            public static void Cleanup(IChannel channel)
            {
                IAsset asset;
                if (channel != null)
                {
                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();
    
                        Log("Stopping program");
                        var programStopOperation = program.SendStopOperation();
                        TrackOperation(programStopOperation, "Program stop");
    
                        program.Delete();
    
                        if (asset != null)
                        {
                            Log("Deleting locators");
                            foreach (var l in asset.Locators)
                                l.Delete();
    
                            Log("Deleting asset");
                            asset.Delete();
                        }
                    }
    
                    Log("Stopping channel");
                    var channelStopOperation = channel.SendStopOperation();
                    TrackOperation(channelStopOperation, "Channel stop");
    
                    Log("Deleting channel");
                    var channelDeleteOperation = channel.SendDeleteOperation();
                    TrackOperation(channelDeleteOperation, "Channel delete");
                }
            }
    
    
            /// <summary>
            /// Track long running operations.
            /// </summary>
            /// <param name="operation"></param>
            /// <param name="description"></param>
            /// <returns></returns>
            public static string TrackOperation(IOperation operation, string description)
            {
                string entityId = null;
                bool isCompleted = false;
    
                Log("starting to track ", null, operation.Id);
                while (isCompleted == false)
                {
                    operation = _context.Operations.GetOperation(operation.Id);
                    isCompleted = IsCompleted(operation, out entityId);
                    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
                }
                // If we got here, the operation succeeded.
                Log(description + " in completed", operation.TargetEntityId, operation.Id);
    
                return entityId;
            }
    
            /// <summary> 
            /// Checks if the operation has been completed. 
            /// If the operation succeeded, the created entity Id is returned in the out parameter.
            /// </summary> 
            /// <param name="operationId">The operation Id.</param> 
            /// <param name="channel">
            /// If the operation succeeded, 
            /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
            /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
            private static bool IsCompleted(IOperation operation, out string entityId)
            {
    
                bool completed = false;
    
                entityId = null;
    
                switch (operation.State)
                {
                    case OperationState.Failed:
                        // Handle the failure. 
                        // For example, throw an exception. 
                        // Use the following information in the exception: operationId, operation.ErrorMessage.
                        Log("operation failed", operation.TargetEntityId, operation.Id);
                        break;
                    case OperationState.Succeeded:
                        completed = true;
                        entityId = operation.TargetEntityId;
                        break;
                    case OperationState.InProgress:
                        completed = false;
                        Log("operation in progress", operation.TargetEntityId, operation.Id);
                        break;
                }
                return completed;
            }
    
    
            private static void Log(string action, string entityId = null, string operationId = null)
            {
                Console.WriteLine(
                    "{0,-21}{1,-51}{2,-51}{3,-51}",
                    DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
                    action,
                    entityId ?? string.Empty,
                    operationId ?? string.Empty);
            }
        }
    }   


##<a name="next-step"></a>Siguiente paso

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>¿Busca algo más?

Si este tema no contiene lo estaba esperando, falta algo o en otra forma no satisface sus necesidades, por favor, explique nos con usted mediante el subproceso Disqus a continuación.
