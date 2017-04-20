<properties
    pageTitle="Actualización de servicios móviles a Azure de aplicación de servicio - Node.js"
    description="Obtenga información sobre cómo actualizar fácilmente la aplicación de servicios móviles para una aplicación móvil de servicio de aplicación"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Actualizar el servicio de móvil de Azure Node.js existente a la aplicación de servicio

Servicio de la aplicación móvil es una forma nueva para generar aplicaciones móviles con Microsoft Azure. Para obtener más información, consulte [¿qué aplicaciones móviles?].

En este tema se describe cómo actualizar una aplicación existente de back-end de Node.js de servicios móviles de Azure a una nueva aplicación Servicio móvil aplicaciones. Mientras realiza esta actualización, puede que la aplicación de servicios móviles existente seguirán funcionando.  Si necesita actualizar una aplicación de back-end Node.js, consulte [actualizar los servicios de Mobile .NET](./app-service-mobile-net-upgrading-from-mobile-services.md).

Cuando se actualiza un back-end móvil al servicio de la aplicación de Azure, tiene acceso a todas las características de servicio de aplicaciones y se cargarán según [tarifas de servicio de aplicación], no los servicios móviles de precios.

## <a name="migrate-vs-upgrade"></a>Migrar frente a la actualización

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Se recomienda que [realizar una migración](app-service-mobile-migrating-from-mobile-services.md) antes de pasar a través de una actualización. De este modo, puede colocar ambas versiones de la aplicación en el mismo Plan de servicio de aplicación y no provocar costo adicional.

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Mejoras en el SDK de Mobile aplicaciones Node.js server

