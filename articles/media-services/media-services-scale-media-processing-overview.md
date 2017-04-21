<properties
    pageTitle="Información general del procesamiento de Media de ajuste de escala | Microsoft Azure"
    description="Este tema es una descripción general de escalado de procesamiento multimedia con Azure Media Services."
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
    ms.date="08/29/2016"
    ms.author="juliako"/>


# <a name="scaling-media-processing-overview"></a>Información general del procesamiento de Media de ajuste de escala

Esta página le ofrece una descripción general de cómo y por qué escalar procesamiento de medios. 

## <a name="overview"></a>Información general

Una cuenta de Media Services está asociada con un tipo de unidad reservadas, que determina la velocidad con que se procesan los medios de procesamiento de tareas. Puede elegir entre los siguientes tipos de unidad reservadas: **S1**, **S2**o **S3**. Por ejemplo, el mismo trabajo codificación se ejecuta más rápidamente cuando se usa la comparación de tipo de unidad **S2** reservado para el tipo de **S1** . Para obtener más información, vea los [Tipos de unidad reservados](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Además de especificar el tipo de unidad reservadas, puede especificar para aprovisionar su cuenta con unidades reservados. El número de unidades reservados aprovisionados determina el número de tareas de elementos multimedia que se puede procesar simultáneamente en una cuenta determinada. Por ejemplo, si su cuenta tiene cinco unidades reservados, tareas de cinco medios se ejecutará simultáneamente siempre como hay tareas que deben procesarse. Las tareas restantes esperan en la cola y se seleccionar para procesar secuencialmente cuando termina de una tarea en ejecución. Si no tiene una cuenta de cualquier unidades reservados que se aprovisione, a continuación, las tareas se recogen secuencialmente. En este caso, el tiempo de espera entre la finalización de una tarea y el inicio siguiente dependerá la disponibilidad de recursos del sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Elegir entre tipos diferentes de unidad reservadas

La siguiente tabla le ayuda a tomar la decisión al elegir entre distintas velocidades de codificación. También se proporcionan algunos casos de referencia y se proporciona direcciones URL de SA que puede usar para descargar los vídeos en los que puede realizar sus propias pruebas:

Escenarios|**S1**|**S2**|**S3**|
----------|------------|----------|------------
Caso de uso| Codificación de velocidad única. <br/>Archivos en SD o por debajo de resoluciones, tiempo no confidencial, bajo costo.|Velocidad única y varios codificación de velocidad.<br/>Uso normal de codificación SD y HD. |Velocidad única y varios codificación de velocidad.<br/>Vídeos de resolución HD y 4K completas. Tiempo de codificación de respuesta más rápida, confidencial. 
Banco de pruebas|[Archivo de entrada: 5 minutos largas 640x360p en 29,97 marcos por segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Codificación a una velocidad de bits único archivo MP4, con la misma resolución, tarda aproximadamente 11 minutos.|[Archivo de entrada: 5 minutos largas 1280x720p en 29,97 marcos por segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>Codificación con "H264 solo velocidad 720p" preestablecidos tarda aproximadamente 5 minutos.<br/><br/>Codificación con "H264 velocidad varias 720p" preestablecidos tardan aproximadamente 11,5 minutos.|[Archivo de entrada: 5 minutos largas 1920x1080p en 29,97 marcos por segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>Codificación con "H264 solo velocidad 1080p" preestablecidos tarda aproximadamente 2,7 minutos.<br/><br/>Codificación con "H264 velocidad varias 1080p" preestablecidos tardan aproximadamente 5,7 minutos.

##<a name="considerations"></a>Consideraciones

>[AZURE.IMPORTANT] Revisar las consideraciones descritas en esta sección.  

- Unidades reservados trabajan en paralelo todos procesamiento de medios, incluida la indización trabajos mediante indizador de Media de Azure.  Sin embargo, a diferencia de codificación, trabajos de indexación no se procesan más rápido con más rápidos reservados unidades.

- Si usa el grupo compartido, es decir, sin las unidades reservados, a continuación, las tareas de codificar tienen el mismo rendimiento como con S1 RUs. Sin embargo, no hay ningún límite superior a la vez pueden dedica a las tareas en estado en cola y en cualquier momento, una tarea es estar ejecutándose.

- Los centros de datos siguientes no ofrecen el tipo de unidad **S2** reservados: sur de Brasil, India oeste, India Central y India sur.

- Los centros de datos siguientes no ofrecen el tipo de unidad **S3** reservados: sur de Brasil, India oeste, India Central.

- El número máximo de unidades especificadas para el período de 24 horas se utiliza para calcular el costo.


##<a name="quotas-and-limitations"></a>Cuotas y limitaciones

Para obtener información sobre las cuotas y cómo abrir una incidencia de soporte técnico y limitaciones, consulte [cuotas y limitaciones](media-services-quotas-and-limitations.md).

##<a name="next-step"></a>Siguiente paso

Obtener la tarea de procesamiento de medios escalado con una de estas tecnologías: 

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-scale-media-processing.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
