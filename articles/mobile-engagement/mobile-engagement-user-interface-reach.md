<properties 
   pageTitle="Interfaz de usuario de Azure compromiso móvil - alcance" 
   description="Obtenga información sobre cómo llegar a los usuarios de la aplicación con las notificaciones de inserción con compromiso de móvil de Azure" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>


# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Cómo llegar a los usuarios de la aplicación con las notificaciones de inserción

Este artículo describe la pestaña **alcance** del portal **Compromiso de móvil** . Usar el portal de **Mobile compromiso** para supervisar y administrar las aplicaciones móviles. Tenga en cuenta que para empezar a usar el portal primero tiene que crear una cuenta de **Azure Mobile compromiso** . Para obtener más información, vea [crear una cuenta de Azure Mobile compromiso](mobile-engagement-create.md).

La sección alcance de la interfaz de usuario es la herramienta de administración de campaña de inserción donde puede crear o editar y activar o finalizar o monitor y obtener estadísticas sobre las campañas de notificación de inserción y características que también se pueden acceder a través de la API alcanzar (y algunos elementos de la API de inserción de bajo nivel). Recuerde que si está utilizando la interfaz de usuario o de la API, necesita integrar Azure Mobile compromiso y alcance en la aplicación para cada plataforma con el SDK antes de poder usar llegar a las campañas.

>[AZURE.NOTE] Muchas secciones del portal de **Mobile compromiso** IU contienen el botón **Mostrar ayuda** . Presione este botón para obtener más información contextual sobre una sección.

## <a name="four-types-of-push-notifications"></a>Cuatro tipos de notificaciones de inserción
1.    Anuncios - permiten enviar mensajes de publicidad a los usuarios que redirección a otra ubicación dentro de la aplicación o enviarlos a una página Web o el almacén de fuera de la aplicación. 
2.    Sondeos - le permiten recopilar información de los usuarios finales indicándole preguntas.
3.    Inserta datos - le permiten enviar un archivo de datos binarios o base 64. La información contenida en Insertar datos se envía a la aplicación para modificar la experiencia actual de los usuarios en la aplicación. La aplicación necesita poder procesar los datos de inserción de datos.

## <a name="campaign-details"></a>Detalles de la campaña

Puede editar, clonar, eliminar o activar las campañas que no se han activado aún pasando por encima de sus nombres o puede hacer clic para abrirlos. Puede clonar campañas que ya se han activado pasando por encima de sus nombres o puede hacer clic para abrirlos. Sin embargo, no puede cambiar una campaña cuando se haya activado.
 
![Reach1][18]

## <a name="reach-feedback"></a>Llegar a los comentarios

Haga clic en **estadísticas** para ver los detalles de una campaña de alcance. La vista **Simple** ofrece una representación visual en forma de un gráfico de barras de la columna sobre qué ha pasado después de la activación de una campaña. La vista **avanzada** proporciona más detallada acerca de la campaña de inserción. Estos detalles no estarán disponibles si va a enviar una campaña de prueba, es decir, una inserción enviado a un dispositivo de prueba. Le mostramos cómo debe interpretar estos detalles:

1. **Pushed** - especifica el número de mensajes que se envían a los dispositivos. Este número dependerá de la audiencia de destino que especificó al crear la campaña de inserción. Si no especifica ningún audiencia, a continuación, esta inserción se enviarán a todos los dispositivos registrados. Al igual que todos los servicios de inserción, no las notificaciones de inserción directamente a los dispositivos pero insertarlos en su lugar la plataforma respectivos servicios de notificación de inserción específicos (PNS - GCM/APNS/WNS) para que pueden entregar las notificaciones a los dispositivos. 

