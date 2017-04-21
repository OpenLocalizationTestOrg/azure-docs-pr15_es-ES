<properties 
    pageTitle="Configuración de directivas de protección de contenido con el portal de Azure | Microsoft Azure" 
    description="En este artículo se muestra cómo usar el portal de Azure para configurar las directivas de protección de contenido. El artículo también muestra cómo habilitar el cifrado dinámico para los activos." 
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

# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Configuración de directivas de protección de contenido con el portal de Azure

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="overview"></a>Información general

Microsoft Azure Media Services (AMS) le permite proteger su medio de la hora de sale de su equipo a través de almacenamiento, procesamiento y entrega. Media Services le permite ofrecer su contenido cifrado dinámicamente con cifrado estándar avanzado (AES) (con claves de cifrado de 128 bits), cifrado comunes (CENC) con PlayReady o DRM Widevine y FairPlay de Apple. 

AMS proporciona un servicio para proporcionar licencias DRM y AES Borrar claves a los clientes autorizados. El portal de Azure le permite crear una **Directiva de autorización de licencia o clave** para todos los tipos de cifrado.

En este artículo se muestra cómo configurar las directivas de protección de contenido con el portal de Azure. El artículo también muestra cómo aplicar cifrado dinámico a los activos.

> [AZURE.NOTE]  Si utiliza el portal clásico Azure para crear directivas de protección, las directivas podrían no aparecer en el [portal de Azure](https://portal.azure.com/). No obstante, todas las directivas de la antigua todavía existen. Revíselos usando el SDK de .NET de Azure Media Services o la herramienta de [Servicios de explorador Azure-multimedia](https://github.com/Azure/Azure-Media-Services-Explorer/releases) (para ver las directivas, con el botón secundario en el activo -> Mostrar información (F4) -> haga clic en la ficha contenido teclas -> haga clic en la tecla). 
> 
> Si desea cifrar los activos con nuevas directivas, configurarlos con el portal de Azure, haga clic en Guardar y volver a aplicar cifrado dinámico. 

## <a name="start-configuring-content-protection"></a>Iniciar la configuración de la protección de contenido

Para usar el portal para iniciar la configuración de la protección de contenido, global a su cuenta de AMS, haga lo siguiente:

1. En el [portal de Azure](https://portal.azure.com/), seleccione su cuenta de Azure Media Services.
2. Seleccione **configuración** > **protección del contenido**.

![Proteger el contenido](./media/media-services-portal-content-protection/media-services-content-protection001.png)
 

## <a name="keylicense-authorization-policy"></a>Directiva de autorización de licencia o clave

AMS admite varias maneras de autenticar a los usuarios realizar las solicitudes de licencia o clave. La directiva de autorización de clave de contenido debe ser configurada por el usuario y cumplir el cliente en el orden de la licencia de clave de ser delived al cliente. La directiva de autorización de clave de contenido podría tener una o más restricciones de autorización: **Abrir** o **token** de restricción.

El portal de Azure le permite crear una **Directiva de autorización de licencia o clave** para todos los tipos de cifrado.

###<a name="open"></a>Abrir 

Restricción abrir significa que el sistema proporcionará la clave para cualquier persona que realiza una solicitud de clave. Esta restricción puede ser útil para fines de pruebas. 

### <a name="token"></a>Token

Se adjuntará la directiva token restringido por un token emitido por una Token servicio seguro (STS). Media Services admite tokens en el formato Simple Web token (SWT) y el formato de JSON Web Token (JWT). Media Services no proporciona servicios de Token seguro. Puede crear a un STS personalizado o aprovechar Microsoft Azure ACS tokens de problema. El STS debe estar configurado para crear un símbolo que se ha iniciado sesión con las notificaciones de clave y problema especificadas especificada en la configuración de restricción de token. El servicio de entrega clave Media Services devolverá la clave solicitada (o la licencia) al cliente si el token es válido y las notificaciones de la coincidencia de token aquellos configurados para la clave (o la licencia).

Al configurar el token restringido directiva, debe especificar la clave principal de la comprobación, emisor y parámetros de la audiencia. La clave principal de comprobación contiene la clave que se firmó el token con, emisor es el servicio de token seguro que emite el token. La audiencia (a veces denominada ámbito) describe el objetivo del token o el recurso el token autoriza el acceso a. El servicio de entrega clave Media Services valida que estos valores en el símbolo que coincidan con los valores de la plantilla.

![Proteger el contenido](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>Plantilla de derechos de PlayReady

Para obtener información detallada acerca de la plantilla de derechos de PlayReady, vea [Media Services PlayReady licencia plantilla Overview](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>No persistente

Si configura licencia como no persistente, solo se guarda en memoria mientras el Reproductor está utilizando la licencia.  

![Proteger el contenido](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente

Si configura la licencia como persistente, se guarda en almacenamiento persistente en el cliente.

![Proteger el contenido](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Plantilla de derechos de Widevine

Para obtener información detallada acerca de la plantilla de derechos Widevine, vea [Información general sobre plantillas de Widevine licencia](media-services-widevine-license-template-overview.md).

### <a name="basic"></a>Básico

Cuando se selecciona **básicas**, se creará la plantilla con todos los valores predeterminados.

### <a name="advanced"></a>Avanzadas

Para una explicación detallada acerca de la opción avanzada de Widevine configuraciones, vea [este](media-services-widevine-license-template-overview.md) tema.

![Proteger el contenido](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuración de FairPlay

Para habilitar el cifrado de FairPlay, debe proporcionar el certificado de la aplicación y la aplicación secreto clave (pedir) a través de la opción de configuración de FairPlay. Para obtener información detallada sobre los requisitos y configuración de FairPlay, consulte [este](media-services-protect-hls-with-fairplay.md) artículo.

![Proteger el contenido](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Aplicar cifrado dinámico a su activo

Para aprovechar las ventajas de cifrado dinámico, debe hacer lo siguiente:

- Codificar el archivo de origen en un conjunto de archivos de velocidad adaptación MP4.
- Obtener al menos una unidad de transmisión por secuencias a petición del extremo de transmisión por secuencias desde el que tiene previsto ofrecer su contenido. Para obtener más información, vea [cómo escalar transmisión unidades reservados a petición](media-services-portal-manage-streaming-endpoints.md).

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Seleccionar un activo en el que quiere cifrar

Para ver todos los activos, seleccione **configuración** > **activos**.

![Proteger el contenido](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Cifrar con AES o DRM

Una vez que presiona **cifrar** de un activo, se presentan con dos opciones: **AES** o **DRM**. 

#### <a name="aes"></a>AES

Borrar el cifrado de clave se habilitarán en todos los protocolos de streaming AES: Smooth Streaming, HLS y MPEG guión.

![Proteger el contenido](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM

Cuando se selecciona la ficha DRM, se presentan con distintas opciones de directivas de protección de contenido (que debe tener configurada por ahora) + un conjunto de protocolos de transmisión.

- Va a **PlayReady y Widevine con guión MPEG** - dinámicamente cifrar la secuencia de MPEG-guión con PlayReady y Widevine DRMs.
- **PlayReady y Widevine con guión MPEG + FairPlay con HLS** - se dinámicamente cifrar se secuencia de MPEG-guión con PlayReady y Widevine DRMs. También se le cifrar su secuencias HLS con FairPlay.
- **PlayReady sólo con Smooth Streaming, HLS y guión MPEG** - se dinámicamente cifrar Smooth Streaming, HLS, secuencias MPEG-guión con DRM de PlayReady.
- **Widevine solo con guión MPEG** - dinámicamente se cifre, MPEG-guión con DRM Widevine.
- **FairPlay solo con HLS** - dinámicamente se cifre su secuencia HLS con FairPlay.

Para habilitar el cifrado de FairPlay, debe proporcionar el certificado de la aplicación y la aplicación secreto clave (pedir) a través de la opción de configuración de FairPlay del módulo de configuración de protección de contenido.

![Proteger el contenido](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Una vez que realice la selección de cifrado, haga clic en **Aplicar**.

##<a name="next-steps"></a>Pasos siguientes

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





