<properties 
    pageTitle="Preguntas más frecuentes | Microsoft Azure" 
    description="Preguntas más frecuentes (P+f)" 
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


#<a name="frequently-asked-questions"></a>Preguntas más frecuentes

##<a name="general-ams-faqs"></a>Preguntas frecuentes de AMS general

P: ¿cómo escala indización?

R: las unidades reservadas son los mismos para las tareas de codificación e indización. Siga las instrucciones sobre [cómo codificación reservado unidades de escala](media-services-scale-media-processing-overview.md). **Nota** que el rendimiento del indizador no se ve afectado por tipo de unidad reservadas.

P: ¿puedo cargado, codificado y había publicado un vídeo. ¿Cuál es el motivo por el vídeo no se reproducen cuando intento lo transmita?

R: uno de los motivos más comunes es que no tiene al menos una unidad streaming reservada asignada en el extremo streaming desde el que está intentando reproducción.  Siga las instrucciones sobre [cómo transmisión reservado unidades de escala](media-services-portal-scale-streaming-endpoints.md).

P: ¿puedo hacer composición en una secuencia live?

R: composición en directo secuencias actualmente no está disponible en Azure Media Services, por lo que tendrá que redacta previamente en el equipo.

P: ¿puedo usar CDN Azure con Live transmisión?

R: Media Services admite la integración con Azure CDN (para obtener más información, consulte [cómo administrar transmisión extremos en una cuenta de servicios multimedia](media-services-portal-manage-streaming-endpoints.md)).  Puede usar Live transmisión con CDN. Azure Media Services proporciona salidas Streaming, HLS y MPEG guión suave. Todos estos formatos utilizan HTTP para transferir datos y obtienen beneficios de almacenamiento en caché HTTP. En directo transmisión real de los datos de audio y vídeo se dividen en fragmentos y obtengan caché este fragmentos individuales en CDN. Solo las necesidades de datos que se actualice son los datos manifiestos. CDN actualiza periódicamente los datos.

P: ¿es Media de Azure services admite almacenar imágenes?

R: si solo desea para almacenar imágenes JPEG o PNG, debe conservarlos de almacenamiento de blobs de Windows Azure. No es necesario para colocarlos en su cuenta de Media Services, a menos que desee mantenerlos asociado con el vídeo o Audio activos. O bien, si es posible que tenga una necesidad de usar las imágenes como superposiciones del codificador de vídeo. Media Encoder estándar es compatible con imágenes de capas encima de vídeos, y eso es se muestran JPEG y PNG como admite formatos de entrada. Para obtener más información, consulte [Crear superposiciones](media-services-custom-mes-presets-with-dotnet.md#overlay).

P: ¿¿Cómo puedo copiar activos desde una cuenta de Media Services a otro.

R: para copiar los activos de una cuenta de Media Services a otro mediante .NET, utilizar el método de extensión [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) disponible en el repositorio de [.NET SDK extensiones de Azure Media Services](https://github.com/Azure/azure-sdk-for-media-services-extensions/) . Para obtener más información, consulte [esta](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) conversación del foro.

P: ¿qué son los caracteres admitidos para nombrar archivos cuando se trabaja con AMS?

R: Media Services usa el valor de la propiedad IAssetFile.Name al generar direcciones URL para la transmisión de contenido (por ejemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, no se permite la codificación de porcentaje. El valor de la propiedad **nombre** no puede tener cualquiera de los siguientes [caracteres por ciento codificación-reservados](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Además, sólo puede haber una '.' para la extensión de nombre de archivo.


P: ¿Cómo puedo conectar utilizando el resto?

R: después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de servicios de medios. Debe realizar las siguientes llamadas para el nuevo URI tal como se describe en [Conectar con Media Services con la API de REST](media-services-rest-connect-programmatically.md). 


P: ¿Cómo puedo girar un vídeo durante el proceso de codificación.

R: el [Media Encoder estándar](media-services-dotnet-encode-with-media-encoder-standard.md) es compatible con rotación de ángulos de 90, 180 o 270. El comportamiento predeterminado es "Automático" cuando intenta detectar los metadatos de rotación en el archivo MP4 o .mov entrante y compensación. El siguiente elemento de **orígenes** a uno de los json preestablecidos definido [aquí](http://msdn.microsoft.com/library/azure/mt269960.aspx)se incluyen:
    
    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...




##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
