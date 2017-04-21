<properties 
    pageTitle="Cómo llevar a cabo la transmisión directo con Azure Media Services para crear secuencias de velocidad múltiple con el portal de Azure | Microsoft Azure" 
    description="Este tutorial le guiará por los pasos de la creación de un canal que recibe un flujo en vivo solo velocidad y codifica en secuencia de velocidad múltiple con el portal de Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
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


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Cómo llevar a cabo la transmisión directo con Azure Media Services para crear secuencias de velocidad múltiple con el portal de Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API DE REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

Este tutorial le guiará por los pasos de la creación de un **canal** que recibe un flujo en vivo solo velocidad y codifica en secuencia de velocidad múltiple.

>[AZURE.NOTE]Para obtener más información relacionada con los canales que están habilitados para la codificación en directo, vea [Live transmisión mediante Azure Media Services para crear secuencias de velocidad múltiple](media-services-manage-live-encoder-enabled-channels.md).

##<a name="common-live-streaming-scenario"></a>Escenario de transmisión directo comunes

Los siguientes son pasos generales implicados en la creación de aplicaciones de streaming directo comunes.

>[AZURE.NOTE] Actualmente, la duración máxima recomendada de un evento en directo es 8 horas. Póngase en contacto con amslived en Microsoft.com si necesita para ejecutar un canal para largos períodos de tiempo.

