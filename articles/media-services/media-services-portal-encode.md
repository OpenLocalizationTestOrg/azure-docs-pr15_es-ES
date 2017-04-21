<properties
    pageTitle="Codificar un activo mediante Media Encoder estándar con el portal de Azure | Microsoft Azure"
    description="Este tutorial le guiará por los pasos de codificación de un activo mediante Media Encoder estándar con el portal de Azure."
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
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Codificar un activo mediante Media Encoder estándar con el portal de Azure

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 

Cuando se trabaja con Azure Media Services adaptación velocidad transmisión uno de los escenarios más comunes ofrece a sus clientes. Media Services es compatible con la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe). Para preparar los vídeos adaptación velocidad transmisión, debe codificar el vídeo de origen en velocidad de entre varios archivos. Debe usar el codificador **Multimedia codificador estándar** para codificar los vídeos.  

Media Services también proporciona embalaje dinámico que le permite ofrecer su MP4s velocidad de entre varios de los siguientes formatos de streaming: guión MPEG, HLS, Smooth Streaming o HDS, sin tener que volver a empaquetar en estos formatos de transmisión. Con embalaje dinámico solo debe almacenar y de pago para los archivos en formato de almacenamiento único y Media Services generará y servir la respuesta adecuada en función de las solicitudes de un cliente.

Para aprovechar las ventajas de embalaje dinámico, debe hacer lo siguiente:

- Codificar el archivo de origen en un conjunto de archivos de velocidad múltiple MP4 (los pasos de codificación se muestran más adelante en esta sección).
- Obtener al menos una unidad de transmisión del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido. Para obtener más información, vea [configurar transmisiones de los extremos](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

Para cambiar la escala de procesamiento de medios, vea [este](media-services-portal-scale-media-processing.md) tema.

## <a name="encode-with-the-azure-portal"></a>Codificar con el portal de Azure

Esta sección describe los pasos que puede realizar para codificar el contenido con Media Encoder estándar.

1.  En el [portal de Azure](https://portal.azure.com/), seleccione su cuenta de Azure Media Services.
2.  En la ventana **configuración** , seleccione **activos**.  
2.  En la ventana de **activos** , seleccione el recurso que desea codificar.
3.  Presione el botón **codificar** .
4.  En la ventana de **codificar un activo** , seleccione el procesador "Media Encoder estándar" y una programación. Por ejemplo, si conoce el vídeo de entrada con una resolución de 1920 x 1080 píxeles, a continuación, podría usar el "H264 velocidad varias 1080p" preestablecido. Para obtener más información sobre los valores preestablecidos, consulte [este](https://msdn.microsoft.com/library/azure/mt269960.aspx) artículo: es importante seleccionar el ajuste preestablecido que es más apropiado para el vídeo de entrada. Si tiene un vídeo de baja resolución (640 x 360), que no debería utilizar el valor predeterminado "H264 velocidad varias 1080p" preestablecido.
    
    Para facilitar la administración, tiene una opción de editar el nombre del activo de salida y el nombre de la tarea.
        
    ![Codificar activos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Pulse **crear**.


##<a name="next-step"></a>Siguiente paso

Puede supervisar el progreso de trabajo codificación con el portal de Azure, como se describe en [este](media-services-portal-check-job-progress.md) artículo.  

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


