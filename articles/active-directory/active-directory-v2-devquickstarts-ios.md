<properties
    pageTitle="IOS de versión 2.0 de Azure AD aplicación | Microsoft Azure"
    description="Cómo crear una aplicación iOS que inicia sesión en cuentas de trabajo o escuela y los usuarios con ambos cuenta personal de Microsoft mediante el uso de bibliotecas de terceros."
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="brandwe"/>

# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Agregar inicio de sesión a una aplicación de iOS con una biblioteca de terceros con la API de gráfico con el extremo de la versión 2.0

La plataforma de identidad de Microsoft utiliza estándares abiertos como OAuth2 y OpenID conectarse. Los programadores pueden utilizar cualquier biblioteca que desean integrarlo con nuestros servicios. Para ayudar a los desarrolladores usar nuestra plataforma con otras bibliotecas, hemos escrito algunos tutoriales como este para demostrar cómo configurar las bibliotecas de terceros para conectarse a la plataforma de identidad de Microsoft. Mayoría de las bibliotecas que implementan [la especificación de RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) puede conectarse a la plataforma de identidad de Microsoft.

Con la aplicación que crea en este tutorial, los usuarios pueden iniciar sesión en su organización y buscar para que otras personas de su organización mediante la API de gráfico.

Si es nuevo en OAuth2 o OpenID conectarse, gran parte de esta configuración de ejemplo puede no sentido para usted. Se recomienda que lea [protocolos versión 2.0: flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols-oauth-code.md) para el fondo.


> [AZURE.NOTE]
    Algunas características de nuestra plataforma que tienen una expresión en los estándares OAuth2 o OpenID conectar, como acceso condicional y administración de directivas de Intune requerir utilizar nuestro Abrir origen bibliotecas de identidad de Microsoft Azure.

El punto final de la versión 2.0 no admite todas las características y escenarios de Azure Active Directory.

> [AZURE.NOTE]
    Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).

