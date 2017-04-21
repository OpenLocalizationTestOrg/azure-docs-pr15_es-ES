<properties 
    pageTitle="Guía de solución de problemas para la transmisión directo | Microsoft Azure" 
    description="Este tema proporciona sugerencias sobre cómo solucionar problemas de transmisiones de live." 
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
    ms.topic="article" 
    ms.date="10/12/2016"  
    ms.author="juliako"/>

#<a name="troubleshooting-guide-for-live-streaming"></a>Guía de solución de problemas para la transmisión directo

Este tema ofrece sugerencias sobre cómo solucionar problemas de transmisiones directo.

## <a name="issues-related-to-on-premises-encoders"></a>Problemas relacionados con codificadores local 

Esta sección proporciona sugerencias sobre cómo solucionar problemas relacionados con codificadores local que se haya configurado para enviar una secuencia de velocidad solo a los canales de AMS que están habilitados para la codificación en directo.

###<a name="problem-would-like-to-see-logs"></a>Problema: Le gustaría ver registros 

- **Problema**: no se puede buscar los registros de codificador pueden ayudar a depurar problemas.
    
    - **Telestream Wirecast**: normalmente se pueden encontrar registros en C:\Users\{username} \AppData\Roaming\Wirecast\ 
    - **Elemento Live**: puede encontrar tiene vínculos a los registros en el portal de administración. Haga clic en **estadísticas**, a continuación, en **los registros**. En la página de **Archivos de registro** , verá una lista de registros de todos los elementos de AcontecimientoEnDirecto; Seleccione la coincidencia de la sesión actual. 
    - **Flash Media Encoder Live**: puede encontrar el **Directorio de registro...** , vaya a la pestaña **Codificación de registro** .
    
###<a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: No hay ninguna opción para generar una secuencia progresiva

- **Problema**: el codificador que se usa no Desentrelazar automáticamente. 

    **Pasos de solución de problemas**: busque una opción entrelazada dentro de la interfaz de codificador. Una vez habilitado la entrelazado, vuelva a comprobar la configuración de salida progresivo. 
 
###<a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: Intentado varias opciones de salida de codificador y sigue sin poder conectarse. 

- **Problema**: canal de codificación Azure no se ha restablecido correctamente. 

    **Pasos de solución de problemas**: asegúrese de que el codificador ya no está presión a AMS, detenga y restablecer el canal. Una vez que se ejecuta de nuevo, intente conectar el codificador con la nueva configuración. Si aún no se soluciona el problema, intente crear un nuevo canal por completo, a veces pueden dañado canales después de varios intentos.  

- **Problema**: GOP la configuración de tamaño o fotograma clave no es óptimas. 

    **Pasos de solución de problemas**: intervalo de tamaño o fotograma clave GOP recomendado es de 2 segundos. Algunos codificadores calculan esta configuración en el número de marcos, mientras que otros utilizan a segundos. Por ejemplo: cuando se muestren 30fps, el tamaño de GOP sería 60 cuadros, que es equivalente a 2 segundos.  
     
- **Problema**: puertos cerrados impiden la secuencia. 

    **Pasos de solución de problemas**: cuando transmisión mediante RTMP, compruebe la configuración de proxy o de firewall para confirmar que los puertos de salida 1935 y 1936 están abiertos. Cuando se utiliza la transmisión por secuencias RTP, confirme que el puerto saliente 2010 está abierto. 


###<a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problema: Al configurar el codificador en secuencia con el protocolo RTP, hay ningún lugar para escribir un nombre de host. 

- **Problema**: codificadores RTP muchos no permitir nombres de host y, a continuación, necesitará adquirir una dirección IP.  

    **Pasos de solución de problemas**: para buscar la dirección IP, abra un símbolo del sistema en cualquier equipo. Para hacer esto en Windows, abra el selector de ejecutar (Windows + R) y escriba "cmd" para abrir.  

    Una vez abierto el símbolo del sistema, escriba "Ping [nombre de Host de AMS]". 

    El nombre de host puede derivarse omitiendo el número de puerto desde la URL de recopilación de Azure, como resaltado en el ejemplo siguiente: 

    RTP://Test2-amstest009.RTP.Channel.mediaservices.Windows.NET:2010 / 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###<a name="problem-unable-to-playback-the-published-stream"></a>Problema: No se puede reproducir la secuencia publicada.
 
- **Problema**: no hay ningún extremo transmisión ejecutando o no hay ningún unidades streaming (unidades de escala) asignadas. 

    **Pasos de solución de problemas**: vaya a la ficha "Transmisión extremo" en la herramienta AMSE y confirme que hay un extremo de transmisión con una unidad streaming. 
    


>[AZURE.NOTE] Si después de seguir los pasos de solución de problemas que aún no puede correctamente transmitir, enviar una incidencia de soporte técnico con el portal de Azure.

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
