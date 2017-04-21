<properties
    pageTitle="Notificación Hubs más reciente noticias Tutorial - iOS"
    description="Aprenda a usar Hubs de notificación de Bus de servicio de Azure para enviar notificaciones de noticias de más reciente a dispositivos iOS."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Usar Hubs de notificación para enviar noticias

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Información general

Este tema muestra cómo usar Hubs de notificación de Azure difundir las notificaciones de noticias de más reciente a una aplicación. Cuando haya terminado, se podrá registrarse para interrumpir categorías de noticias que le interese y recibir notificaciones de inserción solo para las categorías. Este escenario es un modelo común para muchas aplicaciones donde tienen las notificaciones se envíen a los grupos de usuarios que previamente han declarado interés, por ejemplo, lector RSS, aplicaciones para ventiladores de música, etcetera.

Escenarios de difusión están habilitados, incluyendo una o más _etiquetas_ al crear un registro en el hub de notificación. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos que se han registrado para la etiqueta recibirá la notificación. Dado que las etiquetas son simplemente cadenas, no tiene estén configurados con anticipación. Para obtener más información acerca de las etiquetas, consulte [el enrutamiento de Hubs de notificación y expresiones de etiqueta](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Requisitos previos

En este tema se basa en la aplicación que creó en [Introducción a Hubs de notificación][get-started]. Antes de comenzar este tutorial, debe haber ya completado [empezar con notificación Hubs][get-started].

##<a name="add-category-selection-to-the-app"></a>Agregar selección de categoría a la aplicación

El primer paso es agregar los elementos de interfaz de usuario a su guión gráfico existente que permiten al usuario seleccionar categorías para registrar. Las categorías seleccionadas por el usuario se almacenan en el dispositivo. Cuando se inicie la aplicación, se crea un registro de dispositivo en el hub de notificación con las categorías seleccionadas como etiquetas.

1. En su MainStoryboard_iPhone.storyboard agregue los siguientes componentes de la biblioteca de objetos:
    + Etiqueta con el texto "Noticias hora",
    + Etiquetas con textos de categoría "Todos", "Política", "Trabajo", "Tecnología", "Ciencia", "Deportes"
    + Seis conmutadores por categoría, establecen cada cambio de **estado** a estar **desactivada** de forma predeterminada.
    + Un botón con la etiqueta "Suscribirse"

    El guión gráfico debe tener el siguiente aspecto:

    ![][3]

2. En el editor del asistente, crear salidas para todos los modificadores y llamada "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"


3. Crear una acción para el botón denominado "suscripción". El ViewController.h debe contener lo siguiente:

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4. Crear una nueva **Clase de toque de cacao** denominada `Notifications`. Copie el código siguiente en la sección de la interfaz del archivo Notifications.h:

        @property NSData* deviceToken;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;

        - (NSSet*)retrieveCategories;

        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Agregue la siguiente directiva de importación a Notifications.m:

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Copie el código siguiente en la sección de implementación del archivo Notifications.m.

        SBNotificationHub* hub;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];

            return self;
        }

        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

            [self subscribeWithCategories:categories completion:completion];
        }


        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }


        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }



    Esta clase usa almacenamiento local para almacenar y recuperar las categorías de noticias que recibirá este dispositivo. Además, contiene un método para registrarse en estas categorías con un registro de la [plantilla](notification-hubs-templates-cross-platform-push-messages.md) .

7. En el archivo AppDelegate.h, agregue una instrucción de importación para Notifications.h y agregue una propiedad de una instancia de la clase de notificaciones:

        #import "Notifications.h"

        @property (nonatomic) Notifications* notifications;
    

8. En el método **didFinishLaunchingWithOptions** AppDelegate.m, agregue el código para inicializar la instancia de notificaciones al principio del método.  
 
    `HUBNAME`y `HUBLISTENACCESS` (definido en hubinfo.h) ya debe tener la `<hub name>` y `<connection string with listen access>` reemplazan de marcadores de posición con su nombre de concentrador de notificación y la cadena de conexión para *DefaultListenSharedAccessSignature* que obtuvo anteriormente

        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];

    > [AZURE.NOTE] Porque las credenciales que se distribuyen con una aplicación de cliente no son seguras por lo general, solo debe distribuir la clave para escuchar acceso con la aplicación de cliente. Escuchar permite el acceso no se puede modificar la aplicación para registrar para las notificaciones, pero los registros existentes y no se pueden enviar notificaciones. La tecla de acceso completo se usa en un servicio de segura back-end para enviar notificaciones y modificar los registros existentes.


