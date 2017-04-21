<properties 
    pageTitle="Reproducir el contenido | Microsoft Azure" 
    description="En este tema se enumeran los reproductores existentes que puede usar para reproducir el contenido." 
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
    ms.date="10/12/2016" 
    ms.author="juliako"/>


#<a name="playing-your-content-with-existing-players"></a>Reproducir contenido con reproductores existentes

Azure Media Services es compatible con muchos formatos de streaming populares, como Smooth Streaming Live transmisión HTTP y MPEG guión. Este tema muestra reproductores existentes que puede usar para probar sus secuencias.

>[AZURE.NOTE]Para reproducir dinámicamente empaquetados o contenido dinámicamente cifrado, asegúrese de que recibe al menos una unidad de transmisión del extremo de transmisión por secuencias desde el que tiene previsto ofrecer su contenido. Para obtener información sobre la escala de unidades streaming, vea: [cómo escalar streaming unidades](media-services-portal-manage-streaming-endpoints.md).

### <a name="the-azure-portal-media-services-content-player"></a>Reproductor de contenido Media Services portal Azure

El portal de **Azure** ofrece un Reproductor de contenido que puede usar para probar el vídeo.

Haga clic en el vídeo que desee (asegúrese de que fue [publicado](media-services-portal-publish.md)) y haga clic en el botón **Reproducir** en la parte inferior del portal.

Aplicarán algunas consideraciones:

- Se reproduce el **Reproductor de contenido de los servicios** de transmisión extremo predeterminado. Si desea reproducir desde un extremo de streaming no predeterminada, use otro reproductor. Por ejemplo, [El Reproductor multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]

###<a name="azure-media-player"></a>Reproductor multimedia de Azure

Usa [Un reproductor multimedia Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para reproducir el contenido (borrar o protegido) en cualquiera de los siguientes formatos:

- Smooth Streaming
- MPEG GUIÓN
- HLS
- MP4 progresivo


###<a name="flash-player"></a>Flash Player

####<a name="aes-encrypted-with-token"></a>Cifrado AES con Token

[http://aestoken.azurewebsites.NET](http://aestoken.azurewebsites.net)

###<a name="silverlight-players"></a>Reproductores de Silverlight

####<a name="monitoring"></a>Supervisión

[http://SMF.cloudapp.NET/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####<a name="playready-with-token"></a>PlayReady con Token

[http://sltoken.azurewebsites.NET](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Reproductores de guión

[http://dashplayer.azurewebsites.NET](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###<a name="other"></a>Otros

Para probar las direcciones URL de HLS también puede usar:

- **Safari** en un dispositivo iOS o
- **3ivx HLS Reproductor** de Windows.

##<a name="developing-video-players"></a>Desarrollar reproductores de vídeo

Para obtener información sobre cómo desarrollar sus propios jugadores, vea [reproductores de vídeo de desarrollo](media-services-develop-video-players.md)




##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
