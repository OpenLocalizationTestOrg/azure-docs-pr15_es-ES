<properties
    pageTitle=" Cargar archivos a una cuenta de Media Services con el portal de Azure | Microsoft Azure"
    description="Este tutorial le guiará por los pasos de carga de archivos a una cuenta de Media Services con el portal de Azure"
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
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="juliako"/>


# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Cargar archivos a una cuenta de Media Services con el portal de Azure 

> [AZURE.SELECTOR]
- [Portal](media-services-portal-upload-files.md)
- [.NET](media-services-dotnet-upload-files.md)
- [REST](media-services-rest-upload-files.md)

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 

En servicios de medios, cargar los archivos digitales en un activo. Activo puede contener vídeo, audio, imágenes, colecciones de miniaturas, texto pistas y subtítulos archivos (y los metadatos acerca de estos archivos.) Una vez que se cargan los archivos, el contenido se almacena de forma segura en la nube para el procesamiento de más y transmisión.
 
1. En el [portal de Azure](https://portal.azure.com/), seleccione su cuenta de Azure Media Services.

2. En el módulo de **configuración** , haga clic en **activos**.

    ![Cargar archivos](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Haga clic en el botón **cargar** .

    Aparece la ventana **cargar un recurso de vídeo** .

    >[AZURE.NOTE] No hay ninguna limitación de tamaño de archivo.
    
4. Busque el vídeo deseado en su equipo, selecciónelo y haga clic en Aceptar.  

    Se inicia la carga y puede ver el progreso en el nombre de archivo.  

Una vez completada la carga, verá el nuevo activo que aparece en la ventana **activos** . 


## <a name="next-steps"></a>Pasos siguientes

Ahora puede codificar los activos cargados. Para obtener más información, vea [codificar activos](media-services-portal-encode.md).

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


