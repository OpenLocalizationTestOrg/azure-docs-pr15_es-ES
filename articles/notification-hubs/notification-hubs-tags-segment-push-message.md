<properties
    pageTitle="Enrutamiento y expresiones de etiqueta"
    description="Este tema explica el enrutamiento y etiqueta expresiones para hubs de notificación de Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="routing-and-tag-expressions"></a>Expresiones de enrutamiento y la etiqueta

##<a name="overview"></a>Información general

Expresiones de etiqueta habilitar conjuntos específicos de destino de dispositivos, o más concretamente registros, cuando se envía una notificación de inserción a través de Hubs de notificación.


## <a name="targeting-specific-registrations"></a>Identificación de registros específicos

La única manera de destino notificación específica registros es asociar etiquetas a ellos, a continuación, destinar las etiquetas. Como se describe en [Administración de registro](notification-hubs-push-notification-registration-management.md), para recibir notificaciones de inserción una aplicación tiene que registrar un controlador de dispositivo en un concentrador de notificación. Una vez que se crea un registro en un concentrador de notificación, el servidor de la aplicación puede enviar notificaciones de inserción.
El servidor de la aplicación puede elegir los registros de destino con una notificación específica de las siguientes maneras:

1. **Difusión**: todos los registros en el hub de notificación recibirán la notificación.
2. **Etiqueta**: todos los registros que contienen la etiqueta especificada recibirán la notificación.
3. **Expresión de etiqueta**: todos los registros cuyo conjunto de etiquetas que coincida con la expresión especificada recibirán la notificación.

## <a name="tags"></a>Etiquetas

Una etiqueta puede ser cualquier cadena de caracteres hasta 120, que contiene alfanuméricos y los siguientes caracteres no alfanuméricos: '_' ‘@’, '#', '. ',':', '-'. En el ejemplo siguiente se muestra una aplicación desde la que puede recibir notificaciones de aviso acerca de los grupos de música específico. En este escenario, registros de etiqueta con etiquetas que representan las bandas diferentes, como se muestra en la siguiente imagen es una forma sencilla de notificaciones de la ruta.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

En esta imagen, el mensaje etiquetado **Beatles** llega tablet registrado con la etiqueta **Beatles**.

Para obtener más información sobre la creación de registros de etiquetas, vea [Administración de registro](notification-hubs-push-notification-registration-management.md).

Puede enviar notificaciones a etiquetas mediante los métodos de notificaciones de envío de la `Microsoft.Azure.NotificationHubs.NotificationHubClient` clase en [Microsoft Azure notificación Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. También puede utilizar Node.js o las API de REST de notificaciones de inserción.  Aquí tenemos un ejemplo con el SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Las etiquetas no tienen que ser previamente preparación y pueden hacer referencia a varias conceptos específicos de la aplicación. Por ejemplo, los usuarios de esta aplicación de ejemplo pueden comentar bandas y desea recibir brindis, no solo para los comentarios en sus favoritos bandas, sino también para todos los comentarios de sus amigos, independientemente de la banda en el que se comentarios. En la siguiente imagen se muestra un ejemplo de este escenario:



![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

En esta imagen, Ana está interesada en actualizaciones de los Beatles y Bob está interesado en actualizaciones de la Wailers. Bob también está interesado en comentarios de Carlos y Carlos están en la Wailers le interesa. Cuando se envía una notificación de comentario de Carlos en los Beatles, Ana y Bob recibirlo.

Aunque puede codificar preocupaciones varias etiquetas (por ejemplo, "band_Beatles" o "follows_Charlie"), etiquetas son cadenas simples y no propiedades con valores. Un registro coincide únicamente en la presencia o ausencia de una etiqueta específica.

Para obtener un tutorial paso a paso completa sobre cómo usar etiquetas para enviar a los grupos de interés, vea [Último noticias](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).


## <a name="using-tags-to-target-users"></a>Usar etiquetas para los usuarios de destino

Es otra forma de usar etiquetas identificar todos los dispositivos de un usuario concreto. Los registros se pueden etiquetar con una etiqueta que contiene un identificador de usuario, como se muestra en la siguiente imagen:


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

En esta imagen, el uid:Alice mensaje etiquetado llega a todos los registros uid:Alice etiquetado; por lo tanto, todos los dispositivos de Ana.


##<a name="tag-expressions"></a>Expresiones de etiqueta

Hay casos en los que se tiene una notificación a un conjunto de registros que se identifica no por una sola etiqueta, pero por una expresión booleana en etiquetas de destino.

Supongamos que envía un aviso a todos los usuarios de San Cristóbal sobre un juego entre la Red Sox y Cardinals aplicación deportes. Si la aplicación cliente registra etiquetas de interés en equipos y la ubicación, debería estar dirigida a todos los usuarios de San Cristóbal que está interesado en Red Sox o la Cardinals la notificación. Esta condición se puede expresar con la siguiente expresión booleana:

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Expresiones de etiqueta pueden contener todos los operadores booleanos, como AND (& &), o (|) y no (!). También pueden contener paréntesis. Expresiones de etiqueta están limitadas a 20 etiquetas si contienen solo or; en caso contrario, están limitados a 6 etiquetas.

Aquí tenemos un ejemplo para enviar notificaciones con expresiones de etiqueta mediante el SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)"; 

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
