<properties 
   pageTitle="Interfaz de usuario de Azure compromiso móvil - alcance cómo"
   description="Información general de la interfaz de usuario para Azure contratación móvil" 
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

# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Cómo empezar a usar y administrar inserta llegar a los usuarios finales

Una vez que el SDK está totalmente integrado en la aplicación, puede empezar a usar el la sección alcance de la interfaz de usuario para las notificaciones de inserción a los usuarios de la aplicación.  

## <a name="do-your-first-push-notification-campaign"></a>Realice su primera campaña de notificación de inserción
-    Confirme que el alcance de su está integrado en la aplicación con el SDK. 
-    Seleccione la aplicación
 
![First1][1]

-    Vaya a la sección "Alcance" y haga clic en "nuevo anuncio"
 
![First2][2]

-    Crear una campaña de nueva y asígnele el nombre
 
 ![First3][3]

-    Seleccione cómo se debe entregar la notificación, como de la aplicación solo
 
![First4][4]

-    Crear el mensaje que desea insertar
 
![First5][5]

-    Puede escribir un título en la notificación (opcional).
-    Escribir el contenido del mensaje de inserción.
-    Puede cargar una imagen. Tenga en cuenta que el tamaño del archivo no exceda 32.768 bytes.
-    También tiene la posibilidad de seleccionar más opciones, pero para el enfoque de este tutorial, veremos más adelante.

-    Seleccione el tipo de contenido como notificación solo
 
![First6][6]

-    Crear su campaña de inserción y aparecerá en la lista de la campaña.
 
![First7][7]

## <a name="test-your-push-notification-campaign"></a>Probar su campaña de notificación de inserción
![Prueba1][8]

-    Registrar el dispositivo.
-    Haga clic en la casilla de verificación del dispositivo que desea insertar.
-    Haga clic en el botón "Prueba" para enviar la inserción al dispositivo.
 
![Test2][9]

-    Activar la campaña
 
![Test3][10]

-    Ahora que ha creado su campaña que necesite activarlo hasta que se envían a los usuarios.
 
## <a name="send-personalized-pushes"></a>Enviar push personalizados
-    Este ejemplo crea un push donde se introduce un código de descuento personalizado en la notificación de inserción.
 
![Personalize1][11]

Personalización works reemplazando un marcador, de una etiqueta de información de la aplicación, por lo tanto, tendrá para asegurarse de que el usuario tiene definidas por el primero la información de aplicación correcta. En este ejemplo, los usuarios destinados tendrá una etiqueta de información de la aplicación denominada rebate_code definido.
A medida que ve encima del contenido de la notificación de inserción incluye $ de marcador {rebate_code} que indica que resulta ser reemplazada por el contenido real de la etiqueta de información de la aplicación.

> Advertencia: Si no ha definido la etiqueta de información de la aplicación para el usuario, el usuario no recibirá la inserción.

-    Resultado
 
