<properties
    pageTitle="Introducción a Azure iOS de AD | Microsoft Azure"
    description="Cómo crear una aplicación iOS que se integra con Azure AD para iniciar sesión y llamadas de Azure AD protegido API con OAuth."
    services="active-directory"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-ios-app"></a>Integrar Azure AD en una aplicación de iOS

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Azure AD proporciona la Active Directory Authentication Library o ADAL, para los clientes de iOS que necesitan tener acceso a los recursos protegidos.  Propósitos de ADAL en la vida es más fácil para su aplicación obtener el token de acceso.  Para mostrar solo lo fácil que es, a continuación, crearemos una aplicación de la lista de tareas pendientes de objetivo C:

-   Obtiene acceso a tokens para llamar a la API de Azure AD gráfico mediante el [Protocolo de autenticación de OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Busca en un directorio para los usuarios con un alias determinado.

Para generar la aplicación de trabajo completado, tendrá que:

2. Registrar la aplicación con Azure AD.
3. Instalar y configurar ADAL.
5. Use ADAL para obtener tokens de Azure AD.

Para comenzar, [Descargue la estructura de la aplicación](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) o [descargar el ejemplo completo](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  También necesitará a un inquilino de Azure AD en el que puede crear usuarios y registrar una aplicación.  Si aún no dispone de un inquilino, [Obtenga información sobre cómo obtener una](active-directory-howto-tenant.md).

> [AZURE.TIP] Pruebe la vista previa de nuestro nuevo [portal para desarrolladores](https://identity.microsoft.com/Docs/iOS) que le ayudarán a ponerse en marcha con Azure Active Directory en tan solo unos minutos.  El portal de programadores lo guiará por el proceso de registrar una aplicación y la integración de Azure AD en el código.  Cuando haya terminado, tendrá una aplicación simple que puede autenticar a los usuarios en su inquilino y back-end que pueden aceptar tokens y realizar una validación. 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>*1. determinar cuál será la redirección URI para iOS*

Para iniciar las aplicaciones en ciertos escenarios SSO de forma segura es necesario crear una **Redirección URI** en un formato concreto. Una redirección URI se usa para asegurarse de que los tokens se devuelven a la aplicación correcta que se le solicite para ellos.

El formato de iOS de una redirección URI es:

```
<app-scheme>://<bundle-id>
```

-   **combinación de aap** - Esto se registra en el proyecto XCode. Es cómo pueden llamar a otras aplicaciones. Puede encontrar esto en Info.plist -> tipos de URL -> identificador de dirección URL. Debe crearla si todavía no tiene uno o varios configurado.
-   **Id. de paquete** : es el identificador de paquete se encuentra en la "identidad" anule la configuración del proyecto en XCode.

Un ejemplo de código de este tutorial rápido: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>*2. registrar la aplicación DirectorySearcher*
Para habilitar la aplicación obtener tokens, primero deberá registrar en su inquilino de Azure AD y conceder permiso de acceso a la API de Azure AD gráfico:

-   Inicie sesión en el Portal de administración de Azure
-   En la barra de navegación izquierda, haga clic en **Active Directory**
-   Seleccione a un inquilino en el que se va a registrar la aplicación.
-   Haga clic en la pestaña de **aplicaciones** y haga clic en **Agregar** en la Bandeja de la parte inferior.
-   Siga las indicaciones y cree una nueva **Aplicación nativa de cliente**.
    -   El **nombre** de la aplicación describe la aplicación a los usuarios finales
    -   La **Redirección Uri** es una combinación de combinación y la cadena que usará Azure AD para devolver respuestas tokens.  Escriba un valor específico para una aplicación basada en la información anterior.
-   Una vez haya completado el registro, AAD asignar su aplicación un identificador único del cliente.  Necesitará este valor en las siguientes secciones, así que cópielo desde la ficha **Configurar** .
- También en la ficha **Configurar** , busque la sección "Permisos a otras aplicaciones".  Para la aplicación "Azure Active Directory", agregue el permiso de **acceso a su directorio de la organización** en **Permisos de delegado**.  Esto le permitirá la aplicación de la API de gráfico para los usuarios de la consulta.

## <a name="3-install--configure-adal"></a>*3. instalar y configurar ADAL*
Ahora que ya tiene una aplicación en Azure AD, puede instalar ADAL y escriba el código de identidad.  Para ADAL puedan comunicarse con Azure AD, debe proporcionar con información sobre el registro de aplicación.
-   Empiece agregando ADAL al proyecto DirectorySearcher con Cocapods.

```
$ vi Podfile
```
Para este podfile, agregue lo siguiente:

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Cargar ahora la podfile con cocoapods. Esta opción creará una nueva área de trabajo XCode que se cargará.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-   En el proyecto Tutorial rápido, abra el archivo plist `settings.plist`.  Reemplace los valores de los elementos de la sección para reflejar los valores que introduce en el Portal de Azure.  El código hará referencia a estos valores siempre que use ADAL.
    -   La `tenant` es el dominio de su inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com
    -   La `clientId` está dentro de la aplicación que ha copiado desde el portal.
    -   La `redirectUri` es la redirección de url registrado en el portal.

## <a name="4--use-adal-to-get-tokens-from-aad"></a>*4. utilizar ADAL para obtener Tokens de AAD*
El principio básico detrás de ADAL es que siempre que su aplicación necesita un token de acceso, simplemente llama un completionBlock `+(void) getToken : `, y ADAL hace el resto.  

-   En la `QuickStart` proyecto, abrir `GraphAPICaller.m` y busque el `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` comentario cerca de la parte superior.  Esto es donde se pasa ADAL las coordenadas a través de un CompletionBlock para comunicarse con Azure AD y saber cómo almacenar en caché tokens.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- Ahora es necesario utilizar este token para buscar usuarios en el gráfico. Buscar la `// TODO: implement SearchUsersList` commentThis método hace una solicitud GET a la API de Azure AD Graph para consulta para los usuarios cuyo UPN comienza con el término de búsqueda especificado.  Pero con el fin de la API de gráfico de la consulta, tendrá que incluir una access_token en el `Authorization` encabezado de la solicitud - esto es donde se ADAL.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

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
                         s.name =[keyValuePairs valueForKey:@"givenName"];

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
     }];

}

