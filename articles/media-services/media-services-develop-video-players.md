<properties 
    pageTitle="Desarrollar aplicaciones de Reproductor de vídeo" 
    description="El tema proporciona vínculos a Reproductor marcos y complementos que puede usar para desarrollar sus propias aplicaciones cliente que pueden consumir transmisiones de Media Services." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="develop-video-player-applications"></a>Desarrollar aplicaciones de Reproductor de vídeo

##<a name="overview"></a>Información general

Azure Media Services proporciona las herramientas que necesita para crear aplicaciones de Reproductor de cliente enriquecido dinámica para la mayoría de las plataformas incluidas: iOS dispositivos, dispositivos Android, Windows, Windows Phone, Xbox y Set-top cuadros. En este tema también proporciona vínculos a los SDK y marcos de Reproductor que puede usar para desarrollar sus propias aplicaciones cliente que pueden consumir transmisiones de Azure Media Services.

##<a name="azure-media-player"></a>Reproductor multimedia de Azure

[Azure Media Player](http://aka.ms/ampinfo) es un Reproductor de vídeo web diseñado para reproducir contenido multimedia de Microsoft Azure Media Services en una amplia variedad de dispositivos y exploradores. Reproductor Azure utiliza estándares, como HTML5, extensiones de origen multimedia (MSE) y extensiones de medios cifrado (EME) para proporcionar una experiencia de streaming adaptación enriquecida. Cuando estos estándares no están disponibles en un dispositivo o en un explorador, el Reproductor multimedia de Azure usa Flash y Silverlight como tecnología de reserva. Independientemente de la tecnología de reproducción utiliza, los desarrolladores tendrán una interfaz unificada de JavaScript para tener acceso a las API. Esto permite contenido servida por Azure Media Services se pueden reproducir en una amplia gama de dispositivos y exploradores sin ningún esfuerzo adicional.

Microsoft Azure Media Services permite ofrecer con guión Smooth Streaming y formatos de transmisión de HLS contenido para reproducir contenido. Reproductor Azure tiene en cuenta estos formatos diversos y automáticamente se reproduce el vínculo mejor en función de las funciones de plataforma y explorador. Microsoft Azure Media Services también permite cifrado dinámico de activos con cifrado PlayReady o AES de 128 bits cifrado de sobres. Reproductor Azure permite descifrado de PlayReady y AES de 128 bits contenido cifrado cuando bien configurados. 

Para obtener más información:

- [Reproductor multimedia de Azure](http://aka.ms/ampinfo)
- [Documentación de Reproductor multimedia de Azure](http://aka.ms/ampdocs) 
- [Obtener el Reproductor multimedia de Azure iniciado Blog](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
- [Suscríbase para estar al día con la información más reciente de Azure Media Player](http://aka.ms/ampsignup)
- [Agregar nuevas solicitudes, ideas, comentarios](http://aka.ms/ampuservoice ) 


##<a name="other-tools-for-creating-player-applications"></a>Otras herramientas para la creación de aplicaciones de Reproductor

También puede utilizar cualquiera de los SDK siguientes:

- [Suavizados Streaming SDK de cliente](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Smooth Streaming aplicación de la tienda Windows](media-services-build-smooth-streaming-apps.md)
- [Plataforma multimedia de Microsoft: Marco de Reproductor](http://playerframework.codeplex.com/) 
- [HTML5 Documentación de Framework Reproductor](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Suave Microsoft transmisión complemento para OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Licencia Microsoft® Smooth Streaming cliente trasladar Kit](http://aka.ms/sspk) 
- [Desarrollo de aplicaciones de vídeo de XBOX](http://xbox.create.msdn.com/) 
 

##<a name="advertising"></a>Anunciar

Azure Media Services proporciona soporte técnico para la inserción de ad a través de la plataforma de Windows Media: marcos del Reproductor. Marcos de Reproductor con el soporte técnico de ad están disponibles para los dispositivos de Windows 8, Silverlight, Windows Phone 8 y iOS. Cada marco Reproductor contiene código de ejemplo que muestra cómo implementar una aplicación de reproducción. Existen tres tipos diferentes de anuncios que se puede insertar en el medio:

Lineal: anuncios de marco completo que pausar el vídeo principal

Nonlinear – anuncios de superposición que se muestran cuando se está reproduciendo el vídeo principal, normalmente un logotipo u otra imagen estática situarse dentro del Reproductor

Companion – anuncios que se muestran fuera del Reproductor

Anuncios se pueden colocar en cualquier punto de la escala de tiempo del vídeo principal. Debe indicar al Reproductor cuándo reproducir ad y los anuncios para reproducir. Esto se hace con un conjunto de archivos basado en XML estándar: plantilla de servicio de Ad de vídeo (VAST), lista de reproducción múltiples de Ad (VMAP) de Digital vídeo, multimedia abstractos secuencia de plantilla (más) y Ad interfaz definición (VPAID) el Reproductor de vídeo Digital. Archivos grandes especifican los anuncios para mostrar. Archivos VMAP especifican cuándo reproducir varios anuncios y contienen gran XML. Archivos más constituyen otra forma a los anuncios de secuencia que también puede contener gran XML. Archivos VPAID definen una interfaz entre el Reproductor de vídeo y el anuncio o servidor ad. Para obtener más información, vea [Insertar anuncios](https://msdn.microsoft.com/library/dn387398.aspx).

Para obtener información sobre la compatibilidad de anuncios y subtítulos cerrada en Live secuencias de vídeos, vea [admite subtítulos y estándares de inserción de Ad](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vea también

[Incrustar un vídeo de transmisión adaptación MPEG-guión en una aplicación de HTML5 con DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Repositorio de dash.js GitHub](https://github.com/Dash-Industry-Forum/dash.js)
 
