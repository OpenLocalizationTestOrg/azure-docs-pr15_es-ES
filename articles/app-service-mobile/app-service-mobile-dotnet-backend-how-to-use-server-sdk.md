<properties
    pageTitle="Cómo trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles | Servicio de aplicaciones de Azure"
    description="Obtenga información sobre cómo trabajar con el servidor back-end de .NET SDK para las aplicaciones de Azure aplicación de servicio móvil."
    keywords="servicio de aplicaciones, servicio de aplicación de azure, una aplicación móvil, servicio móvil de escala, scalable, aplicación implementación, azure implementación de la aplicación"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este tema muestra cómo usar el servidor back-end de .NET SDK en escenarios clave de aplicaciones de Azure aplicación Servicio móvil. El SDK de aplicaciones móviles de Azure le ayuda a trabajar con los clientes móviles de la aplicación ASP.NET.

>[AZURE.TIP] El [servidor de .NET SDK para las aplicaciones móviles de Azure] [ 2] es Abrir origen en GitHub. El repositorio contiene todo el código fuente incluido el conjunto de pruebas de unidad SDK de todo el servidor y algunos ejemplos de proyectos.

## <a name="reference-documentation"></a>Documentación de referencia

La documentación de referencia para el servidor de SDK se encuentra aquí: [Referencia de Azure Mobile aplicaciones .NET][1].

## <a name="create-app"></a>Cómo: crear un aplicación móvil de .NET de back-end

Si va a iniciar un nuevo proyecto, puede crear una aplicación de servicio de la aplicación con el [portal de Azure] o Visual Studio. Puede ejecutar la aplicación de servicio de aplicación localmente o publicar el proyecto en su aplicación móvil de servicio de aplicaciones en la nube.  

