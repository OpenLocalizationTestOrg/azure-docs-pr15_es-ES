<properties 
   pageTitle="Interfaz de usuario de Azure compromiso móvil - segmentos" 
   description="Obtenga información sobre cómo crear y administrar los segmentos de usuarios para identificar patrones de uso con compromiso de móvil de Azure" 
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

# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Cómo crear y administrar los segmentos de usuarios para identificar patrones de uso

Este artículo describe la pestaña **segmentos** del portal **Compromiso de móvil** . Usar el portal de **Mobile compromiso** para supervisar y administrar las aplicaciones móviles.

La sección de segmentos de la interfaz de usuario le permite trabajar en la segmentación de los usuarios según el comportamiento diferente y análisis que puede acceder desde la aplicación y también puede obtener acceso a través de la API de segmentos. Segmentos primero se calculan 24 horas después de que se crean y se vuelven a calcular cada 24 horas en función de la información más reciente de análisis. Una vez que se calcula un segmento, se muestra un gráfico de "Día al historial de día" cada día.


>[AZURE.NOTE] Muchas secciones del portal de **Mobile compromiso** IU contienen el botón **Mostrar ayuda** . Presione este botón para obtener más información contextual sobre una sección.

## <a name="create-segments"></a>Crear segmentos
Puede crear un segmento basándose en criterios de hasta 10 en un período específico de 60 días en el pasado de la sección de análisis. Por ejemplo, puede crear un segmento basándose en las personas que han visto ciertas páginas o buscar contenido específico dentro de la aplicación dentro de los últimos 10 días. Esta información está disponible en la sección de análisis. Por lo tanto, puede usarlo para crear un segmento y configurar una notificación de inserción a este subconjunto de usuarios de destino para volver a la aplicación. 
 
> Nota: Una vez que se ha calculado un segmento, no se puede editar; solo se pueden clonar (copiado) o destruido (eliminado). Se puede clonar un segmento de la misma aplicación (con el mismo ID) y, a continuación, también se pueden clonar en otras aplicaciones (con un ID diferente). 
 
 ![segments1][35] 

## <a name="examples-segments"></a>Segmentos de ejemplos
 ![segments2][36]

Segmentos le permiten a los usuarios finales de la aplicación de segmento.
Segmentación de los usuarios es una estrategia de marketing importante. Compromiso de Azure Mobile permite obtener datos históricos y crear segmentos personalizados. Esta herramienta eficaz permite para obtener información acerca de la experiencia de los clientes de la aplicación. Puede analizar los segmentos y usar los segmentos como destinos de inserción fácilmente.
Un caso de uso común es la que desea enviar una notificación para animar a los usuarios finales puntuar la aplicación en el almacén de inserción. En lugar de enviar una notificación a todos los usuarios finales, puede crear un segmento que especificaría sólo los usuarios que han utilizado la aplicación cada día del último mes y han tenido un usuario excelente experiencia. Cuando envía notificaciones de inserción menos, de una manera, se obtiene un mejor ROI.
 
 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Segmentos que puede crear basan en los principales elementos de Azure Mobile compromiso:
- Evento: cree un segmento que destinos un determinado evento de la aplicación que ha pasado más de dos veces por semana. 
- Sesión: crear un segmento de usuarios que han usado la aplicación de la semana pasada más de 5 veces.
- Actividad: crear un segmento de usuarios que han usado una página o contenido más o menos de 10 tiempo mes pasado.
- Trabajo: crear un segmento de usuarios que han completado un trabajo más de dos veces un día.
- Bloqueo: crear un segmento de todos los usuarios que tienen un bloqueo semana pasada de más de 10 veces. (Podría Empujar este segmento con un disculpas o incluso cupón!)
- Error: crear un segmento de todos los usuarios que tienen un error más de 100 veces últimos 3 días.
- Información de la aplicación: crear un segmento que dirige a una información de aplicación personalizada que se hicieron durante los últimos días 25.
 
 ![segments4][38]

Para usar el segmento de manera óptima, debe realizar una integración personalizada del SDK en la aplicación con un plan de marcado de etiquetas de "Información de la aplicación".
A continuación, vaya a la página principal de la interfaz de, seleccione la aplicación que desee y haga clic en la sección "Segmentos".

1. Seleccione la sección "Segmentos".
2. Haga clic en "nuevo segmento" botón para crear un nuevo segmento.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Ejemplo de la vida real: Crear un segmento simple en función de la información de "Sesión"
Crear un segmento de todos los usuarios finales que ha utilizado la aplicación al menos 50 veces en la última semana. Desde allí, busque solo los usuarios finales que pasó al menos 30 segundos en su aplicación por sesión. Esto le mostrará todos los usuarios finales que tengan una experiencia positiva en la aplicación. A continuación, podría utilizarse el segmento creado a una notificación de inserción a estos usuarios finales y pregúntele puntuar la aplicación de la tienda.
 
 ![segments5][39]

1. Asigne un nombre a su segmento para encontrarlo rápidamente en la lista "Segmento".
2. Haga clic en el botón "Crear".
 
 ![segments6][40]

Seleccione la sesión.
 
 ![segments7][41]

1. Seleccione el periodo de "Semana pasada".
2. Haga clic en siguiente.
 
 ![segments8][42]

1. Seleccione el operador que es relevante en la lista: =; ≥, ≤.
2. Escriba el número que desee.
3. Seleccione la repetición que desee. 
4. Haga clic en siguiente.
En este ejemplo se establece por lo que en la última semana, coinciden con los usuarios que han realizado al menos 50 sesiones.
 
 ![segments9][43]

Para la segmentación de sesión, puede elegir la longitud por sesión como un criterio.

1. Seleccione el operador de la lista.
2. Proporcionar la longitud por sesión.
3. Haga clic en siguiente.
En este ejemplo, dice que sobre todas las sesiones que han sido segmentado en la sección de repetición, seleccione solo los usuarios que han invertido más de 30 segundos por sesión.
 
 ![segments10][44]

Asigne un nombre a su criterio para recuperar en el embudo completo y haga clic en Finalizar.
 
 ![segments11][45]

Cuando haya terminado de configurar el criterio, aparecerá en el embudo de segmento.
Dado que se basa en un segmento de datos de análisis, se calculan los segmentos de una vez al día.
En este ejemplo, 47,7% de los usuarios finales total coincide con el criterio. Deben ser los usuarios que tengan una buena experiencia y estarán probable que proporcionan una clasificación superior si se inserta una notificación pedirle a puntuar la aplicación de la tienda.


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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 
