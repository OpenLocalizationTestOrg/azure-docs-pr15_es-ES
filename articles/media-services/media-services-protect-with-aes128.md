<properties
    pageTitle="Usar cifrado dinámico de AES de 128 y servicio de entrega clave | Microsoft Azure"
    description="Microsoft Azure Media Services le permite proporcionar su contenido cifrado con claves de cifrado AES de 128 bits. Media Services también proporciona el servicio de entrega de clave que ofrece las claves de cifrado a los usuarios autorizados. Este tema muestra cómo cifrar con AES de 128 y utilizar el servicio de entrega clave dinámicamente."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/24/2016"
    ms.author="juliako"/>

#<a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>Usar cifrado dinámico de AES de 128 y servicio de entrega clave

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-aes128.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Información general

>[AZURE.NOTE] Vea [este](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) vídeo para obtener información general sobre cómo proteger el contenido multimedia con cifrado AES.

Microsoft Azure Media Services le permite proporcionar Http-Live-transmisión (HLS) y secuencias suaves cifradas con cifrado estándar avanzado (AES) (con claves de cifrado de 128 bits). Media Services también proporciona el servicio de entrega de clave que ofrece las claves de cifrado a los usuarios autorizados. Si desea que Media Services cifrar un activo, debe asociar una clave de cifrado al activo y también configurar directivas de autorización de la clave. Cuando se solicita una secuencia con un Reproductor, Media Services usa la clave especificada dinámicamente cifrar el contenido que usa cifrado AES. Para descifrar la secuencia, el Reproductor solicitará la clave del servicio de entrega clave. Para decidir si el usuario está autorizado para obtener la clave, el servicio evalúa las directivas de autorización especificada para la clave.

