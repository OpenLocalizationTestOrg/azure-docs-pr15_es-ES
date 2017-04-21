<properties 
    pageTitle="Con socios de ofrecer licencias Widevine a Azure Media Services | Microsoft Azure" 
    description="Este artículo describe cómo puede usar Azure Media Services (AMS) para ofrecer una secuencia que se cifra dinámicamente AMS con PlayReady y Widevine DRMs. La licencia de PlayReady proviene de servidor de licencias PlayReady de servicios de medios y se ha entregado Widevine licencia castLabs servidor de licencias." 
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

#<a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Uso de socios para ofrecer Widevine licencias a Azure Media Services

##<a name="overview"></a>Información general

Microsoft Azure Media Services le permite ofrecer MPEG-guión protegido con DRM Widevine, que está cifrado por la especificación de cifrado común (CENC).

A partir de la Media Services .NET SDK versión 3.5.2, Media Services le permite configurar la plantilla de la licencia de Widevine y obtener Widevine licencias. También puede usar los siguientes asociados de AMS que le ayudarán a ofrecer Widevine licencias: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##<a name="castlabs"></a>castLabs

Puede usar [castLabs](http://castlabs.com/company/partners/azure/) para ofrecer Widevine licencias. Para obtener más información, vea [usar castLabs para proporcionar licencias DRM a Azure Media Services](media-services-castlabs-integration.md)

##<a name="axinom"></a>Axinom

Puede usar [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) para ofrecer Widevine licencias. Para obtener más información, vea [Usar Axinom para proporcionar licencias DRM a Azure Media Services](media-services-axinom-integration.md)


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vea también

[Uso del cifrado comunes de PlayReady o Widevine dinámico](media-services-protect-with-drm.md)

[Blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

