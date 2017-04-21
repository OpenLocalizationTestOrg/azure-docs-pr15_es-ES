<properties 
   pageTitle="Interfaz de usuario de Azure compromiso móvil - alcance contenido" 
   description="Obtenga información sobre cómo administrar el contenido de los diferentes tipos de campañas de notificación de inserción en Azure Mobile compromiso único" 
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

# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Cómo administrar el contenido exclusivo de los diferentes tipos de campañas de notificación de inserción
 
Puede usar la sección de contenido de una nueva campaña alcance para modificar el contenido de los anuncios, los sondeos, inserta datos y mosaicos (solo en Windows Phone). La configuración de contenido de las campañas de inserción es específica del tipo de campaña. 
 
### <a name="content-types"></a>Tipos de contenido:
- Anuncios
- Sondeos
- Inserta datos
- Mosaicos (solo en Windows Phone)
 
## <a name="content-of-announcements"></a>Contenido de anuncios
 ![Alcance Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Elija el tipo de su presentación:
-    Sólo notificación: es una notificación estándar simple. Lo que significa que si un usuario hace clic en él, no aparecerá vista adicional, pero se producirá únicamente la acción asociada a ella.
-    Anuncio de texto: es una notificación que se activa el usuario tenga un vistazo a una vista de texto.
-    Anuncio de Web: es una notificación que se activa el usuario tenga un vistazo a una vista de web.

### <a name="see-also"></a>Vea también
- [Alcance - cómo visuales - anuncios][Link 3] 

### <a name="about-web-view-announcements"></a>Acerca de Web anuncios de vista:
Repeticiones del patrón "{deviceid}" en el código HTML o código de JavaScript que proporciona aquí se reemplazará automáticamente por el identificador del dispositivo que muestra el anuncio. Esta es una forma sencilla para recuperar los identificadores de dispositivos de compromiso de Azure Mobile en un servicio web externo hospedado en su oficina.
Si desea crear una vista de pantalla completa web (sin los botones predeterminados de acción y salida proporcionamos) puede usar las siguientes funciones de código de JavaScript su web vista presentación: 

-    realizar la acción de anuncio: ReachContent.actionContent()
-    salir de la presentación: ReachContent.exitContent()
 
### <a name="choose-your-action"></a>Elija la acción:

### <a name="about-action-urls"></a>Acerca de las direcciones URL de acción:
Cualquier dirección URL que se pueden interpretar el sistema operativo del dispositivo destino se puede utilizar como una dirección URL de la acción.
Cualquiera dedicado URL compatibles con la aplicación (por ejemplo, para hacer que los usuarios saltar a una pantalla en particular) puede utilizarse también como una dirección URL de la acción.
Cada aparición del patrón {deviceid} se reemplaza automáticamente por el identificador del dispositivo realizar la acción. Esto puede usarse para recuperar fácilmente los identificadores de dispositivos de Azure Mobile compromiso a través de un servicio web externo hospedado en su oficina.

- **Android + iOS acciones**
    - Abrir una página web
    - http://\[dominio del sitio web\] 
    - Ejemplo: http://www.azure.com
    - Enviar un correo electrónico
    - mailto:\[destinatario de correo electrónico\]?subject =\[asunto\]& cuerpo =\[mensaje\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Enviar un SMS
    - SMS:\[número de teléfono\] 
    - Ejemplo: sms:2125551212
    - Marcar un número de teléfono
    - Tel:\[número de teléfono\] 
    - Ejemplo: tel:2125551212
- **Solo acciones de Android**
    - Descargar una aplicación en el repositorio de reproducción
    - Market://details?id=\[paquete de aplicación\] 
    - Ejemplo: market://details?id=com.microsoft.office.word
    - Iniciar una búsqueda localizados geo
    - geo:0, 0?q =\[consulta de búsqueda\] 
    - Ejemplo: geo:0, 0? q = starbucks, París
- **solo acciones de iOS**
    - Descargar una aplicación en la tienda de aplicaciones
    - http://iTunes.Apple.com/ [País] /app/ [nombre de aplicación] /id [id de aplicación]? mt = 8 
    - Ejemplo: http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Acciones de Windows
    - Abrir una página web
    - http://\[dominio del sitio web\] 
    - Ejemplo: http://www.azure.com
    - Enviar un correo electrónico
    - mailto:\[destinatario de correo electrónico\]?subject =\[asunto\]& cuerpo =\[mensaje\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Enviar un SMS (aplicación de la tienda de Skype requerido)
    - SMS:\[número de teléfono\] 
    - Ejemplo: sms:2125551212
    - Marcar un número de teléfono (aplicación de la tienda de Skype requerido)
    - Tel:\[número de teléfono\] 
    - Ejemplo: tel:2125551212
    - Descargar una aplicación en el repositorio de reproducción
    - MS-windows-almacén: PDP?PFN =\[Id. de paquete de aplicación\] 
    - ¿Ejemplo: ms-windows-almacén: PDP? PFN = 4d91298a 07cb 40fb aecc 4cb5615d53c1
    - Iniciar una búsqueda bingmaps
    - bingmaps:?q =\[consulta de búsqueda\] 
    - Ejemplo: bingmaps:? q = starbucks, París
    - Usar una combinación personalizada
    - \[combinación personalizada\]://\[parámetros de combinación personalizada\] 
    - Ejemplo: myCustomProtocol://myCustomParams
    - Utilizar datos del paquete (aplicación de la tienda de extensión leer requerido)
    - \[carpeta\]\[datos\]. \[extensión\] 
    - Example:myfolderdata.txt
 
### <a name="build-a-tracking-url"></a>Generar una URL de seguimiento:
-    Consulte la sección "Configuración" de la <UI Documentation> para obtener instrucciones sobre la creación de una dirección URL de seguimiento que permite a los usuarios descargar una de las otras aplicaciones.
 
### <a name="define-the-texts-of-your-announcement"></a>Definir el texto de la presentación
Escriba el título, el contenido y el texto de botón de la presentación. Se pueden destinar una audiencia de la campaña futura basándose en las respuestas de alcance de la forma en que los usuarios han respondido a esta campaña. Identificación de audiencia se pueden basar los comentarios de si solo se ha insertado esta campaña, respondidos, ejecutados o terminado.

### <a name="see-also"></a>Vea también
- [Documentación - alcance - de interfaz de usuario nuevo criterio de inserción][Link 28]

## <a name="content-of-polls"></a>Contenido de sondeos
![Alcance Content2][31] rellene el título, la descripción y el texto de botón de la presentación. A continuación, agregue preguntas y opciones para las respuestas a las preguntas.
Se pueden destinar una audiencia de la campaña futura basándose en las respuestas de alcance de la forma en que los usuarios han respondido a esta campaña. Identificación de audiencia se pueden basar en si solo se ha insertado esta campaña, respondidos, ejecutados o terminado. Identificación de audiencia también se basa en los comentarios de respuesta de sondeo, donde la pregunta y respuesta elección se usan como criterios.

### <a name="see-also"></a>Vea también
- [Documentación - alcance - de interfaz de usuario nuevo criterio de inserción][Link 28]
 
## <a name="content-of-data-pushes"></a>Inserta el contenido de datos
![Alcance Content3][32] 

### <a name="choose-the-type-of-your-data"></a>Elija el tipo de datos:
- Texto
- Datos binarios
- Datos base 64

### <a name="define-the-content-of-your-data"></a>Definir el contenido de los datos
- Si ha seleccionado inserta los datos de texto, copie y pegue el texto en el cuadro "contenido".
- Si ha seleccionado Insertar datos binarios o base 64, use el botón "cargar el archivo" para cargar el archivo.
- Se pueden destinar una audiencia de la campaña futura basándose en las respuestas de alcance de la forma en que los usuarios han respondido a esta campaña. Identificación de audiencia se pueden basar en si solo se ha insertado esta campaña, respondidos, ejecutados o terminado.

### <a name="see-also"></a>Vea también
- [Documentación - alcance - de interfaz de usuario nuevo criterio de inserción][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Contenido de los mosaicos (solo en Windows Phone)
![Alcance Content4][33]

### <a name="define-the-content-of-your-tile"></a>Definir el contenido de su mosaico
La carga de mosaico es el texto que se mostrará en el mosaico de la aplicación en dispositivos Windows Phone.
Inserción de un mosaico es la versión de servicio de notificación de inserción de Microsoft (MPNS) de inserción nativo para Windows Phone. El tipo de inserción de mosaico es el único tipo de inserción que no tiene una respuesta y por tanto no se puede generar la audiencia de campañas futuras en los resultados de una campaña de inserción de mosaico. 

### <a name="see-also"></a>Vea también
- [Inserción de API documentación - alcance API - nativo][Link 4]

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
 
