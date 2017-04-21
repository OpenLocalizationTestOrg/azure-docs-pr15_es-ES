<properties
    pageTitle="Notificación de Azure Hubs enriquecido Push"
    description="Obtenga información sobre cómo enviar notificaciones de inserción enriquecido a una aplicación de Azure. Ejemplos de código escritos en objetivo C y C#."
    documentationCenter="ios"
    services="notification-hubs"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-rich-push"></a>Notificación de Azure Hubs enriquecido Push


##<a name="overview"></a>Información general

A fin de efectuar a los usuarios con contenido enriquecido instantánea, una aplicación que desee insertar más allá de texto sin formato. Estas notificaciones promover interacciones de usuario y presentar contenido como direcciones URL, sonidos, imágenes o cupones y más. En este tutorial se basa en el tema de [Informar a los usuarios](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) y se muestra cómo enviar notificaciones de inserción que incorporan cargas (por ejemplo, imágenes).


Este tutorial es compatible con iOS 7 y 8.

  ![][IOS1]

En un nivel alto:

1. El servidor de la aplicación:
    - Almacena la carga enriquecida (en este caso, imágenes) en el almacenamiento de la base de datos o local de back-end
    - Envía el identificador de esta notificación enriquecida al dispositivo
2. Aplicación en el dispositivo:
    - El back-end que solicita la carga enriquecida con el identificador que recibe de contactos
    - Envía notificaciones de los usuarios en el dispositivo cuando se completa la recuperación de datos y muestra la carga inmediatamente cuando los usuarios puntee en para obtener más información


## <a name="webapi-project"></a>Proyecto WebAPI

1. En Visual Studio, abra el proyecto **AppBackend** que creó en el tutorial de [Informar a los usuarios](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
2. Obtener una imagen que desea notificar a los usuarios con y coloca en una carpeta de **img** en el directorio del proyecto.
3. Haga clic en **Mostrar todos los archivos** en el Explorador de soluciones y haga clic en la carpeta para **Incluir en el proyecto**.
4. Con la imagen seleccionada, cambie su acción en la ventana Propiedades de generación a **Recurso incrustado**.

    ![][IOS2]

5. En **Notifications.cs**, agregue la siguiente instrucción using:

        using System.Reflection;

6. Actualizar toda la clase **notificaciones** con el siguiente código. Asegúrese de reemplazar los marcadores de posición con sus credenciales de concentrador de notificación y el nombre de archivo de imagen.

        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }

            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }

    >[AZURE.NOTE]  (opcional) Para obtener más información sobre cómo agregar y obtener recursos del proyecto, consulte [cómo incrustar y acceso a los recursos mediante Visual C#](http://support.microsoft.com/kb/319292) .

7. En **NotificationsController.cs**, redefinir **NotificationsController** con los fragmentos de código siguientes. Este envía un identificador de notificación inicial de enriquecido silencioso al dispositivo y permite la recuperación de cliente de imagen:

        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);

            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

            return result;
        }

        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

8. Ahora se vuelve a implementar esta aplicación a un sitio Web de Azure para que sea accesible desde todos los dispositivos. Haga clic en el proyecto **AppBackend** y seleccione **Publicar**.

9. Seleccione el sitio Web de Azure como el destino de publicación. Inicie sesión con su cuenta de Azure y seleccione un sitio Web existente o nuevo y tome nota de la propiedad de **dirección URL de destino** en la ficha **conexión** . Nos hará referencia a esta dirección URL como el *extremo de back-end* más adelante en este tutorial. Haga clic en **Publicar**.

## <a name="modify-the-ios-project"></a>Modificar el proyecto de iOS

Ahora que ha modificado la aplicación de back-end para enviar el *identificador* de una notificación, cambiará la aplicación iOS para controlar ese id y recuperar el mensaje completo de su servidor.

1. Abra el proyecto de iOS y habilitar notificaciones remotas yendo al objetivo principal de aplicación en la sección de **destinos** .

2. Haga clic en **funciones**, activar **Los modos de fondo**y marque la casilla **Notificaciones remoto** .

    ![][IOS3]

3. Vaya a **Main.storyboard**y asegúrese de que tiene un controlador de vista (denomina controlador de Vista Home en este tutorial) del [Usuario notificar](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) tutorial.

4. Agregar un **Controlador de navegación** a su guión gráfico y arrastre de control para ver controlador Home para que sea la **vista de raíz** de navegación. Asegúrese de que está seleccionado el **Controlador de vista inicial está** en el inspector de atributos para el controlador de navegación sólo.

5. Agregar un **Controlador de vista** para crear un guión gráfico y agregar una **Imagen de la vista**. Esta es la página que los usuarios verán una vez que elijan más haciendo clic en el notifiication. El guión gráfico debe tener el siguiente aspecto:

    ![][IOS4]

