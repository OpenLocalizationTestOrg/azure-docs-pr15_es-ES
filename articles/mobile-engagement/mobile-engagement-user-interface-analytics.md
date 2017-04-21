<properties
   pageTitle="Interfaz de usuario de Azure compromiso móvil - análisis"
   description="Obtenga información sobre cómo analizar datos históricos acerca de la aplicación mediante el compromiso de móvil de Azure"
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

# <a name="how-to-analyze-historical-data-about-your-application"></a>Cómo analizar datos históricos acerca de la aplicación

Este artículo describe la pestaña **análisis** del portal de **Compromiso de móvil** . Usar el portal de **Mobile compromiso** para supervisar y administrar las aplicaciones móviles. Tenga en cuenta que para empezar a usar el portal primero tiene que crear una cuenta de **Azure Mobile compromiso** .


La sección de análisis de la interfaz de usuario proporciona agregada información sobre la aplicación basada en los datos históricos que se actualizan cada 24 horas. La información se muestra en los distintos paneles que se compone de mapas, cuadrículas y gráficos de líneas o subgráfico. Asimismo, puede descargar los datos como archivos CSV. La mayoría de la misma información está disponible en tiempo real en la sección Monitor de la interfaz de usuario, y también se puede acceder desde la API de análisis.

>[AZURE.NOTE] Muchas secciones del portal de **Mobile compromiso** IU contienen el botón **Mostrar ayuda** . Presione este botón para obtener más información contextual sobre una sección.

## <a name="standard-and-custom-analytics"></a>Análisis estándar y personalizado

Compromiso de Azure Mobile proporciona un conjunto de información analítica estándar básica sobre las aplicaciones que se puede representar gráficamente tan pronto como integrar la aplicación con el SDK. Compromiso de Azure Mobile también proporciona la capacidad para recopilar información sobre el comportamiento de los usuarios de finales análisis personalizado adicional que desee. Puede hacer esto mediante la creación de un plan de etiqueta de "Etiquetas personalizadas (información de la aplicación)", creado a partir de **configuración** para que el compromiso de Azure Mobile puede recopilar datos adicionales para usted.



## <a name="analytics"></a>Análisis
- Panel: Muestra información general acerca de los usuarios nuevos y activa y sus tendencias.
- Usuarios: Los usuarios se identifican por su identificador de dispositivo: este identificador es único para cada dispositivo (un usuario nuevo es realmente un dispositivo nuevo). Se considera un usuario como nueva en un intervalo de tiempo determinado, si ha llevado a cabo su primera sesión durante este intervalo de tiempo. Se considera un usuario se conservan si ha llevado a cabo al menos una sesión durante los últimos 7 días. Los usuarios activos son realizados por al menos una sesión durante un período determinado. Puede ordenar por, mensual, semanal, diaria o períodos de tiempo de cada hora. Todos los gráficos son similares pero permiten para filtrar por las diferentes características, como la versión de la aplicación y, a continuación, ordenar por un período de tiempo. La información estándar recopilada mediante la integración del SDK incluye lo siguiente: usuarios activos, nuevo usuario, número de sesiones, la longitud de cada sesión, la información técnica sobre el país, variables locales, ubicación, carrier de idioma, dispositivos, firmware, red (Wi-Fi), versiones de la aplicación y el SDK, usados por los clientes. Esta información se puede ver en tiempo real en la sección supervisar.

> Nota: El período de tiempo se basa en la fecha de la configuración de dispositivo de los usuarios, por lo que un usuario cuyo teléfono tiene la fecha establecida correctamente podría mostrarse en el período de tiempo incorrecta.

