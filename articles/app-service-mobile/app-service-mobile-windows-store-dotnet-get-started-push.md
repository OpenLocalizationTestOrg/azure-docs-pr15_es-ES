<properties
    pageTitle="Agregar las notificaciones de inserción a la aplicación de la plataforma de Windows Universal (UWP) | Aplicaciones móviles de Azure"
    description="Aprenda a usar aplicaciones de Mobile de servicio de aplicación de Azure y Azure notificación Hubs enviar notificaciones de inserción a la aplicación de la plataforma de Windows Universal (UWP)."
    services="app-service\mobile,notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-windows-app"></a>Agregar las notificaciones de inserción a la aplicación de Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Información general

En este tutorial, agregue las notificaciones de inserción para el proyecto de [Inicio rápido de Windows](app-service-mobile-windows-store-dotnet-get-started.md) para que se envía una notificación de inserción al dispositivo cada vez que se inserta un registro.

Si no utiliza el proyecto de servidor de inicio rápido descargado, tendrá el paquete de extensión de notificación de inserción. Para obtener más información, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="configure-hub"></a>Configurar un concentrador de notificación

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="register-your-app-for-push-notifications"></a>Registrar su aplicación para las notificaciones de inserción

Debe enviar su aplicación de la tienda de Windows y, después, configurar el proyecto de servidor para integrar con los servicios de notificación de Windows (WNS) para enviar la inserción.

1. En el Explorador de soluciones de Visual Studio, haga clic en el proyecto de aplicación UWP, haga clic en **almacén** > **Asociar la aplicación en la tienda …**. 

    ![Asociar la aplicación con la tienda Windows](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
    
2. En el asistente, haga clic en **siguiente**, inicie sesión con su cuenta de Microsoft, escriba un nombre para la aplicación de **reserva un nuevo nombre de la aplicación**y haga clic en **reservar**.

3. Una vez creado correctamente el registro de aplicación, seleccione el nuevo nombre de la aplicación, haga clic en **siguiente**y, a continuación, haga clic en **asociar**. Esto agrega la información de registro de la tienda Windows necesaria al manifiesto de aplicación.  

7. Vaya al [Centro de desarrollo de Windows](https://dev.windows.com/en-us/overview), iniciar sesión con su cuenta de Microsoft, haga clic en el nuevo registro de aplicación en **Mis aplicaciones**, expanda **Servicios** > **notificaciones de inserción**. 

8. En la página de **notificaciones de inserción** , haga clic en **el sitio de servicios de Live** en **Servicios de Microsoft Azure móviles**.

9. En la página de registro, anote el valor en **información confidencial de la aplicación** y el **Paquete SID**, que se usará siguiente para configurar la aplicación móvil de back-end. 

    ![Asociar la aplicación con la tienda Windows](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

    > [AZURE.IMPORTANT] El secreto de cliente y el paquete SID son las credenciales de seguridad importantes. No compartir estos valores con cualquier persona o distribuir con la aplicación. El **Identificador de la aplicación** se usa con el secreto para configurar la autenticación de Microsoft Account.

##<a name="configure-the-backend-to-send-push-notifications"></a>Configurar el servidor para enviar notificaciones de inserción

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

##<a id="update-service"></a>Actualizar el servidor para enviar notificaciones de inserción

Use el procedimiento siguiente que coincida con el tipo de proyecto de back-end&mdash; [.NET back-end](#dotnet) o [Node.js back-end](#nodejs).

### <a name="dotnet"></a>Proyecto de back-end.

1. En Visual Studio, haga clic en el proyecto de servidor y haga clic en **Administrar paquetes de NuGet**, busque Microsoft.Azure.NotificationHubs y haga clic en **instalar**. Instala la biblioteca de cliente de Hubs de notificación.

2. Expanda **controladores**, abra TodoItemController.cs y agregue las siguientes instrucciones:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

3. En el método **PostTodoItem** , agregue el código siguiente después de la llamada a **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Este código indica el hub de notificación para enviar una notificación de inserción después de un nuevo elemento de inserción.

4. Volver a publicar el proyecto de servidor.

### <a name="nodejs"></a>Proyecto de back-end Node.js

1. Si aún no lo ha hecho, [Descargue el proyecto de tutorial rápido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) o bien use el [editor en línea en el portal de Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Reemplace el código existente en el archivo todoitem.js con los siguientes elementos:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
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

    Envía una notificación de WNS que contiene la item.text Cuando se inserta un nuevo elemento de todo.

2. Al editar el archivo en el equipo local, vuelva a publicar el proyecto de servidor.

##<a id="update-app"></a>Agregar las notificaciones de inserción a la aplicación

A continuación, la aplicación debe registrarse para las notificaciones de inserción en el inicio. Cuando ya ha habilitado la autenticación, asegúrese de que el usuario signos de antes de intentar registrar las notificaciones de inserción.

1. Abra el archivo de proyecto **App.xaml.cs** y agregue las siguientes `using` instrucciones:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. En el mismo archivo, agregue la definición del método **InitNotificationsAsync** siguiente a la clase de **aplicación** :

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Este código recupera la ChannelURI para la aplicación de WNS y, a continuación, registra que ChannelURI con la aplicación móvil de servicio de aplicación.

3. En la parte superior del controlador de eventos **OnLaunched** en **App.xaml.cs**, agregue el modificador **asincrónica** a la definición del método y agregue la llamada siguiente a la nueva forma de **InitNotificationsAsync** , como en el ejemplo siguiente:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Así se garantiza que los ChannelURI de corta duración se registra cada vez que se inicia la aplicación.

4. Volver a crear el proyecto de aplicación UWP. La aplicación está preparada para recibir notificaciones de aviso.

##<a id="test"></a>Notificaciones de inserción de prueba en la aplicación

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>Pasos siguientes

Más información sobre las notificaciones de inserción:

* [Cómo usar al cliente administrado para aplicaciones móviles de Azure](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)  
Plantillas de proporcionan flexibilidad para enviar Push entre plataformas e inserta localizado. Obtenga información sobre cómo registrar plantillas.

* [Diagnosticar problemas de notificación de inserción](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Existen varias razones por las notificaciones pueden quitar o no terminan en dispositivos. Este tema muestra cómo analizar y averiguar la causa de errores de notificación de inserción. 

Considere la posibilidad de continuar con uno de los siguientes tutoriales:

+ [Agregar autenticación para su aplicación](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Aprenda a autenticar a los usuarios de la aplicación con un proveedor de identidades.

+ [Habilitar la sincronización sin conexión para la aplicación](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Obtenga información sobre cómo agregar la aplicación con una aplicación móvil de back-end de soporte técnico sin conexión. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no hay ninguna conexión de red.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

