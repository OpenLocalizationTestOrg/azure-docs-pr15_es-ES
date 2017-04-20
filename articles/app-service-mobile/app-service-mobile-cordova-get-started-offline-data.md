<properties
    pageTitle="Habilitar la sincronización sin conexión para la aplicación móvil de Azure (Cordova) | Microsoft Azure"
    description="Obtenga información sobre cómo usar la aplicación móvil de aplicación de servicio para caché y sincronización de datos sin conexión en la aplicación de Cordova"
    documentationCenter="cordova"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-cordova-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Habilitar la sincronización sin conexión para la aplicación móvil de Cordova

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

En este tutorial se presenta la característica de sincronización sin conexión de aplicaciones móviles de Azure para Cordova. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no hay ninguna conexión de red. Cambios se almacenan en una base de datos local. una vez que se vuelve a conectar el dispositivo, estos cambios se sincronizan con el servicio remoto.

En este tutorial se basa en la solución de tutorial rápido Cordova para las aplicaciones móviles que cree cuando haya completado el tutorial de [Inicio rápido de Apache Cordova]. En este tutorial, se actualizará la solución tutorial rápido para agregar características sin conexión de aplicaciones móviles de Azure. También resaltará el código de conexión específico en la aplicación.

Para obtener más información sobre la característica de sincronización sin conexión, vea el tema de la [Sincronización sin conexión de datos en las aplicaciones móviles de Azure]. Para obtener más información de uso de la API, vea el archivo [Léame] en el complemento.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Agregar la sincronización sin conexión para la solución de tutorial rápido

El código de sincronización sin conexión debe agregarse a la aplicación. Sincronización sin conexión requiere el complemento de almacenamiento de sqlite cordova, se agrega automáticamente a su aplicación cuando el complemento de aplicaciones de Azure Mobile se incluye en el proyecto. El proyecto de tutorial incluye dos de estos complementos.

1. En el Explorador de soluciones de Visual Studio, abra index.js y reemplace el código siguiente

        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend

    con este código:

        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context

2. A continuación, reemplace el siguiente código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    con este código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Las adiciones de código anterior inicializar el almacén local y definir una tabla local que coincida con los valores de columna utilizados en su Azure back-end. (No es necesario incluir todos los valores de columna en el código.)

    Obtener una referencia al contexto de sincronización llamando **getSyncContext**. El contexto de sincronización ayuda a conservar las relaciones de tablas de seguimiento y presión cambios en todas las tablas que se ha modificado una aplicación de cliente cuando se denomina **inserción** .

3. Actualizar la dirección URL de la aplicación a la URL de aplicación de la aplicación móvil.

4. A continuación, reemplazar este código:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    con este código:

        // todoItemTable = client.getTable('todoitem');

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    El código anterior inicializa el contexto de sincronización y, a continuación, llama a getSyncTable (en lugar de getTable) para obtener una referencia a la tabla local.

    Este código se utiliza la base de datos local para todas las crear, leer, actualiza y elimina operaciones de tabla (CRUD).

    En este ejemplo se realiza en los conflictos de sincronización de control de errores simple. Una aplicación real ¿controlar los diversos errores como condiciones de red, conflictos de servidor y otros usuarios. Para obtener ejemplos de código, vea el [ejemplo de sincronización sin conexión].

5. A continuación, agregue esta función para llevar a cabo la sincronización real.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Decidir cuándo se debe aplicar cambios a la aplicación móvil de back-end mediante una llamada de **inserción** en el objeto **syncContext** usado por el cliente. Por ejemplo, puede agregar una llamada a **syncBackend** a un controlador de eventos de botón en la aplicación como un nuevo botón de sincronización, o puede agregar la llamada a la función **addItemHandler** para sincronizar cada vez que se agrega un nuevo elemento.

##<a name="offline-sync-considerations"></a>Consideraciones de sincronización sin conexión

En el ejemplo, el método de **inserción** de **syncContext** se denomina solo en el inicio de la aplicación de la función de devolución de llamada para inicio de sesión.  En una aplicación del mundo real, también puede hacer que esta funcionalidad de sincronización activa manualmente o cuando se cambia el estado de la red.

Cuando una extracción se ejecuta en una tabla que se espera actualizaciones locales seguimiento por el contexto, esa operación de extracción activará automáticamente una inserción de contexto anterior. Al actualizar, agregar y completar los elementos de este ejemplo, puede omitir la llamada explícita **push** , puesto que pueden redundante (primera Compruebe el [archivo Léame] de estado actual de la característica).