- Retención: Un usuario se considera que se conservan en un intervalo de tiempo determinado, si ha llevado a cabo su primera sesión durante este intervalo de tiempo. Puede cambiar los intervalos de tiempo durante el cual se cuentan los usuarios retención (y los nuevos usuarios) a horas, días, semanas o meses. El análisis de retención de usuario se basa en las cohortes. Un grupo de edad es el conjunto de todos los usuarios nuevos detectado durante un período determinado (por ejemplo, el conjunto de usuarios que pueden realizar su primera sesión durante este período). Usamos a las cohortes de 1 día, 2 días, 4 días, 7 días o 1 mes. Dada una edad, cada día 1, 2 días, 4 días, 7 días, o 1 mes, Azure compromiso móvil calcula el conjunto de todos los usuarios que pertenecen a la edad y se sigue activo (es decir, el conjunto de usuarios que ha realizado al menos una sesión durante el período). Este conjunto de usuarios se denomina una versión de edad. (Azure compromiso Mobile puede mostrar cuántos de los usuarios sigue usando la aplicación, pero solo el almacén específico de plataforma puede saber cuántos de los usuarios desinstalan la aplicación: por ejemplo, GooglePlay, de iTunes, tienda de Windows, etcetera.).
- Sesiones: Uso de la aplicación de un usuario. Sesiones se generan a partir de la secuencia de actividades realizadas por los usuarios (una actividad generalmente asociada al uso de una pantalla de la aplicación, pero esto puede variar según la manera en que el SDK se ha integrado en la aplicación). Un usuario sólo puede realizar una actividad a la vez: inicia una sesión tan pronto como el usuario inicia su primera actividad y se detiene cuando acabe su última actividad. Si un usuario permanece más de unos pocos segundos sin realizar ninguna actividad, su secuencia de actividades se divide en dos sesiones distintas.
- Actividades: Los nombres de cada pantalla de la aplicación y la longitud de tiempo que los usuarios se dedican a cada pantalla. Las actividades son una opción analítica personalizada que se correspondan con las etiquetas de "información de la aplicación" que se ha configurado para su propia aplicación:
- Ruta de acceso de usuario: Muestra cómo los usuarios navegar a través de actividades de la aplicación (pantallas). Puede mover el control deslizante para ajustar el nivel de detalles. Azules nodos representan las actividades de la aplicación. Su tamaño es proporcional el tiempo invertido usuarios en él. Nodos blancos representan detener e iniciar sesión. Rojo nodos representan se bloquea. Vínculos representan las transiciones entre las actividades de la aplicación (o entre las actividades y se bloquea). Haga clic en un nodo o un vínculo para mostrar una sugerencia con más información sobre los datos: el tiempo invertido en una pantalla en particular, el recuento de las transiciones y el porcentaje de transiciones de la actividad de origen a la actividad de destino. (Un---60%---> significa B que los usuarios de actividad A va a actividad B 60% del tiempo.) Puede reorganizar el gráfico que desea aclarar cada vez que realice un cambio, se guardará su posición. Puede mostrar u ocultar los bloqueos para aclarar el gráfico.
- Eventos: Acciones específicas realizadas por un usuario en la aplicación. Se muestra la distribución de eventos como el recuento de eventos por usuario y sesión. Un evento representa una instantánea acción, por ejemplo, al hacer clic en un botón o la recepción de una notificación. (El significado de eventos depende de cómo se ha integrado el SDK en la aplicación.) Un evento puede producirse durante una sesión o un trabajo, o puede ser independiente.
- Trabajos: Similares a los eventos salvo que centran en la longitud de la acción. Por ejemplo, trabajos podrían indicar información técnica sobre cuánto tiempo tarda contenido para cargar o una llamada al servicio web. También puede mostrar cuánto tiempo tarda un usuario para rellenar un formulario, cree una cuenta o realizar una compra. Un trabajo representa la duración de una tarea, por ejemplo, la duración de una tarea de descarga o el tiempo que se muestra una pancarta en la pantalla. (El significado de trabajos depende de cómo se ha integrado el SDK en la aplicación.) Trabajos se asocian normalmente a tareas en segundo plano que se realizan fuera del ámbito de una sesión (es decir, sin actividad de usuario).
- Technicals: Información técnica sobre los dispositivos de los usuarios de la aplicación que puede realizar un seguimiento, como la configuración regional Carrier, red, dispositivo, Firmware y tamaño de los dispositivos de los usuarios y la versión de la aplicación y la versión SDK usada en la aplicación de la pantalla.
- Errores: Información sobre los errores técnicos dentro de la aplicación que hacen que la aplicación que se bloquea. Un error representa un problema instantáneo, por ejemplo, un error de red o una manipulación incorrecta. (El significado de eventos depende de cómo se ha integrado el SDK en la aplicación.) Un error puede producirse durante una sesión o un trabajo, o puede ser independiente.
- Bloqueos: Información sobre los errores que hacer que la aplicación que se bloquea. Un bloqueo es una condición inesperada donde la aplicación deja de realizar sus funciones esperados y debe detenerse. Un bloqueo suele ser debido a un error en la aplicación.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Obtener acceso a la información general de retención
![Analytics3][12]

La información general de retención se divide por la mitad en varias tarjetas, cada una que muestra la información general de un período de retención. En el ejemplo, se ve el período de retención de 2 días. Las otras tarjetas muestran los períodos de retención de 4 días y 7 días.

## <a name="understanding-the-retention-overview-cards"></a>Descripción de las tarjetas de información general de retención
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Cada tarjeta se compone de 3 partes principales:
1. 1: consideran edad y el período
2. 2-4: la retención para el período actual
3. 5: un minigráfico del historial

### <a name="here-is-detailed-information-about-each-element"></a>Esto es información detallada sobre cada elemento:
1.    Edad y período: este encabezado indica el tipo de edad. Aquí "2 días período" significa que veremos el comportamiento de los usuarios más de 2 días, los usuarios recibidos durante un período de 2 días y si se conectan en los siguientes bloques de 2 días. El ejemplo anterior considera la actividad de los usuarios entre los 21 y 22 de noviembre.
2.    Esto le da la tasa de retención sobre la 21 y 22 de noviembre de los usuarios que llegan en 19 y 20 de noviembre. Aquí tenemos 1 el usuario activo entre los 21 y 22, sobre las 3 que estaban nuevos usuarios entre el 19 y 20.
3.    Este indicador visual proporciona la misma información que anteriores representar gráficamente. (El tercer del círculo es desde el número de 33%). El color proporciona información adicional: verde indica que este número está creciendo desde el cálculo anterior. Amarillo significa estable y rojo significa disminuir.
4.    Esto indica los valores que se utilizan para el cálculo.
5.    Se trata de un minigráfico del historial de los valores de retención. Permite ver los valores en el pasado a tiene una vista amplia de cómo desarrollado a partir.


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
