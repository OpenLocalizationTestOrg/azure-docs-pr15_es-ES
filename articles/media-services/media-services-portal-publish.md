<properties
    pageTitle="  Publicar contenido en el portal de Azure | Microsoft Azure"
    description="Este tutorial le guiará por los pasos de la publicación de contenido con el portal de Azure."
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

# <a name="publish-content-with-the-azure-portal"></a>Publicar contenido en el portal de Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-publish.md)
- [.NET](media-services-deliver-streaming-content.md)
- [REST](media-services-rest-deliver-streaming-content.md)

## <a name="overview"></a>Información general

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 

Para proporcionar el usuario con una dirección URL que puede utilizarse para transmitir o descargar el contenido, debe "publicación" su activo mediante la creación de un localizador. Localizadores proporcionan acceso a archivos contenidos en el activo. Media Services es compatible con dos tipos de localizadores: 

- Transmisión localizadores (OnDemandOrigin), utilizados para la transmisión adaptación (por ejemplo, a la secuencia MPEG guión, HLS o Smooth Streaming). Para crear un localizador streaming su activo debe contener un archivo .ism. 
- Localizadores progresivos de (SA), utilizados para el envío de vídeo mediante descarga progresiva.


Una dirección URL streaming tiene el siguiente formato y puede usarlo para reproducir Smooth Streaming activos.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para generar una URL de transmisión de HLS, anexar (formato = m3u8 aapl) a la dirección URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para crear un guión MPEG transmisión URL, anexar (formato = csf de tiempo de mpd) a la dirección URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Una dirección URL de SA tiene el siguiente formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

Para obtener más información, vea [información general de ofrecer contenido](media-services-deliver-content-overview.md).

>[AZURE.NOTE] Si utiliza el portal para crear localizadores antes de marzo de 2015, se crearon localizadores con una fecha de caducidad de dos años.  

Para actualizar una fecha de vencimiento de un localizador, utilice el [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) de las API de [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Observe que, cuando se actualiza la fecha de vencimiento de un localizador SA, cambia la dirección URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Usar el portal para publicar un activo

Para usar el portal para publicar un activo, haga lo siguiente:

1. En el [portal de Azure](https://portal.azure.com/), seleccione su cuenta de Azure Media Services.
1. Seleccione **configuración** > **activos**.
1. Seleccione el elemento que desea publicar.
1. Haga clic en el botón **Publicar** .
1. Seleccione el tipo de localizador.
2. Presione **Agregar**.

    ![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

La dirección URL se agregará a la lista de **Direcciones URL publicado**.

## <a name="play-content-from-the-portal"></a>Reproducir contenido desde el portal

El portal de Azure ofrece un Reproductor de contenido que puede usar para probar el vídeo.

Haga clic en el vídeo que desee y, a continuación, haga clic en el botón **Reproducir** .

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

Aplicarán algunas consideraciones:

- Asegúrese de que el vídeo se ha publicado.
- Este **Reproductor** reproduce de transmisión extremo predeterminado. Si desea reproducir desde un extremo de streaming no predeterminada, haga clic en para copiar la dirección URL y usar el Reproductor de otro. Por ejemplo, [El Reproductor de Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
- Debe ejecutar el extremo streaming desde la que se transmisión.  
- Para transmitir desde un extremo streaming, debe agregar al menos una unidad streaming. Para obtener más información, vea [este](media-services-portal-scale-streaming-endpoints.md) tema.   

##<a name="next-steps"></a>Pasos siguientes

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


