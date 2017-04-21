<properties 
    pageTitle="Información general sobre Live agua con Azure Media Services | Microsoft Azure" 
    description="Este tema ofrece una descripción general de agua directo con Azure Media Services." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="juliako"/>

#<a name="overview-of-live-steaming-using-azure-media-services"></a>Información general sobre Live agua con Azure Media Services

##<a name="overview"></a>Información general

Cuando realiza eventos streaming directo con Azure Media Services comúnmente participan los siguientes componentes:

- Una cámara que se utiliza para difundir un evento.
- Codificador vídeo directo que convierte señales de la cámara a secuencias que se envían a un servicio de transmisión directo.

    Opcionalmente, varios codificadores directo tiempo sincronizado. Críticas para determinados eventos petición muy alta disponibilidad y la calidad de la experiencia, se recomienda emplear activo activo codificadores redundantes con la sincronización de tiempo para lograr failover transparente sin pérdida de datos en directo.
- Un servicio de transmisión directo que le permite hacer lo siguiente:
    
    - incorporación de contenido directo mediante varios protocolos de streaming directo (por ejemplo RTMP o Smooth Streaming)
    - (opcionalmente) codificar su secuencia en secuencia de adaptación de velocidad de bits
    - obtener una vista previa de la secuencia en directo
    - grabar y almacenar el contenido integrado para ser transmitida posterior (vídeo a la carta)
    - entregar el contenido a través de protocolos de transmisión comunes (por ejemplo, MPEG guión, suave, HLS, HDS) directamente a los clientes o a una red de entrega de contenido (CDN) para ampliar la distribución.


**Microsoft Azure Media Services** (AMS) proporciona la capacidad de recopilar, codificar, obtener una vista previa, almacenar y ofrecer la transmisión de contenido directo.

Cuando realiza el contenido a los clientes su objetivo es ofrecer un vídeo de alta calidad a varios dispositivos en condiciones de red diferente. Para ello, use codificadores directo para codificar la secuencia de una secuencia de vídeo de velocidad de bits múltiple (adaptación de velocidad de bits).  Para ocuparse de transmisión en los distintos dispositivos, use Media Services [embalaje dinámico](media-services-dynamic-packaging-overview.md) dinámicamente empaquetar volver a la secuencia de protocolos diferentes. Media Services admite la entrega de la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe).

En Azure Media Services, **canales**, **programas**y **StreamingEndpoints** controlar todas las funciones streaming directo incluidos ingesta, formato, DVR, seguridad, escalabilidad y redundancia.

Un **canal** representa una canalización para el procesamiento de contenido de transmisión por secuencias en directo. Un canal puede recibir un live secuencias de entrada de las siguientes maneras:

- En local live codificador envía múltiples velocidad **RTMP** o **Smooth Streaming** (fragmentados MP4) para el canal que está configurado para el envío de **paso a través** . La entrega de **paso a través** es cuando las secuencias integradas pasan a través de **canal**sin ningún otro proceso. Puede usar los siguientes codificadores directo entre varios velocidad Smooth Streaming de salida: elementos, Envivio, Cisco.  Los siguientes codificadores directo salida RTMP: transcodificadores Adobe Flash Media Live codificador (FMLE), Telestream Wirecast y Tricaster.  Un codificador directo también puede enviar una secuencia de velocidad solo en un canal que no está habilitado para codificación en directo, pero que no se recomienda. Cuando se solicita, Media Services ofrece la secuencia a los clientes.

    >[AZURE.NOTE] Paso a través del método es la manera más económica live streaming cuando hacen varios eventos durante un largo período de tiempo y, a continuación, ya se ha invertido en codificadores local. Ver detalles de [precios](/pricing/details/media-services/) .
    
    
- Un codificador de live local envía una secuencia de velocidad solo para el canal que está habilitado para realizar la codificación en directo con Media Services en uno de los siguientes formatos: RTMP o Smooth Streaming (fragmentados MP4). RTP (MPEG-TS) también es compatible, ya que dispone de una conexión dedicada al centro de datos de Azure. Los siguientes codificadores directo con RTMP salida se conocen para trabajar con canales de este tipo: Telestream Wirecast, FMLE. El canal, a continuación, realiza la codificación en directo de la entrada secuencia de velocidad único en una secuencia de vídeo de multi-velocidad (adaptación). Cuando se solicita, Media Services ofrece la secuencia a los clientes.


A partir de la versión 2.10 de servicios de medios, cuando se crea un canal, puede especificar de qué manera que desee para que el canal recibir la secuencia de entrada y si no desea para que el canal realizar la codificación en directo de la secuencia. Tiene dos opciones:

