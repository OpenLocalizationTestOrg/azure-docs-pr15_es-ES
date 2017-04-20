<properties
    pageTitle="Cómo usar iOS SDK para las aplicaciones móviles de Azure"
    description="Cómo usar iOS SDK para las aplicaciones móviles de Azure"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Cómo usar iOS biblioteca de cliente para las aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Esta guía le enseña a realizar escenarios comunes con la última [iOS aplicaciones móviles de Azure SDK][1]. Si está acostumbrado a trabajar con aplicaciones de Azure Mobile, primera completa [Inicio rápido de Azure Mobile aplicaciones] para crear un back-end, crear una tabla y descargar un proyecto de Xcode predefinida iOS. En esta guía nos centrarse en el SDK de cliente iOS. Para obtener más información sobre el SDK de servidor para el back-end, consulte las guías de uso del SDK de servidor.

## <a name="reference-documentation"></a>Documentación de referencia

La documentación de referencia para el cliente de iOS SDK se encuentra aquí: [aplicaciones de Azure Mobile iOS referencia de cliente][2].

## <a name="supported-platforms"></a>Plataformas admitidas

IOS SDK admite proyectos de objetivo C, Swift 2.2 proyectos y proyectos de Swift 2.3 para iOS versiones 8.0 o posteriores.

La autenticación de "flujo de servidor" utiliza una vista de Web para la interfaz de usuario presentado.  Si el dispositivo no puede presentar una UI WebView, entonces es necesario otro método de autenticación que está fuera del ámbito del producto.  
Este SDK, por tanto, no es adecuado para el tipo de inspección o del mismo modo restringidos dispositivos.

##<a name="Setup"></a>El programa de instalación y los requisitos previos

Esta guía asume que ha creado un back-end con una tabla. Esta guía se supone que la tabla tiene el mismo esquema que las tablas en los tutoriales. Esta guía también se supone que en el código se hace referencia `MicrosoftAzureMobile.framework` e importar `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Cómo: crear cliente

Para obtener acceso a un servidor de aplicaciones móviles de Azure en su proyecto, crear un `MSClient`. Reemplazar `AppUrl` con la dirección URL de la aplicación. Puede dejar `gatewayURLString` y `applicationKey` vacía. Si configura una puerta de enlace para la autenticación, rellenar `gatewayURLString` con la dirección URL de la puerta de enlace.

**Objetivo C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Cómo: crear la referencia de tabla

En access o actualizar los datos, crear una referencia a la tabla de back-end. Reemplazar `TodoItem` con el nombre de la tabla

**Objetivo C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Cómo: consultar datos

Para crear una consulta de base de datos, la consulta la `MSTable` objeto. La siguiente consulta obtiene todos los elementos de `TodoItem` y registre el texto de cada elemento.

**Objetivo C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Cómo: filtrar los datos devueltos

Para filtrar los resultados, hay muchas opciones disponibles.

Para filtrar utilizando un predicado, use un `NSPredicate` y `readWithPredicate`. Los siguientes filtros devuelven datos para buscar solo los elementos de Todo incompletos.

**Objetivo C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Cómo: usar MSQuery

Para realizar una consulta compleja (incluida la ordenación y paginación), cree un `MSQuery` objeto directamente o mediante un predicado:

**Objetivo C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`le permite controlar varios comportamientos de consulta.

* Especificar el orden de los resultados
* Limitar los campos que desea devuelto
* Limitar el número de registros para devolver
* Especificar el recuento total de respuesta
* Especificar los parámetros de cadena de consulta personalizada en la solicitud
* Aplicar funciones adicionales

Ejecutar una `MSQuery` consulta llamando `readWithCompletion` en el objeto.

## <a name="sorting"></a>Cómo: ordenar datos con MSQuery

Para ordenar los resultados, veamos un ejemplo. Para ordenar por orden ascendente 'texto' de campo y luego por descendente 'complete', invocar `MSQuery` así:

**Objetivo C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Cómo: limitar campos y expanda parámetros de cadena de consulta con MSQuery

Para limitar los campos que se devolverán en una consulta, especifique los nombres de los campos en la propiedad **selectFields** . En este ejemplo se devuelve únicamente el texto y los campos completados:

**Objetivo C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Para incluir los parámetros de cadena de consulta adicionales en la solicitud del servidor (por ejemplo, porque utiliza un script de servidor personalizado), rellenar `query.parameters` así:

