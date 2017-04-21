<properties 
    pageTitle="Configurar una directiva de autorización de clave contenido mediante Media Services .NET SDK | Microsoft Azure" 
    description="Aprenda a configurar una directiva de autorización para una clave de contenido mediante Media Services .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;mingfeiy"/>



# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>Cifrado dinámico: configurar la directiva de autorización de clave de contenido

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

##<a name="overview"></a>Información general

Microsoft Azure Media Services le permite proporcionar secuencias MPEG-guión Smooth Streaming y Streaming Live HTTP (HLS) protegidas con cifrado estándar avanzado (AES) (con claves de cifrado de 128 bits) o [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS también le permite proporcionar secuencias de guión cifradas con DRM Widevine. PlayReady y Widevine se cifran por la especificación de cifrado comunes (ISO/IEC 23001 7 CENC).

Media Services también proporciona un **Servicio de entrega de la clave de la licencia o** desde la que los clientes pueden obtener claves AES o licencias de PlayReady/Widevine para reproducir el contenido cifrado.

Si desea que Media Services cifrar un activo, debe asociar una clave de cifrado (**CommonEncryption** o **EnvelopeEncryption**) al activo (como se describe [a continuación](media-services-dotnet-create-contentkey.md)) y también configurar directivas de autorización de la clave (como se describe en este artículo).

Cuando se solicita una secuencia con un Reproductor, Media Services usa la clave especificada para cifrar dinámicamente el contenido que usa cifrado AES o DRM. Para descifrar la secuencia, el Reproductor solicitará la clave del servicio de entrega clave. Para decidir si el usuario está autorizado para obtener la clave, el servicio evalúa las directivas de autorización especificada para la clave.

Media Services admite varias formas de autenticar a los usuarios realizar las solicitudes clave. La directiva de autorización de clave de contenido podría tener una o más restricciones de autorización: **Abrir** o **token** de restricción. Se adjuntará la directiva token restringido por un token emitido por una Token servicio seguro (STS). Media Services admite tokens en los **Símbolos de Web Simple** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) y formato **JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)).

Media Services no proporciona servicios de Token seguro. Puede crear a un STS personalizado o aprovechar Microsoft Azure ACS tokens de problema. El STS debe estar configurado para crear un símbolo que se ha iniciado sesión con las notificaciones de clave y problema especificadas especificada en la configuración de restricción de token (como se describe en este artículo). El servicio de entrega clave Media Services devolverá la clave de cifrado del cliente si el token es válido y las notificaciones del token coinciden con los configurados para la clave de contenido.

Para obtener más información, vea

