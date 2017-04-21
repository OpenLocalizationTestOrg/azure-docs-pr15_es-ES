<properties 
    pageTitle="Introducción a Azure Media Services y escenarios comunes | Microsoft Azure" 
    description="Este tema ofrece una descripción general de Azure Media Services" 
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
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>

#<a name="azure-media-services-overview-and-common-scenarios"></a>Introducción a Azure Media Services y escenarios comunes

Servicios de Microsoft Azure Media es una plataforma extensible basada en nube que permite a los desarrolladores crear aplicaciones de administración y la entrega de scalable multimedia. Media Services se basa en las API de REST que le permiten de forma segura cargar, almacenar, codificar y empaquetar contenido de audio o vídeo para la entrega de transmisiones a petición y directo a varios clientes (por ejemplo, televisión, PC y dispositivos móviles).

Puede crear flujos de trabajo de llevar a cabo por completo con Media Services. También puede usar los componentes de terceros para algunas partes de un flujo de trabajo. Por ejemplo, codificar con un codificador de terceros. A continuación, cargar, proteger, empaquetar, ofrecer con Media Services.

Puede elegir en secuencia su contenido live o distribuir contenido a petición. Este tema muestra escenarios comunes para ofrecer su contenido [live](media-services-overview.md#live_scenarios) o [a petición](media-services-overview.md#vod_scenarios). El tema también contiene vínculos a otros temas de interés.

## <a name="sdks-and-tools"></a>SDK y herramientas

Para crear soluciones de Media Services, puede usar:

- [Media Services API de REST](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Uno del cliente SDK disponible:
- [Servicios multimedia de azure SDK para .NET](https://github.com/Azure/azure-sdk-for-media-services)
- [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java),
- [Azure SDK PHP](https://github.com/Azure/azure-sdk-for-php),
- [Azure Media Services para Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Es decir, una versión del SDK de Node.js. Se mantiene por una Comunidad y actualmente no tiene una cobertura del 100% de la APIs AMS).
- Herramientas existentes:
- [Portal de Azure](https://portal.azure.com/)
- [Servicios de explorador Azure-multimedia](https://github.com/Azure/Azure-Media-Services-Explorer) (El Explorador de servicios de azure Media (AMSE) es un formularios Windows Forms / aplicación C# para Windows)

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

Puede ver AMS aquí rutas de aprendizaje:

- [AMS Live transmisiones de flujo de trabajo](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS a petición transmisión de flujo de trabajo](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##<a name="prerequisites"></a>Requisitos previos

Para empezar a usar Azure Media Services, debe tener lo siguiente:

3. Una cuenta de Azure. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com).
2. Una cuenta de Azure Media Services. Usar el portal de Azure, .NET o API de REST para crear la cuenta de Azure Media Services. Para obtener más información, vea [Crear una cuenta](media-services-portal-create-account.md).
3. (Opcional) Configurar el entorno de desarrollo. Elija .NET o API de REST en su entorno de desarrollo. Para obtener más información, vea [Configurar el entorno](media-services-dotnet-how-to-use.md).

Además, aprenda a [Conectar mediante programación](media-services-dotnet-connect-programmatically.md).
4. (Recomendado) Asignar uno o más unidades de escala. Se recomienda asignar uno o más unidades de la escala de las aplicaciones en el entorno de producción.   Para obtener más información, vea [Administrar transmisión extremos](media-services-portal-manage-streaming-endpoints.md).

##<a name="concepts-and-overview"></a>Introducción y conceptos

Para los conceptos de Azure Media Services, vea [conceptos](media-services-concepts.md).

Para una serie de procedimientos que se presentan a todos los componentes principales de Azure Media Services, vea [tutoriales paso a paso de Azure Media Services](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Esta serie tiene una excelente introducción de conceptos y utiliza la herramienta AMSE para mostrar las tareas de AMS. Tenga en cuenta que la herramienta AMSE es una herramienta de Windows. Esta herramienta es compatible con la mayoría de las tareas que puede lograr mediante programación con [AMS SDK para .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK para Java](https://github.com/Azure/azure-sdk-for-java)o [PHP SDK de Azure](https://github.com/Azure/azure-sdk-for-php).

##<a id="vod_scenarios"></a>Realiza multimedia a petición con Azure Media Services: escenarios y tareas comunes

En esta sección se describe los escenarios comunes y proporciona vínculos a temas relevantes. El siguiente diagrama muestra las partes principales de la plataforma de Media Services que intervienen en la entrega de contenido a petición. 

![Flujo de trabajo VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###<a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Proteger el contenido de almacenamiento y entregar transmisiones sin cifrar (no cifrados)

1. Cargar un archivo de alta calidad mezzanine en un activo.
    
    Se recomienda aplicar la opción de cifrado de almacenamiento a su activo para proteger el contenido durante la carga y al resto de almacenamiento.
 
1. Codificar a un conjunto de archivos de velocidad adaptación MP4. 

    Se recomienda para aplicar la opción de cifrado de almacenamiento al activo de salida para proteger el contenido al resto.
    
1. Configurar la directiva de entrega de activos (utilizada por embalaje dinámico). 
    
    Si el activo es almacenamiento cifrado, **debe** configurar Directiva de entrega de activos. 

1. Publicar el activo mediante la creación de un localizador de petición.

    Asegúrese de tener al menos una unidad reservada en el extremo streaming desde la que desee para transmitir contenido de transmisión.

1. Secuencia de contenido publicado.

###<a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteger el contenido de almacenamiento, entregar transmisiones dinámicamente cifrada  

Para poder usar cifrado dinámico, primero debe obtener al menos una unidad reservada streaming en el extremo streaming desde la que desea transmitir contenido cifrado.

1. Cargar un archivo de alta calidad mezzanine en un activo. Aplicar la opción de cifrado de almacenamiento al activo.
1. Codificar a un conjunto de archivos de velocidad adaptación MP4. Opción de almacenamiento de cifrado se aplican a los activos de salida.
1. Crear clave de cifrado de contenido para el activo que desea cifrar dinámicamente durante la reproducción.
2. Configurar la directiva de autorización de clave de contenido.
1. Configurar la directiva de entrega de activos (utilizada por embalaje dinámico y cifrado dinámico).
1. Publicar el activo mediante la creación de un localizador de petición.
1. Secuencia de contenido publicado. 

###<a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Usar el análisis de medios para derivan perspectivas que puede utilizar los vídeos 

Análisis de Media es una colección de componentes de voz y visuales que hacen que sea más fácil para las organizaciones y empresas para obtener información útil de sus archivos de vídeo. Para obtener más información, vea [Introducción a Azure Media Services análisis](media-services-analytics-overview.md).

1. Cargar un archivo de alta calidad mezzanine en un activo.
2. Use uno de los siguientes servicios de análisis de medios para procesar los vídeos:
    
    - **Indizador** - [vídeos de proceso con Azure Media indizador 2](media-services-process-content-with-indexer2.md)
    - **Hyperlapse** : [archivos multimedia de Hyperlapse con Hyperlapse multimedia de Azure](media-services-hyperlapse-content.md)
    - **Detección de movimiento** : [Detección de movimiento para análisis de multimedia de Azure](media-services-motion-detection.md).
    - **Detección de cara y emociones cara** – [nominal y detección de emoción para análisis de multimedia de Azure](media-services-face-and-emotion-detection.md).
    - **Resumen de vídeo** : [Usar miniaturas de vídeo de Media de Azure para crear un resumen de vídeo](media-services-video-summarization.md)
3. Procesadores de media de análisis de medios generan archivos MP4 o archivos de JSON. Si un procesador multimedia produce un archivo MP4, puede descargar el archivo cada vez. Si un procesador multimedia genera un archivo JSON, puede descargar el archivo desde el almacenamiento de blobs de Windows Azure. 


###<a name="deliver-progressive-download"></a>Entregar progresiva descarga 

1. Cargar un archivo de alta calidad mezzanine en un activo.
1. Codificar en un único archivo MP4.
1. Publicar el activo mediante la creación de un localizador petición o SA.

    Si usa localizador de petición, asegúrese de tener al menos una transmisión unidad reservada en el extremo streaming desde la que desea descargar el contenido cada vez.

    Si usa localizador SA, el contenido se descargará desde el almacenamiento de blobs de Windows Azure. En este caso, es necesario tener la transmisión unidades reservados.
  
1. Descargar el contenido cada vez.

##<a id="live_scenarios"></a>Entrega de eventos Streaming directo con Azure Media Services

Cuando se trabaja con Live transmisión suelen relacionarse los siguientes componentes:

- Una cámara que se utiliza para difundir un evento.
- Codificador vídeo directo que convierte señales de la cámara a secuencias que se envían a un servicio de transmisión directo.

Opcionalmente, varios codificadores directo tiempo sincronizado. Críticas para determinados eventos petición muy alta disponibilidad y la calidad de la experiencia, se recomienda emplear activo activo codificadores redundantes con hora sincronizaciónPara lograr failover transparente sin pérdida de datos en directo.
- Un servicio de transmisión directo que le permite hacer lo siguiente:

- incorporación de contenido directo mediante varios protocolos de streaming directo (por ejemplo RTMP o Smooth Streaming)
- (opcionalmente) codificar su secuencia en secuencia de adaptación de velocidad de bits
- obtener una vista previa de la secuencia en directo
- grabar y almacenar el contenido integrado para ser transmitida posterior (vídeo a la carta)
- entregar el contenido a través de protocolos de transmisión comunes (por ejemplo, MPEG guión, suave, HLS, HDS) directamente a los clientes o a una red de entrega de contenido (CDN) para ampliar la distribución.


**Microsoft Azure Media Services** (AMS) proporciona la capacidad de recopilar, codificar, obtener una vista previa, almacenar y ofrecer la transmisión de contenido directo.

Cuando realiza el contenido a los clientes su objetivo es ofrecer un vídeo de alta calidad a varios dispositivos en condiciones de red diferente. Para tener en cuenta las condiciones de calidad y red, use codificadores directo para codificar la secuencia de secuencia de vídeo de velocidad de bits múltiple (adaptación de velocidad de bits).  Para ocuparse de transmisión en los distintos dispositivos, use Media Services [embalaje dinámico](media-services-dynamic-packaging-overview.md) dinámicamente empaquetar volver a la secuencia de protocolos diferentes. Media Services admite la entrega de la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe).

En Azure Media Services, **canales**, **programas**y **StreamingEndpoints** controlar todas las funciones streaming directo incluidos ingesta, formato, DVR, seguridad, escalabilidad y redundancia.

Un **canal** representa una canalización para el procesamiento de contenido de transmisión por secuencias en directo. Un canal puede recibir un live secuencias de entrada de las siguientes maneras:

- En local live codificador envía múltiples velocidad **RTMP** o **Smooth Streaming** (fragmentados MP4) para el canal que está configurado para el envío de **paso a través** . La entrega de **paso a través** es cuando las secuencias integradas pasan a través de **canal**sin ningún otro proceso. Puede usar los siguientes codificadores directo entre varios velocidad Smooth Streaming de salida: elementos, Envivio, Cisco.  Los siguientes codificadores directo salida RTMP: transcodificadores Adobe Flash Live, Telestream Wirecast y Tricaster.  Un codificador directo también puede enviar una secuencia de velocidad solo en un canal que no está habilitado para codificación en directo, pero que no se recomienda. Cuando se solicita, Media Services ofrece la secuencia a los clientes.

>[AZURE.NOTE] Paso a través del método es la manera más económica live streaming cuando hacen varios eventos durante un largo período de tiempo y, a continuación, ya se ha invertido en codificadores local. Ver detalles de [precios](/pricing/details/media-services/) .

- Un codificador de live local envía una secuencia de velocidad solo para el canal que está habilitado para realizar la codificación en directo con Media Services en uno de los siguientes formatos: RTP (MPEG-TS), RTMP o Smooth Streaming (fragmentar MP4). El canal, a continuación, realiza la codificación en directo de la entrada secuencia de velocidad único en una secuencia de vídeo de multi-velocidad (adaptación). Cuando se solicita, Media Services ofrece la secuencia a los clientes.


###<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabajar con los canales que reciban secuencia en directo entre varios velocidad de codificadores local (paso)

El siguiente diagrama muestra las partes principales de la plataforma de AMS que intervienen en el flujo de trabajo de **paso a través** .

![Flujo de trabajo activa][live-overview2]

Para obtener más información, vea [trabajar con canales que Multi recepción-velocidad secuencia en directo de codificadores local](media-services-live-streaming-with-onprem-encoders.md).

###<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabajar con los canales que están habilitados para realizar la codificación en directo con Azure Media Services

El siguiente diagrama muestra las partes principales de la plataforma de AMS que intervienen en Live transmisión del flujo de trabajo donde está habilitado un canal para realizar la codificación en directo con Media Services.

![Flujo de trabajo activa][live-overview1]

Para obtener más información, vea [trabajar con canales que están habilitados para realizar codificación en directo con Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).


##<a name="consuming-content"></a>Consumo de contenido

Azure Media Services proporciona las herramientas que necesita para crear aplicaciones de Reproductor de cliente enriquecido dinámica para la mayoría de las plataformas incluidas: iOS dispositivos, dispositivos Android, Windows, Windows Phone, Xbox y Set-top cuadros. El tema siguiente proporciona vínculos a los SDK y marcos de Reproductor que puede usar para desarrollar sus propias aplicaciones cliente que pueden consumir transmisiones de Media Services.

[Desarrollo de aplicaciones de Reproductor de vídeo](media-services-develop-video-players.md)

##<a name="enabling-azure-cdn"></a>Habilitar CDN de Azure

Media Services admite la integración con Azure CDN. Para obtener información sobre cómo habilitar CDN de Azure, consulte [cómo administrar transmisión extremos en una cuenta de servicios de medios](media-services-portal-manage-streaming-endpoints.md).

##<a name="scaling-a-media-services-account"></a>Ajuste de escala de una cuenta de servicios de medios

Puede escalar **Media Services** especificando el número de **Unidades reservado transmisión** y **Codificación reservado unidades** que desea que estén configurados con su cuenta de.

También puede escalar su cuenta de Media Services agregando cuentas de almacenamiento a él. Cada cuenta de almacenamiento se limita a 500 TB. Para expandir el almacenamiento más allá de las limitaciones de forma predeterminada, puede adjuntar varias cuentas de almacenamiento para una sola cuenta de Media Services.

[Este](media-services-portal-scale-streaming-endpoints.md) tema se vincula a temas relevantes.

##<a name="support"></a>Soporte técnico

[Soporte técnico de Azure](https://azure.microsoft.com/support/options/) proporciona opciones de soporte para Azure, incluidos los servicios de medios.

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="service-level-agreement-sla"></a>Contrato de nivel de servicio (SLA)

- Para la codificación de servicios de medios, garantizamos 99,9% de disponibilidad de transacciones de la API de REST.
- Para Streaming, se le correctamente solicitudes de servicio con contenido multimedia existente en la garantía de disponibilidad de un 99,9% al comprar al menos una unidad de transmisión.
- Para Live canales, garantizamos que ejecuta canales tenga conectividad externa al menos un 99,9% del tiempo.
- Protección del contenido, garantizamos que nos correctamente cumplirá las solicitudes clave al menos 99,9% del tiempo.
- Indizador, se correctamente atienda las solicitudes de tarea indizador procesadas con un reservado de codificación unidad 99,9% del tiempo.

Para obtener más información, vea [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 
