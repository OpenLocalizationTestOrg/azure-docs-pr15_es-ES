<properties 
    pageTitle="Cómo crear un procesador multimedia | Microsoft Azure" 
    description="Obtenga información sobre cómo crear un componente de procesador multimedia para codificar, convertir formato, cifrar o descifrar contenido multimedia de Azure Media Services." 
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
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="how-to-get-a-media-processor-instance"></a>Cómo: obtener una instancia de procesador de medios


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)

##<a name="overview"></a>Información general

Servicios de Media que un procesador multimedia es un componente que trata de una tarea de procesamiento específico, como la codificación, formato conversión, cifrar o descifrar contenido multimedia. Normalmente, se crea un procesador multimedia cuando se crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

La tabla siguiente proporciona el nombre y la descripción de cada procesador de medios disponibles.

Nombre del procesador de medios|Descripción|Más información
---|---|---
Media Encoder Standard|Proporciona características estándares de codificación a petición. |[Información general y comparación de Azure en petición multimedia codificadores](media-services-encode-asset.md)
Flujo de trabajo de Media Encoder Premium|Le permite realizar tareas de codificación con el flujo de trabajo de Media Encoder Premium.|[Información general y comparación de Azure en petición multimedia codificadores](media-services-encode-asset.md)
Indizador multimedia de Azure| Permite hacer posible la búsqueda de contenido y archivos multimedia, así como para generar pistas de subtítulos cerradas y palabras clave.|[Indizador multimedia de Azure](media-services-index-content.md)
Azure Media Hyperlapse (vista previa)|Permite redondear "resaltes" en el vídeo con vídeo estabilización. También puede acelerar el contenido en un clip de consumible.|[Hyperlapse multimedia de Azure](media-services-hyperlapse-content.md)
Codificador multimedia de Azure|Depreciación
Descifrado de almacenamiento| Depreciación|
Empaquetador multimedia de Azure|Depreciación|
Cifrador multimedia de Azure|Depreciación|

##<a name="get-mediaprocessor"></a>Obtener MediaProcessor

>[AZURE.NOTE] Cuando se trabaja con la API de REST de servicios de medios, se aplican las consideraciones siguientes:
>
>Al acceder a las entidades de Media Services, debe establecer los campos de encabezado específico y los valores en las solicitudes HTTP. Para obtener más información, consulte [configuración de desarrollo de API de REST de servicios de multimedia](media-services-rest-how-to-use.md).

>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de servicios de medios. Debe realizar las siguientes llamadas para el nuevo URI tal como se describe en [Conectar con Media Services con la API de REST](media-services-rest-connect-programmatically.md). 


La siguiente llamada resto muestra cómo obtener una instancia de procesador multimedia por nombre (en este caso, **Media Encoder estándar**). 



    
Solicitar:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net
    
Respuesta:
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo obtener una instancia de procesador de medios, vaya al tema [cómo codificar un activo](media-services-rest-get-started.md) que le mostrará cómo usar el estándar de Media Encoder para codificar un activo.
