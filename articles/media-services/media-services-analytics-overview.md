<properties
    pageTitle="Información general de análisis de servicios multimedia de Azure | Microsoft Azure"
    description="Azure Media Services ofrece la versión preliminar pública de Azure Media análisis, una colección de voz y el equipo servicios a escala empresarial, cumplimiento, seguridad y alcance global. Servicios de análisis de medios Azure creados mediante los componentes principales de la plataforma de Azure Media Services y, por tanto, están listos para tratar los medios de procesamiento a escala en primer día. "
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/24/2016"   
    ms.author="milanga;juliako;johndeu"/>

# <a name="azure-media-services-analytics-overview"></a>Información general de análisis de servicios multimedia de Azure

##<a name="overview"></a>Información general

Más organizaciones y empresas están adoptando vídeo como el medio preferido para formar a los empleados, comuníquese con sus clientes y las funciones de negocio de documento. Nube informática hace que sea eficaz para almacenar, transmitir y tener acceso a estos archivos multimedia de gran tamaño, pero que las empresas crecen su biblioteca de contenido de vídeo, debe tener un medio igualmente eficaz para extracción nueva información de vídeo con el fin de crear más significativo personalizadas interacciones con su público y llevar su negocio al siguiente nivel.

Para satisfacer esta necesidad creciente en el mercado, Azure Media Services ofrece análisis de medios, una colección de componentes de voz y visión (en la escala de empresa, cumplimiento, seguridad y alcance global) que hacen que sea más fácil para las organizaciones y empresas para obtener información útil de sus archivos de vídeo. Servicios de análisis de medios Azure creados mediante los componentes principales de la plataforma de Azure Media Services y, por tanto, están listos para tratar los medios de procesamiento a escala en primer día.

Análisis de Azure Media permiten a los desarrolladores rápida introducción a las capacidades de visión para vídeo en escala limitada y ofrecer esta funcionalidad avanzada en aplicaciones. Análisis de Azure Media está diseñado para ser usada por entornos empresariales con escala completa, cumplimiento, seguridad y alcance global necesarios para grandes organizaciones.

El siguiente diagrama muestra **El análisis de medios** y otras partes principales de la plataforma de Media Services. 

