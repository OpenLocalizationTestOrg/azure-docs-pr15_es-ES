<properties 
    pageTitle="Información general y comparación de Azure en codificadores de media de petición | Microsoft Azure" 
    description="Este tema ofrece información general y comparación de Azure en codificadores de media de petición." 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>

#<a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Información general y comparación de Azure en codificadores de media de petición

##<a name="encoding-overview"></a>Información general de codificación

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

Este artículo ofrece una breve introducción a petición codificadores multimedia y proporciona vínculos a artículos que proporcionan información más detallada. El tema también proporciona la comparación de los codificadores.

Tenga en cuenta que de forma predeterminada cada cuenta Media Services puede tener una tarea de codificación activada en un momento. Puede reservar codificación unidades que permiten tener varias tareas de codificación ejecutando simultáneamente, uno para cada unidad reservada codificación que comprarlo. Para obtener más información, vea [unidades de codificación de escala](media-services-scale-media-processing-overview.md).

##<a name="media-encoder-standard"></a>Media Encoder Standard

###<a name="how-to-use"></a>Cómo usar

[Cómo codificar con Media Encoder estándar](media-services-dotnet-encode-with-media-encoder-standard.md)

###<a name="formats"></a>Formatos

[Formatos y códecs](media-services-media-encoder-standard-formats.md)

###<a name="presets"></a>Valores predeterminados

