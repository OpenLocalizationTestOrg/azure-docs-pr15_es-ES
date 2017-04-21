<properties 
    pageTitle="Formatos estándar de codificador multimedia y códecs" 
    description="Este tema ofrece una descripción general de los formatos estándar de codificador multimedia y códecs." 
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
    ms.topic="article" 
    ms.date="10/10/2016"
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-standard-formats-and-codecs"></a>Códecs y formatos estándar de codificador multimedia


Este documento contiene una lista de la importación más comunes y formatos de archivo de exportación que puede utilizar con Media Encoder estándar.


##<a name="input-containerfile-formats"></a>Formatos de archivo o contenedor de entrada

Formatos de archivo (extensiones de archivo)|Compatibles
---|---|---|---
FLV (con códecs H.264 y AAC) (.flv)          |Sí 
MXF (.mxf)                  |Sí 
GXF (.gxf)                  |Sí 
MPEG2-PS, MPEG2-TS, 3GP (.ts, PS,. 3gp, .3gpp, .mpg)   |Sí 
Windows Media Video (WMV) o ASF (.wmv, .asf) |Sí 
AVI (sin comprimir de 8 bits/10 bits) (.avi)|Sí 
MP4 (. mp4,. m4a,. m4v) / ISMV (.isma, .ismv)|Sí 
[Recording(DVR-MS) de vídeo Digital de Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Sí 
Matroska/WBEM (.mkv)        |Sí 
ONDA/WAV (.wav) |Sí 
QuickTime (.mov) |Sí

>[AZURE.NOTE] Es una lista de las extensiones de archivo más habituales. Media Encoder estándar es compatible con muchas otras (por ejemplo: .mpeg2video,. m2ts, .qt). Si intenta codificar un archivo y recibe un mensaje de error sobre el formato que no se admite, proporcione comentarios [aquí](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).

###<a name="audio-formats-in-input-containers"></a>Formatos de audio en contenedores de entrada 

Media Encoder estándar admite llevar los siguientes formatos de audio en contenedores de entrada:

- MXF, GXF y QuickTime archivos que tienen pistas de audio con intercaladas estéreo o 5.1 muestras

o

- Archivos MXF, GXF y QuickTime donde el audio se realiza como pistas PCM independientes, pero puede deducir la asignación de canal (a estéreo o 5.1) de los metadatos de archivo

Tenga en cuenta que admiten para la asignación de canal explícitas o proporcionado por el usuario se proporcionará en un futuro próximo.


##<a name="input-video-codecs"></a>Códecs de vídeo de entrada

Códecs de vídeo de entrada|Compatibles
---|---|---|---
AVC 8/10 bits, hasta 4:2:2, incluidos AVCIntra   |4 de 8 bits: 2:0 y 4:2:2 
DNxHD Avid (en MXF)                                 |Sí 
DVCPro/DVCProHD (en MXF)                            |Sí 
Vídeo digital (DV) (en archivos AVI)                   |Sí
JPEG 2000                                           |Sí 
MPEG-2 (hasta 422 perfil y de alto nivel, incluida la variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)|Perfil hasta 422 
MPEG-1                                              |Sí 
VC-1/WMV9                                           |Sí 
Canopus HQ/HQX                                      |No 
MPEG-4, parte 2                                       |Sí 
[Theora](https://en.wikipedia.org/wiki/Theora)      |Sí 
YUV420 sin comprimir o mezzanine                   |Sí
Apple ProRes 422                                    |Sí
Apple ProRes 422 LT |Sí
Apple ProRes 422 HQ |Sí
Apple ProRes Proxy|Sí
Apple ProRes 4444 |Sí
Apple ProRes 4444 XQ |Sí



##<a name="input-audio-codecs"></a>Códecs de Audio de entrada

Códecs de Audio de entrada|Compatibles
---|---|---|---
AAC (AAC-LC, HE-AAC y AAC-HEv2; hasta 5.1)|Sí 
MPEG Layer 2|Sí 
MP3 (MPEG-1 Audio Layer 3)|Sí 
Windows Media Audio|Sí 
WAV/PCM|Sí 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sí 
[Opus](http://go.microsoft.com/fwlink/?LinkId=822667) |Sí 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sí 
AMR (tasa múltiples adaptación)|Sí
AES (SMPTE 331 M y 302 M, AES3-2003)        |No 
Dolby® E                                    |No 
Dolby® Digital (AC3)                        |No 
Dolby® Digital Plus (E-AC3)                 |No 


##<a name="output-formats-and-codecs"></a>Formatos de salida y códecs

En la siguiente tabla enumera los formatos de archivo y códecs son compatibles para la exportación.


Formato de archivo|Códec de vídeo|Códec de audio
---|---|---
MP4 <br/><br/>(incluidos los contenedores de velocidad múltiple MP4) |H.264 (alta, principal y perfiles de línea base)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2 TS |H.264 (alta, principal y perfiles de línea base)|AAC-LC, HE-AAC v1, HE-AAC v2 



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vea también

[Codificación de contenido a petición con servicios multimedia de Azure](media-services-encode-asset.md)

[Cómo codificar con Media Encoder estándar](media-services-dotnet-encode-with-media-encoder-standard.md)
