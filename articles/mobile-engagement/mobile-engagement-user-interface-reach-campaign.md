<properties 
   pageTitle="Interfaz de usuario de Azure compromiso móvil - alcance campaña" 
   description="Laern cómo crear y administrar las notificaciones de inserción campañas con compromiso de móvil de Azure" 
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


# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Cómo crear y administrar las campañas de notificación de inserción
Puede usar la sección alcance de la interfaz de usuario para crear una nueva campaña de inserción con una fórmula compleja proporcionando toda la información que necesita para enviar una notificación de inserción. Las opciones de una campaña de inserción varían en función de los tipos de cuatro campaña: anuncios, los sondeos, inserta datos y mosaicos (solo en Windows Phone).

### <a name="option-applies-to"></a>Opción se aplica a:
- Idiomas: Todos (anuncios, sondeos, datos Push, mosaicos)
- Campaña: Todos (anuncios, sondeos, datos Push, mosaicos)
- Notificación: Anuncios, sondeos
- Content: Único para cada tipo de campaña
- Público: Todos (anuncios, sondeos, datos Push, mosaicos)
- Período de tiempo: anuncios, sondeos, mosaicos
- Prueba: Todos (anuncios, sondeos, datos Push, mosaicos)
 
![Alcance Campaign1][20]

## <a name="languages"></a>Idiomas
Puede usar el menú desplegable de idiomas para enviar una versión diferente de la inserción a dispositivos configurados para usar diferentes idiomas. De forma predeterminada, todos los dispositivos recibirán la inserción del misma independientemente del lenguaje se configuran para usar. Los usuarios con su dispositivo configurado en un idioma diferente recibirán la versión de idioma predeterminado de la inserción. Muchas de las opciones de la campaña de inserción le permiten especificar contenido alternativo para cada uno de los idiomas adicionales que seleccione. 
 
![Alcance Campaign2][21]

### <a name="language-differences-apply-to"></a>Diferencias de idioma se aplican a:
- Idiomas: Idiomas únicos se pueden seleccionar además del idioma predeterminado
- Campaña: Igual para todos los idiomas
- Notificación: Único para cada idioma además del idioma predeterminado
- Content: Único para cada idioma además del idioma predeterminado
- Público: Puede filtrar por un criterio de idioma independiente
- Período de tiempo: mismo para todos los idiomas
- Prueba: Puede enviarse a cada idioma a la vez
 
### <a name="supported-languages"></a>Idiomas admitidos:
- Árabe (ar) 
- Búlgaro (bg) 
- Catalán (ca) 
- Chino (zh) 
- Croata (h) 
- Czech (cs) 
- Danés (da) 
- Neerlandés (Países Bajos) 
- Inglés (en) 
- Finés (fi) 
- Francés (f) 
- Alemán (de) 
- Griego (el) 
- Hebreo (he) 
- Hindi (Hola) 
- Húngaro (hu) 
- Indonesio (Id.) 
- Italiano (it) 
- Japonés (ja) 
- Coreano (ko) 
- Letón (lv) 
- Lituano (LP) 
- Malayo (macrolanguage) (ms) 
- Noruego (bokmaal) (Nota) 
- Polaco (pl) 
- Portugués (pt) 
- Rumano (ro) 
- Ruso (ru) 
- Serbio (sr) 
- Eslovaco (sk) 
- Esloveno (sl) 
- Español (es) 
- Sueco (sv) 
- Tagala (tl) 
- Tailandés (th) 
- Turco (tr) 
- Ucraniano (Reino Unido) 
- Vietnamita (vi) 
 
## <a name="campaign"></a>Campaña
Puede usar la sección de campaña para establecer el nombre y la categoría de la campaña, así como si va a pasar por alto la sección audiencia de una campaña de inserción y enviar esta campaña a través de la API alcanzar (y algunos elementos con la API de inserción de bajo nivel) en su lugar. Categorías se pueden utilizar con una plantilla de notificación personalizado para notificaciones de control de la aplicación en función de los valores predefinidos. Puede obtener una lista de las "categorías" existentes mediante la API de llegar a.

