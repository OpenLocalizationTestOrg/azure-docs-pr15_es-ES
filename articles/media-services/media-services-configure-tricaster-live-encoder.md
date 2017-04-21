<properties 
    pageTitle="Configurar el codificador NewTek TriCaster para enviar un flujo en vivo velocidad única | Microsoft Azure" 
    description="Este tema muestra cómo configurar el codificador live Tricaster para enviar una secuencia de velocidad solo a los canales de AMS que están habilitados para la codificación en directo." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="juliako;cenkd;anilmur"/>

#<a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Utilice el codificador de NewTek TriCaster para enviar un flujo en vivo velocidad única

> [AZURE.SELECTOR]
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Elementos directo](media-services-configure-elemental-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Este tema muestra cómo configurar el codificador directo [NewTek TriCaster](http://newtek.com/products/tricaster-40.html) para enviar una secuencia de velocidad solo a los canales de AMS que están habilitados para la codificación en directo. Para obtener más información, vea [trabajar con canales que están habilitados para realizar codificación en directo con Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial muestra cómo administrar Azure Media Services (AMS) con la herramienta de Azure Media Services Explorer (AMSE). Esta herramienta sólo se ejecuta en equipos con Windows. Si se encuentra en Mac o Linux, utilice el portal de Azure para crear [canales](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) y los [programas](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

>[AZURE.NOTE]Cuando se utiliza Tricaster para el envío de una fuente de canales de AMS que están habilitados para la codificación en directo de contribución, puede haber problemas de audio y vídeo en el evento directo si utiliza determinadas características de Tricaster como rápida cortando entre fuentes o cambiar de pizarras. El equipo de AMS está trabajando sobre cómo solucionar estos problemas, hasta ese momento, no es recomendable usar estas características.


##<a name="prerequisites"></a>Requisitos previos

- [Crear una cuenta de Azure Media Services](media-services-portal-create-account.md)
- Asegúrese de que hay un extremo de transmisión con al menos una unidad streaming asignada. Para obtener más información, vea [Administrar transmisión extremos en una cuenta de servicios de medios](media-services-portal-manage-streaming-endpoints.md)
- Instalar la última versión de la herramienta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Inicie la herramienta y conectarse a su cuenta de AMS.

##<a name="tips"></a>Sugerencias

- Siempre que sea posible, use una conexión a internet cableado.
- Es una buena regla general al determinar los requisitos de ancho de banda doble la transmisión velocidades de bits bajas. Aunque no es un requisito obligatorio, ayudará a mitigar el impacto de congestión de la red.
- Cuando con software basado codificadores, cierre todos los programas innecesarios.

## <a name="create-a-channel"></a>Crear un canal

1.  En la herramienta AMSE, vaya a la pestaña de **Live** y haga clic con el botón secundario en el área de canal. Seleccione **Crear canal...** en el menú.

![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Especifique un nombre de canal, el campo Descripción es opcional. En configuración de canal, seleccione **estándar** para la opción de codificación en directo con el protocolo de entrada establecida en **RTMP**. Puede dejar todas las demás opciones como está.


Asegúrese de **iniciar el nuevo canal** está seleccionada.

3. Haga clic en **Crear canal**.
![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

>[AZURE.NOTE] El canal puede tardar hasta 20 minutos para empezar.


Mientras se inicia el canal puede [Configurar el codificador](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

>[AZURE.IMPORTANT] Tenga en cuenta que facturación se inicia tan pronto como canal entra en un estado listo. Para obtener más información, vea [Estados del canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_tricaster_rtmp></a>Configurar el codificador NewTek TriCaster

En este tutorial se usa la siguiente configuración de salida. El resto de esta sección describe los pasos de configuración con más detalle. 

**Vídeo**:
 
- Códec: h.264. 
- Perfil: Alta (nivel 4.0) 
- Velocidad: 5000 kbps 
- Fotograma clave: 2 segundos (60) 
- Velocidad de imagen: 30
 
**Audio**:

- Códec: AAC (LC) 
- Velocidad: 192 kbps 
- Tasa de ejemplo: 44.1kHz.


###<a name="configuration-steps"></a>Pasos de configuración

1. Crear un nuevo proyecto de **NewTek TriCaster** según se utiliza el origen de entrada de vídeo. 
2. Una vez dentro de ese proyecto, ve el botón de **secuencia** y haga clic en el icono de engranaje junto a él para acceder al menú de configuración de la secuencia.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Una vez que ha abierto el menú, haga clic en **nuevo** en el encabezado de la conexión. Cuando se le solicite para el tipo de conexión, seleccione **Adobe Flash**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)

4. Haga clic en **Aceptar**.

5. Ahora se puede importar un perfil FMLE haciendo clic en la flecha desplegable en **Transmisión perfil** y navegar a **Examinar**.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)

6. Vaya a la ubicación donde guardó el perfil FMLE configurado.
7. Selecciónelo y haga clic en **Aceptar**.

    Una vez que se carga el perfil, continúe con el paso siguiente.

6. Obtener la URL de entrada del canal para poder asignar al Tricaster **RTMP extremo**.
    
    Vaya a la herramienta AMSE y comprobar el estado de finalización de canal. Una vez que se cambió el estado de **Inicio** **en ejecución**, puede obtener la URL de entrada.
      
    Cuando se ejecuta el canal, haga clic con el botón secundario del mouse en el nombre del canal, desplácese a mantener el mouse sobre la **Dirección URL de la entrada de copiar al Portapapeles** y, a continuación, seleccione **URL de entrada principal**.  
    
    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)

7. Pegue esta información en el campo **ubicación** en **Flash Server** dentro del proyecto Tricaster. También asignar un nombre de la secuencia en el campo **Identificador de la secuencia** . 

    Si la información de secuencia se agregó al perfil FMLE, también se pueden importar a esta sección haciendo clic en **Opciones de importación**, navegar hasta el perfil FMLE guardado y haga clic en **Aceptar**. Deben rellenar los campos correspondientes del servidor Flash con la información de FMLE.

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)

9. Cuando haya terminado, haga clic en **Aceptar** en la parte inferior de la pantalla. Cuando están listos entradas de audio y vídeo en el Tricaster, empezar a transmitir a AMS haciendo clic en el botón de la **secuencia** .

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

>[AZURE.IMPORTANT]Antes de hacer clic en **secuencia**, **debe** asegurarse de que el canal está listo. 
>Además, asegúrese de no dejar el canal en un estado preparado sin una entrada contribución fuente durante más de 15 minutos de >. 

##<a name="test-playback"></a>Reproducción de prueba
  
1. Vaya a la herramienta AMSE y haga clic con el botón secundario del mouse en el canal para comprobar. En el menú, mantenga el mouse sobre **la vista previa de la reproducción** y seleccione **con Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Si la secuencia aparece en el Reproductor, el codificador se ha configurado correctamente para conectarse a AMS. 

Si recibe un error, necesitará restablecer el canal y ajusta la configuración del codificador. Vea el tema [solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones.  

##<a name="create-a-program"></a>Crear un programa

1. Una vez que se confirma la reproducción de canal, cree un programa. En la ficha **Live** en la herramienta AMSE, haga clic con el botón secundario en el área de programa y seleccione **Crear nuevo programa**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)

2. Nombre del programa y, si es necesario, ajustar la **Duración de la ventana de archivo** (cuyo valor predeterminado es 4 horas). También puede especificar una ubicación de almacenamiento o dejar el valor predeterminado.  
3. Active la casilla **iniciar el programa ahora** .
4. Haga clic en **Crear programa**.  
  
    Nota: Creación de programa tiene menos tiempo que la creación de canal.    
 
5. Una vez que se está ejecutando el programa, confirme la reproducción derecha haciendo clic en el programa y navegar por **los programas** de la reproducción y seleccionando **con el Reproductor multimedia de Azure**.  
6. Una vez confirmado, haga clic derecho en el programa de nuevo y seleccione **copiar la dirección URL de salida al Portapapeles** (o recuperar esta información de la opción de **configuración y la información del programa** en el menú). 

La secuencia está preparada para ser incrustados en un reproductor o distribuido a la audiencia para la visualización directo.  


## <a name="troubleshooting"></a>Solución de problemas

Vea el tema [solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones. 


##<a name="next-step"></a>Siguiente paso

Revise las rutas de aprendizaje de Media Services.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
