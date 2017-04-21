<properties
    pageTitle="Notificación Hubs localizados Tutorial de noticias de más reciente"
    description="Aprenda a utilizar Hubs de notificación de Azure para enviar notificaciones de noticias de separación localizados."
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

# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>Usar Hubs de notificación para enviar noticias localizados

> [AZURE.SELECTOR]
- [La tienda Windows C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

##<a name="overview"></a>Información general

Este tema muestra cómo usar la característica de **plantilla** de Azure notificación Hubs difundir las notificaciones de noticias importantes que se han localizado por idioma y dispositivo. En este tutorial se inicia con la aplicación de la tienda de Windows creada en [Uso Hubs de notificación para enviar noticias]. Cuando haya terminado, podrá registrarse en categorías que le interesa, especifique un idioma en el que desea recibir notificaciones y recibir notificaciones de inserción solo para las categorías seleccionadas en ese idioma.


Existen dos partes en este escenario:

- la aplicación de la tienda de Windows permite especificar un idioma y suscribirse a más reciente diferentes categorías de noticias; los dispositivos de cliente

- el back-end emite las notificaciones, con las características de **etiqueta** y una **plantilla** de Hubs de notificación de Azure.



##<a name="prerequisites"></a>Requisitos previos

Debe ya ha completado el tutorial de [Hubs de notificación de uso para enviar las últimas noticias] y obtener el código disponible, dado que este tutorial se basa directamente en código.

También debe Visual Studio 2012 o posterior.


##<a name="template-concepts"></a>Conceptos de plantilla

En [Uso Hubs de notificación para enviar noticias] genera una aplicación que utiliza **etiquetas** para suscribirse a las notificaciones de categorías de noticias diferente.
Sin embargo, muchas aplicaciones, varios mercados de destino y requieren localización. Esto significa que tiene el contenido de las notificaciones a sí mismos localizados y entregar para el conjunto de dispositivos correcto.
En este tema le mostrará cómo usar la característica de la **plantilla** de notificación Hubs para entregar fácilmente las notificaciones de noticias de separación localizados.

Nota: una forma de enviar notificaciones localizadas es crear varias versiones de cada etiqueta. Por ejemplo, para admitir inglés, francés y mandarín, será necesario tres etiquetas diferentes para obtener noticias del mundo: "world_en", "world_fr" y "world_ch". Tendríamos que enviar una versión localizada de noticias de llamadas internacionales a cada una de estas etiquetas. En este tema se use plantillas para evitar la proliferación de etiquetas y el requisito de envío de varios mensajes.

En un nivel alto, plantillas son una forma para especificar cómo un dispositivo específico recibirán una notificación. La plantilla especifica el formato de carga exacta puede hacer referencia a las propiedades que forman parte del mensaje enviado por la aplicación back-end. En este caso, le enviaremos un mensaje independiente de la configuración regional que contiene todos los idiomas compatibles:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

A continuación, se asegurará de que los dispositivos registran con una plantilla que hace referencia a la propiedad correcta. Por ejemplo, una aplicación de la tienda de Windows que desea recibir un mensaje de aviso simple registrará para la siguiente plantilla con todas las etiquetas correspondientes:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



Plantillas son una característica muy eficaz que puede obtener más información acerca de nuestro artículo de [plantillas](notification-hubs-templates-cross-platform-push-messages.md) . 


##<a name="the-app-user-interface"></a>La interfaz de usuario de la aplicación

A continuación modificará la aplicación de noticias de hora que creó en el tema [Usar Hubs de notificación para enviar las últimas noticias] para enviar noticias localizados con plantillas.

En la aplicación de la tienda de Windows:

Cambiar su MainPage.xaml para incluir un cuadro combinado de configuración regional:

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

##<a name="building-the-windows-store-client-app"></a>Creación de la aplicación de cliente de la tienda de Windows

1. En la clase de notificaciones, agregar un parámetro de configuración regional a los métodos *StoreCategoriesAndSubscribe* y *SubscribeToCateories* .

        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }

        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }

    Tenga en cuenta que en lugar de llamar al método *RegisterNativeAsync* llamamos *RegisterTemplateAsync*: nos estamos registrar un formato específico de notificación en la que la plantilla depende de la configuración regional. También le proporcionamos un nombre para la plantilla ("localizedWNSTemplateExample"), ya que podría desear registrar más de una plantilla (por ejemplo uno para las notificaciones de brindar) y otro para mosaicos y debemos asignarle un nombre para poder actualizar o eliminarlos.

    Tenga en cuenta que si un dispositivo se registra varias plantillas con la misma etiqueta, una identificación mensaje entrante a la que da etiqueta de varias notificaciones de entrega en el dispositivo (uno por cada plantilla). Este comportamiento es útil cuando el mismo mensaje lógico tiene como resultado varias notificaciones visuales, por ejemplo que muestra un distintivo y rosas en una aplicación de almacenamiento de Windows.

2. Agregue el siguiente método para recuperar la configuración regional almacenada:

        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. En su MainPage.xaml.cs, el botón Actualizar haga clic en el controlador de recuperar el valor actual del cuadro combinado de configuración regional y proporcionando a la llamada a la clase de notificaciones, tal como se muestra:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;

            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);

            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }


4. Por último, en el archivo App.xaml.cs, asegúrese de actualizar su `InitNotificationsAsync` método para recuperar la configuración regional y usarlo al suscribirse:

        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


##<a name="send-localized-notifications-from-your-back-end"></a>Enviar notificaciones localizadas desde su back-end

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]






<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Usar Hubs de notificación para enviar noticias]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