**Objetivo C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Cómo: configurar el tamaño de página

Con aplicaciones de Azure Mobile, el tamaño de página controla el número de registros que se extraen en un momento de las tablas de back-end. Una llamada a `pull` datos haría lotes, a continuación, los datos, según el tamaño de esta página hasta que no existen más registros para extraer.

Es posible configurar un tamaño de página mediante **MSPullSettings** tal como se muestra a continuación. El tamaño de página predeterminado es 50 y el ejemplo siguiente cambia a 3.

Puede configurar un tamaño de página diferentes para mejorar el rendimiento. Si tiene una gran cantidad de registros de datos pequeña, un tamaño de página alto reduce el número de ida y vuelta del servidor. 

Esta opción controla el tamaño de página en el cliente. Si el cliente solicita un tamaño de página más grande que admite el servidor de aplicaciones de Mobile, el tamaño de página se limita a máxima que el servidor está configurado para admitir. 

Esta configuración también es el _número_ de registros de datos, no el _tamaño de bytes_.

Si aumentar el tamaño de la página de cliente, [también debe aumentar el tamaño de página en el servidor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#_how-to-adjust-the-table-paging-size).

**Objetivo C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                } 
                           }];
```


**Swift**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    } 
}
```

##<a name="inserting"></a>Cómo: insertar datos

Para insertar una nueva fila de tabla, cree una `NSDictionary` e invocar `table insert`. Si se habilita el [Esquema dinámico] , el servicio de aplicación de Azure de back-end móvil genera automáticamente las nuevas columnas en función de la `NSDictionary`.

Si `id` es no se proporcionan, el servidor genera automáticamente un nuevo Id. Proporcionar su propio `id` para usar el correo electrónico direcciones, nombres de usuario o los valores de su propios personalizada como Id. Puede proporcionar su propio ID facilidad combinaciones y lógica de base de datos comerciales.

La `result` contiene el elemento nuevo que insertó. Dependiendo de la lógica del servidor, puede que los datos adicionales o modificados en comparación con lo que se ha pasado con el servidor.

**Objetivo C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Cómo: modificar datos

Para actualizar una fila existente, modificar un elemento y la llamada `update`:

**Objetivo C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Como alternativa, proporcione el identificador de fila y el campo actualizado:

**Objetivo C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Como mínimo, el `id` atributo debe establecerse cuando se realizan actualizaciones.

##<a name="deleting"></a>Cómo: eliminar datos

Para eliminar un elemento, invocar `delete` con el elemento:

**Objetivo C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

También puede eliminar proporcionando un identificador de fila:

**Objetivo C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Como mínimo, el `id` atributo debe establecerse cuando efectúen elimina.

##<a name="customapi"></a>Cómo: llamar a API personalizado

Con una API personalizada, puede exponer ninguna funcionalidad back-end. No tiene que asignar a una operación de la tabla. No sólo obtendrá más control sobre mensajería, incluso puede leer o establecer encabezados y cambiar el formato del cuerpo de respuesta. Para obtener información sobre cómo crear una API personalizada en el back-end, lea [Las API de personalizado](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Para llamar a una API personalizada, llame a `MSClient.invokeAPI`. La solicitud y la respuesta contenido se tratan como JSON. Usar otros tipos de medios, [Utilice la otra sobrecarga de `invokeAPI` ] [ 5].  Para realizar una `GET` solicitar en lugar de un `POST` solicitar parámetro definido `HTTPMethod` a `"GET"` y parámetro `body` a `nil` (como las solicitudes GET no tienen cuerpo de los mensajes.) Si la API personalizada admite otros verbos HTTP, cambiar `HTTPMethod` correctamente.

**Objetivo C**:

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Cómo: registrar plantillas de inserción para enviar notificaciones de varias plataformas

Para registrar plantillas, pase plantillas con el método de **client.push registerDeviceToken** en la aplicación de cliente.

**Objetivo C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Las plantillas de tipo NSDictionary y pueden contener varias plantillas en el siguiente formato:

**Objetivo C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Se eliminan todas las etiquetas de la solicitud de seguridad.  Para agregar etiquetas a las instalaciones o plantillas dentro de las instalaciones, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones de Azure Mobile][4].  Para enviar notificaciones con estas plantillas registradas, trabajar con [Las API de Hubs de notificación][3].

##<a name="errors"></a>Cómo: controlar los errores

