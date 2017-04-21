<properties
    pageTitle="Información general de embalaje dinámico | Microsoft Azure"
    description="El tema proporciona e información general sobre embalaje dinámico."
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
    ms.date="10/24/2016" 
    ms.author="juliako"/>


# <a name="dynamic-packaging"></a>Embalaje dinámico

##<a name="overview"></a>Información general

Microsoft Azure Media Services puede usarse para ofrecer muchos origen formatos de archivos multimedia, formatos, de transmisión de medios y formatos de protección de contenido a una amplia variedad de tecnologías de cliente (por ejemplo, iOS, XBOX, Silverlight, Windows 8). Estos clientes entienden distintos protocolos, por ejemplo iOS requiere un formato de transmisión de Live de HTTP (HLS) V4 y Silverlight y Xbox requieren Smooth Streaming. Si tiene un conjunto de velocidad adaptación (entre varios velocidad) MP4 archivos (ISO Base multimedia 14496 12) o un conjunto de velocidad adaptación Smooth Streaming archivos que desea que sirven para los clientes que entienden guión MPEG, HLS o Smooth Streaming, deben sacar partido de embalaje dinámico de Media Services.

Con embalaje dinámico todo lo que necesita es crear un activo, que contiene un conjunto de archivos de velocidad adaptación MP4 o adaptación de velocidad Smooth Streaming. A continuación, según el formato especificado en la convocatoria de manifiesto o fragmento, la transmisión de On Demand server se asegurará de que recibe la secuencia en el protocolo que haya elegido. Como resultado, solo tiene que almacenar y de pago para los archivos en formato de almacenamiento único y servicios multimedia generará y servir la respuesta adecuada en función de las solicitudes de un cliente.

El diagrama siguiente muestra la codificación tradicional y el flujo de trabajo de embalaje estático.

![Codificación estático](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

El diagrama siguiente muestra el flujo de trabajo de embalaje dinámico.

![Codificación dinámicas](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Para aprovechar las ventajas de embalaje dinámico, primero debe obtener al menos una unidad de transmisión por secuencias a petición del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido. Para obtener más información, vea [Cómo escala Media Services](media-services-portal-manage-streaming-endpoints.md).

##<a name="common-scenario"></a>Escenarios comunes

1. Cargar un archivo de entrada (denominado archivo mezzanine). Por ejemplo, H.264, MP4 o WMV (para la lista de formatos admitidos, consulte [Formatos admitidos por el estándar de codificador multimedia](media-services-media-encoder-standard-formats.md).

1. Codificar el archivo mezzanine a conjuntos de velocidad adaptación h.264 MP4.

1. Publicar el activo que contiene la velocidad de adaptación MP4 establecer creando el localizador On Demand.

1. Crear las direcciones URL streaming para acceder a y el contenido de la secuencia.


##<a name="preparing-assets-for-dynamic-streaming"></a>Preparar activos para transmisión dinámica

Para preparar su activo transmisión dinámica tiene dos opciones:

1. [Cargar un archivo maestro](media-services-dotnet-upload-files.md).
2. [Usar el Codificador multimedia codificador estándar para generar conjuntos de velocidad adaptación h.264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [El contenido de la secuencia](media-services-deliver-content-overview.md).


##<a id="unsupported_formats"></a>Formatos que no son compatibles con embalaje dinámico

Los siguientes formatos de archivo de origen no son compatibles con embalaje dinámico.

- Archivos de Dolby digital mp4.
- Archivos suaves de Dolby digitales.

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