6. Haga clic en el **Controlador de Vista Home** guión gráfico y a continuación, asegúrese de que tiene **homeViewController** como su **Clase personalizada** y el **Identificador de guión gráfico** en el inspector de identidad.

7. Haga lo mismo para el controlador de vista de imagen como **imageViewController**.

8. A continuación, cree una nueva clase de controlador de vista titulado **imageViewController** para controlar la interfaz de usuario que acaba de crear.

9. En **imageViewController.h**, agregue lo siguiente a las declaraciones de interfaz del controlador. Asegúrese de control y arrastre desde la vista de imágenes de guión gráfico a estas propiedades para vincular los dos:

        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;

10. En **imageViewController.m**, agregue lo siguiente al final de **viewDidload**:

        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];

11. En **AppDelegate.m**, importar el controlador de imagen que ha creado:

        #import "imageViewController.h"

12. Agregar una sección de la interfaz con la declaración siguiente:

        @interface AppDelegate ()

        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;

        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;

        @end

13. En **AppDelegate**, asegúrese de que la aplicación se registra para notificaciones silenciosas en **aplicación: didFinishLaunchingWithOptions**:

        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");

            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";

            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];


            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

14. Sustituirlas en la siguiente implementación de **aplicaciones: didRegisterForRemoteNotificationsWithDeviceToken** tomar el guión gráfico de interfaz de usuario cambia en cuenta:

        // Access navigation controller which is at the root of window
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        // Get home view controller from stack on navigation controller
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        hvc.deviceToken = deviceToken;

15. A continuación, agregue los siguientes métodos para **AppDelegate.m** para recuperar la imagen desde el punto final y enviar una notificación local cuando se complete la recuperación. Asegúrese de sustituir el marcador de posición `{backend endpoint}` con el extremo de back-end:

        NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

        // Helper: retrieve notification content from backend with rich notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
            UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
            homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
            NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
            // Check if authenticated
            if (!authenticationHeader) return;

            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];

            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200) {
                    // From NSData to UIImage
                    self.imagePayload = [UIImage imageWithData:data];

                    completion(nil);
                }
                else {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(error);
                    else {
                        completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        // Handle silent push notifications when id is sent from backend
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
            self.userInfo = userInfo;
            int richId = [[self.userInfo objectForKey:@"richId"] intValue];
            NSString* richType = [self.userInfo objectForKey:@"richType"];

            // Retrieve image data
            if ([richType isEqualToString:@"img"]) {  
                [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                    if (!error){
                        // Send local notification
                        UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                        // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                        localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                        localNotification.userInfo = self.userInfo;
                        localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                        localNotification.timeZone = [NSTimeZone defaultTimeZone];

                        // iOS8 categories
                        if (self.iOS8) {
                            localNotification.category = @"richPush";
                        }

                        [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                        handler(UIBackgroundFetchResultNewData);
                    }
                    else{
                        handler(UIBackgroundFetchResultFailed);
                    }
                }];
            }
            // Add "else if" here to handle more types of rich content such as url, sound files, etc.
        }

16. Controlar la notificación local encima abriendo el controlador de vista de imagen en **AppDelegate.m** con los métodos siguientes:

        // Helper: redirect users to image view controller
        - (void)redirectToImageViewWithImage: (UIImage *)img {
            UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
            UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                     bundle: nil];
            imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
            // Pass data/image to image view controller
            imgViewController.imagePayload = img;

            // Redirect
            [navigationController pushViewController:imgViewController animated:YES];
        }

        // Handle local notification sent above in didReceiveRemoteNotification
        - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
            if (application.applicationState == UIApplicationStateActive) {
                // Show in-app alert with an extra "more" button
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];

                [alert show];
            }
            // App becomes active from user's tap on notification
            else {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
        }

        // Handle buttons in in-app alerts and redirect with data/image
        - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
            // Handle "more" button
            if (buttonIndex == 1)
            {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            // Add "else if" here to handle more buttons
        }

        // Handle notification setting actions in iOS8
        - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
            // Handle richPush related buttons
            if ([identifier isEqualToString:@"richPushMore"]) {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            completionHandler();
        }

## <a name="run-the-application"></a>Ejecute la aplicación

1. En XCode, ejecute la aplicación en un dispositivo iOS físico (push notificaciones no funcionarán en el simulador).

2. En el interfaz de usuario de aplicación de iOS, escriba un nombre de usuario y contraseña del mismo valor para la autenticación y haga clic en **Log In**.

3. Haga clic en **Enviar inserción** y verá una alerta de la aplicación. Si hace clic en **más**, se ha insertado la imagen que decide incluir en su aplicación de back-end.

4. Puede hacer clic en **Enviar inserción** e inmediatamente presione el botón principal del dispositivo. En unos momentos, recibirá una notificación de inserción. Si pulsa en él o haga clic en más, se puede poner a la aplicación y el contenido de una imagen.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