Cuando llame a un servicio de aplicación de Azure de back-end móvil, el bloque de finalización contiene un `NSError` parámetro. Cuando se produce un error, este parámetro es no nulo. En el código, debe comprobar este parámetro y controlar el error según sea necesario, como se muestra en los fragmentos de código anterior.

El archivo [`<WindowsAzureMobileServices/MSError.h>`] [6] define las constantes `MSErrorResponseKey`, `MSErrorRequestKey`, y `MSErrorServerItemKey`. Para obtener más datos relacionados con el error:

**Objetivo C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Además, el archivo define constantes para cada código de error:

**Objetivo C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Cómo: autenticar a los usuarios con el Active Directory Authentication Library

Puede usar la biblioteca de autenticación de Active Directory (ADAL) para iniciar la sesión de los usuarios en la aplicación con Azure Active Directory. Autenticación de flujo de cliente mediante un proveedor de identidades SDK es preferible al uso de la `loginWithProvider:completion:` método.  Autenticación de flujo de cliente proporciona una apariencia más nativo de UX y permite personalizar adicional.

1. Configurar la aplicación móvil de back-end para el inicio de sesión de AAD siguiendo [cómo configurar la aplicación de servicio de inicio de sesión de Active Directory] [ 7] tutorial. Asegúrese de completar el paso opcional de registrar una aplicación nativa de cliente. Para iOS, le recomendamos que la redirección URI tiene el formato `<app-scheme>://<bundle-id>`. Para obtener más información, consulte el [Tutorial de iOS ADAL][8].

2. Instale ADAL con Cocoapods. Editar su Podfile para incluir la siguiente definición, reemplazando **Su proyecto** con el nombre de su proyecto Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   y la caja:

        pod 'ADALiOS'

3. Mediante el uso de la Terminal, ejecute `pod install` desde el directorio que contiene el proyecto y, a continuación, abra el área de trabajo Xcode generado (no en el proyecto).

4. Agregue el código siguiente a la aplicación, según el idioma que está utilizando. En cada uno, realizar estas sustituciones:

    * Reemplazar **Aquí de autoridad de INSERCIÓN** con el nombre del inquilino en el que se aprovisione la aplicación. El formato debe ser https://login.windows.net/contoso.onmicrosoft.com. Este valor se puede copiar en la ficha de dominio de Azure Active Directory en [Azure clásico portal].
    * Reemplazar **Insertar recurso ID aquí** con el ID de cliente para la aplicación móvil de back-end. Puede obtener el identificador de cliente de la ficha **Opciones avanzadas** , en **Configuración de Azure Active Directory** en el portal.
    * Reemplazar **Insertar cliente ID aquí** con el ID de cliente que ha copiado de la aplicación cliente nativa.
    * Reemplazar **Insertar REDIRECCIÓN URI aquí** con extremo de _/.auth/login/done_ de su sitio, mediante la combinación de HTTPS. Este valor debe ser similar a _https://contoso.azurewebsites.net/.auth/login/done_.

**Objetivo C**:

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**Swift**:

    // add the following imports to your bridging header:
    //      #import <ADALiOS/ADAuthenticationContext.h>
    //      #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>Cómo: autenticar a los usuarios con el SDK de Facebook para iOS

Puede usar el SDK de Facebook para iOS para iniciar la sesión de los usuarios en la aplicación con Facebook.  Utilizando la autenticación de flujo de un cliente es preferible al uso de la `loginWithProvider:completion:` método.  La autenticación de flujo de cliente proporciona una apariencia más nativo de UX y permite personalizar adicional.

1. Configurar la aplicación móvil de back-end para el inicio de sesión en Facebook siguiendo [cómo configurar la aplicación de servicio de inicio de sesión de Facebook] [ 9] tutorial.

2. Instalar el SDK de Facebook para iOS siguiendo el [SDK de Facebook para iOS - Introducción] [ 10] documentación. En lugar de crear una aplicación, puede agregar la plataforma de iOS para el registro existente. 

3. Documentación de Facebook incluye algún código C objetivo en el delegado de aplicación. Si está utilizando **Swift**, puede usar las siguientes traducciones para AppDelegate.swift:
  
        // Add the following import to your bridging header:
        //      #import <FBSDKCoreKit/FBSDKCoreKit.h>
        
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }

4. Además de agregar `FBSDKCoreKit.framework` a su proyecto, también puede agregar una referencia a `FBSDKLoginKit.framework` de la misma manera. 