Si está agregando capacidades de móviles a un proyecto existente, vea la sección [descargar e inicializar el SDK](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Crear un .NET back-end con el portal de Azure

Para crear un servicio de aplicaciones de back-end móvil, ya sea siga el [tutorial rápido] [ 3] o siga estos pasos:

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

En el módulo de _Introducción_ , en **crear una tabla API**, elija **C#** como el **idioma de back-end**. Haga clic en **Descargar**, extraer los archivos de proyecto comprimida al equipo local y abra la solución en Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2013-and-visual-studio-2015"></a>Crear un .NET back-end mediante 2013 de Visual Studio y Visual Studio de 2015

Instalar el [SDK de Azure para .NET] [ 4] (versión 2.9.0 o posterior) para crear un proyecto de aplicaciones de Azure Mobile en Visual Studio. Una vez haya instalado el SDK, cree una aplicación de ASP.NET mediante los siguientes pasos:

1. Abrir el cuadro de diálogo **Nuevo proyecto** ( *archivo* > **nuevo** > **proyecto...**).
2. Expanda **plantillas** > **Visual C#**y seleccione **Web**.
3. Seleccione la **aplicación Web de ASP.NET**.
4. Escriba el nombre del proyecto. A continuación, haga clic en **Aceptar**.
5. En _ASP.NET 4.5.2 plantillas_, seleccione la **Aplicación móvil de Azure**. Compruebe el **Host en la nube** para crear un back-end móvil en la nube que puede publicar este proyecto.
6. Haga clic en **Aceptar**.

## <a name="install-sdk"></a>Cómo: descargar e inicializar el SDK

El SDK está disponible en [NuGet.org]. Este paquete incluye la funcionalidad básica necesaria para empezar a usar el SDK. Para inicializar el SDK, debe realizar acciones en el objeto **HttpConfiguration** .

### <a name="install-the-sdk"></a>Instalar el SDK

Para instalar el SDK, haga clic en el proyecto de servidor en Visual Studio, seleccione **Administrar paquetes de NuGet**, busque el paquete de [Microsoft.Azure.Mobile.Server] y luego haga clic en **instalar**.

###<a name="server-project-setup"></a>Inicializar el proyecto de servidor

Un proyecto de servidor back-end de .NET se inicializado similar a otros proyectos ASP.NET, incluyendo una clase de inicio OWIN. Asegúrese de que ha indicado el paquete NuGet `Microsoft.Owin.Host.SystemWeb`. Para agregar esta clase en Visual Studio, haga clic en el proyecto de servidor y seleccione **Agregar** > 
**Nuevo elemento**y, a continuación, **Web** > **General** > **clase OWIN inicio**.  Se genera una clase con los siguientes atributos:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

En la `Configuration()` método de la clase de inicio OWIN, use un objeto **HttpConfiguration** para configurar el entorno de aplicaciones móviles de Azure.
En el ejemplo siguiente se inicializa el proyecto de servidor con ningún características adicionales:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Para habilitar las características individuales, debe llamar métodos de extensión en el objeto **MobileAppConfiguration** antes de llamar a **Aplicar para**. Por ejemplo, el siguiente código agrega las rutas de forma predeterminada en todos los controladores de API que tienen el atributo `[MobileAppController]` durante la inicialización:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

El tutorial de servidor desde el portal de Azure llama **UseDefaultConfiguration()**. Este equivalente a la siguiente configuración:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Los métodos de extensión utilizados son:

* `AddMobileAppHomeController()`proporciona la página de inicio de aplicaciones de Azure Mobile predeterminada.
* `MapApiControllers()`ofrece capacidades de API personalizadas para WebAPI controladores decorada con el `[MobileAppController]` atributo.
* `AddTables()`Proporciona una asignación de la `/tables` puntos finales de los controladores de la tabla.
* `AddTablesWithEntityFramework()`es un breve disponible para la asignación del `/tables` controladores basados en los extremos con el marco de trabajo de entidad.
* `AddPushNotifications()`Proporciona un método simple para registrar dispositivos para Hubs de notificación.
* `MapLegacyCrossDomainController()`Proporciona los encabezados CORS estándar para el desarrollo local.

### <a name="sdk-extensions"></a>Extensiones SDK

Los siguientes paquetes de extensión basada en NuGet proporcionan diversas características para móviles que pueden ser usadas por la aplicación. Habilitar extensiones durante la inicialización mediante el objeto **MobileAppConfiguration** .

- [Microsoft.Azure.Mobile.Server.Quickstart] es compatible con la configuración básica de aplicaciones móviles. Agregar a la configuración llamando el método de extensión **UseDefaultConfiguration** durante la inicialización. Esta extensión incluye los siguientes extensiones: notificaciones, autenticación, entidad, tablas, dominios y paquetes de inicio. Este paquete se usa en el tutorial de aplicaciones móviles disponibles en el portal de Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) 
   implementa el valor predeterminado *esta aplicación móvil está funcionando página* para la raíz del sitio web. Agregar a la configuración llamando al método de extensión   **AddMobileAppHomeController** .

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) 
   incluye clases para trabajar con datos y los conjuntos de la canalización de datos. Agregar a la configuración llamando al método de extensión **AddTables** .

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) 
   permite al marco de entidad a datos de access en la base de datos de SQL. Agregar a la configuración llamando al método de extensión **AddTablesWithEntityFramework** .

- [Microsoft.Azure.Mobile.Server.Authentication] habilita la autenticación y el software intermedio OWIN utilizado para validar tokens conjuntos de seguridad. Agregar a la configuración llamando el **AddAppServiceAuthentication**  
   y **IAppBuilder**. Métodos de extensión **UseAppServiceAuthentication** .

- [Microsoft.Azure.Mobile.Server.Notifications] permite notificaciones de inserción y define un extremo de registro de inserción. Agregar a la configuración llamando al método de extensión **AddPushNotifications** .

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) 
   crea un controlador que sirve de datos para exploradores web heredados desde la aplicación móvil. Agregar a la configuración llamando al método de extensión   **MapLegacyCrossDomainController** .

- [Microsoft.Azure.Mobile.Server.Login] proporciona el método AppServiceLoginHandler.CreateToken(), que es un método estático que se utiliza durante los escenarios de autenticación personalizada.   

## <a name="publish-server-project"></a>Cómo: publicar el proyecto de servidor

Esta sección muestra cómo publicar el proyecto de back-end de .NET desde Visual Studio. También puede implementar el proyecto de back-end mediante Git o cualquiera de los otros métodos que se tratan en la [documentación de implementación de servicio de aplicaciones de Azure](../app-service-web/web-sites-deploy.md).

1. En Visual Studio, generar el proyecto para restaurar los paquetes de NuGet.

2. En el Explorador de soluciones, haga clic en el proyecto, haga clic en **Publicar**. La primera vez que publique, debe definir un perfil de publicación. Cuando ya tiene un perfil definido, puede seleccionarlo y haga clic en **Publicar**.

