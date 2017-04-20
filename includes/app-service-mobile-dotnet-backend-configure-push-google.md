Utilice el procedimiento que coincida con el tipo de proyecto de back-end&mdash; [.NET back-end](#dotnet) o [Node.js back-end](#nodejs).

### <a name="dotnet"></a>Proyecto de back-end.

1. En Visual Studio, haga clic en el proyecto de servidor y haga clic en **Administrar paquetes de NuGet**, busque `Microsoft.Azure.NotificationHubs`, a continuación, haga clic en **instalar**. Instala la biblioteca de cliente de Hubs de notificación.

2. En la carpeta controladores, abra TodoItemController.cs y agregue las siguientes `using` instrucciones:

        using Microsoft.Azure.Mobile.Server.Config;
        using Microsoft.Azure.NotificationHubs;

3. Reemplazar el `PostTodoItem` método con el siguiente código:  

      
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
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

            // Android payload
            var androidNotificationPayload = "{ \"data\" : {\"message\":\"" + item.Text + "\"}}";

            try
            {
                // Send the push notification and log the results.
                var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

                // Write the success result to the logs.
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                // Write the failure result to the logs.
                config.Services.GetTraceWriter()
                    .Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

4. Volver a publicar el proyecto de servidor.

### <a name="nodejs"></a>Proyecto de back-end Node.js

1. Si aún no lo ha hecho, [Descargue el proyecto de tutorial rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) o bien use el [editor en línea en el portal de Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
 
1. Reemplace el código existente en el archivo todoitem.js con los siguientes elementos:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
        
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the GCM payload.
        var payload = {
            "data": {
                "message": context.item.text
            }
        };   
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a GCM native notification.
                    context.push.gcm.send(null, payload, function (error) {
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

    Se envía una notificación de GCM que contiene la item.text Cuando se inserta un nuevo elemento de todo. 

2. Al editar el archivo en su equipo local, vuelva a publicar el proyecto de servidor. 
