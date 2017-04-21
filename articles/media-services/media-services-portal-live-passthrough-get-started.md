<properties 
    pageTitle="Cómo llevar a cabo la transmisión directo con codificadores local con el portal de Azure | Microsoft Azure" 
    description="Este tutorial le guiará por los pasos de la creación de un canal que está configurado para el envío de paso a través." 
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
    ms.topic="get-started-article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


#<a name="how-to-perform-live-streaming-with-on-premise-encoders-using-the-azure-portal"></a>Cómo llevar a cabo la transmisión directo con codificadores local con el portal de Azure

> [AZURE.SELECTOR]
- [Portal]( media-services-portal-live-passthrough-get-started.md)
- [.NET]( media-services-dotnet-live-encode-with-onpremises-encoders.md)
- [REST]( https://msdn.microsoft.com/library/azure/dn783458.aspx)

Este tutorial le guiará por los pasos con el portal de Azure para crear un **canal** que está configurado para el envío de paso a través. 

##<a name="prerequisites"></a>Requisitos previos

Para completar el tutorial se requiere lo siguiente:

- Una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Una cuenta de Media Services. Para crear una cuenta de Media Services, vea [cómo crear una cuenta de servicios de medios](media-services-portal-create-account.md).
- Una cámara Web. Por ejemplo, [Telestream Wirecast codificador](http://www.telestream.net/wirecast/overview.htm).

Es recomendable revisar los siguientes artículos:

- [Azure Media Services RTMP soporte técnico y Live codificadores](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Información general sobre Live agua con Azure Media Services](media-services-manage-channels-overview.md)
- [Live transmisión con codificadores local que crear secuencias de velocidad múltiple](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Escenario de transmisión directo comunes

Los pasos siguientes describen las tareas relacionadas con la creación de aplicaciones streaming directo comunes que utilizan los canales que se han configurado para el envío de paso a través. Este tutorial muestra cómo crear y administrar un canal de paso a través y eventos en directo.

1. Conectar una cámara de vídeo en un equipo. Iniciar y configurar un codificador live local que genera una secuencia de velocidad múltiple RTMP o fragmentar MP4. Para obtener más información, vea [soporte técnico de Azure Media Services RTMP y codificadores Live](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    También se pudo realizar este paso después de crear el canal.

1. Crear e iniciar un canal de paso a través.
1. Recuperar el canal recopile la dirección URL. 

    La dirección URL de ingesta se usa el codificador directo para enviar la secuencia para el canal.
1. Recuperar la dirección URL de vista previa de canal. 

    Utilizar esta dirección URL para comprobar que su canal correctamente recibe la secuencia en directo.

3. Crear un evento directo o programa. 

    Al usar el portal de Azure, al crear un evento en directo también crea un activo. 
      
    >[AZURE.NOTE]Asegúrese de tener al menos una unidad reservada en el extremo streaming desde la que desee para transmitir contenido de transmisión.
1. Inicie el programa de eventos cuando esté listo para iniciar la transmisión y archivado.
2. Si lo desea, puede señalar el codificador directo para iniciar un anuncio. El anuncio se inserta en la secuencia de salida.
1. Detener el programa de evento siempre que desee detener la transmisión y archivado el evento.
1. Eliminar el programa de evento (y, opcionalmente, eliminar el activo).     

>[AZURE.IMPORTANT] Revise la [transmisión directo con codificadores local que crean secuencias de velocidad múltiple](media-services-live-streaming-with-onprem-encoders.md) para obtener información sobre los conceptos y consideraciones relacionadas con la transmisión directo con codificadores local y canales paso a través.

##<a name="to-view-notifications-and-errors"></a>Ver las notificaciones y errores

Si desea ver las notificaciones y errores producidos por el portal de Azure, haga clic en el icono de notificación.

![Notificaciones](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##<a name="configure-streaming-endpoints"></a>Configurar extremos streaming 

Media Services proporciona embalaje dinámico, que le permite ofrecer su MP4s velocidad de entre varios de los siguientes formatos de streaming: guión MPEG, HLS, Smooth Streaming o HDS, sin tener que volver a empaquetar en estos formatos de transmisión. Con embalaje dinámico solo debe almacenar y de pago para los archivos en formato de almacenamiento único y Media Services crea y sirve la respuesta adecuada en función de las solicitudes de un cliente.

Para aprovechar las ventajas de embalaje dinámico, debe obtener al menos una unidad de transmisión del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido.  

Para crear y cambiar el número de unidades reservadas de transmisión, haga lo siguiente:

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
1. En la ventana **configuración** , haga clic en **los extremos de transmisión por secuencias**. 

2. Haga clic en el valor predeterminado transmisión extremo. 

    Aparecerá el cuadro de diálogo **Detalles de extremo de transmisión predeterminado** .

3. Para especificar el número de unidades streaming, deslice el control deslizante de **unidades de flujo de datos** .

    ![Unidades de transmisión](./media/media-services-portal-passthrough-get-started/media-services-streaming-units.png)

4. Haga clic en el botón **Guardar** para guardar los cambios.

    >[AZURE.NOTE]La asignación de las nuevas unidades puede tardar hasta 20 minutos en completarse.
    
##<a name="create-and-start-pass-through-channels-and-events"></a>Crear e iniciar eventos y canales paso a través

Un canal está asociado con los eventos o programas que le permiten controlar la publicación y el almacenamiento de segmentos en una secuencia en directo. Canales administración eventos. 
    
Puede especificar el número de horas que desea conservar el contenido registrado para el programa al establecer la longitud de la **Ventana Archivar** . Este valor se puede establecer desde un mínimo de 5 minutos hasta un máximo de 25 horas. Longitud de la ventana de archivar también determina que la cantidad máxima de clientes de tiempo puede buscar en el tiempo desde la posición actual de live. Pueden ejecutar eventos durante el período de tiempo especificado, pero continuamente se descarta el contenido que se encuentre detrás de la longitud de la ventana. El valor de esta propiedad también determina cuánto tiempo pueden aumentar los manifiestos de cliente.

Cada evento está asociado con un activo. Para publicar el evento, debe crear un localizador de petición para el activo asociado. Tener este localizador permite generar una URL streaming que puede proporcionar a sus clientes.

Un canal es compatible con hasta tres eventos ejecutan de forma simultánea, por lo que puede crear varios archivos de la misma secuencia entrante. Esto le permite publicar y archivar distintas partes de un evento según sea necesario. Por ejemplo, necesidades de su empresa es archivar 6 horas de un programa, pero difundir solo últimos 10 minutos. Para ello, debe crear dos programas al mismo tiempo. Un programa se establece archivar 6 horas del evento, pero no se ha publicado el programa. El otro programa está establecido en archivar para 10 minutos y este programa se publica.

No debería volver a usar los eventos existentes directo. En su lugar, crear e iniciar un nuevo evento para cada evento.

Iniciar el evento cuando esté listo para iniciar la transmisión y archivado. Detener el programa siempre que desee detener la transmisión y archivado el evento. 

Para eliminar el contenido archivado, detener y eliminar el evento y, a continuación, eliminar el activo asociado. No se puede eliminar un activo si se utiliza un evento; primero debe eliminarse el evento. 

Después de detener y eliminar el evento, los usuarios podrán transmitir el contenido archivado como un vídeo a petición, para siempre y cuando no elimine el activo.

Si desea conservar el contenido archivado, pero no lo tiene disponible para la transmisión, elimine el localizador streaming.

###<a name="to-use-the-portal-to-create-a-channel"></a>Usar el portal para crear un canal 

Esta sección muestra cómo usar la opción **Crear rápido** para crear un canal de paso a través.

Para obtener más detalles acerca de los canales paso a través, vea [Live transmisión con codificadores local que crean secuencias de velocidad múltiple](media-services-live-streaming-with-onprem-encoders.md).

1. En el [portal de Azure](https://portal.azure.com/), seleccione su cuenta de Azure Media Services.
2. En la ventana **configuración** , haga clic en **Live transmisión**. 

    ![Introducción](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    Aparecerá el cuadro de diálogo **transmisión directo** .

3. Haga clic en **Crear rápido** para crear un canal de paso a través con la RTMP recopilar protocolo.

    Aparecerá el cuadro de diálogo **Crear nuevo canal** .
4. Escriba un nombre para el nuevo canal y haga clic en **crear**. 

    Esto crea un canal de paso a través con la RTMP recopilar protocolo.

##<a name="create-events"></a>Crear eventos

1. Seleccione un canal al que desea agregar un evento.
2. Pulse el botón de **Live evento** .

![Evento](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)


##<a name="get-ingest-urls"></a>Obtener recopilar direcciones URL

Una vez creado el canal, puede obtener recopilar direcciones URL que se proporciona al codificador directo. El codificador utiliza estas direcciones URL para una secuencia en directo de entrada.

![Creado](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##<a name="watch-the-event"></a>Ver el evento

Para ver el evento, haga clic en **Ver** en el portal de Azure o copie la dirección URL streaming y usar el Reproductor de su elección. 
 
![Creado](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Evento directo obtener convierten automáticamente al contenido de a petición cuando se detiene.

##<a name="clean-up"></a>Limpiar

Para obtener más detalles acerca de los canales paso a través, vea [Live transmisión con codificadores local que crean secuencias de velocidad múltiple](media-services-live-streaming-with-onprem-encoders.md).

- Un canal puede detenerla solo cuando se han detenido todos los eventos o programas en el canal.  Una vez que se detiene el canal, no incurre los cargos. Cuando necesite iniciar de nuevo, tendrá la misma recopile la dirección URL para que no tendrá que volver a configurar su codificador.
- Se pueden eliminar un canal solo cuando se han eliminado todos los eventos en directo en el canal.

##<a name="view-archived-content"></a>Ver contenido archivado

Después de detener y eliminar el evento, los usuarios podrán transmitir el contenido archivado como un vídeo a petición, para siempre y cuando no elimine el activo. No se puede eliminar un activo si se utiliza un evento; primero debe eliminarse el evento. 

Para administrar los activos, seleccione **configuración** y haga clic en **activos**.

![Activos](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##<a name="next-step"></a>Siguiente paso

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