![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Se puede personalizar aún más el texto de la notificación
![Personalize3][13]

-    Incluya el título de la notificación
-    Y el contenido del mensaje.
-    Elija el tipo de anuncio (vista de texto o la vista Web)
 
![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>También se puede personalizar el cuerpo de un anuncio con:
-    La URL de la acción, si desea personalizar la página de inicio
-    El título
-    Cuerpo del mensaje.
 
 
## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Diferenciar su notificación de inserción (en o fuera de la aplicación)
-    Elija el tipo de notificación se push, seleccione la aplicación, vaya a la sección "Alcance", seleccione o crear una campaña de inserción y vaya a la sección "Notificación".
 
-    Haga clic en "modo de entrega" que desee.
-    Haga clic en la casilla de verificación "Restringir actividades" cuando quiera que la notificación se produce en actividades específicas (pantallas).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Modo de entrega de "Fuera de la aplicación solo"
![Differentiate2][16]

"Fuera de la aplicación solo" modo de entrega proporciona una notificación de inserción cuando se cierra la aplicación. Esta es la notificación de inserción estándar.
Cuando se selecciona "fuera de la aplicación solo", ha proporcionado los certificados de la plataforma de la aplicación que está creando en (APN o GCM).

### <a name="see-also"></a>Vea también
-  [Servicio de notificación: certificados de inserción de Apple](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), Google Cloud mensajería: certificado] (http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>"de la aplicación solo" modo de entrega
![Differentiate3][17]

Modo de entrega de "De la aplicación solo" proporciona una notificación de inserción cuando se ejecuta la aplicación.
Para esta notificación, no debe pasar por el sistema APN y GCM.
Puede usar el sistema de entrega de la aplicación para llegar a los usuarios finales.
Totalmente, puede personalizar la notificación y decidir en el que se mostrará actividad (pantalla) la notificación.

### <a name="anytime-delivery-mode"></a>Modo de entrega "En cualquier momento"
Puede elegir un modo de entrega "En cualquier momento", asegura que para llegar a los usuarios finales si la aplicación se está ejecutando o no.
Cuando se selecciona "En cualquier momento", ha proporcionado los certificados de la plataforma de la aplicación partiendo (APN o GCM). 
 
## <a name="schedule-a-push-campaign"></a>Programar una campaña de inserción
### <a name="plan-to-start-a-campaign"></a>Va a iniciar una campaña
![Shedule1][18]

Es el 21 de marzo y tiene un anuncio para realizar y cepillada para el 22 de marzo a medianoche. No tiene que estar delante de la interfaz de impulsar una! Puede planificar con antelación el minuto exacto notificaciones se enviarán.
-    Desactive el "ninguno" casilla de verificación y seleccione una hora de inicio 
-    Elija la fecha y la hora en que desea iniciar la campaña de inserción.

### <a name="plan-to-end-a-campaign"></a>Plan para finalizar una campaña
![Shedule2][19]

Desea que su campaña de detener el 25 de marzo en 3 pm pero sabe que no estará hay que hacerlo.
¡No tiene que estar delante de la interfaz de inserción! Puede planificar con antelación el minuto exacto que se detendrá la campaña.
-    Haga clic en la "ninguno" casilla de verificación o seleccione una hora de finalización
-    Elija la fecha y la hora que desee para terminar la campaña de inserción.

### <a name="end-a-campaign-manually"></a>Finalizar una campaña de forma manual
![Shedule3][20]

De forma predeterminada, la "ninguno" casillas de verificación están seleccionadas.
Esto significa que la campaña se iniciará tan pronto como activar en la sección alcance y finalizará cuando se detiene en la sección alcance.
 
> Nota: Las campañas creadas sin una fecha de finalización almacenan la inserción localmente en el dispositivo y mostrar la próxima vez que se abre la aplicación incluso si manualmente se finaliza la campaña.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Mejorar una notificación de inserción con una vista de texto
### <a name="what-is-a-text-view"></a>¿Qué es una vista de texto?
![TextView1][21]

Una vista de texto es una ventana emergente con contenido de texto. Esta ventana emergente aparece después de que el usuario final hace clic en la notificación de inserción.
Una vista de texto le permite presentar más contenido para el usuario final. Esta es también la posibilidad de presentar una llamada a la acción como saltar a una página de la aplicación, redirigir a un almacén, abrir una página web, enviar un correo electrónico, iniciar una búsqueda localizados geo, etcetera...

### <a name="example-text-view"></a>Ejemplo: Vista de texto
-    Crear su campaña de notificación de inserción en la sección "Alcance" y asigne un nombre de la campaña
 
![TextView2][22]

-    Escribir el mensaje que desee que aparezca en la notificación.
-    Seleccione el tipo de contenido de anuncio de "texto"
 
![TextView3][23]

> Nota: al insertar una vista de texto, siempre incluye una notificación en primer lugar. 

- Definir el texto (después de seleccionar el contenido de la presentación de texto, aparecerá la sección subsitios, lo que le permite definir el texto que desee que se muestren.)
 
![TextView4][24]

-    Escriba el título que aparecerá en la parte superior del mensaje.
-    Escribir el contenido principal de la vista de texto.
-    Escriba el contenido que desee que aparezca en el botón de acción (un botón de acción permite a la aplicación realizar una acción específica como abrir una página de la aplicación, redirigir a una tienda de aplicaciones o cualquier tipo de orígenes que puede proporcionar).
-    Escribir el contenido que desee que aparezca en el botón Salir (haciendo clic en el botón Salir, la vista de texto desaparecerá.)
 
-    Crear su campaña de notificación de inserción y aparecerá en la lista de la campaña.
 
![TextView5][25]

-    Activar su campaña de notificación de inserción para enviar la vista de texto a los usuarios.
 
![TextView6][26]

-    Resultado
 
![TextView7][27]

-    El usuario recibe la notificación y haga clic en él.
-    La vista de texto aparece como una ventana emergente que permite al usuario interactuar con él.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Mejorar una notificación de inserción con una vista Web
### <a name="what-is-a-web-view"></a>¿Qué es vista Web?
![WebView1][28]

Una vista web es una ventana emergente con contenido web. Esta ventana emergente aparece cuando el usuario final hace clic en la notificación de inserción.
Una vista web le permite tener más interacción con el usuario final.
Esta es también la posibilidad de presentar una llamada a la acción, como la redirección a la tienda de aplicaciones, al abrir una página web, enviar un correo electrónico, iniciar una búsqueda localizados geo, etcetera...

### <a name="example-web-view"></a>Ejemplo: Vista Web
-    Crear su campaña de inserción en la sección "Alcance" y asigne un nombre de la campaña.
 
![WebView2][29]

-    Escribir el mensaje que desee que aparezca en la notificación.
-    Seleccione el tipo de contenido de anuncio como "web"
 
![WebView3][30]

### <a name="about-announcement-types"></a>Acerca de los tipos de anuncio:
- Sólo notificación: es una notificación estándar simple. Lo que significa que si un usuario hace clic en él, no aparecerá vista adicional, pero se producirá únicamente la acción asociada a ella.
- Anuncio de texto: es una notificación que se activa el usuario tenga un vistazo a una vista de texto.
- Anuncio de Web: es una notificación que se activa el usuario tenga un vistazo a una vista de web.
Seleccione el contenido de la "Presentación Web".

> Nota: Al insertar una vista web, siempre incluye una notificación en primer lugar.

- Definir el contenido web (después de seleccionar el contenido de la presentación web, aparecerá la subsección, lo que le permite definir el contenido de la vista web que desea que se muestren.)

 
![WebView4][31]

-    Escriba el título que aparecerá en la parte superior del mensaje (opcional).
-    Escriba el código HTML aquí.
-    Haga clic en el botón de modo para cambiar la edición y ver cómo la apariencia de edición de origen.
-    Escriba el contenido que desee que aparezca en el botón de acción (un botón de acción permite a la aplicación realizar una acción específica como abrir una página de la aplicación, redirigir a una tienda o cualquier tipo de orígenes que puede proporcionar).
-    Escribir el contenido que desee que aparezca en el botón Salir (haciendo clic en el botón Salir, la vista web desaparecerá).
 
-    Resultado
 
![WebView5][32]

-    El usuario recibe la notificación y haga clic en ella.
-    La vista de texto aparece como una ventana emergente que permite al usuario interactuar con él.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 
