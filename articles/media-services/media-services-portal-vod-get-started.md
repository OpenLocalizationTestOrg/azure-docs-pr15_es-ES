<properties
    pageTitle=" Introducción a la entrega de contenido a petición con el portal de Azure | Microsoft Azure"
    description="Este tutorial le guiará por los pasos de la implementación de un servicio de entrega de contenido de vídeo a la carta (VoD) básico con aplicaciones de Azure Media Services (AMS) con el portal de Azure."
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
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Introducción a la entrega de contenido a petición con el portal de Azure

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Este tutorial le guiará por los pasos de la implementación de un servicio de entrega de contenido de vídeo a la carta (VoD) básico con aplicaciones de Azure Media Services (AMS) con el portal de Azure.

> [AZURE.NOTE] Para completar este tutorial, necesita una cuenta de Azure. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 

Este tutorial incluye las siguientes tareas:

1.  Cree una cuenta de Azure Media Services.
2.  Configurar transmisiones de extremo.
1.  Cargar un archivo de vídeo.
1.  Codificar el archivo de origen en un conjunto de archivos de velocidad adaptación MP4.
1.  Publicar el activo y las direcciones URL de descarga de obtener transmisión y progresivo.  
1.  Reproducir el contenido.


## <a name="create-an-azure-media-services-account"></a>Crear una cuenta de Azure Media Services