[Autenticación de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar Azure Media Services OWIN MVC según la aplicación con Azure Active Directory y restringir el contenido entrega clave basada en notificaciones JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Usar Azure ACS tokens de problema](http://mingfeiy.com/acs-with-key-services).

###<a name="some-considerations-apply"></a>Aplicarán algunas consideraciones:

- Para poder usar embalaje dinámico y cifrado dinámico, debe asegurarse tiene al menos una unidad reservada de transmisión. Para obtener más información, vea [cómo ajustar el tamaño de un servicio de medios](media-services-portal-manage-streaming-endpoints.md).
- Los activos deben contener un conjunto de velocidad adaptación MP4s o archivos de velocidad adaptación Smooth Streaming. Para obtener más información, vea [codificar un activo](media-services-encode-asset.md).
- Cargar y codificar los activos con la opción **AssetCreationOptions.StorageEncrypted** .
- Si va a tener varias claves contenidas que requieren la misma configuración de directiva, se recomienda encarecidamente para crear una directiva de autorización única y volver a usarla con varias claves de contenido.
- El servicio de entrega de clave almacena ContentKeyAuthorizationPolicy y sus objetos relacionados (opciones de directiva y restricciones) de 15 minutos.  Si crea un ContentKeyAuthorizationPolicy y especificar que se utilice una restricción "Token", probar y a continuación, actualice la directiva de restricción "Abrir", esta acción tardará aproximadamente 15 minutos antes de la directiva cambia a la versión "Abierta" de la directiva.
- Si agrega o actualizar la directiva de entrega de su activo, debe eliminar un localizador existente (si existe) y crear un nuevo localizador.
- Actualmente, no puede cifrar HDS transmisión formato o progresivo descargas.


##<a name="aes-128-dynamic-encryption"></a>Cifrado de AES de 128 dinámico

###<a name="open-restriction"></a>Restricción abierto

Abrir restricción significa que el sistema proporcionará la clave para cualquier persona que realiza una solicitud de clave. Esta restricción puede ser útil con fines de pruebas.

En el ejemplo siguiente se crea una directiva de autorización abierto y lo agrega a la clave de contenido.

AddOpenAuthorizationPolicy anular público estático (IContentKey contentKey) {/ / crear ContentKeyAuthorizationPolicy con restricciones de apertura / / y crear la directiva de autorización de directiva IContentKeyAuthorizationPolicy = _context.
ContentKeyAuthorizationPolicies.
CreateAsync ("Directiva de autorización de abrir"). Resultado;

Lista<ContentKeyAuthorizationPolicyRestriction> restricciones = nueva lista<ContentKeyAuthorizationPolicyRestriction>();
    
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


###<a name="token-restriction"></a>Restricción de token

Esta sección describe cómo crear una directiva de autorización de clave de contenido y asociar con la clave de contenido. La directiva de autorización describe debe cumplir los requisitos de autorización para determinar si el usuario está autorizado para recibir la clave (por ejemplo, sirve la lista "clave de verificación" incluir la clave que se firmó el token con).

Para configurar la opción de restricción token, debe usar un XML para describir los requisitos de autorización del token. La configuración de restricción token XML debe cumplir el esquema XML siguiente.

####<a id="schema"></a>Esquema de restricción de token
    
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

Al configurar el **token** restringido directiva, debe especificar los parámetros de** clave de verificación**, **emisor** y **audiencia** principales. La **clave principal de comprobación **contiene la clave que se firmó el token con, **emisor** es el servicio de token seguro que emite el token. La **audiencia** (a veces denominado **ámbito**) describe el objetivo del token o el recurso el token autoriza el acceso a. El servicio de entrega clave Media Services valida que estos valores en el símbolo que coincidan con los valores de la plantilla. 

Cuando se usa el **SDK de servicios de medios para .NET**, puede usar la clase **TokenRestrictionTemplate** para generar el token de restricción.
En el ejemplo siguiente se crea una directiva de autorización con una restricción de token. En este ejemplo, el cliente tendría que presentar un token que contenga: firma clave (VerificationKey), un emisor de tokens y reclamaciones necesarias.
    
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

####<a id="test"></a>Token de prueba

Para obtener un token de prueba en función de la restricción de token que se usó para la directiva de autorización de clave, haga lo siguiente.
    
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
    
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


##<a name="playready-dynamic-encryption"></a>Cifrado PlayReady dinámicos 

Media Services le permite configurar los derechos y restricciones que desea para el tiempo de ejecución de DRM de PlayReady exigir cuando un usuario intenta reproducir contenido protegido. 

Cuando se protege el contenido con PlayReady, una de las cosas que debe especificar en la directiva de autorización es una cadena XML que define la [plantilla de la licencia de PlayReady](media-services-playready-license-template-overview.md). En el SDK de servicios de medios para .NET, las clases **PlayReadyLicenseResponseTemplate** y **PlayReadyLicenseTemplate** le ayudará a definir la plantilla de la licencia de PlayReady.

[Este tema](media-services-protect-with-drm.md) muestra cómo cifrar el contenido con **PlayReady** y **Widevine**.

###<a name="open-restriction"></a>Restricción abierto
    
Abrir restricción significa que el sistema proporcionará la clave para cualquier persona que realiza una solicitud de clave. Esta restricción puede ser útil con fines de pruebas.

En el ejemplo siguiente se crea una directiva de autorización abierto y lo agrega a la clave de contenido.

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
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
    
    
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

###<a name="token-restriction"></a>Restricción de token

Para configurar la opción de restricción token, debe usar un XML para describir los requisitos de autorización del token. La configuración de restricción token XML debe cumplir el esquema XML que se muestran en [esta](#schema) sección.
    
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();
    
        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
                
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
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


Para obtener un token de prueba en función de la restricción de token que se usó para la autorización de clave directiva consulte [esta](#test) sección. 

##<a id="types"></a>Tipos de usan al definir ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

###<a id="TokenType"></a>TokenType en

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Siguiente paso
Ahora que ha configurado la directiva de autorización de la clave de contenido, vaya al tema de [cómo configurar la directiva de entrega de activos](media-services-dotnet-configure-asset-delivery-policy.md) .
 
