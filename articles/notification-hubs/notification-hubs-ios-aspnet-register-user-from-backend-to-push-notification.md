<properties
    pageTitle="Registrar el usuario actual para las notificaciones de inserción, con la API de Web | Microsoft Azure"
    description="Obtenga información sobre cómo solicitar el registro de la notificación de inserción en una aplicación de iOS con Azure Hubs de notificación cuando se realiza registeration por la API de Web de ASP.NET."
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
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrar el usuario actual para las notificaciones de inserción mediante ASP.NET

> [AZURE.SELECTOR]
- [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)



##<a name="overview"></a>Información general

Este tema muestra cómo solicitar el registro de la notificación de inserción con Azure Hubs de notificación cuando el registro se realiza por la API de Web de ASP.NET. En este tema se amplía el tutorial [usuarios notificar con Hubs de notificación]. Debe haber completado los pasos necesarios en ese tutorial para crear el servicio móvil autenticado. Para obtener más información sobre el escenario de usuarios de notificación, vea [los usuarios notificar con Hubs de notificación].

##<a name="update-your-app"></a>Actualizar la aplicación  

1. En su MainStoryboard_iPhone.storyboard, agregue los siguientes componentes de la biblioteca de objetos:

    + **Etiqueta**: "Push al usuario con Hubs de notificación"
    + **Etiqueta**: "InstallationId"
    + **Etiqueta**: "Usuario"
    + **Campo de texto**: "Usuario"
    + **Etiqueta**: "Contraseña"
    + **Campo de texto**: "Contraseña"
    + **Botón**: "Inicio de sesión"

    En este momento, el guión gráfico es similar a la siguiente:

    ![][0]

2. En el editor del asistente, crear salidas para todos los controles conmutadas llamada, conectar los campos de texto con el controlador de vista (delegado) y crear una **acción** para el botón de **Inicio de sesión** .

    ![][1]

    El archivo BreakingNewsViewController.h debe contener ahora el siguiente código:

        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;

        - (IBAction)login:(id)sender;

5. Crear una clase denominada **DeviceInfo**y copie el código siguiente en la sección de la interfaz del archivo DeviceInfo.h:

        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;

6. Copie el código siguiente en la sección de implementación del archivo DeviceInfo.m:

            @synthesize installationId = _installationId;

            - (id)init {
                if (!(self = [super init]))
                    return nil;

                NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
                if(!_installationId) {
                    CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
                    _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
                    CFRelease(newUUID);

                    //store the install ID so we don't generate a new one next time
                    [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
                    [defaults synchronize];
                }

                return self;
            }

            - (NSString*)getDeviceTokenInHex {
                const unsigned *tokenBytes = [[self deviceToken] bytes];
                NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                      ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                      ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                      ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                return hexToken;
            }

7. En PushToUserAppDelegate.h, agregue el siguiente singleton de propiedad:

        @property (strong, nonatomic) DeviceInfo* deviceInfo;

8. En el método **didFinishLaunchingWithOptions** PushToUserAppDelegate.m, agregue el siguiente código:

        self.deviceInfo = [[DeviceInfo alloc] init];

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    La primera línea inicializa **DeviceInfo** singleton. La segunda línea inicia el registro de inserción notificaciones, que ya está presenten es que ya ha completado el tutorial de [Introducción a Hubs de notificación] .

9. En PushToUserAppDelegate.m, implementar el método **didRegisterForRemoteNotificationsWithDeviceToken** en su AppDelegate y agregue el siguiente código:

        self.deviceInfo.deviceToken = deviceToken;

    Esto establece el token de dispositivo de la solicitud.

    > [AZURE.NOTE] En este momento, no debe haber cualquier otro código en este método. Si ya dispone de una llamada al método **registerNativeWithDeviceToken** que se agregó al completar el tutorial de [Introducción a Hubs de notificación](/manage/services/notification-hubs/get-started-notification-hubs-ios/) , debe fuera del comentario o quitar esa llamada.

10. En el archivo PushToUserAppDelegate.m, agregue el siguiente método de controlador:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

     Este método muestra una alerta en la interfaz de usuario cuando la aplicación recibe las notificaciones mientras se está ejecutando.

9. Abra el archivo PushToUserViewController.m y devolver el teclado en la implementación siguiente:

        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }

9. En el método **viewDidLoad** en el archivo PushToUserViewController.m, inicializar la etiqueta installationId como sigue:

        DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
        Self.installationId.text = deviceInfo.installationId;

10. Agregue las siguientes propiedades de interfaz en PushToUserViewController.m:

        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;

11. A continuación, agregue la siguiente implementación:

            - (NSOperationQueue *)downloadQueue {
                if (!_downloadQueue) {
                    _downloadQueue = [[NSOperationQueue alloc] init];
                    _downloadQueue.name = @"Download Queue";
                    _downloadQueue.maxConcurrentOperationCount = 1;
                }
                return _downloadQueue;
            }

            // base64 encoding
            - (NSString*)base64forData:(NSData*)theData
            {
                const uint8_t* input = (const uint8_t*)[theData bytes];
                NSInteger length = [theData length];

                static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

                NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
                uint8_t* output = (uint8_t*)data.mutableBytes;

                NSInteger i;
                for (i=0; i < length; i += 3) {
                    NSInteger value = 0;
                    NSInteger j;
                    for (j = i; j < (i + 3); j++) {
                        value <<= 8;

                        if (j < length) {
                            value |= (0xFF & input[j]);
                        }
                    }

                    NSInteger theIndex = (i / 3) * 4;
                    output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
                    output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
                    output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
                    output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
                }

                return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
            }


12. Copie el siguiente código en el método de controlador de **Inicio de sesión** creado por XCode:

            DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

            // build JSON
            NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

            // build auth string
            NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
            [request setHTTPMethod:@"POST"];
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
            [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

            // connect with POST
            [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
                // add UIAlert depending on response.
                if (error != nil) {
                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if ([httpResponse statusCode] == 200) {
                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    } else {
                        NSLog(@"status: %ld", (long)[httpResponse statusCode]);
                    }
                } else {
                    NSLog(@"error: %@", error);
                }
            }];

    Este método obtiene un identificador de instalación y el canal para las notificaciones de inserción y lo envía, junto con el tipo de dispositivo, en el método de Web API autenticado que crea un registro en Hubs de notificación. Esta API Web se definió en [notificar a los usuarios con Hubs de notificación].

Ahora que la aplicación cliente se ha actualizado, vuelva a los [usuarios notificar con Hubs de notificación] y actualizar el servicio móvil para enviar notificaciones mediante Hubs de notificación.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Notificar a los usuarios con Hubs de notificación]: /manage/services/notification-hubs/notify-users-aspnet

[Introducción a Hubs de notificación]: /manage/services/notification-hubs/get-started-notification-hubs-ios