Los pasos de esta sección muestran cómo crear una cuenta de AMS.

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **+ nuevo** > **Web + Mobile** > **Media Services**.

    ![Crear Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. En **Crear cuenta de servicios de medios** escriba valores requeridos.

    ![Crear Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. En **Nombre de la cuenta**, escriba el nombre de la nueva cuenta de AMS. Un nombre de cuenta de Media Services es todos los números en minúsculas o letras sin espacios y es de 3 a 24 caracteres de longitud.
    2. En la suscripción, seleccione entre las diferentes suscripciones Azure que tienen acceso a.
    
    2. En el **Grupo de recursos**, seleccione el recurso nuevo o existente.  Un grupo de recursos es una colección de recursos que comparten directivas, permisos y ciclo de vida. Más información [aquí](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. En **ubicación**, seleccione la región geográfica se usa para almacenar los registros de medios y metadatos para su cuenta de Media Services. Esta área se usa para procesar y transmita los elementos multimedia. Solo las regiones Media Services disponibles aparecen en el cuadro de lista desplegable. 
    
    3. En la **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento para proporcionar el almacenamiento de blobs del contenido multimedia desde su cuenta de Media Services. Puede seleccionar una cuenta existente de almacenamiento en la misma región geográfica como su cuenta de Media Services, o puede crear una cuenta de almacenamiento. Se crea una nueva cuenta de almacenamiento en la misma región. Las reglas de nombres de cuenta de almacenamiento son los mismos que las cuentas de Media Services.

        Obtenga más información sobre el almacenamiento [aquí](storage-introduction.md).

    4. Seleccione **Anclar al panel** para ver el progreso de la implementación de la cuenta.
    
7. Haga clic en **crear** en la parte inferior del formulario.

    Una vez que la cuenta se ha creado correctamente, el estado cambia a **está ejecutando**. 

    ![Configuración de servicios de medios](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para administrar su cuenta AMS (por ejemplo, cargar vídeos, codificar activos, supervisar el progreso de tarea) Utilice la ventana de **configuración** .

## <a name="manage-keys"></a>Administrar las claves

Necesita el nombre de cuenta y la información de clave principal acceso mediante programación a la cuenta de Media Services.

1. En el portal de Azure, seleccione su cuenta. 

    Aparece la ventana de **configuración** de la derecha. 

2. En la ventana **configuración** , seleccione **teclas**. 

    Las ventanas de **administrar claves** muestra el nombre de cuenta y se muestra la clave principal y secundaria. 
3. Presione el botón Copiar para copiar los valores.
    
    ![Servicios multimedia de teclas](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="configure-streaming-endpoints"></a>Configurar extremos streaming

Cuando se trabaja con uno de los escenarios más comunes ofrece vídeo a través de la velocidad de adaptación transmisión a los clientes de Azure Media Services. Media Services es compatible con la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe).

Media Services proporciona embalaje dinámico, que le permite ofrecer la velocidad de adaptación contenido MP4 codificado en formatos admitidos por el Media Services (MPEG guión, HLS, Smooth Streaming, HDS) just-in-time, sin tener que almacenar versiones preconfiguradas de cada uno de estos formatos de transmisión de transferencia.

Para aprovechar las ventajas de embalaje dinámico, debe hacer lo siguiente:

- Codificar el archivo mezzanine (origen) en un conjunto de archivos de velocidad adaptación MP4 (se muestran los pasos de codificación más adelante en este tutorial).  
- Crear al menos una unidad de transmisión por secuencias para la *transmisión de extremo* desde el que tiene previsto entrega el contenido. Los pasos que muestran cómo cambiar el número de unidades streaming.

Con embalaje dinámico, solo debe almacenar y de pago para los archivos en formato de almacenamiento único y Media Services crea y sirve la respuesta adecuada en función de las solicitudes de un cliente.

Para crear y cambiar el número de unidades reservadas de transmisión, haga lo siguiente:


1. En la ventana **configuración** , haga clic en **los extremos de transmisión por secuencias**. 

2. Haga clic en el valor predeterminado transmisión extremo. 

    Aparecerá el cuadro de diálogo **Detalles de extremo de transmisión predeterminado** .

3. Para especificar el número de unidades streaming, deslice el control deslizante de **unidades de flujo de datos** .

    ![Unidades de transmisión](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Haga clic en el botón **Guardar** para guardar los cambios.

    >[AZURE.NOTE]La asignación de las nuevas unidades puede tardar hasta 20 minutos en completarse.

## <a name="upload-files"></a>Cargar archivos

En secuencia vídeos con Azure Media Services, necesita cargar los vídeos de origen, codificar varias velocidades de bits bajas y publicar el resultado. El primer paso se trata en esta sección. 

1. En la ventana **configuración** , haga clic en **activos**.

    ![Cargar archivos](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Haga clic en el botón **cargar** .

    Aparece la ventana **cargar un recurso de vídeo** .

    >[AZURE.NOTE] No hay ninguna limitación de tamaño de archivo.
    
4. Busque el vídeo deseado en su equipo, selecciónelo y haga clic en Aceptar.  

    Se inicia la carga y puede ver el progreso en el nombre de archivo.  

Una vez completada la carga, consulte el nuevo activo que aparece en la ventana **activos** . 

## <a name="encode-assets"></a>Codificar activos

Cuando se trabaja con Azure Media Services adaptación velocidad transmisión uno de los escenarios más comunes ofrece a sus clientes. Media Services es compatible con la velocidad de adaptación siguiente transmisión tecnologías: transmisión Live HTTP (HLS), Smooth Streaming, MPEG guión y HDS (para las licencias de acceso o PrimeTime Adobe). Para preparar los vídeos adaptación velocidad transmisión, debe codificar el vídeo de origen en velocidad de entre varios archivos. Debe usar el codificador **Multimedia codificador estándar** para codificar los vídeos.  

Media Services también proporciona embalaje dinámico, que le permite ofrecer su MP4s velocidad de entre varios de los siguientes formatos de streaming: guión MPEG, HLS, Smooth Streaming o HDS, sin tener que volver a empaquetar en estos formatos de transmisión. Con embalaje dinámico, solo debe almacenar y de pago para los archivos en formato de almacenamiento único y Media Services crea y sirve la respuesta adecuada en función de las solicitudes de un cliente.

Para aprovechar las ventajas de embalaje dinámico, debe hacer lo siguiente:

- Codificar el archivo de origen en un conjunto de archivos de velocidad múltiple MP4 (los pasos de codificación se muestran más adelante en esta sección).
- Obtener al menos una unidad de transmisión del extremo de transmisión por secuencias desde el que tiene previsto entrega el contenido. Para obtener más información, vea [configurar transmisiones de los extremos](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### <a name="to-use-the-portal-to-encode"></a>Usar el portal para codificar

Esta sección describe los pasos que puede realizar para codificar el contenido con Media Encoder estándar.

1.  En la ventana **configuración** , seleccione **activos**.  
2.  En la ventana de **activos** , seleccione el recurso que desea codificar.
3.  Presione el botón **codificar** .
4.  En la ventana de **codificar un activo** , seleccione el procesador "Media Encoder estándar" y una programación. Por ejemplo, si conoce el vídeo de entrada con una resolución de 1920 x 1080 píxeles, a continuación, podría usar el "H264 velocidad varias 1080p" preestablecido. Para obtener más información sobre los valores preestablecidos, consulte [este](https://msdn.microsoft.com/library/azure/mt269960.aspx) artículo: es importante seleccionar el ajuste preestablecido que es más apropiado para el vídeo de entrada. Si tiene un vídeo de baja resolución (640 x 360), que no debería utilizar el valor predeterminado "H264 velocidad varias 1080p" preestablecido.
    
    Para facilitar la administración, tiene una opción de editar el nombre del activo de salida y el nombre de la tarea.
        
    ![Codificar activos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Pulse **crear**.

### <a name="monitor-encoding-job-progress"></a>Supervisar el progreso de tarea de codificación

Supervisar el progreso de la tarea de codificación, haga clic en **configuración** (en la parte superior de la página) y, a continuación, seleccione **tareas**.

![Trabajos](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publicar contenido

Para proporcionar el usuario con una dirección URL que puede utilizarse para transmitir o descargar el contenido, debe "publicación" su activo mediante la creación de un localizador. Localizadores proporcionan acceso a archivos contenidos en el activo. Media Services es compatible con dos tipos de localizadores: 

- Transmisión localizadores (OnDemandOrigin), utilizados para la transmisión adaptación (por ejemplo, a la secuencia MPEG guión, HLS o Smooth Streaming). Para crear un localizador streaming su activo debe contener un archivo .ism. 
- Localizadores progresivos de (SA), utilizados para el envío de vídeo mediante descarga progresiva.


Una dirección URL streaming tiene el siguiente formato y puede usarlo para reproducir Smooth Streaming activos.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para generar una URL de transmisión de HLS, anexar (formato = m3u8 aapl) a la dirección URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para crear un guión MPEG transmisión URL, anexar (formato = csf de tiempo de mpd) a la dirección URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Una dirección URL de SA tiene el siguiente formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

>[AZURE.NOTE] Si utiliza el portal para crear localizadores antes de marzo de 2015, se crearon localizadores con una fecha de caducidad de dos años.  

Para actualizar una fecha de vencimiento de un localizador, utilice el [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) de las API de [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Al actualizar la fecha de vencimiento de un localizador SA, cambia la dirección URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Usar el portal para publicar un activo

Para usar el portal para publicar un activo, haga lo siguiente:

1. Seleccione **configuración** > **activos**.
1. Seleccione el elemento que desea publicar.
1. Haga clic en el botón **Publicar** .
1. Seleccione el tipo de localizador.
2. Presione **Agregar**.

    ![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

La dirección URL se agrega a la lista de **Direcciones URL publicado**.

## <a name="play-content-from-the-portal"></a>Reproducir contenido desde el portal

El portal de Azure ofrece un Reproductor de contenido que puede usar para probar el vídeo.

Haga clic en el vídeo que desee y, a continuación, haga clic en el botón **Reproducir** .

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

Aplicarán algunas consideraciones:

- Asegúrese de que el vídeo se ha publicado.
- Este **Reproductor** reproduce de transmisión extremo predeterminado. Si desea reproducir desde un extremo de streaming no predeterminada, haga clic en para copiar la dirección URL y usar el Reproductor de otro. Por ejemplo, [El Reproductor de Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

##<a name="next-steps"></a>Pasos siguientes

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


