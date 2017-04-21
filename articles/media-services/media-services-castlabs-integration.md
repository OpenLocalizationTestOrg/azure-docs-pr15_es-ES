<properties 
    pageTitle="Usar castLabs para ofrecer Widevine licencias a Azure Media Services | Microsoft Azure" 
    description="Este artículo describe cómo puede usar Azure Media Services (AMS) para ofrecer una secuencia que se cifra dinámicamente AMS con PlayReady y Widevine DRMs. La licencia de PlayReady proviene de servidor de licencias PlayReady de servicios de medios y se ha entregado Widevine licencia castLabs servidor de licencias." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="Mingfeiy;willzhan;Juliako"/>


#<a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Usar castLabs para ofrecer Widevine licencias a Azure Media Services

> [AZURE.SELECTOR]
- [Axinom](media-services-axinom-integration.md)
- [castLabs](media-services-castlabs-integration.md)

##<a name="overview"></a>Información general

Este artículo describe cómo puede usar Azure Media Services (AMS) para ofrecer una secuencia que se cifra dinámicamente AMS con PlayReady y Widevine DRMs. La licencia de PlayReady proviene de servidor de licencias PlayReady de servicios de medios y se ha entregado Widevine licencia **castLabs** servidor de licencias.

Para la reproducción transmisión contenido protegido por CENC (PlayReady o Widevine), puede usar [El Reproductor multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Para obtener información detallada, consulte [documento AMP](http://amp.azure.net/libs/amp/latest/docs/) .

El siguiente diagrama muestra un alto nivel de Azure Media Services y la arquitectura de integración de castLabs.

![integración](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##<a name="typical-system-set-up"></a>Configuración del sistema típico

- Contenido multimedia se almacena en AMS.
- Identificadores de clave de las teclas de contenido se almacenan en castLabs y AMS.
- castLabs y AMS tiene integrada la autenticación token. Las secciones siguientes describen tokens de autenticación. 
- Cuando un cliente solicita secuencia el vídeo, el contenido se dinámicamente cifrado con **Cifrado comunes** (CENC) y empaquetado dinámicamente por AMS Smooth Streaming y guión. También se ofrecen cifrado de secuencia primaria PlayReady M2TS protocolo de transferencia de HLS.
- Licencias PlayReady se recuperan desde el servidor de licencias de AMS y Widevine licencia que se recuperen castLabs servidor de licencias. 
- Reproductor multimedia automáticamente decide qué licencia para capturar en función de la capacidad de la plataforma de cliente. 

##<a name="authentication-token-generation-for-getting-a-license"></a>Generación de token de autenticación para obtener una licencia

CastLabs y AMS compatible con formato de token de JWT (JSON Web Token) utilizado para autorizar una licencia. 

###<a name="jwt-token-in-ams"></a>Token JWT en AMS 

La siguiente tabla describe token JWT en AMS. 

Emisor|Cadena de emisor desde el elegido servicio de Token seguro (STS)
---|---
Audiencia|Cadena de audiencia desde el STS usado
Reclamaciones|Un conjunto de notificaciones
NotBefore|Iniciar la validez del token
Caduca|Final de validez del token
Credenciales de firma|La clave que se comparte entre el servidor de licencias PlayReady, castLabs servidor de licencias y STS, podría ser simétrica o asimétricos clave.

###<a name="jwt-token-in-castlabs"></a>Símbolo JWT en castLabs

La siguiente tabla describe token JWT en castLabs. 

Nombre|Descripción
---|---
optData|Cadena JSON que contiene información sobre el usuario. 
CRT|Una cadena JSON que contiene información sobre el activo, sus derechos de información y la reproducción de licencia.
tabla|La fecha y hora actual en el tiempo.
jti|Un identificador único sobre este token (cada token sólo puede utilizarse una vez en el sistema de castLabs).

##<a name="sample-solution-set-up"></a>Solución de ejemplo configurar 

La [solución de ejemplo](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) consta de dos proyectos:

-   Una aplicación de consola que puede usarse para establecer restricciones de DRM de un activo ya integrado, para PlayReady y Widevine.
-   Una aplicación Web que distribuye tokens, que podrían aparecer como una versión muy simplificado de un STS.


Para usar la aplicación de consola:

1.  Cambiar app.config para configurar las credenciales de AMS, castLabs credenciales, configuración de STS y clave compartida.
2.  Cargar un activo en AMS.
3.  Obtener el UUID de los activos cargados y cambiar 32 de línea en el archivo Program.cs:

         var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.  Use un idtema nomenclatura activo en el sistema castLabs (44 de línea en el archivo Program.cs).

    Se debe establecer idtema para **castLabs**; debe ser una cadena alfanumérica única.

5.  Ejecute el programa.


Para usar la aplicación Web (STS):

1.  Cambiar el archivo web.config a vendedor de castlabs configuración ID, la configuración de STS y la clave compartida.
2.  Implementar a sitios Web de Azure.
3.  Vaya al sitio Web.

##<a name="playing-back-a-video"></a>Reproducir un vídeo

Para reproducir un vídeo cifrado con cifrado comunes (PlayReady o Widevine), puede usar el [Reproductor multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Cuando ejecute la aplicación de consola, se reflejan el identificador de clave de contenido y la dirección URL de manifiestos.

1.  Abrir una nueva pestaña e inicie su STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.  Vaya a [Reproductor multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.  Pegue la dirección URL streaming.
4.  Haga clic en la casilla de verificación **Opciones avanzadas** .
5.  En la lista desplegable de **protección** , seleccione PlayReady o Widevine.
6.  Pegue el símbolo que obtuvo de su STS en el cuadro de texto de Token. 
    
    El servidor de licencias de castLab no es necesario el "portador =" prefijo delante del token. Así que quite antes de enviar el token.
7.  Actualizar el Reproductor.
8.  Se debe reproducir el vídeo.


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