Media Encoder estándar está configurado con uno del codificador valores preestablecidos se describe [a continuación](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Metadatos de entrada y salido

Los metadatos de entrada codificadores se describen [aquí](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Los metadatos de salida codificadores se describen [aquí](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###<a name="generate-thumbnails"></a>Generar miniaturas

Para obtener más información, vea [cómo generar miniaturas utilizando Media Encoder estándar](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###<a name="trim-videos-clipping"></a>Recortar vídeos (recorte)

Para obtener más información, vea [cómo recortar vídeos con Media Encoder estándar](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###<a name="create-overlays"></a>Crear superposiciones

Para obtener más información, vea [cómo crear superposiciones con Media Encoder estándar](media-services-custom-mes-presets-with-dotnet.md#overlay).

###<a name="see-also"></a>Vea también

[El blog de Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##<a name="media-encoder-premium-workflow"></a>Flujo de trabajo de Media Encoder Premium

###<a name="overview"></a>Información general

[Introducción a Premium codificación en Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###<a name="how-to-use"></a>Cómo usar

Flujo de trabajo de Media Encoder Premium se configura mediante flujos de trabajo complejos. Archivos de flujo de trabajo pueden crearse y actualizan mediante la herramienta de [Diseñador de flujo de trabajo](media-services-workflow-designer.md) .

[Cómo usar Premium codificación en Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###<a name="known-issues"></a>Problemas conocidos

Si el vídeo de entrada no contiene los subtítulos, el resultado activo contendrá un archivo TTML vacío. 


##<a id="compare_encoders"></a>Comparar codificadores

###<a id="billing"></a>Contador de facturación usado por cada codificador

Nombre del procesador de medios|Precios aplicable|Notas
---|---|---
**Media Encoder Standard** |CODIFICADOR|Codificación de tareas se cargará según el tamaño del resultado activo, en GB a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR.
**Flujo de trabajo de Media Encoder Premium** |CODIFICADOR PREMIUM|Codificación de tareas se cargará según el tamaño del resultado activo, en GB a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR PREMIUM.


Esta sección compara las capacidades de codificación de **Media Encoder estándar** y **Flujo de trabajo de Media Encoder Premium**.


###<a name="input-containerfile-formats"></a>Formatos de archivo o contenedor de entrada

Formatos de archivo o contenedor de entrada|Media Encoder Standard|Flujo de trabajo de Media Encoder Premium
---|---|---
Adobe® Flash® F4V           |Sí|Sí
MXF/SMPTE 377M              |Sí|Sí
GXF                         |Sí|Sí
Flujos de transporte MPEG-2    |Sí|Sí
Secuencias de sistema MPEG-2      |Sí|Sí
MPEG-4/MP4                  |Sí|Sí
ASF de Windows Media           |Sí|Sí
AVI (sin comprimir de 8 bits/10 bits)|Sí|Sí
3GPP y 3GPP2                  |Sí|No
Smooth Streaming File Format (PIFF 1,3)|Sí|No
[Recording(DVR-MS) de vídeo Digital de Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Sí|No
Matroska/WBEM               |Sí|No
QuickTime (.mov) |Sí|No

###<a name="input-video-codecs"></a>Códecs de vídeo de entrada

Códecs de vídeo de entrada|Media Encoder Standard|Flujo de trabajo de Media Encoder Premium
---|---|---
AVC 8/10 bits, hasta 4:2:2, incluidos AVCIntra   |4 de 8 bits: 2:0 y 4:2:2|Sí
DNxHD Avid (en MXF)                                 |Sí|Sí
DVCPro/DVCProHD (en MXF)                            |Sí|Sí
JPEG2000                                            |Sí|Sí
MPEG-2 (hasta 422 perfil y de alto nivel, incluida la variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)|Perfil hasta 422|Sí
MPEG-1                                              |Sí|Sí
Windows Media Video/VC-1                            |Sí|Sí
Canopus HQ/HQX                                      |No|No
MPEG-4, parte 2                                       |Sí|No
[Theora](https://en.wikipedia.org/wiki/Theora)      |Sí|No
Apple ProRes 422    |Sí|No
Apple ProRes 422 LT |Sí|No
Apple ProRes 422 HQ |Sí|No
Apple ProRes Proxy|Sí|No
Apple ProRes 4444 |Sí|No
Apple ProRes 4444 XQ |Sí|No

###<a name="input-audio-codecs"></a>Códecs de Audio de entrada

Códecs de Audio de entrada|Media Encoder Standard|Flujo de trabajo de Media Encoder Premium
---|---|---
AES (SMPTE 331 M y 302 M, AES3-2003)        |No|Sí
Dolby® E                                    |No|Sí
Dolby® Digital (AC3)                        |No|Sí
Dolby® Digital Plus (E-AC3)                 |No|Sí
AAC (AAC-LC, HE-AAC y AAC-HEv2; hasta 5.1)|Sí|Sí
MPEG Layer 2|Sí|Sí
MP3 (MPEG-1 Audio Layer 3)|Sí|Sí
Windows Media Audio|Sí|Sí
WAV/PCM|Sí|Sí
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sí|No
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format)) |Sí|No
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sí|No


###<a name="output-containerfile-formats"></a>Formatos de archivo o contenedor de salida

Formatos de archivo o contenedor de salida|Media Encoder Standard|Flujo de trabajo de Media Encoder Premium
---|---|---
Adobe® Flash® F4V|No|Sí
MXF (OP1a, XDCAM y AS02)|No|Sí
DPP (incluidos AS11)|No|Sí
GXF|No|Sí
MPEG-4/MP4|Sí|Sí
MPEG-TS|Sí|Sí
ASF de Windows Media|No|Sí
AVI (sin comprimir de 8 bits/10 bits)|No|Sí
Smooth Streaming File Format (PIFF 1,3)|No|Sí

###<a name="output-video-codecs"></a>Códecs de vídeo de salida

Códecs de vídeo de salida|Media Encoder Standard|Flujo de trabajo de Media Encoder Premium
---|---|---
AVC (H.264; 8 bits; hasta el perfil de alto nivel HD de Ultra K 5.2; 4; Dentro de AVC)|Solo 8 bit 4:2:0|Sí
DNxHD Avid (en MXF)|No|Sí
MPEG-2 (hasta 422 perfil y de alto nivel, incluida la variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)|No|Sí
MPEG-1|No|Sí
Windows Media Video/VC-1|No|Sí
Creación de miniaturas de JPEG|No|Sí

###<a name="output-audio-codecs"></a>Códecs de Audio de salida

Códecs de Audio de salida|Media Encoder Standard|Flujo de trabajo de Media Encoder Premium
---|---|---
AES (SMPTE 331 M y 302 M, AES3-2003)|No|Sí
Dolby® Digital (AC3)|No|Sí
Dolby® Digital Plus (E-AC3) hasta 7.1|No|Sí
AAC (AAC-LC, HE-AAC y AAC-HEv2; hasta 5.1)|Sí|Sí
MPEG Layer 2|No|Sí
MP3 (MPEG-1 Audio Layer 3)|No|Sí
Windows Media Audio|No|Sí


##<a name="error-codes"></a>Códigos de error  

La siguiente tabla enumeran los códigos de error que podrían devolverse en caso de que se produjo un error durante la ejecución de la tarea de codificación.  Para obtener detalles del error en el código de .NET, use la clase [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . Para obtener detalles del error en el código del resto, use la API de REST de [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) .

ErrorDetail.Code|Las posibles causas de error
-----|-----------------------
Desconocido| Error desconocido al ejecutar la tarea
ErrorDownloadingInputAssetMalformedContent|Categoría de errores que cubre los errores de descarga de entrada activo como nombres de archivo incorrecto, cero archivos de longitud incorrectos formatos y así sucesivamente.
ErrorDownloadingInputAssetServiceFailure|Categoría de errores que cubre los problemas en el lado de servicio - errores de red o almacenamiento de ejemplo al descargar.
ErrorParsingConfiguration|Categoría de errores de tareas donde <see cref="MediaTask.PrivateData"/> (configuración) no es válida, por ejemplo la configuración no es un sistema válido preestablecidos o contiene XML no válido.
ErrorExecutingTaskMalformedContent|Categoría de errores durante la ejecución de la tarea donde problemas dentro de los archivos multimedia entrada producen errores.
ErrorExecutingTaskUnsupportedFormat|Categoría de errores donde el procesador multimedia no puede procesar los archivos proporcionados - media formato no compatible o no coincide con la configuración. Por ejemplo, intenta generar un resultado solo de audio de un activo, que tiene sólo vídeo
ErrorProcessingTask|Categoría de otros errores que se encuentra el procesador multimedia durante el procesamiento de la tarea que están relacionados con el contenido.
ErrorUploadingOutputAsset|Categoría de errores al cargar el activo de salida
ErrorCancelingTask|Categoría de errores para cubrir errores al intentar cancelar la tarea
TransientError|Categoría de errores para cubrir problemas temporales (ej. problemas de red temporales con el almacenamiento de Azure)


Para obtener ayuda desde el equipo de **Media Services** , abra una [incidencia de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-articles"></a>Artículos relacionados

- [Realizar tareas de codificación avanzadas personalizando preestablecidos Media Encoder estándar](media-services-custom-mes-presets-with-dotnet.md)
- [Cuotas y limitaciones](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
