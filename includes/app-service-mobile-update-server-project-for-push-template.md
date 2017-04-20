En esta sección, actualice el código del proyecto existente de back-end de aplicaciones móviles para enviar una notificación de inserción cada vez que se agrega un nuevo elemento. Esto funciona con la característica de la [plantilla](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) de notificación Hubs, habilitar Push entre plataformas. Los clientes están registrados para su uso de plantillas de notificaciones de inserción y push universal solo puede acceder a todas las plataformas de cliente.

Elija el procedimiento siguiente que coincida con el tipo de proyecto de back-end&mdash; [.NET back-end](#dotnet) o [Node.js back-end](#nodejs).

### <a name="dotnet"></a>Proyecto de back-end.
1. En Visual Studio, haga clic en el proyecto de servidor y haga clic en **Administrar paquetes de NuGet**, busque `Microsoft.Azure.NotificationHubs`, a continuación, haga clic en **instalar**. Instala la biblioteca Hubs de notificación para enviar notificaciones de su back-end.

3. En el proyecto de servidor, abra **controladores** > **TodoItemController.cs**y agregue las siguientes instrucciones:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
    

2. En el método **PostTodoItem** , agregue el código siguiente después de la llamada a **InsertAsync**:  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Se envía una notificación de plantilla que contiene el elemento. Texto cuando se inserta un nuevo elemento.

4. Volver a publicar el proyecto de servidor. 

### <a name="nodejs"></a>Proyecto de back-end Node.js

1. Si aún no lo ha hecho, [Descargue el proyecto de back-end de tutorial rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) o bien use el [editor en línea en el portal de Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Reemplace el código existente en todoitem.js con lo siguiente:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
    
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;  

    Se envía una notificación de plantilla que contiene la item.text Cuando se inserta un nuevo elemento.

2. Al editar el archivo en el equipo local, vuelva a publicar el proyecto de servidor.
