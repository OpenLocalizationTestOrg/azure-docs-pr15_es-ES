<properties
    pageTitle="Habilitar la sincronización sin conexión para la aplicación móvil de Azure (Xamarin.Forms) | Microsoft Azure"
    description="Obtenga información sobre cómo usar la aplicación móvil de aplicación de servicio para caché y sincronización de datos sin conexión en la aplicación de Xamarin.Forms"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Habilitar la sincronización sin conexión para la aplicación móvil de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Información general
En este tutorial se presenta la característica de sincronización sin conexión de aplicaciones móviles de Azure para Xamarin.Forms. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil: ver, agregar o modificar datos, incluso cuando no hay ninguna conexión de red. Cambios se almacenan en una base de datos local. Una vez que se vuelve a conectar el dispositivo, estos cambios se sincronizan con el servicio remoto.

En este tutorial se basa en la solución de tutorial rápido Xamarin.Forms para las aplicaciones móviles que cree cuando haya completado el tutorial [crear una aplicación de iOS Xamarin]. La solución de tutorial rápido para Xamarin.Forms contiene el código para admitir la sincronización sin conexión, que solo debe estar habilitado. En este tutorial, actualice la solución tutorial rápido para activar las características de aplicaciones de Azure Mobile sin conexión. También hemos resaltar el código de conexión específico en la aplicación. Si no utiliza la solución tutorial descargado, debe agregar los paquetes de extensión de acceso de datos a su proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, vea [trabajar con el servidor back-end de .NET SDK para las aplicaciones de Azure Mobile][1].

