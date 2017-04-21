<properties
    pageTitle="Enviar notificaciones de distintas plataformas a los usuarios con notificación Hubs (ASP.NET)"
    description="Obtenga información sobre cómo usar las plantillas de notificación Hubs para enviar una solicitud de solo una notificación independiente de la plataforma destinada a todas las plataformas."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Enviar notificaciones de distintas plataformas a los usuarios con Hubs de notificación


En el tutorial anterior [usuarios notificar con Hubs de notificación], aprendió a notificaciones de inserción a todos los dispositivos registrados por un usuario autenticado concreto. En ese tutorial, tuvieron varias solicitudes para enviar una notificación a cada plataforma de cliente compatibles. Notificación Hubs admite plantillas, que le permiten especificar el modo en que desea recibir notificaciones de un dispositivo específico. Esto simplifica el envío de notificaciones de varias plataformas. Este tema muestra cómo sacar partido de plantillas para enviar una solicitud de solo una notificación independiente de la plataforma destinada a todas las plataformas. Para obtener más información sobre las plantillas, vea [Introducción a Azure notificación Hubs][Templates].

> [AZURE.NOTE] Notificación Hubs permite un dispositivo para registrar varias plantillas con la misma etiqueta. En este caso, un mensaje entrante que se dirige a esa etiqueta el resultado de varias notificaciones entregadas el dispositivo, uno por cada plantilla. Esto le permite mostrar el mismo mensaje en varias notificaciones visuales, por ejemplo, como un distintivo y como una notificación en una aplicación de la tienda de Windows.

Complete los pasos siguientes para enviar notificaciones de entre plataformas mediante plantillas:

1. En el Explorador de soluciones en Visual Studio, expanda la carpeta de **controladores** y abra el archivo RegisterController.cs.

2. Busque el bloque de código en el método de **entrada** que crea una nueva Reemplazar registro la `switch` contenido con el siguiente código:

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    Este código llama al método de específico de la plataforma para crear un registro de plantilla en lugar de un registro nativo. No es necesario modificar los registros existentes como registros plantilla derivan de registros nativos.

3. En el controlador de **notificaciones** , reemplace el método **sendNotification** con el siguiente código:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    Este código envía una notificación a todas las plataformas al mismo tiempo y sin tener que especificar una carga nativa. Notificación Hubs crea y ofrece la carga correcta para todos los dispositivos con el valor proporcionado _etiqueta_ , como se especifica en las plantillas registradas.

4. Volver a publicar el proyecto de back-end WebApi.

5. Vuelva a ejecutar la aplicación cliente y compruebe que el registro se realiza correctamente.

6. (Opcional) Implementar la aplicación de cliente en un segundo dispositivo y luego ejecute la aplicación.

    Tenga en cuenta que se muestra una notificación en cada dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha finalizado este tutorial, obtenga más información acerca de la notificación Hubs y plantillas en estos temas:

+ **[Usar Hubs de notificación para enviar noticias]** <br/>Se muestra otro escenario para el uso de plantillas

+  **[Información general de Hubs de notificación de Azure][Templates]**<br/>Tema de introducción contiene más información detallada sobre plantillas.


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Usar Hubs de notificación para enviar noticias]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificar a los usuarios con Hubs de notificación]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