9. En el método **didRegisterForRemoteNotificationsWithDeviceToken** en AppDelegate.m, reemplace el código en el método con el código siguiente para pasar el token de dispositivo a la clase de notificaciones. La clase de notificaciones realizará el registro para las notificaciones de las categorías. Si el usuario cambia las selecciones de categoría, llamamos el `subscribeWithCategories` método en el botón **suscribirse** actualizarlos.

    > [AZURE.NOTE] Dado que el token de dispositivo asignado por el servicio de notificación Push de Apple (APN) puede cambiar en cualquier momento, debe registrar notificaciones con frecuencia evitar errores de notificación. En este ejemplo se registra para la notificación cada vez que se inicia la aplicación. Para las aplicaciones que se ejecutan con frecuencia, más de una vez al día, puede omitir probablemente registro para conservar el ancho de banda si ha pasado menos de un día desde el registro anterior.

        self.notifications.deviceToken = deviceToken;

        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.

        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];


    Tenga en cuenta que en este momento no debe haber ningún otro código en el método **didRegisterForRemoteNotificationsWithDeviceToken** .

10. Los métodos siguientes deben estar ya presentes en AppDelegate.m de completar la [Introducción con notificación Hubs] [ get-started] tutorial.  Si no es así, agregarlos.

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

    Este método controla las notificaciones que recibe cuando la aplicación se está ejecutando mostrando un simple **UIAlert**.

11. En ViewController.m, agregue una instrucción de importación para AppDelegate.h y copie el siguiente código en el método generado XCode **suscribirse** . Este código actualizará el registro de la notificación para usar las nuevas etiquetas de categoría, que el usuario ha elegido en la interfaz de usuario.

        ```
        #import "Notifications.h"
        ```

        NSMutableArray* categories = [[NSMutableArray alloc] init];

        if (self.WorldSwitch.isOn) [categories addObject:@"World"];
        if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
        if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
        if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
        if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
        if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
            if (!error) {
                [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

    Este método crea un **NSMutableArray** de categorías y utiliza la clase de **notificaciones** para almacenar la lista en el almacenamiento local y registra las etiquetas correspondientes con su centro de notificación. Cuando se cambian las categorías, se vuelve a crear el registro con las categorías.

12. En ViewController.m, agregue el siguiente código en el método **viewDidLoad** para establecer la interfaz de usuario basada en las categorías guardadas anteriormente.


        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



Ahora, la aplicación puede almacenar un conjunto de categorías en el almacenamiento local del dispositivo utilizado para registrar con el concentrador de notificación siempre que se inicie la aplicación.  El usuario puede cambiar la selección de categorías en tiempo de ejecución y haga clic en el método **suscribirse** para actualizar el registro para el dispositivo. A continuación, se actualizará la aplicación para enviar las notificaciones de noticias importantes directamente en el propio.


##<a name="optional-sending-tagged-notifications"></a>(opcional) Enviar notificaciones etiquetadas

Si no tiene acceso a Visual Studio, puede pasar a la siguiente sección y enviar notificaciones de la aplicación. También puede enviar la notificación de plantilla adecuada desde el [Portal de Azure clásica] con la ficha Depurar para su centro de notificación. 

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]


##<a name="optional-send-notifications-from-the-device"></a>(opcional) Enviar notificaciones desde el dispositivo

Normalmente se enviará notificaciones por un servicio de back-end, pero puede enviar notificaciones de noticias de separación directamente desde la aplicación. Para hacer esto actualizaremos la `SendNotificationRESTAPI` método que se ha definido en la [Introducción a la notificación Hubs] [ get-started] tutorial.


1. En la actualización de ViewController.m la `SendNotificationRESTAPI` método como sigue para que acepte un parámetro de la etiqueta de categoría y envía una notificación de nombre propio [plantilla](notification-hubs-templates-cross-platform-push-messages.md) .

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
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];

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



2. En ViewController.m actualizar la acción **Enviar notificación** tal como se muestra en el código que sigue. Para que se envíen las notificaciones utilizando cada etiqueta de forma individual y enviar a varias plataformas.



        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



3. Volver a crear el proyecto y asegúrese de que no tiene generar errores.


##<a name="run-the-app-and-generate-notifications"></a>Ejecute la aplicación y generar notificaciones

1. Presione el botón ejecutar para generar el proyecto e iniciar la aplicación. Seleccionar algunas opciones de noticias de separación suscribirse a y, a continuación, presione el botón **suscribirse** . Verá un cuadro de diálogo que indica que se han suscrito las notificaciones.

    ![][1]

    Al elegir **suscribirse**, la aplicación convierte las categorías seleccionadas en las etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas desde el hub de notificación.

2. Escriba un mensaje para enviar como noticias, a continuación, presione el botón **Enviar notificación** . Como alternativa, ejecute la aplicación de consola de .NET para generar notificaciones.

    ![][2]


3. Cada dispositivo está suscrito a noticias recibirá las notificaciones de noticias de más reciente que acaba de enviar.



## <a name="next-steps"></a>Pasos siguientes

En este tutorial hemos visto cómo difundir noticias por categoría. Considere la posibilidad de finalización de uno de los siguientes tutoriales que resaltan otros escenarios de notificación Hubs avanzadas:

+ **[Usar notificación Hubs difundir noticias localizados]**

    Obtenga información sobre cómo ampliar la aplicación de noticias de más reciente para habilitar enviar notificaciones localizadas.





<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Usar notificación Hubs difundir noticias localizados]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Portal de clásico de Azure]: https://manage.windowsazure.com
