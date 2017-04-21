<properties 
   pageTitle="Interfaz de usuario de Azure compromiso móvil - mi cuenta" 
   description="Obtenga información sobre cómo administrar los dispositivos de perfil y prueba de cuenta con el compromiso de Azure Mobile" 
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

# <a name="how-to-manage-your-account-profile-and-test-devices"></a>Cómo administrar los dispositivos de prueba y perfiles de cuenta
 
Este artículo describe la página **principal** del portal de **Compromiso de móvil** . Usar el portal de **Mobile compromiso** para supervisar y administrar las aplicaciones móviles. 
 
Para ir a la página **Mi cuenta** , haga clic en su cuenta en la parte superior de la página.

La sección Mi cuenta de la interfaz de usuario es donde puede ver y cambiar la configuración asociada a su cuenta, incluida la configuración de perfil y probar el Id. Esta configuración contiene elementos que también se pueden acceder a través de la API del dispositivo.

![MyAccount1][7]  

## <a name="profile"></a>Perfil:
Puede ver o cambiar la configuración de cuenta que se muestra a continuación. También puede dar otro usuario permiso para usar la aplicación en función de su dirección de correo electrónico desde el [Inicio](mobile-engagement-user-interface-home.md).

![MyAccount2][8]  

## <a name="devices"></a>Dispositivos:
Puede ver, agregar o quitar probar Id. de dispositivo de los dispositivos de prueba que puede usar para probar las campañas de **inserción** o **póngase en contacto** . Contextual las instrucciones encontrar el identificador de dispositivo de dispositivos para cada plataforma (iOS, Android, Windows Phone, etc.) se muestran al hacer clic en "Nuevo dispositivo". 
 
![MyAccount3][9]  
 
Para utilizar la API de inserción o API de dispositivo debe conocer el identificador único del dispositivo de los usuarios (el parámetro deviceid). Hay varias formas de recuperarla:
 
1. Desde su back-end, puede usar la característica de "Get" de la API del dispositivo para obtener la lista completa de los identificadores de dispositivos.
2. Desde la aplicación, puede usar el SDK obtenerlo. (En Android, llame a la función getDeviceID() de la clase Agent y en iOS, lea la propiedad deviceid de la clase agente.)
3. Desde el anuncio alcance, si la URL de la acción asociada con la presentación contiene el patrón de {deviceid} se automáticamente reemplazará por el identificador del dispositivo desencadenar la acción.
http://<example>.com/registeruser?deviceid = {deviceid} & otherparam = myparamdata se reemplazará por: http://<example>.com/registeruser?deviceid = XXXXXXXXXXXXXXXX & otherparam = myparamdata 
4. Desde el anuncio de web alcance, si el código HTML de la presentación contiene el patrón de {deviceid} se automáticamente reemplazará por el identificador del dispositivo que muestra la presentación web.
Aquí es mi identificador de dispositivo: {deviceid} se reemplazará por: aquí es mi identificador de dispositivo: XXXXXXXXXXXXXXXX
5.  Abra la aplicación en el dispositivo y realice un evento en la aplicación que se ha etiquetado.
Desde "Detalles de la interfaz de usuario: los eventos de aplicación - Monitor - -", busque el evento que ha realizado en la lista.
Haga clic en a este evento en el Monitor.
Encontrará el identificador de dispositivo en la lista de los dispositivos que ha realizado este evento.
A continuación, puede copiar este ID de dispositivo y registrar en "Dispositivos de interfaz de usuario - Mi cuenta - - nuevo dispositivo - seleccionar la plataforma de dispositivo".
>(Tenga en cuenta que cuando IDFA está deshabilitado para iOS, el identificador de dispositivo pueden cambiar en el tiempo si desinstalar y volver a instalar la aplicación.)

##<a name="troubleshooting-guide"></a>Guía de solución de problemas
-  [Solución de problemas - Guía de servicio][Link 24]

## <a name="see-also"></a>Vea también
-  [Documentación de la interfaz de usuario: inicio][Link 13]


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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md


 
 
