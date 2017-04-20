<properties
    pageTitle="Habilitar la sincronización sin conexión para la aplicación de la plataforma de Windows Universal (UWP) con aplicaciones móviles | Servicio de aplicaciones de Azure"
    description="Aprenda a usar una aplicación móvil de Azure caché y sincronización de los datos sin conexión en la aplicación de la plataforma de Windows Universal (UWP)."
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-windows-app"></a>Habilitar la sincronización sin conexión para la aplicación de Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Información general

En este tutorial se muestra cómo agregar soporte técnico sin conexión a una aplicación de la plataforma de Windows Universal (UWP) con una aplicación móvil de Azure de back-end. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil: ver, agregar o modificar datos - incluso cuando no hay ninguna conexión de red. Cambios se almacenan en una base de datos local. Una vez que se vuelve a conectar el dispositivo, estos cambios se sincronizan con el servidor remoto.

En este tutorial, actualice el proyecto de aplicación UWP desde el tutorial [crear una aplicación de Windows] para admitir las características de aplicaciones de Azure Mobile sin conexión. Si no utiliza el proyecto de servidor de inicio rápido descargado, debe agregar los paquetes de extensión de acceso de datos a su proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información sobre la característica de sincronización sin conexión, vea el tema de la [Sincronización sin conexión de datos en las aplicaciones móviles de Azure].

## <a name="requirements"></a>Requisitos

Este tutorial requiere los siguientes requisitos previos:

* 2013 de Visual Studio instalado en Windows 8.1 o posterior.
* Finalización de [crear una aplicación de Windows],[crear una aplicación de windows].
* [Almacén de SQLite de servicios de móvil de Azure][sqlite store nuget]
* [SQLite para el desarrollo de la plataforma de Windows Universal](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Actualizar la aplicación cliente para admitir características sin conexión

Características de sin conexión de Azure aplicación Mobile permiten interactuar con una base de datos local cuando se encuentra en un escenario sin conexión. Para usar estas características en la aplicación, inicializar un [SyncContext] [ synccontext] en un almacén local. A continuación, hacer referencia a la tabla mediante la interfaz de[IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite se usa como el almacén local en el dispositivo.

1. Instale el [tiempo de ejecución de SQLite para la plataforma de Windows Universal](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. En Visual Studio, abra el Administrador de paquetes de NuGet para el proyecto de aplicación UWP completado en el tutorial de [crear una aplicación de Windows] .
    Buscar e instalar el paquete de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

4. En el Explorador de soluciones, haga clic en **referencias** > **Agregar referencia**  >  **Windows Universal** > **extensiones**, habilitar **SQLite para Universal plataforma de Windows** y **Visual C++ 2015 Runtime para las aplicaciones de la plataforma de Windows Universal**.

    ![Agregar referencia SQLite UWP][1]

5. Abra el archivo MainPage.xaml.cs y quite los comentarios de la `#define OFFLINE_SYNC_ENABLED` definición.

6. En Visual Studio, presione la tecla **F5** para volver a crear y ejecutar la aplicación de cliente. La aplicación funciona de la misma que tenía antes de habilitar la sincronización sin conexión. Sin embargo, la base de datos local se rellena con datos que se pueden usar en un escenario sin conexión.

## <a name="update-sync"></a>Actualizar la aplicación que desconectar el back-end

En esta sección, se rompe la conexión a la aplicación móvil de back-end para simular una situación sin conexión. Al agregar elementos de datos, el controlador de excepciones le indica que la aplicación está en modo sin conexión. En este estado, los nuevos elementos agregan en el almacén local y se sincronizarán con el servidor de la aplicación móvil cuando se vuelva a ejecutar inserción en un estado de conexión.

1. Editar App.xaml.cs en el proyecto compartido. Comentar la inicialización de la **MobileServiceClient** y agregue la línea siguiente, que usa una dirección URL de la aplicación móvil no válidos:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    También puede mostrar comportamiento sin conexión al deshabilitar Wi-Fi y redes de telefonía móvil en el dispositivo o usar el modo de avión.

2. Presione **F5** para generar y ejecutar la aplicación. Observe que la sincronización no se pudo actualizar cuando se inicia la aplicación.

3. Escriba los nuevos elementos y observe que inserción produce un error con un estado de [CancelledByNetworkError] cada vez que haga clic en **Guardar**. Sin embargo, los nuevos elementos todo existen en el almacén local hasta que se pueden insertar en la aplicación móvil de back-end.  En una aplicación de producción, si suprimir estas excepciones la aplicación cliente se comporta como si aún está conectado a la aplicación móvil de back-end.

4. Cierre la aplicación y reinícielo para comprobar que los nuevos elementos que ha creado se conservan en el almacén local.

5. (Opcional) En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos de **Azure**->**Bases de datos de SQL**. Haga clic en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede ir a la tabla de base de datos SQL y su contenido. Compruebe que no ha cambiado los datos de la base de datos back-end.

6. (Opcional) Utilice una herramienta de resto como Fiddler o Postman para consultar el servidor móvil, mediante una consulta GET en el formulario `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Actualizar la aplicación para volver a conectar la aplicación móvil de back-end

En esta sección, vuelva a conectar la aplicación a la aplicación móvil de back-end. Estos cambios simulan una volver de red en la aplicación.

Primera vez que ejecute la aplicación, la `OnNavigatedTo` controlador de eventos llama `InitLocalStoreAsync`. Este método llama a su vez `SyncAsync` para sincronizar su almacén local con la base de datos back-end. La aplicación intenta sincronizar en el inicio.

1. Abra App.xaml.cs en el proyecto compartido y, a continuación, quite la inicialización anterior de `MobileServiceClient` usar la correcta de la dirección URL de la aplicación móvil.

2. Presione la tecla **F5** para volver a crear y ejecutar la aplicación. La aplicación sincroniza los cambios locales con la aplicación móvil de Azure back-end con operaciones de inserción y extracción cuando la `OnNavigatedTo` ejecuta el controlador de eventos.

3. (Opcional) Ver los datos actualizados con el Explorador de objetos de SQL Server o una herramienta de resto como Fiddler. Se ha sincronizado el aviso de los datos entre la base de datos de back-end de aplicación móvil de Azure y el almacén local.

4. En la aplicación, haga clic en la casilla de verificación junto a algunos elementos para completarlas en el almacén local.

  `UpdateCheckedTodoItem`llamadas `SyncAsync` al elemento de sincronización cada completada con la aplicación móvil de back-end. `SyncAsync`llamadas de inserción y extracción. Sin embargo, **cada vez que ejecute una extracción frente a una tabla que el cliente ha realizado cambios, push siempre se ejecuta automáticamente**. Este comportamiento garantiza que todas las tablas en el almacén local junto con relaciones siguen siendo coherentes. Este comportamiento puede traducirse en una inserción inesperada.  Para obtener más información sobre este comportamiento, consulte [Sincronización de datos sin conexión en las aplicaciones móviles de Azure].


##<a name="api-summary"></a>API de resumen

Para admitir las características de servicios móviles sin conexión, se utiliza la interfaz [IMobileServiceSyncTable] e inicializado [MobileServiceClient.SyncContext] [ synccontext] con una base de datos local de SQLite. Cuando está sin conexión, las operaciones de CRUD normales para las aplicaciones móviles funcionan como si la aplicación aún está conectada mientras se realizan las operaciones en el almacén local. Los métodos siguientes se utilizan para sincronizar el almacén local con el servidor:

*  **[PushAsync]** Dado que este método es un miembro de [IMobileServicesSyncContext], cambios en todas las tablas se insertan del back-end. Solo los registros con los cambios locales se envían al servidor.

* **[PullAsync] ** 
   una extracción se inicia desde un [IMobileServiceSyncTable]. Cuando hay cambios realizados en la tabla, se ejecuta una inserción implícita para asegurarse de que todas las tablas en el almacén local junto con relaciones siguen siendo coherentes. El parámetro *pushOtherTables* controla si se insertan otras tablas en el contexto de una inserción implícita. El parámetro de *consulta* toma una [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] 
   o OData cadena de consulta para filtrar los datos devueltos. El parámetro *queryId* se utiliza para definir la sincronización incremental. Para obtener más información, consulte  [Sincronización de datos sin conexión en las aplicaciones móviles de Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]** La aplicación debe llamar periódicamente este método para purgar datos obsoletos desde el almacén local. Usar el parámetro *Forzar* cuando necesite purgar todos los cambios que aún no se han sincronizado.

Para obtener más información sobre estos conceptos, consulte [Sincronización de datos sin conexión en las aplicaciones móviles de Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Más información

Los temas siguientes proporcionan información adicional sobre la característica de sincronización sin conexión de aplicaciones móviles:

* [Sincronización de datos sin conexión en aplicaciones móviles de Azure]
* [Aplicaciones móviles Azure .NET SDK cómo][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronización de datos sin conexión en aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md
[crear una aplicación de windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
