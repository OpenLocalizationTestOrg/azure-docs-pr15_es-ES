<properties
    pageTitle="Habilitar la sincronización sin conexión para la aplicación móvil de Azure (Android)"
    description="Aprenda a usar aplicaciones de aplicación de servicio móvil a la memoria caché y sincronización de datos sin conexión en la aplicación de Android"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Habilitar la sincronización sin conexión para la aplicación móvil Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Información general

Este tutorial trata sobre la característica de sincronización sin conexión de aplicaciones de Azure Mobile para Android. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no hay ninguna conexión de red. Cambios se almacenan en una base de datos local. Una vez que se vuelve a conectar el dispositivo, estos cambios se sincronizan con el servidor remoto.

Si esta es su primera experiencia con aplicaciones móviles de Azure, debe completar el tutorial [crear una aplicación de Android]. Si no utiliza el proyecto de servidor de inicio rápido descargado, debe agregar los paquetes de extensión de acceso de datos a su proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información sobre la característica de sincronización sin conexión, vea el tema de la [Sincronización sin conexión de datos en las aplicaciones móviles de Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Actualizar la aplicación para admitir la sincronización sin conexión

Con la sincronización sin conexión, lee y escribe desde una *tabla de sincronización* (mediante la interfaz de *IMobileServiceSyncTable* ), que forma parte de una base de datos de **SQLite** en su dispositivo.

Para insertar y extraer los cambios entre el dispositivo y servicios móviles de Azure, use un *contexto de sincronización* (*MobileServiceClient.SyncContext*), que inicializarse con la base de datos local para almacenar datos localmente.

1. En `TodoActivity.java`, comente la definición existente de `mToDoTable` y quite los comentarios de la versión de la tabla de sincronización:

        private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. En la `onCreate` método, comente la inicialización existente de `mToDoTable` y quite los comentarios de esta definición:

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. En `refreshItemsFromTable` comentario la definición de `results` y quite los comentarios de esta definición:

        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Comentario la definición de `refreshItemsFromMobileServiceTable`.

5. Quite los comentarios de la definición de `refreshItemsFromMobileServiceTableSyncTable`:

        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }

6. Quite los comentarios de la definición de `sync`:

        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Probar la aplicación

En esta sección, pruebe el comportamiento con Wi-Fi en y, a continuación, desactivar Wi-Fi para crear un escenario sin conexión.

Al agregar elementos de datos, se encuentran en el almacén local de SQLite pero no se sincronizan con el servicio móvil hasta que presione el botón **Actualizar** . Otras aplicaciones pueden tener requisitos diferentes sobre cuándo deben sincronizarse datos, pero con fines de demostración este tutorial tiene el usuario solicite explícitamente.

Al presionar un botón, se inicia una nueva tarea en segundo plano. En primer lugar, inserta todos los cambios realizados en el almacén local mediante el contexto de sincronización y, a continuación, extrae de cambiar todos los datos de Azure en la tabla local.

### <a name="offline-testing"></a>Probar sin conexión

1. Coloque el dispositivo o simulator en *Modo de avión*. Esto crea un escenario sin conexión.

2. Agregue algunos elementos *ToDo* o marcar algunos elementos como completado. Cierre el dispositivo o simulator (o Forzar cierre la aplicación) y reinicie. Comprobar que los cambios se han almacenado en el dispositivo debido a se encuentran en el almacén local de SQLite.

3. Ver el contenido de la tabla de Azure *TodoItem* con SQL herramienta como *SQL Server Management Studio*o un cliente de resto como *Fiddler* o *Postman*. Compruebe que los nuevos elementos _no_ se han sincronizado con el servidor

    + Para Node.js back-end, vaya al [portal de Azure](https://portal.azure.com/)y, en la aplicación móvil back-end haga clic en **Tablas fácil** > **TodoItem** para ver el contenido de la `TodoItem` tabla.
    + Para .NET back-end, vea la tabla de contenido con una herramienta SQL, como *SQL Server Management Studio*o un cliente de resto como *Fiddler* o *Postman*.

4. Activar Wi-Fi en el dispositivo o simulator. A continuación, presione el botón **Actualizar** .

5. Volver a ver los datos de TodoItem en el portal de Azure. Ahora debe aparecer la TodoItems nuevas y modificadas.

## <a name="additional-resources"></a>Recursos adicionales

* [Sincronización de datos sin conexión en aplicaciones móviles de Azure]

* [Portada en la nube: sincronización sin conexión en Azure servicios móviles] \(Nota: el vídeo de servicios móviles, pero funciona la sincronización sin conexión de manera similar en las aplicaciones móviles de Azure\)


<!-- URLs. -->

[Sincronización de datos sin conexión en aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md

[Crear una aplicación de Android]: app-service-mobile-android-get-started.md

[Portada de la nube: Sincronización sin conexión en Azure servicios móviles]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

