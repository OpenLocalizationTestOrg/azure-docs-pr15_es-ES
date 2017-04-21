<properties
    pageTitle="Notificación de Azure Hubs seguras de inserción"
    description="Obtenga información sobre cómo enviar notificaciones de inserción segura en Azure. Ejemplos de código escritos en C# con la API de .NET."
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs" 
    ms.workload="mobile"
    ms.tgt_pltfrm="windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Notificación de Azure Hubs seguras de inserción

> [AZURE.SELECTOR]
- [Universal de Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Información general

Soporte de notificaciones de inserción en Microsoft Azure le permite acceder a una infraestructura de inserción de fácil de usar, varias plataformas, escala horizontal, que simplifica la implementación de notificaciones de inserción para las aplicaciones de consumidor y empresa para las plataformas móviles.

Debido a normativos o restricciones de seguridad, a veces una aplicación que desee incluir algo en la notificación de que no se pueden transmitir a través de la infraestructura de notificación de inserción estándar. Este tutorial describe cómo lograr la misma experiencia mediante el envío de información confidencial a través de una conexión segura y autenticada entre el dispositivo de cliente y el servidor de la aplicación.

En un nivel alto, el flujo es como sigue:

1. La aplicación de fondo:
    - Carga de seguridad de almacena en la base de datos back-end.
    - El identificador de esta notificación se envía al dispositivo (no se envía ninguna información segura).
2. La aplicación en el dispositivo, al recibir la notificación:
    - El dispositivo de contactos el back-end que solicita la carga de seguridad.
    - La aplicación puede mostrar la carga como una notificación en el dispositivo.

Es importante que tenga en cuenta que en el flujo anterior (y en este tutorial), se supone que el dispositivo almacena un símbolo de autenticación en almacenamiento local, después de que el usuario inicia sesión. Así garantiza una experiencia perfecta por completo, como el dispositivo puede recuperar la carga de seguridad de la notificación con este token. Si la aplicación no almacenar tokens de autenticación en el dispositivo, o si se pueden caducar estos tokens, la aplicación de dispositivo, al recibir la notificación debe mostrar una notificación genérica preguntar al usuario para iniciar la aplicación. A continuación, la aplicación autentica al usuario y muestra la carga de notificación.

Este tutorial seguro inserción muestra cómo enviar una notificación de inserción de forma segura. El tutorial se basa en el tutorial de [Informar a los usuarios](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , por lo que debe completar los pasos de ese tutorial en primer lugar.

> [AZURE.NOTE] En este tutorial se asume que ha creado y configurado su centro de notificación, como se describe en [Introducción a la notificación Hubs (almacén de Windows)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
Recuerde que Windows Phone 8.1 requiere credenciales de Windows (no de Windows Phone), y que no funcionan en Windows Phone 8.0 o Silverlight 8.1 tareas en segundo plano. Para las aplicaciones de la tienda Windows, puede recibir notificaciones a través de una tarea en segundo plano solo si la aplicación está habilitada la pantalla de bloqueo (haga clic en la casilla de verificación en la Appmanifest).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modificar el proyecto de Windows Phone

1. En el proyecto **NotifyUserWindowsPhone** , agregue el código siguiente a App.xaml.cs para registrar la tarea de fondo de inserción. Agregue la siguiente línea de código al final de la `OnLaunched()` método:

        RegisterBackgroundTask();

2. Aún en App.xaml.cs, agregue el siguiente código inmediatamente después de la `OnLaunched()` método:

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3. Agregue las siguientes `using` instrucciones en la parte superior del archivo App.xaml.cs:

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4. En el menú **archivo** , en Visual Studio, haga clic en **Guardar todo**.

## <a name="create-the-push-background-component"></a>Crear el componente de fondo de inserción

El siguiente paso es crear el componente de fondo de inserción.

1. En el Explorador de soluciones, haga clic en el nodo de nivel superior de la solución (**SecurePush solución** en este caso), haga clic en **Agregar**y haga clic en **Nuevo proyecto**.

2. Expandir **Almacén de aplicaciones**, haga clic en **Aplicaciones de Windows Phone**, a continuación, haga clic en el **Componente de tiempo de ejecución de Windows (Windows Phone)**. Nombre del proyecto **PushBackgroundComponent**y, a continuación, haga clic en **Aceptar** para crear el proyecto.

    ![][12]

3. En el Explorador de soluciones, haga clic en el proyecto **PushBackgroundComponent (Windows Phone 8.1)** , a continuación, haga clic en **Agregar**, haga clic en **clase**. Nombre de la nueva clase **PushBackgroundTask.cs**. Haga clic en **Agregar** para generar la clase.

4. Reemplazar todo el contenido de la definición de espacio de nombres de **PushBackgroundComponent** con el siguiente código, sustituya el marcador de posición `{back-end endpoint}` con el extremo de back-end que se obtienen al implementar el fondo:

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5. En el Explorador de soluciones, haga clic en el proyecto **PushBackgroundComponent (Windows Phone 8.1)** y, a continuación, haga clic en **Administrar paquetes de NuGet**.

6. En el lado izquierdo, haga clic en **línea**.

7. En el cuadro **Buscar** , escriba **Cliente Http**.

8. En la lista de resultados, haga clic en **Las bibliotecas de cliente de HTTP de Microsoft**y, a continuación, haga clic en **instalar**. Completar la instalación.

9. En el cuadro de **búsqueda** de NuGet, escriba **Json.net**. Instalar el paquete de **Json.NET** y luego cierre la ventana Administrador de paquetes de NuGet.

10. Agregue las siguientes `using` instrucciones en la parte superior del archivo **PushBackgroundTask.cs** :

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. En el Explorador de soluciones, en el proyecto **NotifyUserWindowsPhone (Windows Phone 8.1)** , haga clic en **referencias**, haga clic en **Agregar referencia**. En el cuadro de diálogo Administrador de referencia, active la casilla situada junto a **PushBackgroundComponent**y, a continuación, haga clic en **Aceptar**.

12. En el Explorador de soluciones, haga doble clic en **Package.appxmanifest** en el proyecto **NotifyUserWindowsPhone (Windows Phone 8.1)** . En **las notificaciones**, establezca **Capaz de brindar** en **Sí**.

    ![][3]

13. Aún en **Package.appxmanifest**, haga clic en el menú de **declaraciones** cerca de la parte superior. En la lista desplegable **Declaraciones disponibles** , haga clic en **Tareas en segundo plano**y, a continuación, haga clic en **Agregar**.

14. En **Package.appxmanifest**, en **Propiedades**, active la **notificación de inserción**.

15. En **Package.appxmanifest**, en **Configuración de la aplicación**, escriba **PushBackgroundComponent.PushBackgroundTask** en el campo de **Punto de entrada** .

    ![][13]

16. En el menú **archivo** , haga clic en **Guardar todo**.

## <a name="run-the-application"></a>Ejecute la aplicación

Para ejecutar la aplicación, haga lo siguiente:

1. En Visual Studio, ejecute la aplicación de Web API de **AppBackend** . Se muestra una página web ASP.NET.

2. En Visual Studio, ejecute la aplicación de Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)** . El emulador de Windows Phone se ejecuta y la aplicación carga automáticamente.

3. En la aplicación de **NotifyUserWindowsPhone** de interfaz de usuario, escriba un nombre de usuario y contraseña. Pueden ser cualquier cadena, pero deben ser el mismo valor.

4. En la interfaz de usuario de **NotifyUserWindowsPhone** app, haga clic en **iniciar sesión y registrar**. A continuación, haga clic en **Enviar push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
