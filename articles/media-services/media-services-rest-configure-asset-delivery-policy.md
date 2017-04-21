<properties 
    pageTitle="Configuración de directivas de entrega de activos con la API de REST de servicios multimedia | Microsoft Azure" 
    description="Este tema muestra cómo configurar las directivas de entrega de activos diferentes con la API de REST de servicios de medios." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"  
    ms.author="juliako"/>

#<a name="configuring-asset-delivery-policies"></a>Configuración de directivas de entrega de activos

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Si piensa ofrecer a dinámicamente cifrados activos, uno de los pasos del flujo de trabajo de la entrega de contenido Media Services está configurando las directivas de entrega de activos. La directiva de entrega activos indica Media Services cómo desea para que su activo se ha entregado: en la transmisión de protocolo debe su activo dinámicamente empaquetar (por ejemplo, MPEG guión, HLS, Smooth Streaming o todos), si no desea cifrar dinámicamente su activo y cómo se (sobres o cifrado comunes).

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
- Si tiene un activo con un localizador de transmisión por secuencias existente, no puede vincular una nueva directiva activo, desvincular una directiva existente del activo o actualizar una directiva de entrega asociada con el activo.  Primero deberá quitar el localizador streaming, ajustar las directivas y, a continuación, vuelva a crear el localizador streaming.  Puede usar el mismo locatorId cuando vuelva a crear el localizador streaming pero debe asegurarse de que no provocar problemas para clientes, ya que puede almacenarse en caché de contenido por el origen o una CDN descendente.

>[AZURE.NOTE] Cuando se trabaja con la API de REST de servicios de medios, se aplican las consideraciones siguientes:
>
>Al acceder a las entidades de Media Services, debe establecer los campos de encabezado específico y los valores en las solicitudes HTTP. Para obtener más información, consulte [configuración de desarrollo de API de REST de servicios de multimedia](media-services-rest-how-to-use.md).

>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de servicios de medios. Debe realizar las siguientes llamadas para el nuevo URI tal como se describe en [Conectar con Media Services con la API de REST](media-services-rest-connect-programmatically.md).


##<a name="clear-asset-delivery-policy"></a>Directiva de entrega de borrar activo

###<a id="create_asset_delivery_policy"></a>Crear directivas de entrega de activos
La solicitud HTTP siguiente crea una directiva de entrega de activos que especifica para no aplicar cifrado dinámico y entregar la secuencia en cualquiera de los siguientes protocolos: protocolos guión MPEG, HLS y Smooth Streaming. 

Para obtener información sobre qué valores puede especificar al crear un AssetDeliveryPolicy, vea la sección [tipos utilizan al definir AssetDeliveryPolicy](#types) .   


Solicitar:
      
    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net
    
    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Respuesta:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT
    
    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}
    
###<a id="link_asset_with_asset_delivery_policy"></a>Activo de vínculo con la directiva de entrega de activos

La siguiente solicitud HTTP vínculos activo especificado en la directiva de entrega de activos para.

Solicitar:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net
    
    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Respuesta:

    HTTP/1.1 204 No Content


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Directiva de entrega de activos DynamicEnvelopeEncryption 

###<a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Crear clave de contenido del tipo EnvelopeEncryption y vincularlo a activo

Al especificar directiva de entrega DynamicEnvelopeEncryption, debe asegurarse de que vincular el activo a una clave de contenido del tipo EnvelopeEncryption. Para obtener más información, vea: [crear una clave de contenido](media-services-rest-create-contentkey.md)).


###<a id="get_delivery_url"></a>Obtener la dirección URL de entrega

Obtener la dirección URL de entrega para el método de entrega especificado de la clave de contenido que creó en el paso anterior. Un cliente utiliza la dirección URL devuelta para solicitar una clave AES o un PlayReady licencia en orden de reproducción de contenido protegido.

Especifique el tipo de la dirección URL para obtener en el cuerpo de la solicitud HTTP. Si va a proteger el contenido con PlayReady, solicitar una dirección URL de adquisición de licencia de Media Services PlayReady, usando 1 para la keyDeliveryType: {"keyDeliveryType": 1}. Si va a proteger el contenido con el cifrado de sobres, solicitar una dirección URL de adquisición clave especificando 2 para keyDeliveryType: {"keyDeliveryType": 2}.

Solicitar:
    
    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21
    
    {"keyDeliveryType":2}

Respuesta:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


###<a name="create-asset-delivery-policy"></a>Crear directivas de entrega de activos

La siguiente solicitud HTTP crea **AssetDeliveryPolicy** que está configurado para aplicar cifrado envolvente dinámica (**DynamicEnvelopeEncryption**) para el protocolo **HLS** (en este ejemplo, otros protocolos se bloqueará transmisión). 


Para obtener información sobre qué valores puede especificar al crear un AssetDeliveryPolicy, vea la sección [tipos utilizan al definir AssetDeliveryPolicy](#types) .   

Solicitar:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}

    
Respuesta:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


###<a name="link-asset-with-asset-delivery-policy"></a>Activo de vínculo con la directiva de entrega de activos

Vea [activo de vínculo con la directiva de entrega de activos](#link_asset_with_asset_delivery_policy)

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Directiva de entrega de activos DynamicCommonEncryption 

###<a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Crear clave de contenido del tipo CommonEncryption y vincularlo a activo

Al especificar directiva de entrega DynamicCommonEncryption, debe asegurarse de que vincular el activo a una clave de contenido del tipo CommonEncryption. Para obtener más información, vea: [crear una clave de contenido](media-services-rest-create-contentkey.md)).


###<a name="get-delivery-url"></a>Obtener la dirección URL de entrega

Obtener la dirección URL de entrega para el método de entrega de PlayReady de la clave de contenido que creó en el paso anterior. Un cliente utiliza la dirección URL devuelta para solicitar una licencia de PlayReady en orden para reproducir el contenido protegido. Para obtener más información, vea [Obtener la URL de entrega](#get_delivery_url).

###<a name="create-asset-delivery-policy"></a>Crear directivas de entrega de activos

La siguiente solicitud HTTP crea **AssetDeliveryPolicy** que está configurado para aplicar cifrado comunes dinámico (**DynamicCommonEncryption**) para el protocolo **Smooth Streaming** (en este ejemplo, otros protocolos se bloqueará transmisión). 

Para obtener información sobre qué valores puede especificar al crear un AssetDeliveryPolicy, vea la sección [tipos utilizan al definir AssetDeliveryPolicy](#types) .   


Solicitar:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Si desea proteger su contenido mediante DRM Widevine, actualice los valores de AssetDeliveryConfiguration para usar WidevineLicenseAcquisitionUrl (que tiene el valor de 7) y especifique la dirección URL de un servicio de entrega de licencia. Puede usar los siguientes asociados de AMS que le ayudarán a ofrecer Widevine licencias: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Por ejemplo: 
 
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

>[AZURE.NOTE]Al cifrar con Widevine, sólo sería capaz de ofrecer con guión. Asegúrese de especificar guión (2) en el protocolo de entrega de activos.
  
###<a name="link-asset-with-asset-delivery-policy"></a>Activo de vínculo con la directiva de entrega de activos

Vea [activo de vínculo con la directiva de entrega de activos](#link_asset_with_asset_delivery_policy)


##<a id="types"></a>Tipos de usan al definir AssetDeliveryPolicy

###<a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol 

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

###<a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

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

###<a name="contentkeydeliverytype"></a>ContentKeyDeliveryType


    /// <summary>
    /// Delivery method of the content key to the client.
    ///
    </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


###<a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

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
