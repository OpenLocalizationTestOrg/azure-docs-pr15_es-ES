<properties 
    pageTitle="Configurar el codificador Telestream Wirecast para enviar un flujo en vivo velocidad única | Microsoft Azure" 
    description="Este tema muestra cómo configurar el codificador live Wirecast para enviar una secuencia de velocidad solo a los canales de AMS que están habilitados para la codificación en directo. " 
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
    ms.date="10/12/2016"
    ms.author="juliako;cenkdin;anilmur"/>

#<a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Utilice el codificador de Wirecast para enviar un flujo en vivo velocidad única

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Elementos directo](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Este tema muestra cómo configurar el codificador directo [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) para enviar una secuencia de velocidad solo a los canales de AMS que están habilitados para la codificación en directo.  Para obtener más información, vea [trabajar con canales que están habilitados para realizar codificación en directo con Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial muestra cómo administrar Azure Media Services (AMS) con la herramienta de Azure Media Services Explorer (AMSE). Esta herramienta sólo se ejecuta en equipos con Windows. Si se encuentra en Mac o Linux, utilice el portal de Azure para crear [canales](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) y los [programas](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).


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

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Especifique un nombre de canal, el campo Descripción es opcional. En configuración de canal, seleccione **estándar** para la opción de codificación en directo con el protocolo de entrada establecida en **RTMP**. Puede dejar todas las demás opciones como está.


Asegúrese de **iniciar el nuevo canal** está seleccionada.

3. Haga clic en **Crear canal**.
![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] El canal puede tardar hasta 20 minutos para empezar.

Mientras se inicia el canal puede [Configurar el codificador](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

>[AZURE.IMPORTANT] Tenga en cuenta que facturación se inicia tan pronto como canal entra en un estado listo. Para obtener más información, vea [Estados del canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_wirecast_rtmp></a>Configurar el codificador Telestream Wirecast

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

1. Abra la aplicación de Telestream Wirecast en el equipo que utiliza y configurar para la transmisión de RTMP.
2. Configurar la salida navegar a la pestaña de **salida** y seleccionando la **Configuración de salida**.
    
    Asegúrese de que el **Destino de salida** se establece en **Servidor RTMP**.
3. Haga clic en **Aceptar**.
4. En la página Configuración, establezca el campo de **destino** que **Azure Media Services**.
 
    El perfil de codificación está seleccionado previamente a **Azure H.264 720 p 16:9 (1280 x 720)**. Para personalizar esta configuración, seleccionar el icono de engranaje a la derecha de la lista desplegable y, a continuación, elija **Nueva preestablecida**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. Configurar valores predeterminados de codificador.

    Asigne un nombre a la programación y busque la siguiente configuración recomendada:

    **Vídeo**
    
    - Codificador: MainConcept h.264.
    - Cuadros por segundo: 30
    - Velocidad de bits promedio: 5000 kbits/seg (se puede ajustar en función de las limitaciones de red)
    - Perfil: principal
    - Fotograma clave cada: 60 cuadros

    **Audio**

    - Tasa de bits de destino: 192 kbits/seg.
    - Tasa de ejemplo: 44,100 kHz
     
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. Pulse **Guardar**.

    El campo de codificación tiene ahora el perfil recién creado disponible para su selección. 

    Asegúrese de que está seleccionado el nuevo perfil.

7. Obtener la URL de entrada del canal para poder asignar al Wirecast **RTMP extremo**.
    
    Vaya a la herramienta AMSE y comprobar el estado de finalización de canal. Una vez que se cambió el estado de **Inicio** **en ejecución**, puede obtener la URL de entrada.
      
    Cuando se ejecuta el canal, haga clic con el botón secundario del mouse en el nombre del canal, desplácese a mantener el mouse sobre la **Dirección URL de la entrada de copiar al Portapapeles** y, a continuación, seleccione **URL de entrada principal**.  
    
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. En la ventana de Wirecast **Configuración de salida** , pegue esta información en el campo **dirección** de la sección de salida y asignar un nombre de la secuencia. 


    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. Seleccione **Aceptar**.

10. En la pantalla principal de **Wirecast** , confirme orígenes de entrada de audio y vídeo están listos y, a continuación, haga clic en **secuencia** en la esquina superior izquierda.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT]Antes de hacer clic en **secuencia**, **debe** asegurarse de que el canal está listo. 
>Además, asegúrese de no dejar el canal en un estado preparado sin una entrada contribución fuente durante más de 15 minutos de >.

##<a name="test-playback"></a>Reproducción de prueba
  
1. Vaya a la herramienta AMSE y haga clic con el botón secundario del mouse en el canal para comprobar. En el menú, mantenga el mouse sobre **la vista previa de la reproducción** y seleccione **con Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Si la secuencia aparece en el Reproductor, el codificador se ha configurado correctamente para conectarse a AMS. 

Si recibe un error, necesitará restablecer el canal y ajusta la configuración del codificador. Vea el tema [solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones.  

##<a name="create-a-program"></a>Crear un programa

1. Una vez que se confirma la reproducción de canal, cree un programa. En la ficha **Live** en la herramienta AMSE, haga clic con el botón secundario en el área de programa y seleccione **Crear nuevo programa**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

2. Nombre del programa y, si es necesario, ajustar la **Duración de la ventana de archivo** (cuyo valor predeterminado es 4 horas). También puede especificar una ubicación de almacenamiento o dejar el valor predeterminado.  
3. Active la casilla **iniciar el programa ahora** .
4. Haga clic en **Crear programa**.  
  
    Nota: Creación de programa tiene menos tiempo que la creación de canal.    
 
5. Una vez que se está ejecutando el programa, confirme la reproducción derecha haciendo clic en el programa y navegar por **los programas** de la reproducción y seleccionando **con el Reproductor multimedia de Azure**.  
6. Una vez confirmado, haga clic derecho en el programa de nuevo y seleccione **copiar la dirección URL de salida al Portapapeles** (o recuperar esta información de la opción de **configuración y la información del programa** en el menú). 

La secuencia está preparada para ser incrustados en un reproductor o distribuido a la audiencia para la visualización directo.  


## <a name="troubleshooting"></a>Solución de problemas
 
Vea el tema [solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones. 

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
