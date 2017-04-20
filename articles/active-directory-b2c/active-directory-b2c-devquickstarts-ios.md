<properties
    pageTitle="Azure Active B2C de directorio: Llame un web API desde una aplicación de iOS usar bibliotecas de terceros | Microsoft Azure"
    description="En este artículo le mostrará cómo crear una aplicación de 'lista de tareas pendientes' iOS que llama a un sitio web de Node.js API utilizando tokens portador de OAuth 2.0 con una biblioteca de terceros"
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

< etiquetas ms.service= "b2c de directorio de active" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic= "héroe artículo"

    ms.date="07/26/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c--call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>B2C de Azure AD: Llamar a un web API desde una aplicación de iOS mediante una biblioteca de terceros

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

La plataforma de identidad de Microsoft utiliza estándares abiertos como OAuth2 y OpenID conectarse. Esto permite a los desarrolladores aprovechar cualquier biblioteca que desean integrar con nuestros servicios. Ayudar a los desarrolladores en el uso de nuestra plataforma con otras bibliotecas hemos escrito algunos tutoriales como este para demostrar cómo configurar las bibliotecas de terceros para conectarse a la plataforma de identidad de Microsoft. Mayoría de las bibliotecas que implementan [la especificación de RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) podrá conectarse a la plataforma de Microsoft Identity.


