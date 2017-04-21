<properties 
    pageTitle="Administrar transmisiones extremos con el portal de Azure | Microsoft Azure" 
    description="Este tema muestra cómo administrar streaming extremos con el portal de Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    writer="juliako" 
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


#<a name="manage-streaming-endpoints-with-the-azure-portal"></a>Administrar transmisiones extremos con el portal de Azure

## <a name="overview"></a>Información general

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 

En Microsoft Azure Media Services, un **Extremo de transmisión** representa un servicio de transmisión que puede proporcionar contenido directamente a una aplicación de reproducción de cliente o a una red de entrega de contenido (CDN) para ampliar la distribución. Media Services también proporciona una integración de CDN de Azure. La secuencia de salida de un servicio de StreamingEndpoint puede ser una secuencia en directo o un vídeo a petición activo en su cuenta de Media Services.

Además, puede controlar la capacidad del servicio de transmisión extremo manejar las necesidades de ancho de banda crecientes ajustando streaming unidades. Se recomienda asignar uno o más unidades de la escala de las aplicaciones en el entorno de producción. Unidades de transmisión proporcionarán la capacidad de salida dedicado que se puede comprar en incrementos de 200 Mbps y funcionalidad adicional, que incluye: [embalaje dinámico](media-services-dynamic-packaging-overview.md), la integración de CDN y la configuración avanzada.

>[AZURE.NOTE]Solo se cargarán cuando el extremo de transmisión está en estado de ejecución.

Este tema ofrece una descripción general de las funciones principales que se proporcionan transmisión extremos. El tema también muestra cómo usar el portal de Azure para administrar los extremos streaming. Para obtener información acerca de cómo cambiar la escala del extremo de transmisión por secuencias, vea [este](media-services-portal-scale-streaming-endpoints.md) tema.

## <a name="start-managing-streaming-endpoints"></a>Empiece a administrar streaming extremos

Para empezar a administrar streaming extremos de la cuenta, haga lo siguiente.

1. En el [portal de Azure](https://portal.azure.com/), seleccione su cuenta de Azure Media Services.
2. En la ventana **configuración** , seleccione **extremos de flujo de datos**.

    ![Extremo Streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

##<a name="adddelete-a-streaming-endpoint"></a>Agregar o eliminar un punto final de secuencias

Para agregar o eliminar streaming extremo con el portal de Azure, haga lo siguiente:

1. Para agregar un extremo streaming, haga clic en el **+ punto final de** la parte superior de la página. 
2. Para eliminar un extremo streaming, presione el botón **Eliminar** . 

    No se puede eliminar la transmisión extremo predeterminado.
2. Haga clic en el botón **Iniciar** para iniciar el extremo streaming.

    ![Extremo Streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

De forma predeterminada puede tener hasta dos extremos streaming. Si necesita solicitar más información, consulte [cuotas y limitaciones](media-services-quotas-and-limitations.md).
    
##<a id="configure_streaming_endpoints"></a>Configuración del extremo Streaming

Extremo de transmisión le permite configurar las siguientes propiedades cuando tenga al menos 1 unidad de escala: 

- Control de acceso
- Control de caché
- Entre las directivas de acceso de sitio

Para obtener información detallada acerca de estas propiedades, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Puede configurar extremo streaming haciendo lo siguiente:

1. Seleccione el extremo de transmisión por secuencias que desea configurar.
1. Haga clic en **configuración**.
  
Una breve descripción de los campos sigue.

![Extremo Streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)
  
1. Directiva de caché máximo: se usa para configurar la duración de la caché de activos servida a través de este extremo streaming. Si se especifica ningún valor, se usa el valor predeterminado. Los valores predeterminados también pueden definirse directamente en el almacenamiento de Azure. Si CDN de Azure está habilitada para el extremo streaming, no debe establecer el valor de la directiva de caché a menos de 600 segundos.  

2. Direcciones IP permitidas: se utiliza para especificar las direcciones IP que podrán conectarse al extremo streaming publicado. Si no hay direcciones IP especificado, cualquier dirección IP sería pueda conectarse. Direcciones IP que se pueden especificar como una dirección IP (por ejemplo, ' 10.0.0.1'), un intervalo IP con una dirección IP y la máscara de subred CIDR (por ejemplo, ' 10.0.0.1/22') o un rango IP utilizando la dirección IP y una máscara de subred decimales con puntos (por ejemplo, ' 10.0.0.1(255.255.255.0)').

3. Configuración de autenticación de encabezado de firma Akamai: se utiliza para especificar cómo se configura la solicitud de autenticación de encabezado de firma de Akamai servidores. Caducidad es en UTC.



##<a id="enable_cdn"></a>Habilitar la integración de CDN de Azure

Puede especificar para habilitar la integración de Azure CDN para un extremo de transmisión (está deshabilitada de forma predeterminada).

Para configurar la integración de CDN de Azure en true:

- El extremo streaming debe tener al menos una unidad streaming. Si más tarde desea establecer las unidades de escala a 0, primero debe deshabilitar la integración de CDN. De forma predeterminada cuando se crea una nueva transmisión se establece automáticamente una unidad de transmisión de extremo.

- El extremo streaming debe estar en un estado de detención. Una vez que se habilita la CDN, puede iniciar el extremo streaming. 

Podría tardar hasta 90 min para la integración de Azure CDN obtener habilitadas.  Se necesitan dos horas para que los cambios que están activas en todos los CDN POP.

Está habilitada la integración de CDN en todos los centros de datos de Azure: nos oeste, nos Oriente, Europa del Norte, Europa occidental, Japón oeste, Japón oriental, sudeste asiático y Asia oriental.

Una vez habilitada, se deshabilita la configuración de **Control de acceso** .

![Extremo Streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

>[AZURE.IMPORTANT] Integración de Azure Media Services con Azure CDN se implementa en **Azure CDN de Verizon**.  Si desea usar **Azure CDN de Akamai** para Azure Media Services, debe [Configurar el extremo de forma manual](../cdn/cdn-create-new-endpoint.md).  Para obtener más información sobre las características de Azure CDN, vea la [información general CDN](../cdn/cdn-overview.md).

###<a name="additional-considerations"></a>Consideraciones adicionales

- Cuando se habilita CDN para un extremo streaming, clientes no pueden solicitar el contenido directamente desde el origen. Si necesita la capacidad para probar el contenido con o sin CDN, puede crear otro extremo streaming que no está habilitado de CDN.
- El nombre de host de extremo streaming se mantiene igual después de habilitar CDN. No es necesario realizar los cambios en el flujo de trabajo de servicios multimedia después CDN está habilitado. Por ejemplo, si el nombre de host de extremo streaming es strasbourg.streaming.mediaservices.windows.net, después de habilitar CDN, se utiliza el nombre de host mismo exacta.
- Nuevos extremos streaming, puede habilitar CDN creando un nuevo punto final; para los extremos streaming existentes, debe detener primero el extremo y, a continuación, habilite la CDN.
 

Para obtener más información, vea [integración de anuncio de Azure Media Services con Azure CDN (red de entrega de contenido)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##<a name="next-steps"></a>Pasos siguientes

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
