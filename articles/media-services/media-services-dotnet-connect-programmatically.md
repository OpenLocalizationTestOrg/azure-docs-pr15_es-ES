<properties 
    pageTitle="Conectarse a la cuenta de servicios de medios con .NET" 
    description="Este tema muestra cómo conectarse a usar Media Services .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-sdk-for-net"></a>Conectarse a Media Services cuenta con Media Services SDK para .NET

> [AZURE.SELECTOR]
- [REST](media-services-rest-connect-programmatically.md)
- [.NET](media-services-dotnet-connect-programmatically.md)


En este tema se describe cómo obtener una conexión a Microsoft Azure Media Services mediante programación al programar con el SDK de servicios de medios para .NET.


## <a name="connecting-to-media-services"></a>Conectarse a los servicios de medios

Para conectarse a servicios de medios mediante programación, debe ha configurado previamente una cuenta de Azure, configurado Media Services en esa cuenta y, a continuación, configurar un proyecto de Visual Studio para el desarrollo con el SDK de servicios de medios para .NET. Para obtener más información, consulte Configuración de desarrollo con el SDK de servicios de medios para .NET.

Al final del proceso de configuración de cuenta de Media Services, obtienen los siguientes valores de conexión necesaria. Utilice estos realizar programación conexiones a servicios de medios.

- Nombre de su cuenta de Media Services.

- La clave de cuenta Media Services.

Para buscar estos valores, vaya al Portal de administración de Azure, seleccione su cuenta de servicio de medios y haga clic en el icono de "**Administrar claves**" en la parte inferior de la ventana del portal. Al hacer clic en el icono junto a cada cuadro de texto, copia el valor al Portapapeles del sistema.


## <a name="creating-a-cloudmediacontext-instance"></a>Crear una instancia de CloudMediaContext

Para empezar a programar con Media Services debe crear una instancia de **CloudMediaContext** que representa el contexto del servidor. La **CloudMediaContext** incluye referencias a las colecciones importantes incluidos trabajos, activos, archivos, las directivas de acceso y localizadores.

>[AZURE.NOTE] La clase **CloudMediaContext** no es segura para subprocesos. Debe crear un nuevo CloudMediaContext por subproceso o por el conjunto de operaciones.


CloudMediaContext tiene cinco sobrecargas. Se recomienda usar constructores que toman **MediaServicesCredentials** como un parámetro. Para obtener más información, vea **Volver a utilizar Tokens de servicio de Control de acceso** que sigue. 

En el ejemplo siguiente se utiliza el constructor CloudMediaContext(MediaServicesCredentials credentials) público:

    // _cachedCredentials and _context are class member variables. 
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
    _context = new CloudMediaContext(_cachedCredentials);


## <a name="reusing-access-control-service-tokens"></a>Volver a utilizar los símbolos de servicio de Control de acceso

Esta sección muestra cómo volver a utilizar tokens de servicio de Control de acceso mediante el uso de constructores CloudMediaContext que toman MediaServicesCredentials como un parámetro.


