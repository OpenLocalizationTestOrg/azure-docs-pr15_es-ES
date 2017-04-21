<properties 
    pageTitle="Proteger la información del contenido | Microsoft Azure" 
    description="En este artículo proporciona una descripción general de protección de contenido con Media Services." 
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
    ms.date="09/27/2016" 
    ms.author="juliako"/>

#<a name="protecting-content-overview"></a>Proteger la información del contenido


Microsoft Azure Media Services le permite proteger su medio de la hora de sale de su equipo a través de almacenamiento, procesamiento y entrega. Media Services le permite entregar el contenido directo y a petición dinámicamente cifrado con cifrado estándar avanzado (AES) (con claves de cifrado de 128 bits) o cualquiera de las principales DRMs: Microsoft PlayReady, Google Widevine y FairPlay de Apple. Media Services también proporciona un servicio para proporcionar claves AES y DRM (PlayReady, Widevine y FairPlay) licencias a los clientes autorizados. 

La imagen siguiente muestra los flujos de trabajo de protección de contenido que admite AMS. 

![Proteger con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]Para poder usar cifrado dinámico, primero debe obtener al menos una unidad reservada streaming en el extremo streaming desde la que desea transmitir contenido cifrado.

En este tema se explica [conceptos y terminología](media-services-content-protection-overview.md) relevantes para comprender la protección del contenido con AMS. El tema también contiene [vínculos](media-services-content-protection-overview.md#common-scenarios) a temas que muestran cómo realizar tareas de protección de contenido. 

##<a name="dynamic-encryption"></a>Cifrado dinámico

Microsoft Azure Media Services le permite proporcionar su contenido dinámicamente cifrado con cifrado de DRM o AES borrar clave: Microsoft PlayReady, Google Widevine y FairPlay de Apple.

Actualmente, puede cifrar los siguientes formatos de streaming: HLS, MPEG guión y Smooth Streaming. No puede cifrar HDS transmisión formato o progresivo descargas.

Si desea que Media Services cifrar un activo, debe asociar una clave de cifrado (CommonEncryption o EnvelopeEncryption) a su activo y también configurar directivas de autorización de la clave.

También debe configurar la directiva de entrega del activo. Si desea un activo cifrado de almacenamiento de secuencias, asegúrese de que especificar cómo desea entregar mediante la configuración de directiva de entrega de activos.

Cuando un Reproductor solicita una secuencia, Media Services usa la clave especificada para cifrar dinámicamente el contenido con AES borrar clave o cifrado de DRM. Para descifrar la secuencia, el Reproductor solicitará la clave del servicio de entrega clave. Para decidir si el usuario está autorizado para obtener la clave, el servicio evalúa las directivas de autorización especificada para la clave.

>[AZURE.NOTE]Para aprovechar las ventajas de cifrado dinámico, primero debe obtener al menos una unidad de transmisión por secuencias a petición del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido cifrado. Para obtener más información, vea [Cómo escala Media Services](media-services-portal-manage-streaming-endpoints.md).

##<a name="storage-encryption"></a>Cifrado de almacenamiento

Uso del cifrado de almacenamiento para cifrar el contenido de borrar localmente mediante el cifrado AES 256 bits y luego cárguelo en el almacenamiento de Azure donde esté almacenado cifrado al resto. Activos protegidos con cifrado de almacenamiento automáticamente sin cifrar y colocados en un sistema de archivos cifrados antes de codificación y, opcionalmente, cifrados de nuevo antes de cargarlo como un nuevo activo de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos de medios de entrada de alta calidad con cifrado almacenados en disco.

Para poder ofrecer a un activo cifrado de almacenamiento, debe configurar Directiva de entrega del activo para que Media Services sepa cómo desea entregar el contenido. Antes de que puedan transmitir su activo, el servidor de transmisión quita el cifrado de almacenamiento y transmite el contenido con la directiva de entrega especificada (por ejemplo, AES, cifrado comunes o sin cifrado).

## <a name="common-encryption-cenc"></a>Cifrado comunes (CENC)

Cifrado comunes se utiliza para cifrar el contenido con PlayReady o / y Widewine.

## <a name="using-cbcs-aapl-encryption"></a>Uso del cifrado de cbcs aapl

Cbcs aapl se usa para cifrar el contenido con FairPlay.

## <a name="envelope-encryption"></a>Cifrado de sobres 

Use esta opción si desea proteger el contenido con las claves AES de 128. Si desea que una opción más segura, elija uno de los DRMs mencionados en este tema. 

##<a name="licenses-and-keys-delivery-service"></a>Servicio de entrega de licencias y claves

Media Services proporciona un servicio para proporcionar licencias DRM (PlayReady, Widevine, FairPlay) y AES Borrar claves a los clientes autorizados. Puede usar [el portal de Azure](media-services-portal-protect-content.md), API de REST o Media Services SDK para .NET para configurar directivas de autenticación y autorización para sus claves y licencias.

##<a name="token-restriction"></a>Restricción de token

La directiva de autorización de clave de contenido podría tener una o más restricciones de autorización: abrir o token de restricción. Se adjuntará la directiva token restringido por un token emitido por una Token servicio seguro (STS). Media Services admite tokens en el formato Simple Web token (SWT) y el formato de JSON Web Token (JWT). Media Services no proporciona servicios de Token seguro. Puede crear a un STS personalizado o aprovechar Microsoft Azure ACS tokens de problema. El STS debe estar configurado para crear un símbolo que se ha iniciado sesión con las notificaciones de clave y problema especificadas especificada en la configuración de restricción de token. El servicio de entrega clave Media Services devolverá la clave solicitada (o la licencia) al cliente si el token es válido y las notificaciones de la coincidencia de token aquellos configurados para la clave (o la licencia).

Al configurar el token restringido directiva, debe especificar la clave principal de la comprobación, emisor y parámetros de la audiencia. La clave principal de comprobación contiene la clave que se firmó el token con, emisor es el servicio de token seguro que emite el token. La audiencia (a veces denominada ámbito) describe el objetivo del token o el recurso el token autoriza el acceso a. El servicio de entrega clave Media Services valida que estos valores en el símbolo que coincidan con los valores de la plantilla.

##<a name="streaming-urls"></a>Direcciones URL Streaming

Si su activo estaba cifrado con más de un DRM, debe usar una etiqueta de cifrado en la dirección URL streaming: (formato = 'm3u8-aapl' cifrado = 'largo y LARGOB').

Aplica teniendo en cuenta lo siguiente:

- Puede especificar el tipo de cifrado solo cero o uno.
- Tipo de cifrado no tiene que especificar la dirección URL si solo una cifrado aplicado al activo.
- Tipo de cifrado distingue mayúsculas de minúsculas.
- Pueden especificar los siguientes tipos de cifrado:  
    - **cenc**: cifrado comunes (Playready o Widevine)
    - **cbcs aapl**: Fairplay
    - **CBC**: cifrado AES de sobres.

##<a name="common-scenarios"></a>Escenarios comunes

Los temas siguientes muestran cómo proteger el contenido de almacenamiento, entregar dinámicamente cifrada transmisiones, use el servicio de entrega de la licencia o clave AMS

- [Proteger con AES](media-services-protect-with-aes128.md) 
- [Proteger con PlayReady o Widevine](media-services-protect-with-drm.md)
- [Transmita su protegida contenido HLS con Apple FairPlay o PlayReady](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>Escenarios adicionales

- [Cómo integrar el servicio de licencias de PlayReady Azure con su propio servidor cifrador/transmisión](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
- [Usar castLabs para ofrecer licencias DRM a Azure Media Services](media-services-castlabs-integration.md)
 
##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Vínculos relacionados

[Anuncio de PlayReady como servicio y cifrado AES de dinámico con Azure Media Services](http://mingfeiy.com/playready)

[Explicación de Azure Media Services PlayReady licencia entrega precios](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Cómo depurar de secuencia cifrado AES en Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticación de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar Azure Media Services OWIN MVC según la aplicación con Azure Active Directory y restringir el contenido entrega clave basada en notificaciones JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Usar Azure ACS tokens de problema](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
