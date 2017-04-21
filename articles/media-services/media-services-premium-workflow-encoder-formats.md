<properties 
    pageTitle="Formatos de flujo de trabajo de Media Encoder Premium y códecs | Microsoft Azure" 
    description="Este tema ofrece una descripción general de los formatos de formatos de flujo de trabajo de Premium de codificador multimedia y códecs" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erik43" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"    
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-premium-workflow-formats-and-codecs"></a>Formatos de flujo de trabajo de Media Encoder Premium y códecs


>[AZURE.NOTE]Para las preguntas de codificador premium, correo electrónico mepd en Microsoft.com.
>
>Procesador de medios de flujo de trabajo de Media Encoder Premium analizado en este tema no está disponible en China. 

Este documento contiene una lista de entrada y los formatos de archivo de salida y códecs compatibles con la versión preliminar pública del codificador de **Flujo de trabajo de Media Encoder Premium** .

[Formatos de entrada Worflow de Premium de codificador multimedia y códecs](#input_formats)

[Media Encoder Premium Worflow formatos y códecs](#output_formats)

**Flujo de trabajo de Media Encoder Premium** admite subtítulos descritas en [esta](#closed_captioning) sección. 


##<a id="input_formats"></a>Formatos y códecs de entrada de flujo de trabajo de Media Encoder Premium

En la sección siguiente muestra los formatos de códecs y archivo que admite este procesador de medios como entrada.

###<a name="input-containerfile-formats"></a>Formatos de archivo o contenedor de entrada

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- Flujos de transporte MPEG-2
- Secuencias de sistema MPEG-2
- MPEG-4/MP4
- ASF de Windows Media
- AVI (sin comprimir de 8 bits/10 bits)

###<a name="input-video-codecs"></a>Códecs de vídeo de entrada

- AVC 8/10 bits, hasta 4:2:2, incluidos AVCIntra
- DNxHD Avid (en MXF)
- DVCPro/DVCProHD (en MXF)
- JPEG2000
- MPEG-2 (hasta 422 perfil y de alto nivel, incluida la variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)
- MPEG-1
- Windows Media Video/VC-1

###<a name="input-audio-codecs"></a>Códecs de Audio de entrada

- AES (SMPTE 331 M y 302 M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, HE-AAC y AAC-HEv2; hasta 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio
- WAV/PCM
 
##<a id="output_format"></a>Formatos de salida de flujo de trabajo de Media Encoder Premium y códecs

En la sección siguiente se enumera los formatos de códecs y archivo que son compatibles con como resultado de este procesador de medios.

###<a name="output-containerfile-formats"></a>Formatos de archivo o contenedor de salida

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM y AS02)
- DPP (incluidos AS11)
- GXF
- MPEG-4/MP4
- ASF de Windows Media
- AVI (sin comprimir de 8 bits/10 bits)
- Smooth Streaming File Format (PIFF 1,3)
- MPEG-TS 


###<a name="output-video-codecs"></a>Códecs de vídeo de salida

- AVC (H.264; 8 bits; hasta el perfil de alto nivel HD de Ultra K 5.2; 4; Dentro de AVC)
- DNxHD Avid (en MXF)
- DVCPro/DVCProHD (en MXF)
- MPEG-2 (hasta 422 perfil y de alto nivel, incluida la variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)
- MPEG-1
- Windows Media Video/VC-1
- Creación de miniaturas de JPEG

###<a name="output-audio-codecs"></a>Códecs de Audio de salida

- AES (SMPTE 331 M y 302 M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) hasta 7.1
- AAC (AAC-LC, HE-AAC y AAC-HEv2; hasta 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio

##<a id="closed_captioning"></a>Compatibilidad con transcripción

En recopilar, es compatible con **El flujo de trabajo de Media Encoder Premium** :

1. Archivos de control de código fuente
1. Archivos de SMPTE TT
1. CEA-608/CEA-708-ejecutan como datos de usuario (mensajes SEI de secuencias H.264 primaria, ATSC/53, SCTE20) o realiza como datos auxiliares en archivos MXF/GXF
1. Archivos STL subtítulo

En el resultado, están disponibles las siguientes opciones:

1. CEA 608 CEA 708 traducción
1. Pasar CEA-608/CEA-708 (incrustados en mensajes SEI de secuencias de primaria H.264 o lleva a los datos como auxiliares en archivos MXF)
1. CONTROL DE CÓDIGO FUENTE
1. Texto temporizado de SMPTE (de origen CEA 608 por SMPTE RP2052, incluida la creación de archivos DFXP)
1. Archivo de iniciar subtítulo
1. Secuencias de DVB subtítulo

Nota: todos los formatos de salida anteriores no son compatibles para la entrega a través de transmisión de Azure Media Services.

##<a name="known-issues"></a>Problemas conocidos

Si el vídeo de entrada no contiene los subtítulos, el resultado activo contendrá un archivo TTML vacío. 


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
