<properties
    pageTitle="Usar Hubs de notificación para enviar noticias (Windows Phone)"
    description="Use Hubs de notificación de Azure utilizar etiquetas en los registros para enviar noticias acerca de una aplicación de Windows Phone."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Usar Hubs de notificación para enviar noticias

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Información general

Este tema muestra cómo usar Hubs de notificación de Azure difundir las notificaciones de noticias de más reciente a una aplicación de Windows Phone 8.0/8.1 Silverlight. Si se dirige a la tienda Windows o aplicación de Windows Phone 8.1, consulte a la versión de [Windows Universal](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) . Cuando haya terminado, se podrá registrarse para interrumpir categorías de noticias que le interese y recibir notificaciones de inserción solo para las categorías. Este escenario es un modelo común para muchas aplicaciones donde tienen las notificaciones se envíen a los grupos de usuarios que previamente han declarado interés, por ejemplo, lector RSS, aplicaciones para ventiladores de música, etcetera.

Escenarios de difusión están habilitados, incluyendo una o más _etiquetas_ al crear un registro en el hub de notificación. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos que se han registrado para la etiqueta recibirá la notificación. Dado que las etiquetas son simplemente cadenas, no tiene estén configurados con anticipación. Para obtener más información acerca de las etiquetas, consulte [el enrutamiento de Hubs de notificación y expresiones de etiqueta](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Requisitos previos

En este tema se basa en la aplicación que creó en [Introducción a Hubs de notificación]. Antes de comenzar este tutorial, debe haber ya completado [empezar con Hubs de notificación].

##<a name="add-category-selection-to-the-app"></a>Agregar selección de categoría a la aplicación

El primer paso es agregar los elementos de interfaz de usuario a la página principal existente que permiten al usuario seleccionar categorías para registrar. Las categorías seleccionadas por el usuario se almacenan en el dispositivo. Cuando se inicie la aplicación, se crea un registro de dispositivo en el hub de notificación con las categorías seleccionadas como etiquetas.

1. Abra el archivo de proyecto MainPage.xaml, a continuación, reemplace los elementos de **cuadrícula** denominados `TitlePanel` y `ContentPanel` con el siguiente código:

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. En el proyecto, crear una nueva clase denominada **notificaciones**, agregue el modificador **public** a la definición de la clase y agregue las siguientes instrucciones de **usar** el nuevo archivo de código:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;

3. Copie el código siguiente en la clase de **notificaciones de** nuevo:

        private NotificationHub hub;

        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();

            return await SubscribeToCategories();
        }

        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();

            var channel = HttpNotificationChannel.Find("MyPushChannel");

            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;

                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }

            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
            
            return await registrationTask.Task;
        }

        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }

        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";

            // The stored categories tags are passed with the template registration.

            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

            return await registrationTask.Task;
        }

        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;

            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }

            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }


    Esta clase utiliza el almacenamiento aislado para almacenar las categorías de noticias que este dispositivo va a recibir. También contiene métodos para registrar estas categorías con un registro de la notificación de [plantilla](notification-hubs-templates-cross-platform-push-messages.md) .


4. En el archivo de proyecto App.xaml.cs, agregue la siguiente propiedad a la clase de **aplicación** . Reemplazar el `<hub name>` y `<connection string with listen access>` marcadores de posición con su nombre de concentrador de notificación y la cadena de conexión para *DefaultListenSharedAccessSignature* que obtuvo anteriormente.

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    > [AZURE.NOTE] Porque las credenciales que se distribuyen con una aplicación de cliente no son seguras por lo general, solo debe distribuir la clave para escuchar acceso con la aplicación de cliente. Escuchar permite el acceso no se puede modificar la aplicación para registrar para las notificaciones, pero los registros existentes y no se pueden enviar notificaciones. La tecla de acceso completo se usa en un servicio de segura back-end para enviar notificaciones y modificar los registros existentes.

5. En su MainPage.xaml.cs, agregue la línea siguiente:

        using Windows.UI.Popups;

6. En el archivo de proyecto MainPage.xaml.cs, agregue el método siguiente:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
    
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
    
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }

    Este método crea una lista de categorías y utiliza la clase de **notificaciones** para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes con su centro de notificación. Cuando se cambian las categorías, se vuelve a crear el registro con las categorías.

La aplicación está ahora puede almacenar un conjunto de categorías en el almacenamiento local en el dispositivo y registrar con el hub de notificación cuando el usuario cambia la selección de categorías.

##<a name="register-for-notifications"></a>Registro de notificaciones

Estos pasos se registran con el concentrador de notificación en el inicio utilizando las categorías que se han almacenado en el almacenamiento local.

> [AZURE.NOTE] Dado que el canal URI asignado por el servicio de notificación de inserción de Microsoft (MPNS) puede cambiar en cualquier momento, debe registrar notificaciones con frecuencia evitar errores de notificación. Este ejemplo registra para las notificaciones cada vez que se inicia la aplicación. Para las aplicaciones que se ejecutan con frecuencia, más de una vez al día, puede omitir probablemente registro para conservar el ancho de banda si ha pasado menos de un día desde el registro anterior.


1. Abra el archivo App.xaml.cs y agregue el modificador **asincrónica** al método de **Application_Launching** y reemplace el código de registro de Hubs de notificación que haya agregado en [Introducción a Hubs de notificación] con el siguiente código:

        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();

            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }

    Esto se asegura de que cada vez que inicie la aplicación recupera las categorías de almacenamiento local y solicita un registro para estas categorías.

2. En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente código que implementa el método **OnNavigatedTo** :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
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

    ![][2]

3. Después de recibir una confirmación de que las categorías eran suscripción completado, ejecute la aplicación de consola para enviar notificaciones de cada categorías. Comprobar que solo recibe una notificación de las categorías que se ha suscrito a.

    ![][3]

Se ha completado este tema.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Introducción a Hubs de notificación]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

