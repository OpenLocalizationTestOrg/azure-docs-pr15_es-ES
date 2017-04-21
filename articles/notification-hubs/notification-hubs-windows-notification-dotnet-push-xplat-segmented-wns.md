<properties
    pageTitle="Usar Hubs de notificación para enviar noticias (Windows Universal)"
    description="Use Hubs de notificación de Azure con etiquetas en el registro de noticias para una aplicación de Windows universal."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>


<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Usar Hubs de notificación para enviar noticias


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Información general

Este tema muestra cómo usar Hubs de notificación de Azure difundir las notificaciones de noticias de más reciente a una tienda de Windows o una aplicación de Windows Phone 8.1 (no de Silverlight). Si se dirige a Windows Phone 8.1 Silverlight, consulte la versión [De Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) . Cuando haya terminado, se podrá registrarse para interrumpir categorías de noticias que le interese y recibir notificaciones de inserción solo para las categorías. Este escenario es un modelo común para muchas aplicaciones donde tienen las notificaciones se envíen a los grupos de usuarios que previamente han declarado interés en ellos, por ejemplo, el lector RSS, aplicaciones para ventiladores música y así sucesivamente. 

Escenarios de difusión están habilitados, incluyendo una o más _etiquetas_ al crear un registro en el hub de notificación. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos que se han registrado para la etiqueta recibirá la notificación. Dado que las etiquetas son simplemente cadenas, no tiene estén configurados con anticipación. Para obtener más información acerca de las etiquetas, consulte [el enrutamiento de Hubs de notificación y expresiones de etiqueta](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Requisitos previos

En este tema se basa en la aplicación que creó en [Introducción a Hubs de notificación][get-started]. Antes de comenzar este tutorial, debe haber ya completado [empezar con notificación Hubs][get-started].

##<a name="add-category-selection-to-the-app"></a>Agregar selección de categoría a la aplicación

El primer paso es agregar los elementos de interfaz de usuario a la página principal existente que permiten al usuario seleccionar categorías para registrar. Las categorías seleccionadas por el usuario se almacenan en el dispositivo. Cuando se inicie la aplicación, se crea un registro de dispositivo en el hub de notificación con las categorías seleccionadas como etiquetas.

1. Abra el archivo de proyecto MainPage.xaml, copie el código siguiente en el elemento de la **cuadrícula** :

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. Haga clic con el botón secundario del mouse en el proyecto **compartido** y agregar una nueva clase denominada **notificaciones**, agregue el modificador **public** a la definición de clase, agregue las siguientes instrucciones de **usar** el nuevo archivo de código:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Copie el código siguiente en la clase de **notificaciones de** nuevo:

        private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }

    Esta clase utiliza el almacenamiento local para almacenar las categorías de noticias que este dispositivo debe recibir. Tenga en cuenta que en lugar de llamar al método *RegisterNativeAsync* llamamos *RegisterTemplateAsync* registrarse para las categorías con un registro de la plantilla. 
    
    También le proporcionamos un nombre para la plantilla ("simpleWNSTemplateExample"), ya que podría desear registrar más de una plantilla (por ejemplo uno para las notificaciones de brindar) y otro para mosaicos y debemos asignarle un nombre para poder actualizar o eliminarlos.

    Tenga en cuenta que si un dispositivo se registra varias plantillas con la misma etiqueta, una identificación mensaje entrante a la que da etiqueta de varias notificaciones de entrega en el dispositivo (uno por cada plantilla). Este comportamiento es útil cuando el mismo mensaje lógico tiene como resultado varias notificaciones visuales, por ejemplo que muestra un distintivo y rosas en una aplicación de almacenamiento de Windows.

    Para obtener más información sobre plantillas, vea [plantillas](notification-hubs-templates-cross-platform-push-messages.md).




