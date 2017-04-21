<properties 
    pageTitle="Notas de la versión de Media Services | Microsoft Azure" 
    description="Notas de la versión de servicios de multimedia" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="media" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>

# <a name="azure-media-services-release-notes"></a>Notas de la versión de Azure Media Services

Estas notas de la versión resumen los cambios de los problemas conocidos y versiones anteriores.

>[AZURE.NOTE] Queremos escuchar de nuestros clientes y centrarse en la solución de problemas que le afectan. Para informar sobre un problema o preguntas, publicar en el [Foro de MSDN de Azure Media Services].


##<a id="issues"></a>Problemas conocidos actualmente

### <a id="general_issues"></a>Servicios multimedia de problemas generales

Problema|Descripción
---|---
En la API de REST no se proporcionan varios encabezados HTTP comunes.|Si se desarrollan aplicaciones Media Services con la API de REST, verá que algunos campos de encabezado HTTP comunes (incluidos cliente ID solicitud, identificador de la solicitud y solicitud de identificador retorno de cliente) no son compatibles. Los encabezados se agregará en una futura actualización.
No se permite la codificación de porcentaje.|Media Services usa el valor de la propiedad IAssetFile.Name al generar direcciones URL para la transmisión de contenido (por ejemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, no se permite la codificación de porcentaje. El valor de la propiedad **nombre** no puede tener cualquiera de los siguientes [caracteres por ciento codificación-reservados](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Además, sólo puede haber una '.' para la extensión de nombre de archivo.
El método ListBlobs que forma parte de Azure almacenamiento SDK versión 3.x se produce un error.|Media Services genera direcciones URL de SA basándose en la versión de [2012-02-12](http://msdn.microsoft.com/library/azure/dn592123.aspx) . Si desea usar el SDK de almacenamiento de Azure para blobs de lista en un contenedor de blob, use el método de [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) que forma parte de la versión de Azure almacenamiento SDK 2.x. El método ListBlobs que forma parte de la versión de Azure almacenamiento SDK 3.x se producirá un error.
Media Services mecanismo de limitación restringe el uso de recursos para las aplicaciones que solicitar excesivo al servicio. El servicio puede devolver el código de estado HTTP servicio no disponible (503).|Para obtener más información, vea la descripción del código de estado HTTP 503 en el tema de [Códigos de Error de Azure Media Services](http://msdn.microsoft.com/library/azure/dn168949.aspx) .
Al consultar entidades, hay un límite de 1000 entidades devuelto a la vez porque público v2 resto los límites de los resultados de la consulta a los resultados de 1000. | Debe usar **Saltar** y **tomar** (. NET) o **superior** (REST) como se describe en [este ejemplo .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) y [este ejemplo API de REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
Algunos clientes pueden provenir a través de un problema de etiqueta de repetición en el manifiesto Smooth Streaming.|Para obtener más información, consulte [esta](media-services-deliver-content-overview.md#known-issues) sección.
Objetos de Azure Media Services .NET SDK no pueden ser serie y como resultado no funcionan con Azure almacenamiento en caché.|Si intenta serializar el objeto AssetCollection SDK para agregarlo a Azure almacenamiento en caché, se produce una excepción.
Errores de trabajos de codificación con una cadena de mensaje "fase: DownloadFile. Código: System.NullReferenceException ".|El flujo de trabajo de codificación típico es cargar archivos de vídeo de entrada a un activo de entrada y a continuación, enviar uno o más trabajos de codificación de entrada activo, sin modificar aún más que la entrada de activos. Sin embargo, si se modifica la entrada activo (por ejemplo, agregar o eliminar o cambiar el nombre de archivos en el activo), trabajos posteriores pueden fallar con un error DownloadFile. La solución consiste en eliminar la entrada activo y volver a cargar los archivos de entrada a un nuevo activo. 

##<a id="rest_version_history"></a>Historial de versiones de la API de REST

Para obtener información sobre el historial de versiones de la API de REST de servicios de medios, vea [Referencia de la API de REST de Azure Media Services].

##<a id="july_changes16"></a>Lanzamiento de julio de 2016

###<a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Actualizaciones de manifiestos archivo (*. ISM) generado mediante la codificación de tareas

Cuando se envía una tarea de codificación Media Encoder estándar o Codificador multimedia de Azure, la tarea de codificación genera un [archivo manifiesto streaming](media-services-deliver-content-overview.md) (* .ism) archivo en el resultado activo. Con la última versión de servicio, la sintaxis de este archivo manifiesto streaming se ha actualizado.

>[AZURE.NOTE]La sintaxis de la transmisión archivo manifiesto (.ism) está reservada para uso interno y está sujeto a cambios en futuras versiones. No modifique o manipular el contenido de este archivo.

###<a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Un nuevo manifiesto de cliente (*. Archivo ISMC) se genera en el resultado activo cuando una tarea de codificación envía uno o más archivos MP4

Comenzando con la última versión de servicio, después de la finalización de una tarea de codificación que genera uno más archivos MP4, el resultado activo también contendrá un archivo de manifiesto (*.ismc) transmisiones de cliente. El archivo .ismc le ayuda a mejorar el rendimiento de transmisión dinámica. 

>[AZURE.NOTE]La sintaxis del archivo de manifiesto (.ismc) del cliente está reservada para uso interno y está sujeto a cambios en futuras versiones. No modifique o manipular el contenido de este archivo.

Para obtener más información, consulte [este](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blog.

### <a name="known-issues"></a>Problemas conocidos

Algunos clientes pueden provenir aross un problema de etiqueta de repetición en el manifiesto Smooth Streaming. Para obtener más información, consulte [esta](media-services-deliver-content-overview.md#known-issues) sección.

##<a id="apr_changes16"></a>Versión de 2016 de abril

### <a name="azure-media-analytics"></a>Análisis de multimedia de Azure

Azure Services multimedia presentó Azure Media análisis eficaces inteligencia de vídeo. Para obtener información detallada, vea [Introducción a Azure Media Services análisis](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (vista previa)

Azure Media Services permite cifrar dinámicamente su HTTP Live transmisión (HLS) contenido con FairPlay de Apple. También puede usar el servicio de entrega de AMS licencias para proporcionar licencias FairPlay a clientes. Para obtener más información, vea [usar Azure Media Services para transmitir su HLS contenido protegido con FairPlay de Apple ](media-services-protect-hls-with-fairplay.md).
  
##<a id="feb_changes16"></a>Lanzamiento de febrero de 2016

La última versión de Azure Media Services SDK para .NET (3.5.3) contiene una corrección de errores de Widevine relacionada. Era el problema: no se pudo reutilizarse AssetDeliveryPolicy para varios activos cifrados con Widevine. Como parte de esta corrección del error se agregó la siguiente propiedad en el SDK: **WidevineBaseLicenseAcquisitionUrl**.
    
    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
        
    };

##<a id="jan_changes_16"></a>Lanzamiento de enero de 2016

Codificación unidades reservadas de nombre para reducir la confusión con nombres de codificador.

Se cambia el nombre de las unidades reservadas codificación básico, estándar y Premium a S1, S2, y S3 reserva unidades, respectivamente.  Los clientes que usan RUs codificación básicas hoy verán S1 como etiqueta en el Portal de Azure (y en la factura), mientras estándar y Premium verá las etiquetas S2 y S3 respectivamente. 

##<a id="dec_changes_15"></a>Versión de diciembre de 2015

El equipo de Azure SDK había publicado una nueva versión del paquete de [Azure SDK para PHP](http://github.com/Azure/azure-sdk-for-php) que contiene nuevas características y actualizaciones de Microsoft Azure Media Services. En particular, el SDK de Azure Media Services para PHP ahora es compatible con las últimas características de [protección de contenido](media-services-content-protection-overview.md) : cifrado dinámico con AES y DRM (PlayReady y Widevine) con y sin restricciones de Token. También es compatible con [Codificación unidades](media-services-dotnet-encoding-units.md)de escala.

Para obtener más información, consulte:

- Blog de [Microsoft Azure Media Services SDK para PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) .
- Los siguientes [ejemplos de código](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) que le ayudarán a empezar rápidamente:
    - **vodworkflow_aes.php**: se trata de un archivo PHP que muestra cómo usar cifrado dinámico de AES de 128 y servicio de entrega de clave. Se basa en el ejemplo de .NET que se explica en [este](media-services-protect-with-aes128.md) artículo.
    - **vodworkflow_aes.php**: se trata de un archivo PHP que muestra cómo usar cifrado dinámico de PlayReady y servicio de entrega de licencia. Se basa en el ejemplo de .NET que se explica en [este](media-services-protect-with-drm.md) artículo.
    - **scale_encoding_units.php**: se trata de un archivo PHP que muestra cómo ampliar codificación unidad reservada.


##<a id="nov_changes_15"></a>Versión de noviembre de 2015

Azure Media Services ahora ofrece el servicio de entrega de licencia de Google Widevine en la nube. Para obtener más detalles, consulte [este blog de anuncio](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Consulte también [este tutorial](media-services-protect-with-drm.md) y [repositorio de GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Tenga en cuenta que Servicios de entrega de licencia Widevine proporcionados por Azure Media Services se encuentra en la vista previa. Para obtener más información, consulte [este blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

##<a id="oct_changes_15"></a>Versión de octubre de 2015

Azure Media Services (AMS) ahora está disponible en los centros de datos siguientes: sur de Brasil, India oeste, India sur y Central de India. Ahora puede usar el Portal de Azure clásico para [crear cuentas de servicio de medios](media-services-portal-create-account.md) y realizar diversas tareas descritas [aquí](https://azure.microsoft.com/documentation/services/media-services/). Sin embargo, la codificación en directo no está habilitada en estos centros de datos. Además, no todos los tipos de codificación reservado unidades están disponibles en estos centros de datos.

- Sur de Brasil: Solo están disponibles los estándar y unidades reservado codificación básicas
- India oeste, India sur y Central de India: están disponibles solo codificación reservado unidades básicas


##<a id="september_changes_15"></a>Versión de septiembre de 2015 

- AMS ahora ofrece la capacidad de proteger vídeo a la carta (VOD) y secuencias en directo con tecnología Widevine Modular DRM. Puede usar los siguientes asociados de servicios de entrega que le ayudarán a ofrecer Widevine licencias: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Para obtener más información, consulte [este blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

    Puede usar [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (comenzando por la versión 3.5.1) o API de REST para configurar su AssetDeliveryConfiguration para usar Widevine.  

- AMS agregado Apple ProRes vídeos. Ahora puede cargar los archivos de vídeos de origen de QuickTime que use Apple ProRes u otros códecs. Para obtener más información, consulte [este blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- Ahora puede usar Media Encoder estándar para hacer la extracción de archivo subtipo de recorte y directo. Para obtener más información, consulte [este blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- Se han realizado las siguientes actualizaciones de filtrado: 

    - Ahora puede utilizar el formato Apple HTTP Live transmisión (HLS) con filtro solo de audio. Esta actualización le permite quitar control solo de audio especificando (solo de audio = false) en la dirección URL.
    - Al definir filtros para los activos, ahora tienen la capacidad para combinar varios filtros (hasta 3) en una única dirección URL.

    Para obtener más información, consulte [este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

- AMS admite ahora-marcos en HLS v4. Compatibilidad con el marco optimiza las operaciones de avance rápido y rebobinar. De forma predeterminada, todos los resultados de v4 HLS incluyen el marco reproducción (EXT-X-I-FRAME-STREAM-INF).
 
    Para obtener más información, consulte [este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

##<a id="august_changes_15"></a>Versión de agosto de 2015

- SDK de servicios de Azure Media para las versiones de Java V0.8.0 y nuevos ejemplos ahora están disponibles. Para obtener más información, consulte:

    - [Entrada de blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
    - [Repositorio de ejemplos de Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Actualización de Reproductor Azure con soporte técnico de secuencia de audio múltiple. Para obtener más información, consulte:
    - [Entrada de blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Lanzamiento de julio de 2015

- Anuncio de la disponibilidad general de Media Encoder estándar. Para obtener más información, vea [esta entrada de blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

    Media Encoder estándar usa preestablecidos descritas en [esta](http://go.microsoft.com/fwlink/?LinkId=618336) sección. Tenga en cuenta que cuando usa un valor preestablecido de 4k codifica, debe obtener el tipo de unidad **Premium** reservados. Para obtener más información, vea [cómo la codificación de escala](media-services-scale-media-processing-overview.md).
- Live títulos en tiempo real con Azure Media Services y el Reproductor. Para obtener más información, consulte [esta entrada de blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###<a name="media-services-net-sdk-updates"></a>Servicios multimedia .NET SDK actualizaciones

Azure Media Services .NET SDK es ahora la versión 3.4.0.0. Las siguientes funciones se agregan de esta versión:  

- Compatibilidad con archivos activos implementada. Tenga en cuenta que no se puede descargar un activo, que contiene un archivo de live.
- Compatibilidad con los filtros dinámicos implementada.
- Funcionalidad de implementada que permite a los usuarios mantener contenedor de almacenamiento al eliminar activos.
- Correcciones de errores relacionados con directivas de canales de reintento.
- Habilita **el flujo de trabajo de Media Encoder Premium**.

##<a id="june_changes_15"></a>Versión de junio de 2015

###<a name="media-services-net-sdk-updates"></a>Servicios multimedia .NET SDK actualizaciones

Azure Media Services .NET SDK es ahora la versión 3.3.0.0. Las siguientes funciones se agregan de esta versión:  

- soporte técnico para la especificación de detección de conectar OpenId
- soporte técnico para controlar la conversión de teclas en el lado del proveedor de identidades. 

Si está utilizando un proveedor de identidades que expone OpenID conectar el documento de descubrimiento (como los siguientes proveedores: Azure Active Directory, Google, Salesforce), puede indicar a Azure Media Services para obtener claves de firma de validación de token JWT desde OpenID especificación de detección de conexión. 

Para obtener más información, consulte [Mediante las teclas Json Web desde la especificación de detección OpenID conectarse a trabajar con la autenticación de token JWT en Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Versión de mayo de 2015

Presentación de las siguientes características nuevas:

- [Una vista previa de codificación en directo con Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Manifiesto dinámico](media-services-dynamic-manifest-overview.md)
- [Una vista previa de procesador multimedia Hyperlapse multimedia de Azure](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Versión de abril de 2015

 ###<a name="general-media-services-updates"></a>Actualizaciones de servicios de Media de general

- [Anuncio de Reproductor multimedia de Azure](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- Iniciar Media Services resto 2.10 canales que están configurados para recopilar un protocolo RTMP, se crean con primario y secundario recopilar direcciones URL. Para obtener más información, vea [configuraciones de recopilación de canal](media-services-live-streaming-with-onprem-encoders.md#channel_input)
- Actualizaciones de indizador de Media de Azure
- Soporte técnico para español
- Nuevo formato de xml de configuración

Para obtener más información, consulte [este blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###<a name="media-services-net-sdk-updates"></a>Servicios multimedia .NET SDK actualizaciones

Azure Media Services .NET SDK es ahora la versión 3.2.0.0.

Las siguientes son algunas de cliente opuestas actualizaciones:

- **Cambio destacado**: cambiado **TokenRestrictionTemplate.Issuer** y **TokenRestrictionTemplate.Audience** de un tipo de cadena.
- Actualizaciones relacionadas con la creación personalizada Reintentar directivas.
- Correcciones de errores relacionados con la carga o descarga de archivos.
- La clase **MediaServicesCredentials** ahora acepta extremo de control de acceso principal y secundario para autenticarse.



##<a id="march_changes_15"></a>Lanzamiento de marzo de 2015

### <a name="general-media-services-updates"></a>Actualizaciones de servicios de Media de general

- Media Services ahora proporciona la integración de CDN de Azure. Para admitir la integración, la propiedad **CdnEnabled** se agregó a **StreamingEndpoint**.  **CdnEnabled** puede usarse con las API de REST empezando con la versión 2,9 (para obtener más información, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)).  **CdnEnabled** pueden utilizarse con .NET SDK empezando con la versión 3.1.0.2 (para obtener más información, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Anuncio de **flujo de trabajo de Media Encoder Premium**. Para obtener más información, vea [Introducción a Premium codificación en Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).
 


##<a id="february_changes_15"></a>Lanzamiento de febrero de 2015

### <a name="general-media-services-updates"></a>Actualizaciones de servicios de Media de general

API de REST de servicios de medios es ahora la versión 2,9. Comenzando con esta versión, puede habilitar la integración de Azure CDN con transmisión extremos. Para obtener más información, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Lanzamiento de enero de 2015

### <a name="general-media-services-updates"></a>Actualizaciones de servicios de Media de general

Anuncio de disponibilidad General (GA) de protección de contenido con cifrado dinámico. Para obtener más información, vea [Azure Media Services mejora la seguridad streaming con tecnología de disponibilidad General de DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

###<a name="media-services-net-sdk-updates"></a>Servicios multimedia .NET SDK actualizaciones

Azure Media Services .NET SDK es ahora la versión 3.1.0.1.

Esta versión había marcado constructor predeterminado Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate como obsoletas. El nuevo constructor toma TokenType en como un argumento.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Versión de diciembre de 2014

###<a name="general-media-services-updates"></a>Actualizaciones de servicios de Media de general

- Algunas actualizaciones y nuevas características se agregaron al procesador multimedia de Azure indizador. Para obtener más información, consulte [Notas de la versión de Azure Media indizador versión 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Agregar una nueva API de REST que le permite actualizar codificación reservados unidades: [EncodingReservedUnitType con el resto](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- CORS agregados compatibilidad con el servicio de entrega clave.
- Se han hecho mejoras de rendimiento de la consulta a opciones de directiva de autorización.
- En el centro de datos de China, la [Dirección URL de la entrega de clave](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) es ahora por cliente (como en otros centros de datos).
- Duración de destino de agregado HLS automático. Cuando se realiza la transmisión directo, HLS siempre se empaqueta dinámicamente. De forma predeterminada, Media Services calcula automáticamente HLS segmento embalaje relación (FragmentsPerSegment) según el intervalo de fotograma clave (KeyFrameInterval), también conocida como grupo de imágenes: grupo, que se recibe desde el codificador directo. Para obtener más información, vea [trabajar con la transmisión Live de Azure Media Services].
 
###<a name="media-services-net-sdk-updates"></a>Servicios multimedia .NET SDK actualizaciones

- [Azure Media Services.NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) es ahora la versión 3.1.0.0.
- Actualizar la dependencia de .net SDK a 4.5 de .NET Framework.
- Agregar una nueva API que permite actualizar codificación unidades reservados. Para obtener más información, vea [Actualizar tipo de unidad reservado y aumentar RUs codificación mediante .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- JWT agregado (JSON Web Token) de soporte técnico para la autenticación de token. Para obtener más información, vea [autenticación de token JWT en Azure Media Services y cifrado dinámico](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Agregado desplazamientos relativos para BeginDate y ExpirationDate en la plantilla de la licencia de PlayReady.


##<a id="november_changes_14"></a>Versión de noviembre de 2014

- Media Services permite recopilar un contenido directo Smooth Streaming (FMP4) en una conexión SSL. Para recopilar sobre SSL, asegúrese de actualizar la dirección URL de ingesta a HTTPS.  Para obtener más información acerca de live streaming, vea [trabajar con la transmisión Live de Azure Media Services].
- Tenga en cuenta que en este momento no puede recopilar una secuencia de live RTMP en una conexión SSL.
- También puede difundir su contenido en una conexión SSL. Para ello, asegúrese de que la URL streaming iniciar con HTTPS.
- Tenga en cuenta que puede transmitir sólo sobre SSL si creó el extremo streaming desde el que ofrecer el contenido después de 10 de septiembre de 2014. Si la URL streaming se basa en los extremos streaming creados después de 10 de septiembre, la dirección URL contiene "streaming.mediaservices.windows.net" (el formato nuevo). Transmisiones de direcciones URL que contienen "origin.mediaservices.windows.net" (el antiguo formato) no admiten SSL. Si es la dirección URL en el antiguo formato y desea poder transmitir a través de SSL, [crear un nuevo extremo streaming](media-services-portal-manage-streaming-endpoints.md). Usar direcciones URL que se crean basándose en el nuevo extremo streaming para transmitir el contenido a través de SSL.

##<a id="october_changes_14"></a>Versión de octubre de 2014

### <a id="new_encoder_release"></a>Servicios de Media Encoder lanzamiento

Anuncio de la nueva versión de Media Services Azure Media Encoder. Con la información más reciente Azure Media Encoder sólo se cargan para GB de salida, pero en caso contrario el codificador nuevo función compatible con el codificador anterior. Para obtener más información [Detalles de precios de servicios de medios]).

### <a id="oct_sdk"></a>Media Services .NET SDK 

Media Services SDK para las extensiones de .NET es ahora la versión 2.0.0.3.

Media Services SDK para .NET es ahora la versión 3.0.0.8.

Se han realizado los cambios siguientes:

- Refactorización en clases de directivas de reintento.
- Agregar cadena de agente de usuario a los encabezados de solicitud http.
- Agregar paso de compilación de nuget restaurar.
- Solución de pruebas de escenario para usar x509 certificado de repositorio.
- Validar la configuración cuando se actualiza el canal y transmisión final.
 

### <a name="new-github-repository-to-host-media-services-samples"></a>Nuevo repositorio de GitHub a ejemplos de Media Services de host

Ejemplos se encuentran en el [repositorio de GitHub muestras de Azure Media Services](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>Versión de septiembre de 2014

Metadatos del resto de los servicios de medios son ahora la versión 2.7. Para obtener más información sobre las últimas actualizaciones REST, vea [Referencia de la API de REST de Azure Media Services].

Media Services SDK para .NET es ahora la versión 3.0.0.7
 
### <a id="sept_14_breaking_changes"></a>Cambios importantes

* Se cambió el nombre de **origen** a [StreamingEndpoint].
* Un cambio en el comportamiento predeterminado al usar el **Portal de Azure clásico** para codificar y, a continuación, publicar archivos MP4.

Anteriormente, cuando con el Portal de clásico de Azure para publicar un recurso de vídeo MP4 de un solo archivo una URL SA se crearía (SA URL le permiten descargar el vídeo de almacenamiento de blobs). Actualmente, al usar el Portal de Azure clásico para codificar y, a continuación, publicar un recurso de vídeo de un solo archivo MP4, la dirección URL generada apunta a un extremo de transmisión de Azure Media Services.  Este cambio no afecta a los vídeos MP4 cargados en Media Services y publicados sin estar codificados por Azure Media Services directamente.

Actualmente, tiene dos opciones para resolver el problema.

* Habilitar unidades streaming y empaquetado dinámico se utiliza para transmitir activo como una presentación smooth streaming. mp4.

* Crear una dirección url de SA para descargar (o reproducir cada vez) el formato. mp4. Para obtener más información acerca de cómo crear un localizador SA, consulte la [Entrega de contenido].


### <a id="sept_14_GA_changes"></a>Nuevas características o escenarios que forman parte de la versión de GA

* **Procesador multimedia indizador**. Para obtener más información, vea [Indización archivos multimedia con Azure Media indizador].

* La entidad [StreamingEndpoint] permite agregar nombres de dominio personalizado (host).

    Un nombre de dominio personalizado que se utilizará como el nombre del extremo streaming Media Services, debe agregar los nombres de host personalizado a su punto final streaming. Use el SDK de .NET API de REST de servicios multimedia para agregar nombres de host personalizado.
    
    Aplica teniendo en cuenta lo siguiente:
    
    * Debe tener la propiedad del nombre de dominio personalizado.
    
    * La propiedad del nombre de dominio debe validar Azure Media Services. Para validar el dominio, cree un CName que asigna <MediaServicesAccountId>.<parent domain> para verifydns. < zona de dns mediaservices >. 
    
    * Debe crear otro CName que asigna el nombre de host personalizado (por ejemplo, sports.contoso.com) al nombre de host de su StreamingEndpont de servicios multimedia (por ejemplo, amstest.streaming.mediaservices.windows.net).


    Para obtener más información, vea la propiedad **CustomHostNames** en el tema de [StreamingEndpoint] .

### <a id="sept_14_preview_changes"></a>Nuevas características y escenarios que forman parte de la versión preliminar pública

* Vista previa dinámica Streaming. Para obtener más información, vea [trabajar con la transmisión Live de Azure Media Services].

* Servicio de entrega clave. Para obtener más información, vea [utilizando el cifrado AES de 128 dinámico y servicio de entrega de clave].

* Cifrado AES de dinámica. Para obtener más información, vea [utilizando el cifrado AES de 128 dinámico y servicio de entrega de clave].

* Servicio de entrega de licencias PlayReady. Para obtener más información, vea [usar cifrado dinámico de PlayReady y servicio de entrega de licencia].

* Cifrado PlayReady dinámicos. Para obtener más información, vea [usar cifrado dinámico de PlayReady y servicio de entrega de licencia].

* Plantilla de licencia de PlayReady de servicios multimedia. Para obtener más información, vea [Media Services PlayReady licencia plantilla Overview].

* Almacenamiento de secuencias cifran activos. Para obtener más información, vea [Cifrar contenido de la almacenamiento de secuencias].

##<a id="august_changes_14"></a>Versión de agosto de 2014

Cuando se codifica un activo, se produce un activo de salida cuando finalice el trabajo de codificación de. Hasta esta versión, servicios de Azure Media Encoder fabrican metadatos acerca de los activos de salida. A partir de esta versión, el codificador, también genera metadatos acerca de los activos de entrada. Para obtener más información, vea los temas de [Metadatos de entrada] y [Salida de metadatos] .


##<a id="july_changes_14"></a>Lanzamiento de julio de 2014

Se han realizado las siguientes correcciones de errores de Azure Media Services empaquetador y cifrador:

* Sólo de audio se reproduce cuando transmuxing un activo archivar directo a Live transmisión HTTP: Esto se ha corregido y ahora se reproducen audio y vídeo.

* Cuando empaquetado de un activo a Live transmisión HTTP y el cifrado AES de 128 bits sobres, las secuencias empaquetadas no reproducir en dispositivos Android: este error se ha corregido y la secuencia de empaquetado se reproduce en dispositivos Android que admiten Live transmisión HTTP.

##<a id="may_changes_14"></a>Versión de mayo de 2014

### <a id="may_14_changes"></a>Actualizaciones de servicios de Media de general

Ahora puede usar [Embalaje dinámicos] a secuencia HTTP Live transmisión (HLS) v3. Para transmitir HLS v3, agregue el siguiente formato a la ruta de acceso de localizador de origen: * .ism/manifest(format=m3u8-aapl-v3). Para obtener más información, consulte el [Blog de Nick Drouin].

Embalaje dinámico ahora también es compatible con realiza HLS (v3 y v4) cifrados con PlayReady basa Smooth Streaming estática cifrado con PlayReady. Para obtener información acerca de cómo cifrar Smooth Streaming con PlayReady, vea [proteger secuencia suave con PlayReady].

### <a name="may_14_donnet_changes"></a>Servicios multimedia .NET SDK actualizaciones

Se incluyen las siguientes mejoras en la versión de Media Services .NET SDK 3.0.0.5:

* Aumentar la velocidad y resistencia para cargar o descargar activos de medios.

* Mejoras en el control de excepciones de lógica y transitorios de reintento: 

    * Lógica de detección y vuelva a intentarlo transitorias error se han mejorado para las excepciones que son causadas por consultas, guardar los cambios, cargar o descargar archivos. 
    
    * Cuando recibe excepciones de Web (por ejemplo, durante una solicitud de token de ACS), observará que los errores graves se producen errores más rápido ahora.

Para obtener más información, vea [Lógica de reintento en el SDK de servicios de medios para .NET].

##<a id="april_changes_14"></a>Versión de codificador de abril de 2014

### <a name="april_14_enocer_changes"></a>Servicios de Media Encoder actualizaciones

* Compatibilidad agregada con recopila archivos AVI creados mediante el editor nonlinear hierba mínimo EDIUS, donde es deslice suavemente el vídeo comprimido mediante códec hierba mínimo HQ/HQX. Para obtener más información, vea [Hierba mínimo anuncia EDIUS 7 transmisión a través de la nube].

* Agregado compatibilidad para especificar la convención de nomenclatura para los archivos creados por el Codificador multimedia. Para obtener más información, vea [Controlar Media servicio codificador salida nombres de archivo].

* Ha agregado compatibilidad para las superposiciones de vídeo o audio. Para obtener más información, consulte [Crear superposiciones].

* Compatibilidad agregada con desea unir varios segmentos de vídeo. Para obtener más información, vea [Panorámicas segmentos de vídeo].

* Corregir un error relacionado con transcodificación de MP4s donde el audio se ha codificado con MPEG-1 Audio layer 3 (también conocido como MP3).


##<a id="jan_feb_changes_14"></a>Versiones de enero o febrero de 2014

### <a name="jan_fab_14_donnet_changes"></a>Servicios multimedia de Azure .NET SDK 3.0.0.1, 3.0.0.2 y 3.0.0.3

Los cambios en 3.0.0.1 y 3.0.0.2 se incluyen:

* Fijos problemas relacionados con el uso de consultas LINQ con instrucciones OrderBy.

* Soluciones de prueba de división en [GitHub] en pruebas basado en unidad y basados en escenarios.

Para obtener más detalles acerca de los cambios, vea: [versiones de Azure Media Services .NET SDK 3.0.0.1 y 3.0.0.2].

Se han realizado los cambios siguientes en 3.0.0.3:

* Actualizar las dependencias de almacenamiento de Azure para usar la versión 3.0.3.0. 

* Corregir el problema de compatibilidad con versiones anteriores de 3.0. *.* versiones. 


##<a id="december_changes_13"></a>Versión de diciembre de 2013

### <a name="dec_13_donnet_changes"></a>Servicios multimedia de Azure .NET SDK 3.0.0.0

>[AZURE.NOTE] versiones de 3.0.x.x no son compatibles con las versiones de 2.4.x.x.

La última versión de SDK de servicios de Media es ahora la versión 3.0.0.0. Puede descargar el paquete más reciente de Nuget u obtener los bits de [GitHub].

Comenzando con el SDK de Media Services versión 3.0.0.0, puede volver a usar los símbolos de [Azure Active Directory Access Control Service (ACS)] . Para obtener más información, vea la sección "Volver a utilizar de Tokens de servicio de Control de acceso" en el tema de [Conectar con Media Services con el SDK de servicios de medios para .NET] .

### <a name="dec_13_donnet_ext_changes"></a>Servicios multimedia de Azure .NET SDK extensiones 2.0.0.0

Las extensiones de Azure Media Services .NET SDK es un conjunto de métodos de extensión y funciones auxiliares que simplifica el código y que sea más fácil desarrollar con Azure Media Services. Puede obtener la última bits de [Azure Media Services .NET SDK extensiones].

##<a id="november_changes_13"></a>Versión de noviembre de 2013

### <a name="nov_13_donnet_changes"></a>Servicios multimedia de Azure .NET SDK cambios

Comenzando con esta versión, el SDK de servicios de medios para .NET controladores transitorias errores que pueden producirse al realizar llamadas a la capa API de REST de servicios de medios.

##<a id="august_changes_13"></a>Versión de agosto de 2013

### <a name="aug_13_powershell_changes"></a>Cmdlets de PowerShell de servicios multimedia incluida en las herramientas del Sdk de Azure

Ahora se incluyen los siguientes cmdlets de PowerShell de servicios multimedia en las [Herramientas del sdk de azure].

* Get-AzureMediaServices 

    Por ejemplo, `Get-AzureMediaServicesAccount`.

* Nueva AzureMediaServicesAccount 

    Por ejemplo, `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* Nueva AzureMediaServicesKey 

    Por ejemplo, `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Quitar AzureMediaServicesAccount 

    Por ejemplo, `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Versión de junio de 2013

### <a name="june_13_general_changes"></a>Cambios de Azure Media Services

Los cambios que se mencionan en esta sección son actualizaciones incluidas en las versiones de junio de 2013 Media Services.

* Capacidad para vincular varias cuentas de almacenamiento a una cuenta de servicio de medios. 

    StorageAccount
    
    Asset.StorageAccountName y Asset.StorageAccount

* Posibilidad de actualizar Job.Priority. 

* Notificación relacionadas con entidades y propiedades: 

    JobNotificationSubscription
    
    NotificationEndPoint
    
    Trabajo

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Cambios de Azure Media Services .NET SDK

Los cambios siguientes se incluyen en junio de 2013 libera SDK de servicios de medios. El SDK de servicios multimedia más reciente está disponible en GitHub.

* A partir de la versión 2.3.0.0, Media Services SDK es compatible con la vinculación de almacenamiento de varias cuentas a una cuenta de Media Services. Esta característica es compatible con las siguientes API:
    
    El tipo de IStorageAccount.
    
    La propiedad Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
    
    La propiedad StorageAccount.
    
    La propiedad StorageAccountName.
    
    Para obtener más información, vea [Administrar activos de medios servicios entre varias cuentas de almacenamiento].

* Notificación API relacionadas. Comenzando con la versión 2.2.0.0 que tiene la posibilidad de escuchar las notificaciones de almacenamiento de cola de Azure. Para obtener más información, vea [Controlar notificaciones de trabajo de servicios multimedia].
    
    La propiedad Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
    
    El tipo de Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
    
    El tipo de Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
    
    El tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
    
    El tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
    
    El tipo de Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dependencia en el cliente de almacenamiento de Azure SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dependencia de OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Versión de diciembre de 2012

### <a name="dec_12_dotnet_changes"></a>Cambios de Azure Media Services .NET SDK

* IntelliSense: Agrega Falta documentación de Intellisense para muchos tipos.

* Microsoft.Practices.TransientFaultHandling.Core: Corregir un problema donde el SDK aún tiene una dependencia con una versión anterior de este ensamblado. El SDK ahora hace referencia a la versión 5.1.1209.1 de este ensamblado.

Soluciones para problemas que se encuentra en el SDK de noviembre de 2012:

* IAsset.Locators.Count: Este recuento es ahora correctamente informado sobre nuevas interfaces IAsset después de que se hayan eliminado todos los localizadores.

* IAssetFile.ContentFileSize: Se establece este valor ahora correctamente después de una carga por IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize: Ahora se puede establecer esta propiedad al crear un archivo de activos. Fue previamente de solo lectura.

* IAssetFile.Upload(filepath): Corregir un problema donde este método de carga sincrónico produce el siguiente error al cargar varios archivos al activo. El error es "el servidor no se puede autenticar la solicitud. Asegúrese de que el valor del encabezado de autorización se ha creado correctamente, incluidas la firma."

* IAssetFile.UploadAsync: Corregir un problema donde no más de 5 archivos se pueden cargar simultáneamente.

* IAssetFile.UploadProgressChanged: Este evento ahora se proporciona el SDK de.

* IAssetFile.DownloadAsync (cadena, BlobTransferClient, ILocator, CancellationToken): ahora se proporciona la sobrecarga de este método.

* IAssetFile.DownloadAsync: Corregir un problema donde se pueden descargar no más de 5 archivos al mismo tiempo.

* IAssetFile.Delete(): Corregir un problema donde llamar a delete puede iniciar una excepción si se ha cargado ningún archivo para el IAssetFile.

* Trabajos: Fijo un problema donde encadenar una "MP4 a tarea secuencias suave" con una "PlayReady protección tarea" mediante una plantilla de trabajo no se crearía cualquier tarea en absoluto.

* EncryptionUtils.GetCertificateFromStore(): Este método ya no produce una excepción de referencia null debido a errores de encuentra el certificado en función de los problemas de configuración de certificados.

##<a id="november_changes_12"></a>Versión de noviembre de 2012

Los cambios que se mencionan en esta sección fueron actualizaciones incluidas en el de 2012 de noviembre (versión 2.0.0.0) SDK. Estos cambios pueden requerir cualquier código escrito para la versión preliminar de junio de 2012 SDK suelte para modificar o volver a escribir.

* Activos
    
    IAsset.Create(assetName) es la función de creación solo activo. IAsset.Create ya no se carga archivos como parte de la llamada al método. Usar IAssetFile para cargar.
    
    El método de IAsset.Publish y el valor de la enumeración AssetState.Publish se han quitado del SDK de servicios. Cualquier código que se basa este valor debe ser volver a escribir.

* FileInfo

    Esta clase se ha quitado y sustituido por IAssetFile.

    IAssetFiles

    IAssetFile reemplaza FileInfo y tiene un comportamiento diferente. Para utilizarla, crear una instancia del objeto IAssetFiles, seguido de una carga de archivos mediante el SDK de servicios de medios o almacenamiento de Azure. Pueden utilizar las sobrecargas IAssetFile.Upload siguientes:

    * IAssetFile.Upload(filePath): Método sincrónico que bloquea el subproceso y se recomienda cuando se carga un único archivo.
    
    * IAssetFile.UploadAsync (ruta del archivo, blobTransferClient, localizador, cancellationToken): un método asincrónico. Éste es el mecanismo de carga preferido. 

    Error conocido: usar cancellationToken hecho cancelará la carga; Sin embargo, el estado de cancelación de las tareas puede ser cualquier número de Estados. Debe detectar y controlar las excepciones correctamente.

* Localizadores
    
    Se han quitado las versiones específicas de origen. El contexto de SA específico. Locators.CreateSasLocator (activo, accessPolicy) se marcará obsoletas o quita la disponibilidad general. Consulte la sección de localizadores en nueva funcionalidad para comportamiento actualizado.


##<a id="june_changes_12"></a>Versión preliminar de junio de 2012

Las siguientes funciones era nueva en la versión de noviembre de SDK.

* Eliminación de entidades

    IAsset, IAssetFile, ILocator, IAccessPolicy, objetos IContentKey ahora se eliminan en el nivel de objeto, es decir, IObject.Delete(), en lugar de requerir una eliminación de la colección, que es cloudMediaContext.ObjCollection.Delete(objInstance).

* Localizadores

    Localizadores ahora deben crearse con el método CreateLocator y use los valores de enumeración LocatorType.SAS o LocatorType.OnDemandOrigin como argumento para el tipo específico de localizador que desea crear.

    Nuevas propiedades se agregaron a localizadores para que sea más fácil obtener a utilizable URI para el contenido. Este cambio de diseño de localizadores se pretendía proporcionan más flexibilidad para uso futuro de terceros y aumente la facilidad de uso para las aplicaciones cliente de medios.

* Compatibilidad del método asincrónica

    Compatibilidad asincrónica se ha agregado a todos los métodos.


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Foro de MSDN de servicios multimedia de Azure]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Referencia de la API de REST de servicios multimedia de Azure]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Los detalles de precios de servicios multimedia]: http://azure.microsoft.com/pricing/details/media-services/
[Entrada de metadatos]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadatos de salida]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Entrega de contenido]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indización de archivos multimedia con indizador multimedia de Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Trabajar con Azure Media Services Live Streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Usar cifrado dinámico de AES de 128 y servicio de entrega clave]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Usar cifrado PlayReady dinámicos y servicio de entrega de licencia]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Información general sobre plantillas de licencias PlayReady de servicios multimedia]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Almacenamiento de secuencias contenido cifrado]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[Embalaje dinámico]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Proteger la secuencia suave con PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Vuelva a intentar la lógica en el SDK de Media Services para .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley anuncia EDIUS 7 transmisión a través de la nube]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Los nombres de archivo de salida de codificador de servicio de control de medios]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Crear superposiciones]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Panorámicas segmentos de vídeo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Versiones de Azure Media Services .NET SDK 3.0.0.1 y 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Servicio de Control de acceso de Azure Active Directory (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Conectar con Media Services con los servicios multimedia SDK para .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Servicios multimedia de Azure .NET SDK extensiones]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[herramientas del sdk de Azure]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Administración de medios servicios activos entre varias cuentas de almacenamiento]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Notificaciones de trabajo de servicios de administración de medios]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 