2. Si se le pida que seleccione un destino de publicación, haga clic en la **Aplicación de servicio de Microsoft Azure** > **siguiente**, a continuación, (si es necesario) iniciar sesión con sus credenciales de Azure. 
   Descargas de Visual Studio y almacena de forma segura su configuración directamente de Azure de publicación.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Elija la **suscripción**, seleccione el **Tipo de recurso** de la **vista**, expanda la **Aplicación móvil**, haga clic en la aplicación móvil de back-end y haga clic en **Aceptar**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Compruebe la información de perfil de publicación y haga clic en **Publicar**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Cuando la aplicación móvil de back-end ha publicado correctamente, verá una página de aterrizaje que indica éxito.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

##<a name="define-table-controller"></a>Cómo: definir un controlador de tabla

Definir un controlador de la tabla para mostrar una tabla SQL para clientes móviles.  Configurar un controlador de tabla requiere tres pasos:

1. Crear una clase de objeto de transferencia de datos (DTO).
2. Configurar una referencia de tabla en la clase DbContext Mobile.
3. Crear un controlador de la tabla.

Un objeto de transferencia de datos (DTO) es un objeto sencillo de C# que herede de `EntityData`.  Por ejemplo:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

El campo se utiliza para definir la tabla en la base de datos SQL.  Para crear la entrada de la base de datos, agregue un `DbSet<>` propiedad a la DbContext está utilizando.  En la plantilla de proyecto predeterminada para las aplicaciones móviles de Azure, se denomina la DbContext `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Si tiene instalado el SDK de Azure, ahora puede crear un controlador de tabla de la plantilla como sigue:

1. Haga clic en la carpeta de controladores y seleccione **Agregar** > **controlador...**.
2. Seleccione la opción **Controlador de tabla de Azure Mobile aplicaciones** y luego haga clic en **Agregar**.
3. En el cuadro de diálogo **Agregar controlador** :
    * En la lista desplegable de **clase de modelo** , seleccione el nuevo DTO.
    * En el menú desplegable **DbContext** , seleccione la clase DbContext del servicio móvil.
    * El nombre del controlador se creará.
4. Haga clic en **Agregar**.

El proyecto de servidor tutorial contiene un ejemplo de un simple **TodoItemController**.

### <a name="how-to-adjust-the-table-paging-size"></a>Cómo: ajustar el tamaño de paginación de tabla

De forma predeterminada, aplicaciones móviles de Azure devuelve 50 registros por solicitud.  Paginación se asegura de que el cliente no ocupar su subproceso de interfaz de usuario ni el servidor durante mucho tiempo, garantizar una buena experiencia de usuario. Para cambiar el tamaño de tabla paginación, aumentar el lado del servidor "tamaño de la consulta de permitido" y cambiar el tamaño de la página del cliente del lado servidor "tamaño de la consulta de permitido" se ajusta mediante el `EnableQuery` atributo:

    [EnableQuery(PageSize = 500)]

Garantizar PageSize es igual o mayor que el tamaño solicitado por el cliente.  Consulte el cliente específico documentación cómo para obtener más información sobre cómo cambiar el tamaño de la página de cliente.

## <a name="how-to-define-a-custom-api-controller"></a>Cómo: definir un controlador de API personalizado

El controlador de API personalizado proporciona la funcionalidad más básica para la aplicación móvil de back-end al exponer un extremo. Puede registrar un controlador de API específicas de portátiles mediante el atributo [MobileAppController]. La `MobileAppController` atributo registra la ruta, configura el serializador JSON de aplicaciones móviles y activa la [comprobación de versión de cliente](app-service-mobile-client-and-server-versioning.md).

1. En Visual Studio, haga clic en la carpeta de controladores y luego haga clic en **Agregar** > **controlador**, seleccione **controlador de Web API 2&mdash;vacía** y haga clic en **Agregar**.

2. Proporcionar un **nombre de controlador**, como por ejemplo `CustomController`y haga clic en **Agregar**.

3. En el nuevo archivo de clase de controlador, agregue la siguiente instrucción using:

        using Microsoft.Azure.Mobile.Server.Config;

4. El atributo **[MobileAppController]** se aplican a la definición de clase de controlador de API, como en el ejemplo siguiente:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }

4. En el archivo de App_Start/Startup.MobileApp.cs, agregue una llamada al método de extensión **MapApiControllers** , como en el ejemplo siguiente:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

También puede usar el `UseDefaultConfiguration()` método de extensión en lugar de `MapApiControllers()`. Aún se puede acceder a cualquier controlador que no tenga **MobileAppControllerAttribute** aplicado por los clientes, pero no se pueden utilizar correctamente los clientes mediante un cliente de aplicación Mobile SDK.

## <a name="how-to-work-with-authentication"></a>Cómo: trabajar con la autenticación

Aplicaciones de Azure Mobile usa la aplicación de servicio de autenticación y autorización para proteger su back-end móvil.  Esta sección muestra cómo realizar las siguientes tareas relacionadas con la autenticación en el proyecto de servidor back-end de .NET:

+ [Cómo: agregar autenticación a un proyecto de servidor](#add-auth)
+ [Cómo: usar autenticación personalizada para la aplicación](#custom-auth)
+ [Cómo: recuperar autenticado información de usuario](#user-info)
+ [Cómo: restringir el acceso de datos para los usuarios autorizados](#authorize)

### <a name="add-auth"></a>Cómo: agregar autenticación a un proyecto de servidor

Puede agregar autenticación al proyecto server ampliar el objeto **MobileAppConfiguration** y configurando software intermedio OWIN. Cuando instale el paquete de [Microsoft.Azure.Mobile.Server.Quickstart] y llamar al método de extensión **UseDefaultConfiguration** , vaya al paso 3.

1. En Visual Studio, instale el paquete de [Microsoft.Azure.Mobile.Server.Authentication] .

2. En el archivo de proyecto Startup.cs, agregue la siguiente línea de código al principio del método de **configuración** :

        app.UseAppServiceAuthentication(config);

    Este componente de software intermedio OWIN valida tokens de la puerta de enlace asociada de servicio de aplicación.

3. Agregar la `[Authorize]` atributo a cualquier controlador o método que requiere autenticación. 

Para obtener información acerca de cómo autenticar a los clientes a su servidor de aplicaciones móviles, vea [agregar autenticación para su aplicación](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Cómo: usar autenticación personalizada para la aplicación

Si no desea usar uno de los proveedores de autenticación y autorización del servicio de aplicación, puede implementar su propio sistema de inicio de sesión. Instale el paquete de [Microsoft.Azure.Mobile.Server.Login] para ayudar con la generación de token de autenticación.  Proporcionar su propio código para validar las credenciales de usuario. Por ejemplo, podría comprobar contra saladas y hash contraseñas en una base de datos. En el ejemplo siguiente, la `isValidAssertion()` método (definido en otra aplicación) es el responsable de estas comprobaciones.

La autenticación personalizada se expone creando un ApiController y exponer `register` y `login` acciones. El cliente debe utilizar una interfaz de usuario personalizada para recopilar la información del usuario.  La información se envía a la API con una llamada de HTTP POST estándar. Una vez que el servidor valida la aserción, un token emitido mediante la `AppServiceLoginHandler.CreateToken()` método.  ApiController **no se debe** usar la `[MobileAppController]` atributo. 

Un ejemplo `login` acción:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

En el ejemplo anterior, LoginResult y LoginResultUser son objetos serializables exponer propiedades necesarias. El cliente espera respuestas de inicio de sesión que se devolverá como objetos JSON del formulario:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

La `AppServiceLoginHandler.CreateToken()` método incluye un parámetro de _emisor_ y una _audiencia_ . Ambos parámetros se establecen en la dirección URL de la raíz de la aplicación, usando el esquema HTTPS. Del mismo modo debe establecer _secretKey_ sea que el valor de la aplicación de la clave de firma. No distribuya la clave de firma en un cliente que se puede utilizar para menta teclas y representar a los usuarios. Puede obtener la clave de firma mientras alojado en la aplicación de servicio mediante una referencia a la _sitio Web\_AUTH\_firma\_clave_ variable de entorno. Si es necesario en un contexto de depuración local, siga las instrucciones de la sección [Local de depuración con autenticación](#local-debug) para recuperar la clave y guardarla como una configuración de aplicación.

También puede incluir el token emitido otras reclamaciones y una fecha de expiración.  Como mínimo, el token emitido debe incluir una solicitud de asunto (**sub**).

Es compatible con el cliente estándar `loginAsync()` método por la sobrecarga de la ruta de autenticación.  Si el cliente llama `client.loginAsync('custom');` para iniciar sesión, debe ser la ruta `/.auth/login/custom`.  Puede establecer la ruta para el controlador de autenticación personalizada mediante `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

