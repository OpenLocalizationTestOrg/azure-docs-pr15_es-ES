<properties
    pageTitle=" Escala de transmisión extremos con el portal de Azure | Microsoft Azure"
    description="Este tutorial le guiará por los pasos de escala streaming extremos con el portal de Azure."
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


# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Escala de transmisión extremos con el portal de Azure

##<a name="overview"></a>Información general

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 

Cuando se trabaja con uno de los escenarios más comunes ofrece vídeo a través de la velocidad de adaptación transmisión a los clientes de Azure Media Services. Media Services es compatible con la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe).

Media Services proporciona embalaje dinámico que le permite ofrecer la velocidad de adaptación contenido MP4 codificado en formatos admitidos por el Media Services (MPEG guión, HLS, Smooth Streaming, HDS) just-in-time, sin tener que almacenar versiones preconfiguradas de cada uno de estos formatos de transmisión de transferencia.

Para aprovechar las ventajas de embalaje dinámico, debe hacer lo siguiente:

- Codificar el archivo mezzanine (origen) en un conjunto de archivos de velocidad adaptación MP4 (se muestran los pasos de codificación más adelante en este tutorial).  
- Crear al menos una unidad de transmisión por secuencias para la *transmisión de extremo* desde el que tiene previsto entrega el contenido. Los pasos que muestran cómo cambiar el número de unidades streaming.

Con embalaje dinámico solo debe almacenar y de pago para los archivos en formato de almacenamiento único y Media Services generará y servir la respuesta adecuada en función de las solicitudes de un cliente.

Además, puede controlar la capacidad del servicio de transmisión extremo manejar las necesidades de ancho de banda crecientes ajustando streaming unidades. Se recomienda asignar uno o más unidades de la escala de las aplicaciones en el entorno de producción. Unidades de transmisión proporcionarán ambas capacidad de salida dedicado que se puede comprar en incrementos de 200 Mbps y funcionalidad qué funcionalidad que incluye: [embalaje dinámico](media-services-dynamic-packaging-overview.md), la integración de CDN y la configuración avanzada. Para obtener más información, vea [Administrar extremos streaming con el portal de Azure](media-services-portal-manage-streaming-endpoints.md).

## <a name="scale-streaming-endpoints"></a>Escala de transmisión extremos

Para crear y cambiar el número de unidades reservadas de transmisión, haga lo siguiente:

1. En el [portal de Azure](https://portal.azure.com/), seleccione su cuenta de Azure Media Services.
2. En la ventana **configuración** , seleccione **extremos de flujo de datos**.
3. Haga clic en el extremo de transmisión por secuencias que desee escalar. 
4. Mueva el control deslizante para especificar el número de transmisiones de unidades
 
![Extremo Streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

Aplica teniendo en cuenta lo siguiente:

- La asignación de las nuevas unidades streaming puede tardar unos 20 minutos para completar. 
- Actualmente, va desde cualquier valor positivo de transmisión de unidades a ninguno, puede deshabilitar a petición transmisión para hasta una hora.
- El número máximo de unidades especificadas para el período de 24 horas se utiliza para calcular el costo. Para obtener información acerca de detalles de los precios, consulte [Detalles de precios de servicios de medios](http://go.microsoft.com/fwlink/?LinkId=275107).

##<a name="next-steps"></a>Pasos siguientes

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


