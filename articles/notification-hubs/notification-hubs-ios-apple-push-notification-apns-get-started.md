<properties
    pageTitle="Enviar notificaciones de inserción para iOS con Hubs de notificación de Azure | Microsoft Azure"
    description="En este tutorial, aprenderá a usar Hubs de notificación de Azure para enviar notificaciones de inserción a una aplicación de iOS."
    services="notification-hubs"
    documentationCenter="ios"
    keywords="inserción de notificación, las notificaciones de inserción, notificaciones de inserción de ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>Enviar notificaciones de inserción para iOS con Hubs de notificación de Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Información general

> [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

En este tutorial se muestra cómo usar Hubs de notificación de Azure para enviar notificaciones de inserción a la aplicación iOS. Debe crear una aplicación de iOS en blanco que recibe las notificaciones de inserción mediante el [servicio de notificación de inserción de Apple (APN)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). 

Cuando haya terminado, podrá usar su centro de notificación para difundir las notificaciones de inserción a todos los dispositivos que ejecutan la aplicación.

## <a name="before-you-begin"></a>Antes de empezar

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

El código finalizado para este tutorial se puede encontrar [en GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

##<a name="prerequisites"></a>Requisitos previos

Este tutorial requiere lo siguiente:

+ [Servicios para dispositivos móviles iOS versión 1.2.4 SDK]
+ Versión más reciente de [Xcode]
+ Un iOS 8 (o una versión posterior)-dispositivo compatible
+ Miembros del [Programa de desarrollador de Apple](https://developer.apple.com/programs/) .

   > [AZURE.NOTE] Debido a los requisitos de configuración para las notificaciones de inserción, debe implementar y probar las notificaciones de inserción en un dispositivo físico iOS (iPhone o iPad) en lugar de iOS Simulator.

Realizar este tutorial es un requisito previo para todos los otros tutoriales Hubs de notificación para las aplicaciones de iOS.

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##<a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configurar su centro de notificación de notificaciones de inserción de iOS

Esta sección le guiará a través de la creación de un nuevo concentrador de notificación y configurar la autenticación con APN utilizando el certificado push **. p12** que ha creado. Si desea usar un concentrador de notificación que ya ha creado, vaya al paso 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li>
<p>Haga clic en el botón de <b>Servicios de notificación</b> en el módulo de <b>configuración</b> y luego seleccione <b>Apple (APN)</b>. Haga clic en <b>Cargar certificado</b> y seleccione el archivo <b>. p12</b> que exportó anteriormente. Asegúrese de que también especifique la contraseña correcta.</p>
<p>Asegúrese de seleccionar el modo de <b>recinto de seguridad</b> ya que es para el desarrollo. Use únicamente la <b>producción</b> si desea enviar notificaciones de inserción a los usuarios que han comprado la aplicación desde la tienda.</p>
</li>
</ol>
&emsp;&emsp;![Configurar APN en el Portal de Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![Configurar certificación APN en el Portal de Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)



El concentrador de notificación ahora está configurado para trabajar con APN y tiene las cadenas de conexión para la aplicación de registrar y enviar notificaciones de inserción.

##<a name="connect-your-ios-app-to-notification-hubs"></a>Conectar la aplicación iOS a Hubs de notificación

1. En Xcode, cree un nuevo proyecto de iOS y seleccione la plantilla de **Aplicación de la vista única** .

    ![Xcode - vista única aplicación][8]

2. Al establecer las opciones para el nuevo proyecto, asegúrese de usar el mismo **Nombre de producto** e **El identificador de organización** que utiliza al establecer el identificador de paquete previamente en el portal de programadores de Apple.

    ![Xcode - opciones de proyecto][11]

3. En **destinos**, haga clic en el nombre del proyecto, haga clic en la pestaña **Configuración de generación** y expandir la **Identidad de inicio de sesión de código**y, a continuación, en **Depurar**, establezca su identidad de firma de código. Alternar **niveles** de **básico** a **todos los**y establezca **El aprovisionamiento de perfil** al perfil de aprovisionamiento que creó anteriormente.

    Si no ve el nuevo perfil de aprovisionamiento que creó en Xcode, intente actualizar los perfiles de su identidad de firma. Haga clic en **Xcode** en la barra de menús, haga clic en **Preferencias**, haga clic en la pestaña **cuenta** , haga clic en el botón **Ver detalles** , haga clic en la identidad de firma y, a continuación, haga clic en el botón Actualizar en la esquina inferior derecha.

    ![Xcode - perfil de aprovisionamiento][9]

4. Descargue los [Servicios para dispositivos móviles iOS SDK versión 1.2.4] y descomprima el archivo. En Xcode, haga clic en el proyecto y haga clic en la opción de **Agregar archivos a** agregar la carpeta **WindowsAzureMessaging.framework** a su proyecto Xcode. Seleccione **Copiar elementos si es necesario**y, a continuación, haga clic en **Agregar**.

    >[AZURE.NOTE] Los centros de notificación SDK no admite bitcode en Xcode 7.  Debe establecer **Bitcode habilitar** a **No** en las **Opciones de compilación** para el proyecto.

    ![Descomprima el SDK de Azure][10]

5. Agregar un nuevo archivo de encabezado al proyecto denominado `HubInfo.h`. Este archivo contiene las constantes para su centro de notificación.  Agregue las siguientes definiciones y reemplace los marcadores de posición literal de cadena con el *nombre del concentrador* y *DefaultListenSharedAccessSignature* que anotó anteriormente.

        #ifndef HubInfo_h
        #define HubInfo_h
        
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
        
        #endif /* HubInfo_h */

6. Abrir su `AppDelegate.h` archivo agregue las siguientes directivas de importación:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
        
7. En el `AppDelegate.m file`, agregue el código siguiente en la `didFinishLaunchingWithOptions` método basado en su versión de iOS. Este código registra el controlador de dispositivo con APN:

    Para iOS 8:

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    Para las versiones de iOS anteriores a 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


8. En el mismo archivo, agregue los métodos siguientes. Este código se conecta al concentrador de notificación utilizando la información de conexión especificada en HubInfo.h. A continuación, proporciona el token de dispositivo en el hub de notificación para que el concentrador de notificación puede enviar notificaciones:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }


9. En el mismo archivo, agregue el siguiente método para mostrar un **UIAlert** si la notificación se recibe mientras la aplicación esté activa:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. Crear y ejecutar la aplicación en el dispositivo para comprobar que no hay ningún error.

## <a name="send-test-push-notifications"></a>Enviar notificaciones de inserción de prueba


Puede probar recibir notificaciones en su aplicación mediante el envío de notificaciones de inserción en el [Portal de Azure] a través de la sección de **solución de problemas** en el módulo de concentrador (use la opción *Probar enviar* ).

![Portal de Azure - enviar de prueba][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="optional-send-push-notifications-from-the-app"></a>(Opcional) Enviar notificaciones de inserción de la aplicación

>[AZURE.IMPORTANT] En este ejemplo de enviar notificaciones de la aplicación cliente se proporciona con fines de aprendizaje. Dado que esto requerirá la `DefaultFullSharedAccessSignature` para presentar en la aplicación de cliente, expone su centro de notificación para el riesgo de que un usuario puede tener acceso para enviar notificaciones no autorizadas a sus clientes.

Si desea enviar las notificaciones de inserción de una aplicación, esta sección proporciona un ejemplo de cómo hacer esto mediante la interfaz REST.

1. En Xcode, abra `Main.storyboard` y agregue los siguientes componentes de interfaz de usuario de la biblioteca de objetos para permitir al usuario enviar notificaciones de inserción en la aplicación:

    - Una etiqueta con ningún texto de etiqueta. Se utilizará para el informe de errores de envío de notificaciones. La propiedad de **líneas** se debe establecer a **0** para que hará automáticamente tamaño limitado a la derecha y los márgenes de la izquierda y la parte superior de la vista.
    - Establece un campo de texto con texto de **marcador de posición** para **Escribir el mensaje de notificación**. Restringir el campo justo debajo de la etiqueta como se muestra a continuación. Establezca el controlador de vista como delegado de salida.
    - Un botón titulado **Enviar notificación** limitado justo debajo del campo de texto y en el centro horizontal.

    La vista debería tener el siguiente aspecto:

    ![Diseñador de Xcode][32]


2. [Agregar salidas](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) para el campo de etiqueta y texto conectado a la vista y actualizar su `interface` definición para admitir `UITextFieldDelegate` y `NSXMLParserDelegate`. Agregue las declaraciones de tres propiedad que se muestra a continuación para ayudar a admite llamar a la API de REST y analizar la respuesta.

    El archivo ViewController.h debe tener un aspecto como sigue:

        #import <UIKit/UIKit.h>

        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }

        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;

        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;

        @end

3. Abrir `HubInfo.h` y agregue las constantes que se usará para enviar notificaciones a su centro. Reemplace el literal de cadena de marcador de posición con la cadena de conexión *DefaultFullSharedAccessSignature* real.

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. Agregue las siguientes `#import` instrucciones para su `ViewController.h` archivo.

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. En `ViewController.m` agregue el código siguiente a la implementación de la interfaz. Este código analiza la cadena de conexión *DefaultFullSharedAccessSignature* . Como se mencionó en la [referencia de la API de REST](http://msdn.microsoft.com/library/azure/dn495627.aspx), esta información analizada se utilizará para generar un token SA para el encabezado de la solicitud de **autorización** .

        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;

        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;

            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];

                @throw parseException;
            }

            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }

6. En `ViewController.m`, actualizar la `viewDidLoad` método para analizar la cadena de conexión cuando se cargue la vista. Agregar los métodos de utilidad, que se muestra a continuación, para la implementación de la interfaz.  


        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





7. En `ViewController.m`, agregue el código siguiente a la implementación de la interfaz para generar el token de autorización de SA que se proporcionan en el encabezado de **autorización** , como se mencionó en la [Referencia de la API de REST](http://msdn.microsoft.com/library/azure/dn495627.aspx).

        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;

            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }

            return token;
        }