Media Services admite varias formas de autenticar a los usuarios realizar las solicitudes clave. La directiva de autorización de clave de contenido podría tener una o más restricciones de autorización: abrir o token de restricción. Se adjuntará la directiva token restringido por un token emitido por una Token servicio seguro (STS). Media Services admite tokens en los [Símbolos de Web Simple](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) y formato [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obtener más información, vea [Configurar la directiva de autorización de la clave de contenido](media-services-protect-with-aes128.md#configure_key_auth_policy).

Para aprovechar las ventajas de cifrado dinámico, debe tener un activo, que contiene un conjunto de archivos de velocidad múltiple MP4 o velocidad de entre varios archivos de origen Smooth Streaming. También debe configurar la directiva de entrega para el activo (que se describe más adelante en este tema). A continuación, según el formato especificado en la dirección URL streaming, el servidor de transmisión de On Demand se asegurará de que se ha entregado en el protocolo que ha elegido la secuencia. Como resultado, solo tiene que almacenar y de pago para los archivos en formato de almacenamiento único y servicios multimedia generará y servir la respuesta adecuada en función de las solicitudes de un cliente.

En este tema sería útil para los desarrolladores que funcionan en aplicaciones que ofrecen medios protegidos. El tema muestra cómo configurar el servicio de entrega clave con las directivas de autorización para que solo los clientes autorizados podrían recibir las claves de cifrado. También se muestra cómo usar cifrado dinámico.

>[AZURE.NOTE]Para empezar a usar cifrado dinámico, primero debe obtener al menos una unidad de escala (también conocido como una unidad de transmisión). Para obtener más información, vea [cómo ajustar el tamaño de un servicio de medios](media-services-portal-manage-streaming-endpoints.md).

##<a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Cifrado de AES de 128 dinámico y flujo de trabajo de servicio de entrega clave

Los siguientes son los pasos generales que debe realizar para cifrar los activos con AES, usando el servicio de entrega clave Media Services y también usa cifrado dinámico.

1. [Crear un activo y cargar archivos en el activo](media-services-protect-with-aes128.md#create_asset).
1. [Codificar activo que contiene el archivo a la velocidad de adaptación que establecer MP4](media-services-protect-with-aes128.md#encode_asset).
1. [Crear una clave de contenido y asociarlo con el codificado activo](media-services-protect-with-aes128.md#create_contentkey). En servicios de medios, la clave de contenido contiene la clave de cifrado del activo.
1. [Configurar la directiva de autorización de la clave de contenido](media-services-protect-with-aes128.md#configure_key_auth_policy). La directiva de autorización de clave de contenido debe ser configurada por el usuario y cumplen por el cliente en el orden de la clave de contenido se ha entregado al cliente.
1. [Configurar la directiva de entrega de un activo](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Incluye la configuración de directiva de entrega: dirección URL de adquisición y Vector de inicialización (IV) clave (AES de 128 requiere el mismo IV de proporcionarse al cifrar y descifrar), protocolo de entrega (por ejemplo, MPEG guión, HLS, HDS, Smooth Streaming o todos), el tipo de cifrado dinámico (por ejemplo, sobres o sin cifrado dinámico).

Puede aplicar directivas diferentes para cada protocolo en el mismo activo. Por ejemplo, puede aplicar cifrado PlayReady suave o guión y AES sobre a HLS. Todos los protocolos que no se definen en una directiva de entrega (por ejemplo, agregar una directiva que especifica solo HLS como el protocolo) se bloqueará transmisión. La excepción es si no tiene ninguna directiva de entrega de activos definida en absoluto. A continuación, se le permitirá todos los protocolos en texto sin cifrar.

1. [Crear un localizador de petición](media-services-protect-with-aes128.md#create_locator) para obtener una dirección URL streaming.

El tema también muestra [cómo una aplicación de cliente puede solicitar una clave del servicio de entrega clave](media-services-protect-with-aes128.md#client_request).

Encontrará de .NET [ejemplo](media-services-protect-with-aes128.md#example) completo al final del tema.

La imagen siguiente muestra el flujo de trabajo que se indica más arriba. Aquí se utiliza el token para la autenticación.

![Proteger con AES de 128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

El resto de este tema proporciona una explicación detallada, ejemplos de código y vínculos a temas que muestran cómo conseguir las tareas descritas.

##<a name="current-limitations"></a>Limitaciones actuales

Si agrega o actualizar la directiva de entrega de su activo, debe eliminar un localizador existente (si existe) y crear un nuevo localizador.

##<a id="create_asset"></a>Crear un activo y cargar archivos en el de activos

Para administrar, codificar y transmita los vídeos, primero debe cargar el contenido en Microsoft Azure Media Services. Una vez cargado, el contenido está almacenado de forma segura en la nube para procesamiento y transmisión. 

Para obtener información detallada, vea [Cargar archivos a una cuenta de Media Services](media-services-dotnet-upload-files.md).

##<a id="encode_asset"></a>Codificar el recurso que contiene el archivo a la velocidad de adaptación que establecer MP4

Con cifrado dinámico todo lo que necesita es crear un activo, que contiene un conjunto de archivos de velocidad múltiple MP4 o velocidad de entre varios archivos de origen Smooth Streaming. A continuación, según el formato especificado en la convocatoria de manifiesto o fragmento, la transmisión de On Demand server se asegurará de que recibe la secuencia en el protocolo que haya elegido. Como resultado, solo tiene que almacenar y de pago para los archivos en formato de almacenamiento único y servicios multimedia generará y servir la respuesta adecuada en función de las solicitudes de un cliente. Para obtener más información, vea el tema de [Información general de embalaje dinámico](media-services-dynamic-packaging-overview.md) .

Para obtener instrucciones sobre cómo codificar, vea [cómo codificar un activo mediante Media Encoder estándar](media-services-dotnet-encode-with-media-encoder-standard.md).

##<a id="create_contentkey"></a>Crear una clave de contenido y asociar con codificación activo

En servicios de medios, la clave de contenido contiene la clave que desea cifrar un activo.

Para obtener información detallada, vea [crear contenido clave](media-services-dotnet-create-contentkey.md).

##<a id="configure_key_auth_policy"></a>Configurar la directiva de autorización de la clave de contenido

Media Services admite varias formas de autenticar a los usuarios realizar las solicitudes clave. La directiva de autorización de clave de contenido debe ser configurada por el usuario y cumplirse por el cliente (Reproductor) para la clave se ha entregado al cliente. La directiva de autorización de clave de contenido podría tener una o más restricciones de autorización: abrir, token restricción o restricción de IP.

Para obtener información detallada, vea [Configurar la directiva de autorización de clave de contenido](media-services-dotnet-configure-content-key-auth-policy.md).

##<a id="configure_asset_delivery_policy"></a>Configurar la directiva de entrega de activos 

Configurar la directiva de entrega para el activo. Algunas de las cosas que la configuración de directiva de entrega de activos incluye:

- La dirección URL de adquisición de clave. 
- El Vector de inicialización (IV) que se utilizará para el cifrado de sobres. AES de 128 requiere el mismo IV de proporcionarse al cifrar y descifrar. 
- El protocolo de entrega de activos (por ejemplo, MPEG guión, HLS, HDS, Smooth Streaming o todos).
- El tipo de cifrado dinámico (por ejemplo, envolvente AES) o ningún cifrado dinámico. 

Para obtener información detallada, vea [Configurar la directiva de entrega de activos ](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Crear una petición transmisión localizador para obtener una dirección URL streaming

Debe proporcionar el usuario con la dirección URL streaming suave, guión o HLS.

>[AZURE.NOTE]Si agrega o actualizar la directiva de entrega de su activo, debe eliminar un localizador existente (si existe) y crear un nuevo localizador.

Para obtener instrucciones sobre cómo publicar un activo y generar una URL streaming, vea [crear una dirección URL streaming](media-services-deliver-streaming-content.md).

##<a name="get-a-test-token"></a>Obtener una prueba de token

Obtener una prueba de token en función de la restricción de token que se usó para la directiva de autorización de clave.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Puede usar el [Reproductor de AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para probar la secuencia.

##<a id="client_request"></a>¿Cómo cliente solicitar una clave del servicio de entrega clave?

En el paso anterior, construir la dirección URL que apunta a un archivo de manifiestos. El cliente debe extraer la información necesaria de los archivos de manifiestos streaming para poder realizar una solicitud al servicio de entrega clave.

###<a name="manifest-files"></a>Archivos de manifiestos

El cliente debe extraer la dirección URL (que también contiene contenido clave Id (niños)) el valor del archivo de manifiestos. El cliente intentará obtener la clave de cifrado desde el servicio de entrega clave. El cliente también necesita extraer el valor de IV y el uso descifrar la secuencia. El fragmento de código siguiente se muestra la <Protection> elemento del manifiesto Smooth Streaming.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

En el caso de HLS, el manifiesto raíz se divide en archivos de segmento. 

Por ejemplo, es el manifiesto raíz: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) y contiene una lista de nombres de archivo de segmento.
    
    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Si abre uno de los archivos de segmento en el editor de texto (por ejemplo, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels (514369)/Manifest(video,format=m3u8-aapl), debe contener #EXT-X-clave que indica que el archivo está cifrado.
    
    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST
    
###<a name="request-the-key-from-the-key-delivery-service"></a>Solicitar la clave del servicio de entrega clave

El código siguiente muestra cómo enviar una solicitud al servicio de entrega clave de Media Services con una clave entrega Uri (que se extrae el manifiesto) y un token (en este tema no hablar sobre cómo obtener Tokens de Web Simple de un servicio de Token seguro).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);
                
        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;
    
        var response = request.GetResponse();
     
        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }
    
        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();
    
        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
    
##<a id="example"></a>Ejemplo

1. Crear un nuevo proyecto de consola.
1. Use NuGet para instalar y agregar .NET SDK extensiones de Azure Media Services. Instalar el paquete, también instala .NET SDK de Media Services y agrega todas las dependencias necesarias.
2. Agregar archivo de configuración que contiene el nombre de cuenta y la información de clave:

    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
            
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
            
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>

1. Sobrescribir el código del archivo Program.cs con el código mostrado en esta sección.
    
    Asegúrese de actualizar las variables para que apunten a las carpetas donde se encuentran los archivos de entrada.
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace AESDynamicEncryptionAndKeyDeliverySvc
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // A Uri describing the issuer of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                // The Audience or Scope of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine();
        
                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);
        
                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();
        
                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();
        
                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
        
                        // Generate a test token based on the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
        
                        //The GenerateTestToken method returns the token without the word “Bearer” in front
                        //so you have to add it in front of the token string. 
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the bit.ly/aesplayer Flash player to test the URL 
                    // (with open authorization policy). 
                    // Paste the URL and click the Update button to play the video. 
                    //
                    string URL = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
                    Console.WriteLine();
                    Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
                    Console.WriteLine();
        
                    Console.ReadLine();
                }
        
                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }
        
                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
        
                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
        
                    Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);
        
                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
        
                    Console.WriteLine("Upload {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    locator.Delete();
                    policy.Delete();
        
                    return inputAsset;
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Create a task with the encoding details, using a string preset.
                    // In this case "H264 Multiple Bitrate 720p" preset is used.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        TaskOptions.None);
        
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.StorageEncrypted);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
                static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
                {
                    // Create envelope encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.EnvelopeEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy             
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("Open Authorization Policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                        new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "HLS Open Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null // no requirements needed for HLS
                                };
        
                    restrictions.Add(restriction);
        
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                        "policy",
                        ContentKeyDeliveryType.BaselineHttp,
                        restrictions,
                        "");
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("HLS token restricted authorization policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                            new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                            new ContentKeyAuthorizationPolicyRestriction
                            {
                                Name = "Token Authorization Policy",
                                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                                Requirements = tokenTemplateString
                            };
        
                    restrictions.Add(restriction);
        
                    //You could have multiple options 
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                            "Token option for HLS",
                            ContentKeyDeliveryType.BaselineHttp,
                            restrictions,
                            null  // no key delivery data is needed for HLS
                            );
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
        
                    return tokenTemplateString;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        
                    string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));
            
                    // When configuring delivery policy, you can choose to associate it
                    // with a key acquisition URL that has a KID appended or
                    // or a key acquisition URL that does not have a KID appended  
                    // in which case a content key can be reused. 

                    // EnvelopeKeyAcquisitionUrl:  contains a key ID in the key URL.
                    // EnvelopeBaseKeyAcquisitionUrl:  the URL does not contains a key ID

                    // The following policy configuration specifies: 
                    // key url that will have KID=<Guid> appended to the envelope and
                    // the Initialization Vector (IV) to use for the envelope encryption.
                    
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                    {
                                {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()}
                    };
        
                    IAssetDeliveryPolicy assetDeliveryPolicy =
                        _context.AssetDeliveryPolicies.Create(
                                    "AssetDeliveryPolicy",
                                    AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                                    AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                                    assetDeliveryPolicyConfiguration);
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
                    Console.WriteLine();
                    Console.WriteLine("Adding Asset Delivery Policy: " +
                        assetDeliveryPolicy.AssetDeliveryPolicyType);
                }
        
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                EndsWith(".ism")).
                                                FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);
        
                    // Create a locator to the streaming content on an origin. 
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));
        
                    // Create a URL to the manifest file. 
                    return originLocator.Path + assetFile.Name;
                }
        
                static private string GenerateTokenRequirements()
                {
                    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
        
                    template.PrimaryVerificationKey = new SymmetricVerificationKey();
                    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                    template.Audience = _sampleAudience.ToString();
                    template.Issuer = _sampleIssuer.ToString();
        
                    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
        
                    return TokenRestrictionTemplateSerializer.Serialize(template);
                }
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int size)
                {
                    byte[] randomBytes = new byte[size];
                    using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(randomBytes);
                    }
        
                    return randomBytes;
                }
            }
        }


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