2.  **Entregado** - especifica el número de los mensajes entregados por la PNS al dispositivo y confirmado correctamente como recibidos por Mobile compromiso SDK. 
        
    *Razones para entregado contar es menor que el recuento de insertado:*
    
    1. Si el usuario ha desinstalado la aplicación desde el dispositivo, pero no sabe la PNS sobre él en el momento de que la inserción de enviar a la PNS se eliminará el mensaje.
    2. Si el dispositivo tiene la aplicación, pero los propios dispositivos estaban desconectados durante largos períodos de tiempo, la PNS provocarán entregar el mensaje en el dispositivo. 
    3. Si recibe entregar el mensaje en el dispositivo, pero el SDK de contratación Mobile en la aplicación no reconoce el contenido del mensaje, quita dicho mensaje. Esto puede suceder si la personalización de la notificación en la aplicación genera una excepción que se pueda ponerse al día en el SDK y colocar el mensaje. Esto también puede ocurrir si la aplicación en el dispositivo está utilizando una versión del SDK de contratación Mobile que no sea capaz de entender la versión más reciente del mensaje de inserción enviado desde la plataforma, pero esto es solo cuando la aplicación se actualizó después de la notificación se haya enviado desde la plataforma de servicio. La ficha **Opciones avanzadas** le indicará cuántos mensajes se han perdido. 
    4. En dispositivos iOS, mensajes a veces se entrega si está en el dispositivo de batería baja o la aplicación consume gran cantidad de energía al procesar notificaciones remotas. Esta es una limitación de los dispositivos iOS.   

3.  **Muestra** - especifica el número de mensajes que se muestran correctamente para el usuario de la aplicación en el dispositivo en el formulario de una notificación de inserción/fuera-de-app sistema en el centro de la notificación o una notificación de la aplicación dentro de la aplicación móvil.  La ficha **Opciones avanzadas** le indicará cuántos estaban notificaciones del sistema y cuántas notificaciones de la aplicación. 
    
    *Razones para muestra contar es menor que el recuento de entregado (en espera para que se muestre)*
    
    1. Si la campaña de notificación tiene una fecha de finalización en él, es posible que se ha entregado la notificación, pero la entrada para abrir y mostrar al usuario de aplicación, ya se ha caducado para que nunca se muestre.   
    2. Si la notificación es una notificación en la aplicación de la notificación se muestra solo cuando el usuario de la aplicación abre la aplicación. En casos donde el usuario de la aplicación no abre la aplicación, el SDK informará de que la notificación se ha entregado pero aún no se muestra hasta que se abre la aplicación. 
    2. Si la notificación es una notificación de la aplicación y configurado mostrarlo en una actividad o pantalla específica, a continuación, también se mostrarán como la notificación de entrega, pero aún no se ha entregado hasta que el usuario abre la aplicación en una pantalla específica. 
    
4.  **Interacciones de usuario** - especifica el número de mensajes que el usuario de la aplicación interactúe con e incluirá los mensajes que son ejecutados o terminado. 

    - *El usuario de la aplicación puede acción una notificación en cualquiera de las siguientes maneras:*
            
        1. Si la notificación es una notificación del sistema/fuera-de-app o notificación de la aplicación enviado solo notificación, a continuación, hace clic en el usuario de la aplicación en la notificación.
        2. Si la notificación es una notificación de la aplicación con un texto o vista de web o sondeos el usuario de la aplicación hace clic en el botón de acción en la notificación.
        3. Si la notificación es una notificación de la aplicación con una vista web, a continuación, hace clic en el usuario de la aplicación en una dirección URL en la vista web [Android sólo]
    
    - *El usuario de la aplicación puede salir de una notificación en cualquiera de las siguientes maneras:*
    
        1. Haga clic en el botón Cerrar en la notificación directamente. 
        2. Deslizar ausente o eliminar la notificación. 
        3. Notificaciones de la aplicación con contenido de texto o web y sondeos normalmente se muestran para el usuario de la aplicación de un proceso de dos pasos. Verán una notificación en primer lugar y cuándo deben hacer clic en él, ver el contenido de texto/web/sondeo subsiguientes. El usuario de la aplicación puede salir de una notificación en cualquiera de estos pasos y los detalles de la vista avanzada captura esto. 

5.  **Actioned** - especifica el número de mensajes que se explícitamente ejecutados por el usuario de la aplicación. Esta es la cantidad más interesante, tal y como se indica cuántos usuarios de la aplicación se está interesados en el mensaje que sobresale en la notificación. 
 
> [AZURE.NOTE] En iOS y ventanas abrir plataformas, si el usuario tiene la aplicación y la campaña era una campaña de "En cualquier momento", a continuación, es posible que ambos fuera de la aplicación y notificaciones de la aplicación se muestran al mismo tiempo. Esto puede provocar un recuento de muestra superior a la entregado. Si el usuario interactúa o acciones de la notificación y, a continuación, incluso el recuento de las interacciones de usuario/Actioned podría ser mayor que entregado. 


![Reach2][19]

## <a name="see-also"></a>Vea también

- [Conceptos][Link 6]
- [Servicio de guía de solución de problemas][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