> Advertencia: Si se usa la opción "Ignorar audiencia, inserción se enviarán a los usuarios a través de la API" en la sección "Campaña" de una campaña de alcance de la campaña no enviará automáticamente, debe enviar de forma manual a través de la API de alcance.
 
![Alcance Campaign3][22]
 
### <a name="option-applies-to"></a>Opción se aplica a:
- Nombre: todos
- Categoría: Anuncios, sondeos
- Omitir la audiencia, inserción se enviarán a los usuarios a través de la API: todos
 
## <a name="notification"></a>Notificación
Puede utilizar la sección de notificación para establecer la configuración básica para la inserción incluidos: el título de la inserción, el mensaje, una imagen de la aplicación, o si se pueden pasar por alto. Muchas configuraciones de notificación son específicos de la plataforma de su dispositivo. Puede seleccionar si la inserción se enviarán "en la aplicación" o "fuera de la aplicación" o ambas. (Recuerde que los usuarios pueden "participar en la" o "rechazar" de "fuera de la aplicación" inserta en el sistema operativo nivel en sus dispositivos, y Azure Mobile compromiso no podrá reemplazar esta configuración. Recuerde que controladores de la API de llegar a "en la aplicación" y "fuera de la aplicación" inserta. La API de inserción puede usarse para controlar "fuera de la aplicación" Push demasiado.) Inserta puede personalizarse con imágenes o contenido HTML, incluidos los vínculos de profundidad para vincular fuera de la aplicación o a otra ubicación en la aplicación (SDK Android 2.1.0 o posterior categorías intención requeridos). Puede cambiar el icono o iOS distintivo y enviar contenido web o de texto (una ventana emergente con el vínculo de dirección URL contenido html a otra ubicación dentro o fuera de la aplicación). También puede realizar llamadas de dispositivos Android o Vibrar con la inserción. (Recuerde que tendrá el correcto permisos SDK en Android manifiestos archivo para llamar o un dispositivo de vibración.) Actualmente no hay ningún sector estándar para tamaños de Android "Panorama general", porque los tamaños de pantalla son diferentes en todos los dispositivos, pero 400 x 100 imágenes trabajar en casi cualquier tamaño de la pantalla.

### <a name="delivery-types"></a>Tipos de entrega:
-    En la aplicación solo: la notificación se entregarán cuando el usuario no utiliza la aplicación.
- Fuera de la notificación de aplicación única requiere un certificado de Apple o Google (certificado APN o GCM).
- En la aplicación solo: la notificación aparece sólo cuando se ejecuta la aplicación.
- La notificación usa el sistema de entrega Capptain para comunicarse con el usuario. Puede personalizar el diseño o presentación visual de la inserción completamente.
- En cualquier momento: Esta opción garantiza que se envía una notificación que en la aplicación se está ejecutando o no.

 
![Alcance Campaign4][23]

### <a name="option-applies-to"></a>Opción se aplica a:
- Notificación: Anuncios, sondeos
 
## <a name="content"></a>Contenido
Puede usar la sección contenido para modificar el contenido de los anuncios, los sondeos, inserta datos y mosaicos (solo en Windows Phone). La configuración de contenido de las campañas de inserción es específica del tipo de campaña. 

### <a name="see-also"></a>Vea también
- [Documentación de la interfaz de usuario - alcance: insertar contenido][Link 29]
 
![Alcance Campaign5][24]

## <a name="audience"></a>Audiencia
Puede usar la sección audiencia para definir una lista de elementos para limitar la campaña o los límites de la campaña en función de criterios personalizados estándar. El conjunto de opciones para limitar la audiencia estándar le permite ingresar a usuarios nuevos o antiguos o solo los usuarios de inserción nativa. También puede establecer una cuota para limitar el número de usuarios que recibe la inserción. Puede editar manualmente la expresión de cómo está filtrada la campaña para incluir uno o varios de los criterios para los usuarios de destino. Puede escribir manualmente una expresión de la audiencia. Este tipo de expresión debe definir explícitamente la relación entre los criterios. Un criterio se describe mediante un identificador que debe comenzar con una letra capital y no puede contener espacios. Puede describir la relación entre los criterios con 'and', 'o', 'not' y operadores como '(',')'. Ejemplo: "Criterion1 o (Criterion1 y no Criterion2)".