```
- Cuando la aplicación solicita un token llamando `getToken(...)`, ADAL intentará devolver un token sin preguntar al usuario las credenciales.  Si ADAL determina que el usuario debe iniciar sesión obtener un token, mostrar un cuadro de diálogo de inicio de sesión, recopilar las credenciales del usuario y devolver un token tras la autenticación.  Si no puede devolver un token por cualquier motivo ADAL, producirá un `AdalException`.
- Observe que la `AuthenticationResult` objeto contiene un `tokenCacheStoreItem` objeto que puede usarse para recopilar la información que necesite la aplicación.  En el tutorial, `tokenCacheStoreItem` se utiliza para determinar si ya se ha producido autenticación.


## <a name="step-5-build-and-run-the-application"></a>Paso 5: Crear y ejecutar la aplicación



¡Felicidades! Ahora tiene una aplicación iOS que funciona con la capacidad para autenticar a los usuarios, llamar de forma segura API Web mediante OAuth 2.0 y obtenga información básica sobre el usuario.  Si todavía no lo ha hecho, ahora es el momento para rellenar al inquilino de algunos usuarios.  Ejecute la aplicación de tutorial rápido e inicie sesión con uno de esos usuarios.  Buscar otros usuarios en función de su UPN.  La aplicación se cierra y vuelve a ejecutarla.  Observe cómo la sesión del usuario permanece intacta.

ADAL facilita la incorporar todas estas características de identidad comunes a la aplicación.  Se encarga de todo el trabajo dirty para usted, gestión de caché, compatibilidad con el protocolo OAuth, presentando el usuario con un inicio de sesión de interfaz de usuario, actualizar tokens caducados y mucho más.  Todo lo que necesita saber es una sola llamada API, `getToken`.

Para referencia, se proporciona el ejemplo completo (sin los valores de configuración) [aquí](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="additional-scenarios"></a>Escenarios adicionales
Ahora puede mover a escenarios adicionales.  Desea probar:

- [Proteger un Web Node.JS API con Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
- Aprenda [a habilitar SSO entre aplicaciones en iOS con ADAL](active-directory-sso-ios.md)  

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
