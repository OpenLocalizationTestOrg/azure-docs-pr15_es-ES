<properties 
    pageTitle="Configurar directivas de entrega de activos con .NET SDK | Microsoft Azure" 
    description="Este tema muestra cómo configurar las directivas de entrega de activos distintas con Azure Media Services .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako;mingfeiy"/>

#<a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurar directivas de entrega de activos con .NET SDK
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##<a name="overview"></a>Información general

Si planea activos de entrega cifrado, uno de los pasos del flujo de trabajo de la entrega de contenido Media Services está configurando las directivas de entrega de activos. La directiva de entrega activos indica Media Services cómo desea para que su activo se ha entregado: en la transmisión de protocolo debe su activo dinámicamente empaquetar (por ejemplo, MPEG guión, HLS, Smooth Streaming o todos), si no desea cifrar dinámicamente su activo y cómo se (sobres o cifrado comunes).

Este tema explica por qué y cómo crear y configurar directivas de entrega de activos.

>[AZURE.NOTE]Para poder usar embalaje dinámico y cifrado dinámico, debe asegurarse tiene al menos una escala unidades (también conocido como streaming). Para obtener más información, vea [cómo ajustar el tamaño de un servicio de medios](media-services-portal-manage-streaming-endpoints.md).
>
>Además, los activos deben contener un conjunto de velocidad adaptación MP4s o archivos de velocidad adaptación Smooth Streaming.

Puede aplicar directivas diferentes para el mismo activo. Por ejemplo, puede aplicar cifrado PlayReady cifrado AES sobre y Smooth Streaming MPEG guión y HLS. Todos los protocolos que no se definen en una directiva de entrega (por ejemplo, agregar una directiva que especifica solo HLS como el protocolo) se bloqueará transmisión. La excepción es si no tiene ninguna directiva de entrega de activos definida en absoluto. A continuación, se le permitirá todos los protocolos en texto sin cifrar.

Si desea ofrecer a un activo cifrado de almacenamiento, debe configurar Directiva de entrega del activo. Antes de que puedan transmitir su activo, el servidor de transmisión quita el cifrado de almacenamiento y transmite el contenido con la directiva de entrega especificado. Por ejemplo, para ofrecer a su activo cifrado con la clave de cifrado de sobres estándar de cifrado avanzado (AES), establezca el tipo de directiva en **DynamicEnvelopeEncryption**. Para quitar el cifrado de almacenamiento y transmita activo en texto sin cifrar, establezca el tipo de directiva en **NoDynamicEncryption**. Siguen los ejemplos que muestran cómo configurar estos tipos de directiva.

Dependiendo de cómo configurar la directiva de entrega activos podrían dinámicamente empaquetar, dinámicamente cifrar y transmita los protocolos de transmisión siguientes: secuencias Smooth Streaming, HLS, MPEG guión y HDS.

La siguiente lista muestra los formatos que use para transmitir suave, HLS, guión y HDS.

Smooth Streaming:

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG GUIÓN

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

HDS

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Para obtener instrucciones sobre cómo publicar un activo y generar una URL streaming, vea [crear una dirección URL streaming](media-services-deliver-streaming-content.md).

##<a name="considerations"></a>Consideraciones

- No puede eliminar un AssetDeliveryPolicy asociada a un activo mientras hay un localizador de petición (transmisión) para ese activo. Se recomienda quitar la directiva de activo antes de eliminar la directiva.
- No se puede crear un localizador streaming en un activo cifrado de almacenamiento cuando se establece ninguna directiva de entrega de activos.  Si el activo no está cifrado de almacenamiento, el sistema le permitirá crear un localizador y transmita activo en el claro sin una directiva de entrega de activos.
- Puede tener varias directivas de entrega de activos asociadas con un solo activo, pero solo puede especificar una forma de un determinado AssetDeliveryProtocol.  Es decir, si se intenta vincular dos directivas de entrega que especifican el protocolo AssetDeliveryProtocol.SmoothStreaming que se producirá un error porque el sistema no sabe que una que desea que se aplican cuando un cliente realiza una solicitud Smooth Streaming.
- Si tiene un activo con un localizador de transmisión por secuencias existente, no se puede vincular una nueva directiva al activo (puede desvincular una directiva existente del activo, o actualizar una directiva de entrega asociada al activo).  Primero deberá quitar el localizador streaming, ajustar las directivas y, a continuación, vuelva a crear el localizador streaming.  Puede usar el mismo locatorId cuando vuelva a crear el localizador streaming pero debe asegurarse de que no provocar problemas para clientes, ya que puede almacenarse en caché de contenido por el origen o una CDN descendente.