>[AZURE.TIP] Uso de la `loginAsync()` enfoque garantiza que el símbolo de autenticación está conectado a cada llamada posterior al servicio.

###<a name="user-info"></a>Cómo: recuperar autenticado información de usuario

Cuando un usuario se autentica mediante la aplicación de servicio, puede tener acceso el identificador de usuario asignado y otra información en el código de back-end .NET. La información de usuario se puede utilizar para tomar decisiones de autorización en el servidor. El código siguiente obtiene el identificador de usuario asociado a una solicitud:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

El SID se deriva el identificador de usuario específicas del proveedor y es estático para un usuario determinado y un proveedor de inicio de sesión.  El SID es null para tokens de autenticación no válido.

Aplicación de servicio también le permite solicitar notificaciones específicas de su proveedor de inicio de sesión. Cada proveedor de identidades puede proporcionar más información con el proveedor de identidades SDK.  Por ejemplo, puede usar la API de Facebook gráfico para obtener información de amigos.  Puede especificar reclamaciones que se solicitan en el módulo de proveedor en el portal de Azure. Algunas notificaciones requieren configuración adicional con el proveedor de identidades.

El código siguiente llama al método de extensión de **GetAppServiceIdentityAsync** para obtener las credenciales de inicio de sesión, que incluyen el token de acceso es necesario realizar solicitudes con la API de Facebook gráfico:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Agregar un uso de la declaración de `System.Security.Principal` para proporcionar el método de extensión **GetAppServiceIdentityAsync** .

