<properties
    pageTitle="Azure compromiso móvil iOS SDK alcance integración | Microsoft Azure"
    description="Últimas actualizaciones y procedimientos para iOS SDK para Azure Mobile contratación"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-reach-on-ios"></a>Cómo integrar el alcance de contratación en iOS

Debe seguir los procedimientos de integración descritos en [cómo integrar compromiso en iOS documento](mobile-engagement-ios-integrate-engagement.md) antes de seguir a esta guía.

Esta documentación requiere XCode 8. Si realmente depende XCode 7 puede usar [iOS compromiso SDK v3.2.4](https://aka.ms/r6oouh). Hay un error conocido en esta versión anterior mientras se ejecuta en dispositivos iOS 10: las notificaciones del sistema no están activadas. Para corregir esto, deberá implementar la API desusada `application:didReceiveRemoteNotification:` en su aplicación delegar como sigue:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **No se recomienda esta solución** como este comportamiento, puede cambiar cualquier actualización de la versión de próximas iOS (incluso los pequeños) porque este iOS API es obsoleto. Debe cambiar a 8 XCode tan pronto como sea posible.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitar la aplicación recibir notificaciones de inserción silencioso

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

##<a name="integration-steps"></a>Pasos de integración

### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Integrar el SDK de contratación alcance en su proyecto de iOS

-   Agregar el sdk de alcance de su proyecto Xcode. En Xcode, vaya a **proyecto \> agregar al proyecto** y elija la `EngagementReach` carpeta.

### <a name="modify-your-application-delegate"></a>Modificar al delegado de la aplicación

-   En la parte superior de su archivo de implementación, importar el módulo alcance de compromiso:

        [...]
        #import "AEReachModule.h"

-   Método `applicationDidFinishLaunching:` o `application:didFinishLaunchingWithOptions:`, cree un módulo de alcance y pasar a la línea de inicialización compromiso existente:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
          AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
          [...]

          return YES;
        }

-   Modificar la cadena de **'icon.png'** con el nombre de la imagen que desee como el icono de notificación.
-   Si desea usar la opción *valor distintivo de actualización* en las campañas de alcance o si desea usar push nativo \<nativa y formato de SaaS/alcance API/campaña de inserción\> campañas, debe permitir el alcance módulo administrar el icono de notificación de la propia (se desactiva automáticamente la notificación de la aplicación y también restablecer el valor almacenado por compromiso cada vez que la aplicación se ha iniciado o foregrounded). Para ello, agregue la línea siguiente después de la inicialización del módulo de alcance:

        [reach setAutoBadgeEnabled:YES];

-   Si desea controlar la inserción de datos de alcance, debe permitir que el delegado de la aplicación se ajustan a la `AEReachDataPushDelegate` protocolo. Agregue la línea siguiente después de la inicialización del módulo de alcance:

        [reach setDataPushDelegate:self];

-   A continuación, puede implementar los métodos `onDataPushStringReceived:` y `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` en el delegado de aplicación:

        -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
        {
           NSLog(@"String data push message with category <%@> received: %@", category, body);
           return YES;
        }

        -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
        {
           NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
           // Do something useful with decodedBody like updating an image view
           return YES;
        }

### <a name="category"></a>Categoría

El parámetro de categoría es opcional al crear una campaña de inserción de datos y permite filtrar los datos que inserta. Esto es útil si desea insertar diferentes tipos de `Base64` datos y desea identificar su tipo antes de analizar ellos.

**La aplicación está preparada para recibir y mostrar contenido alcance!**

##<a name="how-to-receive-announcements-and-polls-at-any-time"></a>Cómo recibir anuncios y sondeos en cualquier momento

Compromiso puede enviar notificaciones de alcance a los usuarios finales en cualquier momento mediante el servicio de notificación de inserción de Apple.

Para habilitar esta funcionalidad, debe preparar la aplicación para las notificaciones de inserción de Apple y modificar a su delegado de la aplicación.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Preparar la aplicación para las notificaciones de inserción de Apple

