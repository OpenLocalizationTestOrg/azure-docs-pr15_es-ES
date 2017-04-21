<properties 
    pageTitle="Notificación de Azure Hubs - directrices de diagnóstico" 
    description="Instrucciones sobre cómo diagnosticar problemas comunes con Hubs de notificación de Azure." 
    services="notification-hubs" 
    documentationCenter="Mobile" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs---diagnosis-guidelines"></a>Azure notificación Hubs - directrices de diagnóstico

##<a name="overview"></a>Información general

Una de las preguntas más frecuentes se oír de los clientes de Azure notificación Hubs es cómo averiguar por qué no verán una notificación que se envía desde su aplicación de back-end aparecen en el dispositivo cliente - dónde y por qué se han quitado las notificaciones y procedimientos para la corrección. En este artículo se pasarán por los diversos motivos por qué notificaciones pueden quitar o no terminan en los dispositivos. También se tratará a través de la manera en que puede analizar y averiguar la causa. 

En primer lugar, es fundamental comprender cómo se inserta Hubs de notificación de Azure notificaciones a los dispositivos.
![][0]

En un flujo de notificación enviar típico, el mensaje se envía desde la **aplicación back-end** a **Concentrador de notificación de Azure (NH)** que a su vez realiza algún procesamiento en todos los registros teniendo en cuenta la etiquetas configuradas y expresiones de etiqueta para determinar "destinos", es decir, todos los registros que necesita recibir la notificación de inserción. Estos registros pueden abarcar alguna o todas nuestras plataformas compatibles - iOS, Google, Windows, Windows Phone, Kindle y Baidu para Android en China. Una vez que se establecen los objetivos, NH, a continuación, notificaciones, dividir entre varios lotes de los registros, la plataforma de dispositivo específica **Servicio de notificación de inserción (PNS)** - por ejemplo, APN para Apple, GCM para etcetera de Google. NH autentica con la PNS respectivos basándose en las credenciales que se establece en el Portal de clásico de Azure en la página Configurar concentrador de notificación. La PNS reenvía las notificaciones de los correspondientes **dispositivos cliente**. Esta es la plataforma recomendada forma de entregar notificaciones de inserción y observe que la sección final de la entrega de notificaciones lleve a cabo entre la plataforma PNS y el dispositivo. Por lo tanto, tenemos cuatro componentes principales: *cliente*, *aplicación back-end*, *Hubs de notificación de Azure (NH)* y *Servicios de notificación de inserción (PNS)* y cualquiera de estas acciones puede provocar notificaciones perdiendo la conexión. Más detalles sobre esta arquitectura está disponible en la [Información general de Hubs de notificación].

Error al entregar notificaciones puede ocurrir durante el ensayo/prueba inicial que puede indicar un problema de configuración de la fase o puede ocurrir en producción donde pueden obtener los todas o algunas de las notificaciones colocadas que indica que alguna aplicación más profundo o mensajería problema de trama. En la sección a continuación veremos varios escenarios de notificaciones colocadas comprendido entre comunes del tipo menos, que algunos de los cuales es posible obvios y otros no tanto. 

##<a name="azure-notifications-hub-mis-configuration"></a>Configuración incorrecta de Azure concentrador de notificaciones 

Azure Hubs notificación debe autenticarse en el contexto de la aplicación del desarrollador para poder enviar notificaciones correctamente el PNS respectivos. Esto se hace posible por el programador crear una cuenta de desarrollador con la plataforma respectiva (Google, Apple, Windows etcetera) y, a continuación, registrar su aplicación dónde obtener credenciales que se deben configurar en el portal de la sección de configuración de Hubs de notificación. Si ninguna notificación logran a través de, el primer paso debe ser para asegurarse de que están configuradas las credenciales correctas en el Hub de notificación de coincidencia de con la aplicación creada en su cuenta de programador específicas de plataforma. Encontrará los [Tutoriales de Introducción introducción] útiles para ir a través de este proceso de manera paso a paso. Estas son algunas configuraciones comunes errónea:

1. **General**
 
    a) hacer que su nombre de concentrador de notificación (sin errores tipográficos) es la misma:

    - ¿Dónde se va a registrar desde el cliente 
    - Que desea enviar notificaciones del servidor  
    - Cuando haya configurado las credenciales PNS y 
    - Cuyas credenciales SA ha configurado en el cliente y el back-end. 
        
    b) hacer asegurarse de que está utilizando las cadenas de configuración de SA correctas en el cliente y el servidor de la aplicación. Como regla general, debe estar usando el **DefaultListenSharedAccessSignature** en el cliente y **DefaultFullSharedAccessSignature** en el servidor de la aplicación (que le da permiso para poder enviar una notificación a la NH)

2. **Configuración del servicio de notificación de inserción de Apple (APN)**
 
    Debe mantener dos hubs diferentes: uno para producción y otro para realizar pruebas de uso. Esto significa cargar el certificado que se va a usar en el entorno de recinto de seguridad a un concentrador independiente y el certificado que se va a usar en producción a un concentrador independiente. No intente cargar al mismo concentrador distintos tipos de certificados puede producir errores de notificación hacia abajo de la línea. Si se encuentra en una posición donde ha cargado accidentalmente diferentes tipos de certificado al mismo concentrador, se recomienda eliminar el concentrador y empezar desde cero. Si por algún motivo, no puede eliminar el concentrador, a continuación, al menos, debe eliminar todos los registros existentes del concentrador. 

3. **Configuración de mensajería de nube de Google (GCM)** 

    una) realizar seguro que habilita "Google Cloud mensajería para Android" en el proyecto de la nube. 
    
    ![][2]
    
    b) asegúrese de, cree una clave de"servidor" mientras obtener las credenciales qué NH usarán para autenticar con GCM. 
    
    ![][3]
    
    c) hacer Compruebe que ha configurado "Id. de proyecto" en el cliente que es una entidad totalmente numérica que puede obtener en el panel:
    
    ![][1]

##<a name="application-issues"></a>Problemas de aplicaciones

1) **Las etiquetas o expresiones de etiqueta**

Si está utilizando etiquetas o expresiones de etiqueta segmentar la audiencia, siempre es posible que cuando envíe la notificación, no hay ningún destino que se encuentra en función de las expresiones de etiquetas o etiqueta que se especifica en la llamada de envío. Es mejor revisar los registros para asegurarse de que las etiquetas que coinciden al enviar notificación y, a continuación, compruebe la recepción de notificación solo de los clientes con los registros. Por ejemplo Si todos los registros con NH realizados con dicen "y política" de etiqueta y envía una notificación con la etiqueta "Deportes", no se enviarán a cualquier dispositivo. Un caso complejo puede implicar expresiones de etiqueta que se ha solo registrado con la "etiqueta A" o "Etiqueta B", pero al enviar notificaciones, se dirige a "Etiqueta A & & etiqueta B". En la sección diagnosticar los consejos siguiente, hay formas en que puede revisar los registros junto con las etiquetas tienen. 

2) **Problemas de plantilla**

Si está utilizando plantillas, asegúrese de que está siguiendo las instrucciones descritas en la [orientación de la plantilla]. 

3) **Registros no válidos**

Suponiendo que el concentrador de notificación está configurado correctamente y las expresiones de etiquetas o etiqueta se usaron correctamente es el resultado de la búsqueda de destinos válidos al que deben enviarse las notificaciones, se aplica NH desactivar varias secciones de procesamiento en paralelo - cada lote enviar mensajes a un conjunto de registros. 

> [AZURE.NOTE] Puesto que se realice el procesamiento en paralelo, no garantizamos el orden en que se van a entregar las notificaciones. 

Ahora concentrador de notificaciones de Azure está optimizado para un modelo de entrega de mensaje "en la mayoría de una vez". Esto significa que intentamos la duplicación para que no las notificaciones se entregarán más de una vez en un dispositivo. Para garantizar esto se revise los registros y asegúrese de que solo un mensaje se envía por identificador de dispositivo antes de enviar realmente el mensaje a la PNS. Como cada lote se envía a la PNS, que a su vez acepta y validar los registros, es posible que la PNS detecta un error con uno o varios de los registros en un lote, devuelve un error al NH Azure y deja de procesar, con ello, anulación de dicho lote por completo. Esto es cierto con APN que usa el protocolo de secuencia TCP. Aunque nos estamos optimizadas para en la mayoría de una vez que se entrega en este caso, quite el registro con errores de nuestra base de datos y vuelva a intentar la entrega de notificación para el resto de los dispositivos de dicho lote.

Puede obtener información de error para el intento de entrega no frente a un registro mediante las API de REST de Azure notificación Hubs: [por mensaje telemetría: obtener telemetría de mensaje de notificación](https://msdn.microsoft.com/library/azure/mt608135.aspx) y [PNS comentarios](https://msdn.microsoft.com/library/azure/mt705560.aspx). Consulte [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) por ejemplo, código.

##<a name="pns-issues"></a>Problemas PNS

Una vez que se ha recibido el mensaje de notificación por la PNS respectivos es su responsabilidad de ofrecer la notificación en el dispositivo. Azure Hubs de notificación está fuera de la imagen y no tiene ningún control cuando o si se va la notificación se ha entregado al dispositivo. Puesto que los servicios de notificación de la plataforma son bastante sólidos, notificaciones tiende a llegar a los dispositivos en pocos segundos de la PNS. Si el PNS sin embargo es limitación Hubs de notificación de Azure aplicar un fondo exponencial desactivar estrategia y si la PNS permanece inaccesible durante 30 minutos, a continuación, tenemos una directiva en lugar de caducar y soltar los mensajes permanentemente. 

Si un PNS intenta entregar una notificación, pero el dispositivo está desconectado, la notificación se almacena por el PNS durante un período de tiempo limitado y entregada al dispositivo cuando esté disponible. Se almacena solo una notificación reciente para una aplicación en particular. Si varias notificaciones se envían mientras el dispositivo está sin conexión, cada nueva notificación hace que la notificación anterior se descartan. Este comportamiento de mantener solo la notificación más reciente se conoce como unión notificaciones en APN y contraer en GCM (que usa una clave de contracción). Si el dispositivo permanece sin conexión por mucho tiempo, se descartan las notificaciones que se almacenaron para él. Origen - [Guía de APN] & [orientación GCM]

Con Azure Hubs de notificación - puede pasar una clave de uso combinada a través de un encabezado HTTP mediante el genérico `SendNotification` API (por ejemplo, para .NET SDK- `SendNotificationAsync`) que también tiene encabezados HTTP que se pasan como es el PNS respectivos. 

##<a name="self-diagnose-tips"></a>Diagnosticar automáticamente sugerencias

Aquí se examinar las distintas vías para diagnosticar y root producen problemas concentrador de notificación:

###<a name="verify-credentials"></a>Comprobar credenciales

1. **Portal de programadores PNS**

    Compruebe en el respectivo portal de desarrolladores PNS (APN, GCM, WNS etcetera) con los [Tutoriales de Introducción introducción].

2. **Portal de Azure clásico**

    Vaya a la ficha configurar para revisar y coincidir con las credenciales con las que se obtienen desde el portal de programadores PNS. 

    ![][4]

###<a name="verify-registrations"></a>Comprobar los registros

1. **Visual Studio**

    Si utiliza Visual Studio para el desarrollo puede conectarse a Microsoft Azure y ver y administrar un conjunto de Azure servicios incluidos concentrador de notificaciones desde el Explorador de"servidor". Esto es útil principalmente para su entorno de desarrollo o prueba. 

    ![][9]

    Puede ver y administrar todos los registros en su centro que están clasificados satisfactoriamente para plataforma, nativo o registro de la plantilla, las etiquetas, PNS identificador, id de registro y la fecha de vencimiento. También puede editar un registro sobre la marcha - que resulta útil Supongamos, por ejemplo, si desea modificar las etiquetas. 

    ![][8]
 
    > [AZURE.NOTE] Sólo se debe utilizar la funcionalidad de Visual Studio para editar registros durante el desarrollo o probar con un número limitado de registros. Si surge hay una necesidad de corregir los registros de forma masiva, considere la posibilidad de usar la funcionalidad de registro de importación o exportación aquí describe - [Registros de importación/exportación] (https://msdn.microsoft.com/library/dn790624.aspx)

2. **Explorador de Bus de servicio**

    Muchos clientes usan ServiceBus explorer describe aquí - [ServiceBus explorador] para ver y administrar su concentrador de notificación. Es un proyecto de código abierto disponible desde code.microsoft.com - [código ServiceBus Explorer]

###<a name="verify-message-notifications"></a>Comprobar las notificaciones de mensajes

1. **Portal de clásico de Azure**

    Puede ir a la pestaña "Depuración" enviar notificaciones de prueba a sus clientes sin necesidad de cualquier servicio de back-end hacia arriba y ejecutar. 

    ![][7]

2. **Visual Studio**

    También puede enviar notificaciones de prueba de las comodidades de Visual Studio:

    ![][10]

    Puede obtener más información sobre la funcionalidad de Visual Studio notificación concentrador Azure explorer aquí: 
    
    - [Información general sobre el Explorador de servidor de VS]
    - [Entrada de Blog del explorador de servidor de VS - 1]
    - [Entrada de Blog del explorador de servidor de VS - 2]

###<a name="debug-failed-notifications-review-notification-outcome"></a>Depurar notificaciones de error y revisar los resultados de notificación

**EnableTestSend (propiedad)**

Cuando se envía una notificación a través de Hubs de notificación, inicialmente simplemente obtiene en la cola para que NH hacer procesamiento para calcular todos los destinos y finalmente NH envía a la PNS. Esto significa que cuando se usa la API de REST o cualquiera de lo SDK de cliente, el retorno de la llamada de envío correcta solo significa que el mensaje ha sido correctamente en la cola con concentrador de notificación. No le da una perspectiva de ¿qué ha ocurrido cuando NH finalmente obtuvo enviar el mensaje a PNS. Si la notificación no llega en el dispositivo cliente, es posible que cuando trata de NH entregar el mensaje a PNS, se ha producido un error, por ejemplo, el tamaño de carga supera el máximo permitido por la PNS o las credenciales configuradas en NH etcetera no válido. Para obtener una información detallada sobre los errores PNS, hemos incluido una propiedad denominada [EnableTestSend característica]. Esta propiedad se habilita automáticamente al enviar mensajes de prueba desde el portal o el cliente de Visual Studio y, por tanto, le permite ver la información de depuración detallada. Puede usar esta a través de las API de tomar notas en el ejemplo de .NET SDK donde está disponible ahora y se agregará a todos los SDK de cliente finalmente. Para usar esta opción con la llamada del resto, agregar un parámetro de cadena de consulta denominado, por ejemplo, "probar" al final de la llamada de envío 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Ejemplo (SDK de .NET)*
 
Supongamos que va a usar .NET SDK para enviar una notificación nativa:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State`le simplemente estado `Enqueued` al final de la ejecución sin ninguna información sobre qué ha ocurrido con la inserción. Ahora puede usar el `EnableTestSend` propiedad booleana mientras iniciando el `NotificationHubClient` y puede obtener estado detallado sobre los errores PNS encontró al enviar la notificación. La llamada de enviar a continuación tarda más tiempo para devolver porque solo devuelve después NH ha entregado la notificación a PNS para determinar el resultado. 
 
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
    
    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);
    
    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Resultados de ejemplo*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
Este mensaje indica alguno credenciales no válidas están configuradas en el hub de notificación o un problema con los registros del concentrador y el curso recomendado sería eliminar este registro y permitir que el cliente de volver a crearlo antes de enviar el mensaje. 
 
> [AZURE.NOTE] Observe que el uso de esta propiedad está limitado intensamente y debe sólo utilizar este entorno de desarrollo o prueba con un conjunto limitado de registros. Solo se enviar notificaciones de depuración 10 dispositivos. También tenemos un límite de procesamiento de depuración envía 10 por minuto. 

###<a name="review-telemetry"></a>Revisar telemetría 

1. **Usar Azure Portal clásica**

    El portal permite obtener una descripción general de todas las actividades en su centro de notificación. 
    
    un) desde la ficha "tablero" puede ver una vista de registros, notificaciones, así como errores por plataforma agregada. 
    
    ![][5]
    
    b) también puede agregar muchas otras métricas específicas de plataforma en la pestaña "Monitor" para echar un vistazo más profundo, especialmente en los errores específicos de PNS devuelve cuando NH intenta enviar la notificación a la PNS. 
    
    ![][6]
    
    c) debe comenzar con la revisión de las **Notificaciones correctas** de **Los mensajes entrantes**, **Las operaciones de registro**, y, a continuación, vaya a cada pestaña de la plataforma para revisar los errores específicos de PNS. 
    
    d) si tiene el hub de notificación mal configurado con la configuración de autenticación verá PNS Error de autenticación. Esta es una buena indicación para comprobar las credenciales PNS. 

2) **Acceso mediante programación**

Más información sobre aquí: 

- [Acceso de telemetría mediante programación]
- [Acceso de telemetría a través de ejemplo de la API] 

> [AZURE.NOTE] Características relacionadas con varios telemetría como **Registros de importación o exportación**, **Acceso de telemetría a través de la API** etcetera solo están disponibles en nivel estándar. Si intenta usar estas características si se encuentra en libre o nivel básico obtendrá el mensaje de excepción a este efecto mientras utiliza el SDK y un HTTP 403 (prohibido) para usarlas directamente desde las API de REST. Asegúrese de que se han movido hasta estándar de nivel a través del Portal de Azure clásico.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[Información general de Hubs de notificación]: notification-hubs-push-notification-overview.md
[Tutoriales de introducción]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Guía de plantilla]: https://msdn.microsoft.com/library/dn530748.aspx 
[Guía de APN]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[Guía de GCM]: http://developer.android.com/google/gcm/adv.html
[Registros de importación/exportación]: http://msdn.microsoft.com/library/dn790624.aspx
[Explorador de ServiceBus]: http://msdn.microsoft.com/library/dn530751.aspx
[Código de ServiceBus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Información general sobre el Explorador de servidor de VS]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Entrada de Blog del explorador de servidor de VS - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Entrada de Blog del explorador de servidor de VS - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[Característica EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Acceso de telemetría mediante programación]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Acceso de telemetría a través de ejemplo de la API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 