### <a name="authorize"></a>Cómo: restringir el acceso de datos para los usuarios autorizados

En la sección anterior, mostramos cómo recuperar el identificador de usuario de un usuario autenticado. Para restringir el acceso a datos y otros recursos basándose en este valor. Por ejemplo, agregar una columna de identificador de usuario a tablas y filtrar los resultados de la consulta mediante el identificador de usuario es una forma sencilla para limitar los datos devueltos únicamente a los usuarios autorizados. El código siguiente devuelve filas de datos solo cuando el SID coincide con el valor de la columna ID de usuario en la tabla TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

La `Query()` método devuelve un `IQueryable` que pueden manipular LINQ manejar el filtrado.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Cómo: agregar inserción notificaciones para un proyecto de servidor

Agregar las notificaciones de inserción al proyecto server ampliar el objeto **MobileAppConfiguration** y creando a un cliente Hubs de notificación.

1. En Visual Studio, haga clic en el proyecto de servidor y haga clic en **Administrar paquetes de NuGet**, busque `Microsoft.Azure.Mobile.Server.Notifications`, a continuación, haga clic en **instalar**. 

2. Repita este paso para instalar la `Microsoft.Azure.NotificationHubs` paquete, que incluye la biblioteca de cliente de Hubs de notificación.

3. En App_Start/Startup.MobileApp.cs y agregue una llamada al método de extensión **AddPushNotifications()** durante la inicialización:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);

4. Agregue el código siguiente que crea a un cliente Hubs de notificación:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Ahora puede usar al cliente de notificación Hubs enviar notificaciones de inserción a dispositivos registrados. Para obtener más información, vea [Agregar las notificaciones de inserción a la aplicación](app-service-mobile-ios-get-started-push.md). Para obtener más información acerca de la notificación Hubs, vea [Información general de Hubs de notificación](../notification-hubs/notification-hubs-push-notification-overview.md).

##<a name="tags"></a>Cómo: habilitar dirigirá la inserción con etiquetas

Hubs de notificación le permite enviar notificaciones de destinadas a los registros específicos con etiquetas. Varias etiquetas se crean automáticamente:

* El identificador de instalación identifica un dispositivo específico.
* El identificador de usuario en función del SID autenticado identifica un usuario específico.

