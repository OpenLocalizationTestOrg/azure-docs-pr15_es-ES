<properties
    pageTitle="Enviar notificaciones de inserción con Hubs de notificación de Azure en Windows Phone | Microsoft Azure"
    description="En este tutorial, aprenderá a usar Hubs de notificación de Azure para las notificaciones de inserción para una aplicación de Windows Phone 8 o Windows Phone 8.1 Silverlight."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="notificación de inserción, notificación de inserción push en windows phone"
    authors="ysxu"
    manager="erikre"
    editor="erikre"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Enviar notificaciones de inserción con Hubs de notificación de Azure en Windows Phone

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Información general

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

En este tutorial se muestra cómo usar Hubs de notificación de Azure para enviar notificaciones de inserción a una aplicación de Windows Phone 8 o Windows Phone 8.1 Silverlight. Si se dirige a Windows Phone 8.1 (no de Silverlight), a continuación, hacer referencia a la versión [Universal de Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
En este tutorial, se crea una aplicación de Windows Phone 8 en blanco que recibe las notificaciones de inserción mediante el servicio de notificación de inserción de Microsoft (MPNS). Cuando haya terminado, podrá usar su centro de notificación para difundir las notificaciones de inserción a todos los dispositivos que ejecutan la aplicación.

> [AZURE.NOTE] El SDK de Windows Phone de notificación Hubs no admite el uso del servicio de notificación de inserción de Windows (WNS) con aplicaciones de Windows Phone 8.1 Silverlight. Para usar WNS (en lugar de MPNS) con aplicaciones de Silverlight de Windows Phone 8.1, siga [Notificación Hubs - tutorial de Windows Phone Silverlight], que usa las API de REST.

El tutorial muestra el escenario de difusión simple en el uso de Hubs de notificación.

##<a name="prerequisites"></a>Requisitos previos

Este tutorial requiere lo siguiente:

+ [Visual Studio 2012 Express para Windows Phone], o una versión posterior.

Realizar este tutorial es un requisito previo para todos los otros tutoriales Hubs de notificación para las aplicaciones de Windows Phone 8.

##<a name="create-your-notification-hub"></a>Crear su centro de notificación

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Haga clic en la sección de <b>Servicios de notificación</b> (dentro de la <i>configuración</i>), haga clic en <b>Windows Phone (MPNS)</b> y, a continuación, haga clic en la casilla de verificación <b>Habilitar la inserción no autenticado</b> .</p>
</li>
</ol>

&emsp;&emsp;![Portal de Azure - habilitar unathenticated las notificaciones de inserción](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

Su centro ya creado y configurado para enviar una notificación no autenticado para Windows Phone.

> [AZURE.NOTE] Este tutorial usa MPNS en modo no autenticado. Modo MPNS no autenticado viene con restricciones de notificaciones que puede enviar a cada canal. Notificación Hubs es compatible con [MPNS autenticado modo](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) por lo que le permite cargar el certificado.

##<a name="connecting-your-app-to-the-notification-hub"></a>Conectar la aplicación en el hub de notificación

1. En Visual Studio, cree una nueva aplicación de Windows Phone 8.

    ![Aplicación de Visual Studio - nuevo proyecto - Windows Phone][13]

    En Visual Studio 2013 actualización 2 o posterior, en su lugar se crea una aplicación de Windows Phone Silverlight.

    ![Visual Studio - nuevo proyecto - en blanco la aplicación: Windows Phone Silverlight][11]

2. En Visual Studio, haga clic en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**.

    Aparecerá el cuadro de diálogo **Administrar paquetes de NuGet** .

3. Buscar `WindowsAzure.Messaging.Managed` y haga clic en **instalar**y, a continuación, acepte los términos de uso.

    ![Visual Studio - Administrador de paquetes de NuGet][20]

    Descargas, instalaciones y agrega una referencia a la biblioteca de Azure mensajería de Windows mediante el <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">paquete WindowsAzure.Messaging.Managed NuGet</a>.

4. Abra el archivo App.xaml.cs y agregue las siguientes `using` instrucciones:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Agregue el código siguiente en la parte superior del método de **Application_Launching** en App.xaml.cs:

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    >[AZURE.NOTE] El valor **MyPushChannel** es un índice que se utiliza para buscar un canal existente en la colección de [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . Si no hay uno allí, cree una nueva entrada con ese nombre.
    
    Asegúrese de insertar el nombre de su centro y la cadena de conexión denominado **DefaultListenSharedAccessSignature** que obtuvo en la sección anterior.
    Este código recupera el URI del canal para la aplicación de MPNS y, a continuación, registra URI del canal con su centro de notificación. También se garantiza el canal que URI está registrado en el hub de notificación cada vez que se inicia la aplicación.

    >[AZURE.NOTE]En este tutorial, se envía una notificación al dispositivo. Cuando se envía una notificación de mosaico, debe llamar en su lugar el método **BindToShellTile** en el canal. Para admitir ambos brindar y notificaciones de mosaico, llame a **BindToShellTile** y **BindToShellToast**.

6. En el Explorador de soluciones, expanda **Propiedades**, abra la `WMAppManifest.xml` de archivo, haga clic en la pestaña de **capacidades** y asegúrese de que está activada la capacidad **ID_CAP_PUSH_NOTIFICATION** .

    ![Visual Studio - Windows Phone capacidades de aplicación][14]

    Esto garantiza que la aplicación puede recibir notificaciones de inserción. Sin ella, se producirá un error si se intenta enviar una notificación de inserción a la aplicación.

7. Presione la `F5` tecla para ejecutar la aplicación.

    Se muestra un mensaje de registro de la aplicación.

8. Cierre la aplicación.  

   >[AZURE.NOTE] Para recibir una notificación de inserción, la aplicación no se ejecuta en primer plano.

##<a name="send-push-notifications-from-your-backend"></a>Enviar notificaciones de inserción desde el back-end

Puede enviar notificaciones de inserción mediante Hubs de notificación de cualquier back-end mediante la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>de público. En este tutorial, enviar notificaciones de inserción mediante una aplicación de consola de .NET. 

Para obtener un ejemplo de cómo enviar notificaciones de inserción de un back-end de ASP.NET WebAPI que se integra con Hubs de notificación, vea [Azure notificación Hubs informar a los usuarios con .NET back-end](./notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md).  

Para obtener un ejemplo de cómo enviar notificaciones de inserción mediante las [API de REST](https://msdn.microsoft.com/library/azure/dn223264.aspx), consulte [cómo usar Hubs de notificación de Java](./notification-hubs-java-push-notification-tutorial.md) y [cómo usar Hubs de notificación de PHP](./notification-hubs-php-push-notification-tutorial.md).

1. Haga clic en la solución, seleccione **Agregar** y **Nuevo proyecto...**y, a continuación, en **Visual C#**, haga clic en **Windows** y **Aplicación de consola**y haga clic en **Aceptar**.

    ![Aplicación de consola de Visual Studio - nuevo proyecto:][6]

    Esto agrega una nueva aplicación de consola Visual C# a la solución. También puede hacerlo en una solución independiente.

4. Haga clic en **Herramientas**, haga clic en **Administrador de paquetes de biblioteca**y, a continuación, haga clic en la **Consola del Administrador de paquetes**.

    Muestra la consola del Administrador de paquetes.

5.  En la ventana de la **Consola del Administrador de paquetes** , establezca **predeterminado de proyecto** en el nuevo proyecto de aplicación de consola y, a continuación, en la ventana de la consola, ejecute el siguiente comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Esto agrega una referencia en el SDK de Hubs de notificación de Azure utilizando el <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">paquete Microsoft.Azure.Notification Hubs NuGet</a>.

6. Abrir la `Program.cs` de archivos y agregue las siguientes `using` instrucción:

        using Microsoft.Azure.NotificationHubs;

6. En la `Program` de clase, agregue el método siguiente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    Asegúrese de reemplazar el `<hub name>` marcador de posición con el nombre del concentrador de notificación que aparece en el portal. Asimismo, reemplace el marcador de posición de cadena de conexión con la cadena de conexión denominada **DefaultFullSharedAccessSignature** que obtuvo en la sección "Configurar su centro de notificación".

    >[AZURE.NOTE]Asegúrese de que usa la cadena de conexión con acceso **completo** , no **escuchar** el acceso. La cadena de escucha access no tiene permisos para enviar notificaciones de inserción.

4. Agregue la línea siguiente en su `Main` método:

         SendNotificationAsync();
         Console.ReadLine();

5. Con el emulador de Windows Phone ejecutando y su aplicación cerrado, establecer el proyecto de aplicación de consola como proyecto de inicio predeterminado y, a continuación, presione la `F5` tecla para ejecutar la aplicación.

    Recibirá una notificación de inserción. Al puntear en el encabezado de brindar carga la aplicación.

Puede encontrar todas las cargas posibles en los temas de [catálogo brindar] y [catálogo de mosaico] en MSDN.

##<a name="next-steps"></a>Pasos siguientes

En este sencillo ejemplo, las notificaciones de inserción se difusión a todos los dispositivos de Windows Phone 8. 

Para dirigirse a usuarios específicos, consulte el tutorial de [Uso Hubs de notificación para las notificaciones de inserción a los usuarios] . 

Si desea agrupar los usuarios por grupos de interés, puede leer [Hubs de notificación de uso para enviar noticias]. 

Más información sobre cómo usar notificación Hubs en [Guía de Hubs de notificación].



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Guía de Hubs de notificación]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Usar Hubs de notificación para las notificaciones de inserción a los usuarios]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Usar Hubs de notificación para enviar noticias]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[catálogo de aviso]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catálogo de mosaico]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notificación Hubs - tutorial de Silverlight de Windows Phone]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

