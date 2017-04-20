<properties
    pageTitle="Actualización de servicios móviles a Azure de aplicación de servicio"
    description="Obtenga información sobre cómo actualizar fácilmente la aplicación de servicios móviles para una aplicación móvil de servicio de aplicación"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Actualizar el servicio de móvil de Azure .NET existente al servicio de aplicación

Servicio de la aplicación móvil es una forma nueva para generar aplicaciones móviles con Microsoft Azure. Para obtener más información, consulte [¿qué aplicaciones móviles?].

En este tema se describe cómo actualizar una aplicación existente de back-end de .NET de servicios móviles de Azure a una nueva aplicación Servicio móvil aplicaciones. Mientras realiza esta actualización, puede que la aplicación de servicios móviles existente seguirán funcionando.   Si necesita actualizar una aplicación de back-end Node.js, consulte [actualizar los servicios de Mobile Node.js](./app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Cuando se actualiza un back-end móvil al servicio de la aplicación de Azure, tiene acceso a todas las características de servicio de aplicaciones y se cargarán según [tarifas de servicio de aplicación], no los servicios móviles de precios.

##<a name="migrate-vs-upgrade"></a>Migrar frente a la actualización

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Se recomienda que [realizar una migración](app-service-mobile-migrating-from-mobile-services.md) antes de pasar a través de una actualización. De este modo, puede colocar ambas versiones de la aplicación en el mismo Plan de servicio de aplicación y no provocar costo adicional.

###<a name="improvements-in-mobile-apps-net-server-sdk"></a>Mejoras en el servidor de Mobile aplicaciones .NET SDK

Actualizar a la nueva [SDK de aplicaciones móviles](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) proporciona las siguientes ventajas:

- Más flexibilidad sobre las dependencias de NuGet. El entorno de hospedaje ya no proporciona sus propias versiones de paquetes de NuGet, por lo que puede usar alternativas versiones compatibles. Sin embargo, si hay nuevos bugfixes críticas o actualizaciones de seguridad en el SDK de servidor Mobile o dependencias, debe actualizar manualmente su servicio.

- Más flexibilidad en el SDK de móvil. Puede controlar explícitamente qué características y rutas están configuradas, como autenticación, las API de la tabla y el extremo del registro de inserción. Para obtener más información, vea [cómo usar el servidor de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Compatibilidad con otros tipos de proyectos ASP.NET y rutas. Ahora puede hospedar controladores MVC y API de Web en el mismo proyecto como el proyecto móvil back-end.

- Soporte técnico para las nuevas características de autenticación de servicio de aplicaciones, que le permite usar una configuración de autenticación común a través de la web y aplicaciones móviles.

##<a name="overview"></a>Visión general de actualización básica

En muchos casos, la actualización será tan sencilla como cambiar al nuevo servidor de aplicaciones de Mobile SDK y volver a publicar el código en una nueva instancia de la aplicación móvil. Hay, sin embargo algunos escenarios que requieren una configuración adicional, como los escenarios de autenticación avanzada y trabajar con programar trabajos. Cada una de ellas se describe en las secciones posteriores.

>[AZURE.TIP] Es recomendable que leer y entender el resto de este tema completamente antes de iniciar una actualización. Tome nota de las características que utilizar que están resaltadas debajo.

El cliente de servicios móviles SDK son **no** es compatible con el nuevo servidor de aplicaciones de Mobile SDK. Para proporcionar continuidad del servicio de la aplicación, no debe publicar los cambios en un sitio de servicio a clientes publicados. En su lugar, debe crear una nueva aplicación móvil que sirve como un duplicado. Puede colocar esta aplicación en el mismo plan de servicio de la aplicación para evitar provocar costo financiero adicional.

A continuación, tendrá dos versiones de la aplicación: uno que sea la misma y sirve publica aplicaciones en silvestres y otro que, a continuación, puede actualizar y de destino con una nueva versión de cliente. Puede mover y probar el código a su ritmo, pero debe asegurarse de que las correcciones de errores que se aplican a ambos. Una vez que se observa que un número deseado de aplicaciones en la naturaleza han actualizado a la última versión de cliente, puede eliminar la aplicación migrada original si así lo desea.

El esquema completo para el proceso de actualización es la siguiente:

1. Crear una nueva aplicación móvil
2. Actualizar el proyecto para usar el nuevo SDK de servidor
3. Crear una nueva versión de la aplicación de cliente
4. (Opcional) Eliminar la instancia migrada original

##<a name="mobile-app-version"></a>Crear una segunda instancia de aplicación
El primer paso para actualizar es crear el recurso de la aplicación móvil que contendrá la nueva versión de la aplicación. Si ya ha migrado un servicio móvil existente, desea crear esta versión en el mismo plan de hospedaje. Abra el [portal de Azure] y desplácese a la aplicación migrada. Anote el Plan de servicio de aplicación que se ejecuta en.

A continuación, cree la segunda instancia de aplicación siguiendo las [instrucciones de creación de .NET back-end](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Cuando se le pida seleccione Plan de servicios de aplicación o "plan de hospedaje" elegir el plan de la aplicación migrado.

Que es probable que desea usar la misma base de datos y notificación concentrador como hizo en servicios para dispositivos móviles. Puede copiar estos valores, abra el [portal de Azure] y vaya a la aplicación original y luego haga clic en **configuración de** > **configuración de la aplicación**. En **Las cadenas de conexión**, copie `MS_NotificationHubConnectionString` y `MS_TableConnectionString`. Vaya a su nuevo sitio de actualización y péguelas en sobrescribir los valores existentes. Repita este proceso para cualquier otra configuración de aplicación sus necesidades de la aplicación. Si no usa un servicio migrado, puede leer cadenas de conexión y configuración de la aplicación de la ficha **Configurar** de la sección de servicios móviles del [portal clásica Azure].

Hacer una copia del proyecto ASP.NET para su aplicación y publicar a su nuevo sitio. Con una copia de la aplicación cliente actualizada con la nueva dirección URL, validar que todo funciona según lo esperado.

## <a name="updating-the-server-project"></a>Actualización del proyecto de servidor

Aplicaciones móviles proporciona un nuevo [SDK de servidor de aplicación móvil] que proporciona gran parte de la misma funcionalidad que el tiempo de ejecución de servicios móviles. En primer lugar, debe quitar todas las referencias a los paquetes de servicios móviles. En el Administrador de paquetes de NuGet, busque `WindowsAzure.MobileServices.Backend`. La mayoría de aplicaciones vea varios paquetes aquí, incluso `WindowsAzure.MobileServices.Backend.Tables` y `WindowsAzure.MobileServices.Backend.Entity`. En este caso, empezará con el paquete más bajo en el árbol de dependencia, como `Entity`y quítela. Cuando se le solicite, no quite todos los paquetes de dependientes. Repita este proceso hasta que haya quitado `WindowsAzure.MobileServices.Backend` propiamente dicho.

En este momento, tendrá un proyecto que ya no se hace referencia SDK de servicios de Mobile.

A continuación agregará referencias el SDK de aplicaciones móviles. Para esta actualización de la mayoría de los desarrolladores desea descargar e instalar la `Microsoft.Azure.Mobile.Server.Quickstart` empaquetar, como extraerá todo el conjunto necesario.

Habrá unos cuantos errores del compilador es el resultado de las diferencias entre los SDK, pero son fáciles de dirección y en el resto de esta sección se tratan.

### <a name="base-configuration"></a>Configuración básica

A continuación, en WebApiConfig.cs, puede reemplazar:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

con

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

>[AZURE.NOTE] Si desea obtener más información sobre el nuevo servidor .NET SDK y cómo agregar o quitar características de la aplicación, vea el tema de [cómo usar el SDK de .NET server] .

Si su aplicación realiza el uso de las características de autenticación, también debe registrar un software de OWIN intermedio. En este caso, debe mover el código de configuración anterior en una nueva clase de inicio de OWIN.

1. Agregar el paquete de NuGet `Microsoft.Owin.Host.SystemWeb` si ya no está incluido en el proyecto.
2. En Visual Studio, haga clic con el botón secundario en el proyecto y seleccione **Agregar** -> **Nuevo elemento**. Seleccione **Web** -> **General** -> **clase OWIN inicio**.
3. Mover el código anterior para MobileAppConfiguration de `WebApiConfig.Register()` a la `Configuration()` método de su nueva clase de inicio.

Asegúrese de que el `Configuration()` método termina con:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Hay cambios adicionales relacionados con la autenticación que aparecen en la sección autenticación completa.

### <a name="working-with-data"></a>Trabajar con datos

En servicios móviles, el nombre de la aplicación móvil a servir como el nombre del esquema de forma predeterminada en la configuración del marco de la entidad.

Para asegurarse de que tiene el mismo esquema que se hace referencia como antes, use el siguiente para establecer el esquema de la DbContext para su aplicación:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Asegúrese de que tiene MS_MobileServiceName establecer automáticamente las respuestas anteriores. También puede proporcionar otro nombre de esquema si su aplicación personalizó previamente.

### <a name="system-properties"></a>Propiedades del sistema

#### <a name="naming"></a>Los nombres

En el servidor de servicios móviles de Azure SDK, propiedades del sistema siempre contienen un carácter de subrayado doble (`__`) prefijo para las propiedades:

- __createdAt
- __updatedAt
- __deleted
- __version

El cliente de servicios móviles SDK tiene lógica especial para analizar las propiedades del sistema en este formato.

En las aplicaciones móviles de Azure, propiedades del sistema ya no tienen un formato especial y tienen los siguientes nombres:

- registroen
- updatedAt
- eliminar
- Versión

El cliente de aplicaciones Mobile SDK utilizar los nuevos nombres de propiedades del sistema, por lo que no son necesarios para el código de cliente cambios. Sin embargo, si directamente para realizar llamadas resto en el servicio, a continuación, debe cambiar las consultas en consecuencia.

#### <a name="local-store"></a>Almacén local

Los cambios en los nombres de las propiedades del sistema, quiere decir que una base de datos local de sincronización sin conexión para los servicios de Mobile no es compatible con aplicaciones móviles. Si es posible, debe evitar la actualización de aplicaciones de servicios móviles aplicaciones móviles hasta después de cambios pendientes se han enviado al servidor de cliente. A continuación, la aplicación actualizada debe usar un nombre de archivo de base de datos nueva.

Si se actualiza una aplicación de cliente de servicios móviles aplicaciones móviles, mientras que hay cambios sin conexión pendientes en la cola de operaciones, la base de datos se debe actualizar para usar los nuevos nombres de columna. En iOS, esto puede conseguirse mediante migraciones ligeras para cambiar los nombres de columna. En Android y el cliente de .NET administrado, debe escribir SQL personalizado para cambiar el nombre de las columnas para las tablas de objeto de datos.

En iOS, debe cambiar el esquema de datos básicos para las entidades de datos para que coincida con el siguiente. Tenga en cuenta que las propiedades `createdAt`, `updatedAt` y `version` ya no tiene un `ms_` prefijo:

| Atributo |  Tipo   | Nota                                                 |
|---------- |  ------ | -----------------------------------------------------|
| Id.        | Cadena de marcado necesaria  | clave principal en el almacén remoto         |
| registroen | Fecha    | (opcional) se asigna a la propiedad del sistema registroen         |
| updatedAt | Fecha    | (opcional) se asigna a la propiedad del sistema updatedAt         |
| Versión   | Cadena  | utiliza (opcional) para detectar conflictos, se asigna a la versión |

#### <a name="querying-system-properties"></a>Consulta de las propiedades del sistema

En servicios de Azure móviles, propiedades del sistema no se envían de forma predeterminada, pero solo cuando se solicitan usando la cadena de consulta `__systemProperties`. Por el contrario, en el sistema de aplicaciones móviles de Azure propiedades son **siempre seleccionado** ya forman parte del modelo de objetos de servidor SDK.

Este cambio afecta principalmente implementaciones personalizadas de administradores de dominio, como las extensiones de `MappedEntityDomainManager`. En servicios móviles, si un cliente no solicita nunca las propiedades del sistema, es posible utilizar un `MappedEntityDomainManager` que realmente no asignar todas las propiedades. Sin embargo, en aplicaciones móviles de Azure, estas propiedades no asignadas producen un error en las consultas de obtener.

La manera más sencilla de resolver el problema es modificar el dto para que heredan de `ITableData` en lugar de `EntityData`. A continuación, agregue el `[NotMapped]` atributo a los campos que se deben omitir.

Por ejemplo, el siguiente define `TodoItem` sin propiedades del sistema:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Nota: si se producen errores `NotMapped`, agregue una referencia al ensamblado `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS

Servicios móviles incluye compatibilidad para CORS ajustando la solución ASP.NET CORS. Se quitó para dar a los desarrolladores más control, por lo que puede aprovechar directamente [CORS ASP.NET admiten](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)esta capa de ajuste.

Son las principales áreas de interés si usa CORS la `eTag` y `Location` encabezados se deben permitir en orden para el cliente SDK funcione correctamente.

### <a name="push-notifications"></a>Notificaciones de inserción
Inserción, el elemento principal que puede encontrarse con que faltan desde el SDK de servidor es la clase PushRegistrationHandler. Registros se administran ligeramente diferente en las aplicaciones móviles y los registros se habilitan de forma predeterminada. Administrar etiquetas puede realizarse usando API personalizadas. Vea las instrucciones de [registro para las etiquetas](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) para obtener más información.

### <a name="scheduled-jobs"></a>Tareas programadas
Tareas programadas no se integran en aplicaciones móviles, por lo que los trabajos existentes que tiene en el back-end de .NET deberá actualizarse de forma individual. Una opción es crear un programado [Trabajo Web] en el sitio de código de la aplicación móvil. También puede configurar un controlador que contiene el código de trabajo y configurar el [Programador de Azure] para acertar ese extremo en la programación esperada.

### <a name="miscellaneous-changes"></a>Varios cambios
Ahora debe tener todos los ApiControllers que se utiliza en un cliente móvil la `[MobileAppController]` atributo. Ya no incluye de forma predeterminada para que otros usuarios ApiControllers para ir afectadas por los formateadores móviles.

La `ApiServices` objeto ya no forma parte del SDK. Para obtener acceso a configuración de la aplicación móvil, puede utilizar lo siguiente:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Asimismo, registro ahora se realiza mediante la escritura de seguimiento ASP.NET estándar:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Consideraciones de autenticación

Los componentes de autenticación de servicios móviles ahora se han movido a la característica de autenticación o autorización del servicio de aplicación. Puede obtener la información acerca de cómo habilitar para su sitio lea el tema [autenticación de agregar a la aplicación móvil](app-service-mobile-ios-get-started-users.md) .

Para algunos proveedores, como AAD, Facebook y Google, debería poder aprovechar el registro existente desde la aplicación de copia. Simplemente debe ir al portal del proveedor de identidades y agregar una nueva dirección URL de redirección al registro. Configurar autenticación/autorización de aplicación de servicio con el identificador de cliente y el secreto.

### <a name="controller-action-authorization"></a>Autorización de acción de controlador
Todas las instancias de la `[AuthorizeLevel(AuthorizationLevel.User)]` atributo ahora debe cambiarse para usar ASP.NET estándar `[Authorize]` atributo. Además, los controladores están ahora anónimo de forma predeterminada, al igual que en otras aplicaciones de ASP.NET.
Si estaba utilizando una de las otras opciones de AuthorizeLevel, como administrador o aplicación, tenga en cuenta que se ha desaparecido. En su lugar puede configurar AuthorizationFilters para información confidencial compartida o configurar a una identidad de servicio de AAD para habilitar las llamadas de servicio de servicio de forma segura.

### <a name="getting-additional-user-information"></a>Obtener información de usuario adicionales

Puede obtener información de usuario adicionales, incluidos tokens de acceso a través de la `GetAppServiceIdentityAsync()` método:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Además, si su aplicación tarda dependencias en Id, por ejemplo, almacenarlos en una base de datos, es importante que tenga en cuenta que los identificadores de usuario entre servicios móviles y aplicaciones de aplicación de servicio móvil son diferentes. Aún puede obtener el identificador de usuario de servicios móviles, aunque. Todas las subclases ProviderCredentials tienen una propiedad de Id. Para continuar el ejemplo antes:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Si su aplicación tomar dependencias en los identificadores de usuario, es importante aprovechar el mismo registro con un proveedor de identidades si es posible. Identificadores de usuario normalmente ámbito están el registro de aplicación que se utilizó para introducir un nuevo registro podría crear problemas con que coincidan con los usuarios a sus datos.

### <a name="custom-authentication"></a>Autenticación personalizada

Si la aplicación está utilizando una solución de autenticación personalizada, desea asegurarse de que el sitio actualizado tiene acceso al sistema. Siga las instrucciones nuevas para la autenticación personalizada en la [información general sobre el SDK de servidor .NET] integrar la solución. Tenga en cuenta que son los componentes de autenticación personalizada en vista previa.

##<a name="updating-clients"></a>Actualización de clientes
Una vez que tenga una aplicación móvil operativas, back-end, puede trabajar en una nueva versión de la aplicación cliente que utiliza. Aplicaciones móviles también incluye una nueva versión del cliente SDK y similar a la actualización del servidor encima, debe quitar todas las referencias a los SDK de servicios de Mobile antes de instalar las versiones de aplicaciones móviles.

Uno de los cambios entre las versiones principales es que los constructores ya no requieren una tecla de aplicación. Ahora simplemente es que pase la dirección URL de la aplicación móvil. Por ejemplo, en los clientes. NET, la `MobileServiceClient` constructor está ahora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Puede leer acerca de lo nuevos SDK de instalación y uso de la nueva estructura a través de los vínculos siguientes:

- [iOS versión 3.0.0 o posterior](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versión 2.0.0 o posterior](app-service-mobile-dotnet-how-to-use-client-library.md)

Si su aplicación hace uso de las notificaciones de inserción, tome nota de las instrucciones de registro específico para cada plataforma, como ha habido algunos cambios que considere oportunos también.

Cuando tenga la nueva versión de cliente lista, probar con el proyecto de servidor actualizado. Después de validar que funciona, puede lanzar una nueva versión de la aplicación a clientes. Finalmente, una vez que sus clientes han tenido la oportunidad de recibir estas actualizaciones, puede eliminar la versión de servicios móviles de la aplicación. En este momento, ha actualizado completamente a una aplicación de aplicación de servicio móvil con el servidor de aplicaciones de Mobile SDK más reciente.

<!-- URLs. -->

[Portal de Azure]: https://portal.azure.com/
[Portal de clásico de Azure]: https://manage.windowsazure.com/
[¿Qué aplicaciones móviles?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[SDK de servidor de aplicación móvil]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Programador de Azure]: /en-us/documentation/services/scheduler/
[Trabajo de Web]: ../app-service-web/websites-webjobs-resources.md
[Cómo usar el servidor de .NET SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Precios de aplicación de servicio]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Información general sobre el SDK de servidor .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
