<properties 
    pageTitle="Cómo crear un procesador multimedia | Microsoft Azure" 
    description="Obtenga información sobre cómo crear un componente de procesador multimedia para codificar, convertir formato, cifrar o descifrar contenido multimedia de Azure Media Services. Ejemplos de código están escritos en C# y usan el SDK de servicios de medios para .NET." 
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

##<a name="get-media-processor"></a>Obtener procesador de medios

El método siguiente muestra cómo obtener una instancia de procesador de medios. El ejemplo de código, supone el uso de una variable de nivel de módulo denominado **_context** hacer referencia el contexto de servidor, como se describe en la sección [Cómo: conectarse a servicios de medios mediante programación](media-services-dotnet-connect-programmatically.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
        return processor;
    }


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo obtener una instancia de procesador de medios, vaya al tema [cómo codificar un activo](media-services-dotnet-encode-with-media-encoder-standard.md) que le mostrará cómo usar el estándar de Media Encoder para codificar un activo.


