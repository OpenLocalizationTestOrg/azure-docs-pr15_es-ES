<properties 
   pageTitle="Interfaz de usuario de Azure compromiso móvil - alcance criterio" 
   description="Aprenda a usar criterios destinatarios enviar campañas de inserción a un subconjunto de los usuarios mediante el compromiso de Azure Mobile seleccionado" 
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


# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Cómo usar criterios de destino para enviar las campañas de inserción a un subconjunto seleccionado de los usuarios

Identificación de la audiencia por criterios específicos con el botón "Nuevo criterio" es uno de los conceptos más eficaces en Azure Mobile compromiso que ayuda a que envíe relevantes notificaciones de inserción que los clientes se responderán a en lugar de correo basura todos los usuarios. Puede limitar la audiencia basándose en los criterios estándares y simular Push para determinar cuántas personas recibirán la notificación.

**Vea también:**

- [Documentación - alcance - nueva inserción campaña de interfaz de usuario][Link 27]

## <a name="audience-criteria-can-include"></a>Criterios de audiencia se pueden incluir:
- **Technicals:** Se pueden destinar basado en la misma información técnica que puede ver en las secciones Monitor y análisis. **Vea también:** [Documentación de la interfaz de usuario - análisis] [ Link 15], [Documentación de la interfaz de usuario - Monitor][Link 16]
- **Ubicación:** Aplicaciones que usen "ubicación informes en tiempo Real" con Geo barrera pueden usar ubicación geográfica como criterio para una audiencia de la ubicación de GPS de destino. Llamada "Informes de ubicación de área diferida" también pueden utilizarse para una audiencia de la ubicación de teléfono de la celda de destino ("Tiempo Real ubicación informes" y "diferida área ubicación" deben activarse desde el SDK). **Vea también:** [Documentación de SDK - iOS - integración] [ Link 5], [SDK documentación - Android - integración][Link 5]
- **Llegar comentarios:** Se pueden destinar la audiencia en función de sus comentarios de notificaciones de alcance anteriores a través de comentarios de alcance de anuncios, los sondeos e inserta datos. Esto le permite dirigir mejor el público después de dos o tres llegar a las campañas de podría la primera vez. También puede usarse para filtrar los usuarios que ya ha recibido una notificación con contenido similar, estableciendo una campaña no se envíen a los usuarios que ya ha recibido una determinada campaña anterior. Incluso puede excluir los usuarios que se incluye una campaña específica que todavía está activa recibir Push nuevo. **Vea también:** [Documentación de la interfaz de usuario - alcance - inserción de contenido][Link 29]
- **Instalar seguimiento:** Puede realizar un seguimiento de la información en función de donde los usuarios instalaron la aplicación. **Vea también:** [Documentación de la interfaz de usuario - configuración][Link 20]
- **Perfil de usuario:** Se pueden destinar estándar en función de información de usuario y que puede destino basándose en la información de la aplicación personalizada que ha creado. Esto incluye los usuarios que han iniciado sesión en usuarios y que han respondido a las preguntas específicas que solicitaron establecer en la aplicación en lugar de simplemente cómo han respondido a campañas anteriores. Toda la información de la aplicación definida para su aplicación aparecen en esta lista.
- Segmentos: También puede destino basándose en segmentos que ha creado en función de comportamiento de usuario específico que contiene varios criterios. Todos los segmentos definidos para la aplicación se muestran en esta lista. **Vea también:** [Documentación de la interfaz de usuario - segmentos][Link 18]
- **Información de la aplicación:** Etiquetas de información de aplicación personalizadas pueden crearse en "Configuración" para realizar un seguimiento de comportamiento de los usuarios. **Vea también:** [Documentación de la interfaz de usuario - configuración][Link 20]

## <a name="example"></a>Ejemplo: 
Si desea insertar un anuncio solo en el conjunto de subcarpetas de los usuarios que ha realizado una acción en la aplicación de compra.

1. Vaya a la página de configuración de aplicación, seleccione el menú "Información de la aplicación" y seleccione "Nueva información de aplicación"
2. Registrar una nueva información de aplicación booleana denominada "inAppPurchase"
3. Asegúrese de establecer esta información de la aplicación "true" cuando el usuario realiza correctamente una compra en la aplicación de la aplicación (mediante el uso de la sendAppInfo ("inAppPurchase",...) función)
4. Si no desea hacerlo desde la aplicación, puede hacerlo desde el back-end mediante la API de dispositivo)
5. A continuación, solo tiene que crear su presentación, con un criterio limitar la audiencia a los usuarios que tengan "inAppPurchase" establecido en "true")
 
> Nota: Basándose en los criterios que no sea de etiquetas de la información de aplicación de destino requiere Azure Mobile compromiso recopilar información de los dispositivos de los usuarios antes de enviar la inserción y, por lo que puede producir un retraso. Inserta la configuración de inserción compleja también pueden retrasar opciones (por ejemplo, actualizar distintivos). Una campaña de "uno captura" de la API de inserción es el método más rápido de inserción absoluta en Azure Mobile compromiso. Solo las etiquetas de información de aplicación como criterios de inserción para una campaña de alcance (ya sea de la API de llegar o la interfaz de usuario) es el método más rápido siguiente como etiquetas de información de aplicación se almacenan en el servidor. Otros destinatarios criterios para una campaña de inserción es el método de inserción más flexible pero más lento debido a que tiene el compromiso de Azure Mobile consultar los dispositivos para poder enviar la campaña.
 
![Alcance Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Opciones de los criterios que se aplican a:
- **Technicals**     
- Nombre de firmware: nombre Firmware
- Versión de firmware: versión de Firmware
- Modelo de dispositivo: modelo de dispositivo
- Fabricante del dispositivo: fabricante del dispositivo
- Versión de la aplicación: versión de la aplicación
- Nombre de transportista: nombre de transportista, sin definir
- País Carrier: país Carrier, sin definir
- Tipo de red: tipo de red
- Configuración regional: configuración regional
- Tamaño de pantalla: tamaño de pantalla
- **Ubicación**      
- Última conocida área: país, región, ubicación
- En tiempo real geo-barrera: lista de interés (nombre, acciones), Circular POI (nombre, latitud, longitud, radio en metros)
- **Llegar a los comentarios**     
- Comentarios de anuncio: anuncio, comentarios
- Sondeos comentarios: sondeo, comentarios
- Sondeo de comentarios de respuesta: sondeos comentarios de respuesta, pregunta, opción
- Comentarios de inserción de datos: inserción de datos, comentarios
- **Instalación de seguimiento**     
- Almacén: Almacén, no definida
- Fuente: Origen, sin definir
- **Perfil de usuario**     
- Género: macho o hembra, sin definir
- Fecha de nacimiento: operador, fecha, no definida
- Opcional: verdadero o falso, no definido
- **Información de la aplicación**      
- Cadena: Cadena, sin definir
- Fecha: operador, la fecha, no definido
- Entero: operador, número, no definida
- Valor booleano: verdadero o falso, no definida
- **Segmento**    
- Nombre de segmentos (desde la lista desplegable), exclusión (usuarios de destino que no forman parte de este segmento).

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
 
