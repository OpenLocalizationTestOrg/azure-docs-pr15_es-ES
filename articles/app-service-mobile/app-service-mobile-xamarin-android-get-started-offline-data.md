<properties
    pageTitle="Habilitar la sincronización sin conexión para la aplicación móvil de Azure (Xamarin Android)"
    description="Obtenga información sobre cómo usar la aplicación móvil de aplicación de servicio para caché y sincronización de datos sin conexión en la aplicación Xamarin Android"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Habilitar la sincronización sin conexión para la aplicación móvil de Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Información general

En este tutorial se presenta la característica de sincronización sin conexión de aplicaciones móviles de Azure para Xamarin.Android. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil: ver, agregar o modificar datos, incluso cuando no hay ninguna conexión de red. Cambios se almacenan en una base de datos local.
Una vez que se vuelve a conectar el dispositivo, estos cambios se sincronizan con el servicio remoto.

En este tutorial, actualice el proyecto de cliente desde el tutorial [crear una aplicación de Xamarin Android] para admitir las características de aplicaciones de Azure Mobile sin conexión. Si no utiliza el proyecto de servidor de inicio rápido descargado, debe agregar los paquetes de extensión de acceso de datos a su proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información sobre la característica de sincronización sin conexión, vea el tema de la [Sincronización sin conexión de datos en las aplicaciones móviles de Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Actualizar la aplicación cliente para admitir características sin conexión

Características de sin conexión de Azure aplicación Mobile permiten interactuar con una base de datos local cuando se encuentra en un escenario sin conexión. Para usar estas características en la aplicación, inicializar un [SyncContext] en un almacén local. A continuación, hacer referencia a la tabla mediante la interfaz de [IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite se usa como el almacén local en el dispositivo.

1. En Visual Studio, abra el Administrador de paquetes de NuGet en el proyecto que se ha completado en el tutorial de [crear una aplicación de Xamarin Android] .  Buscar e instalar el paquete de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

2. Abra el archivo ToDoActivity.cs y quite los comentarios de la `#define OFFLINE_SYNC_ENABLED` definición.

3. En Visual Studio, presione la tecla **F5** para volver a crear y ejecutar la aplicación de cliente. La aplicación funciona de la misma que tenía antes de habilitar la sincronización sin conexión. Sin embargo, la base de datos local se rellena con datos que se pueden usar en un escenario sin conexión.

## <a name="update-sync"></a>Actualizar la aplicación que desconectar el back-end

En esta sección, se rompe la conexión a la aplicación móvil de back-end para simular una situación sin conexión. Al agregar elementos de datos, el controlador de excepciones le indica que la aplicación está en modo sin conexión. En este estado, los nuevos elementos agregan en el almacén local y se sincronizan con el servidor de la aplicación móvil cuando se ejecuta un comando en un estado de conexión.

1. Editar ToDoActivity.cs en el proyecto compartido. Cambiar la **ApplicationUrl** para que apunten a una URL no válida:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    También puede mostrar comportamiento sin conexión deshabilitando Wi-Fi y redes de telefonía móvil en el dispositivo o usar el modo de avión.

2. Presione **F5** para generar y ejecutar la aplicación. Observe que la sincronización no se pudo actualizar cuando se inicia la aplicación.

3. Escriba los nuevos elementos y observe que inserción produce un error con un estado de [CancelledByNetworkError] cada vez que haga clic en **Guardar**. Sin embargo, los nuevos elementos todo existen en el almacén local hasta que se pueden insertar en la aplicación móvil de back-end.  En una aplicación de producción, si suprimir estas excepciones la aplicación cliente se comporta como si aún está conectado a la aplicación móvil de back-end.

4. Cierre la aplicación y reinícielo para comprobar que los nuevos elementos que ha creado se conservan en el almacén local.

5. (Opcional) En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos de **Azure**->**Bases de datos de SQL**. Haga clic en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede ir a la tabla de base de datos SQL y su contenido. Compruebe que no ha cambiado los datos de la base de datos back-end.

6. (Opcional) Utilice una herramienta de resto como Fiddler o Postman para consultar el servidor móvil, mediante una consulta GET en el formulario `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Actualizar la aplicación para volver a conectar la aplicación móvil de back-end

En esta sección, vuelva a conectar la aplicación a la aplicación móvil de back-end. Primera vez que ejecute la aplicación, la `OnCreate` controlador de eventos llama `OnRefreshItemsSelected`. Este método llama `SyncAsync` para sincronizar su almacén local con la base de datos back-end.

1. Abra ToDoActivity.cs en el proyecto compartido y revertir el cambio de la propiedad **ApplicationUrl del** .

2. Presione la tecla **F5** para volver a crear y ejecutar la aplicación. La aplicación sincroniza los cambios locales con la aplicación móvil de Azure back-end con operaciones de inserción y extracción cuando la `OnRefreshItemsSelected` método se ejecuta.

3. (Opcional) Ver los datos actualizados con el Explorador de objetos de SQL Server o una herramienta de resto como Fiddler. Se ha sincronizado el aviso de los datos entre la base de datos de back-end de aplicación móvil de Azure y el almacén local.

4. En la aplicación, haga clic en la casilla de verificación junto a algunos elementos para completarlas en el almacén local.

  `CheckItem`llamadas `SyncAsync` al elemento de sincronización cada completada con la aplicación móvil de back-end. `SyncAsync`llamadas de inserción y extracción. **Cada vez que ejecute una extracción frente a una tabla que el cliente ha realizado cambios, push siempre se ejecuta automáticamente**. Así se garantiza que todas las tablas en el almacén local junto con relaciones siguen siendo coherentes. Este comportamiento puede traducirse en una inserción inesperada. Para obtener más información sobre este comportamiento, consulte [Sincronización de datos sin conexión en las aplicaciones móviles de Azure].

## <a name="review-the-client-sync-code"></a>Revise el código de sincronización de cliente

El proyecto de cliente de Xamarin que ha descargado cuando finaliza el tutorial [crear una aplicación de Xamarin Android] ya contiene código admite la sincronización sin conexión con una base de datos local de SQLite. Aquí es un breve resumen de lo que ya está incluido en el código c tutorial. Para obtener información general de la característica, consulte [Sincronización de datos sin conexión en las aplicaciones móviles de Azure].

* Antes de pueden realizar las operaciones de tabla, debe inicializarse el almacén local. Se ha inicializado la base de datos de la tienda cuando `ToDoActivity.OnCreate()` ejecuta `ToDoActivity.InitLocalStoreAsync()`. Este método crea una base de datos de SQLite local mediante la `MobileServiceSQLiteStore` clase proporcionada por el cliente de aplicaciones móviles de Azure SDK.

    La `DefineTable` método crea una tabla en el almacén local que coincida con los campos en el tipo proporcionado, `ToDoItem` en este caso. No tiene el tipo incluir todas las columnas que están en la base de datos remota. Es posible almacenar únicamente un subconjunto de columnas.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }


* La `toDoTable` miembro de `ToDoActivity` de la `IMobileServiceSyncTable` escriba en lugar de `IMobileServiceTable`. La IMobileServiceSyncTable dirige todo crear, leer, actualizar y eliminar (CRUD) operaciones de tabla a la base de datos del almacén local.

    Decidir cuándo se insertan los cambios a la aplicación móvil de Azure de back-end llamando `IMobileServiceSyncContext.PushAsync()`. El contexto de sincronización ayuda a conservar las relaciones de tablas de seguimiento y presión cambios en todas las tablas que ha modificado una aplicación de cliente cuando `PushAsync` se denomina.

    El código proporcionado llama `ToDoActivity.SyncAsync()` para sincronizar cada vez que se actualiza la lista de todoitem o un todoitem se agrega o se ha completado. Sincroniza de código después de cada cambio local.

    En el código proporcionado, todos los registros en el equipo remoto `TodoItem` son consultar la tabla, pero también es posible filtrar registros pasando un identificador de la consulta y la consulta a `PushAsync`. Para obtener más información, consulte la sección *Sincronización Incremental* en la [Sincronización de datos sin conexión en las aplicaciones móviles de Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Recursos adicionales

* [Sincronización de datos sin conexión en aplicaciones móviles de Azure]
* [Aplicaciones móviles Azure .NET SDK cómo][8]

<!-- URLs. -->
[Crear una aplicación de Xamarin Android]: ../app-service-mobile-xamarin-android-get-started.md
[Sincronización de datos sin conexión en aplicaciones móviles de Azure]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Crear una aplicación de Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Sincronización de datos sin conexión en aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
