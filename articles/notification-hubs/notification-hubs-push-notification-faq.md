<properties
    pageTitle="Notificación de Azure Hubs - preguntas más frecuentes (P+f)"
    description="Preguntas más frecuentes sobre diseñar e implementar soluciones en Hubs de notificación"
    services="notification-hubs"
    documentationCenter="mobile"
    authors="ysxu"
    manager="erikre"
    keywords="notificación de inserción, las notificaciones de inserción, notificaciones de inserción de iOS, las notificaciones de inserción android, inserción de ios, android inserción"
    editor="" />

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu" />

#<a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Notificaciones con Azure notificación Hubs - Push preguntas más frecuentes

##<a name="general"></a>General
###<a name="1---what-is-the-price-model-for-notification-hubs"></a>1. ¿Qué es el modelo de precio de notificación Hubs?
Notificación Hubs se ofrece en tres niveles:

* **Liberar** - obtener hasta 1 millón Push por suscripción mes.
* **Básico** - Push de 10 millones de obtener por suscripción mes como una línea de base, con opciones de cuotas de crecimiento.
* **Estándar** - obtener Push de 10 millones por suscripción mes como una línea base, con cuota aumentar opciones, además de capacidades de telemetría enriquecido.

Los últimos detalles pueden encontrarse en la página de [Precios de Hubs de notificación] . Los precios se establecen en el nivel de suscripción y se basa en el número de inicios de notificación de inserción para que no importa cuántas hubs de espacios de nombres o notificación de que haya creado en su suscripción de Azure.

Nivel de **libre** se ofrece para el propósito de desarrollo con ninguna garantía SLA. Mientras este nivel podría ser un buen punto de partida para los que desee explorar las capacidades de notificaciones de inserción a través Azure notificación Hubs, no sería la mejor opción para medio para las aplicaciones de gran escala.

**Básico** & se ofrecen niveles**estándar** de uso de producción con las siguientes características claves activada *sólo para el estándar de nivel*:

- *Telemetría enriquecido* - oferta de notificación Hubs un número de capacidades para exportar los datos de telemetría así como información de registro de notificación para verlos sin conexión y análisis de inserción.
- *Soporte para múltiples usuarios* - Ideal si va a crear una aplicación móvil usando Hubs de notificación para ser compatible con varios inquilinos. Esto le permite establecer las credenciales de servicios de notificación de inserción (PNS) en el nivel de espacio de nombres de concentrador de notificación para la aplicación y, a continuación, puede separar a los inquilinos proporcionarles hubs individuales en este espacio de nombres común. Esto permite facilidad de mantenimiento mientras mantiene las claves de SA para enviar y recibir notificaciones de inserción de los centros de notificación separada para cada inquilino garantizar una superposición de inquilinos de cruzado no.
- *Inserción programada* - le permite programar las notificaciones de inserción, que se utilizará en la cola y envía posteriormente.
- *Importación masiva* - le permite importar los registros de forma masiva.

###<a name="2---what-is-the-notification-hubs-sla"></a>2. ¿Qué es el SLA de Hubs notificación?
Niveles **básicos** y **estándar** Hubs de notificación, garantizamos que al menos un 99,9% del tiempo, aplicaciones configuradas correctamente podrán enviar notificaciones de inserción o realizar operaciones de administración de registro con respecto a un concentrador de notificación implementado en un nivel compatible. Para obtener más información acerca de nuestro SLA, visite la página de [Notificación Hubs SLA] .

> [AZURE.NOTE] No hay ninguna garantía SLA para el segmento entre el servicio de notificación de la plataforma y el dispositivo desde Hubs de notificación dependen de proveedores externos plataforma de ofrecer la notificación de inserción al dispositivo.

###<a name="3---which-customers-are-using-notification-hubs"></a>¿3. los clientes están usando Hubs de notificación?
Tenemos una gran cantidad de clientes con notificación Hubs algunos estilos notables a continuación:

* Sochi de 2014: 100s de grupos de interés, los dispositivos de 3 + millones, notificación de 150 millones envío de 2 semanas. [CaseStudy - Sochi]
* Skanska - [CaseStudy - Skanska]
* Horas de Seattle - [CaseStudy - Seattle Times]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Aplicaciones de Bing-10s de millones de dispositivos, enviar notificaciones 3 millones al día.

###<a name="4-how-do-i-upgrade-or-downgrade-my-notification-hubs-to-change-my-service-tier"></a>4. ¿cómo actualizar o degradar mi Hubs notificación para cambiar el nivel de servicio?
Vaya al [Portal de Azure clásica], haga clic en Bus de servicio y haga clic en el espacio de nombres, su centro de notificación. En la ficha escala, podrá cambiar el nivel de servicio de notificación Hubs.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##<a name="design--development"></a>Diseño y desarrollo
###<a name="1---which-server-side-platforms-do-you-support"></a>1. las plataformas de servidor ¿admiten?
Le proporcionamos los SDK y [ejemplos completadas] para .NET, Java, PHP, Python, Node.js para que una aplicación de back-end pueden configurar para comunicarse con notificación Hubs con cualquiera de estas plataformas. Notificación Hubs API se basan en interfaces resto puede hablar directamente con las en su lugar si no desea agregar una dependencia adicional. Encontrará más detalles en la página [NH - API de REST] .

