<properties
    pageTitle="Notificación de Azure Hubs seguras de inserción"
    description="Obtenga información sobre cómo enviar notificaciones de inserción seguro a una aplicación de Azure. Ejemplos de código escritos en objetivo C y C#."
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
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

Este tutorial seguro inserción muestra cómo enviar una notificación de inserción de forma segura. El tutorial se basa en el tutorial de [Informar a los usuarios](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , por lo que debe completar los pasos de ese tutorial en primer lugar.

> [AZURE.NOTE] En este tutorial se asume que ha creado y configurado su centro de notificación, como se describe en [Introducción a la notificación Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Modificar el proyecto de iOS

Ahora que ha modificado su aplicación back-end para enviar el *identificador* de una notificación, debe cambiar su aplicación iOS para controlar la notificación y el back-end para recuperar el mensaje seguro para que se muestre de devolución de llamada.

Para ello, se debe escribir la lógica para recuperar el contenido seguro desde la aplicación back-end.

1. En **AppDelegate.m**, asegúrese de que los registros de aplicación para las notificaciones silenciosos por lo que procesa el identificador de notificación envían desde el servidor. Agregue la opción **UIRemoteNotificationTypeNewsstandContentAvailability** en didFinishLaunchingWithOptions:

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. En su **AppDelegate.m** agregar una sección de implementación en la parte superior con la declaración siguiente:

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

3. A continuación, agregue el código siguiente, sustituir el marcador de posición en la sección de implementación `{back-end endpoint}` con el punto final para el fondo obtenido anteriormente:

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. Ahora que tenemos que controle la notificación entrante y use el método anterior para recuperar el contenido para mostrar. En primer lugar, hay que activar la aplicación iOS para que se ejecute en segundo plano cuando se recibe una notificación de inserción. En **XCode**, seleccione el proyecto de aplicación en el panel izquierdo, haga clic en el destino de la aplicación principal en la sección **destinos** desde el panel central.

5. A continuación, haga clic en la pestaña **capacidades** en la parte superior del panel central de y marque la casilla **Notificaciones remoto** .

    ![][IOS1]


6. En **AppDelegate.m** agregue el siguiente método para controlar las notificaciones de inserción:

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    Tenga en cuenta que es preferible para controlar los casos de la propiedad de encabezado de autenticación que falta o rechace por el back-end. La administración de estos casos específica dependen principalmente de la experiencia del usuario de destino. Es una opción Mostrar una notificación con un mensaje genérico para autenticar al usuario para recuperar la notificación.

## <a name="run-the-application"></a>Ejecute la aplicación

Para ejecutar la aplicación, haga lo siguiente:

1. En XCode, ejecute la aplicación en un dispositivo iOS físico (push notificaciones no funcionarán en el simulador).

2. En la interfaz de usuario de aplicación de iOS, escriba un nombre de usuario y contraseña. Pueden ser cualquier cadena, pero deben ser el mismo valor.

3. En el interfaz de usuario de aplicación de iOS, haga clic en **iniciar sesión**. A continuación, haga clic en **Enviar push**. Ahora debe aparecer la notificación segura que se muestra en el centro de notificaciones.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
