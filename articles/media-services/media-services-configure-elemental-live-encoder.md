<properties 
    pageTitle="Configurar el codificador Live elemento para enviar un flujo en vivo velocidad única | Microsoft Azure" 
    description="Este tema muestra cómo configurar el codificador Live elemento para enviar una secuencia de velocidad solo a los canales de AMS que están habilitados para la codificación en directo." 
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
    ms.author="cenkdin;anilmur;juliako"/>

#<a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Utilice el codificador Live elemento para enviar un flujo en vivo velocidad única

> [AZURE.SELECTOR]
- [Elementos directo](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Este tema muestra cómo configurar el codificador [Live elemento](http://www.elementaltechnologies.com/products/elemental-live) para enviar una secuencia de velocidad solo a los canales de AMS que están habilitados para la codificación en directo.  Para obtener más información, vea [trabajar con canales que están habilitados para realizar codificación en directo con Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial muestra cómo administrar Azure Media Services (AMS) con la herramienta de Azure Media Services Explorer (AMSE). Esta herramienta sólo se ejecuta en equipos con Windows. Si se encuentra en Mac o Linux, utilice el portal de Azure para crear [canales](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) y los [programas](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).

##<a name="prerequisites"></a>Requisitos previos

- Debe tener un conocimiento práctico de usar Live elementos de interfaz de web para crear eventos en directo.
- [Crear una cuenta de Azure Media Services](media-services-portal-create-account.md)
- Asegúrese de que hay un extremo de transmisión con al menos una unidad streaming asignada. Para obtener más información, vea [Administrar transmisión extremos en una cuenta de servicios de medios](media-services-portal-manage-streaming-endpoints.md).
- Instalar la última versión de la herramienta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Inicie la herramienta y conectarse a su cuenta de AMS.

##<a name="tips"></a>Sugerencias

- Siempre que sea posible, use una conexión a internet cableado.
- Es una buena regla general al determinar los requisitos de ancho de banda doble la transmisión velocidades de bits bajas. Aunque no es un requisito obligatorio, ayudará a mitigar el impacto de congestión de la red.
- Cuando con software basado codificadores, cierre todos los programas innecesarios.

## <a name="elemental-live-with-rtp-ingest"></a>Recopilar elemento Live con RTP

Esta sección muestra cómo configurar el codificador Live elementos que envía un flujo en vivo velocidad solo a través de RTP.  Para obtener más información, vea [secuencia MPEG-TS sobre RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Crear un canal

1.  En la herramienta AMSE, vaya a la pestaña de **Live** y haga clic con el botón secundario en el área de canal. Seleccione **Crear canal...** en el menú.

![Elemento](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Especifique un nombre de canal, el campo Descripción es opcional. En configuración de canal, seleccione **estándar** para la opción de codificación en directo con el protocolo de entrada establecida en **RTP (MPEG-TS)**. Puede dejar todas las demás opciones como está.


Asegúrese de **iniciar el nuevo canal** está seleccionada.

3. Haga clic en **Crear canal**.
![Elemento](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE] El canal puede tardar hasta 20 minutos para empezar.

Mientras se inicia el canal puede [Configurar el codificador](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

>[AZURE.IMPORTANT] Tenga en cuenta que facturación se inicia tan pronto como canal entra en un estado listo. Para obtener más información, vea [Estados del canal](media-services-manage-live-encoder-enabled-channels.md#states).

###<a id=configure_elemental_rtp></a>Configurar el codificador Live elemento 

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


####<a name="configuration-steps"></a>Pasos de configuración

1. Vaya a la interfaz de web **Live elemento** y configurar el codificador de transmisión **UDP/TS** . 

2. Una vez que se crea un nuevo evento, desplácese hacia abajo hasta los grupos de salida y agregue el grupo de resultados **UDP/TS** . 

3. Crear una nueva salida seleccionando **secuencia nueva** y haga clic en **Agregar resultado**.  
    
    ![Elemento](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
    
    >[AZURE.NOTE] Se recomienda que el evento elemento tiene el código de tiempo que se establece en "Reloj del sistema" para ayudar a que el codificador conectar de nuevo en el caso de un error de la secuencia.

4. Ahora que se ha creado el resultado, haga clic en **Agregar secuencia**. Ahora se puede configurar la configuración de salida. 
5. Desplácese hacia abajo hasta la "secuencia 1" que acaba de crear, haga clic en la pestaña de **vídeo** a la izquierda y expanda la sección de configuración **avanzada** . 

    ![Elemento](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Aunque Live elemento tiene una amplia gama de personalización disponibles, se recomienda la siguiente configuración para empezar con la transmisión a AMS. 
    
    - Resolución: 1280 x 720 
    - Velocidad: 30 
    - Tamaño de GOP: 60 cuadros 
    - Entrelazado modo: progresivo 
    - Velocidad: 5000000 bit/s (Esto se puede ajustar en función de las limitaciones de red) 
    

    ![Elemento](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. Obtener la dirección URL de entrada del canal.
    
    Vaya a la herramienta AMSE y comprobar el estado de finalización de canal. Una vez que se cambió el estado de **Inicio** **en ejecución**, puede obtener la URL de entrada.
      
    Cuando se ejecuta el canal, haga clic con el botón secundario del mouse en el nombre del canal, desplácese a mantener el mouse sobre la **Dirección URL de la entrada de copiar al Portapapeles** y, a continuación, seleccione **URL de entrada principal**.  
    
    ![Elemento](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
    
1. Pegue esta información en el campo de **Destino principal** de elementos. Todas las demás opciones pueden permanecer en el valor predeterminado.
    
    ![Elemento](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Para redundancia adicional, repita estos pasos con la dirección URL de entrada secundario mediante la creación de una pestaña independiente de "Salida" para la transmisión UDP/TS.
    
7. Haga clic en **crear** (si se ha creado un nuevo evento) o **Actualizar** (si está modificando un evento existente) y, a continuación, vaya al iniciar el codificador. 

>[AZURE.IMPORTANT]Antes de hacer clic en **Iniciar** en la interfaz de web Live elemento, **debe** asegurarse de que el canal está listo. 
>Además, asegúrese de que no dejar el canal en un estado de lista sin un evento de más de 15 minutos de >.

Después de que se ha ejecutado la secuencia durante 30 segundos, vaya a la reproducción de herramienta y la prueba AMSE.  

###<a name="test-playback"></a>Reproducción de prueba
  
1. Vaya a la herramienta AMSE y haga clic con el botón secundario del mouse en el canal para comprobar. En el menú, mantenga el mouse sobre **la vista previa de la reproducción** y seleccione **con Azure Media Player**.  

    ![Elemento](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Si la secuencia aparece en el Reproductor, el codificador se ha configurado correctamente para conectarse a AMS. 

Si recibe un error, necesitará restablecer el canal y ajusta la configuración del codificador. Vea el tema [solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones.   

###<a name="create-a-program"></a>Crear un programa

1. Una vez que se confirma la reproducción de canal, cree un programa. En la ficha **Live** en la herramienta AMSE, haga clic con el botón secundario en el área de programa y seleccione **Crear nuevo programa**.  

    ![Elemento](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

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