[Control de acceso de Azure Active Directory](https://msdn.microsoft.com/library/hh147631.aspx) (también conocido como servicio de Control de acceso o ACS) es un servicio basado en la nube que proporciona una forma sencilla de autenticación y autorización de usuarios para obtener acceso a sus aplicaciones web. Microsoft Azure Media Services controla el acceso a sus servicios aunque protocolo OAuth que requiere un token de ACS. Media Services recibe tokens de ACS desde un servidor de autorización.

Al desarrollar con el SDK de servicios de medios, puede elegir no tratar los tokens porque el SDK de código administradores invitarlos por usted. Sin embargo, permitiendo que el SDK totalmente administrar tokens de ACS lleva a las solicitudes de tokens innecesarias. Solicitar tokens requiere tiempo y consume los recursos de servidor y cliente. Además, el servidor ACS limita las solicitudes si la tasa es demasiado alta. El límite es de 30 peticiones por segundo, vea [Limitaciones de servicio ACS](https://msdn.microsoft.com/library/gg185909.aspx) para obtener más detalles.

Comenzando con el SDK de Media Services versión 3.0.0.0, puede volver a utilizar los tokens de ACS. Los constructores de **CloudMediaContext** que toman **MediaServicesCredentials** como parámetro permiten compartir tokens de ACS entre varios contextos. La clase MediaServicesCredentials encapsula las credenciales de Media Services. Si un símbolo de ACS está disponible y se conoce su fecha de expiración, puede crear una nueva instancia de MediaServicesCredentials con el token y pasar al constructor de CloudMediaContext. Tenga en cuenta que el SDK de servicios de multimedia se actualicen automáticamente tokens siempre que expiren. Hay dos formas de reutilizar tokens de ACS, tal como se muestra en los ejemplos siguientes.

- Puede almacenar en caché el objeto **MediaServicesCredentials** en memoria (por ejemplo, en una variable de clase estática). A continuación, pase el objeto en caché al constructor CloudMediaContext. El objeto MediaServicesCredentials contiene un símbolo de ACS que puedan reutilizarse si sigue siendo válido. Si el token no es válido, se actualizará el SDK de servicios de medios con las credenciales proporcionadas al constructor MediaServicesCredentials.

    Tenga en cuenta que el objeto **MediaServicesCredentials** Obtiene un token válido después de la RefreshToken. La **CloudMediaContext** llama al método de **RefreshToken** en el constructor. Si va a guardar los valores del token en un almacenamiento externo, asegúrese de comprobar si el valor de TokenExpiration es válido antes de guardar los datos de tokens. Si no es válido, llame a RefreshToken antes de almacenamiento en caché.

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

        
        // Use the cached credentials to create a new CloudMediaContext object.
        if(_cachedCredentials == null)
        {
            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
        }
        
        CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- También puede almacenar en caché la cadena AccessToken y los valores de TokenExpiration. Los valores más adelante pueden utilizarse para crear un nuevo objeto MediaServicesCredentials con los datos de tokens en caché.  Esto es especialmente útil para escenarios donde el token puede compartirse con seguridad entre varios procesos o equipos.

    Fragmentos de código siguientes llamar a los métodos SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage y UpdateTokenDataInExternalStorageIfNeeded que no se definen en este ejemplo. Puede definir estos métodos para almacenar, recuperar y actualizar datos tokens en un almacenamiento externo. 

        CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
        
        // Get token values from the context.
        var accessToken = context1.Credentials.AccessToken;
        var tokenExpiration = context1.Credentials.TokenExpiration;
        
        // Save token values for later use. 
        // The SaveTokenDataToExternalStorage method should check 
        // whether the TokenExpiration value is valid before saving the token data. 
        // If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
        SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
        
    Use los valores del token guardados para crear MediaServicesCredentials.


        var accessToken = "";
        var tokenExpiration = DateTime.UtcNow;
        
        // Retrieve saved token values.
        GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
        
        // Create a new MediaServicesCredentials object using saved token values.
        MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
        {
            AccessToken = accessToken,
            TokenExpiration = tokenExpiration
        };
        
        CloudMediaContext context2 = new CloudMediaContext(credentials);

    Actualizar la copia del token en caso de que el token se actualizó por el SDK de servicios de medios. 
    
        if(tokenExpiration != context2.Credentials.TokenExpiration)
        {
            UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
        }
        

- Si tiene varias cuentas de Media Services (por ejemplo, para cargar el uso compartido con fines o distribución Geo) puede almacenar en caché objetos MediaServicesCredentials uso de la colección de System.Collections.Concurrent.ConcurrentDictionary (la colección de ConcurrentDictionary representa una colección de subprocesos de pares de clave/valor que se puede tener acceso simultáneamente varios subprocesos). A continuación, puede usar el método GetOrAdd para obtener las credenciales en caché. 

        // Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
        private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
            new ConcurrentDictionary<string, MediaServicesCredentials>();
        

        // Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
        static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
        {
            CloudMediaContext cloudMediaContext;
            MediaServicesCredentials mediaServicesCredentials;
        
            mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
                accountName,
                valueFactory => new MediaServicesCredentials(accountName, accountKey));
        
            cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
        
            return cloudMediaContext;
        }
        
## <a name="connecting-to-a-media-services-account-located-in-the-north-china-region"></a>Conectarse a una cuenta de Media Services ubicada en la región del Norte de China

Si su cuenta se encuentra en la región del Norte de China, use el constructor siguiente:

    public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Por ejemplo:


    _context = new CloudMediaContext(
        new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
        _mediaServicesAccountName,
        _mediaServicesAccountKey,
        "urn:WindowsAzureMediaServices",
        "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## <a name="storing-connection-values-in-configuration"></a>Almacenar valores de conexión en la configuración

Es una práctica muy recomendable para almacenar los valores de la conexión, especialmente confidenciales valores como el nombre de la cuenta y la contraseña, en configuración. También es una práctica recomendada para cifrar datos confidenciales de la configuración. Puede cifrar el archivo de configuración completo mediante el sistema de archivos cifrados (EFS) de Windows. Para habilitar EFS en un archivo, haga clic en el archivo, seleccione **Propiedades**y habilitar el cifrado en la ficha de configuración **avanzada** . O bien, puede crear una solución personalizada para cifrar partes seleccionadas de un archivo de configuración mediante configuración protegida. Consulte [cifrado configuración información usando configuración protegida](https://msdn.microsoft.com/library/53tyfkaw.aspx).

El siguiente archivo App.config contiene los valores de conexión necesaria. Los valores de la <appSettings> elemento son los valores requeridos que obtuvo desde el proceso de configuración de la cuenta de Media Services.

    <configuration>
      <appSettings>
        <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
        <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
      </appSettings>
    </configuration>


Para recuperar los valores de conexión de configuración, puede usar la clase **ConfigurationManager** y, a continuación, asignar los valores a los campos en el código:
    
    private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
    private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