Si es nuevo en OAuth2 o conectar OpenID no gran parte de esta configuración de ejemplo podría ser mucho sentido para usted. Se recomienda que mira una breve [Descripción general del protocolo que nos hemos documentado aquí](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Algunas características de nuestra plataforma que tienen una expresión en estos estándares, como la administración de directivas de acceso condicional y Intune, requieren que use nuestro Abrir origen bibliotecas de identidad de Microsoft Azure. 
   
No todas las características y escenarios de Azure Active Directory son compatibles con la plataforma B2C.  Para determinar si debe usar la plataforma B2C, lea acerca de [las limitaciones de B2C](active-directory-b2c-limitations.md).


## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C

Antes de poder usar Azure AD B2C, debe crear un directorio o de inquilinos. Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos y más. Si no tiene uno ya, [Cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Crear una aplicación

A continuación, deberá crear una aplicación en el directorio B2C. Esto le da información de Azure AD que necesita para comunicarse de forma segura con la aplicación. La aplicación y la web API están representados por un solo **Identificador de la aplicación** en este caso, debido a que incluyen una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir un **dispositivo móvil** en la aplicación.
- Copie el **Identificador de la aplicación** que se asigna a la aplicación. También necesitará esto más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define por una [Directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene una experiencia de identidad: un signo combinado de entrada y de suscripción. Debe crear esta directiva de cada tipo, como se describe en el [artículo de referencia de la directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando se crea la directiva, asegúrese de:

- Seleccione el **nombre para mostrar** y atributos de suscripción en la directiva.
- Elija el **nombre para mostrar** y el **Identificador de objeto de** notificaciones de la aplicación en cada directiva. Puede elegir otras reclamaciones también.
- Copiar el **nombre** de cada directiva después de crearlo. Debe tener el prefijo `b2c_1_`.  Más adelante tendrá el nombre de la directiva.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de haber creado las directivas, está listo para crear la aplicación.


## <a name="download-the-code"></a>Descargar el código

El código de este tutorial se mantiene [en GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  Para seguir a lo largo, puede [Descargar la aplicación como un .zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) o copia de la misma:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

O simplemente descargar el código completado y empezar de inmediato: 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>Descargar el nxoauth2 de la biblioteca de terceros e iniciar un área de trabajo

En este tutorial usamos el OAuth2Client de GitHub, una biblioteca de OAuth2 para Mac OS X & iOS (cacao y cacao táctil). Esta biblioteca se basa en borrador 10 de la especificación de OAuth2. Implementa el perfil de la aplicación nativa y es compatible con el extremo de autorización del usuario final. Estos son todo lo que necesita en orden a integrado y con la plataforma de identidad de Microsoft.

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>Agregar la biblioteca para el proyecto mediante CocoaPods

CocoaPods es un administrador de dependencia para proyectos de Xcode. Administra automáticamente los pasos de instalación anteriores.

```
$ vi Podfile
```
Para este podfile, agregue lo siguiente:

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Cargar ahora la podfile con cocoapods. Esta opción creará una nueva área de trabajo XCode que se cargará.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>La estructura del proyecto

Tenemos la estructura siguiente configurar para que el proyecto en el esquema:

* **Vista patrón** con un panel de tareas
* Una **Vista de tareas de agregar** para los datos sobre la tarea seleccionada
* **Vista de inicio de sesión** que permite a un usuario iniciar sesión en la aplicación.

Se desplazará en a varios archivos en el proyecto para agregar la autenticación. Otras partes del código como el código de visual no es germane de identidad y se proporcionan para usted.

## <a name="create-the-settingsplist-file-for-your-application"></a>Crear la `settings.plist` archivo de la aplicación

Es más fácil de configurar la aplicación si tenemos una ubicación centralizada para colocar los valores de configuración. También le ayuda entender lo que hace cada configuración en la aplicación. Se aprovechar la *Lista de propiedades* como una manera de proporcionar estos valores a la aplicación.

* Crear o abrir la `settings.plist` de archivos en `Supporting Files` en el área de trabajo de la aplicación

* Escriba los valores siguientes (vaya por ellas en detalle próximamente)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Volvamos en estos detalladamente.


Para `authURL`, `loginURL`, `bhh`, `tokenURL` observará necesite rellenar el nombre del inquilino. Este es el nombre del inquilino de su inquilino B2C que se ha asignado a usted. Por ejemplo, `kidventusb2c.onmicrosoft.com`. Si usa nuestro Abrir origen bibliotecas de identidad de Microsoft Azure se ¿desplegable estos datos utilizando el extremo de metadatos. Hemos hecho el trabajo de extraer estos valores para usted.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

La `keychain` valor es el contenedor de la biblioteca de NXOAuth2Client se usan para crear una cadena de claves para almacenar los símbolos. Si desea obtener SSO a través de varias aplicaciones puede especificar la misma cadena de claves en cada una de las aplicaciones así como solicita el uso de esa llaves en su entitements XCode. Se trata en la documentación de Apple.

La `<policy name>` al final de cada dirección URL son los lugares donde desea colocar la directiva que creó anteriormente. La aplicación llamará estas directivas según el flujo.

La `taskAPI` es el extremo del resto llamamos con el token B2C: agregar tareas o consulta existente. Esto se ha configurado específicamente para este ejemplo. No es necesario cambiar para el ejemplo funcione.

El resto de los valores siguientes se requieren para usar la biblioteca y crear simplemente lugares para realizar los valores para el contexto.

Ahora que tenemos la `settings.plist` archivo creado, es necesario código leerlo.

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>Configurar una clase AppData leer nuestra configuración

Vamos a hacer un archivo sencillo que simplemente analiza nuestra `settngs.plist` archivo creamos encima y realizar las configuraciones que estará disponible en el futuro en cualquier clase. Puesto que no desea crear una nueva copia de los datos cada vez que se solicita una clase, se utilizará un modelo Singleton y solo devolver la misma instancia que se crea cada vez que se realiza una solicitud para la configuración

* Crear un `AppData.h` archivo:

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Crear un `AppData.m` archivo:

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Ahora podemos obtener fácilmente en nuestros datos mediante la llamada a `  AppData *data = [AppData getInstance];` en cualquiera de las clases como se verá más adelante.



## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>Configurar la biblioteca de NXOAuth2Client en su AppDelegate

La biblioteca de NXOAuthClient requiere algunos valores para obtener la configuración. Una vez completado puede usar el token de obtenido para llamar a la API de REST. Dado que se sabe que el `AppDelegate` llamará a cualquier momento, cargar la aplicación tiene sentido incluimos nuestros valores de configuración de ese archivo.
* Abrir `AppDelegate.m` archivo

* Importar algunos archivos de encabezado que se utilizará más adelante.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Agregar la `setupOAuth2AccountStore` método en la AppDelegate

Se necesita crear una AccountStore y luego lo fuente los datos que se acaba de leer de la `settings.plist` archivo.

Hay algunas cosas que debería considerar relacionada con el servicio de B2C en este momento que le será más comprensible a este código:


1. Azure AD B2C utiliza la *Directiva* proporcionada por los parámetros de consulta a la solicitud de servicio. Esto permite Azure Active Directory para que actúe como un servicio independiente para la aplicación. Para proporcionar estos parámetros necesitamos proporcionar extra de consulta la `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` método con parámetros de nuestra directiva personalizada. 

2. Azure AD B2C utiliza ámbitos de gran parte del mismo modo que otros servidores de OAuth2. Sin embargo, dado que el uso de B2C es tanto sobre la autenticación de un usuario como acceso a los recursos son necesarios algunos ámbitos estrictamente en orden para el flujo de trabajo correctamente. Esta es la `openid` ámbito. Nuestra identidad Microsoft SDK proporcionan automáticamente la `openid` ámbito por usted, no verá que en nuestra configuración SDK. Sin embargo, puesto que estamos usando una biblioteca de terceros, es necesario especificar este ámbito.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
A continuación, asegúrese de que llama en el AppDelegate en `didFinishLaunchingWithOptions:` método. 

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>Crear un `LoginViewController` clase que usamos para controlar las solicitudes de autenticación

Usamos una vista Web inicio de sesión de cuenta. Esto nos permite solicitar al usuario factores adicionales como mensaje de texto SMS (si está configurado) o devolver mensajes de error al usuario. Aquí se configure hasta la vista Web y, a continuación, escribir más adelante el código para controlar las devoluciones de llamada que se repite en la vista Web del servicio de identidad de Microsoft.

* Crear un `LoginViewController.h` clase

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Vamos a crear cada uno de estos métodos a continuación.

> [AZURE.NOTE] 
    Asegúrese de que se enlazan la `loginView` a la vista Web real que está dentro de su guión gráfico. En caso contrario, no tiene una vista Web que puede aparecer cuando es el momento para autenticar.

* Crear un `LoginViewController.m` clase

* Agregar algunas variables lleven estado mientras se autentica

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Reemplazar los métodos de vista Web para controlar la autenticación

Es necesario indicar a la vista Web el comportamiento que queremos cuando un usuario necesita iniciar sesión como se indicó anteriormente. Simplemente, puede cortar y pegar el código siguiente.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Escribir código para controlar el resultado de la solicitud de OAuth2

Es necesario código que controle la redirectURL que procede la vista Web. Si es correcta, se volverá a intentarlo. Mientras tanto la biblioteca le proporcionará el error que puede ver en la consola o controlador de forma asincrónica. 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Configurar las fábricas de notificación.

Creamos el mismo método que hizo en la `AppDelegate` anteriormente, pero esta vez, agregaremos algunos `NSNotification`s a Díganos qué está sucediendo en nuestro servicio. Se configura un observador que nos indique cuando cambia cualquier aspecto con el token. Una vez que se obtiene el token se devuelven el usuario volver a la `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Agregar código que controla el usuario cada vez que se inicia una solicitud para nativo de inicio de sesión

Vamos a crear un método que se llamará siempre que tenemos una solicitud de autenticación. Este será el método que realmente crea una vista Web

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Por último, vamos a llamar a todos estos métodos hemos escrito anteriormente cada vez el `LoginViewController` carga. Para ello mediante la adición de estos métodos para nuestro `viewDidLoad` método Apple nos da

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Ahora se realizan con la creación de la manera principal que podrá interactuar con la aplicación para iniciar sesión. Una vez nos hemos iniciado sesión, debemos usar nuestros tokens que hemos recibido. Para que vamos a crear código auxiliar que llame a las API de REST nos con esta biblioteca.


## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>Crear un `GraphAPICaller` clase para controlar las solicitudes a una API de REST

Tenemos una configuración de carga cada vez que se carga nuestra aplicación. Ahora es necesario hacer algo con él una vez que tenemos un símbolo. 

* Crear un `GraphAPICaller.h` archivo

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Ver en este código que crearemos dos métodos: uno para obtener las tareas de una API y otro para agregar tareas a la API.

Ahora que hemos creado nuestra interfaz, vamos a agregar la implementación real:

* Crear un`GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>Ejecute la aplicación de ejemplo

Por último, crear y ejecutar la aplicación en Xcode. Registrarse o iniciar sesión la aplicación y crear tareas para un usuario que ha iniciado sesión. Cerrar la sesión y vuelva a iniciarla como un usuario diferente y crear tareas para ese usuario.

Observe que las tareas están almacenados por usuario en la API, porque la API extrae la identidad del usuario desde el token de acceso que recibe.


## <a name="next-steps"></a>Pasos siguientes

Ahora puede mover en temas B2C más avanzados. Puede probar:

[Llamar a un sitio web de Node.js API desde una aplicación web de Node.js]()

[Personalizar la UX para una aplicación B2C]()
