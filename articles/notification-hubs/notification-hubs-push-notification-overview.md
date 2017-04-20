<properties
    pageTitle="Notificación de Azure Hubs"
    description="Obtenga información sobre cómo usar las notificaciones de inserción en Azure. Ejemplos de código escritos en C# con la API de .NET."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="multiple"
    ms.devlang="multiple"
    ms.topic="hero-article"
    ms.date="08/25/2016"
    ms.author="yuaxu"/>


#<a name="azure-notification-hubs"></a>Notificación de Azure Hubs

##<a name="overview"></a>Información general

Azure Hubs de notificación proporcionar una infraestructura de inserción de fácil de usar, varias plataformas y salida de la escala que le permiten enviar notificaciones de inserción móvil desde cualquier back-end (en la nube o local) para cualquier plataforma móvil.

Con Hubs de notificación, puede enviar fácilmente las notificaciones de inserción de varias plataformas, personalizada, condensar los detalles de los sistemas de notificación de plataforma diferente (PNS). Con una sola llamada API, se pueden destinar usuarios individuales o segmentos de todos los destinatarios que contienen millones de usuarios, a través de todos sus dispositivos.

Puede usar Hubs de notificación para escenarios empresariales y para consumidores. Por ejemplo:

- Enviar notificaciones de noticias de más reciente a millones con baja latencia (notificación Hubs potencia aplicaciones de Bing preinstaladas en todos los dispositivos de Windows y Windows Phone).
- Enviar cupón según ubicación segmentos de usuario.
- Enviar notificaciones de eventos a los usuarios o grupos de finanzas/deportes o juegos las aplicaciones.
- Notificar a los usuarios de eventos de la empresa como nuevos mensajes y correos electrónicos y clientes potenciales.
- Enviar un-: contraseñas necesario para la autenticación multifactor.



##<a name="what-are-push-notifications"></a>¿Qué son las notificaciones de inserción?

Smartphones y tabletas pueden "notificar" usuarios cuando se ha producido un evento. Estas notificaciones pueden adoptar muchas formas.

En las aplicaciones de la tienda de Windows y Windows Phone, puede ser la notificación en el formulario de _brindar_: aparece una ventana no modal, con un sonido para indicar una nueva notificación. Otros tipos de notificación que son compatibles con incluyen notificaciones de _mosaico_, _sin formato_y el _distintivo_ . Para obtener más información sobre los tipos de notificaciones compatibles en dispositivos de Windows, vea [mosaicos, distintivos y las notificaciones](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx).

En dispositivos de Apple iOS, la inserción del mismo modo notifica que el usuario con un cuadro de diálogo que solicita el usuario para ver o cerrar la notificación. Haga clic en **vista** , se abre la aplicación que recibe el mensaje. Para obtener más información sobre las notificaciones de iOS, consulte [iOS notificaciones](http://go.microsoft.com/fwlink/?LinkId=615245).

Notificaciones de inserción ayudan a dispositivos móviles mostrar información nueva mientras sigue siendo eficiencia energética. Las notificaciones pueden enviarse por sistemas de back-end para dispositivos móviles, incluso cuando no están activas las aplicaciones correspondientes en un dispositivo. Notificaciones de inserción son un componente fundamental para las aplicaciones de consumidor, donde se usan para aumentar el uso y compromiso de aplicación. Las notificaciones también son útiles para empresas, cuando información actualizada aumenta la capacidad de respuesta de empleado a los eventos de la empresa.

Algunos ejemplos específicos de escenarios de contratación móvil son:

1.  Actualizar un mosaico en Windows 8 o Windows Phone con información financiera actual.
2.  Alertas de un usuario con un aviso que algunos elementos de trabajo se ha asignado a dicho usuario, en una aplicación empresarial basada en el flujo de trabajo.
3.  Mostrar un distintivo con el número de ventas actuales de clientes potenciales en una aplicación CRM (como Microsoft Dynamics CRM).

##<a name="how-push-notifications-work"></a>¿Cómo trabajo notificaciones de inserción

Notificaciones de inserción se entregarán a través de infraestructuras específicas de la plataforma denominadas _Sistemas de notificación de la plataforma_ (PNS). Un PNS ofrece funciones básica (es decir, sin compatibilidad con difusión, personalización) y no tienen ninguna interfaz comunes. Por ejemplo, para enviar una notificación a una aplicación de la tienda de Windows, un desarrollador debe ponerse en contacto con el WNS (servicio de notificación de Windows). Para enviar una notificación a un dispositivo iOS, el mismo programador tiene contacto APN (servicio de notificación de inserción de Apple) y enviar el mensaje una segunda vez. Ayuda de Azure hubs de notificación proporcionando una interfaz común, junto con otras características para admitir notificaciones de inserción a través de cada plataforma.

En un nivel alto, no obstante, todos los sistemas de notificación de plataforma siguen el mismo patrón:

1.  El PNS para recuperar su _controlador_de contactos de la aplicación de cliente. El tipo de identificador depende del sistema. Para WNS, es un URI o "canal de notificación". Para APN, es un símbolo.
2.  La aplicación cliente almacena este controlador en aplicación _back-end_ para uso posterior. WNS, back-end normalmente es un servicio de nube. Para Apple, el sistema se denomina un _proveedor_.
3.  Para enviar una notificación de inserción, el back-end de la aplicación se pone en contacto con la PNS con el controlador a una instancia de la aplicación cliente específico de destino.
4.  La PNS reenvía la notificación en el dispositivo especificado por el controlador.

![][0]

##<a name="the-challenges-of-push-notifications"></a>Los desafíos de notificaciones de inserción

Aunque estos sistemas son muy eficaces, aún abandonan cantidad de trabajo para el desarrollador de la aplicación para implementar escenarios de notificación de inserción incluso comunes, como difundir o enviar notificaciones de inserción a los usuarios segmentados.

Notificaciones de inserción son una de las características más solicitadas en servicios en la nube para aplicaciones móviles. El motivo es que la infraestructura necesaria para que funcionen es bastante complejo y principalmente relacionado con la lógica empresarial principal de la aplicación. Algunos de los desafíos de creación de una infraestructura de inserción a petición son:

- **Dependencia de la plataforma.** Para enviar notificaciones a dispositivos en distintas plataformas, se deben codificar varias interfaces en el back-end. No solo se muestran los detalles de bajo nivel diferente, pero la presentación de la notificación (mosaico, brindar o distintivo) también es depende de la plataforma. Estas diferencias pueden producir complejo y difícil de mantener el código de back-end.

- **Escala.** Escala esta infraestructura tiene dos aspectos:
    + Según directrices PNS, tokens de dispositivo deben actualizarse cada vez que se inicia la aplicación. Esto lleva a una gran cantidad de tráfico (y accesos consecutivas de la base de datos) para mantener actualizados los símbolos del dispositivo. Cuando se aumenta el número de dispositivos (posiblemente a millones), el coste de crear y mantener esta infraestructura es nonnegligible.

    + La mayoría de PNSs no admiten difusión en varios dispositivos. Sigue resultados de una difusión de millones de dispositivos en millones de llamadas a la PNSs. Poder escalar estas solicitudes es importantes, ya que normalmente los desarrolladores de la aplicación desean reducir la latencia total. Por ejemplo, el último dispositivo para recibir el mensaje no debería recibir la notificación de 30 minutos después de enviar las notificaciones, en muchos casos anulará el propósito para que las notificaciones de inserción.
- **Enrutamiento.** PNSs proporciona una manera de enviar un mensaje a un dispositivo. Sin embargo, en la mayoría de aplicaciones de notificaciones están destinadas a usuarios o grupos de interés (por ejemplo, todos los empleados asignados a una determinada cuenta de cliente). Por lo tanto, para enrutar las notificaciones para los dispositivos correctos, la aplicación back-end debe mantener un registro que asocia grupos de interés tokens del dispositivo. Esta sobrecarga agrega al tiempo total de los costos de mercado y el mantenimiento de una aplicación.

##<a name="why-use-notification-hubs"></a>¿Por qué usar notificación Hubs?

Notificación Hubs eliminan la complejidad: no tiene que administrar los desafíos de notificaciones de inserción. En su lugar, puede usar un concentrador de notificación. Notificación Hubs usan una infraestructura de notificación de inserción completa varias plataformas y escala horizontal y reducen considerablemente el código de inserción específico que se ejecuta en el servidor de la aplicación. Notificación Hubs implementan todas las funciones de una infraestructura de inserción. Dispositivos solo están responsables de registrar los controladores de PNS y el back-end es responsable de enviar mensajes independiente de la plataforma a los usuarios o grupos de interés, tal como se muestra en la siguiente ilustración:

![][1]


Hubs de notificación proporcionan una infraestructura de notificación de inserción de listos para usar con las siguientes ventajas:

- **Varias plataformas.**
    +  Compatibilidad con las principales plataformas de dispositivos móviles. Hubs de notificación pueden enviar notificaciones de inserción para las aplicaciones de la tienda Windows, iOS, Android y Windows Phone.

    +  Hubs notificación proporcionan una interfaz común para enviar notificaciones a todas las plataformas compatibles. Específica de la plataforma protocolos no son necesarios. El back-end de la aplicación puede enviar notificaciones en formatos específicos de la plataforma o independiente de la plataforma. La aplicación sólo se comunica con Hubs de notificación.

    +  Administración del controlador de dispositivos. Notificación Hubs mantiene el controlador de registro y los comentarios de PNSs.

- **Funciona con cualquier back-end**: nube o local,. NET, PHP, Java, nodo, etc..

- **Escala.** Notificación hubs escala millones de dispositivos sin necesidad de volver a diseñar o fragmentar.


- **Conjunto enriquecido de patrones de entrega**:

    - *Difusión*: permite la difusión cerca simultánea a millones de dispositivos con una sola llamada API.

    - *Unidifusión y multidifusión*: insertar en las etiquetas que representan los usuarios individuales, incluidas todas sus dispositivos; o más amplio grupo; Por ejemplo, factores de forma independiente (tablet frente a teléfono).

    - *Segmentación*: Push al segmento complejo definido por expresiones de etiqueta (por ejemplo, los dispositivos de Nueva York siguiendo los Yankees en compañía).

    Cada dispositivo, al enviar su controlador a un concentrador de notificación, puede especificar una o varias _etiquetas_. Para obtener más información acerca de [las etiquetas]. Etiquetas no debe proporcionarse previamente o eliminado. Etiquetas proporcionan una forma sencilla de enviar notificaciones a usuarios o grupos de interés. Dado que las etiquetas pueden contener cualquier identificador específico de la aplicación (por ejemplo, identificadores de usuario o grupo), su uso libera el back-end de aplicación de la carga de tener que almacenar y administrar los controladores de dispositivo.

- **Personalización**: cada dispositivo puede tener una o varias plantillas para lograr localización por dispositivo y personalización sin que ello afecte código back-end.

- **Seguridad**: compartido secreto de Access (SA) o autenticación federados.

- **Telemetría enriquecido**: disponible en el portal y mediante programación.


##<a name="integration-with-app-service-mobile-apps"></a>Integración con aplicaciones móviles de servicio de aplicaciones

Para facilitar una experiencia perfecta y unificar en servicios de Azure, [Aplicaciones de aplicación de servicio móvil] tiene compatibilidad integrada para las notificaciones de inserción con Hubs de notificación. [Aplicación de servicio móvil aplicaciones] ofrece una plataforma de desarrollo de aplicaciones móviles de alta escalabilidad, disponibles globalmente para programadores de empresa e integradores que recupera un amplio conjunto de capacidades a los desarrolladores de móviles.

Los programadores de aplicaciones móviles pueden utilizar Hubs de notificación con el flujo de trabajo siguiente:

1. Recuperar el controlador de dispositivo PNS
2. Registrar [plantillas] y dispositivo con notificación Hubs a través de API de registrar Mobile aplicaciones cliente SDK adecuado
    + Tenga en cuenta que aplicaciones Mobile quita todas las etiquetas en los registros por motivos de seguridad. Trabajar con notificación Hubs desde su back-end directamente a asociar etiquetas con dispositivos.
3. Enviar notificaciones de la aplicación de back-end con Hubs de notificación

Estas son algunas ventajas a los desarrolladores con esta integración:

- **SDK de clientes móviles de aplicaciones.** Estos SDK múltiples plataformas proporcionan API simples para el registro y póngase en contacto con el hub de notificación vinculado automáticamente hacia arriba con la aplicación móvil. Los desarrolladores no es necesario profundizar a través de credenciales de Hubs de notificación y trabajar con un servicio adicional.
    + Los SDK etiquetar automáticamente el dispositivo determinado con aplicaciones móviles de ID de usuario autenticado para habilitar la inserción de escenario de usuario.
    + Los SDK utilizan automáticamente el identificador de instalación de aplicaciones móviles como GUID para registrar con Hubs de notificación, guardar los desarrolladores el problema de mantener varios GUID de servicio.
    
- **Modelo de instalación.** Aplicaciones móviles funciona con el modelo de inserción más reciente de notificación Hubs para representar todas las propiedades de inserción asociadas con un dispositivo en una instalación de JSON que se alinea con los servicios de notificación de inserción y es fácil de usar.

- **Flexibilidad.** Siempre pueden elegir los desarrolladores trabajar con notificación Hubs directamente, incluso con la integración en su lugar.

- **Experiencia integrada en [el portal de Azure].** Insertar como una capacidad se representa visualmente en las aplicaciones móviles y los programadores pueden trabajar fácilmente con el concentrador de notificación asociados a través de aplicaciones móviles.



##<a name="next-steps"></a>Pasos siguientes

Puede encontrar más información acerca de la notificación Hubs en estos temas:

+ **[¿Cómo los clientes usan Hubs de notificación]**

+ **[Guías y tutoriales de Hubs de notificación]**

+ **Tutoriales de notificación Hubs Introducción** ([iOS], [Android], [Universal de Windows], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

.NET relevantes managed API referencias para las notificaciones de inserción se encuentran aquí:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [¿Cómo los clientes usan Hubs de notificación]: http://azure.microsoft.com/services/notification-hubs
  [Guías y tutoriales de Hubs de notificación]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Universal de Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [Aplicaciones de servicio de aplicaciones móviles]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [plantillas]: notification-hubs-templates-cross-platform-push-messages.md
  [Portal de Azure]: https://portal.azure.com
  [etiquetas]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)