##<a name="clear-asset-delivery-policy"></a>Directiva de entrega de borrar activo

El siguiente método de **ConfigureClearAssetDeliveryPolicy** especifica para no aplicar cifrado dinámico y entregar la secuencia en cualquiera de los siguientes protocolos: protocolos guión MPEG, HLS y Smooth Streaming. Desea aplicar esta directiva a los activos de almacenamiento cifrado.

Para obtener información sobre qué valores puede especificar al crear un AssetDeliveryPolicy, vea la sección [tipos utilizan al definir AssetDeliveryPolicy](#types) .

ConfigureClearAssetDeliveryPolicy anular público estático (IAsset activos) {IAssetDeliveryPolicy directiva = _context. AssetDeliveryPolicies.Create ("Borrar directiva", AssetDeliveryPolicyType.NoDynamicEncryption, AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

activo. DeliveryPolicies.Add(policy); }

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Directiva de entrega de activos DynamicCommonEncryption


El siguiente método **CreateAssetDeliveryPolicy** crea **AssetDeliveryPolicy** que está configurado para aplicar cifrado comunes dinámico (**DynamicCommonEncryption**) a un protocolo de transmisión por secuencias suave (se bloquearán otros protocolos de transmisión). El método toma dos parámetros: **activo** (activo al que desea aplicar la directiva de entrega) y **IContentKey** (la clave de contenido del tipo **CommonEncryption** , para obtener más información, vea: [crear una clave de contenido](media-services-dotnet-create-contentkey.md#common_contentkey)).

Para obtener información sobre qué valores puede especificar al crear un AssetDeliveryPolicy, vea la sección [tipos utilizan al definir AssetDeliveryPolicy](#types) .


CreateAssetDeliveryPolicy estático público anular (IAsset activo, IContentKey clave) {Uri acquisitionUrl = clave. GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

Diccionario < AssetDeliveryPolicyConfigurationKey, string > assetDeliveryPolicyConfiguration = nuevo diccionario < AssetDeliveryPolicyConfigurationKey, string > {{AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()}};

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Azure Media Services también le permite agregar Widevine cifrado. En el ejemplo siguiente se muestra PlayReady y Widevine que se agrega a la directiva de entrega de activos.


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
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Al cifrar con Widevine, sólo sería capaz de ofrecer con guión. Asegúrese de especificar guión en el protocolo de entrega de activos.


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Directiva de entrega de activos DynamicEnvelopeEncryption 

El siguiente método **CreateAssetDeliveryPolicy** crea **AssetDeliveryPolicy** que está configurado para aplicar el cifrado de envolvente dinámica (**DynamicEnvelopeEncryption**) a protocolos Smooth Streaming, HLS y guión (si decide no especifica algunos protocolos, se bloqueará transmisión). El método toma dos parámetros: **activo** (activo al que desea aplicar la directiva de entrega) y **IContentKey** (la clave de contenido del tipo **EnvelopeEncryption** , para obtener más información, vea: [crear una clave de contenido](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Para obtener información sobre qué valores puede especificar al crear un AssetDeliveryPolicy, vea la sección [tipos utilizan al definir AssetDeliveryPolicy](#types) .   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
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
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Tipos de usan al definir AssetDeliveryPolicy

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