8. Botón CTRL + arrastre desde la **Notificación de enviar** a `ViewController.m` para agregar una acción denominada **SendNotificationMessage** para el evento **Touch hacia abajo** . El método Update con el siguiente código para enviar la notificación utilizando la API de REST.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }

        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];

            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];

            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }


9. En `ViewController.m`, agregue el siguiente método de delegado para admitir cerrar el teclado para el campo de texto. Presione CTRL y arrastre desde el campo de texto en el icono de controlador de vista en el Diseñador de la interfaz para configurar el controlador de vista como delegado de salida.

        //===[ Implement UITextFieldDelegate methods ]===

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }


10. En `ViewController.m`, agregue los siguientes métodos de delegado para admitir analizar la respuesta usando `NSXMLParser`.

        //===[ Implement NSXMLParserDelegate methods ]===

        -(void)parserDidStartDocument:(NSXMLParser *)parser
        {
            self.statusResult = @"";
        }

        -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
            namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
            attributes:(NSDictionary *)attributeDict
        {
            NSString * element = [elementName lowercaseString];
            NSLog(@"*** New element parsed : %@ ***",element);

            if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
            {
                self.currentElement = element;
            }
        }

        -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
        {
            self.statusResult = [self.statusResult stringByAppendingString:
                [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
        }

        -(void)parserDidEndDocument:(NSXMLParser *)parser
        {
            // Set the status label text on the UI thread
            dispatch_async(dispatch_get_main_queue(),
            ^{
                [self.sendResults setText:self.statusResult];
            });
        }



11. Generar el proyecto y compruebe que no hay ningún error.


> [AZURE.NOTE] Si se produce un error de generación de Xcode7 acerca del soporte técnico de bitcode, debe cambiar la **Configuración de generación** > **Bitcode habilitar (ENABLE_BITCODE)** a **NO** en Xcode. El SDK de Hubs de notificación no se admite actualmente bitcode. 

Puede encontrar las cargas de notificación en la Apple [Local y la Guía de programación de notificaciones de inserción].


##<a name="checking-if-your-app-can-receive-push-notifications"></a>Comprobar si la aplicación puede recibir notificaciones de inserción

Para probar las notificaciones de inserción en iOS, debe implementar la aplicación en un dispositivo iOS física. No puede enviar notificaciones de inserción de Apple mediante el iOS Simulator.

1. Ejecute la aplicación y compruebe que el registro se realiza correctamente y, a continuación, haga clic en **Aceptar**.

    ![Prueba de registro de notificación de inserción de aplicación de iOS][33]

2. Puede enviar una notificación de inserción de prueba desde el [Portal de Azure], como se describió anteriormente. Si ha agregado código para enviar notificaciones de inserción en la aplicación, toque en el campo de texto para escribir un mensaje de notificación. A continuación, presione el botón **Enviar** en el teclado o el botón **Enviar notificación** en la vista para enviar el mensaje de notificación.

    ![Prueba de enviar de notificación de inserción de aplicación de iOS][34]

3. La notificación de inserción se envía a todos los dispositivos que están registrados para recibir las notificaciones del concentrador de notificación determinado.

    ![Prueba de recibir de notificación de inserción de aplicación de iOS][35]


##<a name="next-steps"></a>Pasos siguientes

En este sencillo ejemplo, las notificaciones de inserción se difusión a todos los dispositivos iOS registrados. Le recomendamos como un paso en su aprendizaje que continuar con el tutorial de [Azure notificación Hubs informar a los usuarios para iOS con .NET back-end] , que le guiará en la creación de un back-end para enviar notificaciones de inserción con etiquetas. 

Si desea agrupar los usuarios por grupos de interés, puede mover además al tutorial [Hubs de notificación de uso para enviar noticias] . 

Para obtener información general sobre Hubs de notificación, consulte [Guía de Hubs de notificación].



<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Servicios para dispositivos móviles iOS versión 1.2.4 SDK]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Guía de Hubs de notificación]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure notificación Hubs informar a los usuarios para iOS con .NET back-end]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Usar Hubs de notificación para enviar noticias]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local y la Guía de programación de notificación de inserción]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Portal de Azure]: https://portal.azure.com