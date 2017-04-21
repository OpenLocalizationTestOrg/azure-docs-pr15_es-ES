<properties
    pageTitle="Analizar los archivos multimedia con el portal de Azure | Microsoft Azure"
    description="En este tema se explica cómo procesar multimedia con procesadores de media de análisis de medios (PMP) con el portal de Azure."
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


# <a name="analyze-your-media-using-the-azure-portal"></a>Analizar los archivos multimedia con el portal de Azure

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="overview"></a>Información general

Análisis de Azure Media Services es una colección de componentes de voz y visión (en la escala de empresa, cumplimiento, seguridad y alcance global) que hacen que sea más fácil para las organizaciones y empresas para obtener información útil de sus archivos de vídeo. Para obtener más información general sobre el análisis de Azure Media Services, vea [este](media-services-analytics-overview.md) tema. 

En este tema se explica cómo procesar multimedia con procesadores de media de análisis de medios (PMP) con el portal de Azure. Media análisis PMP producir archivos MP4 o JSON. Si un procesador multimedia produce un archivo MP4, puede descargar el archivo cada vez. Si un procesador multimedia genera un archivo JSON, puede descargar el archivo desde el almacenamiento de blobs de Windows Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Elija un activo en el que desea analizar 
 
1. En el [portal de Azure](https://portal.azure.com/), seleccione su cuenta de Azure Media Services.
2. En la ventana **configuración** , seleccione **activos**.  
.
    ![Analizar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)

2. Seleccione el recurso que desea analizar y presione el botón **analizar** .
        
    ![Analizar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)

3. En la ventana de **activos de medios de proceso con el análisis de medios** , seleccione el procesador. 

    El resto de este artículo explica por qué y cómo usar cada procesador. 
   
4. Presione **crear** al inicio de una tarea.

## <a name="azure-media-indexer"></a>Indizador multimedia de Azure

Procesador de medios de **Azure Media indizador** permite hacer posible la búsqueda de contenido y archivos multimedia, así como para generar pistas de subtítulos cerradas. Esta sección ofrece algunos detalles acerca de las opciones que puede especificar para este panel de administración.

![Analizar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Idioma

El lenguaje natural reconozca en el archivo multimedia. Por ejemplo, inglés o español. 

### <a name="captions"></a>Títulos

Puede elegir un formato de título que se generarán desde su contenido. Una tarea de indización puede generar archivos de subtítulos en los siguientes formatos:  

- **Sami**
- **TTML**
- **WebVTT**

Cerrado título (CC) archivos en estos formatos pueden utilizarse para hacer que los archivos de audio y vídeo accesibles para personas con discapacidades auditivas.

### <a name="aib-file"></a>Archivo AIB

Seleccione esta opción si desea generar el archivo de Audio índice Blob para usar con este último personalizado de servidor de SQL. Para obtener más información, consulte [este](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Palabras clave

Seleccione esta opción si desea generar un archivo XML de palabras clave. Este archivo contiene palabras clave que extrae el contenido de voz con frecuencia y la información de desplazamiento.

### <a name="job-name"></a>Nombre del trabajo

Un nombre descriptivo que le permite identificar el trabajo. [Este](media-services-portal-check-job-progress.md) artículo describe cómo puede supervisar el progreso de una tarea. 

### <a name="output-file"></a>Archivo de salida

Un nombre descriptivo que le permite identificar el contenido de salida. 

## <a name="azure-media-hyperlapse"></a>Hyperlapse multimedia de Azure

Azure Hyperlapse Media es un panel de administración que crea suaves vídeos de tiempo transcurrido desde el contenido de la primera persona o una cámara de acción.  Para obtener más información, vea [este](media-services-hyperlapse-content.md) tema. Esta sección ofrece algunos detalles acerca de las opciones que puede especificar para este panel de administración.

![Analizar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Velocidad 

Especifique la velocidad con que acelerar el vídeo de entrada. El resultado es una representación estabilizada y tiempo transcurrido de la entrada de vídeo.

### <a name="job-name"></a>Nombre del trabajo

Un nombre descriptivo que le permite identificar el trabajo. [Este](media-services-portal-check-job-progress.md) artículo describe cómo puede supervisar el progreso de una tarea. 

### <a name="output-file"></a>Archivo de salida

Un nombre descriptivo que le permite identificar el contenido de salida. 

## <a name="azure-media-face-detector"></a>Detector de cara multimedia de Azure

Procesador de medios **Detector de Azure Media nominal** (programa mínimo) le permite contar, realizar un seguimiento de los movimientos e incluso evaluar la participación de la audiencia y reacción a través de expresiones rostro. Este servicio contiene dos funciones: 

- **Detección de cara**

    Detección de cara que busca y realiza un seguimiento de caras humanas dentro de un vídeo. Varias caras se pueden detectar y posteriormente registrará mientras se desplazan, con los metadatos de hora y la ubicación devuelto en un archivo JSON. Durante el seguimiento, intentará dar un identificador coherente a la misma cara mientras la persona que está moviendo en pantalla, incluso si se obstruida o deje brevemente el marco.

    >[AZURE.NOTE]Este servicio no realiza reconocimiento rostro. Una persona deja el marco o esté obstruida para mucho tiempo se proporcionará un identificador nuevo cuando vuelvan.

- **Detección de emoción**
    
    Detección de emoción es un componente opcional de la cara detección procesador multimedia que devuelve análisis en varios atributos emoción de las caras detectadas, incluida la satisfacción, tristeza, miedo, enojo y mucho más. 

![Analizar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modo de detección

Uno de los siguientes modos puede ser usada por el procesador:

- detección de cara
- por detección de emoción nominal
- detección de emoción agregado

### <a name="job-name"></a>Nombre del trabajo

Un nombre descriptivo que le permite identificar el trabajo. [Este](media-services-portal-check-job-progress.md) artículo describe cómo puede supervisar el progreso de una tarea. 

### <a name="output-file"></a>Archivo de salida

Un nombre descriptivo que le permite identificar el contenido de salida. 

## <a name="azure-media-motion-detector"></a>Detector de movimiento de multimedia de Azure

Procesador de medios **Detector de movimiento de Azure Media** (programa mínimo) le permite identificar eficazmente secciones de interés dentro de un vídeo en caso contrario, largo y sin incidentes. Detección de movimiento puede usarse en grabación de cámara estática identificar las secciones del vídeo donde se produce la animación. Genera un archivo JSON que contiene un metadatos con las marcas de tiempo y el delimitador región donde se produjo el evento.

Esta tecnología y destinada a fuentes de vídeo de seguridad, es posible clasificar movimiento en eventos relevantes y falsos como sombras y los cambios de iluminación. Esto le permite generar las alertas de seguridad de cámara fuentes sin ser spam con infinitas eventos relevante, mientras se pueden extraer momentos de interés de vídeos de supervisión muy largo.

![Analizar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Miniaturas de vídeo multimedia de Azure

Este procesador puede ayudarle a crear resúmenes de los vídeos largos seleccionando automáticamente fragmentos interesantes en el vídeo de origen. Esto es útil cuando desea proporcionar una descripción general de lo que pasará en un vídeo largo. Para obtener información detallada y ejemplos, vea [Usar miniaturas de vídeo de Media de Azure para crear un resumen de vídeo](media-services-video-summarization.md)

![Analizar vídeos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nombre del trabajo

Un nombre descriptivo que le permite identificar el trabajo. [Este](media-services-portal-check-job-progress.md) artículo describe cómo puede supervisar el progreso de una tarea. 

### <a name="output-file"></a>Archivo de salida

Un nombre descriptivo que le permite identificar el contenido de salida. 


##<a name="next-steps"></a>Pasos siguientes

Rutas de aprendizaje de vista Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


