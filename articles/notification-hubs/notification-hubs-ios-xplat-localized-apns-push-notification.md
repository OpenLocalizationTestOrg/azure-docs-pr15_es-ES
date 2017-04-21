<properties
    pageTitle="Notificación Hubs localizados interrumpir noticias Tutorial para iOS"
    description="Aprenda a utilizar Hubs de notificación de Bus de servicio de Azure para enviar notificaciones de noticias de separación localizados (iOS)."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Usar Hubs de notificación para enviar noticias localizados para dispositivos iOS

> [AZURE.SELECTOR]
- [La tienda Windows C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification)


##<a name="overview"></a>Información general

Este tema muestra cómo usar la característica de [plantillas](notification-hubs-templates-cross-platform-push-messages.md) de Azure notificación Hubs difundir las notificaciones de noticias importantes que se han localizado por idioma y dispositivo. En este tutorial se inicia con la aplicación iOS creada en [Uso Hubs de notificación para enviar noticias]. Cuando haya terminado, podrá registrarse en categorías que le interesa, especifique un idioma en el que desea recibir notificaciones y recibir notificaciones de inserción solo para las categorías seleccionadas en ese idioma.


Existen dos partes en este escenario:

- aplicación iOS permite especificar un idioma y suscribirse a más reciente diferentes categorías de noticias; los dispositivos de cliente

- el back-end emite las notificaciones, con las características de **etiqueta** y una **plantilla** de Hubs de notificación de Azure.



##<a name="prerequisites"></a>Requisitos previos

Debe ya ha completado el tutorial de [Hubs de notificación de uso para enviar noticias] y obtener el código disponible, dado que este tutorial se basa directamente en código.

Visual Studio 2012 o posterior es opcional.



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

A continuación, se asegurará de que los dispositivos registran con una plantilla que hace referencia a la propiedad correcta. Por ejemplo, una aplicación iOS que desea registrar para obtener noticias francés registrará lo siguiente:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Plantillas son una característica muy eficaz que puede obtener más información acerca de nuestro artículo de [plantillas](notification-hubs-templates-cross-platform-push-messages.md) .

##<a name="the-app-user-interface"></a>La interfaz de usuario de la aplicación

A continuación modificará la aplicación de noticias de hora que creó en el tema [Usar Hubs de notificación para enviar las últimas noticias] para enviar noticias localizados con plantillas.


En su MainStoryboard_iPhone.storyboard, agregue un Control segmentado con los que es compatible con los lenguajes de tres: inglés, francés y mandarín.

![][13]

A continuación, asegúrese de agregar una IBOutlet en su ViewController.h tal como se muestra a continuación:

![][14]

##<a name="building-the-ios-app"></a>Creación de la aplicación de iOS


1. En su Notification.h agregar el método *retrieveLocale* y modificar el almacén y suscribirse métodos tal como se muestra a continuación:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

        - (NSSet*) retrieveCategories;

        - (int) retrieveLocale;

    En el Notification.m, modifique el método *storeCategoriesAndSubscribe* , agregando el parámetro de configuración regional y almacenar en los valores predeterminados de usuario:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];

            [self subscribeWithLocale: locale categories:categories completion:completion];
        }

    A continuación, modifique el método *suscribirse* para incluir la configuración regional:

        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }

            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }

    Observe cómo ahora estamos utilizando el método *registerTemplateWithDeviceToken*, en lugar de *registerNativeWithDeviceToken*. Cuando se registre para una plantilla tenemos proporcionan la plantilla de json y también un nombre para la plantilla (como nuestra aplicación quizás desee registrar plantillas diferentes). Asegúrese de que registrar categorías como etiquetas, como queremos para asegurarse de que recibe la notifciations para las noticias.

    Agregar un método para recuperar la configuración regional de la configuración de usuario predeterminada:

        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            int locale = [defaults integerForKey:@"BreakingNewsLocale"];

            return locale < 0?0:locale;
        }

2. Ahora que hemos modificado nuestra clase de notificaciones, hay que asegurarse de que nuestros ViewController permite el uso de la nueva UISegmentControl. Agregue la línea siguiente en el método *viewDidLoad* para asegurarse de que mostrar la configuración regional que está seleccionada:

        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    A continuación, en el método *suscribirse* , cambie la llamada a la *storeCategoriesAndSubscribe* a la siguiente:

        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

3. Por último, deberá actualizar el método de *didRegisterForRemoteNotificationsWithDeviceToken* en su AppDelegate.m para que se puede actualizar correctamente el registro cuando se inicia la aplicación. Cambiar la llamada al método *suscribirse* de notificaciones con los siguientes elementos:

        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="optional-send-localized-template-notifications-from-net-console-app"></a>(opcional) Enviar notificaciones de localizados plantilla de aplicación de consola de .NET.

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##<a name="optional-send-localized-template-notifications-from-the-device"></a>(opcional) Enviar notificaciones de plantilla localizados desde el dispositivo

Si no tiene acceso a Visual Studio, o simplemente desea probar a enviar las notificaciones de plantilla localizados directamente desde la aplicación en el dispositivo.  Puede sencillo agregar los parámetros de plantilla localizados a la `SendNotificationRESTAPI` método definido en el tutorial anterior.

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }




## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de plantillas, vea:

- [Notificar a los usuarios con notificación Hubs: ASP.NET]
- [Notificar a los usuarios con notificación Hubs: servicios para dispositivos móviles]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Usar Hubs de notificación para enviar noticias]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notificar a los usuarios con notificación Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notificar a los usuarios con notificación Hubs: servicios para dispositivos móviles]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