El identificador de instalación puede tener acceso desde la propiedad **installationId** en la **MobileServiceClient**.  En el ejemplo siguiente se muestra cómo usar un identificador de instalación para agregar una etiqueta a una instalación específica en Hubs de notificación:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Las etiquetas suministradas por el cliente durante el registro de notificación de inserción se pasan por alto el servidor al crear la instalación. Para habilitar un cliente agregar etiquetas a la instalación, debe crear una API personalizada que agrega etiquetas mediante el modelo anterior. 

Vea [las etiquetas de notificación de inserción de cliente agregado] [ 5] en el ejemplo de tutorial rápido completadas de aplicación de servicio móvil aplicaciones para obtener un ejemplo.

##<a name="push-user"></a>Cómo: enviar notificaciones de inserción a un usuario autenticado

Cuando un usuario autenticado se registra para las notificaciones de inserción, una etiqueta de ID de usuario se agrega automáticamente al registro. Mediante el uso de esta etiqueta, puede enviar notificaciones de inserción a todos los dispositivos registrados por esa persona. El código siguiente obtiene al SID del usuario que hace la solicitud y envía una notificación de inserción de plantilla para cada registro de dispositivo de esa persona:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Cuando se registra para las notificaciones de inserción de un cliente autenticado, asegúrese de que la autenticación es completa antes de intentar el registro. Para obtener más información, vea [Insertar a los usuarios] [ 6] en el ejemplo de tutorial rápido completadas de aplicación de servicio móvil aplicaciones para .NET back-end.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Cómo: depurar y solucionar problemas de .NET Server SDK

Servicio de aplicación Azure proporciona varios depuración y técnicas para aplicaciones ASP.NET de solución de problemas:

- [Supervisar el servicio de una aplicación de Azure](../app-service-web/web-sites-monitor.md)
- [Habilitar el registro de diagnóstico en Azure de aplicación de servicio](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Solucionar problemas de un servicio de aplicación de Azure en Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Registro de información

Puede escribir en registros de diagnóstico del servicio de aplicación mediante la escritura de seguimiento ASP.NET estándar. Antes de escribir en los registros, debe habilitar diagnósticos en la aplicación móvil de back-end.

Para habilitar los diagnósticos y escribir en los registros:

1. Siga los pasos descritos en [cómo habilitar diagnósticos](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Agregue la siguiente instrucción using en el archivo de código:

        using System.Web.Http.Tracing;

3. Crear un escritor de seguimiento para escribir el servidor de .NET en los registros de diagnóstico, como sigue:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");

4. Volver a publicar el proyecto de servidor y obtener acceso a la aplicación móvil de back-end para ejecutar la ruta de acceso del código con el registro.

5. Descargar y evaluar los registros, como se describe en [Cómo: descargar registros](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Local con autenticación de depuración

Puede ejecutar la aplicación localmente para probar los cambios antes de su publicación en la nube. Para back-ends la mayoría de aplicaciones de Azure Mobile, presione *F5* en Visual Studio. Sin embargo, hay algunas consideraciones adicionales que usan autenticación.

Debe tener una aplicación móvil basada en nube con aplicación de servicio de autenticación/autorización configurado y el cliente debe tener el extremo de nube especificado como el host de inicio de sesión alternativo. Consulte la documentación de la plataforma de cliente para los pasos concretos necesarios.

Asegúrese de que su back-end móvil tiene [Microsoft.Azure.Mobile.Server.Authentication] instalado. A continuación, en la clase de inicio OWIN de la aplicación, agregue lo siguiente, después `MobileAppConfiguration` se ha aplicado a su `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

En el ejemplo anterior, debe configurar la configuración de la aplicación _authAudience_ y _authIssuer_ en su archivo Web.config a cada ser la dirección URL de la raíz de la aplicación, usando el esquema HTTPS. Del mismo modo debe establecer _authSigningKey_ sea que el valor de la aplicación de la clave de firma. Para obtener la clave de firma:

1. Vaya a la aplicación en el [portal de Azure] 
2. Haga clic en **Herramientas**, **Kudu**, **vaya**.
3. En el sitio de administración de Kudu, haga clic en el **entorno**.
4. Buscar el valor de _sitio Web\_AUTH\_firma\_clave_. 

Usar la clave de firma para el parámetro _authSigningKey_ en la configuración de la aplicación local.  Ahora dispone de su back-end móvil para validar tokens cuando se ejecuta localmente, el cliente obtiene el token desde el extremo de la nube.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Portal de Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx

