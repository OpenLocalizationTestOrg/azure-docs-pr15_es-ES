<properties 
    pageTitle="Crear ContentKeys con .NET" 
    description="Aprenda a crear claves de contenido que proporcionan acceso seguro a los activos." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="create-contentkeys-with-net"></a>Crear ContentKeys con .NET

> [AZURE.SELECTOR]
- [REST](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)

Media Services le permite crear y ofrecer a cifrados activos. Un **ContentKey** proporciona acceso seguro a su s **activo**. 

Cuando se crea un nuevo activo (por ejemplo, antes de [cargar archivos](media-services-dotnet-upload-files.md)), puede especificar las siguientes opciones de cifrado: **StorageEncrypted**, **CommonEncryptionProtected**o **EnvelopeEncryptionProtected**. 

Al realizar activos a sus clientes, puede [configurar para que activos dinámicamente cifrado](media-services-dotnet-configure-asset-delivery-policy.md) con una de las siguientes dos cifrados: **DynamicEnvelopeEncryption** o **DynamicCommonEncryption**.

Cifrado activos tienen que estar asociadas a s **ContentKey**. En este artículo se describe cómo crear una clave de contenido.

>[AZURE.NOTE] Al crear un nuevo activo **StorageEncrypted** mediante el SDK de .NET de servicios de medios, la **ContentKey** se crea automáticamente y se vinculado al activo.

##<a name="contentkeytype"></a>ContentKeyType

Uno de los valores que debe establecer cuándo crear un contenido clave es el tipo de clave de contenido. Elija uno de los siguientes valores. 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

##<a id="envelope_contentkey"></a>Crear sobres tipo ContentKey

Fragmento de código siguiente crea una clave de contenido del tipo de cifrado de sobres. A continuación, se asocia la tecla con especificado activo.

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

        asset.ContentKeys.Add(key);

        return key;
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

llamada

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>Crear tipo común ContentKey    

Fragmento de código siguiente crea una clave de contenido del tipo de cifrado comunes. A continuación, se asocia la tecla con especificado activo.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
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
llamada

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
