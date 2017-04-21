<properties 
    pageTitle="Filtros y manifiestos dinámicos | Microsoft Azure" 
    description="Este tema describe cómo crear filtros para que el cliente puede usarlos para determinadas secciones de un flujo de secuencia. Media Services crea manifiestos dinámicos para archivar esta transmisión selectivo." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="cenkdin;juliako"/>

# <a name="filters-and-dynamic-manifests"></a>Filtros y manifiestos dinámicos

A partir de la versión 2.11, Media Services le permite definir filtros para los activos. Éstos son reglas del lado servidor que permiten a los clientes elegir hacer cosas como: reproducción solo una sección de un vídeo (en lugar de reproducir el vídeo completo), o especifique únicamente un subconjunto de representaciones de audio y vídeo dispositivo del cliente puede controlar (en lugar de todas las copias que están asociadas al activo). Este filtrado de los activos archivado mediante s **Dinámico manifiestos**que se crean tras la solicitud del cliente para transmitir un vídeo en función de los filtros especificados.

Este tema se describen escenarios comunes en el que con filtros de puede resultar muy útil para los clientes y vínculos a temas que muestran cómo crear filtros mediante programación (actualmente, puede crear filtros con las API de REST solo).

##<a name="overview"></a>Información general

Cuando realiza el contenido a los clientes (transmisión eventos en directo o vídeo a la carta) su objetivo es ofrecer un vídeo de alta calidad a varios dispositivos en condiciones de red diferente. Para lograr este no objetivo lo siguiente:

- codificar la secuencia de secuencia de vídeo de la velocidad de entre varios bits ([velocidad adaptación](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (Esto se ocupará de condiciones de calidad y red) y 
- Use Media Services [Embalaje dinámico](media-services-dynamic-packaging-overview.md) dinámicamente volver a empaquetar su secuencia en diferentes protocolos (Esto se ocupará de transmisión en los distintos dispositivos). Media Services admite la entrega de la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe). 

###<a name="manifest-files"></a>Archivos de manifiestos 

Cuando se codifica un activo para la transmisión de velocidad adaptación, se crea un archivo **de manifiestos** (reproducción) (el archivo está basado en texto o basado en XML). El archivo **manifiestos** incluye transmisión metadatos como: realizar un seguimiento de tipo (audio, vídeo o texto), realizar un seguimiento de nombre, hora de inicio y finalización, la velocidad de bits (calidad), idiomas del control, la ventana de presentación (ventana deslizante de duración fija), el códec de vídeo (código de cuatro caracteres). También se indica el Reproductor para recuperar el fragmento siguiente le proporciona información sobre el siguientes fragmentos vídeo reproducible disponibles y su ubicación. Fragmentos (o segmentos) son reales "fragmentos" de contenido de vídeo.


Aquí tiene un ejemplo de un archivo manifiesto: 

    
    <?xml version="1.0" encoding="UTF-8"?>  
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">
    
    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />
    
    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    </SmoothStreamingMedia>
    
###<a name="dynamic-manifests"></a>Manifiestos dinámicos

Hay [situaciones](media-services-dynamic-manifest-overview.md#scenarios) cuando su cliente necesita más flexibilidad que lo que se describe en el archivo de manifiestos del activo de forma predeterminada. Por ejemplo:

- Específicas del dispositivo: ofrecer sólo las y/o representaciones especificado especifica pistas de idioma que son compatibles con el dispositivo que se usa para reproducir el contenido ("representación filtrado"). 
- Reducir el manifiesto para mostrar una imagen prediseñada secundarias de un evento en directo ("vídeo filtrado").
- Recortar el inicio de un vídeo ("recortar un vídeo").
- Ajustar la ventana de la presentación (DVR) para proporcionar una longitud limitada de la ventana DVR en el Reproductor ("ventana Ajuste de presentación").
 
Para lograr esta flexibilidad, Media Services ofrece **Manifiestos dinámicas** basadas en [filtros](media-services-dynamic-manifest-overview.md#filters)de predefinidos.  Una vez que defina los filtros, los clientes podrían usar para transmitir una copia específica o subsitios clips de vídeo. Se especifican los filtros en la dirección URL streaming. Se pueden aplicar filtros a velocidad adaptación transmisión protocolos admitidos por [Embalaje dinámico](media-services-dynamic-packaging-overview.md): HLS, MPEG-guión, Smooth Streaming y HDS. Por ejemplo:

Dirección URL de guión MPEG con filtro

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL Smooth Streaming con filtro

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Para obtener más información acerca de cómo ofrecer su contenido y generar transmisión direcciones URL, vea [Descripción general de ofrecer contenido](media-services-deliver-content-overview.md).


>[AZURE.NOTE]Tenga en cuenta que manifiestos dinámico no cambian el activo y el manifiesto predeterminado de dicho activo. El cliente puede solicitar una secuencia con o sin filtros. 


###<a id="filters"></a>Filtros 

Existen dos tipos de filtros de activos: 

- Filtros globales (se puede aplicar a cualquier activo de la cuenta de Azure Media Services, tiene una duración de la cuenta) y 
- Filtros locales (solo se puede aplicar a un activo con la que estaba asociado al crear el filtro, tiene una duración de un activo). 

Tipos de filtros globales y locales tienen exactamente las mismas propiedades. La diferencia principal entre los dos es para los escenarios de qué tipo de un sistema de almacenamiento es más adecuado. Filtros globales son generalmente adecuados para los perfiles de dispositivo (representación filtrado) donde se podrían usar filtros locales para recortar un activo específico.


##<a id="scenarios"></a>Escenarios comunes 

Como se mencionó anteriormente, al realizar el contenido a los clientes (transmisión eventos en directo o vídeo a la carta) su objetivo es ofrecer un vídeo de alta calidad a varios dispositivos en condiciones de red diferente. Además, los puede tener otros requisitos que implican los activos de filtrado y el uso de **Manifiestos dinámico**s. Las secciones siguientes proporcionan una breve descripción general de los diferentes escenarios de filtrado.

- Especifique solo un subconjunto de representaciones de audio y vídeo que pueden controlar determinados dispositivos (en lugar de todas las copias que están asociadas al activo). 
- Reproducir solo una sección de un vídeo (en lugar de reproducir el vídeo completo).
- Ajustar la ventana de la presentación de DVR.

##<a name="rendition-filtering"></a>Filtrado de representación 

Puede codificar su activo para varios perfiles de codificación (línea base H.264, H.264 alto, AACL, AACH, Dolby Digital Plus) y múltiples velocidades de bits bajas de calidad. Sin embargo, no todos los dispositivos de cliente admiten todos los activos perfiles y velocidades de bits bajas. Por ejemplo, los dispositivos Android antiguos solo es compatible con H.264 previsto + AACL. Enviar valores más altos a un dispositivo que no puede obtener acceso a las ventajas, residuos el cálculo de ancho de banda y el dispositivo. Dispositivo de este tipo debe descodificar toda la información determinada, sólo para escalar hacia abajo para que se muestre.

Con manifiestos dinámico, puede crear perfiles de dispositivo como mobile, consola HD/SD, etc. e incluir las pistas y las características que desea que sean parte de cada perfil.

 
![Ejemplo filtrado de representación][renditions2]

En el ejemplo siguiente, un codificador se usó para codificar un activo mezzanine en siete representaciones de vídeo de ISO MP4s (de 180p a 1080p). Activo codificado se puedan empaquetar dinámicamente en cualquiera de los protocolos de transmisión siguientes: HLS, suave, MPEG guión y HDS.  En la parte superior del diagrama, se muestra el manifiesto HLS para el activo sin filtros (contiene todas las siete copias).  En la parte inferior izquierda, se muestra el manifiesto HLS al que se aplica un filtro con el nombre "ott". Especifica el filtro de "ott" para quitar todas las velocidades de bits bajas debajo 1Mbps, que ha generado los niveles de calidad inferior dos perdiendo en la respuesta.  En la parte inferior derecha, se muestra el manifiesto HLS al que se aplica un filtro con el nombre "móvil". Especifica el filtro "móvil" para quitar representaciones donde la resolución es mayor que 720p, que ha generado los dos representaciones 1080p perdiendo.

![Filtrado de representación][renditions1]

##<a name="removing-language-tracks"></a>Quitar pistas de idioma

Los activos pueden incluir sonido en varios idiomas como inglés, español, francés, etcetera. Normalmente, los jefes de SDK del reproductor predeterminado de selección de pista de audio y realiza un seguimiento de audio disponible por selección de usuario. Es difícil desarrollar tales SDK Reproductor, requiere implementaciones diferentes a través de marcos de Reproductor específica del dispositivo. Además, en algunas plataformas, API Player están limitadas y no incluye la característica de selección de audio, donde los usuarios no pueden seleccionar o cambiar la pista de audio predeterminado. Con los filtros de activos, puede controlar el comportamiento mediante la creación de filtros que incluyan solo los idiomas de audio que desee.

![Filtrado de pistas de idioma][language_filter]


##<a name="trimming-start-of-an-asset"></a>Inicio de recorte de un activo 

En la mayoría de eventos streaming directo, operadores ejecutan algunas pruebas antes del evento real. Por ejemplo, pueden incluir una pizarra así antes del inicio del evento: "Programa comenzará momentáneamente". Si el programa de archivado, la prueba y los datos de pizarra también se archivan y se incluirán en la presentación. Sin embargo, esta información no se muestra a los clientes. Con manifiestos dinámico, puede crear un filtro de tiempo de inicio y quitar los datos no deseados del manifiesto.

![Inicio de recorte][trim_filter]

##<a name="creating-sub-clips-views-from-a-live-archive"></a>Creación de subsitios clips (vistas) de un archivo de live

Muchos eventos directo son largos ejecutando y archivos activos pueden incluir varios eventos. Después del evento directo extremos emisoras que desee dividir el archivo en el inicio de programas lógico directo y dejar de secuencias. A continuación, publicar estos programas virtuales por separado sin entrada al archivo directo de procesamiento y no crear activos independientes (que no obtendrá ventaja de los fragmentos de caché existentes en la CDN). Ejemplos de estos programas virtuales (subcarpetas clips) son los trimestres de un fútbol o partido de baloncesto, las entradas de béisbol o eventos individuales de una tarde del programa de los Juegos Olímpicos.

Con manifiestos dinámico, puede crear filtros con horas de inicio o finalización y crear vistas virtuales sobre la parte superior de su archivo directo. 

![Filtro de subclip][subclip_filter]

Filtrado activo:

![Esquí][skiing]

##<a name="adjusting-presentation-window-dvr"></a>Ajustar la ventana de la presentación (DVR)

Actualmente, Azure Media Services ofrece circular archivo donde se puede configurar la duración entre 5 minutos - 25 horas. Manifiestos filtrado puede utilizarse para crear una ventana DVR graduales sobre la parte superior del contenedor, sin eliminar multimedia. Hay muchos escenarios donde emisoras desea proporcionar una ventana DVR limitada que se mueve con el borde directo y al mismo tiempo mantener una ventana de archivado más grande. Una cadena que desee utilizar los datos que están fuera de la ventana DVR para resaltar los clips o he\she posible desea proporcionar diferentes windows DVR para distintos dispositivos. Por ejemplo, la mayoría de los dispositivos móviles no controlar ventanas DVR grandes (puede tener una ventana DVR 2 minutos para dispositivos móviles y 1 hora para clientes de escritorio).

![Ventana DVR][dvr_filter]

##<a name="adjusting-livebackoff-live-position"></a>Ajustar LiveBackoff (posición directo)

Manifiestos filtrado puede usarse para quitar varios segundos desde el borde directo de un programa directo. Esto permite emisoras ver la presentación en el punto de publicación de vista previa y crear anuncio puntos de inserción antes de que los visores de reciban la secuencia (normalmente copia-off 30 segundos). Emisoras pueden insertar estos anuncios en sus entornos de cliente de tiempo para que recibido y procesar la información antes de la oportunidad de anuncio.

Además de la compatibilidad de anuncio, LiveBackoff puede utilizarse para ajustar la posición del cliente descarga directo para que cuando los clientes variar y visita el borde directo pueden obtener acceso fragmentos del servidor en lugar de obtener los errores HTTP 404 o 412.

![livebackoff_filter][livebackoff_filter]


##<a name="combining-multiple-rules-in-a-single-filter"></a>Combinar varias reglas en un único filtro

Puede combinar varias reglas filtradas en un único filtro. Puede definir una regla de intervalo para quitar la Pizarra desde un archivo directo y también filtrar velocidades de bits bajas disponibles como ejemplo. Para varias reglas filtradas, el resultado final es la composición (solo intersección) de estas reglas.

![varias reglas][multiple-rules]

##<a name="create-filters-programmatically"></a>Crear filtros mediante programación

El tema siguiente describe entidades Media Services relacionados con los filtros. El tema también muestra cómo crear filtros de programación.  

[Crear filtros con las API de REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Combinar varios filtros (filtro composición)

También puede combinar varios filtros en una única dirección URL. 

El siguiente escenario muestra ¿por qué desea combinar filtros:

1. Debe filtrar la calidad de vídeo para dispositivos móviles, como Android o iPAD (para limitar la calidad de vídeo). Para quitar las características no deseadas, debe crear un filtro que es adecuado para los perfiles de dispositivo. Como se mencionó anteriormente, pueden utilizarse filtros globales de todos los activos en la misma cuenta de servicios de medios sin cualquier otra asociación. 
2. También desea recortar la hora de inicio y final de un activo. Para ello, podría crear un filtro local y establecer la hora de inicio o finalización. 
3. Desea combinar ambas de estas filtros (sin combinación que necesarias para agregar el filtrado de calidad para el filtro de recorte que hará uso de filtro difícil).

Para combinar filtros, es necesario configurar los nombres de filtro a la lista de manifiesto o reproducción URL con punto y coma delimitados por comas. Supongamos que tiene un filtro denominado *MyMobileDevice* que características de filtros y tiene otro nombre *MyStartTime* para establecer una hora de inicio específicos. Se puede combinar similar a esta:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Puede combinar filtros hasta 3. 

Para obtener más información, consulte [este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.


##<a name="know-issues-and-limitations"></a>Conocer los problemas y limitaciones

- Manifiesto dinámica funciona en GOP límites (clave marcos), por tanto, recortar tiene precisión GOP. 
- Puede usar el mismo nombre de filtro para filtros globales y locales. Tenga en cuenta que filtro local tiene prioridad y anulará filtros globales.
- Si actualiza un filtro, puede tardar hasta 2 minutos para streaming extremo actualizar las reglas. Si el contenido se ha servido con algunos filtros (y caché en servidores proxy y CDN en caché), actualizar estos filtros puede provocar errores del Reproductor. Es recomendable borrar la caché después de actualizar el filtro. Si esta opción no es posible, considere la posibilidad de usar un filtro diferente.


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Vea también

[Entrega de contenido a información general de los clientes](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
 