4. Agregue el código siguiente a la aplicación, según el idioma que está utilizando. 

**Objetivo C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {       
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**Swift**:

    // Add the following imports to your bridging header:
    //      #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //      #import <FBSDKCoreKit/FBSDKAccessToken.h>
    
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>Cómo: autenticar a los usuarios con tela Twitter para iOS

Puede usar a tela para iOS para iniciar la sesión de los usuarios en la aplicación mediante Twitter. Autenticación de flujo de cliente es preferible al uso de la `loginWithProvider:completion:` método, tal como se proporciona más nativa apariencia UX y permite personalizar adicional.

1. Configurar la aplicación móvil de back-end para el inicio de sesión de Twitter siguiendo el tutorial de [cómo configurar la aplicación de servicio de inicio de sesión de Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) .

2. Agregar a estructura al proyecto siguiendo la documentación de [tela para iOS - Introducción] y configurando TwitterKit.

    > [AZURE.NOTE] De forma predeterminada, tela crea una aplicación de Twitter para usted. Puede evitar la creación de una aplicación mediante el registro de la clave de consumidor y el secreto de consumidor creada anteriormente con fragmentos de código siguientes.  Como alternativa, puede reemplazar los valores de clave de consumidor y consumidor secreto que proporciona a la aplicación de servicio con los valores que aparecen en el [Panel de tela]. Si elige esta opción, asegúrese de configurar la dirección URL de devolución de llamada en un valor de marcador de posición, como por ejemplo `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Si decide usar la información confidencial que creó anteriormente, agregue el código siguiente a su delegado de aplicación:
    
    **Objetivo C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }
        
    **Swift**:
    
        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
    
3. Agregue el código siguiente a la aplicación, según el idioma que está utilizando. 

**Objetivo C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**Swift**:

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>Cómo: autenticar a los usuarios con el SDK de inicio de sesión de Google para iOS

Puede usar el SDK de inicio de sesión de Google para iOS para iniciar la sesión de los usuarios en la aplicación con una cuenta de Google.  Google recientemente había anunciada cambios en sus directivas de seguridad de OAuth.  Estos cambios de directiva requieren el uso del SDK de Google en el futuro.

1. Configurar la aplicación móvil de back-end para el inicio de sesión de Google siguiendo el tutorial de [cómo configurar la aplicación de servicio de inicio de sesión de Google](app-service-mobile-how-to-configure-google-authentication.md) .

2. Instalar el SDK de Google para iOS siguiendo la documentación de [Inicio de sesión de Google para iOS: empezar a integrar](https://developers.google.com/identity/sign-in/ios/start-integrating) . Puede omitir la sección "Autenticar con un servidor de back-end".

3. Agregue lo siguiente a su delegado `signIn:didSignInForUser:withError:` método, según el idioma que está utilizando.

**Objetivo C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };
        
        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Swift**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

4. Asegúrese de que agregue lo siguiente a `application:didFinishLaunchingWithOptions:` en el delegado de aplicación, reemplazar "SERVER_CLIENT_ID" con el mismo ID que usó para configurar el servicio de aplicación en el paso 1.

**Objetivo C**:

        [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
 
 **Swift**:
 
        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"

 
 5. Agregue el código siguiente a la aplicación en un UIViewController que implementa el `GIDSignInUIDelegate` protocolo, según el idioma que está utilizando.  Ha cerrado la sesión antes de que se ha iniciado sesión en nuevo y, aunque no es necesario volver a introducir sus credenciales, verá un cuadro de diálogo consentimiento.  Sólo llame a este método cuando ha caducado el token de sesión.
 
 **Objetivo C**:

        #import <Google/SignIn.h>
        // ...
        - (void)authenticate
        {
                [GIDSignIn sharedInstance].uiDelegate = self;
                [[GIDSignIn sharedInstance] signOut];
                [[GIDSignIn sharedInstance] signIn];
        }
 
 **Swift**:
    
        // ...
        func authenticate() {
            GIDSignIn.sharedInstance().uiDelegate = self
            GIDSignIn.sharedInstance().signOut()
            GIDSignIn.sharedInstance().signIn()
        }
        
<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Inicio rápido de aplicaciones móviles Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Esquema dinámicas]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Panel de tela]: https://www.fabric.io/home
[Tela para iOS: Introducción]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem
[9]: app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
