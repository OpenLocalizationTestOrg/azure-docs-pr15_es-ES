<properties 
    pageTitle="Proteger el contenido con Apple FairPlay o Microsoft PlayReady HLS | Microsoft Azure" 
    description="Este tema ofrece una descripción general y muestra cómo usar Azure Media Services dinámicamente cifrar el contenido de transmisión Live HTTP (HLS) con FairPlay de Apple. También se muestra cómo usar el servicio de entrega de la licencia de Media Services para proporcionar licencias FairPlay a clientes." 
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
    ms.date="09/27/2016"
    ms.author="juliako"/>

# <a name="protect-your-hls-content-with-apple-fairplay-andor-microsoft-playready"></a>Proteger el contenido con Apple FairPlay o Microsoft PlayReady HLS

Azure Media Services le permite cifrar dinámicamente el contenido de transmisión Live HTTP (HLS) con los siguientes formatos:  

- **Tecla de borrar AES de 128 sobres** 

    El fragmento completo se cifra con el modo **CBC AES de 128** . El descifrado de la secuencia es compatible con iOS y Reproductor OSX forma nativa. Para obtener más información, consulte [este artículo](media-services-protect-with-aes128.md).

- **FairPlay de Apple** 

    Los ejemplos individuales de audio y vídeo están encriptados con el modo **CBC AES de 128** . **FairPlay transmisión** (FPS) está integrado en los sistemas operativos de dispositivos, con compatibilidad nativa en iOS y TV de Apple. Safari en OS X permite FPS con compatibilidad con la interfaz de extensiones de medios cifrado (EME).
- **Microsoft PlayReady**

La imagen siguiente muestra el flujo de trabajo de **cifrado dinámico HLS + FairPlay o PlayReady** .