> Nota: Con una gran audiencia incluida en las campañas, el lado del servidor identificación análisis puede ser lento, especialmente si intenta iniciar varias campañas al mismo tiempo.

- Si es posible, inicie una campaña a la vez.
- Como máximo, inicie cuatro campañas a la vez.
- Inserción solo a los usuarios activos (casilla de verificación "integrarse solo los usuarios que se pueden llegar mediante inserción nativa" y "Integrarse solo los usuarios activos") para que solo los usuarios que todavía tienen la aplicación instalada y usarlo tendrán que analizar.
Una vez que se ha definido la audiencia, puede utilizar el botón de simular para averiguar cuántos usuarios recibirá esta inserción. Se va a calcular el número de usuarios conocidos potencialmente el objetivo de esta audiencia (es decir, una estimación basada en una muestra aleatoria de usuarios). Tenga en cuenta que los usuarios que han desinstalado la aplicación también forman parte de esta audiencia, pero no se puede llegar.

### <a name="see-also"></a>Vea también
- [Documentación - alcance - de interfaz de usuario nuevo criterio de inserción][Link 28]

![Alcance Campaign6][25]

### <a name="edit-expression"></a>Editar expresión
![Alcance Campaign7][26]
 
### <a name="limit-your-audience-option-applies-to"></a>Límite de: que la opción de audiencia se aplica:
- Integrarse únicamente un subconjunto de los usuarios: todos (anuncios, sondeos, inserta datos, mosaicos)
- Contratar a solo los usuarios antiguos: todos (anuncios, sondeos, inserta datos, mosaicos)
- Integrarse solo nuevos usuarios: todos (anuncios, sondeos, inserta datos, mosaicos)
- Contratar a solo los usuarios inactivos: anuncios, sondeos, mosaicos
- Contratar a solo los usuarios activos: todos (anuncios, sondeos, inserta datos, mosaicos)
- Contratar a solo los usuarios que pueda usar Push nativa: anuncios, sondeos
 
## <a name="time-frame"></a>Período de tiempo
Puede usar la sección período de tiempo para establecer cuando la inserción se enviarán o puede dejarse en blanco para iniciar la campaña inmediatamente el período de tiempo. Recuerde que usa la zona horaria de los usuarios puede iniciar la campaña un día antes de lo que pasará con los usuarios finales en Asia y enviar pequeños lotes de Push en un momento hasta que todas las zonas horarias en el mundo que coincida con el período de tiempo establecido para la campaña de. Uso de zona horaria de los usuarios finales puede causar retrasos en las campañas puesto que debe solicitar la hora desde el teléfono antes de iniciar la inserción.

> Nota: Campañas sin una fecha de finalización puede almacenar en caché Push localmente y aún mostrarlas después campañas manualmente completa. Para evitar este comportamiento, específicas de una hora de finalización para las campañas.

### <a name="see-also"></a>Vea también
- [Alcance - cómo visuales: programación][Link 3] 
 
![Alcance Campaign8][27]

### <a name="settings-apply-to"></a>Aplica configuración a:
- Período de tiempo: anuncios, sondeos, mosaicos
 
## <a name="test"></a>Prueba
Puede usar la sección de prueba para enviar esta inserción a su propio dispositivo de prueba antes de guardar la campaña. Si ha configurado los idiomas personalizados para esta campaña, puede probar la inserción en cada idioma. Puede configurar un dispositivo de prueba de "Mi cuenta".
> Nota: Del servidor se registran los datos cuando use el botón "probar" no inserta, solo se registran los datos para las campañas de inserción real.

### <a name="see-also"></a>Vea también
- [Documentación de la interfaz de usuario: Mi cuenta][Link 14]
 
![Alcance Campaign9][28]

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
 