Para obtener más información sobre la característica de sincronización sin conexión, vea el tema de la [Sincronización sin conexión de datos en las aplicaciones de Azure Mobile][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Habilitar la funcionalidad de sincronización sin conexión en la solución de tutorial rápido

El código de sincronización sin conexión se incluye en el proyecto mediante el uso de directivas de preprocesador C#. Cuando el **sin conexión\_sincronización\_habilitado** símbolo se define, se incluyen estas rutas de acceso de código en la compilación. Para las aplicaciones de Windows, también debe instalar la plataforma de SQLite.

1. En Visual Studio, haga clic en la solución > **Administrar paquetes de NuGet para solución...**, a continuación, buscar e instalar el paquete de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** para todos los proyectos de la solución.

2. En el Explorador de soluciones, abra el archivo TodoItemManager.cs del proyecto con **Portable** en el nombre, que es el proyecto de biblioteca de clases portátiles, a continuación, quite los comentarios de la directiva de preprocesador siguiente:

        #define OFFLINE_SYNC_ENABLED

4. (Opcional) Para admitir dispositivos de Windows, instalar uno de los paquetes de tiempo de ejecución de SQLite siguientes:

    * **En tiempo de ejecución de Windows 8.1:** Instalar [SQLite para Windows 8.1][3].
    * **Windows Phone 8.1:** Instalar [SQLite para Windows Phone 8.1][4].
    * **Plataforma Windows universal** Instalar [SQLite para Universal de Windows Universal][5].

    Aunque el tutorial no contiene un proyecto de Windows Universal, la plataforma de Windows Universal es compatible con los formularios Xamarin.

5. (Opcional) En cada proyecto de aplicación de Windows, haga clic en **referencias** > **Agregar referencia**, expanda la carpeta de **Windows** > **extensiones**.
    Habilitar el correspondiente **SQLite para Windows** SDK junto con el SDK de **2013 Runtime para Windows de Visual C++** .
    Los nombres de SQLite SDK varían ligeramente con cada plataforma de Windows.

## <a name="review-the-client-sync-code"></a>Revise el código de sincronización de cliente

Aquí es un breve resumen de lo que ya está incluido en el código del tutorial dentro de la `#if OFFLINE_SYNC_ENABLED` directivas. La funcionalidad de sincronización sin conexión está en el archivo de proyecto TodoItemManager.cs en el proyecto de la biblioteca de clases portátil. Para obtener información general de la característica, consulte [Sincronización de datos sin conexión en las aplicaciones móviles de Azure][2].

* Antes de pueden realizar las operaciones de tabla, debe inicializarse el almacén local. La base de datos de la tienda es inicializarse en el constructor **TodoItemManager** mediante el siguiente código:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Este código crea una nueva base de SQLite local mediante la clase **MobileServiceSQLiteStore** .

    El método **DefineTable** crea una tabla en el almacén local que coincida con los campos en el tipo proporcionado.  No tiene el tipo incluir todas las columnas que están en la base de datos remota. Es posible almacenar un subconjunto de columnas.

* El campo **todoTable** **TodoItemManager** es un tipo de **IMobileServiceSyncTable** en lugar de **IMobileServiceTable**. Esta clase utiliza la base de datos local para todas las crear, leer, actualiza y elimina operaciones de tabla (CRUD). También puede decidir si los cambios se envían a la aplicación móvil de back-end mediante una llamada **PushAsync** en la **IMobileServiceSyncContext**. El contexto de sincronización ayuda a conservar las relaciones de tablas de seguimiento y presión cambios en todas las tablas que se ha modificado una aplicación de cliente cuando se llama **PushAsync** .

    Se llama al siguiente método **SyncAsync** para sincronizar con el servidor de la aplicación móvil:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Este ejemplo usa simple control de errores con el controlador de sincronización predeterminado. Una aplicación real ¿controlar los diversos errores como condiciones de red y servidor entra en conflicto con una implementación de **IMobileServiceSyncHandler** personalizada.

## <a name="offline-sync-considerations"></a>Consideraciones de sincronización sin conexión

En el ejemplo, solo se llama al método **SyncAsync** en el inicio y cuando se solicita una sincronización.  Para iniciar una sincronización en una aplicación de Android o iOS, extraer hacia abajo en la lista de elementos. para Windows, utilice el botón **sincronizar** . En una aplicación del mundo real, también puede hacer que el desencadenador de sincronización cuando cambia el estado de la red.

Cuando se ejecuta una extracción en una tabla que tiene pendiente actualizaciones locales controlan por el contexto, que extraen operación automáticamente desencadenadores push contexto anterior. Al actualizar, agregar y completar los elementos de este ejemplo, puede omitir la llamada de **PushAsync** explícita.

En el código proporcionado, se consultan con todos los registros de la tabla de TodoItem remota, pero también es posible filtrar registros pasando un identificador de la consulta y la consulta a **PushAsync**. Para obtener más información, consulte la sección *Sincronización Incremental* en la [Sincronización de datos sin conexión en las aplicaciones de Azure Mobile][2].

## <a name="run-the-client-app"></a>Ejecute la aplicación de cliente

Con la sincronización sin conexión ahora habilitada, ejecute la aplicación cliente al menos una vez en cada plataforma para rellenar la base de datos del almacén local. Más adelante, simular escenarios sin conexión y modificar los datos en el almacén local mientras la aplicación está sin conexión.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Actualizar el comportamiento de la aplicación cliente de sincronización

En esta sección, modifique el proyecto de cliente para simular un escenario sin conexión mediante una dirección URL de la aplicación no válido para el back-end. Como alternativa, puede desactivar las conexiones de red moviendo el dispositivo a "Modo de avión".  Al agregar o modificar elementos de datos, estos cambios se encuentran en el almacén local, pero no se sincroniza con el almacén de datos back-end hasta que se restablezca la conexión.

1. En el Explorador de soluciones, abra el archivo de proyecto Constants.cs del proyecto **portátil** y cambie el valor de `ApplicationURL` para que apunten a una URL no válida:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";

2. Abra el archivo TodoItemManager.cs del proyecto **portátil** , agregar **ponerse al día** de la clase de **excepción** base para el bloque **try... capturas** en **SyncAsync**. Este bloque **detectar** escribe el mensaje de excepción en la consola, como sigue:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. Crear y ejecutar la aplicación de cliente.  Agregue algunos nuevos elementos. Observe que se registra una excepción en la consola para cada vez que intente sincronizar con el servidor. Estos nuevos elementos sólo existen en el almacén local hasta que se pueden insertar en el back-end móvil. La aplicación cliente se comporta como si está conectado al back-end, auxiliares que todos crear, leer, actualizar, las operaciones de eliminación (CRUD).

4. Cierre la aplicación y reinícielo para comprobar que los nuevos elementos que ha creado se conservan en el almacén local.

5. (Opcional) Use Visual Studio para ver la tabla de base de datos de SQL Azure para ver que no ha cambiado los datos de la base de datos back-end.

    En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos de **Azure**->**Bases de datos de SQL**. Haga clic en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede ir a la tabla de base de datos SQL y su contenido.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Actualizar la aplicación cliente para volver a conectar su back-end móvil

En esta sección, vuelva a conectar la aplicación del móvil back-end, que simula la aplicación volver a un estado en línea. Al realizar el gesto de actualización, los datos se sincronizan con su back-end móvil.

1. Volver a abrir Constants.cs. Corregir el `applicationURL` para que apunten a la dirección URL correcta.

2. Volver a crear y ejecutar la aplicación de cliente. La aplicación intenta sincronizar con la aplicación móvil de back-end después de iniciar. Compruebe que ninguna excepción iniciada en la consola de depuración.

3. (Opcional) Ver los datos actualizados con el Explorador de objetos de SQL Server o una herramienta de resto como Fiddler o [Postman][6]. Observe que se ha sincronizado los datos entre la base de datos back-end y el almacén local.

    Observe que los datos se ha sincronizado entre la base de datos y el almacén local y contiene los elementos agregados mientras la aplicación se ha desconectado.

## <a name="additional-resources"></a>Recursos adicionales

* [Sincronización de datos sin conexión en aplicaciones móviles de Azure][2]
* [Aplicaciones móviles Azure .NET SDK cómo][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md