- **Ninguno** (paso a través): especifique este valor si piensa utilizar un codificador de live local que se envían entre varios velocidad secuencia (una secuencia de paso a través). En este caso, la secuencia entrante pasa a la salida sin ninguna codificación. Este es el comportamiento de un canal antes de la versión 2.10.  

- **Estándar** : seleccione este valor si piensa utilizar Media Services para codificar la secuencia directo velocidad solo en secuencia de velocidad de entre varios. Este método es más económico para escalar rápidamente para eventos poco frecuentes. Tenga en cuenta que hay un impacto facturación de codificación en directo y recuerde que deja un canal de codificación en directo en el estado "Ejecutar" producirá cargos de facturación.  Se recomienda detener la ejecución canales inmediatamente después el evento streaming directo para evitar cargos por horas extra. 

##<a name="comparison-of-channel-types"></a>Comparación de tipos de canal

Tabla siguiente proporciona a una guía para comparar los dos tipos de canal compatibles con servicios de medios

Característica|Paso a través del canal|Canal estándar
---|---|---
Entrada de velocidad de bits solo se codifica en varias velocidades de bits bajas en la nube|No|Sí
Resolución máxima, número de capas|1080p, 8 capas, 60 fps|720p, 6 capas, 30fps
Protocolos de entrada|RTMP, transmisión por secuencias suave|RTMP, Smooth Streaming y RTP
Precio|Consulte la [página de precios](/pricing/details/media-services/) y haga clic en la pestaña "Live vídeo".|Consulte la [página de precios](/pricing/details/media-services/) 
Tiempo de ejecución máximo|24 x 7|8 horas
Soporte técnico para insertar las tabletas|No|Sí
Soporte técnico para señalización ad|No|Sí
Títulos de CEA 608/708 paso a través|Sí|Sí
Capacidad de recuperación de detenciones breves en fuente de contribución|Sí|No (canal comenzará slating después de 6 + segundos sin datos de entrada)
Soporte técnico para no uniforme GOP de entrada|Sí|No, debe corregirse entrada 2 seg GOP
Compatibilidad con entrada de tasas variables de marco|Sí|No, entrada debe corregirse velocidad.<br/>Se tolerarán pequeñas variaciones, por ejemplo, durante las escenas de alta. Pero no se puede quitar codificador a 10 marcos/seg.
Automático-interrupción de canales de entrada cuando la fuente se pierde|No|Después de 12 horas, si no hay ningún programa en ejecución 

##<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabajar con los canales que reciban secuencia en directo entre varios velocidad de codificadores local (paso)

El siguiente diagrama muestra las partes principales de la plataforma de AMS que intervienen en el flujo de trabajo de **paso a través** .