Siga la guía: [cómo preparar la aplicación para las notificaciones de inserción de Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Agregar el código de cliente necesario

*En este momento la aplicación debería tener un certificado de inserción de Apple registrado en el compromiso de front-end.*

Si ya no se realiza, debe registrar la aplicación para recibir notificaciones de inserción.

* Importar la `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>

* Agregue la línea siguiente cuando se inicia la aplicación (normalmente en `application:didFinishLaunchingWithOptions:`):

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

A continuación, debe proporcionar a compromiso el token de dispositivo devueltas por servidores de Apple. Esto se realiza en el método denominado `application:didRegisterForRemoteNotificationsWithDeviceToken:` en el delegado de aplicación:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Por último, debe informar el SDK de contratación cuando la aplicación recibe una notificación remota. Para ello, llame al método `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` en el delegado de aplicación:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [AZURE.NOTE] El método anterior se introdujo en iOS 7. Si se dirige a iOS < 7, asegúrese de que implementar el método `application:didReceiveRemoteNotification:` en su delegado la aplicación y la llamada `applicationDidReceiveRemoteNotification` en la EngagementAgent pasando nulo en lugar de la `handler` argumento:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] De forma predeterminada, contratación alcance controla la completionHandler. Si desea responder manualmente a la `handler` bloquear en su código, puede pasar nulo para la `handler` argumento y controlar la finalización bloquean usted mismo. Consulte la `UIBackgroundFetchResult` tipo de una lista de valores posibles.


### <a name="full-example"></a>Ejemplo completo

Este es un ejemplo completo de integración:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="if-you-have-your-own-unusernotificationcenterdelegate-implementation"></a>Si tiene su propia implementación de UNUserNotificationCenterDelegate

El SDK también tiene su propia implementación del protocolo UNUserNotificationCenterDelegate. Se usa el SDK para supervisar el ciclo de vida de notificaciones de contratación en dispositivos que ejecutan en iOS 10 o superior. Si el SDK detecta al delegado no usará su propia implementación porque puede haber solo un delegado UNUserNotificationCenter por la aplicación. Esto significa que tendrá que agregar la lógica de contratación a su propio delegado.

Hay dos maneras de lograrlo.

Enviando el delegado llama el SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

O mediante la herencia de la `AEUserNotificationHandler` clase

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [AZURE.NOTE] Puede determinar si una notificación proviene de contratación o no pasando su `userInfo` diccionario para el agente `isEngagementPushPayload:` método de clase.

##<a name="how-to-customize-campaigns"></a>Cómo personalizar campañas

### <a name="notifications"></a>Notificaciones

Existen dos tipos de notificaciones: las notificaciones de sistema y de la aplicación.

Las notificaciones del sistema se administran por iOS y no se pueden personalizar.

Notificaciones de la aplicación están compuestas de una vista que se agrega dinámicamente a la ventana de la aplicación actual. Esto se denomina una superposición de notificación. Las superposiciones de notificación son una buena opción para una rápida integración porque no requiere modificar cualquier vista en la aplicación.

#### <a name="layout"></a>Diseño

Para modificar el aspecto de las notificaciones de la aplicación, simplemente puede modificar el archivo `AENotificationView.xib` a sus necesidades, siempre y cuando tenga los valores de etiqueta y los tipos de las subvistas existentes.

De forma predeterminada, se presentan las notificaciones de la aplicación en la parte inferior de la pantalla. Si prefiere mostrarlas en la parte superior de la pantalla, editar el proporcionado `AENotificationView.xib` y cambiar la `AutoSizing` propiedad de la vista principal, por lo que se puede mantener en la parte superior de su superview.

#### <a name="categories"></a>Categorías

Cuando se modifica el diseño, modifique el aspecto de todas las notificaciones. Las categorías permiten definir varios aspectos destinadas (posiblemente comportamientos) para las notificaciones. Una categoría puede especificarse cuando se crea una campaña de alcance. Tenga en cuenta que categorías también le permiten personalizar anuncios y sondeos, que se describe más adelante en este documento.

Para registrar un controlador de categoría para las notificaciones, debe agregar una llamada una vez inicializado el módulo de alcance.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier`debe ser una instancia de un objeto que cumpla con el protocolo `AENotifier`.

Puede implementar los métodos de protocolo por usted mismo o puede volver a implementar la clase existente `AEDefaultNotifier` ya que realiza la mayoría del trabajo.

Por ejemplo, si desea redefinir la vista de notificación de una categoría específica, puede seguir este ejemplo:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Este sencillo ejemplo de categoría, se supone que tiene un archivo llamado `MyNotificationView.xib` en el paquete de aplicación principal. Si el método no se puede encontrar un correspondiente `.xib`, no se mostrará la notificación y compromiso dará como resultado un mensaje en la consola.

El archivo proporcionado plumilla debe respetar las siguientes reglas:

-   Solo debe contener una vista.
-   Subvistas deben tener los mismos tipos que las dentro del archivo plumilla proporcionado nombre`AENotificationView.xib`
-   Subvistas deberían tener las mismas etiquetas que las dentro de la proporcionada con el nombre de archivo de plumilla`AENotificationView.xib`

> [AZURE.TIP] Basta con copiar el archivo proporcionado plumilla, denominado `AENotificationView.xib`y empiece a trabajar desde allí. Pero tenga cuidado, la vista de este archivo plumilla está asociada a la clase `AENotificationView`. Esta clase redefinir el método `layoutSubViews` para mover y cambiar el tamaño de sus subvistas según el contexto. Desea reemplazar con un `UIView` o la clase de vista personalizada.

Si necesita más profunda personalización de las notificaciones (si desea, por ejemplo para cargar la vista directamente desde el código), se recomienda que eche un vistazo a la documentación de código y clase de origen proporcionado de `Protocol ReferencesDefaultNotifier` y `AENotifier`.

Tenga en cuenta que puede usar el mismo notificador de varias categorías.

También puede volver a definir el notificador predeterminado similar a esta:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Control de notificación

Cuando utiliza la categoría de forma predeterminada, se denominan algunos métodos de ciclo de vida en el `AEReachContent` a objeto estadísticas de informe y actualizar el estado de la campaña:

-   Cuando aparezca la notificación en la aplicación, la `displayNotification` método se denomina (que informa estadísticas) por `AEReachModule` si `handleNotification:` devuelve `YES`.
-   Si se descarta la notificación, la `exitNotification` se denomina método, se notifica estadístico y ahora se pueden procesar campañas siguientes.
-   Si se hace clic en la notificación, `actionNotification` es llamado, se notifica estadístico y se realiza la acción asociada.

Si su implementación de `AENotifier` que omita el comportamiento predeterminado, debe llamar a estos métodos de ciclo de vida por usted mismo. Los siguientes ejemplos muestran algunos casos donde se omite el comportamiento predeterminado:

-   No extender `AEDefaultNotifier`, por ejemplo, ¿ha implementado tratamiento de categoría desde cero.
-   Reemplazó `prepareNotificationView:forContent:`, asegúrese de asignar al menos `onNotificationActioned` o `onNotificationExited` a uno de sus I.U controles.

> [AZURE.WARNING] Si `handleNotification:` inicia una excepción, el contenido se elimina y `drop` es llamado, esto se notifica en estadísticas y ahora se pueden procesar campañas siguientes.

#### <a name="include-notification-as-part-of-an-existing-view"></a>Incluir notificación como parte de una vista existente

Las superposiciones son una buena opción para una rápida integración pero pueden ser a veces no adecuadas o pueden tener efectos secundarios no deseados.

Si no está satisfecho con el sistema de superposición en algunas de las vistas, puede personalizarlo para estas vistas.

Puede decidir incluir nuestro diseño de notificación en las vistas existentes. Para ello, hay dos estilos de implementación:

1.  Agregar la vista de notificación con el generador de interfaz

    -   Abrir el *Generador de la interfaz*
    -   Coloque un 320 x 60 (o 768 x 60 si se encuentra en iPad) `UIView` donde desea que aparezca la notificación de
    -   Establezca el valor de la etiqueta para esta vista: **36822491**

2.  Agregar la vista de notificación mediante programación. Basta con agregar el código siguiente cuando se ha inicializado la vista:

        UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
        notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
        [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG`macro se encuentra en `AEDefaultNotifier.h`.

> [AZURE.NOTE] Notificador predeterminado detecta automáticamente que el diseño de notificación incluido en esta vista y no agregará una superposición para el mismo.

### <a name="announcements-and-polls"></a>Anuncios y sondeos

#### <a name="layouts"></a>Diseños

Puede modificar los archivos `AEDefaultAnnouncementView.xib` y `AEDefaultPollView.xib` como mantener los valores de etiqueta y los tipos de las subvistas existentes.

#### <a name="categories"></a>Categorías

##### <a name="alternate-layouts"></a>Diseños alternativos

Como las notificaciones, categoría de la campaña puede usarse para diseños alternativos para los anuncios y sondeos.

Para crear una categoría para un anuncio, debe extender **AEAnnouncementViewController** y registrar una vez inicializado el módulo de alcance:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] Cada vez que un usuario ha hecho clic en una notificación para un anuncio con la categoría "Mi\_categoría", el controlador de vista registrados (en ese caso `MyCustomAnnouncementViewController`) se iniciará llamando al método `initWithAnnouncement:` y la vista se agregará a la ventana de la aplicación actual.

En la implementación de la `AEAnnouncementViewController` clase tendrá que leer la propiedad `announcement` iniciar su subvistas. Considere el ejemplo siguiente, donde dos etiquetas se inicializado con `title` y `body` propiedades de la `AEReachAnnouncement` clase:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Si no desea cargar las vistas pero desea volver a utilizar el diseño de vista de presentación predeterminado, puede crear el controlador de vista personalizada extiende la clase `AEDefaultAnnouncementViewController`. Duplicar en ese caso, el archivo de plumilla `AEDefaultAnnouncementView.xib` y cambie el nombre de modo que se puede cargar el controlador de vista personalizada (para un controlador denominado `CustomAnnouncementViewController`, debe llamar a su archivo de plumilla `CustomAnnouncementView.xib`).

Para reemplazar la categoría predeterminada de anuncios, simplemente registrar el controlador de vista personalizada para la categoría definida en `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Sondeos pueden personalizarse de la misma manera:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

En este momento, la proporcionado `MyCustomPollViewController` debe extender `AEPollViewController`. O puede ampliar desde el controlador de forma predeterminada: `AEDefaultPollViewController`.

> [AZURE.IMPORTANT] No se olvide de llamar a uno `action` (`submitAnswers:` para los controladores de vista personalizada de sondeo) o `exit` método antes de que el controlador de vista se descarta. En caso contrario, estadísticas no irá (es decir, sin análisis en la campaña) y no se notificará más campañas de lo que es importante siguientes hasta que se reinicia el proceso de aplicación.

##### <a name="implementation-example"></a>Ejemplo de implementación

En esta implementación se carga la vista de presentación personalizada desde un archivo externo xib.

Al igual que para la personalización avanzada de notificación, se recomienda para buscar el código fuente de la implementación estándar.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