4. En el archivo de proyecto App.xaml.cs, agregue la siguiente propiedad a la clase de **aplicación** :

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    Esta propiedad se usa para crear y tener acceso a una instancia de **notificaciones** .

    En el código anterior, reemplace el `<hub name>` y `<connection string with listen access>` marcadores de posición con su nombre de concentrador de notificación y la cadena de conexión para *DefaultListenSharedAccessSignature* que obtuvo anteriormente.

    > [AZURE.NOTE] Porque las credenciales que se distribuyen con una aplicación de cliente no son seguras por lo general, solo debe distribuir la clave para escuchar acceso con la aplicación de cliente. Escuchar permite el acceso no se puede modificar la aplicación para registrar para las notificaciones, pero los registros existentes y no se pueden enviar notificaciones. La tecla de acceso completo se usa en un servicio de segura back-end para enviar notificaciones y modificar los registros existentes.

5. En su MainPage.xaml.cs, agregue la línea siguiente:

        using Windows.UI.Popups;

6. En el archivo de proyecto MainPage.xaml.cs, agregue el método siguiente:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Este método crea una lista de categorías y utiliza la clase de **notificaciones** para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes con su centro de notificación. Cuando se cambian las categorías, se vuelve a crear el registro con las categorías.

La aplicación está ahora puede almacenar un conjunto de categorías en el almacenamiento local en el dispositivo y registrar con el hub de notificación cuando el usuario cambia la selección de categorías.

##<a name="register-for-notifications"></a>Registro de notificaciones

Estos pasos se registran con el concentrador de notificación en el inicio utilizando las categorías que se han almacenado en el almacenamiento local.

> [AZURE.NOTE] Dado que el canal URI asignado por el servicio de notificación de Windows (WNS) puede cambiar en cualquier momento, debe registrar notificaciones con frecuencia evitar errores de notificación. En este ejemplo se registra para la notificación cada vez que se inicia la aplicación. Para las aplicaciones que se ejecutan con frecuencia, más de una vez al día, puede omitir probablemente registro para conservar el ancho de banda si ha pasado menos de un día desde el registro anterior.

1. Abra el archivo App.xaml.cs y actualizar el método **InitNotificationsAsync** para usar la `notifications` clase suscribirse basándose en las categorías.

        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
    
        var result = await notifications.SubscribeToCategories();

    Esto se asegura de que cada vez que inicie la aplicación recupera las categorías de almacenamiento local y solicita un registeration para estas categorías. Se creó el método de **InitNotificationsAsync** como parte de la [Introducción a la notificación Hubs] [ get-started] tutorial.

3. En el archivo de proyecto MainPage.xaml.cs, agregue el código siguiente al método *OnNavigatedTo* :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }

    Actualiza la página principal en función del estado de guardado anteriormente categorías.

La aplicación está ahora completa y puede almacenar un conjunto de categorías en el almacenamiento local de dispositivo utilizado para registrar con el hub de notificación cuando el usuario cambia la selección de categorías. A continuación, se definirá un back-end que puede enviar notificaciones de categoría para esta aplicación.

##<a name="sending-tagged-notifications"></a>Enviar notificaciones etiquetadas

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Ejecute la aplicación y generar notificaciones

1. En Visual Studio, presione F5 para compilar e iniciar la aplicación.

    ![][1]

    Tenga en cuenta que la interfaz de usuario de la aplicación proporciona un conjunto de alterna que le permite elegir las categorías para suscribirse a.

2. Habilitar uno o más alterna de categorías, haga clic en **suscribirse**.

    La aplicación convierte las categorías seleccionadas en las etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas desde el hub de notificación. Las categorías registradas son devuelto y se muestran en un cuadro de diálogo.

    ![][19]

4. Enviar una nueva notificación del servidor de una de las siguientes maneras:

    + **Aplicación de consola:** iniciar la aplicación de consola.

    + **Java y PHP:** ejecutar el script de aplicación.

    Las notificaciones de las categorías seleccionadas aparecen como las notificaciones de aviso.

    ![][14]

##<a name="next-steps"></a>Pasos siguientes

En este tutorial hemos visto cómo difundir noticias por categoría. Considere la posibilidad de finalización de uno de los siguientes tutoriales que resaltan otros escenarios de notificación Hubs avanzadas:

+ [Usar notificación Hubs difundir noticias localizados]

    Obtenga información sobre cómo ampliar la aplicación de noticias de más reciente para habilitar enviar notificaciones localizadas.



<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Usar notificación Hubs difundir noticias localizados]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
