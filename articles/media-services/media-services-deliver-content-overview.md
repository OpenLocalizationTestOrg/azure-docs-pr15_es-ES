<properties
    pageTitle="Entrega de contenido a clientes | Microsoft Azure"
    description="Este tema ofrece una descripción general de lo que implica la entrega de contenido con Azure Media Services."
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


# <a name="deliver-content-to-customers"></a>Proporcionar contenido a los clientes
Cuando realiza el contenido de transmisión o vídeo a la carta a los clientes, su objetivo es ofrecer vídeo de alta calidad a varios dispositivos en condiciones de red diferente.

Para ello, puede:

- Codificar la secuencia de una secuencia de vídeo de la velocidad de entre varios bits (velocidad adaptación). Esto se ocupará de condiciones de calidad y de red.
- Use Microsoft Azure Media Services [embalaje dinámico](media-services-dynamic-packaging-overview.md) dinámicamente volver a empaquetar su secuencia en protocolos diferentes. Esto se ocupará de transmisión en los distintos dispositivos. Media Services admite la entrega de la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG-guión y HDS (para las licencias de acceso o Primetime Adobe).

Este artículo proporciona una descripción general de los conceptos de entrega de contenido importante.

Para comprobar problemas conocidos, vea [problemas conocidos](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Embalaje dinámico

Con la presentación dinámica que proporciona servicios de medios, puede ofrecer el contenido de la velocidad de adaptación MP4 o Smooth Streaming codificado en transmisión formatos admitidos por el Media Services (MPEG-guión, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de transmisión. Se recomienda ofrecer su contenido con embalaje dinámico.

Para aprovechar las ventajas de embalaje dinámico, debe hacer lo siguiente:

- Codificar el archivo mezzanine (origen) en un conjunto de archivos de velocidad adaptación MP4 o adaptación de velocidad Smooth Streaming.
- Obtener al menos una unidad de transmisión por secuencias a petición del extremo de transmisión por secuencias desde el que tiene previsto ofrecer su contenido. Para obtener más información, vea [cómo escalar transmisión unidades reservados a petición](media-services-portal-manage-streaming-endpoints.md).

Con embalaje dinámico, almacenar y de pago para los archivos en formato de almacenamiento único. Media Services generará y servir la respuesta adecuada en función de sus solicitudes.

Además de proporcionar acceso a las funciones de empaquetado dinámicos, transmisión unidades reservados a petición proporciona capacidad de salida dedicado que se puede comprar en incrementos de 200 Mbps. De forma predeterminada, la transmisión a petición está configurado en un modelo de instancia compartida para el servidor que comparten los recursos (por ejemplo, capacidad de cálculo o de salida) con todos los demás usuarios. Puede mejorar un rendimiento de transmisión por secuencias a petición adquiriendo unidades reservados streaming a petición.

Para obtener más información, vea [Empaquetar dinámico](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtros y manifiestos dinámicos

Puede definir filtros para los activos con Media Services. Estos filtros son reglas de servidor que le ayudan a los clientes hacer cosas como reproducir una sección específica de un vídeo o especificar un subconjunto de representaciones de audio y vídeo dispositivo del cliente puede controlar (en lugar de todas las copias que están asociadas al activo). Este filtrado se logra mediante *manifiestos dinámicos* que se crean cuando el cliente solicita a transmitir un vídeo en función de uno o más filtros especificados.

Para obtener más información, vea [filtros y manifiestos dinámicos](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a>Localizadores

Para proporcionar el usuario con una dirección URL que puede utilizarse para transmitir o descargar el contenido, debe publicar su activo mediante la creación de un localizador. Un localizador proporciona un punto de entrada para obtener acceso a los archivos de un activo. Media Services es compatible con dos tipos de localizadores:

- Localizadores OnDemandOrigin. Estos se utilizan para transmitir contenido multimedia (por ejemplo, MPEG-guión, HLS o Smooth Streaming) o descargar archivos de cada vez.
-  Localizadores de dirección URL de firma (SA) de acceso compartido. Se utilizan para descargar archivos multimedia en su equipo local.

Una *Directiva de acceso* se utiliza para definir los permisos (por ejemplo, leer, escribir y mostrar) y la duración para que un cliente tiene acceso para un activo en particular. Tenga en cuenta que no se recomienda el permiso de lista (AccessPermissions.List) en la creación de un localizador de OrDemandOrigin.

Localizadores tienen fechas de vencimiento. El portal de Azure establece una fecha de caducidad 100 años en el futuro de localizadores.

>[AZURE.NOTE] Si utiliza el portal de Azure crear localizadores antes de marzo de 2015, esos localizadores se establecieron que expiren después de dos años.

Para actualizar una fecha de vencimiento de un localizador, utilice el [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) de las API de [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Observe que, cuando se actualiza la fecha de vencimiento de un localizador SA, cambia la dirección URL.

Localizadores no están diseñadas para administrar el control de acceso de cada usuario. Puede dar distintos derechos de acceso a usuarios individuales mediante soluciones de administración de derechos digitales (DRM). Para obtener más información, vea [Proteger multimedia](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Cuando se crea un localizador, puede haber 30 segundos de retraso debido a almacenamiento necesario y procesos de propagación en el almacenamiento de Azure.


## <a name="adaptive-streaming"></a>Adaptación transmisión

Tecnologías de velocidad adaptación permiten a aplicaciones de Reproductor de vídeo determinar las condiciones de red y seleccione desde diversas velocidades de bits bajas. Cuando se disminuye la comunicación de red, el cliente puede seleccionar una velocidad de bits inferior para que reproducción puede continuar con la calidad de vídeo inferior. Como para mejoran las condiciones de red, puede cambiar el cliente a una velocidad superior con una mejor calidad de vídeo. Azure Media Services admite las siguientes tecnologías de velocidad adaptación: transmisión Live HTTP (HLS), Smooth Streaming, MPEG-guión y HDS.

Para proporcionar a los usuarios con direcciones URL de transmisión, primero debe crear un localizador de OnDemandOrigin. Crear el localizador proporciona la ruta de acceso base al activo que contiene el contenido que desea transmitir. Sin embargo, para poder transmitir este contenido, debe modificar esta ruta de acceso más. Para construir una dirección URL completa del archivo de manifiestos streaming, debe concatenar el valor de ruta de acceso del localizador y el manifiesto (filename.ism) nombre de archivo. A continuación, anexar **manifiestos** y un formato adecuado (si es necesario) a la ruta de acceso de localizador.

>[AZURE.NOTE]También puede difundir su contenido en una conexión SSL. Para ello, asegúrese de que la URL streaming iniciar con HTTPS.

Solo puede transmitir sobre SSL si creó el extremo streaming desde el que ofrecer el contenido después de 10 de septiembre de 2014. Si la URL streaming se basa en los extremos streaming creados después de 10 de septiembre de 2014, la dirección URL contiene "streaming.mediaservices.windows.net". Transmisiones de direcciones URL que contienen "origin.mediaservices.windows.net" (el antiguo formato) no admiten SSL. Si es la dirección URL en el antiguo formato y desea poder transmitir a través de SSL, cree un nuevo punto final streaming. Usar direcciones URL basándose en el nuevo extremo streaming para transmitir el contenido a través de SSL.


## <a name="streaming-url-formats"></a>Formatos de dirección URL de transmisión

### <a name="mpeg-dash-format"></a>Formato MPEG-guión

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=MPD-Time-CSF)



### <a name="apple-http-live-streaming-hls-v4-format"></a>Formato de Apple HTTP Live transmisión (HLS) V4

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formato de Apple HTTP Live transmisión (HLS) V3

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl-V3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato de Apple HTTP Live transmisión (HLS) con filtro solo de audio

De forma predeterminada, pistas solo de audio se incluyen en la HLS manifiesto. Esto es necesario para la certificación Store de Apple redes de telefonía móvil. En este caso, si un cliente no tiene suficiente ancho de banda o está conectado a través de una conexión de 2G, reproducción cambia a solo de audio. Esto ayuda a evitar la transmisión de contenido sin búfer, pero no hay ningún vídeo. En algunos casos, el Reproductor de búfer podría estar preferido sobre solo de audio. Si desea quitar el control solo de audio, agregue **solo de audio = false** a la dirección URL.

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl-v3,audio-Only=FALSE)

Para obtener más información, vea [características adicionales de salida de soporte técnico de composición dinámica manifiestos y HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).


### <a name="smooth-streaming-format"></a>Formato de transmisión suave

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Ejemplo:

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a id="fmp4_v20"></a>Smooth Streaming 2.0 manifiesto (manifiesto heredado)

De forma predeterminada, Smooth Streaming manifiesto formato contiene la etiqueta de repetición (r etiqueta). Sin embargo, algunos reproductores no admiten la etiqueta de r. Los clientes con estos jugadores pueden utilizar un formato que deshabilita la etiqueta de r:

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

### <a name="hds-for-adobe-primetimeaccess-licensees-only"></a>HDS (para licencias de Adobe PrimeTime/Access)

{transmisión extremo servicios multimedia de nombre de cuenta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

## <a name="progressive-download"></a>Descargar progresivo

Con descarga progresiva, puede iniciar reproducción multimedia antes de que se ha descargado todo el archivo. Cada vez, no puede descargar .ism * archivos de (ismv, isma, ismt o ismc).

Para descargar el contenido cada vez, use el tipo de OnDemandOrigin de localizador. En el ejemplo siguiente se muestra la dirección URL que se basa en el tipo de OnDemandOrigin de localizador:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Debe descifrar activos de cifrado de almacenamiento que desee transmitir desde el servicio de origen para su descarga progresivo.

## <a name="download"></a>Descargar

Para descargar el contenido a un dispositivo de cliente, debe crear un localizador de SA. Localizador de SA le proporciona acceso al contenedor de almacenamiento de Azure donde se encuentra el archivo. Para generar la dirección URL de la descarga, debe insertar el nombre de archivo entre el host y firma SA.

En el ejemplo siguiente se muestra la dirección URL que se basa en el localizador SA:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Aplica teniendo en cuenta lo siguiente:

- Debe descifrar activos de cifrado de almacenamiento que desee transmitir desde el servicio de origen para su descarga progresivo.
- Se producirá una descarga que no ha terminado dentro de 12 horas.

## <a name="streaming-endpoints"></a>Transmisión extremos

Un extremo streaming representa un servicio de transmisión que puede proporcionar contenido directamente a una aplicación de reproducción de cliente o a una red de entrega de contenido (CDN) para ampliar la distribución. La secuencia de salida de un servicio de transmisión extremo puede ser una secuencia en directo o un activo de vídeo a la carta en su cuenta de Media Services. También puede controlar la capacidad del servicio de extremo streaming manejar crecientes necesidades de ancho de banda ajustando la transmisión unidades reservados. Debe asignar al menos una unidad reservada para aplicaciones en un entorno de producción. Para obtener más información, vea [cómo ajustar el tamaño de un servicio de medios](media-services-portal-manage-streaming-endpoints.md).

## <a name="known-issues"></a>Problemas conocidos

### <a name="changes-to-smooth-streaming-manifest-version"></a>Cambios en Smooth Streaming manifiestos versión

Antes de la versión de servicio de 2016 julio--cuando activos creados mediante una Media Encoder estándar, el flujo de trabajo de Media Encoder Premium o el anteriores Codificador multimedia de Azure se transmite mediante el uso de la presentación dinámica: Smooth Streaming manifiesto devuelto podría se ajustan a la versión 2.0. En la versión 2.0, las duraciones de fragmento no use las etiquetas de repetir llamados ('r'). Por ejemplo:

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

En la versión de servicio de julio de 2016 manifiesto Smooth Streaming generado se ajusta a versión 2.2, con duraciones fragmento con etiquetas de repetición. Por ejemplo:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Algunos de los clientes Smooth Streaming heredados no pueden admitir las etiquetas de repetición y se producirá un error al cargar el manifiesto. Para mitigar este problema, puede usar el parámetro de formato manifiestos heredado **(formato = v20 fmp4)** o actualizar su cliente a la versión más reciente, que es compatible con etiquetas de repetición. Para obtener más información, vea [2.0 transmisión suave](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Temas relacionados

[Actualizar localizadores Media Services después sucesiva claves de almacenamiento](media-services-roll-storage-access-keys.md)
