<properties
    pageTitle="Cómo recortar vídeo | Microsoft Azure"
    description="Este artículo le muestra cómo recortar vídeos con Media Encoder estándar."
    services="media-services"
    documentationCenter=""
    authors="anilmur"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"  
    ms.author="anilmur;juliako;"/>

# <a name="crop-videos-with-media-encoder-standard"></a>Vídeos de recorte con Media Encoder estándar

Puede usar Media Encoder estándar (MES) para recortar la entrada de vídeo. Recorte es el proceso de selección de una ventana rectangular dentro del marco del vídeo y codificación solo los píxeles desde la ventana. El siguiente diagrama ayuda a ilustrar el proceso.

![Recortar un vídeo](./media/media-services-crop-video/media-services-crop-video01.png)

Suponga que tiene como entrada de un vídeo que tiene una resolución de 1920 x 1080 píxeles (relación de aspecto 16:9), pero tiene barras negras (pilares cuadros) a la izquierda y derecha, para que solo una ventana de 4:3 o 1440 x 1080 píxeles contiene vídeo activa. Puede usar el MES para recortar o editar fuera de las barras negras y codificar la región 1440 x 1080.

MES de recorte es una fase de preprocesamiento para aplicarán los parámetros de recorte en la programación de codificación para el vídeo de entrada original. Codificación es una fase subsiguientes y la configuración de ancho y alto se aplica a la *procesan previamente* vídeo y no a vídeo original. Cuando diseñe su programación debe hacer lo siguiente: (a) seleccione los parámetros de recorte basándose en el vídeo de entrada original y (b) su codificar configuración basada en el vídeo recortado. Si no coinciden el codificar configuración al vídeo recortada, el resultado no se según lo esperado.

El tema [siguiente](media-services-advanced-encoding-with-mes.md#encoding_with_dotnet) muestra cómo crear un trabajo de codificación con el MES y cómo especificar un valor de la tarea de codificación personalizado. 

## <a name="creating-a-custom-preset"></a>Crear una programación personalizada

En el ejemplo que se muestra en el diagrama:

1. Entrada original es 1920 x 1080
1. Necesita recortada a un resultado de 1440 x 1080, centrado en el marco de entrada
1. Esto significa un desplazamiento X de (1920-1440) / 2 = 240 e ía de desplazamiento de cero
1. El ancho y alto del rectángulo de recorte son 1440 y 1080, respectivamente
1. En la fase de codificación, la ask es producir tres capas, son resoluciones 1440 x 1080, 960 x 720 y 480 x 360, respectivamente

###<a name="json-preset"></a>Valores predeterminados JSON


    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


##<a name="restrictions-on-cropping"></a>Restricciones de recorte

La característica de recorte está pensada para ser manual. Necesarias para cargar el vídeo de entrada en una herramienta de edición adecuada que permite seleccionar marcos de interés, coloque el cursor para determinar desplazamientos para el rectángulo de recorte para determinar la programación de codificación que está optimizada para que etcetera vídeo, determinado. Esta característica no está diseñada para habilitar cosas como: detección automática y eliminación de bordes negros de panorámica/pantalla normal en la entrada de vídeo.

Siguientes restricciones se aplican a la característica de recorte. Si no se cumplen estos, la tarea de codificación puede fallar o generar un resultado inesperado.

1. Tienen las coordenadas y el tamaño del rectángulo de recorte para que quepa en el vídeo de entrada
1. Como se mencionó anteriormente, el ancho y alto en la configuración de codificar tienen que coincidir con el vídeo recortado
1. Recorte se aplica a los vídeos que se capturan en modo horizontal (es decir, no se aplica a los vídeos grabados con un smartphone mantenidos verticalmente o en modo vertical)
1. Funciona mejor con vídeo progresivo genera con píxeles cuadrados

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Siguiente paso
 
Vea Azure Media Services rutas de aprendizaje que le ayudarán a conocer las características excelentes ofrecidas por AMS.  

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