![Flujo de trabajo activa](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Para obtener más información, vea [trabajar con canales que Multi recepción-velocidad secuencia en directo de codificadores local](media-services-live-streaming-with-onprem-encoders.md).

##<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabajar con los canales que están habilitados para realizar la codificación en directo con Azure Media Services

El siguiente diagrama muestra las partes principales de la plataforma de AMS que intervienen en Live transmisión del flujo de trabajo donde está habilitado un canal para realizar la codificación en directo con Media Services.

![Flujo de trabajo activa](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Para obtener más información, vea [trabajar con canales que están habilitados para realizar codificación en directo con Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

##<a name="description-of-a-channel-and-its-related-components"></a>Descripción de un canal y sus componentes relacionados

###<a name="channel"></a>Canal

En Media Services, s de [canal](https://msdn.microsoft.com/library/azure/dn783458.aspx)son responsables de procesamiento de contenido de transmisión por secuencias en directo. Un canal proporciona un extremo de entrada (recopile la dirección URL) a continuación, proporcione a un transcodificador directo. El canal recibe directo secuencias de entrada de la transcodificador directo y hace que esté disponible para la transmisión a través de uno o más StreamingEndpoints. Los canales también proporcionan un extremo de vista previa (vista previa URL) que usa para obtener una vista previa y validar la secuencia antes de procesamiento y la entrega.

Puede obtener la dirección URL de ingesta y la dirección URL de vista previa al crear el canal. Para obtener estas direcciones URL, el canal no tiene que estar en el estado iniciado. Cuando esté listo para iniciar la inserción de datos de un transcodificador directo en el canal, debe iniciar el canal. Una vez que la transcodificador directo inicia recopila datos, puede obtener una vista previa de la secuencia.

Cada cuenta Media Services puede contener varios canales, varios programas y StreamingEndpoints varias. Según las necesidades de seguridad y el ancho de banda, StreamingEndpoint servicios pueden estar dedicados a uno o más canales. Puede extraer cualquier StreamingEndpoint ningún canal.


###<a name="program"></a>Programa 

Un [programa](https://msdn.microsoft.com/library/azure/dn783463.aspx) le permite controlar la publicación y el almacenamiento de segmentos en una secuencia en directo. Canales administración programas. La relación de canal y programa es muy similar a medios tradicionales donde un canal tiene un flujo constante de contenido y un programa se centra en algunas evento con hora en dicho canal.
Puede especificar el número de horas que desea conservar el contenido registrado para el programa estableciendo la propiedad **ArchiveWindowLength** . Este valor se puede establecer desde un mínimo de 5 minutos hasta un máximo de 25 horas. 

ArchiveWindowLength determina también que puede buscar la cantidad máxima de clientes de tiempo en el tiempo desde la posición actual directo. Pueden ejecutar programas durante el período de tiempo especificado, pero continuamente se descarta el contenido que se encuentre detrás de la longitud de la ventana. El valor de esta propiedad también determina cuánto tiempo pueden aumentar los manifiestos de cliente.

Cada programa está asociado con un activo. Para publicar el programa debe crear un localizador para el activo asociado. Tener este localizador le permitirá generar una URL streaming que puede proporcionar a sus clientes.

Un canal es compatible con hasta tres programas al mismo tiempo, por lo que puede crear varios archivos de la misma secuencia entrante. Esto le permite publicar y archivar distintas partes de un evento según sea necesario. Por ejemplo, necesidades de su empresa es archivar 6 horas de un programa, pero difundir solo últimos 10 minutos. Para ello, debe crear dos programas al mismo tiempo. Un programa se establece archivar 6 horas del evento, pero no se ha publicado el programa. El otro programa está establecido en archivar para 10 minutos y este programa se publica.


##<a name="billing-implications"></a>Implicaciones de facturación

Un canal comienza en cuanto es transiciones de estado a "Ejecutar" a través de la API de facturación.  

La siguiente tabla muestra cómo se asignan los Estados de canal a Estados de facturación en el portal de API y Azure. Observe que los Estados son ligeramente diferentes entre la API y UX de Portal. Tan pronto como un canal está en el estado "Móvil" a través de la API o en el estado "Listo" o "Streaming" en el portal de Azure, facturación estará activa.

Para detener el canal de facturación aún más, debe detener el canal a través de la API o en el portal de Azure.
Usted es responsable de detener los canales cuando haya terminado con el canal. Se da error para detener el canal de facturación continuado.

>[AZURE.NOTE]Cuando trabaje con canales estándares, AMS automático interrupción cualquier canal que aún está en estado "Running" 12 horas después de la fuente de entrada se pierde y no hay ningún programas que se ejecutan. Sin embargo, se siguen le facturará por vez que el canal estaba en estado de "Ejecutar".

###<a id="states"></a>Estados de canal y cómo se asignan al modo de facturación 

El estado actual de un canal. Los valores posibles incluyen:

- **Detener**. Este es el estado inicial del canal después de su creación (a menos que iniciar automáticamente seleccionado en el portal.) Sin facturación se produce en este estado. En este estado, se pueden actualizar las propiedades del canal, pero no se permite la transmisión.
- **Iniciar**. Se inicia el canal. Sin facturación se produce en este estado. No hay actualizaciones o transmisión está permitido durante este estado. Si se produce un error, el canal devuelve el estado de su interrupción.
- **Ejecutar**. El canal es capaz de procesar secuencias en directo. Ahora factura uso. Detenga el canal para evitar que más de facturación. 
- **Detener**. Detenido el canal. Sin facturación se produce en este estado transitoria. No hay actualizaciones o transmisión está permitido durante este estado.
- **Eliminar**. Se está eliminando el canal. Sin facturación se produce en este estado transitoria. No hay actualizaciones o transmisión está permitido durante este estado.

La siguiente tabla muestra cómo se asignan los Estados de canal para el modo de facturación. 
 
Estado de canal|Indicadores de portal de la interfaz de usuario|¿Es facturación?
---|---|---
Iniciar|Iniciar|Sin (estado transitoria)
Ejecutar|Listo (sin programas en ejecución)<br/>o<br/>Streaming (al menos un programa en ejecución)|Sí
Detener|Detener|Sin (estado transitoria)
Detener|Detener|No


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="related-topics"></a>Temas relacionados

[Especificación de recopilación de servicios multimedia de Azure MP4 fragmentados Live](media-services-fmp4-live-ingest-overview.md)

[Trabajar con los canales que están habilitados para realizar codificación en directo con Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Trabajar con los canales que reciban secuencia en directo entre varios velocidad de codificadores local](media-services-live-streaming-with-onprem-encoders.md)

[Cuotas y limitaciones](media-services-quotas-and-limitations.md).  

[Conceptos de servicios de medios](media-services-concepts.md)