Actualizar a la nueva [SDK de aplicaciones móviles](https://www.npmjs.com/package/azure-mobile-apps) proporciona una gran cantidad de mejoras, que incluyen:

- Basándose en el [marco de Express](http://expressjs.com/en/index.html), el nuevo SDK de nodo está atenuado y diseñados para mantenerse con nuevas versiones de nodo tal y como llegan. Puede personalizar el comportamiento de la aplicación con software intermedio Express.

- Mejoras de rendimiento significativas en comparación con el SDK de servicios móviles.

- Ahora puede hospedar un sitio Web junto con el servidor móvil; Asimismo, es muy fácil agregar el SDK de Azure Mobile a cualquier aplicación de express.v4 existente.

- Integrado para el desarrollo de varias plataformas y local, puede desarrollado el SDK de aplicaciones móviles y ejecute localmente en plataformas Windows, Linux y OSX. Ahora es fácil de utilizar técnicas de desarrollo de nodo comunes como ejecutar [Mocha](https://mochajs.org/) pruebas antes de la implementación.

## <a name="overview"></a>Visión general de actualización básica

Para ayudar a actualizar Node.js back-end, servicio de aplicación de Azure le ha proporcionado un paquete de compatibilidad.  Después de la actualización, tendrá un sitio de niew que se puede implementar a un nuevo sitio de servicio de aplicación.

El cliente de servicios móviles SDK son **no** es compatible con el nuevo servidor de aplicaciones de Mobile SDK. Para proporcionar continuidad del servicio de la aplicación, no debe publicar los cambios en un sitio de servicio a clientes publicados. En su lugar, debe crear una nueva aplicación móvil que sirve como un duplicado. Puede colocar esta aplicación en el mismo plan de servicio de la aplicación para evitar provocar costo financiero adicional.

A continuación, tendrá dos versiones de la aplicación: uno que sea la misma y sirve publica aplicaciones en silvestres y otro que, a continuación, puede actualizar y de destino con una nueva versión de cliente. Puede mover y probar el código a su ritmo, pero debe asegurarse de que las correcciones de errores que se aplican a ambos. Una vez que se observa que un número deseado de aplicaciones en la naturaleza han actualizado a la última versión de cliente, puede eliminar la aplicación migrada original si así lo desea. No incurre en los costos monetarios adicionales, si está alojado en el mismo plan de servicio de la aplicación que la aplicación móvil.

El esquema completo para el proceso de actualización es la siguiente:

1. Descargue el servicio de móvil Azure (migrado) existente.
2. Convertir el proyecto en una aplicación móvil de Azure mediante el paquete de compatibilidad.
3. Corregir las diferencias (como la configuración de autenticación).
4. Implemente el proyecto de la aplicación móvil de Azure convertir a un nuevo servicio de aplicación.
4. Crear una nueva versión de la aplicación cliente que usan la nueva aplicación móvil.
5. (Opcional) Eliminar la aplicación de servicio móvil migrados original.

Eliminación puede producirse cuando no ve ningún tráfico en su servicio móvil migrado original.

## <a name="install-npm-package"></a>Instalar los requisitos previos

Debe instalar [nodo] en el equipo local.  También debe instalar el paquete de compatibilidad.  Después de instala el nodo, puede ejecutar el comando siguiente desde un nuevo cmd o el símbolo del sistema de PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a>Obtener las secuencias de comandos de servicios móviles Azure

- Inicie sesión en el [Portal de Azure].
- Usa **Todos los recursos** o **Servicios de aplicación**, busque el sitio de servicios móviles.
- En el sitio, haga clic en **Herramientas** -> **Kudu** -> **Ir** para abrir el sitio Kudu.
- Haga clic en la **Consola de depuración** -> **PowerShell** para abrir la consola de depuración.
- Vaya a `site/wwwroot/App_Data/config` haciendo clic en cada directorio por separado
- Haga clic en el icono de descarga junto a la `scripts` directorio.

Esto descargará las secuencias de comandos en formato ZIP.  Crear un nuevo directorio en el equipo local y descomprimir el `scripts.ZIP` archivo dentro del directorio.  Esta opción creará una `scripts` directorio.

## <a name="scaffold-app"></a>Scaffold el nuevo back-end de aplicaciones móviles de Azure

Ejecute el siguiente comando desde el directorio que contiene el directorio de secuencias de comandos:

```scaffold-mobile-app scripts out```

Esta opción creará un scaffolded back-end aplicaciones móviles de Azure en la `out` directorio.  Aunque no es necesario, es una buena idea activar el `out` directorio en un repositorio de código fuente de su elección.

## <a name="deploy-ama-app"></a>Implementar el servidor de aplicaciones móviles de Azure

Durante la implementación, debe hacer lo siguiente:

1. Crear una nueva aplicación móvil en el [Portal de Azure].
2. Ejecutar la `createViews.sql` secuencia de comandos de la base de datos conectado.
3. Vínculo a la base de datos que está vinculado a su servicio móvil a su nuevo servicio de aplicación.
4. Vincular los otros recursos (como notificación Hubs) para el nuevo servicio de aplicación.
5. Implementar el código generado a su nuevo sitio.

### <a name="create-a-new-mobile-app"></a>Crear una nueva aplicación móvil

1. Iniciar sesión en el [Portal de Azure].

2. Haga clic en **+ nuevo** > **Web + Mobile** > **Aplicación móvil**, a continuación, proporcione un nombre para la aplicación móvil de back-end.

3. Para el **Grupo de recursos**, seleccione un grupo de recursos existente o cree uno nuevo (con el mismo nombre que su aplicación.) 
 
    Puede seleccionar otro plan de servicio de aplicación o cree uno nuevo. Para obtener más información acerca de los servicios de aplicación planes y cómo crear un nuevo plan en diferentes precios de nivel y en la ubicación deseada, vea [introducción exhaustiva de planes de servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Para el **plan de servicios de aplicación**, se selecciona el plan predeterminado (en el [nivel estándar](https://azure.microsoft.com/pricing/details/app-service/)). También puede seleccionar un plan diferente o [cree uno nuevo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Configuración del plan de servicio de aplicación determina la [ubicación, características, costo y recursos de cálculo](https://azure.microsoft.com/pricing/details/app-service/) asociado a la aplicación. 

    Después de decidir el plan, haga clic en **crear**. Esto crea la aplicación móvil de back-end. 


### <a name="run-createviewssql"></a>Ejecutar CreateViews.SQL

La aplicación scaffolded contiene un archivo llamado `createViews.sql`.  Debe ejecutar esta secuencia de comandos en la base de datos de destino.  Puede obtener la cadena de conexión para la base de datos de destino de su servicio móvil migrado desde el módulo de **configuración** en **Las cadenas de conexión**.  Se denomina `MS_TableConnectionString`.

Puede ejecutar esta secuencia de comandos de SQL Server Management Studio o Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Vínculo de la base de datos a su aplicación de servicio

Vincular la base de datos existente en el servicio de aplicación:

- En el [Portal de Azure], abra su aplicación de servicio.
- Seleccione **todas las configuraciones** -> **conexiones de datos**.
- Haga clic en **+ Add**.
- En la lista desplegable, seleccione la **Base de datos de SQL**
- En **La base de datos de SQL**, seleccione la base de datos existente y haga clic en **Seleccionar**.
- En la **cadena de conexión**, escriba el nombre de usuario y la contraseña para la base de datos y después haga clic en **Aceptar**.
- En el módulo **Agregar conexiones de datos** , haga clic en **Aceptar**.

El nombre de usuario y la contraseña pueden encontrarse viendo la cadena de conexión para la base de datos de destino en el servicio móvil de migrados.


### <a name="set-up-authentication"></a>Configurar la autenticación

Azure aplicaciones móviles le permite configurar Azure Active Directory, Facebook, Google, Microsoft y Twitter la autenticación dentro del servicio.  Autenticación personalizada tendrán que se desarrollen por separado.  Consulte la documentación de [Los conceptos de autenticación] y [Tutorial de autenticación] para obtener más información.  

## <a name="updating-clients"></a>Actualizar a los clientes móviles

Una vez que tenga una aplicación móvil operativas, back-end, puede trabajar en una nueva versión de la aplicación cliente que utiliza. Aplicaciones móviles también incluye una nueva versión del cliente SDK y similar a la actualización del servidor encima, debe quitar todas las referencias a los SDK de servicios de Mobile antes de instalar las versiones de aplicaciones móviles.

Uno de los cambios entre las versiones principales es que los constructores ya no requieren una tecla de aplicación. Ahora simplemente es que pase la dirección URL de la aplicación móvil. Por ejemplo, en los clientes. NET, la `MobileServiceClient` constructor está ahora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Puede leer acerca de lo nuevos SDK de instalación y uso de la nueva estructura a través de los vínculos siguientes:

- [Versión Android 2.2 o posterior](app-service-mobile-android-how-to-use-client-library.md)
- [iOS versión 3.0.0 o posterior](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versión 2.0.0 o posterior](app-service-mobile-dotnet-how-to-use-client-library.md)
- [Cordova Apache versión 2.0 o posterior](app-service-mobile-cordova-how-to-use-client-library.md)

Si su aplicación hace uso de las notificaciones de inserción, tome nota de las instrucciones de registro específico para cada plataforma, como ha habido algunos cambios que considere oportunos también.

Cuando tenga la nueva versión de cliente lista, probar con el proyecto de servidor actualizado. Después de validar que funciona, puede lanzar una nueva versión de la aplicación a clientes. Finalmente, una vez que sus clientes han tenido la oportunidad de recibir estas actualizaciones, puede eliminar la versión de servicios móviles de la aplicación. En este momento, ha actualizado completamente a una aplicación de aplicación de servicio móvil con el servidor de aplicaciones de Mobile SDK más reciente.

<!-- URLs. -->

[Portal de Azure]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[¿Qué aplicaciones móviles?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Precios de aplicación de servicio]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Conceptos de autenticación]: ../app-service/app-service-authentication-overview.md
[Tutorial de autenticación]: app-service-mobile-auth.md

[Portal de Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