![Proteger con FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Este tema muestra cómo usar Azure Media Services dinámicamente cifrar contenido HLS con FairPlay de Apple. También se muestra cómo usar el servicio de entrega de la licencia de Media Services para proporcionar licencias FairPlay a clientes.

>[AZURE.NOTE] Si también desea cifrar contenido HLS con PlayReady, debe crear una clave de contenido comunes y asociar a su activo. También debe configurar la directiva de autorización de la clave de contenido, como se describe en el tema de [cifrado comunes de PlayReady usando dinámico](media-services-protect-with-drm.md) .

    
## <a name="requirements-and-considerations"></a>Consideraciones y requisitos

- Se requiere lo siguiente cuando se usa AMS para ofrecer HLS cifrados con FairPlay y entregar FairPlay licencias.

    - Una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
    - Una cuenta de Media Services. Para crear una cuenta de Media Services, vea [Crear una cuenta](media-services-portal-create-account.md).
    - Suscríbase a [Programa de desarrollo de Apple](https://developer.apple.com/).
    - Apple requiere el propietario del contenido obtener el [paquete de implementación](https://developer.apple.com/contact/fps/). Estado de la solicitud que ya implementado KSM (módulo de seguridad de clave) con Azure Media Services y que se solicita el paquete FPS final. Habrá instrucciones en el paquete FPS final para generar certificación y obtener ASK, que se va a utilizar para configurar FairPlay. 

    - De versión de Azure Media Services .NET SDK **3.6.0** o posterior.

- En el lado de entrega clave AMS se debe establecer lo siguiente:
    - **Aplicación de certificados (CA)** - archivo .pfx que contiene la clave privada. Este archivo es creado por el cliente y se cifra con una contraseña del cliente. 
        
        Cuando el cliente configura la directiva de entrega clave, debe proporcionar la contraseña y la .pfx en formato base 64.

        Los pasos siguientes describen cómo generar un certificado pfx para FairPlay.
        
        1. Instalar OpenSSL desde https://slproweb.com/products/Win32OpenSSL.html
        
            Vaya a la carpeta donde están el certificado FairPlay y otros archivos entregados por Apple.
        
        2. Línea de comandos para convertir la cer en pem:
        
            "C:\OpenSSL-Win32\bin\openssl.exe" x509-informar der-en fairplay.cer-out fairplay out.pem
        
        3. Línea de comandos para convertir pem en pfx con la clave privada (a continuación, se solicita la contraseña del archivo pfx por OpenSSL).
        
            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-exporte - fairplay out.pfx-privatekey.pem inkey-en fairplay out.pem - passin file:privatekey-pem-pass.txt 
        
    - **Contraseña del certificado de aplicación** : contraseña del cliente para crear el archivo .pfx.
    - **Id. de contraseña de certificado de aplicación** : el cliente debe cargar la contraseña similar a cómo cargan otras teclas AMS y usar el valor de la enumeración **ContentKeyType.FairPlayPfxPassword** . Como resultado, obtendrá id AMS que esto es lo que necesitan para usar dentro de la opción de directiva de entrega clave.
    - **iv** - valor aleatorio de 16 bytes, debe coincidir con la iv en la directiva de entrega de activos. Cliente genera el IV y coloca en dos lugares: activo Directiva y clave entrega directiva opción de entrega. 
    - **Preguntar** - ASK (tecla de aplicación secreto) se recibe al generar la certificación con el portal de programadores de Apple. Cada equipo de desarrollo recibirán un único ASK. Guarde una copia de la preguntar y guardarlo en un lugar seguro. Debe configurar ASK como FairPlayAsk a Azure Media Services más adelante. 
    -  **Pida a ID** - se obtiene cuando el cliente carga ASk en AMS. El cliente debe cargar ASk con el valor de la enumeración **ContentKeyType.FairPlayASk** . Como resultado, se devuelve el identificador de AMS y esto es lo que se debe utilizar al establecer la opción de directiva de entrega clave.

- El cliente FPS deben establecer las siguientes acciones:
    - **Aplicación de certificados (CA)** - archivo.cer/.der que contiene una clave pública que usa el sistema operativo para cifrar algunos carga. AMS debe saber sobre él porque es necesaria para el Reproductor. El servicio de entrega clave descifra con la clave privada correspondiente.

- Para reproducir una secuencia cifrada FairPlay, debe obtener primero ASK real y, a continuación, generar un certificado real. Ese proceso crea todos los elementos de 3:

    -  .DER, 
    -  .pfx y 
    -  la contraseña de la .pfx.
 
- Los clientes que admiten HLS con cifrado **AES de 128 CBC** : Safari en OS X, TV Apple iOS.

##<a name="steps-for-configuring-fairplay-dynamic-encryption-and-license-delivery-services"></a>Pasos para configurar FairPlay dinámico cifrado y licencia de servicios de entrega

Los siguientes son los pasos generales que debe llevar a cabo cuando se protege los activos con FairPlay, usando el servicio de entrega de la licencia de Media Services y también usa cifrado dinámico.

1. Crear un activo y cargar archivos en el activo. 
1. Codificar el recurso que contiene el archivo a la velocidad de adaptación que establecer MP4.
1. Crear una clave de contenido y asociar con codificación activo.  
1. Configurar la directiva de autorización de la clave de contenido. Al crear la directiva de autorización de clave de contenido, debe especificar lo siguiente: 
    
    - método de entrega (en este caso, FairPlay) 
    - Configuración de opciones de directiva de FairPlay. Para obtener más información sobre cómo configurar FairPlay, vea el método ConfigureFairPlayPolicyOptions() en el ejemplo siguiente.
    
        >[AZURE.NOTE] Normalmente, que desea configurar las opciones de directiva de FairPlay solo una vez, ya que solo tendrá un conjunto de certificación y ASK.
-restricciones (abiertas o tokens), - e información específica del tipo de entrega clave que define cómo la clave se ha entregado al cliente. 
    
2. Configurar la directiva de entrega de activos. Incluye la configuración de directiva de entrega: 

    - Protocolo de entrega (HLS) 
    - el tipo de cifrado dinámico (comunes CBC cifrado), 
    - dirección URL de adquisición de licencia. 
    
    >[AZURE.NOTE]Si desea ofrecer una secuencia que se cifra con FairPlay + DRM otra, debe configurar directivas de entrega independiente 
    >
    >- Una IAssetDeliveryPolicy para configurar un guión con CENC (PlayReady + WideVine) y suave con PlayReady. 
    >- Otra IAssetDeliveryPolicy configurar FairPlay para HLS

1. Crear un localizador de petición para obtener una dirección URL streaming.

##<a name="using-fairplay-key-delivery-by-playerclient-apps"></a>Usar FairPlay clave entrega por aplicaciones cliente/Reproductor

Clientes pueden desarrollar aplicaciones de Reproductor mediante iOS SDK. Para poder reproducir contenido de FairPlay clientes tienen que implementar el protocolo de intercambio de licencia. No se especifica el protocolo de intercambio de licencias por Apple. Depende de cada aplicación cómo enviar las solicitudes de entrega clave. Los servicios de entrega clave AMS FairPlay espera SPC para dar como mensaje de entrada codificado www-form-url de la forma siguiente: 

    spc=<Base64 encoded SPC>

>[AZURE.NOTE] Reproductor Azure no admite la reproducción de FairPlay fuera del cuadro. Los clientes necesitan obtener el Reproductor de ejemplo de la cuenta de desarrollador de Apple para obtener FairPlay reproducción en MAC OS x. 
 
##<a name="streaming-urls"></a>Direcciones URL Streaming

Si su activo estaba cifrado con más de un DRM, debe usar una etiqueta de cifrado en la dirección URL streaming: (formato = 'm3u8-aapl' cifrado = 'largo y LARGOB').

Aplica teniendo en cuenta lo siguiente:

- Puede especificar el tipo de cifrado solo cero o uno.
- Tipo de cifrado no tiene que especificar la dirección URL si solo una cifrado aplicado al activo.
- Tipo de cifrado distingue mayúsculas de minúsculas.
- Pueden especificar los siguientes tipos de cifrado:  
    - **cenc**: cifrado comunes (Playready o Widevine)
    - **cbcs aapl**: Fairplay
    - **CBC**: cifrado AES de sobres.


##<a name="net-example"></a>Ejemplo de .NET


En el ejemplo siguiente se muestra la funcionalidad que se introdujo en Azure Media Services SDK para .net-versión 3.6.0 (la capacidad de usar Azure Media Services para ofrecer su contenido cifrado con FairPlay). El siguiente comando de paquete Nuget se utilizó para instalar el paquete:

    PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Crear un proyecto de consola.
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
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
        using Newtonsoft.Json;
        using System.Security.Cryptography.X509Certificates;
        
        namespace DynamicEncryptionWithFairPlay
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
        
                    IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
        
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
        
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
        
                static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
                {
                    // Create HLS SAMPLE AES encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryptionCbcs);
        
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
        
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.FairPlay,
                        restrictions,
                        FairPlayConfiguration);
        
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
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
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                               ContentKeyDeliveryType.FairPlay,
                               restrictions,
                               FairPlayConfiguration);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
                }
        
                private static string ConfigureFairPlayPolicyOptions()
                {
                    // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
                    // However, for production you must use a real ASK from Apple bound to a real prod certificate.
                    byte[] askBytes = Guid.NewGuid().ToByteArray();
                    var askId = Guid.NewGuid();
                    // Key delivery retrieves askKey by askId and uses this key to generate the response.
                    IContentKey askKey = _context.ContentKeys.Create(
                                            askId,
                                            askBytes,
                                            "askKey",
                                            ContentKeyType.FairPlayASk);
        
                    //Customer password for creating the .pfx file.
                    string pfxPassword = "<customer password for creating the .pfx file>";
                    // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
                    var pfxPasswordId = Guid.NewGuid();
                    byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
                    IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                                            pfxPasswordId,
                                            pfxPasswordBytes,
                                            "pfxPasswordKey",
                                            ContentKeyType.FairPlayPfxPassword);
        
                    // iv - 16 bytes random value, must match the iv in the asset delivery policy.
                    byte[] iv = Guid.NewGuid().ToByteArray();
        
                    //Specify the .pfx file created by the customer.
                    var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
        
                    string FairPlayConfiguration =
                        Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                            appCert,
                            pfxPassword,
                            pfxPasswordId,
                            askId,
                            iv);
        
                    return FairPlayConfiguration;
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
        
                    var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
        
                    FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
        
                    // Get the FairPlay license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
        
                    // The reason the below code replaces "https://" with "skd://" is because
                    // in the IOS player sample code which you obtained in Apple developer account, 
                    // the player only recognizes a Key URL that starts with skd://. 
                    // However, if you are using a customized player, 
                    // you can choose whatever protocol you want. 
                    // For example, "https". 

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                            {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
                        };
        
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
                        AssetDeliveryProtocol.HLS,
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


##<a name="next-steps-media-services-learning-paths"></a>Pasos siguientes: Media Services rutas de aprendizaje

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
