<properties
    pageTitle="Cómo enviar notificaciones programadas | Microsoft Azure"
    description="Este tema describe cómo utilizar notificaciones programada con Hubs de notificación de Azure."
    services="notification-hubs"
    documentationCenter=".net"
    keywords="notificaciones de inserción, notificación de inserción, programación de notificaciones de inserción"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="how-to-send-scheduled-notifications"></a>Cómo: Enviar notificaciones programadas


##<a name="overview"></a>Información general

Si tiene un escenario en el que desea enviar una notificación en algún momento en el futuro, pero no tiene una forma sencilla de activar el código de back-end para enviar la notificación. Notificación Hubs de nivel estándar es compatible con una característica que le permite programar las notificaciones de 7 días en el futuro.

Cuando se envía una notificación, simplemente use la clase [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) en el SDK de Hubs notificación tal como se muestra en el ejemplo siguiente:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Además, puede cancelar una notificación programada anteriormente mediante su notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

No existen límites en el número de notificaciones programadas que puede enviar.