## <a name="download-code-from-github"></a>Descargar el código de GitHub
El código de este tutorial se mantiene [en GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Para seguir a lo largo, puede [descargar el esquema de la aplicación como un .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) o clonar la estructura:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

También puede descargar los datos de ejemplo y empezar ahora mismo:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Registrar una aplicación
Crear una nueva aplicación en el [portal de registro de aplicación](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o siga los pasos detallados a [cómo registrar una aplicación con el extremo de la versión 2.0](active-directory-v2-app-registration.md).  Asegúrese de que:

- Copie el **Identificador de la aplicación** que está asignada a su aplicación porque lo necesitará pronto.
- Agregue la plataforma **Mobile** para su aplicación.
- Copie la **Redirección URI** desde el portal. Debe usar el valor predeterminado de `urn:ietf:wg:oauth:2.0:oob`.


## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Descargar la biblioteca de NXOAuth2 de terceros y crear un área de trabajo

En este tutorial, usará el OAuth2Client de GitHub, que es una biblioteca de OAuth2 para Mac OS X y iOS (cacao y sus táctil). Esta biblioteca se basa en borrador 10 de la especificación de OAuth2. Implementa el perfil de la aplicación nativa y es compatible con el extremo de la autorización del usuario. Estos son todo lo que se debe integrar con la plataforma de identidad de Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Agregar la biblioteca a su proyecto mediante CocoaPods

CocoaPods es un administrador de dependencia para proyectos de Xcode. Administra automáticamente los pasos de instalación anterior.

```
$ vi Podfile
```
1. Para este podfile, agregue lo siguiente:

    ```
     platform :ios, '8.0'

     target 'QuickStart' do

     pod 'NXOAuth2Client'

     end
    ```

2. Cargar el podfile mediante CocoaPods. Se creará un área de trabajo Xcode que se cargará.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Explorar la estructura del proyecto

La estructura siguiente está configurada para que el proyecto en el esquema:

- Una vista de patrón con una búsqueda UPN
- Una vista detallada de los datos sobre el usuario seleccionado
- Una vista de inicio de sesión donde un usuario puede iniciar sesión la aplicación para el gráfico de la consulta

Se moverá a varios archivos en el esquema para agregar autenticación. Otras partes del código, como el código de visual no pertenecen a la identidad, pero se proporcionan para usted.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Configurar el archivo settings.plst en la biblioteca

-   En el proyecto Tutorial rápido, abra la `settings.plist` archivo. Reemplace los valores de los elementos de la sección para reflejar los valores que se usan en el portal de Azure. El código hará referencia a estos valores cada vez que usa la biblioteca de autenticación de Active Directory.
    -   La `clientId` es el identificador de cliente de la aplicación que ha copiado desde el portal.
    -   La `redirectUri` es la dirección URL de redirección que proporciona el portal.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Configurar la biblioteca de NXOAuth2Client en su LoginViewController

La biblioteca de NXOAuth2Client requiere algunos valores para obtener la configuración. Después de completar esta tarea, puede usar el token adquirido para llamar a la API de gráfico. Porque `LoginView` será llamar en cualquier momento, necesitamos autenticar, tiene sentido para poner valores de configuración de ese archivo.

- Vamos a agregar algunos valores de la `LoginViewController.m` archivo para establecer el contexto de autenticación y la autorización. Detalles acerca de los valores siguen el código.

    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Echemos un vistazo a los detalles sobre el código.

Es la primera cadena para `scopes`.  La `User.Read` valor permite leer el perfil del usuario iniciado en básica.

Puede obtener más información acerca de todos los ámbitos disponibles en [los ámbitos de permiso de Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Para `authURL`, `loginURL`, `bhh`, y `tokenURL`, debe usar los valores proporcionados anteriormente. Si usa Microsoft Azure identidad bibliotecas de código abierto, nos desplegable estos datos para usted utilizando el extremo de metadatos. Hemos hecho el trabajo de extraer estos valores para usted.

La `keychain` valor es el contenedor de la biblioteca de NXOAuth2Client se usan para crear una cadena de claves para almacenar los símbolos. Si desea obtener el inicio de sesión único (SSO) a través de varias aplicaciones, puede especificar la misma cadena de claves en cada una de las aplicaciones y solicitar el uso de esa llaves en sus derechos Xcode. Esto se explica en la documentación de Apple.

El resto de los valores siguientes se requieren para usar la biblioteca y crear sitios para realizar los valores para el contexto.

### <a name="create-a-url-cache"></a>Crear una dirección URL caché

Dentro de `(void)viewDidLoad()`, siempre que se llama después de la vista se carga, el siguiente código completa una caché para nuestro uso.

Agregue el código siguiente:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Crear una vista Web para iniciar sesión

Una vista Web puede solicitar al usuario factores adicionales como mensaje de texto SMS (si está configurado) o devolver mensajes de error al usuario. Podrá establecer hasta la vista Web y, a continuación, escribir el código para controlar las devoluciones de llamada que se repite en la vista Web de los servicios de identidad de más adelante.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Reemplazar los métodos de vista Web para controlar la autenticación

Para indicar a la vista Web lo que sucede cuando un usuario necesita iniciar sesión como se indicó anteriormente, puede pegar el código siguiente.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Escribir código para controlar el resultado de la solicitud de OAuth2

El siguiente código controlará la redirectURL que devuelve de la vista Web. Si la autenticación es correcta, el código volverá a intentarlo. Mientras tanto, la biblioteca le proporcionará el error que puede ver en la consola o controlar de forma asincrónica.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Configurar el contexto de OAuth (denominado almacén de cuenta)

Aquí puede llamar a `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` en el repositorio de cuenta compartida para cada servicio que desea tener acceso a la aplicación. El tipo de cuenta es una cadena que se utiliza como un identificador de un determinado servicio. Dado que tiene acceso a la API de gráfico, el código hace referencia a él como `"myGraphService"`. A continuación, configure un observador que le indicará cuando cambia cualquier aspecto con el token. Después de obtener el token, regresar al usuario volver a la `masterView`.



```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Configurar la vista de patrón para buscar y mostrar los usuarios de la API de gráfico

Una aplicación de controlador de vista de patrón (MVC) que muestra los datos devueltos en la cuadrícula está fuera del ámbito de este tutorial y muchos tutoriales en línea explican cómo crear uno. Todo este código se encuentra en el archivo de esquema. Sin embargo, debe tratar con algunas cosas en esta aplicación MVC:

* Intersección cuando un usuario escribe algo en el campo de búsqueda
* Proporcionar un objeto de datos a la MasterView para que pueda visualizar los resultados en la cuadrícula

Deberá realizarlos siguientes.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Agregar una comprobación para ver si ha iniciado sesión

La aplicación hace poco si el usuario no ha iniciado sesión, por lo que es inteligente comprobar si ya hay un símbolo en la memoria caché. Si no es así, redirige a la LoginView para el usuario para iniciar sesión. Si recuerda, es la mejor manera de realizar acciones cuando se carga una vista usar la `viewDidLoad()` método que nos proporciona Apple.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Actualizar la vista de tabla cuando se reciben datos

Cuando la API de Graph devuelve datos, debe mostrar los datos. Para simplificar, esto todo el código para actualizar la tabla. Solo puede pegar los valores correctos en el código de modelo MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Proporciona una manera de llamar a la API de gráfico cuando un usuario escribe en el campo de búsqueda

Cuando un usuario escribe una búsqueda en el cuadro, debe se envíen a la API de gráfico. La `GraphAPICaller` clase, que se van a generar en el código siguiente, separa la funcionalidad de búsqueda de la presentación. Por ahora, vamos a escribir el código que las fuentes de los caracteres de búsqueda a la API de gráfico. Para ello, lo que proporciona un método denominado `lookupInGraph`, que toma la cadena que se va a buscar.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Escribir una clase auxiliar para obtener acceso a la API de gráfico

Este es el componente principal de la aplicación. Mientras el resto inserta código en el modelo MVC predeterminada de Apple, aquí se escribe código para consultar el gráfico cuando el usuario escribe y, a continuación, devolver datos. Este es el código y una explicación detallada a continuación.

### <a name="create-a-new-objective-c-header-file"></a>Crear un nuevo archivo de encabezado de objetivo C

Nombre del archivo `GraphAPICaller.h`y agregue el código siguiente.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Aquí puede ver que un método especificado toma una cadena y devuelve un completionBlock. Este completionBlock, como podrá adivinar, actualizará la tabla para mostrar un objeto con datos en tiempo real como las búsquedas de usuario.


### <a name="create-a-new-objective-c-file"></a>Crear un nuevo archivo de objetivo C

Nombre del archivo `GraphAPICaller.m`y agregue el método siguiente.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Veamos este método detalladamente.

Es el componente principal de este código en el `NXOAuth2Request`, el método que toma los parámetros que ya ha definido en el archivo settings.plist.

El primer paso es construir la llamada API de gráfico adecuada. Dado que se llama `/users`, especificar que agrega al recurso API de gráfico junto con la versión. Tiene sentido poner en un archivo de configuración externo, porque estos pueden cambiar como la API evoluciona.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

A continuación, debe especificar los parámetros que también proporcionará a la llamada de la API de gráfico. Es *muy importante* no coloque los parámetros en el extremo de recursos debido a se limpió para todos los caracteres de URI no es compatible en tiempo de ejecución. Todo el código de consulta debe proporcionar en los parámetros.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Es posible que note se llama una `convertParamsToDictionary` método que se ha escrito todavía. Vamos a hacerlo ahora al final del archivo:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
A continuación, vamos a usar la `NXOAuth2Request` método para recuperar los datos de la API en formato JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Por último, echemos un vistazo a cómo se devuelven los datos a la MasterViewController. Los datos se devuelve como en serie y se deben deserializar y cargado en un objeto que puede consumir la MainViewController. Para ello, tiene la estructura un `User.m/h` archivo que crea un objeto de usuario. Rellenar ese objeto de usuario con la información del gráfico.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Ejecutar el ejemplo

Si ha utilizado el esquema o seguidos junto con el tutorial que ahora debe ejecutarse la aplicación. Iniciar el simulador y haga clic en **iniciar sesión** para usar la aplicación.

## <a name="get-security-updates-for-our-product"></a>Obtener actualizaciones de seguridad de nuestros productos

Le recomendamos que obtener notificaciones de cuándo se producen incidencias de seguridad al visitar el [TechCenter de seguridad](https://technet.microsoft.com/security/dd252948) y suscribirse a las alertas de aviso de seguridad.