1. Conectar una cámara de vídeo en un equipo. Iniciar y configurar un codificador live local que puede enviar una secuencia de velocidad solo en uno de los siguientes protocolos: RTMP, Smooth Streaming o RTP (MPEG-TS). Para obtener más información, vea [soporte técnico de Azure Media Services RTMP y codificadores Live](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    También se pudo realizar este paso después de crear el canal.

1. Crear e iniciar un canal. 

1. Recuperar el canal recopile la dirección URL. 

    La dirección URL de ingesta se usa el codificador directo para enviar la secuencia para el canal.
1. Recuperar la dirección URL de vista previa de canal. 

    Utilizar esta dirección URL para comprobar que su canal correctamente recibe la secuencia en directo.

3. Crear un evento o programa (que también se creará un activo). 
1. Publicar el evento (que se creará un localizador de petición para el activo asociado).  

    Asegúrese de tener al menos una unidad reservada en el extremo streaming desde la que desee para transmitir contenido de transmisión.
1. Iniciar el evento cuando esté listo para iniciar la transmisión y archivado.
2. Si lo desea, puede señalar el codificador directo para iniciar un anuncio. El anuncio se inserta en la secuencia de salida.
1. Detener el evento siempre que desee detener la transmisión y archivado el evento.
1. Eliminar el evento (y, opcionalmente, eliminar el activo).   

##<a name="in-this-tutorial"></a>En este tutorial

En este tutorial, se usa el portal de Azure para llevar a cabo las tareas siguientes: 

2.  Configurar extremos streaming.
3.  Crear un canal que está habilitado para realizar la codificación en directo.
1.  Obtener la dirección URL de recopilación para proporcionar para live codificador. El codificador directo usará esta dirección URL para recopilar la secuencia en el canal. .
1.  Crear un programa de evento (y un activo)
1.  Publicar el activo y obtener la transmisión de direcciones URL  
1.  Reproducir el contenido 
2.  Limpiar

##<a name="prerequisites"></a>Requisitos previos

Los siguientes son necesarios para completar el tutorial.

- Para completar este tutorial, necesita una cuenta de Azure. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- Una cuenta de Media Services. Para crear una cuenta de Media Services, vea [Crear una cuenta](media-services-portal-create-account.md).
- Una cámara Web y un codificador que puede enviar un flujo en vivo velocidad única.

##<a name="configure-streaming-endpoints"></a>Configurar extremos streaming 

Media Services proporciona embalaje dinámico que le permite ofrecer su MP4s velocidad de entre varios de los siguientes formatos de streaming: guión MPEG, HLS, Smooth Streaming o HDS, sin tener que volver a empaquetar en estos formatos de transmisión. Con embalaje dinámico solo debe almacenar y de pago para los archivos en formato de almacenamiento único y Media Services generará y servir la respuesta adecuada en función de las solicitudes de un cliente.

Para aprovechar las ventajas de embalaje dinámico, debe obtener al menos una unidad de transmisión del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido.  

Para crear y cambiar el número de unidades reservadas de transmisión, haga lo siguiente:

1. Iniciar sesión en el [portal de Azure](https://portal.azure.com/) y seleccione la cuenta de AMS.
1. En la ventana **configuración** , haga clic en **los extremos de transmisión por secuencias**. 

2. Haga clic en el valor predeterminado transmisión extremo. 

    Aparecerá el cuadro de diálogo **Detalles de extremo de transmisión predeterminado** .

3. Para especificar el número de unidades streaming, deslice el control deslizante de **unidades de flujo de datos** .

    ![Unidades de transmisión](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-streaming-units.png)

4. Haga clic en el botón **Guardar** para guardar los cambios.

    >[AZURE.NOTE]La asignación de las nuevas unidades puede tardar hasta 20 minutos en completarse.

##<a name="create-a-channel"></a>Crear un canal

1. En el [portal de Azure](https://portal.azure.com/), seleccione Media Services y, a continuación, haga clic en el nombre de su cuenta de Media Services.
2. Seleccione **Live Streaming**.
3. Seleccione **Crear personalizado**. Esta opción le permitirá crear un canal que está habilitado para la codificación en directo.

    ![Crear un canal](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
    
4. Haga clic en **configuración**.
    
    1.  Elija el tipo de canal de **Codificación en directo** . Este tipo especifica que desea crear un canal que está habilitado para la codificación en directo. Esto significa que la entrada secuencia de velocidad solo se envía al canal y codificado en una secuencia de velocidad múltiple usando la configuración de codificador directo especificado. Para obtener más información, vea [Live transmisión mediante Azure Media Services para crear secuencias de velocidad múltiple](media-services-manage-live-encoder-enabled-channels.md). Haga clic en Aceptar.
    2. Especifique el nombre del canal.
    3. Haga clic en Aceptar en la parte inferior de la pantalla.
    
5. Seleccione la pestaña **ingesta** .

    1. En esta página, puede seleccionar un protocolo de flujo. Para el tipo de canal de **Codificación en directo** , opciones de protocolo válidos son:
        
        - Un solo velocidad Fragmented MP4 (Smooth Streaming)
        - Velocidad de bits única RTMP
        - (MPEG-TS) RTP: Secuencia de transporte MPEG-2 sobre RTP.
        
        Para detallada sobre cada protocolo, consulte [Live transmisión mediante Azure Media Services para crear secuencias de velocidad múltiple](media-services-manage-live-encoder-enabled-channels.md).
    
        No puede cambiar la opción de protocolo mientras el canal o se ejecutan sus eventos o programas asociados. Si necesita protocolos diferentes, debe crear canales independientes para cada protocolo streaming.  

    2. Puede aplicar restricciones de IP de la ingesta. 
    
        Puede definir las direcciones IP que tiene permiso para recopilar un vídeo en este canal. Permite direcciones IP pueden especificarse como una dirección IP (por ejemplo, ' 10.0.0.1'), un intervalo IP con una dirección IP y la máscara de subred CIDR (por ejemplo, ' 10.0.0.1/22') o un intervalo IP con una dirección IP y una máscara de subred decimal con puntos (por ejemplo, ' 10.0.0.1(255.255.252.0)').

        Si no se especifican direcciones IP y no hay ninguna definición de la regla no se permitirá ninguna dirección IP. Para permitir que cualquier dirección IP, cree una regla y establezca 0.0.0.0/0.

6. En la ficha de **vista previa** , aplicar restricciones de IP en la vista previa.
7. En la pestaña **codificación** , especifique el valor de codificación. 

    En la actualidad, el único sistema preestablecida puede seleccionar es **Default 720p**. Para especificar un valor personalizado, abra una incidencia de soporte técnico de Microsoft. A continuación, escriba el nombre del valor preestablecido creado para usted. 

>[AZURE.NOTE] En la actualidad, el inicio de canal puede tardar hasta 30 minutos. Restablecimiento de un canal puede tardar hasta 5 minutos.

Una vez creado el canal, puede haga clic en el canal y seleccione **configuración** donde puede ver las configuraciones de canales. 

Para obtener más información, vea [Live transmisión mediante Azure Media Services para crear secuencias de velocidad múltiple](media-services-manage-live-encoder-enabled-channels.md).


##<a name="get-ingest-urls"></a>Obtener recopilar direcciones URL

Una vez creado el canal, puede obtener recopilar direcciones URL que se proporciona al codificador directo. El codificador utiliza estas direcciones URL para una secuencia en directo de entrada.

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##<a name="create-and-manage-events"></a>Crear y administrar eventos

###<a name="overview"></a>Información general

Un canal está asociado con los eventos o programas que le permiten controlar la publicación y el almacenamiento de segmentos en una secuencia en directo. Canales administración eventos o programas. La relación de canal y programa es muy similar a medios tradicionales donde un canal tiene un flujo constante de contenido y un programa se centra en algunas evento con hora en dicho canal.

Puede especificar el número de horas que desea conservar el contenido registrado para el evento estableciendo la longitud de la **Ventana Archivar** . Este valor se puede establecer desde un mínimo de 5 minutos hasta un máximo de 25 horas. Longitud de la ventana de archivar también determina que la cantidad máxima de clientes de tiempo puede buscar en el tiempo desde la posición actual de live. Pueden ejecutar eventos durante el período de tiempo especificado, pero continuamente se descarta el contenido que se encuentre detrás de la longitud de la ventana. El valor de esta propiedad también determina cuánto tiempo pueden aumentar los manifiestos de cliente.

Cada evento está asociado con un activo. Para publicar el evento debe crear un localizador de petición para el activo asociado. Tener este localizador le permitirá generar una URL streaming que puede proporcionar a sus clientes.

Un canal es compatible con hasta tres eventos ejecutan de forma simultánea, por lo que puede crear varios archivos de la misma secuencia entrante. Esto le permite publicar y archivar distintas partes de un evento según sea necesario. Por ejemplo, necesidades de su empresa es archivar 6 horas de un evento, pero difundir solo últimos 10 minutos. Para ello, debe crear dos ejecutan simultáneamente evento. Se establece un evento archivar 6 horas del evento pero no se publica el programa. El otro evento está establecido en archivar para 10 minutos y este programa se publica.

No debería volver a usar los programas existentes para eventos. En su lugar, crear e iniciar un programa nuevo para cada evento.

Iniciar un programa de evento cuando esté listo para iniciar la transmisión y archivado. Detener el evento siempre que desee detener la transmisión y archivado el evento. 

Para eliminar el contenido archivado, detener y eliminar el evento y, a continuación, eliminar el activo asociado. No se puede eliminar un activo si se utiliza el evento; primero debe eliminarse el evento. 

Después de detener y eliminar el evento, los usuarios podrán transmitir el contenido archivado como un vídeo a petición, para siempre y cuando no elimine el activo.

Si desea conservar el contenido archivado, pero no lo tiene disponible para la transmisión, elimine el localizador streaming.

###<a name="createstartstop-events"></a>Crear/inicio o parada eventos

Una vez que tenga la secuencia que fluye en el canal puede empezar el evento transmisión mediante la creación de un activo, el programa y el localizador de transmisión. Esto archivar la secuencia y ponerlo a disposición de los lectores a través del extremo de transmisión. 

Existen dos modos de iniciar el evento: 

1. Desde la página de **canal** , presione **Evento Live** para agregar un nuevo evento.

    Especificar: nombre del evento, nombre de activos, ventana Archivar y opción de cifrado.
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Si deja **publicar este evento live ahora** activada, se creará el evento la URL de publicación.
    
    Pulse **Inicio**, cuando esté listo para transmitir el evento.

    Una vez que inicie el evento, puede presionar **inspección** para comenzar la reproducción del contenido.

2. Como alternativa, puede usar un acceso directo y presione el botón **Go Live** en la página de **canal** . Esta opción creará un valor predeterminado activo, programa y el localizador de transmisión.

    El evento es el nombre **predeterminado** y la ventana de archivo se establece en 8 horas.

Puede ver el evento publicado desde la página **Live evento** . 

Si hace clic en **Desactivar Air**, detendrá todos los eventos en directo. 


##<a name="watch-the-event"></a>Ver el evento

Para ver el evento, haga clic en **Ver** en el portal de Azure o copie la dirección URL streaming y usar el Reproductor de su elección. 
 
![Creado](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Evento directo convierte automáticamente eventos a cuando detiene el contenido a petición.

##<a name="clean-up"></a>Limpiar

Si se hacen de eventos streaming y desea limpiar los recursos que se aprovisiona una versión anterior, siga el procedimiento siguiente.

- Detener inserción de la secuencia desde el codificador.
- Detener el canal. Una vez que se detiene el canal, no producirá los cargos. Cuando necesite iniciar de nuevo, tendrá la misma recopile la dirección URL para que no tendrá que volver a configurar su codificador.
- Puede dejar el extremo de transmisión, a menos que desea continuar proporcionar el archivo de su evento directo como una secuencia a petición. Si el canal está en estado de detención, no producirá los cargos.
  
##<a name="view-archived-content"></a>Ver contenido archivado

Después de detener y eliminar el evento, los usuarios podrán transmitir el contenido archivado como un vídeo a petición, para siempre y cuando no elimine el activo. No se puede eliminar un activo si se utiliza un evento; primero debe eliminarse el evento. 

Para administrar los activos, seleccione **configuración** y haga clic en **activos**.

![Activos](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

##<a name="considerations"></a>Consideraciones

- Actualmente, la duración máxima recomendada de un evento en directo es 8 horas. Póngase en contacto con amslived en Microsoft.com si necesita para ejecutar un canal para largos períodos de tiempo.
- Asegúrese de tener al menos una unidad reservada en el extremo streaming desde la que desee para transmitir contenido de transmisión.


##<a name="next-step"></a>Siguiente paso

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