###<a name="2---which-client-platforms-do-you-support"></a>2. ¿las plataformas de cliente se admite?
Se admiten las notificaciones de inserción envío [Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Universal de Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [China Android (mediante Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), plataformas de [Aplicaciones de Chrome](notification-hubs-chrome-push-notifications-get-started.md) y [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) . Para obtener una lista completa de tutoriales procedimientos iniciales abordar enviar notificaciones de inserción en estas plataformas, visite nuestra página de [NH - obtener tutoriales de introducción] .

###<a name="3---do-you-support-smsemailweb-notifications"></a>¿3. se admiten las notificaciones de SMS, web y correo electrónico?
Notificación Hubs está diseñada principalmente para enviar notificaciones a aplicaciones móviles con las plataformas enumeradas anteriormente. Nos aún no proporciona la capacidad para enviar correo electrónico o alertas SMS; Sin embargo, plataformas de terceros que proporcionan estas capacidades pueden integrarse junto con Hubs de notificación para enviar notificaciones de inserción nativa mediante [Aplicaciones móviles de Azure].

Notificación Hubs también no proporciona una inserción en explorador notificación entrega servicio-de-predefinidas. Los clientes pueden elegir implementar esto mediante SignalR sobre las plataformas de servidor compatibles. Si está buscando para enviar notificaciones a aplicaciones de explorador en el espacio aislado de Chrome, consulte el [tutorial de aplicaciones de Chrome].

###<a name="4---what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4. ¿Cuál es la relación entre las aplicaciones móviles de Azure y Azure Hubs de notificación y cuándo se usa qué?
Si tiene una aplicación móvil existente, back-end y desea agregar la capacidad de enviar notificaciones de inserción puede usar Hubs de notificación de Azure. Si desea configurar la aplicación móvil de back-end desde cero debe considerar usar aplicaciones móviles de Azure. Una aplicación móvil de Azure aprovisiona automáticamente un concentrador de notificación para poder enviar notificaciones de inserción fácilmente desde la aplicación móvil de back-end. Precios para las aplicaciones móviles de Azure incluyen los cargos de bases de un concentrador de notificación y solo paga cuando vaya más allá de la Push incluidos. Obtener más detalles sobre los costos están disponibles en la página de [Precios de servicio de aplicación] .

###<a name="5---how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5. ¿cuántos dispositivos puedo admiten si envío notificaciones de inserción a través de notificación Hubs?
Consulte la página de [Notificación Hubs precios] para obtener más información sobre el número de dispositivos compatibles.

Para determinados escenarios, si necesita soporte para dispositivos registrados más de 10.000.000, póngase [en contacto con nosotros](https://azure.microsoft.com/overview/contact-us/) directamente y se le ayudará escalar su solución.

###<a name="6---how-many-push-notifications-can-i-send-out"></a>6. ¿cuántas notificaciones de inserción pueden enviar?
Según el nivel seleccionado, Azure ajusta automáticamente hacia arriba en función del número de notificaciones que fluye a través del sistema.

>[AZURE.NOTE] El costo de uso general puede ir hacia arriba en función del número de notificaciones de inserción atendiendo. Asegúrese de tener en cuenta los límites de nivel existente que se indica en la página de [Precios de Hubs de notificación] .

Los clientes existentes están usando Hubs de notificación para enviar millones de notificaciones de inserción diariamente. No tiene que hacer nada especial para ajustar la inserción notificaciones alcance mientras usa Hubs de notificación de Azure.

###<a name="7---how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7. ¿cuánto tiempo tarda para enviar notificaciones notificaciones para llegar a mi dispositivo?
Azure Hubs de notificación es capaz de procesar al menos **1 millón de notificación de inserción envía un minuto** en normal usar escenario donde la carga entrante es bastante coherente y no spikey de naturaleza. Esta tasa puede variar según el número de etiquetas, la naturaleza de envíos entrantes y otros factores externos.

Durante el tiempo de entrega estimado, el servicio es capaz de calcular los destinos por plataforma y enrutar mensajes a los servicios de entrega de notificación de inserción respectivos basados en las expresiones de etiquetas o etiqueta registrados. Desde aquí en, es la responsabilidad de los servicios de notificaciones Push (PNS) para enviar la notificación al dispositivo.

Un PNS no garantiza ningún SLA para entregar notificaciones; Sin embargo, normalmente una gran mayoría de las notificaciones de inserción se entregarán a dispositivos de destino en unos minutos (normalmente dentro del límite de 10 minutos) desde el momento en que se envían a nuestra plataforma. Es posible que algunos valores atípicos que pueden tardar más tiempo.

>[AZURE.NOTE] Azure Hubs de notificación tiene una directiva en el lugar que ocupa las notificaciones de inserción que no puede se ha entregado el PNS en 30 minutos. Este retraso puede haber varias razones, más comúnmente porque la PNS es limitación de la aplicación.

###<a name="8---is-there-any-latency-guarantee"></a>8. ¿hay ninguna garantía de latencia?
Debido a la naturaleza de notificaciones de inserción (se entregarán por un servicio de notificación de inserción externa, específica de la plataforma), no hay ninguna garantía de latencia. Normalmente, la mayoría de las notificaciones de inserción obtener entregado dentro de un par de minutos.

###<a name="9---what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9. ¿Cuáles son las consideraciones que debo tener en cuenta al diseñar una solución con espacios de nombres y notificación Hubs?

####<a name="mobile-appenvironment"></a>Entorno o aplicación móvil

* Debe haber un concentrador de notificación por la aplicación móvil, por entorno.
* En un escenario de varios inquilino, cada inquilino debería tener un concentrador independiente.
* Nunca deben compartir el mismo concentrador de notificación entre entornos de prueba y de producción ya que puede causar problemas de la línea al enviar notificaciones. Por ejemplo, Apple ofrece extremos de recinto de seguridad y notificaciones de producción con cada uno con credenciales separadas.
* De forma predeterminada, puede enviar notificaciones de prueba a los dispositivos registrados a través del Portal de Azure o el componente integrado Azure en Visual Studio. El umbral se establece en 10 dispositivos que se seleccionan al azar del grupo de registro.

>[AZURE.NOTE] Si el concentrador configuró originalmente con un certificado de recinto de seguridad de Apple y, a continuación, volver a configurar para usar un certificado de producción de Apple, los símbolos de dispositivo antiguo ¿se convierten en no válidos con el nuevo certificado y provocar inserta un error. Es mejor separar su producción y entornos de prueba y utilizar hubs diferentes para diferentes entornos.

####<a name="pns-credentials"></a>Credenciales PNS

Cuando una aplicación móvil está registrada con el portal de programadores de la plataforma (por ejemplo, Apple o Google etcetera), a continuación, se Obtén una aplicación tokens identificador y seguridad que necesita una aplicación de back-end para proporcionar a los servicios de notificación de inserción de la plataforma para poder enviar notificaciones de inserción a los dispositivos. Estos tokens de seguridad que pueden ser en forma de certificados (por ejemplo, para Apple iOS o Windows Phone) o claves de seguridad (Google Android, Windows etcetera) deben configurarse en Hubs de notificación. Esto se hace normalmente en el nivel de concentrador de notificación, pero también puede hacerse en el nivel de espacio de nombres en un escenario de varios inquilino.

####<a name="namespaces"></a>Espacios de nombres

Espacios de nombres pueden usarse para la agrupación de implementación.  También puede usarse para representar todos los Hubs de notificación para todos los inquilinos de la misma aplicación en el escenario de varios inquilino.

####<a name="geo-distribution"></a>Distribución de geo

Geo distribución no siempre es importante en escenarios de notificación de inserción. Es a tener en cuenta que los diversos servicios de notificación de inserción (APN, GCM etcetera), que finalmente entregar las notificaciones de inserción a los dispositivos, no son uniformemente distribuido uno.

Si tiene una aplicación que se usa en todo el mundo, puede crear varios hubs en espacios de nombres distintos aprovechar la disponibilidad del servicio de notificación Hubs en diferentes regiones Azure todo el mundo.

>[AZURE.NOTE] Esto aumentará el costo de administración - especialmente alrededor de registros, por lo que esto realmente no se recomienda y solo debe hacerse si hay una necesidad explícita.

###<a name="10--should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>¿10. debo hacer registros del servidor de aplicación o directamente a través de cliente dispositivos?
Registros del servidor de aplicación son útiles cuando tiene que realizar la autenticación del cliente antes de crear el registro o cuando tiene etiquetas que deben ser creadas o modificadas por la aplicación back-end basado en alguna lógica de aplicación. Para obtener más información, puede leer más en las páginas de [instrucciones de registro de back-end] y [back-end registro orientación - 2] .

###<a name="11--what-is-the-push-notification-delivery-security-model"></a>11. ¿Qué es el modelo de seguridad de entrega de notificación de inserción?
Azure Hubs de notificación de usar una [Firma de acceso compartido (SA)](../storage/storage-dotnet-shared-access-signature-part-1.md)-según el modelo de seguridad. Puede usar los símbolos de asociaciones de seguridad en el nivel de espacio de nombres de raíz o en el nivel de notificación Hubs granular. Estos tokens SA puede establecerse con las reglas de autorización diferente, por ejemplo, enviar mensaje permisos, escuchar permisos de notificación etcetera. Más detalles están disponibles en el documento de [modelo de seguridad de NH] .

###<a name="12--how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12. ¿debo ¿cómo se puede controlar la carga confidencial en las notificaciones de inserción?
Todas las notificaciones se envían a dispositivos de destino por servicios de notificación de inserción de la plataforma (PNS). Cuando un remitente envía una notificación a Azure notificación Hubs nos procesar y pasar la notificación a la PNS respectivos.

Todas las conexiones del remitente a los centros de las notificaciones de Azure y luego a la PNS usan HTTPS.

>[AZURE.NOTE] Azure Hubs notificaciones no registra la carga del mensaje de ninguna forma.

Para enviar cargas confidenciales sin embargo, se recomienda un modelo de inserción seguro donde el remitente ofrece una notificación 'ping' con un identificador de mensaje al dispositivo sin la carga confidencial y cuando la aplicación en el dispositivo recibe esta carga, es posible llamar a una API segura directamente para capturar los detalles del mensaje. Una guía sobre cómo implementar el patrón descrito anteriormente está disponible en la página [NH - seguro Push tutorial] .

##<a name="operations"></a>Operaciones
###<a name="1---what-is-the-disaster-recovery-dr-story"></a>1. ¿Qué es la historia de recuperación de desastres (DR)?
Le proporcionamos cobertura de recuperación de metadatos en nuestra final (nombre del concentrador de notificación, cadena de conexión y otra información crítica). Cuando se activa un escenario de DR, los datos de los registros están el **segmento** de la infraestructura de Hubs de notificación que se perderá. Debe implementar una solución para volver a rellenar estos datos en la recuperación de concentrador posteriores a la nueva.

- *Paso 1* : crear un concentrador de notificación secundario en un centro de datos diferente. Puede crear sobre la marcha en el momento del evento DR o puede crear uno desde el panel get ir. No tiene mucha una diferencia de qué opción elegir porque el aprovisionamiento de concentrador de notificación es un proceso relativamente rápido en el orden de unos pocos segundos. Si dispone de un desde el principio para proteger desde el evento de DR que afectan a las capacidades de administración, por lo que es la opción es muy recomendable.

- *Paso 2* - hidrato el Hub de notificación secundario con los registros del concentrador de notificación principal. No se recomienda para mantener los registros en ambos hubs e intenta mantener sincronizados sobre la marcha como registros vienen-normalmente que no ha podido encontrarlas a causa de su tendencia inherente de registros para que expire en el lado PNS. Notificación Hubs limpiarlos como recibimos comentarios PNS sobre registros expirados o no válidos.  

Se recomienda usar una aplicación de back-end que:

- Mantiene un conjunto determinado de registros en su extremo de modo que puede realizar una inserción masiva en el hub de notificación secundaria en caso de DR

**OR**

- Obtiene normal descarga de registros del concentrador principal como una copia de seguridad de y una masa inserte en la NH secundario.

>[AZURE.NOTE] Funcionalidad de importación o exportación de registros disponible en nivel estándar se describe en el documento de [Importación o exportación de registros] .

Si no tiene un back-end, a continuación, cuando se inicia la aplicación en cualquiera de los dispositivos de destino, realizará un registro nuevo en el Hub de notificación secundario y finalmente secundaria notificación concentrador tendrán todos los dispositivos activos registrados.

La desventaja es que haya un período de tiempo cuando dispositivos donde aplicaciones no ha abierto el no reciben notificaciones.

###<a name="2---is-there-any-audit-log-capability"></a>2. ¿hay ninguna capacidad de registro de auditoría?
Todas las operaciones de administración de notificación Hubs vaya a registros de las operaciones que se exponen en el [Portal de Azure clásico].

##<a name="monitoring--troubleshooting"></a>Supervisión y solución de problemas
###<a name="1---what-troubleshooting-capabilities-are-available"></a>1. ¿Qué capacidades de solución de problemas están disponibles?
Azure Hubs de notificación proporciona varias características para realizar una solución de problemas comunes, especialmente en el caso más frecuente alrededor de notificaciones colocadas. Ver detalles en nuestra [NH - solución de problemas] notas del producto.

###<a name="2---what-telemetry-features-are-available"></a>2. ¿qué características de telemetría están disponibles?
Habilita la notificación Hubs Azure ver los datos de telemetría en el [Portal de Azure clásica]. Detalles de las mediciones disponibles están disponibles en la página [NH - métricas] .

>[AZURE.NOTE] Notificaciones correctas solo significan que se han entregado las notificaciones de inserción al servicio de notificación de inserción externo (por ejemplo, APN para Apple, GCM para Google etcetera). Es el PNS de ofrecer la notificación a dispositivos de destino. Normalmente, la PNS no exponen métricas de entrega a terceros.  

También le proporcionamos la capacidad para exportar los datos de telemetría mediante programación (en nivel **estándar** ). Vea la [NH - muestra métricas] para obtener información detallada.

[Portal de clásico de Azure]: https://manage.windowsazure.com
[Precios de Hubs de notificación]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notificación Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - tiempos de Seattle]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - API REST]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - tutoriales de introducción]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Tutorial de aplicaciones de Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Guía de registro de back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Guía de registro de back-end - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modelo de seguridad de NH]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - tutorial de inserción seguro]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - solución de problemas]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - métricas]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - ejemplo de indicadores]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Importación o exportación de registros]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[ejemplos de completado]: https://github.com/Azure/azure-notificationhubs-samples
[Aplicaciones móviles de Azure]: https://azure.microsoft.com/en-us/services/app-service/mobile/
[Precios de aplicación de servicio]: https://azure.microsoft.com/en-us/pricing/details/app-service/
