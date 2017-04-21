<properties
    pageTitle="Mediante el cifrado de comunes dinámico PlayReady o Widevine | Microsoft Azure"
    description="Microsoft Azure Media Services le permite proporcionar secuencias MPEG-guión Smooth Streaming y Streaming Live Http (HLS) protegidas con Microsoft PlayReady DRM. También le permite entrega guión cifrado con DRM Widevine. Este tema muestra cómo cifrar dinámicamente con PlayReady y Widevine DRM."
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
    ms.topic="get-started-article" 
    ms.date="09/27/2016"
    ms.author="juliako"/>


#<a name="using-playready-andor-widevine-dynamic-common-encryption"></a>Uso del cifrado comunes de PlayReady o Widevine dinámico

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Microsoft Azure Media Services le permite proporcionar secuencias MPEG-guión Smooth Streaming y Streaming Live HTTP (HLS) protegidas con [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). También le permite ofrecer cifradas secuencias de guión con DRM Widevine licencias. PlayReady y Widevine se cifran por la especificación de cifrado comunes (ISO/IEC 23001 7 CENC). Puede usar [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (comenzando por la versión 3.5.1) o API de REST para configurar su AssetDeliveryConfiguration para usar Widevine.

Media Services proporciona un servicio para proporcionar PlayReady y Widevine DRM licencias para. Media Services también proporciona API que le permiten definir los derechos y restricciones que desea para el tiempo de ejecución de PlayReady o Widevine DRM exigir cuando un usuario reproduzca contenido protegido. Cuando un usuario solicita un contenido protegido con DRM, la aplicación de Reproductor solicitará una licencia del servicio de licencia AMS. El servicio de licencias de AMS emite una licencia para el Reproductor si está autorizado. Una licencia de PlayReady o Widevine contiene la clave de descifrado puede ser usada por el Reproductor del cliente descifrar y transmitir el contenido.


También puede usar los siguientes asociados de AMS que le ayudarán a ofrecer Widevine licencias: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Para obtener más información, vea: integración con [Axinom](media-services-axinom-integration.md) y [castLabs](media-services-castlabs-integration.md).

Media Services admite varias formas de autorizar a los usuarios que realicen las solicitudes clave. La directiva de autorización de clave de contenido podría tener una o más restricciones de autorización: abrir o token de restricción. Se adjuntará la directiva token restringido por un token emitido por una Token servicio seguro (STS). Media Services admite tokens en los [Símbolos de Web Simple](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) y formato [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obtener más información, vea Configurar la directiva de autorización de la clave de contenido.

Para aprovechar las ventajas de cifrado dinámico, debe tener un activo, que contiene un conjunto de archivos de velocidad múltiple MP4 o velocidad de entre varios archivos de origen Smooth Streaming. También debe configurar las directivas de entrega para el activo (que se describe más adelante en este tema). A continuación, según el formato especificado en la dirección URL streaming, el servidor de transmisión de On Demand se asegurará de que se ha entregado en el protocolo que ha elegido la secuencia. Como resultado, solo tiene que almacenar y de pago para los archivos en un formato de almacenamiento único y Media Services generará y servir la respuesta HTTP adecuada en función de cada solicitud de un cliente.

En este tema sería útil para los desarrolladores que funcionan en aplicaciones que ofrecen medios protegidos con varios DRMs, como PlayReady y Widevine. El tema muestra cómo configurar el servicio de entrega de la licencia de PlayReady con las directivas de autorización para que solo los clientes autorizados podrían recibir licencias PlayReady o Widevine. También se muestra cómo usar el cifrado de cifrado dinámico con PlayReady o Widevine DRM sobre guión.

>[AZURE.NOTE]Para empezar a usar cifrado dinámico, primero debe obtener al menos una unidad de escala (también conocido como una unidad de transmisión). Para obtener más información, vea [cómo ajustar el tamaño de un servicio de medios](media-services-portal-manage-streaming-endpoints.md).


##<a name="download-sample"></a>Descargar ejemplo

Puede descargar el ejemplo descrito en este artículo desde [aquí](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

##<a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>Configuración de cifrado comunes dinámico y servicios de entrega de licencia DRM

Los siguientes son los pasos generales que debe llevar a cabo cuando se protege los activos con PlayReady, usando el servicio de entrega de la licencia de Media Services y también usa cifrado dinámico.

1. Crear un activo y cargar archivos en el activo.
1. Codificar el recurso que contiene el archivo a la velocidad de adaptación que establecer MP4.
1. Crear una clave de contenido y asociar con codificación activo. En servicios de medios, la clave de contenido contiene la clave de cifrado del activo.
1. Configurar la directiva de autorización de la clave de contenido. La directiva de autorización de clave de contenido debe ser configurada por el usuario y cumplen por el cliente en el orden de la clave de contenido se ha entregado al cliente.

Al crear la directiva de autorización de clave de contenido, debe especificar lo siguiente: método (PlayReady o Widevine), las restricciones de entrega (abiertas o tokens) e información específica del tipo de entrega clave que define cómo la clave se ha entregado al cliente (plantilla de licencia de[PlayReady](media-services-playready-license-template-overview.md) o [Widevine](media-services-widevine-license-template-overview.md) ).
1. Configurar la directiva de entrega de un activo. Incluye la configuración de directiva de entrega: protocolo de entrega (por ejemplo, MPEG guión, HLS, HDS, Smooth Streaming o todos), el tipo de cifrado dinámico (por ejemplo, el cifrado comunes), PlayReady o la dirección URL de adquisición de licencia de Widevine.

Puede aplicar directivas diferentes para cada protocolo en el mismo activo. Por ejemplo, puede aplicar cifrado PlayReady suave o guión y AES sobre a HLS. Todos los protocolos que no se definen en una directiva de entrega (por ejemplo, agregar una directiva que especifica solo HLS como el protocolo) se bloqueará transmisión. La excepción es si no tiene ninguna directiva de entrega de activos definida en absoluto. A continuación, se le permitirá todos los protocolos en texto sin cifrar.
1. Crear un localizador de petición para obtener una dirección URL streaming.

Encontrará un ejemplo de .NET completo al final del tema.

La imagen siguiente muestra el flujo de trabajo que se indica más arriba. Aquí se utiliza el token para la autenticación.

![Proteger con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

El resto de este tema proporciona una explicación detallada, ejemplos de código y vínculos a temas que muestran cómo conseguir las tareas descritas.

##<a name="current-limitations"></a>Limitaciones actuales

Si agrega o actualizar una directiva de entrega de activos, debe eliminar el localizador asociado (si existe) y crear un nuevo localizador.

Limitación de cifrado con Widevine con Azure Media Services: actualmente, no se admiten varias teclas contenidas.

##<a name="create-an-asset-and-upload-files-into-the-asset"></a>Crear un activo y cargar archivos en el de activos

Para administrar, codificar y transmita los vídeos, primero debe cargar el contenido en Microsoft Azure Media Services. Una vez cargado, el contenido está almacenado de forma segura en la nube para procesamiento y transmisión.

Para obtener información detallada, vea [Cargar archivos a una cuenta de Media Services](media-services-dotnet-upload-files.md).

##<a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>Codificar el recurso que contiene el archivo a la velocidad de adaptación que establecer MP4

Con cifrado dinámico todo lo que necesita es crear un activo, que contiene un conjunto de archivos de velocidad múltiple MP4 o velocidad de entre varios archivos de origen Smooth Streaming. A continuación, según el formato especificado en la solicitud de manifiesto y fragmento, el servidor de transmisión de On Demand se asegurará de que recibe la secuencia en el protocolo que haya elegido. Como resultado, solo tiene que almacenar y de pago para los archivos en formato de almacenamiento único y servicios multimedia generará y servir la respuesta adecuada en función de las solicitudes de un cliente. Para obtener más información, vea el tema de [Información general de embalaje dinámico](media-services-dynamic-packaging-overview.md) .

Para obtener instrucciones sobre cómo codificar, vea [cómo codificar un activo mediante Media Encoder estándar](media-services-dotnet-encode-with-media-encoder-standard.md).


##<a id="create_contentkey"></a>Crear una clave de contenido y asociar con codificación activo

En servicios de medios, la clave de contenido contiene la clave que desea cifrar un activo.

Para obtener información detallada, vea [crear contenido clave](media-services-dotnet-create-contentkey.md).


##<a id="configure_key_auth_policy"></a>Configurar la directiva de autorización de la clave de contenido

Media Services admite varias formas de autenticar a los usuarios realizar las solicitudes clave. La directiva de autorización de clave de contenido debe ser configurada por el usuario y cumplirse por el cliente (Reproductor) para la clave se ha entregado al cliente. La directiva de autorización de clave de contenido podría tener una o más restricciones de autorización: abrir o token de restricción.

Para obtener información detallada, vea [Configurar la directiva de autorización de clave de contenido](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

##<a id="configure_asset_delivery_policy"></a>Configurar la directiva de entrega de activos 

Configurar la directiva de entrega para el activo. Algunas de las cosas que la configuración de directiva de entrega de activos incluye:

- La dirección URL de adquisición de licencia DRM. 
- El protocolo de entrega de activos (por ejemplo, MPEG guión, HLS, HDS, Smooth Streaming o todos). 
- El tipo de cifrado dinámico (en este caso, el cifrado comunes). 

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

##<a id="example"></a>Ejemplo


En el ejemplo siguiente se muestra la funcionalidad que se introdujo en Azure Media Services SDK para .net-versión 3.5.2 (en concreto, la capacidad de definir una plantilla de licencia de Widevine y solicitar una licencia de Widevine de Azure Media Services). El siguiente comando de paquete Nuget se utilizó para instalar el paquete:

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Crear un nuevo proyecto de consola.
1. Use NuGet para instalar y agregar .NET SDK de Azure Media Services.
2. Agregar referencias adicionales: System.Configuration.
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

1. Obtener al menos una unidad de transmisión del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido. Para obtener más información, vea: [configurar extremos streaming](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Sobrescribir el código del archivo Program.cs con el código mostrado en esta sección.
    
    Asegúrese de actualizar las variables para que apunten a las carpetas donde se encuentran los archivos de entrada.
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;
        
        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
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
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
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
        
                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, 
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
                     
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
        
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
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
        
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
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";
            
                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));
            
                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
            
                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
            
                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),    AssetCreationOptions.StorageEncrypted);
            
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
            
                    return job.OutputMediaAssets[0];
                }
        
        
                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {
                    
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
        
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy          
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Open",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null
                        }
                    };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("", 
                            ContentKeyDeliveryType.Widevine, 
                            restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;
        
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
                    };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
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
        
                static private string ConfigurePlayReadyLicenseTemplate()
                {
                    // The following code configures PlayReady License Template using .NET classes
                    // and returns the XML string.
        
                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
                    //It contains a field for a custom data string between the license server and the application 
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
        
                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users. 
                    //It contains the data on the content key in the license and any rights or restrictions to be 
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client) 
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
        
                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                    licenseTemplate.AllowTestDevices = true;
        
                    // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                    // It grants the user the ability to playback the content subject to the zero or more restrictions 
                    // configured in the license and on the PlayRight itself (for playback specific policy). 
                    // Much of the policy on the PlayRight has to do with output restrictions 
                    // which control the types of outputs that the content can be played over and 
                    // any restrictions that must be put in place when using a given output.
                    // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                    //then the DRM runtime will only allow the video to be displayed over digital outputs 
                    //(analog video outputs won’t be allowed to pass the content).
        
                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
                    // If the output protections are configured too restrictive, 
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.
        
                    // For example:
                    //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
        
                    responseTemplate.LicenseTemplates.Add(licenseTemplate);
        
                    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
                }
        
        
                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };
        
                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    // Get the PlayReady license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
                
                    // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
                    // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
                    // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
                    // to append /? KID =< keyId > to the end of the url when creating the manifest.
                    // As a result Widevine license acquisition URL will have KID appended twice, 
                    // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.
            
                    Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
                    UriBuilder uriBuilder = new UriBuilder(widevineUrl);
                    uriBuilder.Query = String.Empty;
                    widevineUrl = uriBuilder.Uri;
        
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
        
                        };
        
                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);
        
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
        
                }
        
                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
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
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];
        
                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }
        
                    return returnValue;
                }
            }
        }


## <a name="next-step"></a>Siguiente paso

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Vea también

[CENC con múltiples DRM y Control de acceso](media-services-cenc-with-multidrm-access-control.md)

[Configurar Widevine embalaje con AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Anuncio de servicios de entrega de licencia de Google Widevine en Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
