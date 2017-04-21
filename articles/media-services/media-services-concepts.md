<properties 
    pageTitle="Conceptos de Azure Media Services | Microsoft Azure" 
    description="Este tema ofrece una descripción general de los conceptos de servicios multimedia de Azure" 
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
    ms.date="09/19/2016"
    ms.author="juliako"/>

#<a name="azure-media-services-concepts"></a>Conceptos de Azure Media Services 

Este tema ofrece una descripción general de los conceptos más importantes de Media Services.

##<a id="assets"></a>Almacenamiento y activos

###<a name="assets"></a>Activos

Un [activo](https://msdn.microsoft.com/library/azure/hh974277.aspx) contiene archivos digitales (incluido el vídeo, audio, imágenes, colecciones de miniaturas, pistas de texto y archivos de subtítulos) y los metadatos acerca de estos archivos. Después de que los archivos digitales se cargan en un activo, se pueden usar en los servicios de medios codificación y transmisión de flujos de trabajo.

Un activo se asigna a un contenedor de blob en la cuenta de almacenamiento de Azure y se almacenan los archivos en el activo como BLOB en ese contenedor.

Cuando decida qué contenido multimedia para cargar y almacenar en un activo, se aplican las consideraciones siguientes:

- Un activo debe contener solo una único instancia de contenido multimedia. Por ejemplo, una única edición de ataque TV, película o anuncio.
- Un activo no debe contener varias copias o las modificaciones de un archivo audiovisual. Un ejemplo de un uso incorrecto de un activo sería intentar almacenar más de un ataque de TV, anuncio o varios ángulos de cámara desde un único de producción dentro de un activo. Almacenamiento de varias copias o las modificaciones de un archivo audiovisual en un activo puede provocar problemas al enviar trabajos de codificación, transmisión y proteger el envío de los activos más adelante en el flujo de trabajo.  

###<a name="asset-file"></a>Archivo de activos 
Un [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) representa un archivo de audio o vídeo real que se almacena en un contenedor de blob. Un archivo de activos siempre está asociado a un activo y un recurso puede contener uno o varios archivos. Se produce un error en la tarea de servicios de Media Encoder si un objeto de archivo activo no está asociado a un archivo digital en un contenedor de blob.

La instancia de **AssetFile** y el archivo multimedia reales son dos objetos. La instancia de AssetFile contiene metadatos acerca del archivo multimedia, mientras que el archivo multimedia contiene el contenido multimedia real.

No debe intentar cambiar el contenido de los contenedores de blob generadas por el Media Services sin usar las API de servicio de medios.

###<a name="asset-encryption-options"></a>Opciones de cifrado de activos

Según el tipo de contenido que desea cargar, almacenar y ofrecer, Media Services proporciona varias opciones de cifrado que puede elegir.

**Ninguno** No se utiliza cifrado. Este es el valor predeterminado. Tenga en cuenta que al usar esta opción su contenido no está protegido al transmitirlos o al resto de almacenamiento.

Si va a ofrecer un MP4 mediante descarga progresiva, use esta opción para cargar el contenido.

**StorageEncrypted** : Utilice esta opción para cifrar el contenido de borrar localmente mediante el cifrado AES 256 bits y luego cárguelo en el almacenamiento de Azure donde esté almacenado cifrado al resto. Activos protegidos con cifrado de almacenamiento automáticamente sin cifrar y colocados en un sistema de archivos cifrados antes de codificación y, opcionalmente, cifrados de nuevo antes de cargarlo como un nuevo activo de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos de medios de entrada de alta calidad con cifrado almacenados en disco. 

Para poder ofrecer a un activo cifrado de almacenamiento, debe configurar Directiva de entrega del activo para que Media Services sepa cómo desea entregar el contenido. Antes de que puedan transmitir su activo, el servidor de transmisión quita el cifrado de almacenamiento y transmite el contenido con la directiva de entrega especificada (por ejemplo, AES, PlayReady o sin cifrado). 

**CommonEncryptionProtected** - Use esta opción si desea cifrar (o cargar ya cifrados) contenido con cifrado comunes o DRM de PlayReady (por ejemplo, Smooth Streaming protegido con PlayReady DRM).

**EnvelopeEncryptionProtected** : Use esta opción si desea proteger (o cargar ya protegido) HTTP Live transmisión (HLS) cifrada con cifrado estándar avanzado (AES). Tenga en cuenta que si va a cargar HLS ya cifrado con AES, debe haber cifrado transformar administrador.

###<a name="access-policy"></a>Directiva de acceso 

Un [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) define permisos (como lectura, escritura y lista) y la duración de acceso a un activo. Normalmente pasa un objeto de AccessPolicy a un localizador que se utilizará para tener acceso a los archivos de un activo.


###<a name="blob-container"></a>Contenedor de BLOB

Un contenedor de blob proporciona una agrupación de un conjunto de BLOB. Contenedores de BLOB se usan en Media Services como punto de borde para el control de acceso y localizadores de firma de acceso compartido (SA) en activos. Una cuenta de almacenamiento de Azure puede contener un número ilimitado de contenedores de blob. Un contenedor puede almacenar una cantidad ilimitada de BLOB.

>[AZURE.NOTE]No debe intentar cambiar el contenido de los contenedores de blob generadas por el Media Services sin usar las API de servicio de medios.

###<a id="locators"></a>Localizadores

[Localizador](https://msdn.microsoft.com/library/azure/hh974308.aspx)s proporcionan un punto de entrada para obtener acceso a los archivos de un activo. Una directiva de acceso se utiliza para definir los permisos y la duración que un cliente tiene acceso a un determinado activo. Localizadores pueden tener una relación varios a una relación con una directiva de acceso, por ejemplo, que localizadores diferentes pueden proporcionar horas de inicio diferentes y tipos de conexión a distintos clientes mientras utiliza todas el mismo permiso y la configuración de duración; Sin embargo, debido a una restricción de directiva de acceso compartido configurar servicios de almacenamiento de Azure, no puede tener más de cinco localizadores únicos asociados a un determinado activo a la vez. 

Media Services es compatible con dos tipos de localizadores: localizadores OnDemandOrigin, utilizados para transmitir multimedia (por ejemplo, MPEG guión, HLS o Smooth Streaming) o descargar cada vez medios y localizadores URL SA, se utilizan para cargar o descargar/de medios archivos desde almacenamiento de Azure. 

Tenga en cuenta que no se recomienda el permiso de lista (AccessPermissions.List) al crear un localizador de OrDemandOrigin. 

###<a name="storage-account"></a>Cuenta de almacenamiento

Todo el acceso al almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Puede asociar una cuenta de servicio de medios con una o varias cuentas de almacenamiento. Una cuenta puede contener un número ilimitado de contenedores, como su tamaño total es en 500TB por cuenta de almacenamiento.  Media Services proporciona herramientas de nivel de SDK para permitirle administrar varias cuentas de almacenamiento y la distribución de los activos durante la carga a estas cuentas en función de métricas o distribución aleatoria de equilibrio de carga. Para obtener más información, vea Trabajar con [El almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

##<a name="jobs-and-tasks"></a>Tareas

Normalmente se utiliza un [trabajo](https://msdn.microsoft.com/library/azure/hh974289.aspx) en proceso (por ejemplo, índice o codificar) una presentación de audio y vídeo. Si va a procesar varios vídeos, cree una tarea para cada vídeo que se desea codificar.

Un trabajo contiene metadatos acerca del procesamiento que se realicen. Cada trabajo contiene uno o más s de [tareas](https://msdn.microsoft.com/library/azure/hh974286.aspx)que especifican una tarea de procesamiento atómica sus activos de entrada, resultados activos, un procesador de medios y su configuración asociada. Tareas de un trabajo se pueden encadenar, donde los activos de salida de una tarea se expresa como activo de entrada a la siguiente tarea. De esta manera puede contener un trabajo todo el proceso necesario para una presentación multimedia.

##<a id="encoding"></a>Codificación

Azure Media Services ofrece varias opciones para la codificación de archivos multimedia en la nube.

Cuando empiezan con Media Services, es importante comprender la diferencia entre los formatos de archivo y códecs.
Códecs son el software que implementa los algoritmos de compresión y descompresión, mientras que los formatos de archivo son contenedores que contienen el vídeo comprimido.

Media Services proporciona embalaje dinámico que le permite ofrecer el contenido de la velocidad de adaptación MP4 o Smooth Streaming codificado en transmisión formatos admitidos por el Media Services (MPEG guión, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de transmisión.

Para aprovechar las ventajas de [embalaje dinámico](media-services-dynamic-packaging-overview.md), debe hacer lo siguiente:

- Codificar el archivo mezzanine (origen) en un conjunto de velocidad adaptación MP4 archivos o adaptación de velocidad Smooth Streaming (se muestran los pasos de codificación más adelante en este tutorial).
- Obtener al menos una unidad de transmisión por secuencias a petición del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido. Para obtener más información, vea [cómo a petición transmisión reservado unidades de la escala](media-services-portal-manage-streaming-endpoints.md).

Media Services admite las siguientes en codificadores petición que se describen en este artículo:

- [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
- [Flujo de trabajo de Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Para obtener información acerca de codificadores admitidos, vea [codificadores](media-services-encode-asset.md).


##<a name="live-streaming"></a>Live Streaming

En Azure Media Services, un canal representa una canalización para el procesamiento de contenido de transmisión por secuencias en directo. Un canal recibe directo secuencias de entrada en una de estas dos maneras:

- Un codificador de live local envía RTMP velocidad multi o Smooth Streaming (fragmentar MP4) para el canal. Puede usar los siguientes codificadores directo entre varios velocidad Smooth Streaming de salida: elementos, Envivio, Cisco. Los siguientes codificadores directo salida RTMP: transcodificadores Adobe Flash Live, Telestream Wirecast y Tricaster. Las secuencias integradas pasen a través de canales sin ningún otro proceso. Cuando se solicita, Media Services ofrece la secuencia a los clientes.

- Una secuencia de velocidad única (en uno de los siguientes formatos: RTP (MPEG-TS)), RTMP o Smooth Streaming (fragmentar MP4)) se envía al canal que está habilitado para realizar la codificación en directo con Media Services. El canal, a continuación, realiza la codificación en directo de la entrada secuencia de velocidad único en una secuencia de vídeo de multi-velocidad (adaptación). Cuando se solicita, Media Services ofrece la secuencia a los clientes.

###<a name="channel"></a>Canal

En Media Services, s de [canal](https://msdn.microsoft.com/library/azure/dn783458.aspx)son responsables de procesamiento de contenido de transmisión por secuencias en directo. Un canal proporciona un extremo de entrada (recopile la dirección URL) a continuación, proporcione a un transcodificador directo. El canal recibe directo secuencias de entrada de la transcodificador directo y hace que esté disponible para la transmisión a través de uno o más StreamingEndpoints. Los canales también proporcionan un extremo de vista previa (vista previa URL) que usa para obtener una vista previa y validar la secuencia antes de procesamiento y la entrega.

Puede obtener la dirección URL de ingesta y la dirección URL de vista previa al crear el canal. Para obtener estas direcciones URL, el canal no tiene que estar en el estado iniciado. Cuando esté listo para iniciar la inserción de datos de un transcodificador directo en el canal, debe iniciar el canal. Una vez que la transcodificador directo inicia recopila datos, puede obtener una vista previa de la secuencia.

Cada cuenta Media Services puede contener varios canales, varios programas y StreamingEndpoints varias. Según las necesidades de seguridad y el ancho de banda, StreamingEndpoint servicios pueden estar dedicados a uno o más canales. Puede extraer cualquier StreamingEndpoint ningún canal.


###<a name="program"></a>Programa

Un [programa](https://msdn.microsoft.com/library/azure/dn783463.aspx) le permite controlar la publicación y el almacenamiento de segmentos en una secuencia en directo. Canales administración programas. La relación de canal y programa es muy similar a medios tradicionales donde un canal tiene un flujo constante de contenido y un programa se centra en algunas evento con hora en dicho canal.
Puede especificar el número de horas que desea conservar el contenido registrado para el programa estableciendo la propiedad **ArchiveWindowLength** . Este valor se puede establecer desde un mínimo de 5 minutos hasta un máximo de 25 horas.

ArchiveWindowLength determina también que puede buscar la cantidad máxima de clientes de tiempo en el tiempo desde la posición actual directo. Pueden ejecutar programas durante el período de tiempo especificado, pero continuamente se descarta el contenido que se encuentre detrás de la longitud de la ventana. El valor de esta propiedad también determina cuánto tiempo pueden aumentar los manifiestos de cliente.

Cada programa está asociado con un activo. Para publicar el programa debe crear un localizador para el activo asociado. Tener este localizador le permitirá generar una URL streaming que puede proporcionar a sus clientes.

Un canal es compatible con hasta tres programas al mismo tiempo, por lo que puede crear varios archivos de la misma secuencia entrante. Esto le permite publicar y archivar distintas partes de un evento según sea necesario. Por ejemplo, necesidades de su empresa es archivar 6 horas de un programa, pero difundir solo últimos 10 minutos. Para ello, debe crear dos programas al mismo tiempo. Un programa se establece archivar 6 horas del evento, pero no se ha publicado el programa. El otro programa está establecido en archivar para 10 minutos y este programa se publica.


Para obtener más información, consulte:

- [Trabajar con los canales que están habilitados para realizar codificación en directo con Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
- [Trabajar con los canales que reciban secuencia en directo entre varios velocidad de codificadores local](media-services-live-streaming-with-onprem-encoders.md)
- [Cuotas y limitaciones](media-services-quotas-and-limitations.md).

##<a name="protecting-content"></a>Protección de contenido

###<a name="dynamic-encryption"></a>Cifrado dinámico

Azure Media Services le permite proteger su medio de la hora de sale de su equipo a través de almacenamiento, procesamiento y entrega. Media Services le permite proporcionar su contenido dinámicamente cifrado con cifrado estándar avanzado (AES) (con claves de cifrado de 128 bits) y cifrado comunes (CENC) con PlayReady o Widevine DRM. Media Services también proporciona un servicio para proporcionar claves AES y PlayReady licencias a los clientes autorizados.

Actualmente, puede cifrar los siguientes formatos de streaming: HLS, MPEG guión y Smooth Streaming. No puede cifrar HDS transmisión formato o progresivo descargas.

Si desea que Media Services cifrar un activo, debe asociar una clave de cifrado (CommonEncryption o EnvelopeEncryption) a su activo y también configurar directivas de autorización de la clave.

Si desea un activo cifrado de almacenamiento de secuencias, debe configurar Directiva de entrega del activo para especificar cómo desea ofrecer a su activo.

Cuando se solicita una secuencia con un Reproductor, Media Services usa la clave especificada para cifrar dinámicamente el contenido con un cifrado de sobres (con AES) o comunes (PlayReady o Widevine). Para descifrar la secuencia, el Reproductor solicitará la clave del servicio de entrega clave. Para decidir si el usuario está autorizado para obtener la clave, el servicio evalúa las directivas de autorización especificada para la clave.


###<a name="token-restriction"></a>Restricción de token

La directiva de autorización de clave de contenido podría tener una o más restricciones de autorización: abrir, token restricción o restricción de IP. Se adjuntará la directiva token restringido por un token emitido por una Token servicio seguro (STS). Media Services admite tokens en el formato Simple Web token (SWT) y el formato de JSON Web Token (JWT). Media Services no proporciona servicios de Token seguro. Puede crear a un STS personalizado o aprovechar Microsoft Azure ACS tokens de problema. El STS debe estar configurado para crear un símbolo que se ha iniciado sesión con las notificaciones de clave y problema especificadas especificada en la configuración de restricción de token. El servicio de entrega clave Media Services devolverá la clave solicitada (o la licencia) al cliente si el token es válido y las notificaciones de la coincidencia de token aquellos configurados para la clave (o la licencia).

Al configurar el token restringido directiva, debe especificar la clave principal de la comprobación, emisor y parámetros de la audiencia. La clave principal de comprobación contiene la clave que se firmó el token con, emisor es el servicio de token seguro que emite el token. La audiencia (a veces denominada ámbito) describe el objetivo del token o el recurso el token autoriza el acceso a. El servicio de entrega clave Media Services valida que estos valores en el símbolo que coincidan con los valores de la plantilla.

Para obtener más información, consulte los siguientes artículos:

[Proteger la información del contenido](media-services-content-protection-overview.md)
[proteger con AES de 128](media-services-protect-with-aes128.md)
[proteger con DRM](media-services-protect-with-drm.md)

##<a name="delivering"></a>Realiza

###<a id="dynamic_packaging"></a>Embalaje dinámico

Cuando se trabaja con Media Services se recomienda codificar archivos mezzanine en una velocidad adaptación MP4 establecer y, a continuación, convertir el conjunto en el formato deseado mediante el [Embalaje dinámico](media-services-dynamic-packaging-overview.md).


###<a name="streaming-endpoint"></a>Extremo Streaming

Un StreamingEndpoint representa un servicio de transmisión que puede proporcionar contenido directamente a una aplicación de reproducción de cliente o a una red de entrega de contenido (CDN) para ampliar la distribución (Azure Media Services ahora proporciona la integración de Azure CDN). La secuencia de salida de un servicio de StreamingEndpoint puede ser una secuencia en directo o un vídeo a petición activo en su cuenta de Media Services. Además, puede controlar la capacidad del servicio StreamingEndpoint manejar crecientes necesidades de ancho de banda mediante el ajuste de escala unidades (también conocido como streaming). Se recomienda asignar uno o más unidades de la escala de las aplicaciones en el entorno de producción. Unidades de escala proporcionarán la capacidad de salida dedicado que se puede comprar en incrementos de 200 Mbps y funciones adicionales que actualmente incluyen embalaje dinámicos de uso.

Se recomienda usar cifrado dinámico de embalaje dinámicos o. Para usar estas características, debe tener al menos una unidad de transmisión del extremo desde el que va a transmitir. Para obtener más información, vea [unidades de transmisión de escala](media-services-portal-manage-streaming-endpoints.md).

De forma predeterminada puede tener hasta 2 transmisión extremas en su cuenta de Media Services. Para solicitar un límite superior, consulte [cuotas y limitaciones](media-services-quotas-and-limitations.md).

Solo se cargarán cuando su StreamingEndpoint está en estado de ejecución.

###<a name="asset-delivery-policy"></a>Directiva de entrega de activos

Uno de los pasos del flujo de trabajo de la entrega de contenido Media Services está configurando [las directivas de entrega para los activos ](https://msdn.microsoft.com/library/azure/dn799055.aspx)que desea que se transmite. La directiva de entrega activos indica Media Services cómo desea para que su activo se ha entregado: en la transmisión de protocolo debe su activo dinámicamente empaquetar (por ejemplo, MPEG guión, HLS, Smooth Streaming o todos), si no desea cifrar dinámicamente su activo y cómo se (sobres o cifrado comunes).

Si tiene un activo cifrado de almacenamiento, antes de que puedan transmitir su activo, el servidor de transmisión quita el cifrado de almacenamiento y transmite el contenido con la directiva de entrega especificado. Por ejemplo, para ofrecer a su activo cifrada con la clave de cifrado estándar de cifrado avanzado (AES), establezca el tipo de directiva en DynamicEnvelopeEncryption. Para quitar el cifrado de almacenamiento y transmita activo en texto sin cifrar, establezca el tipo de directiva en NoDynamicEncryption.

###<a name="progressive-download"></a>Descargar progresivo

Descargar progresivo le permite iniciar la reproducción multimedia antes de que se ha descargado todo el archivo. Solo cada vez, puede descargar un archivo MP4.

Tenga en cuenta que debe descifrar activos cifrados si desea que esté disponible para su descarga progresiva.

Para proporcionar a los usuarios con direcciones URL de descarga progresivo, primero debe crear un localizador de OnDemandOrigin. Crear el localizador, le ofrece la ruta de acceso base al activo. A continuación, debe anexar el nombre del archivo MP4. Por ejemplo:

http://amstest1.streaming.mediaservices.Windows.NET/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###<a name="streaming-urls"></a>Direcciones URL Streaming

Transmisión por secuencias el contenido a los clientes. Para proporcionar a los usuarios con direcciones URL de transmisión, primero debe crear un localizador de OnDemandOrigin. Crear el localizador, le ofrece la ruta de acceso base al activo que contiene el contenido que desea transmitir. Sin embargo, para poder transmitir este contenido debe modificar esta ruta de acceso más. Para construir una dirección URL completa del archivo de manifiestos streaming, debe concatenar el valor de ruta de acceso del localizador y el manifiesto (filename.ism) nombre de archivo. A continuación, anexar/manifest y un formato adecuado (si es necesario) a la ruta de acceso de localizador.

También puede difundir su contenido en una conexión SSL. Para ello, asegúrese de que la URL streaming iniciar con HTTPS.

Tenga en cuenta que puede transmitir sólo sobre SSL si creó el extremo streaming desde el que ofrecer el contenido después de 10 de septiembre de 2014. Si la URL streaming se basa en los extremos streaming creados después de 10 de septiembre, la dirección URL contiene "streaming.mediaservices.windows.net" (el formato nuevo). Transmisiones de direcciones URL que contienen "origin.mediaservices.windows.net" (el antiguo formato) no admiten SSL. Si es la dirección URL en el antiguo formato y desea poder transmitir a través de SSL, cree un nuevo punto final streaming. Usar direcciones URL que se crean basándose en el nuevo extremo streaming para transmitir el contenido a través de SSL.

En la lista siguiente describe los diferentes formatos de streaming y proporciona ejemplos:

- Smooth Streaming

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest


- MPEG GUIÓN

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=MPD-Time-CSF)



- Apple HTTP Live Streaming V4 (HLS)

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl)



- Apple HTTP Live Streaming V3 (HLS)

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl-V3)

- HDS (para licencias de Adobe PrimeTime/Access)

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=f4m-F4F)


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
