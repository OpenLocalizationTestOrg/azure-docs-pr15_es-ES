<properties 
    pageTitle="Configurar la directiva de autorización de clave de contenido con el portal de Azure | Microsoft Azure" 
    description="Aprenda a configurar una directiva de autorización para una clave de contenido." 
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
    ms.date="10/12/2016" 
    ms.author="juliako"/>



#<a name="configure-content-key-authorization-policy"></a>Configurar la directiva de autorización de clave de contenido
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##<a name="overview"></a>Información general

Microsoft Azure Media Services le permite proporcionar secuencias MPEG-guión Smooth Streaming y Streaming Live HTTP (HLS) protegidas con cifrado estándar avanzado (AES) (con claves de cifrado de 128 bits) o [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS también le permite proporcionar secuencias de guión cifradas con DRM Widevine. PlayReady y Widevine se cifran por la especificación de cifrado comunes (ISO/IEC 23001 7 CENC).

Media Services también proporciona un **Servicio de entrega de la clave de la licencia o** desde la que los clientes pueden obtener claves AES o licencias de PlayReady/Widevine para reproducir el contenido cifrado.

Este tema muestra cómo usar el portal de Azure para configurar la directiva de autorización de clave de contenido. La clave más tarde puede usarse para cifrar dinámicamente el contenido. Formatos de nota que actualmente puede cifrar la transmisión siguientes: HLS, MPEG guión y Smooth Streaming. No puede cifrar HDS transmisión formato o progresivo descargas.

Cuando un Reproductor solicita una secuencia que está configurada para cifrar dinámicamente, Media Services utiliza la clave configurada para cifrar dinámicamente el contenido que usa cifrado AES o DRM. Para descifrar la secuencia, el Reproductor solicitará la clave del servicio de entrega clave. Para decidir si el usuario está autorizado para obtener la clave, el servicio evalúa las directivas de autorización especificada para la clave.


Si va a tener varias claves contenidas o desea especificar una dirección URL del **Servicio de entrega de clave de la licencia o** que no sea el servicio de entrega clave Media Services, use Media Services .NET SDK o API de REST.

[Configurar la directiva de autorización de clave contenido mediante Media Services .NET SDK](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurar la directiva de autorización de clave de contenido con la API de REST de servicios de medios](media-services-rest-configure-content-key-auth-policy.md)

###<a name="some-considerations-apply"></a>Aplicarán algunas consideraciones:

- Para poder usar embalaje dinámico y cifrado dinámico, debe asegurarse tiene al menos una unidad reservada de transmisión. Para obtener más información, vea [cómo ajustar el tamaño de un servicio de medios](media-services-portal-manage-streaming-endpoints.md).
- Los activos deben contener un conjunto de velocidad adaptación MP4s o archivos de velocidad adaptación Smooth Streaming. Para obtener más información, vea [codificar un activo](media-services-encode-asset.md).
- El servicio de entrega de clave almacena ContentKeyAuthorizationPolicy y sus objetos relacionados (opciones de directiva y restricciones) de 15 minutos.  Si crea un ContentKeyAuthorizationPolicy y especificar que se utilice una restricción "Token", probar y a continuación, actualice la directiva de restricción "Abrir", esta acción tardará aproximadamente 15 minutos antes de la directiva cambia a la versión "Abierta" de la directiva.


##<a name="how-to-configure-the-key-authorization-policy"></a>Cómo: configurar la directiva de autorización de clave

Para configurar la directiva de autorización de clave, seleccione la página de **Protección de contenido** .

Media Services admite varias formas de autenticar a los usuarios realizar las solicitudes clave. La directiva de autorización de clave de contenido puede tener **Abrir**, **token**o restricciones de autorización de **IP** (**IP** se puede configurar con el resto de .NET SDK).

###<a name="open-restriction"></a>Restricción abierto

La restricción **Abrir** significa que el sistema proporcionará la clave para cualquier persona que realiza una solicitud de clave. Esta restricción puede ser útil con fines de pruebas.

![OpenPolicy][open_policy]

###<a name="token-restriction"></a>Restricción de token

Para elegir la directiva restringido token, presione el botón **TOKEN** .

Se adjuntará la directiva **token** restringido por un símbolo de un **Servicio de Token seguro** (STS). Media Services admite tokens en los **Símbolos de Web Simple** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) y formato **JSON Web Token** (JWT). Para obtener más información, vea [autenticación de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services no proporciona **Servicios de Token seguro**. Puede crear a un STS personalizado o aprovechar Microsoft Azure ACS tokens de problema. El STS debe estar configurado para crear un símbolo que se ha iniciado sesión con las notificaciones de clave y problema especificadas especificada en la configuración de restricción de token. El servicio de entrega clave Media Services devolverá la clave de cifrado del cliente si el token es válido y las notificaciones del token coinciden con los configurados para la clave de contenido. Para obtener más información, vea [Usar Azure ACS tokens de problema](http://mingfeiy.com/acs-with-key-services).

Al configurar el **TOKEN** restringido directiva, debe establecer los valores de **clave de verificación**, el **emisor** y el **público**. La clave principal de comprobación contiene la clave que se firmó el token con, emisor es el servicio de token seguro que emite el token. La audiencia (a veces denominada ámbito) describe el objetivo del token o el recurso el token autoriza el acceso a. El servicio de entrega clave Media Services valida que estos valores en el símbolo que coincidan con los valores de la plantilla.

###<a name="playready"></a>PlayReady

Cuando se protege el contenido con **PlayReady**, una de las cosas que debe especificar en la directiva de autorización es una cadena XML que define la plantilla de la licencia de PlayReady. De forma predeterminada, se establece la directiva siguiente:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <LicenseType>Nonpersistent</LicenseType>
          <PlayRight>
            <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
          </PlayRight>
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

Y haga clic en el botón **Importar xml de directiva** a proporcionar un XML diferente que se ajuste al esquema XML definido [aquí](https://msdn.microsoft.com/library/azure/dn783459.aspx).


##<a name="next-step"></a>Siguiente paso

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

