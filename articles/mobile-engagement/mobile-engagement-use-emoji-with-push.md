<properties 
    pageTitle="Utilizar iconos gestuales Emojis dentro de compromiso de móvil de Azure" 
    description="Cómo usar iconos gestuales Emojis dentro de las notificaciones de inserción"     
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="use-emoji-emoticon-within-push-notifications"></a>Usar el icono gestual Emojis dentro de las notificaciones de inserción

Puede incluir Emojis notificación de inserción de iconos gestuales en los en unos sencillos pasos: 

1. En primer lugar que debe buscar lo Emoji que desea enviar el mensaje. Asegúrese de que la Emojis selecciona admitirán el dispositivo de destino como fabricantes de dispositivos dedique un tiempo a agregar Emojis recién aprobados a las plataformas de dispositivos. 

2. En **Windows** : puede navegar a este [vínculo](http://apps.timwhitlock.info/emoji/tables/unicode) y copie el icono 'Native'.

    ![][7] 

3. En **Mac** : puede encontrar que el Emojis en la aplicación de diccionario en Edición -> Emoji y símbolos.

    ![][6] 

4. Ahora, vaya a la pestaña **alcance** en el portal de Azure Mobile compromiso. Seleccione el tipo de la notificación de inserción (anuncio, explora etcetera). En este ejemplo, elija una inserción de anuncio.

5. Especifique los distintos campos de la notificación hasta que llegue el texto de la notificación. Esto es donde desea agregar el emoticono Emoji. Puede colocar en el título, el mensaje o ambos. Debe arrastrar y colocar o copiar el Emojis que encontrará en las ubicaciones anteriores. 

    ![][1]

6. Complete el resto de los campos de la notificación y guardarlo. 

7. Al ejecutar una prueba o activar el anuncio se muestra una notificación con el icono gestual según lo especificado.   

    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