En el código proporcionado, se consultan con todos los registros de la tabla todoItem remoto, pero también es posible filtrar registros pasando un identificador de la consulta y consulta de **inserción**. Para obtener más información, consulte la sección *Sincronización Incremental* en la [Sincronización de datos sin conexión en las aplicaciones móviles de Azure].

## <a name="optional-disable-authentication"></a>(Opcional) Deshabilitar la autenticación

Si no ha configurado ya en autenticación y no desea configurar la autenticación antes de la sincronización sin conexión pruebas, comente la función de devolución de llamada para inicio de sesión, pero dejar el código dentro de la función de devolución de llamada comentario.

El código debe tener este aspecto tras comentar las líneas de inicio de sesión.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Ahora, la aplicación se sincroniza con el servidor de Azure al ejecutar la aplicación en lugar de al iniciar sesión.

## <a name="run-the-client-app"></a>Ejecute la aplicación de cliente

Con la sincronización sin conexión ahora habilitada, ahora puede ejecutar la aplicación cliente al menos una vez en cada plataforma para rellenar la base de datos del almacén local. Más adelante, deberá simular escenarios sin conexión y modificar los datos en el almacén local mientras la aplicación está sin conexión.

## <a name="optional-test-the-sync-behavior"></a>(Opcional) Probar el comportamiento de sincronización

En esta sección, modificará el proyecto de cliente para simular un escenario sin conexión mediante una dirección URL de la aplicación no válido para el back-end. Al agregar o modificar elementos de datos, estos cambios se encuentran en el almacén local pero no se sincroniza con el almacén de datos back-end hasta que se restablezca la conexión.

1. En el Explorador de soluciones, abra el archivo de proyecto index.js y cambiar la dirección URL de la aplicación para que apunten a una dirección URL no válida, como la siguiente:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. En index.html, actualice el CSP `<meta>` elemento con la misma dirección URL válida.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Crear y ejecutar la aplicación cliente y observe que se registra una excepción en la consola cuando la aplicación intenta sincronizar con el servidor después de inicio de sesión. Agregar nuevos elementos se sólo existen en el almacén local hasta que se pueden insertar en el back-end móvil. La aplicación cliente se comporta como si está conectado a la back-end, auxiliares que todos crear, leer, actualizar, las operaciones de eliminación (CRUD).

4. Cierre la aplicación y reinícielo para comprobar que los nuevos elementos que ha creado se conservan en el almacén local.

5. (Opcional) Use Visual Studio para ver la tabla de base de datos de SQL Azure para ver que no ha cambiado los datos de la base de datos back-end.

    En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos de **Azure**->**Bases de datos de SQL**. Haga clic en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede ir a la tabla de base de datos SQL y su contenido.


## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcional) Probar volver a conectar a su back-end móvil

En esta sección se volverá a conectar la aplicación del móvil back-end, que simula la aplicación volver a un estado en línea. Cuando inicie sesión, los datos se sincronizarán con su back-end móvil.

1. Vuelva a abrir index.js y corrija la dirección URL de la aplicación para que apunten a la dirección URL correcta.

2. Vuelva a abrir index.html y corrija la dirección URL de la aplicación en el CSP `<meta>` elemento.

3. Volver a crear y ejecutar la aplicación de cliente. La aplicación intenta sincronizar con la aplicación móvil de back-end después de inicio de sesión. Compruebe que ninguna excepción iniciada en la consola de depuración.

4. (Opcional) Ver los datos actualizados con el Explorador de objetos de SQL Server o una herramienta de resto como Fiddler. Observe que se ha sincronizado los datos entre la base de datos back-end y el almacén local.

    Observe que los datos se ha sincronizado entre la base de datos y el almacén local y contiene los elementos agregados mientras la aplicación se ha desconectado.

## <a name="additional-resources"></a>Recursos adicionales

* [Sincronización de datos sin conexión en aplicaciones móviles de Azure]

* [Portada en la nube: sincronización sin conexión en Azure servicios móviles] \(Nota: el vídeo de servicios móviles, pero funciona la sincronización sin conexión de manera similar en las aplicaciones móviles de Azure\)

* [Visual Studio Tools para Cordova Apache]

## <a name="next-steps"></a>Pasos siguientes

* Mirar las características más avanzadas de sincronización sin conexión, como la resolución de conflictos en el [ejemplo de sincronización sin conexión]
* Mire la referencia de la API en el [archivo Léame] de sincronización sin conexión

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Inicio rápido de Apache Cordova]: app-service-mobile-cordova-get-started.md
[ARCHIVO LÉAME]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[ejemplo de sincronización sin conexión]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Sincronización de datos sin conexión en aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md
[Portada de la nube: Sincronización sin conexión en Azure servicios móviles]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools para Cordova Apache]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