![Flujo de trabajo VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

Procesadores de media de análisis de medios generan archivos MP4 o archivos de JSON. Si un procesador multimedia produce un archivo MP4, puede descargar el archivo cada vez. Si un procesador multimedia genera un archivo JSON, puede descargar el archivo desde el almacenamiento de blobs de Windows Azure. 

## <a name="azure-media-analytics-services"></a>Servicios de análisis de medios Azure

- **Indizador** : Azure Media indizador permite que el contenido para la búsqueda, así como generar pistas de subtítulos cerradas. Azure Media Services había lanzado **Azure Media indizador 2 Preview** con rápida indización y más amplio soporte de idioma. Idiomas admitidos incluyen inglés, español, francés, alemán, italiano, chino, portugués y árabe. Para obtener información detallada y ejemplos, vea [vídeos de proceso con Azure Media indizador 2](media-services-process-content-with-indexer2.md)
 
- **Hyperlapse** : Microsoft Hyperlapse es el resultado de más de 20 años de investigación de visión de equipo en Microsoft Research (MSR), combinando estabilización vídeo y supresión de tiempo para crear vídeos rápidos, consumibles, bonitas desde su formato largo contenido. Además de crear períodos, también puede usar Hyperlapse para crear vídeos estables de vídeos irregular genera a través de teléfonos móviles y videograbadoras. Para obtener información detallada y ejemplos, vea [Hyperlapse los archivos multimedia con Azure Media Hyperlapse](media-services-hyperlapse-content.md)
 
- **Detección de movimiento** : puede usar este servicio para detectar movimiento en un vídeo con fondos de diseño de fondo. Esto es ideal para los clientes que desea comprobar falsas en eventos de movimiento detectados por cámaras supervisión en las fuentes de vídeo de control. Para obtener información detallada y ejemplos, consulte [Detección de movimiento para análisis de multimedia de Azure](media-services-motion-detection.md).
 
- **Detección de cara y emociones cara** – con este servicio, puede detectar caras de personas y sus emociones, incluidos satisfacción, tristeza, sorpresa, enojo, contempt, miedo, disgust e indiferencia en relación o neutro. Tiene varias útiles aplicaciones de la industria, se describe a continuación, como agregar y analizar reacciones de personas asistir a un evento. Para obtener información detallada y ejemplos, vea [nominal y detección de emoción para análisis de multimedia de Azure](media-services-face-and-emotion-detection.md).
 
- **Resumen de vídeo** : resumen de vídeo puede ayudarle a crear resúmenes de los vídeos largos seleccionando automáticamente fragmentos interesantes en el vídeo de origen. Esto es útil cuando desea proporcionar una descripción general de lo que pasará en un vídeo largo. Para obtener información detallada y ejemplos, vea [Usar miniaturas de vídeo de Media de Azure para crear un resumen de vídeo](media-services-video-summarization.md)

- **Reconocimiento óptico de caracteres** - Azure Media análisis OCR (reconocimiento óptico de caracteres) le permite convertir el contenido de texto en archivos de vídeo en editable, que permiten búsqueda texto digital. Esto le permite automatizar la extracción de metadatos significativo de señal de vídeo de su media.
 
- **Redacción de cara Scalable** - **Redactor de Azure Media** es un panel de administración de análisis de multimedia de Azure que ofrece redacción scalable nominal en la nube. Redacción de cara le permite modificar el vídeo con el fin de desenfoque caras de los usuarios seleccionados. Desea usar el servicio de redacción nominal en escenarios de seguridad y medios de noticias públicos. Unos minutos de grabación que contiene varias caras pueden tardar horas en censurar manualmente, pero con este servicio, el proceso de redacción de cara requerirá unos sencillos pasos. Para obtener más información, consulte [este](media-services-face-redaction.md) artículo.

 
## <a name="common-scenarios"></a>Escenarios comunes

A continuación se muestran algunos escenarios donde el análisis de elementos multimedia de Azure puede ayudar a las organizaciones y empresas en industrias recopilar nueva información de vídeo para crear más personalizada público y compromisos de empleado, así como administrar gran volumen de contenido de vídeo de forma más eficaz:

- **Centros de llamadas** , incluso con la llegada de medios sociales, centros facilitan aún un gran porcentaje de transacciones de servicio de atención al cliente de llamada de cliente. Codificado en estos datos audio es una gran cantidad de información acerca de los clientes que se pueden analizar para mejorar la Guía del producto y también entrenar a los empleados de centro de llamadas para lograr mayor satisfacción del cliente. Usando indizador de Media de Azure, los clientes pueden extraer el texto y generar un índice de búsqueda y paneles extraer inteligencia alrededor más común se queja, se queja de origen y otros datos pertinentes.

- **Moderación de contenido había generado por el usuario** : desde tomas de medios a los departamentos de policía, muchas organizaciones cuentan con públicos portales hacia donde aceptar medios UGC, como vídeos e imágenes. El volumen de contenido puede tener picos debido a eventos inesperados. En estos casos, se cerca imposible llevar a cabo una eficaz revisión manual del contenido de idoneidad. Los clientes pueden confiar en el servicio de moderación de contenido para centrarse en el contenido adecuado.

- **Control** - con el crecimiento de cámaras IP, hay una explosión de vídeos de control. Revisar manualmente el vídeo de supervisión es susceptible a errores humanos y demanda mucho tiempo. Análisis de Azure Media proporciona varios componentes, como detección de movimiento, detección de cara y Hyperlapse para que el proceso de revisión, administrar y crear derivados más fácilmente.

## <a name="media-services-analytics-media-processors"></a>Servicios multimedia procesadores de Media de análisis 

Esta sección muestra todos los elementos multimedia servicios análisis Media procesadores (panel de administración) y se muestra cómo usar .NET o resto para obtener un objeto del panel de administración.

### <a name="mp-names"></a>Nombres de panel de administración


- Vista previa de indizador 2 multimedia de Azure
- Indizador multimedia de Azure
- Hyperlapse multimedia de Azure
- Detector de cara multimedia de Azure
- Detector de movimiento de multimedia de Azure
- Miniaturas de vídeo multimedia de Azure
- OCR multimedia de Azure

### <a name="net"></a>.NET

La siguiente función toma uno de los nombres especificados del panel de administración y devolver un objeto del panel de administración.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


## <a name="rest"></a>REST

Solicitar:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net
    
Respuesta:
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

##<a name="demos"></a>Demostraciones

[Azure demostraciones de análisis de medios](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

##<a name="next-steps"></a>Pasos siguientes

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-articles"></a>Artículos relacionados

[Anuncio de análisis de servicios de medios](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)
  

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
