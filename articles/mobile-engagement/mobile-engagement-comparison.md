<properties
    pageTitle="Comparación de Azure Mobile compromiso con otros servicios de Azure similares"
    description="Comparación de Azure Mobile compromiso con otros servicios de Azure similares - HockeyApp, AppInsights, Hubs de notificación"
    services="mobile-engagement"
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Comparación de Azure Mobile compromiso con otros servicios de Azure similares

Nunca se expande la lista de servicios ofrecidos por Microsoft Azure y a veces se preguntará es diferente a este otro servicio que acaba de leer o escuchar acerca de cómo el compromiso de Azure Mobile. En este artículo se intenta borrar la confusión y se dirige a elegir Azure Mobile compromiso cuando es más apropiado para su uso. 
 
Compromiso de Azure Mobile es un servicio dirigido específicamente **para vendedores/CMOs digitales** pero puede ser usado por cualquier **propietario de la aplicación móvil o publisher** que quiera aumentar el uso, retención y monetización de sus aplicaciones móviles. 

*Es una plataforma de compromiso de usuario de software como servicio (SaaS) que proporciona controlados por datos información acerca del uso de la aplicación, segmentación de usuario en tiempo real y habilita las notificaciones de inserción contexto cuenta y en la aplicación de mensajería.* 

Dividir esta definición, tenemos las siguientes características claves que resalta también su propuesta de valor único:

1.  **Notificaciones de inserción contexto cuenta y en la aplicación de mensajería**
        
    Este es el enfoque del núcleo del producto: realizar las notificaciones de inserción personalizada. Y, para que ello, recopilamos datos de análisis de comportamiento enriquecido. 

2.  **Controlados por datos información sobre el uso de la aplicación**

    Le proporcionamos SDK para recopilar el análisis de comportamiento acerca de los usuarios de la aplicación de la plataforma cruzada. Observe el análisis de comportamiento de término (frente a análisis de rendimiento) porque nos centraremos en cómo los usuarios de la aplicación están utilizando la aplicación. Recopilamos datos de análisis de rendimiento básico acerca de errores, bloqueos etcetera pero, es decir, no el foco del núcleo del producto. 

3.  **Segmentación de usuario en tiempo real**

    Una vez que haya recopilado los datos de análisis de comportamiento de los usuarios de la aplicación, se le permiten segmento de la audiencia en función de distintos parámetros y recopila datos para que pueda ejecutar campañas de inserción destino. 

4.  **Software como-servicio (SaaS):**

    Tenemos un portal independiente desde el portal de administración de Azure que está optimizado para interactuar y ver el análisis de comportamiento enriquecido acerca de los usuarios de la aplicación y ejecutar inserción campañas de marketing. ¡El producto está preparado para comenzar a hacerlo en ningún tiempo!   
 
Con este conjunto de diferencias en mano, le mostramos cómo se compara con otras ofertas de Azure aparentemente similares: tenga en cuenta que el artículo no hace una comparación de características de nivel pero tarda un escenario más función enfoque para definir qué producto funciona mejor:
 
1.  [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) es la solución de DevOps móvil de Microsoft. Con ella, puede distribuir versiones al personal de pruebas beta, recopilar datos de bloqueo y obtener comentarios del usuario. También se integra con Visual Studio Team Services habilitar fácil generar implementaciones e integración de elemento de trabajo. 
    
    El enfoque aquí es en DevOps y recopilar datos de análisis de rendimiento sobre las aplicaciones móviles. Puede terminar con integración de ambos HockeyApps y compromiso Mobile en su aplicación y que no será inusual porque aunque también hay algunas superposición en los datos recopilados, el foco principales de los productos es diferente y ayudan a lograr objetivos diferentes para usted.  

2.  [Información de la aplicación](../application-insights/app-insights-overview.md) Si la aplicación móvil tiene un lado del servidor, a continuación, usará perspectivas de aplicación para supervisar el lado del servidor web de la aplicación pero para las aplicaciones móviles de lado cliente, debe usar HockeyApp. 

3.  [Notificación Hubs](https://azure.microsoft.com/services/notification-hubs/) proporciona un servicio ligero en el sentido de que no es necesario un SDK integrado en la aplicación móvil y puede tener un control total de la aplicación móvil y permite enviar notificaciones de inserción con las capacidades de etiquetado básicas. Esta es una buena opción para cualquier propietario de la aplicación que le importa menor sobre identificación y más información acerca de envío de comunicar al instante a sus usuarios de la aplicación (difusión a una población grande). 

    Tenga en cuenta el enfoque aquí sobre el envío de increíbles notificaciones rápidas con la capacidad de segmentación básica. 

Echemos algunos escenarios:

1.  TIM forma parte del equipo de marketing digital en la tienda de Adventure Works que publica aplicaciones móviles para los usuarios. Objetivo de TIM es para asegurarse de que los usuarios que descargar sus aplicaciones móviles seguirán usándolo y con frecuencia. Este no sólo aumenta una marca adjuntar con los usuarios de la aplicación, pero también aumenta monetización cuando los usuarios de la aplicación realizan compras con la aplicación móvil. Para este Tim necesitará enviar notificaciones de destinadas a los usuarios de la aplicación, que le resulte útil les aliente a abrir la aplicación y volver a él con frecuencia. Esto es donde Tim útil compromiso de móvil. 

2.  JoAnn forma parte del equipo de desarrollo de las aplicaciones móviles de Adventure Works y está buscando un producto para registrar los detalles acerca de los bloqueos, excepciones y obtener telemetría de rendimiento de una aplicación. Esto es donde Joann útil HockeyApp. JoAnn podría integrar HockeyApp para sus escenarios de desarrollador centrado tanto Azure Mobile compromiso de Tim en la misma aplicación para sacar el máximo de ambos. 

3.  Robin forma parte del equipo de desarrollo de las aplicaciones móviles de noticias de Contoso y que desea es enviar romper alertas de noticias para todos los usuarios sin mucho segmentación tan pronto como se activa la alerta de noticias. Esto es donde Robin útil Hubs de notificación. En este escenario, es posible no obstante que, a medida que crezca la aplicación móvil, hay un requisito hacer mucho más rica segmentación y obtener información detallada sobre el comportamiento del usuario de la aplicación. En este momento, Robin tendrán que evaluar compromiso de Azure Mobile. 
 
En resumen, el propósito de contratación Mobile no es solamente para recopilar analytics: no es "otro producto de análisis de Microsoft". Es sobre el envío de notificaciones de inserción destino y de destino, recopilamos datos de análisis de comportamiento pero conserva el foco sobre el envío de notificaciones de inserción que más sentido tenga para los usuarios de la aplicación para que no está incluido como correo no deseado. 

Para obtener más detalles - tardar un vistazo a este [vídeo rápido](mobile-engagement-overview.md) sobre Mobile compromiso en pocas palabras. 

